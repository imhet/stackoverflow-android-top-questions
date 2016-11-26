## ListView 中如何实现图片的懒加载

> imhet 译自 [stackoverflow](http://stackoverflow.com/questions/541966/lazy-load-of-images-in-listview)

### 问题描述

我在使用 ListView 显示一组不确定数量的图片和文字。其中图片是从网络中获取，请问有没有一种方式可以实现滚动时图片的懒加载（延迟加载），要求一旦图片下载完毕立即显示到界面上而不会使用界面变得卡顿？

### 高票答案1

下面是我实现的代码，可以参考下：

```

public class DrawableManager {
    private final Map<String, Drawable> drawableMap;

    public DrawableManager() {
        drawableMap = new HashMap<String, Drawable>();
    }

    public Drawable fetchDrawable(String urlString) {
        if (drawableMap.containsKey(urlString)) {
            return drawableMap.get(urlString);
        }

        Log.d(this.getClass().getSimpleName(), "image url:" + urlString);
        try {
            InputStream is = fetch(urlString);
            Drawable drawable = Drawable.createFromStream(is, "src");


            if (drawable != null) {
                drawableMap.put(urlString, drawable);
                Log.d(this.getClass().getSimpleName(), "got a thumbnail drawable: " + drawable.getBounds() + ", "
                        + drawable.getIntrinsicHeight() + "," + drawable.getIntrinsicWidth() + ", "
                        + drawable.getMinimumHeight() + "," + drawable.getMinimumWidth());
            } else {
              Log.w(this.getClass().getSimpleName(), "could not get thumbnail");
            }

            return drawable;
        } catch (MalformedURLException e) {
            Log.e(this.getClass().getSimpleName(), "fetchDrawable failed", e);
            return null;
        } catch (IOException e) {
            Log.e(this.getClass().getSimpleName(), "fetchDrawable failed", e);
            return null;
        }
    }

    public void fetchDrawableOnThread(final String urlString, final ImageView imageView) {
        if (drawableMap.containsKey(urlString)) {
            imageView.setImageDrawable(drawableMap.get(urlString));
        }

        final Handler handler = new Handler() {
            @Override
            public void handleMessage(Message message) {
                imageView.setImageDrawable((Drawable) message.obj);
            }
        };

        Thread thread = new Thread() {
            @Override
            public void run() {
                //TODO : set imageView to a "pending" image
                Drawable drawable = fetchDrawable(urlString);
                Message message = handler.obtainMessage(1, drawable);
                handler.sendMessage(message);
            }
        };
        thread.start();
    }

    private InputStream fetch(String urlString) throws MalformedURLException, IOException {
        DefaultHttpClient httpClient = new DefaultHttpClient();
        HttpGet request = new HttpGet(urlString);
        HttpResponse response = httpClient.execute(request);
        return response.getEntity().getContent();
    }
}

```

### 高票答案2

我写了加载图片的[简单例子](https://github.com/thest1/LazyList)（放在 github 上），可以参考下。在这个例子中，我使用后台线程来下载图片，图片缓存在 SD 卡和内存中，图片缓存的实现比较简单但足够用，解码图片时我使用了图片的 inSampleSize 属性来减少内存消耗，而且我还处理了 view 的回收。

### 高票答案3

推荐你使用开源类库 [Universal Image Loader](https://github.com/nostra13/Android-Universal-Image-Loader)。好处很多，自己看项目介绍。

### 高票答案4

这些开源类库用于加载图片也很给力：

- [Picasso](http://square.github.io/picasso/) square 出品，必属精品，值得参考
- [Glide](https://github.com/bumptech/glide) android 官方出品，支持链式调用
- [Fresco](https://github.com/facebook/fresco) Facebook 出品，在 Android 4.x 及之前版本中，该类库利用了匿名共享内存技术来节省内存使用
