# 进阶视频

在第 9 章中，我们了解了 Android 如何播放放置在设备 SD 卡上的特定视频文件。本章将进一步探讨如何访问由 `MediaStore` 提供的视频以及互联网上的视频。

## 通过 MediaStore 检索视频

如第 1 章所述，Android 提供了一种跨应用共享数据的标准方式。`ContentProvider` 类是实现此功能的基类。同样，为媒体扩展 `ContentProvider` 概念的各种 `MediaStore` 类也是如此。我们之前了解了如何使用 `MediaStore` 处理图像、音频及其相关元数据。用于视频的 `MediaStore` 工作方式与之类似。

`MediaStore.Video` 是 `MediaStore` 类中的嵌套类，专门用于处理视频文件。`MediaStore.Video` 中包含 `MediaStore.Video.Media`，后者定义了指定 `MediaStore` 中与视频媒体本身相关列的常量，其中许多常量继承自其他类（例如 `MediaStore.MediaColumns`）。此外，还有 `MediaStore.Video.Thumbnails`，它定义了 `MediaStore` 中用于存储与视频文件相关的缩略图图像的列常量。

要查询 `MediaStore` 以获取视频内容，我们使用常量 `MediaStore.Video.Media.EXTERNAL_CONTENT_URI` 中指定的 `Uri` 作为查询的数据源。

使用 `Activity` 类中提供的 `managedQuery` 方法时，我们还需要传入一个希望返回的列数组。此处指定的数组表示我们需要 `MediaStore` 中视频的唯一 ID，即 `MediaStore.Video.Media._ID`。接着是 `MediaStore.Video.Media.DATA`，即视频文件本身的路径。接下来的两列指定我们需要文件的标题和 MIME 类型。

```java
String[] mediaColumns = {
    MediaStore.Video.Media._ID,
    MediaStore.Video.Media.DATA,
    MediaStore.Video.Media.TITLE,
    MediaStore.Video.Media.MIME_TYPE
};
```

要查询 `MediaStore` 以获取视频内容，我们使用常量 `MediaStore.Video.Media.EXTERNAL_CONTENT_URI` 中指定的 `Uri` 作为查询的数据源。

```java
Cursor cursor = managedQuery(MediaStore.Video.Media.EXTERNAL_CONTENT_URI,
    mediaColumns, null, null, null);
```

作为返回，我们得到一个可以循环遍历并提取数据的游标。

```java
if (cursor.moveToFirst()) {
    do {
        Log.v("VideoGallery",
            cursor.getString(cursor.getColumnIndex(MediaStore.Video.Media.DATA)));
        Log.v("VideoGallery",
            cursor.getString(cursor.getColumnIndex(MediaStore.Video.Media.TITLE)));
        Log.v("VideoGallery",
            cursor.getString(cursor.getColumnIndex(MediaStore.Video.Media.MIME_TYPE)));
    } while (cursor.moveToNext());
}
```

## 从 MediaStore 获取视频缩略图

从 Android 2.0（API 级别 5）开始，我们可以在循环中提取与每个视频文件关联的缩略图。我们需要选择列中视频文件的 ID（`MediaStore.Video.Media._ID`），然后将其用于 `managedQuery` 的“where”子句中。

```java
int id = cursor.getInt(cursor.getColumnIndex(MediaStore.Video.Media._ID));

String[] thumbColumns = { MediaStore.Video.Thumbnails.DATA,
    MediaStore.Video.Thumbnails.VIDEO_ID };

Cursor thumbCursor = managedQuery(MediaStore.Video.Thumbnails.EXTERNAL_CONTENT_URI,
    thumbColumns, MediaStore.Video.Thumbnails.VIDEO_ID + "=" + id, null, null);
if (thumbCursor.moveToFirst()) {
    Log.v("VideoGallery", thumbCursor.getString(thumbCursor.getColumnIndex(MediaStore.Video.Thumbnails.DATA)));
}
```

## 完整的 MediaStore 视频示例

以下是一个完整的示例，它从 `MediaStore` 中检索所有可用的视频文件，并显示每个文件的缩略图图像和标题。图 10-1 显示了以下示例的运行情况。此示例使用 `MediaStore.Video.Thumbnails` 类，该类在 Android 2.0（API 级别 5）及以上版本中可用。

```java
package com.apress.proandroidmedia.ch10.videogallery;

import java.io.File;
import java.util.ArrayList;
import java.util.List;

import android.app.Activity;
import android.content.Context;
import android.content.Intent;
import android.database.Cursor;
import android.net.Uri;
import android.os.Bundle;
import android.provider.MediaStore;
import android.util.Log;
import android.view.LayoutInflater;
import android.view.View;
import android.view.ViewGroup;
import android.widget.AdapterView;
import android.widget.BaseAdapter;
import android.widget.ImageView;
import android.widget.ListView;
import android.widget.TextView;
import android.widget.AdapterView.OnItemClickListener;

public class VideoGallery extends Activity implements OnItemClickListener {

    Cursor cursor;

    @Override
    public void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.main);

        // 我们将使用 ListView 来显示视频列表。
        ListView listView = (ListView) this.findViewById(R.id.ListView);

        // 接下来是我们要从 MediaStore.Video.Thumbnails 查询中获取的列列表。
        String[] thumbColumns = {
            MediaStore.Video.Thumbnails.DATA,
            MediaStore.Video.Thumbnails.VIDEO_ID
        };

        // 然后是我们要从 MediaStore.Video.Media 查询中获取的列列表。
        String[] mediaColumns = {
            MediaStore.Video.Media._ID,
            MediaStore.Video.Media.DATA,
            MediaStore.Video.Media.TITLE,
            MediaStore.Video.Media.MIME_TYPE
        };

        // 在主查询中，我们将选择 MediaStore 中表示的所有视频。
        cursor = managedQuery(MediaStore.Video.Media.EXTERNAL_CONTENT_URI,
            mediaColumns, null, null, null);

        // 查询返回的每一行都将在以下 ArrayList 中创建一个项目。
        // 每个项目都是一个 VideoViewInfo 对象，这是一个专门在此处定义的类，
        // 用于保存视频信息，供此 Activity 使用。
        ArrayList<VideoViewInfo> videoRows = new ArrayList<VideoViewInfo>();

        // 在这里我们循环遍历 Cursor 对象中包含的数据，
        // 为每一行创建一个 VideoViewInfo 对象并将其添加到 ArrayList 中。
        if (cursor.moveToFirst())
        {
            // ...（循环实现继续）
        }
    }
}
```



