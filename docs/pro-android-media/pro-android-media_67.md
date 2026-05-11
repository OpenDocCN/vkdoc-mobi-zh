# 第 12 章：使用 Web 服务进行媒体消费与发布

查看返回的 JSON 数据后，我们可以确定需要借助 JSON 解析器执行哪些步骤来获取所需信息。通过分解数据可以发现，存在一个名为`photos`的顶层对象，其中包含一个名为`photo`的 JSON 对象数组。

`photo`数组中的每一项都包含一系列其他值：`id`、`owner`、`secret`、`server`、`farm`、`title`等。Flickr API 文档中有一个章节专门介绍了如何使用这些值来构建指向实际图像文件的 URL：

[www.flickr.com/services/api/misc.urls.html](http://www.flickr.com/services/api/misc.urls.html)

让我们完整地演练一个示例，将所有内容整合在一起。

```
package com.apress.proandroidmedia.ch12.flickrjson;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.DefaultHttpClient;
import org.json.JSONArray;
import org.json.JSONObject;

import android.app.Activity;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.os.Bundle;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ImageView;
import android.widget.ListView;
import android.widget.TextView;

public class FlickrJSON extends Activity {
```

在这行代码中，文本`"YOUR_API_KEY"`需要替换为向 Flickr 申请后获得的 API 密钥。

```
public static final String API_KEY = "YOUR_API_KEY";
```

在此示例的后续部分，我们定义了一个名为`FlickrPhoto`的类。每次在 JSON 数据中发现`photo`元素时，都会创建一个`FlickrPhoto`对象。我们将把所有`FlickrPhoto`对象放入一个名为`photos`的数组中，该数组在此处定义。

```
FlickrPhoto[] photos;

@Override
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.main);
```

当我们的 Activity 首次启动时，我们将使用`HttpClient`向 Flickr API 发起请求。

```
HttpClient httpclient = new DefaultHttpClient();
HttpGet httpget = new HttpGet(
    "http://api.flickr.com/services/rest/?method=flickr.photos.search&tags=waterfront&format=json&api_key=" + API_KEY + "&per_page=5&nojsoncallback=1");

HttpResponse response;
try {
    response = httpclient.execute(httpget);
    HttpEntity entity = response.getEntity();
    if (entity != null) {
        InputStream inputstream = entity.getContent();
```

获取内容的`InputStream`后，我们将读取其中的内容并创建一个独立的`String`，以便传递给 JSON 解析器。

```
BufferedReader bufferedreader = new BufferedReader(
    new InputStreamReader(inputstream));
StringBuilder stringbuilder = new StringBuilder();
String currentline = null;
try {
    while ((currentline = bufferedreader.readLine()) != null) {
        stringbuilder.append(currentline + "\n");
    }
} catch (IOException e) {
    e.printStackTrace();
}
String result = stringbuilder.toString();
```



现在我们已经获得了 HTTP 请求的结果，接下来可以着手解析返回的 JSON 数据。首先，我们会创建一个`JSONObject`来存储整个 JSON 数据，然后利用第一个对象获取名为`photos`的主要 JSON 对象。

```
JSONObject thedata = new JSONObject(result);
JSONObject thephotosdata = thedata.getJSONObject("photos");
```

拿到该对象后，我们就可以访问名为`photo`的`JSONArray`。

```
JSONArray thephotodata = thephotosdata.getJSONArray("photo");
```

现在，我们已经获得了 JSON 照片对象的数组，接下来设置`FlickrPhoto`对象数组的长度。之后，我们将遍历这些 JSON 对象，提取所有相关数据，并在`FlickrPhoto`照片数组中创建`FlickrPhoto`对象。

```
photos = new FlickrPhoto[thephotodata.length()];
for (int i = 0; i < thephotodata.length(); i++) {
    JSONObject photodata = thephotodata.getJSONObject(i);
    Log.v("JSON", photodata.getString("id"));
    photos[i] = new FlickrPhoto(
        photodata.getString("id"),
        photodata.getString("owner"),
        photodata.getString("secret"),
        photodata.getString("server"),
        photodata.getString("title"),
        photodata.getString("farm")
    );
}
inputstream.close();
```

```
} catch (Exception e) {
    e.printStackTrace();
}
```

最后，在`onCreate`方法中，我们将获取布局中设置的`ListView`并设置其适配器。我们将使用在此定义的`FlickrGalleryAdapter`类来构造适配器，并传入`FlickrPhotos`数组。

```
ListView listView = (ListView) this.findViewById(R.id.ListView);
listView.setAdapter(new FlickrGalleryAdapter(this, photos));
```

接下来是`FlickrGalleryAdapter`类，其职责是确定将在`ListView`中显示的内容。在本例中，它用于填充布局中定义的`ListView`，并显示来自 Flickr 搜索的照片和标题。

```
class FlickrGalleryAdapter extends BaseAdapter {
    private Context context;
    private FlickrPhoto[] photos;
    LayoutInflater inflater;

    public FlickrGalleryAdapter(Context context, FlickrPhoto[] items) {
        context = _context;
        photos = _items;
        inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    }

    public int getCount() {
        return photos.length;
    }

    public Object getItem(int position) {
        return photos[position];
    }

    public long getItemId(int position) {
        return position;
    }
```

`getView`方法是该类的核心，它决定了`ListView`中每个单元格将显示的内容。

```
    public View getView(int position, View convertView, ViewGroup parent) {
```

首先，我们需要获取代表每一行的视图的引用。在本例中，我们通过填充`list_item`布局（定义在`list_item.xml`中）来实现。

```
        View photoRow = inflater.inflate(R.layout.list_item, null);
```

获得单元格的主视图后，我们可以获取各个元素并设置它们的内容。首先处理`ImageView`，并利用从 JSON 数据返回的信息，在`FlickrPhoto`对象中构造 URL，将`Bitmap`设置为该 URL 返回的图片。

```
        ImageView image = (ImageView) photoRow.findViewById(R.id.ImageView);
        image.setImageBitmap(imageFromUrl(photos[position].makeURL()));
```

然后，对`TextView`进行类似处理，将其文本设置为图片的标题。

```
        TextView imageTitle = (TextView) photoRow.findViewById(R.id.TextView);
        imageTitle.setText(photos[position].title);
        return photoRow;
    }
```

下面是一个用于从在线图片 URL 创建`Bitmap`的方法，供上述`ImageView`使用。它使用`HttpURLConnection`打开指向该图片文件的`InputStream`，并将其传递给`BitmapFactory`来创建`Bitmap`。

```
    public Bitmap imageFromUrl(String url) {
        Bitmap bitmapImage;
        URL imageUrl = null;
        try {
            imageUrl = new URL(url);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        try {
            HttpURLConnection httpConnection = (HttpURLConnection) imageUrl.openConnection();
```



```java
httpConnection.setDoInput(true);

httpConnection.connect();

InputStream is = httpConnection.getInputStream();

bitmapImage = BitmapFactory.decodeStream(is);

} catch (IOException e) {

e.printStackTrace();

bitmapImage = Bitmap.createBitmap(10, 10, Bitmap.Config.ARGB_8888);

}

return bitmapImage;

}

}
```

最后，这是 `FlickrPhoto` 类。它是一个 Java 类，用于表示 JSON 数据中每张照片所需的数据。

```java
class FlickrPhoto {

String id;

String owner;

String secret;

String server;

String title;

String farm;

public FlickrPhoto(String _id, String _owner, String _secret,

String _server, String _title, String _farm) {

id = _id;

owner = _owner;

secret = _secret;

server = _server;

title = _title;

farm = _farm;

}
```

根据 Flickr 的 API 文档，以下 `makeURL` 方法会将数据转换为图片的 URL。

```java
public String makeURL() {

return "http://farm" + farm + ".static.flickr.com/" + server + "/"

+ id + "_" + secret + "_m.jpg";

// http://farm{farm-id}.static.flickr.com/{server-id}/{id}_{secret}_[mstzb].jpg

// 来自：http://www.flickr.com/services/api/misc.urls.html

}

}

}
```

以下是 `main.xml` 文件，其中包含上述代码中使用的布局。

```xml
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"

android:orientation="vertical"

android:layout_width="fill_parent"

android:layout_height="fill_parent"

>

<ListView android:layout_width="wrap_content" android:layout_height="wrap_content"

android:id="@+id/ListView"></ListView>

</LinearLayout>
```

以下是 `list_item.xml` 文件，它定义了 `ListView` 所使用的布局。

```xml
<?xml version="1.0" encoding="utf-8"?>

<LinearLayout

android:layout_width="wrap_content"

android:layout_height="wrap_content">

<ImageView android:id="@+id/ImageView" android:layout_width="wrap_content"

android:layout_height="wrap_content"></ImageView>

<TextView android:text="@+id/TextView01" android:layout_width="wrap_content"

android:layout_height="wrap_content" android:id="@+id/TextView"></TextView>

</LinearLayout>
```

最后是 `AndroidManifest.xml` 文件，它包含了从 Flickr 拉取数据所需的 `INTERNET` 权限。

```xml
<?xml version="1.0" encoding="utf-8"?>

<manifest xmlns:android="http://schemas.android.com/apk/res/android"

package="com.apress.proandroidmedia.ch12.flickrjson"

android:versionCode="1"

android:versionName="1.0">

<application android:icon="@drawable/icon" android:label="@string/app_name">

<activity android:name=".FlickrJSON"

android:label="@string/app_name">

<intent-filter>

<action android:name="android.intent.action.MAIN" />

<category android:name="android.intent.category.LAUNCHER" />

</intent-filter>

</activity>

</application>

<uses-sdk android:minSdkVersion="4" />

<uses-permission android:name="android.permission.INTERNET"></uses-permission>

</manifest>
```

图 12-1 展示了上述示例的结果。

正如我们所见，使用 JSON 与 Flickr 这样的网络服务进行交互非常直接，而且潜力巨大。

**图 12-1.** *ListView 显示来自 Flickr 的、标记为“waterfront”的图片*

## 位置服务

由于我们是在位置可能会变化的移动设备上访问这些服务，因此将位置信息作为请求的一部分可能会很有趣。在一个地方搜索 Flickr 上的“waterfront”，与在另一个地方搜索的结果会截然不同。

Android 为我们提供了 `LocationManager` 类，我们可以使用它在应用中查找和跟踪位置变化。

以下是一个简单的代码片段，演示了如何使用 `LocationManager` 并监听位置更新。

```java
package com.apress.proandroidmedia.ch12.locationtracking;

import android.app.Activity;

import android.content.Context;

import android.location.Location;
```



```java
import android.location.LocationListener;
import android.location.LocationManager;
import android.location.LocationProvider;
import android.os.Bundle;
import android.util.Log;
import android.widget.TextView;
```

为了从 `LocationManager` 接收位置更新，我们将让活动实现 `LocationListener`。

```java
public class LocationTracking extends Activity implements LocationListener {

    LocationManager lm;
    TextView tv;

    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        tv = (TextView) this.findViewById(R.id.location);
```

我们通过使用 `Context`（`Activity` 是其子类，因此我们可以直接使用）中提供的 `getSystemService` 方法来获取 `LocationManager` 的实例。

```java
        lm = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
```

`LocationManager` 允许我们指定希望 `LocationListener`（本例中即我们的活动）在位置相关变化时收到通知。我们通过将活动作为最后一个参数传递给 `requestLocationUpdates` 方法，将其注册为 `LocationListener`。

该方法的第一个参数是我们要使用的位置提供器。`LocationManager` 类中提供了两个位置提供器，以常量形式指定。

我们这里使用的 `NETWORK_PROVIDER` 利用蜂窝基站定位或 WiFi 接入点定位等网络服务来确定位置。另一个可用的提供器是 `GPS_PROVIDER`，它利用 GPS（全球定位卫星）提供位置信息。`NETWORK_PROVIDER` 通常比 GPS 快得多，但定位精度可能较低。GPS 可能需要较长时间才能获取卫星信号，并且在室内或天空视野不佳的区域（例如曼哈顿中城）可能完全无法工作。

第二个参数是系统在两次“位置已更改”通知之间等待的最小时间间隔。它以 `long` 类型表示毫秒数。这里我们设置为 60,000 毫秒，即 1 分钟。

第三个参数是位置必须变化的距离，超过此距离才会触发“位置已更改”通知。它以 `float` 类型表示米数。这里我们设置为 5 米。

```java
        lm.requestLocationUpdates(LocationManager.NETWORK_PROVIDER, 60000l, 5.0f, this);
    }
```

使用 `LocationManager` 时，尤其是在使用 GPS 作为提供器的情况下，当应用不再处于前台时，明智的做法是停止位置更新，以节省电池电量。为此，我们可以在活动中重写标准的 `onPause` 或 `onStop` 方法，并在 `LocationManager` 对象上调用 `removeUpdates` 方法。

```java
    public void onPause() {
        super.onPause();
        lm.removeUpdates(this);
    }
```

当位置发生变化，且变化量大于 `requestLocationUpdates` 方法中指定的距离和时间参数时，已注册的 `LocationListener` 上的 `onLocationChanged` 方法将被调用，并传入一个 `Location` 对象。

传入的 `Location` 对象提供了获取纬度（`getLatitude`）、经度（`getLongitude`）、海拔（`getAltitude`）等方法，更多详细信息可参阅文档：

[`developer.android.com/reference/android/location/Location.html`](http://developer.android.com/reference/android/location/Location.html)

```java
    public void onLocationChanged(Location location) {
        tv.setText(location.getLatitude() + " " + location.getLongitude());
        Log.v("LOCATION", "onLocationChanged: lat=" + location.getLatitude() + ", lon=" + location.getLongitude());
    }
```

如果用户禁用了被监控的提供器，已注册的 `LocationListener` 中的 `onProviderDisabled` 方法将被调用。

```java
    public void onProviderDisabled(String provider) {
        Log.v("LOCATION", "onProviderDisabled: " + provider);
    }
}
```



`onProviderEnabled`方法在已注册的`LocationListener`中，当被监控的提供者被用户启用时会被调用。

```
public void onProviderEnabled(String provider) {
    Log.v("LOCATION", "onProviderEnabled: " + provider);
}
```

最后，已注册`LocationListener`中的`onStatusChanged`方法会在位置提供者的状态发生变化时被调用。`LocationProvider`中有三个常量可以与`status`变量进行比较，用于判断发生的更改。这些常量分别是：`AVAILABLE`（当提供者在不可用一段时间后恢复可用时调用）、`TEMPORARILY_UNAVAILABLE`（顾名思义，提供者暂时无法使用，因为它无法获取当前位置），以及`OUT_OF_SERVICE`（表示提供者可能因连接或信号丢失而无法使用）。

```
public void onStatusChanged(String provider, int status, Bundle extras) {
    Log.v("LOCATION", "onStatusChanged: " + provider + " status:" + status);
    if (status == LocationProvider.AVAILABLE) {
        Log.v("LOCATION", "Provider Available");
    } else if (status == LocationProvider.TEMPORARILY_UNAVAILABLE) {
        Log.v("LOCATION", "Provider Temporarily Unavailable");
    } else if (status == LocationProvider.OUT_OF_SERVICE) {
        Log.v("LOCATION", "Provider Out of Service");
    }
}
```

以下是上述活动所需的布局 XML。

```
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <TextView
        android:layout_width="fill_parent"
        android:layout_height="wrap_content"
        android:text="@string/hello"
        android:id="@+id/location" />
</LinearLayout>
```

访问位置需要请求权限，因此我们需要在`AndroidManifest.xml`文件中添加以下`uses-permission`标签。请注意，以下标签用于`LocationManager.NETWORK_PROVIDER`提供者，它提供粗略位置。

```
<uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION">
</uses-permission>
```

如果我们希望使用更精确的 GPS 定位，则需要使用`ACCESS_FINE_LOCATION`权限。

```
<uses-permission android:name="android.permission.ACCESS_FINE_LOCATION">
</uses-permission>
```

### 使用 JSON 和位置拉取 Flickr 图片

我们可以更新 Flickr JSON 示例，通过请求`LocationManager`的位置变化，并在收到位置通知时执行请求，将位置信息整合进来。当然，我们还需要将位置信息添加到请求中，Flickr 支持在请求的查询字符串中包含位置参数。

```
package com.apress.proandroidmedia.ch12.flickrjsonlocation;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStream;
import java.io.InputStreamReader;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;

import org.apache.http.HttpEntity;
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.DefaultHttpClient;
import org.json.JSONArray;
import org.json.JSONObject;

import android.app.Activity;
import android.content.Context;
import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.location.Location;
import android.location.LocationListener;
import android.location.LocationManager;
import android.os.Bundle;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.BaseAdapter;
import android.widget.ImageView;
import android.widget.ListView;
import android.widget.TextView;
```



```java
public class FlickrJSONLocation extends Activity implements LocationListener {

    public static final String API_KEY = "YOUR_API_KEY";

    FlickrPhoto[] photos;
    TextView tv;
    LocationManager lm;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);
        tv = (TextView) findViewById(R.id.TextView);
        tv.setText("正在查找位置");
    }
}
```

我们将让 `FlickrJSONLocation` 活动实现 `LocationListener`，以便能够接收位置变化的通知。

我们不会直接向 Flickr 发送请求，而是先创建一个 `LocationManager` 实例，并调用 `requestLocationUpdates` 方法，将我们的活动注册为 `LocationListener`，从而指定我们需要获取位置信息。我们指定最多每 60 秒更新一次，且移动距离至少为 500 米。

```java
lm = (LocationManager) getSystemService(Context.LOCATION_SERVICE);
lm.requestLocationUpdates(LocationManager.NETWORK_PROVIDER, 60000l, 500.0f, this);
```

```java
public void onPause() {
    super.onPause();
    lm.removeUpdates(this);
}
```

现在，当我们的 `onLocationChanged` 方法被调用时，我们将向 Flickr 发出请求，并利用通过 `Location` 对象传入的位置信息。

```java
public void onLocationChanged(Location location) {
    tv.setText(location.getLatitude() + " " + location.getLongitude());
    Log.v("LOCATION", "onLocationChanged: lat=" + location.getLatitude() + ", lon=" + location.getLongitude());

    HttpClient httpclient = new DefaultHttpClient();
}
```

我们将构造请求的 URL，并添加几个额外参数：`lat` 表示纬度，`lon` 表示经度，`accuracy` 是一个数字，表示返回结果的经纬度范围。根据 Flickr API 文档，值为 1 表示全球范围，6 表示"区域"，11 大约对应一个城市，16 大约对应一条街道。此外，我们还指定了两个标签 `"halloween"` 和 `"dog"`，按照 Flickr API 文档的要求用逗号分隔。

```java
String url = "http://api.flickr.com/services/rest/?method=flickr.photos.search&tags=dog,halloween&format=json&api_key=" + API_KEY + "&per_page=5&nojsoncallback=1&accuracy=6&lat=" + location.getLatitude() + "&lon=" + location.getLongitude();

HttpGet httpget = new HttpGet(url);
HttpResponse response;

try {
    response = httpclient.execute(httpget);
    HttpEntity entity = response.getEntity();

    if (entity != null) {
        InputStream inputstream = entity.getContent();
        BufferedReader bufferedreader = new BufferedReader(new InputStreamReader(inputstream));
        StringBuilder stringbuilder = new StringBuilder();
        String currentline = null;

        try {
            while ((currentline = bufferedreader.readLine()) != null) {
                stringbuilder.append(currentline + "\n");
            }
        } catch (IOException e) {
            e.printStackTrace();
        }

        String result = stringbuilder.toString();
        JSONObject thedata = new JSONObject(result);
        JSONObject thephotosdata = thedata.getJSONObject("photos");
        JSONArray thephotodata = thephotosdata.getJSONArray("photo");

        photos = new FlickrPhoto[thephotodata.length()];

        for (int i = 0; i < thephotodata.length(); i++) {
            JSONObject photodata = thephotodata.getJSONObject(i);
            photos[i] = new FlickrPhoto(
                photodata.getString("id"),
                photodata.getString("owner"),
                photodata.getString("secret"),
                photodata.getString("server"),
                photodata.getString("title"),
                photodata.getString("farm")
            );
            Log.v("URL", photos[i].makeURL());
        }

        inputstream.close();
    }
} catch (Exception e) {
    e.printStackTrace();
}

ListView listView = (ListView) this.findViewById(R.id.ListView);
listView.setAdapter(new FlickrGalleryAdapter(this, photos));
```

当然，由于我们实现了 `LocationListener`，我们需要提供相应的实现。



`onProviderDisabled()` 和 `onProviderEnabled()` 方法。此处它们为空方法。在你的应用程序中，你可能希望向用户通知这些事件的发生，以解释为何位置更新已停止或开始工作。

```java
public void onProviderDisabled(String provider) {

}

public void onProviderEnabled(String provider) {

}

public void onStatusChanged(String provider, int status, Bundle extras) {

}
```

示例中的其余代码与之前展示的相同。我们将使用一个名为 `FlickrGalleryAdapter` 的类，用来自 `Flickr` 的结果填充 `ListView`。

```java
class FlickrGalleryAdapter extends BaseAdapter {

    private Context context;
    private FlickrPhoto[] photos;
    LayoutInflater inflater;

    public FlickrGalleryAdapter(Context context, FlickrPhoto[] items) {
        context = _context;
        photos = _items;
        inflater = (LayoutInflater) context
                .getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    }

    public int getCount() {
        return photos.length;
    }

    public Object getItem(int position) {
        return photos[position];
    }

    public long getItemId(int position) {
        return position;
    }

    public View getView(int position, View convertView, ViewGroup parent) {
        View videoRow = inflater.inflate(R.layout.list_item, null);
        ImageView image = (ImageView) videoRow.findViewById(R.id.ImageView);
        image.setImageBitmap(imageFromUrl(photos[position].makeURL()));
        TextView videoTitle = (TextView) videoRow
                .findViewById(R.id.TextView);
        videoTitle.setText(photos[position].title);
        return videoRow;
    }

    public Bitmap imageFromUrl(String url) {
        Bitmap bitmapImage;
        URL imageUrl = null;
        try {
            imageUrl = new URL(url);
        } catch (MalformedURLException e) {
            e.printStackTrace();
        }
        try {
            HttpURLConnection httpConnection =
                    (HttpURLConnection) imageUrl.openConnection();
            httpConnection.setDoInput(true);
            httpConnection.connect();
            int length = httpConnection.getContentLength();
            InputStream is = httpConnection.getInputStream();
            bitmapImage = BitmapFactory.decodeStream(is);
        } catch (IOException e) {
            e.printStackTrace();
            bitmapImage = Bitmap.createBitmap(10, 10, Bitmap.Config.ARGB_8888);
        }
        return bitmapImage;
    }
}
```

最后，与前面的示例一样，我们有一个 `FlickrPhoto` 类，用于保存 `Flickr` 通过 JSON 发送给我们的每张照片的数据。

```java
class FlickrPhoto {
    String id;
    String owner;
    String secret;
    String server;
    String title;
    String farm;

    public FlickrPhoto(String id, String owner, String secret,
                       String server, String title, String farm) {
        id = _id;
        owner = _owner;
        secret = _secret;
        server = _server;
        title = _title;
        farm = _farm;
    }

    public String makeURL() {
        return "http://farm" + farm + ".static.flickr.com/" + server + "/"
                + id + "_" + secret + "_m.jpg";
        // http://farm{farm-id}.static.flickr.com/{server-id}/{id}_{secret}_[mstzb].jpg
    }
}
```

以下是示例所用的 `main.xml` 布局文件。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">

    <TextView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/TextView"></TextView>

    <ListView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/ListView"></ListView>
</LinearLayout>
```

以下是示例中用于 `ListView` 布局的 `list_item.xml` 文件。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    android:layout_width="wrap_content"
    android:layout_height="wrap_content">

    <ImageView
        android:id="@+id/ImageView"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"></ImageView>

    <TextView
        android:text="@+id/TextView01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/TextView"></TextView>
</LinearLayout>
```


好的，作为一名高级文档工程师和翻译员，我将严格按照您提供的注意事项和格式要求，将给定的英文文本翻译成中文。


当然，在此示例中，我们需要指定访问互联网和使用位置的权限。在添加了相应的 `uses-permission` 标签后，此示例的 `AndroidManifest.xml` 文件如下所示：

```
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.apress.proandroidmedia.ch12.flickrjsonlocation"
    android:versionCode="1"
    android:versionName="1.0">

    <application android:icon="@drawable/icon" android:label="@string/app_name">
        <activity android:name=".FlickrJSONLocation"
                  android:label="@string/app_name">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

    <uses-sdk android:minSdkVersion="4" />

    <uses-permission android:name="android.permission.ACCESS_COARSE_LOCATION" />
    <uses-permission android:name="android.permission.INTERNET" />

</manifest>
```

正如我们所看到的，仅仅在应用程序中关注位置信息，就能让我们创建出非常动态的体验。在此例中，当用户四处移动时，他/她会看到通过 Flickr 提供的一系列全新的“狗，万圣节”照片，如图 12-2 所示。

**图 12-2.** *显示来自 Flickr、在我当前位置附近拍摄并标记了“dog”和“halloween”的图像*

现在，让我们把注意力转回到网络服务协议上，讨论一下 REST。

## REST

REST 全称是 Representational State Transfer（表述性状态转移）。它是一套用于设计客户端-服务器服务的架构原则。通常，一个网络服务在满足以下条件时，会被认为是“RESTful”的，即遵循 REST 原则：

-   使用 HTTP 方法（`GET`、`POST`）
-   是无状态的，这意味着每个事务都独立于其他事务
-   使用目录风格的 URL 来传递数据，而不是查询字符串变量（使用 `www.afakeurl.com/shawn/van_every` 而不是 `www.afakeurl.com/?firstname=shawn&lastname=van_every`）
-   使用 XML（或 JSON）来传输数据

要了解更多关于基于 REST 的网络服务架构，可以参阅 IBM developerWorks 上 Alex Rodriguez 撰写的一篇文章，题为“RESTful Web Services: The Basics”：[www.ibm.com/developerworks/webservices/library/ws-restful/](http://www.ibm.com/developerworks/webservices/library/ws-restful/)

我们在这里讨论 REST 的原因是，它通常与 XML 结合使用来传输网络服务数据。尽管在 Flickr 的示例中我们没有使用 XML 选项，而是选择了 JSON，但我们本也可以那么做。待传输数据的 XML 表示结构无需遵循严格的文档类型定义（DTD）或 XML 模式，通常由构建网络服务的那些服务根据需求创建和记录。

### 在 XML 中表示数据

以下是一个 XML 文档示例，它在理论上的网络服务中定义了一个“用户”。该文档是对一个使用 `user-id` 查询用户信息请求的响应。

```
<?xml version="1.0"?>
<user>
    <user-id>15</user-id>
    <username>vanevery</username>
    <firstname>Shawn</firstname>
    <lastname>Van Every</lastname>
</user>
```

Android 默认提供几种不同的 XML 解析方式。这些方式包括两种主要方法： SAX（Simple API for XML，XML 简单应用程序接口）和 DOM（Document Object Model，文档对象模型），以及其他方法。在移动设备上，SAX 通常比 DOM 更受青睐，因为它按顺序读取 XML，允许在读取 XML 的同时对其进行操作。



