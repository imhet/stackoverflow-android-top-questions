## 如何唯一确定一台 Android 设备?

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/2785485/is-there-a-unique-android-device-id)

### 问题描述

是否每一台 Android 设备都有唯一 ID，如果有，如果使用 Java 获取它？

### 高票答案1

使用 [Settings.Secure#ANDROID_ID](https://developer.android.com/reference/android/provider/Settings.Secure.html#ANDROID_ID) 可以返回 ANDROID_ID ，它是一个唯一的64位字符串。

```
import android.provider.Settings.Secure;

private String android_id = Secure.getString(getContext().getContentResolver(),Secure.ANDROID_ID);
```

### 高票答案2

对于一般需求来说，`TelephonyManager.getDeviceId()` 就够用了。复杂些的需求可以与 UUID 等结合，如下所示。

```
final TelephonyManager tm = (TelephonyManager) getBaseContext().getSystemService(Context.TELEPHONY_SERVICE);

final String tmDevice, tmSerial, androidId;
tmDevice = "" + tm.getDeviceId();
tmSerial = "" + tm.getSimSerialNumber();
androidId = "" + android.provider.Settings.Secure.getString(getContentResolver(), android.provider.Settings.Secure.ANDROID_ID);

UUID deviceUuid = new UUID(androidId.hashCode(), ((long)tmDevice.hashCode() << 32) | tmSerial.hashCode());
String deviceId = deviceUuid.toString();

```
因为用到了 `TelephonyManager` 的属性，需要添加以下权限。

```
<uses-permission android:name="android.permission.READ_PHONE_STATE" />

```

### 高票答案3

基于 ANROID_ID 的众多缺陷和官方的推荐，在下面代码中我实现了一个用于为每个设备产生唯一 UUID 的类。首先获取 ANDROID_ID 作为种子生成 UUID，如果 ANDROID_ID 失败或 ANDROID_ID 不正确（取到了模拟器的使用的 ANDROID_ID 9774d56d682e549c），则获取 `TelephonyManager.getDeviceId() ` 作为种子，如果这样还失败，则随机生成一个 UUID 作为种子再生成 UUID。

这里要注意的是，产生的唯一 ID 会随着你恢复出厂设置而一直存在。如果你需要在恢复出厂设置后重置你的唯一 ID ，你可以考虑使用 UUID 而不是设备 ID。

再次申明，下面的代码生成的是设备 ID，而不是标记一个应用被安装的唯一 ID。大多数情况下，你可能需要的是标记你的应用安装量的唯一 ID。

```
import android.content.Context;
import android.content.SharedPreferences;
import android.provider.Settings.Secure;
import android.telephony.TelephonyManager;

import java.io.UnsupportedEncodingException;
import java.util.UUID;

public class DeviceUuidFactory {

    protected static final String PREFS_FILE = "device_id.xml";
    protected static final String PREFS_DEVICE_ID = "device_id";
    protected volatile static UUID uuid;

    public DeviceUuidFactory(Context context) {
        if (uuid == null) {
            synchronized (DeviceUuidFactory.class) {
                if (uuid == null) {
                    final SharedPreferences prefs = context.getSharedPreferences(PREFS_FILE, 0);
                    final String id = prefs.getString(PREFS_DEVICE_ID, null);
                    if (id != null) {
                        uuid = UUID.fromString(id);
                    } else {
                        final String androidId = Secure.getString(context.getContentResolver(), Secure.ANDROID_ID);
                        try {
                            if (!"9774d56d682e549c".equals(androidId)) {
                                uuid = UUID.nameUUIDFromBytes(androidId.getBytes("utf8"));
                            } else {
                                final String deviceId = ((TelephonyManager)context.getSystemService(Context.TELEPHONY_SERVICE)).getDeviceId();
                                uuid = deviceId != null ? UUID.nameUUIDFromBytes(deviceId.getBytes("utf8")) : UUID.randomUUID();
                            }
                        } catch (UnsupportedEncodingException e) {
                            throw new RuntimeException(e);
                        }

                        prefs.edit().putString(PREFS_DEVICE_ID, uuid.toString()).commit();
                    }
                }
            }
        }
    }

    public UUID getDeviceUuid() {
        return uuid;
    }
}

```

### 高票答案4

考察了众多唯一 ID 的产生方式后这里提出来一种 `虚拟 ID` 的概念，即组合各种 ID 的生成的唯一 ID。

```
/**
 * 返回 唯一的虚拟 ID
 * @return ID
 */
public static String getUniquePsuedoID() {
    String m_szDevIDShort = "35" + (Build.BOARD.length() % 10) + (Build.BRAND.length() % 10) + (Build.CPU_ABI.length() % 10) + (Build.DEVICE.length() % 10) + (Build.MANUFACTURER.length() % 10) + (Build.MODEL.length() % 10) + (Build.PRODUCT.length() % 10);

    // API >= 9 的设备才有 android.os.Build.SERIAL
    // http://developer.android.com/reference/android/os/Build.html#SERIAL
    // 如果用户更新了系统或 root 了他们的设备，该 API 将会产生重复记录
    String serial = null;
    try {
        serial = android.os.Build.class.getField("SERIAL").get(null).toString();
        return new UUID(m_szDevIDShort.hashCode(), serial.hashCode()).toString();
    } catch (Exception exception) {
        serial = "serial";
    }

    // 最后，组合上述值并生成 UUID 作为唯一 ID
    return new UUID(m_szDevIDShort.hashCode(), serial.hashCode()).toString();
}

```


### 高票答案5

Google I/O 有一年的演讲中提到了使用 UUID 并同步到云端的方案。如果您的项目中对有云端配置备份策略可以考虑使用下面的方法。

```
private static String uniqueID = null;
private static final String PREF_UNIQUE_ID = "PREF_UNIQUE_ID";

public synchronized static String id(Context context) {
    if (uniqueID == null) {
        SharedPreferences sharedPrefs = context.getSharedPreferences(
                PREF_UNIQUE_ID, Context.MODE_PRIVATE);
        uniqueID = sharedPrefs.getString(PREF_UNIQUE_ID, null);
        if (uniqueID == null) {
            uniqueID = UUID.randomUUID().toString();
            Editor editor = sharedPrefs.edit();
            editor.putString(PREF_UNIQUE_ID, uniqueID);
            editor.commit();
        }
    }
    return uniqueID;
}

```
