# 第十章：高级视频

我们使用了一个 `do while` 循环，目的是让它在移动到下一行数据之前，先处理第一行数据。`do` 部分会在 `while` 子句被测试/执行之前执行。在我们的循环中，会为返回的每一行数据创建一个新的 `VideoViewInfo` 对象。

```
do {
    VideoViewInfo newVVI = new VideoViewInfo();
```

然后，我们可以从 `Cursor` 中提取所有相关数据。如上所述，我们还会发起另一个查询来获取每个视频的缩略图。这些数据中的每一项都将存储在 `VideoViewInfo` 对象中。

```
    int id = cursor.getInt(cursor.getColumnIndex(MediaStore.Video.Media._ID));
    Cursor thumbCursor = managedQuery(MediaStore.Video.Thumbnails.EXTERNAL_CONTENT_URI,
                                      thumbColumns,
                                      MediaStore.Video.Thumbnails.VIDEO_ID + "=" + id,
                                      null, null);
    if (thumbCursor.moveToFirst()) {
        newVVI.thumbPath = thumbCursor.getString(
            thumbCursor.getColumnIndex(MediaStore.Video.Thumbnails.DATA));
        Log.v("VideoGallery", "Thumb " + newVVI.thumbPath);
    }
    newVVI.filePath = cursor.getString(
        cursor.getColumnIndexOrThrow(MediaStore.Video.Media.DATA));
    newVVI.title = cursor.getString(
        cursor.getColumnIndexOrThrow(MediaStore.Video.Media.TITLE));
    Log.v("VideoGallery", "Title " + newVVI.title);
    newVVI.mimeType = cursor.getString(
        cursor.getColumnIndexOrThrow(MediaStore.Video.Media.MIME_TYPE));
    Log.v("VideoGallery", "Mime " + newVVI.mimeType);
```

最后，我们将 `VideoViewInfo` 添加到 `videoRows` 这个 `ArrayList` 中。

```
    videoRows.add(newVVI);
} while (cursor.moveToNext());
```

一旦获取完所有视频，我们就可以继续后续操作。我们将 `ListView` 对象的适配器设置为 `VideoGalleryAdapter` 的一个新实例，该类是此处定义的一个内部类。我们还将此活动设置为 `ListView` 的 `OnItemClickListener`。

```
listView.setAdapter(new VideoGalleryAdapter(this, videoRows));
listView.setOnItemClickListener(this);
```

当点击 `ListView` 中的某一项时，`onItemClick` 方法会被调用。在该方法中，我们从 `Cursor` 中提取所需数据，并创建一个 `Intent` 来启动设备上的默认媒体播放器应用以播放该视频。我们本可以创建自己的 `MediaPlayer` 或在此处使用 `VideoView` 类来替代。

```
public void onItemClick(AdapterView<?> l, View v, int position, long id) {
    if (cursor.moveToPosition(position)) {
        int fileColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.DATA);
        int mimeColumn = cursor.getColumnIndexOrThrow(MediaStore.Video.Media.MIME_TYPE);
        String videoFilePath = cursor.getString(fileColumn);
        String mimeType = cursor.getString(mimeColumn);
        Intent intent = new Intent(android.content.Intent.ACTION_VIEW);
        File newFile = new File(videoFilePath);
        intent.setDataAndType(Uri.fromFile(newFile), mimeType);
        startActivity(intent);
    }
}
```

接下来是一个非常基础的 `VideoViewInfo` 类，用于保存关于每个返回视频的信息。

```
class VideoViewInfo {
    String filePath;
    String mimeType;
    String thumbPath;
    String title;
}
```

由于我们在活动中使用 `ListView` 来显示从 `MediaStore` 查询返回的每个视频，我们将使用 `ListView` 来显示视频的标题和缩略图。为了将数据交给 `ListView`，我们需要构建一个适配器。接下来，我们创建一个继承自 `BaseAdapter` 的适配器 `VideoGalleryAdapter`。

当此类被构造时，它会接收到一个保存了所有从 `MediaStore` 查询返回视频的 `ArrayList`。

`BaseAdapter` 是一个抽象类，因此要扩展它，我们需要实现几个方法。它们中的大多数都很简单，仅对传入的 `ArrayList` 进行操作，例如 `getCount` 和 `getItem`。最需要关注的方法是 `getView` 方法。

```
class VideoGalleryAdapter extends BaseAdapter {
    private Context context;
    private List<VideoViewInfo> videoItems;
    LayoutInflater inflater;

    public VideoGalleryAdapter(Context context, ArrayList<VideoViewInfo> items) {
        context = context;
        videoItems = items;
        inflater = (LayoutInflater) context.getSystemService(Context.LAYOUT_INFLATER_SERVICE);
    }

    public int getCount() {
        return videoItems.size();
    }

    public Object getItem(int position) {
        return videoItems.get(position);
    }

    public long getItemId(int position) {
        return position;
    }
```

`getView` 方法用于返回 `ListView` 中每一行所代表的视图。它接收一个表示要返回行的位置参数（以及一个表示当前视图的 `View` 对象和一个表示父 `ViewGroup` 的对象）。

```
    public View getView(int position, View convertView, ViewGroup parent) {
```

为了构造要返回的视图，我们需要对用于每一行的布局进行填充。本例中，我们使用的布局定义在 `list_item.xml` 中（此处已展示）。

```
        View videoRow = inflater.inflate(R.layout.list_item, null);
```

布局填充完成后，我们可以获取其中定义的各个视图，并使用 `VideoViewInfo` 对象的 `ArrayList` 中的数据来定义显示内容。以下是针对用于显示每个视频缩略图的 `ImageView` 的处理方式。

