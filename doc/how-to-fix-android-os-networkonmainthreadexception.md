## 如何修复错误 android.os.NetworkOnMainThreadException?

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/6343166/how-to-fix-android-os-networkonmainthreadexception)

### 问题描述

在开发 RssReader 项目时运行下面的代码遇到了错误，

```
URL url = new URL(urlToRssFeed);
SAXParserFactory factory = SAXParserFactory.newInstance();
SAXParser parser = factory.newSAXParser();
XMLReader xmlreader = parser.getXMLReader();
RssHandler theRSSHandler = new RssHandler();
xmlreader.setContentHandler(theRSSHandler);
InputSource is = new InputSource(url.openStream());
xmlreader.parse(is);
return theRSSHandler.getFeed();

```

下面是错误信息，请问应该如何修复这个问题？

```
android.os.NetworkOnMainThreadException

```


### 高票答案1

上述错误一般是在主线程里做网络操作时发生，可以使用 [AsyncTask](https://developer.android.com/reference/android/os/AsyncTask.html) :

```
class RetrieveFeedTask extends AsyncTask<String, Void, RSSFeed> {

    private Exception exception;

    protected RSSFeed doInBackground(String... urls) {
        try {
            URL url = new URL(urls[0]);
            SAXParserFactory factory = SAXParserFactory.newInstance();
            SAXParser parser = factory.newSAXParser();
            XMLReader xmlreader = parser.getXMLReader();
            RssHandler theRSSHandler = new RssHandler();
            xmlreader.setContentHandler(theRSSHandler);
            InputSource is = new InputSource(url.openStream());
            xmlreader.parse(is);

            return theRSSHandler.getFeed();
        } catch (Exception e) {
            this.exception = e;

            return null;
        }
    }

    protected void onPostExecute(RSSFeed feed) {
        // TODO: check this.exception
        // TODO: do something with the feed
    }
}

```

那么如何执行这个任务呢 ? 只需要在 `MainActivity.java` 的 `onCreate()` 方法里加入下面代码即可：

```
new RetrieveFeedTask().execute(urlToRssFeed);

```

最后不要忘记在 `AndroidManifest.xml` 加入网络权限：

```
<uses-permission android:name="android.permission.INTERNET"/>

```


### 高票答案2

可以使用 Thread 解决该问题：

```
Thread thread = new Thread(new Runnable() {

    @Override
    public void run() {
        try  {
            //Your code goes here
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
});

thread.start();

```

### 高票答案3

使用 AsyncTask 确实是最容易想到的方式，但它也有下面的缺点：

- 创建 AsyncTask 时一般定义为非静态的内部类，它维持了 Activity 对象的一个隐式引用。如果 AsyncTask 没有执行完毕该引用会阻止 Activity 进行垃圾回收。如果网络连接缓慢或者下载的文件很大，这些场景产生的内存泄漏可能会很严重。例如当用户屏幕翻转多次（这时没法取消正在执行的任务）或者用户返回键退出 Activty。
- AsyncTask 在不同版本上有区别，执行的结果可能不是你想要的。API 4到 API 10 它使用了一个上限为 128 线程的线程池，任务可以并行执行；而在 API 11 上，它默认在一个单独的后台线程中（除非你重写 executeOnExecutor 方法并提供一个可用的 executor）串行执行。这样会导致在 API 11可以执行的很好的任务到 API 10 的设备上执行可能就会奔溃。

鉴于此，可以考虑用 `IntentService` 来执行一个单独的后台线程。

**步骤 1** ： 先创建一个 `IntentService` ，通过 `Intent` 传递你想要下载的数据信息， 然后通过 `PendingIntent` 返回结果给 `Activity` ：

```
public class DownloadIntentService extends IntentService {

    private static final String TAG = DownloadIntentService.class.getSimpleName();

    public static final String PENDING_RESULT_EXTRA = "pending_result";
    public static final String URL_EXTRA = "url";
    public static final String RSS_RESULT_EXTRA = "url";

    public static final int RESULT_CODE = 0;
    public static final int INVALID_URL_CODE = 1;
    public static final int ERROR_CODE = 2;

    private IllustrativeRSSParser parser;

    public DownloadIntentService() {
        super(TAG);

        // make one and re-use, in the case where more than one intent is queued
        parser = new IllustrativeRSSParser();
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        PendingIntent reply = intent.getParcelableExtra(PENDING_RESULT_EXTRA);
        InputStream in = null;
        try {
            try {
                URL url = new URL(intent.getStringExtra(URL_EXTRA));
                IllustrativeRSS rss = parser.parse(in = url.openStream());

                Intent result = new Intent();
                result.putExtra(RSS_RESULT_EXTRA, rss);

                reply.send(this, RESULT_CODE, result);
            } catch (MalformedURLException exc) {
                reply.send(INVALID_URL_CODE);
            } catch (Exception exc) {
                // could do better by treating the different sax/xml exceptions individually
                reply.send(ERROR_CODE);
            }
        } catch (PendingIntent.CanceledException exc) {
            Log.i(TAG, "reply cancelled", exc);
        }
    }
}


```

**步骤 2** ：在 manifest 文件中注册服务：

```

<service
        android:name=".DownloadIntentService"
        android:exported="false"/>

```

**步骤 3** ： 从 Activity 中调起 Service ，并传递 pendingResult 对象用于返回 Service 的执行结果：

```
PendingIntent pendingResult = createPendingResult(RSS_DOWNLOAD_REQUEST_CODE, new Intent(), 0);
Intent intent = new Intent(getApplicationContext(), DownloadIntentService.class);
intent.putExtra(DownloadIntentService.URL_EXTRA, URL);
intent.putExtra(DownloadIntentService.PENDING_RESULT_EXTRA, pendingResult);
startService(intent);

```


**步骤 4** ：在 onActivityResult 中处理结果：

```

@Override
protected void onActivityResult(int requestCode, int resultCode, Intent data) {
    if (requestCode == RSS_DOWNLOAD_REQUEST_CODE) {
        switch (resultCode) {
            case DownloadIntentService.INVALID_URL_CODE:
                handleInvalidURL();
                break;
            case DownloadIntentService.ERROR_CODE:
                handleError(data);
                break;
            case DownloadIntentService.RESULT_CODE:
                handleRSS(data);
                break;
        }
        handleRSS(data);
    }
    super.onActivityResult(requestCode, resultCode, data);
}


```