```
        ImageView videoThumb = (ImageView) videoRow.findViewById(R.id.ImageView);
        if (videoItems.get(position).thumbPath != null) {
            videoThumb.setImageURI(Uri.parse(videoItems.get(position).thumbPath));
        }
```

此处我们获取视频标题对应的 `TextView` 引用，并根据 `VideoViewInfo` 对象的 `ArrayList` 中的数据设置其文本。

```
        TextView videoTitle = (TextView) videoRow.findViewById(R.id.TextView);
        videoTitle.setText(videoItems.get(position).title);
```

最后，我们返回新构造的视图。

```
        return videoRow;
    }
}
```

以下是定义活动布局的 `main.xml` 文件。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="fill_parent"
    android:layout_height="fill_parent">
    <ListView
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:id="@+id/ListView"></ListView>
</LinearLayout>
```

以下是 `list_item.xml` 文件，用于定义 `ListView` 每一行的布局。

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout
    xmlns:android="http://schemas.android.com/apk/res/android"
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

**图 10–1.** *VideoGallery 活动界面*

你会注意到，图 10–1 中我们示例显示的缩略图尺寸不同。它们是由 `MediaScanner` 服务创建的，大小与视频本身一致。为了以统一尺寸显示缩略图，我们可以调整 `list_item.xml` 中 `ImageView` 元素的参数。

```xml
<ImageView
    android:id="@+id/ImageView"
    android:layout_width="50dip"
    android:layout_height="50dip"></ImageView>
```

现在，每个视频缩略图都将以 50 dip 乘以 50 dip 的尺寸显示，如图 10–2 所示。（术语 *dip* 代表“密度无关像素”。无论屏幕像素的分辨率或密度如何，160 dip 在显示屏上相当于 1 英寸。）



## 第十章：高级视频

**图 10-2.** *缩略图尺寸相同的 VideoGallery 活动*

## 网络视频

随着越来越多的媒体内容转移到互联网上，Android 提供良好的播放支持是合情合理的，事实上它也做到了。在本章的剩余部分，我们将探讨所支持的协议和视频格式的细节，以及如何利用网络视频。

### 支持的网络视频类型

Android 目前支持两种不同的网络视频传输协议。

#### HTTP

第一种是通过标准 HTTP 传输的媒体。由于 HTTP 在网络中得到广泛支持，且通常不像其他流媒体协议那样会遇到防火墙问题，因此大量媒体都通过这种方式提供。通过 HTTP 传输的媒体通常被称为渐进式下载。

Android 支持通过标准 Web 服务器以 HTTP 方式传输的 MPEG-4 和 3GP 文件中的点播媒体。目前，它不支持使用 Apple、Microsoft 或 Adobe 目前采用的新技术通过 HTTP 传输直播视频。

在准备用于渐进式下载的视频时，需要牢记以下几点。首先，媒体必须使用 Android 支持的编解码器并以支持的格式进行编码（有关 Android 支持的格式和编解码器的详细信息，请参阅第九章）。

有许多免费和商业工具可用于准备通过 HTTP 渐进式下载传输的媒体。以下是一些工具（排名不分先后）：QuickTime X、Adobe Media Encoder、HandBrake 和 VLC。QuickTime X 具有适用于 iPhone 编码的预设，这些预设对 Android 也适用。Adobe Media Encoder 具有适用于 iPod 的预设，似乎也同样有效。一般来说，如果某个软件具有适用于 iPhone 的预设，它们很可能也适用于 Android 设备。

其次，视频的比特率应在能够承载该视频的网络传输能力范围内。例如，GPRS 带宽可能低至 20 kbps，因此音频和视频的编码需要考虑到这一点。通常，在通过 HTTP 传输时，媒体会在设备上进行缓冲，当下载的数据量足以使播放能够直接进行到文件末尾而无需暂停等待更多媒体下载时，播放就会开始。如果媒体传输速度仅为 20 kbps，而媒体的编码速度为 400 kbps，这意味着用户观看每 1 秒的视频就需要下载 20 秒。这大概不太理想。

不过，如果用户使用的是 WiFi，那么 400 kbps 可能就足够了，并且与编码为 20 kbps 的视频相比，能提供画质更好的视频。通常，需要在所用网络的速度与视频质量之间进行权衡。使用 HTTP 渐进式下载的一个好处是它可以做到这一点：媒体不需要像我们接下来要讨论的 RTSP 那样实时传输。

最后，为了使视频能够边下载边播放，必须采用允许这样做的编码方式。具体来说，这意味着最终生成的文件应该在其开头包含所谓的 `moov` 原子。`moov` 原子包含了文件内容及其组织方式的索引。视频播放软件为了能够开始播放视频，需要知道这些信息。如果 `moov` 原子位于文件末尾，那么播放软件必须等到整个文件下载完成后才能开始播放，以便获取 `moov` 原子。

不幸的是，一些视频采集和编码工具不会自动执行此步骤。在某些情况下，这只是一个配置设置；在其他情况下，您可能需要手动执行此步骤。有一个名为 `qt-faststart` 的命令行应用程序已被开发出来，并移植到了许多不同的操作系统上，它也是一些 GUI 应用程序的基础。您可以访问 [`multimedia.cx/eggs/improving-qt-faststart/`](http://multimedia.cx/eggs/improving-qt-faststart) 了解相关信息并进行下载。

#### RTSP

Android 支持的第二种网络视频传输协议是 RTSP。RTSP 代表实时流媒体协议，从技术上讲，它并非媒体传输协议；相反，它是一种用于支持媒体传输的控制协议。



