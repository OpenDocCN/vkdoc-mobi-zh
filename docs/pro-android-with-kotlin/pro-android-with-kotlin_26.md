# 访问系统 Content Provider

Android 操作系统（包括预装的应用）提供了多个内容提供者组件。在在线 API 文档中，内容提供者的契约类可以在 “android.provider/Classes” 下找到。以下章节总结了它们是什么以及如何访问它们。

## BlockedNumberContract

公开一个包含被阻止号码的表。只有系统、默认电话应用、默认短信应用和运营商应用可以访问此表——但 `canCurrentUserBlockNumbers()` 方法除外，任何应用都可以调用它。使用示例：

```
val values = ContentValues()
values.put(BlockedNumbers.COLUMN_ORIGINAL_NUMBER,
    "1234567890")
Uri uri = contentResolver.insert(
    BlockedNumbers.CONTENT_URI, values)
```

## CalendarContract

一个相当复杂的内容提供者，包含多个表。作为示例，我们访问日历列表并添加一个事件：



```kotlin
val havePermissions =
    ContextCompat.checkSelfPermission(this,
        Manifest.permission.WRITE_CALENDAR)
        == PackageManager.PERMISSION_GRANTED
        && ContextCompat.checkSelfPermission(this,
        Manifest.permission.READ_CALENDAR)
        == PackageManager.PERMISSION_GRANTED
if(!havePermissions) {
    // 获取权限...
} else {
    data class CalEntry(val name: String, val id: String)
    val calendars = HashMap()
    val uri = CalendarContract.Calendars.CONTENT_URI
    val cursor = contentResolver.query(
        uri, null, null, null, null)
    cursor.moveToFirst()
    while (!cursor.isAfterLast) {
        val calName = cursor.getString(
            cursor.getColumnIndex(
                CalendarContract.Calendars.NAME))
        val calId = cursor.getString(
            cursor.getColumnIndex(
                CalendarContract.Calendars._ID))
        calendars[calName] = CalEntry(calName, calId)
        cursor.moveToNext()
    }
    Log.e("LOG", calendars.toString())
    val calId = "4" // 你应从映射中获取合适的条目！
    val year = 2018
    val month = Calendar.AUGUST
    val dayInt = 27
    val hour = 8
    val minute = 30
    val beginTime = Calendar.getInstance()
    beginTime.set(year, month, dayInt, hour, minute)
    val event = ContentValues()
    event.put(CalendarContract.Events.CALENDAR_ID,
        calId)
    event.put(CalendarContract.Events.TITLE,
        "MyEvent")
    event.put(CalendarContract.Events.DESCRIPTION,
        "This is test event")
    event.put(CalendarContract.Events.EVENT_LOCATION,
        "School")
    event.put(CalendarContract.Events.DTSTART,
        beginTime.getTimeInMillis())
    event.put(CalendarContract.Events.DTEND,
        beginTime.getTimeInMillis())
    event.put(CalendarContract.Events.ALL_DAY,
        0)
    event.put(CalendarContract.Events.RRULE,
        "FREQ=YEARLY")
    event.put(CalendarContract.Events.EVENT_TIMEZONE,
        "Germany")
    val retUri = contentResolver.insert(
        CalendarContract.Events.CONTENT_URI, event)
    Log.e("LOG", retUri.toString())
}
```

我们并未实现权限查询——权限的详细说明见第 7 章。

## 通话记录

这是一个存储已拨和已接电话的表格。下面展示一个查询该表格的示例：

```kotlin
val havePermissions =
    ContextCompat.checkSelfPermission(this,
        Manifest.permission.READ_CALL_LOG)
        == PackageManager.PERMISSION_GRANTED
        && ContextCompat.checkSelfPermission(this,
        Manifest.permission.WRITE_CALL_LOG)
        == PackageManager.PERMISSION_GRANTED
if(!havePermissions) {
    // 获取权限...
} else {
    val uri = CallLog.Calls.CONTENT_URI
    val cursor = contentResolver.query(
        uri, null, null, null, null)
    cursor.moveToFirst()
    while (!cursor.isAfterLast) {
        Log.e("LOG", "新条目:")
        for(name in cursor.columnNames) {
            val v = cursor.getString(
                cursor.getColumnIndex(name))
            Log.e("LOG","  > " + name + " = " + v)
        }
        cursor.moveToNext()
    }
}
```

我们并未实现权限查询——权限的详细说明见第 7 章。

表格的列信息列在表 6-3 中。

**表 6-3**  
通话记录表格列

| 列名 | 描述 |
|------|-------------|
|`date`| 通话日期，以自纪元起的毫秒数表示。|
|`transcription`| 通话或语音邮件的转录文本。|
|`photo_id`| 关联照片的缓存照片 ID。|
|`subscription_component_name`| 用于拨打或接听电话的账户组件名称。|
|`type`| 通话类型。可选值（对应`CallLog.Calls`中的常量名）：`INCOMING_TYPE`、`OUTGOING_TYPE`、`MISSED_TYPE`、`VOICEMAIL_TYPE`、`REJECTED_TYPE`、`BLOCKED_TYPE`、`ANSWERED_EXTERNALLY_TYPE`。|
|`geocoded_location`| 此通话关联号码的地理编码位置。|
|`presentation`| 网络设置的号码显示规则。可选值（对应`CallLog.Calls`中的常量名）：`PRESENTATION_ALLOWED`、`PRESENTATION_RESTRICTED`、`PRESENTATION_UNKNOWN`、`PRESENTATION_PAYPHONE`。|
|`duration`| 通话持续时间（秒）。|
|`subscription_id`| 用于拨打或接听电话的账户标识符。|
|`is_read`| 用户是否已读取或处理此条目（0 = 否，1 = 是）。|
|`number`| 用户输入的原始电话号码。|



`features` | 位掩码，描述通话的特性，由（`CallLog.Calls` 中的常量名称）构成：`FEATURES_HD_CALL`：高清通话。`FEATURES_PULLED_EXTERNALLY`：通话被外部拉取。`FEATURES_VIDEO`：视频通话。`FEATURES_WIFI`：WIFI 通话。 |

`voicemail_uri` | 语音邮件条目的 URI（如果适用）。 |

`normalized_number` | 电话号码的规范化（E164）缓存版本（如果存在）。 |

`via_number` | 对于来电，表示接听该通话的辅助线路号码。当一张 SIM 卡关联了多个电话号码时，via number 指示呼叫的是 SIM 卡关联的哪个号码。 |

`matched_number` | 与此条目匹配的联系人的缓存电话号码（如果存在）。 |

`last_modified` | 该行最后被插入、更新或标记为删除的日期。单位是自纪元以来的毫秒数。只读。 |

`new` | 通话是否已被确认（0 = 否，1 = 是）。 |

`numberlabel` | 电话号码关联的自定义号码类型的缓存号码标签（如果存在）。 |

`lookup_uri` | 用于查找与该电话号码关联的联系人的缓存 URI（如果存在）。 |

`photo_uri` | 与该电话号码关联的图片的缓存 URI（如果存在）。 |

`data_usage` | 通话的数据使用量，以字节为单位。 |

`phone_account_address` | 未记录。 |

`formatted_number` | 缓存的电话号码，根据用户拨打或接听通话时所处国家的规则格式化。 |

`add_for_all_users` | 未记录。 |

`numbertype` | 与电话号码关联的缓存号码类型（如果适用）。取值为（`CallLog.Calls` 中的常量名称）：`INCOMING_TYPE` `OUTGOING_TYPE` `MISSED_TYPE` `VOICEMAIL_TYPE` `REJECTED_TYPE` `BLOCKED_TYPE` `ANSWERED_EXTERNALLY_TYPE` |

`countryiso` | 用户接收或拨打电话所在国家的 ISO 3166-1 二位字母国家代码。 |

`name` | 与电话号码关联的缓存名称（如果存在）。 |

`post_dial_digits` | 拨号号码的后拨号部分。 |

`transcription_state` | 未记录。 |

`_id` | 表条目的（技术性）ID。 |

## `ContactsContract`

这是一个描述电话联系人的复杂协议。联系信息存储在一个三层数据模型中：

* **`ContactsContract.Data`** – 任何类型的个人数据。
* **`ContactsContract.RawContacts`** – 描述一个人的数据集。
* **`ContactsContract.Contacts`** – 对一个人的聚合视图，可能关联到 `RawContacts` 表中的多行。由于其聚合特性，它只能部分写入。

还有更多与协议相关的表，作为 `ContactsContract` 的内部类进行了描述。为了让你能快速上手，我们不会解释联系人内容提供器的所有可能用例，而是直接提供代码来列出前面提到的三个主要表的内容，展示创建一个新联系人在这些表中写入的内容，除此之外，请参考 `ContactsContract` 类的在线文档。要列出这三个表的内容，请使用：

```
fun showTable(tbl:Uri) {
    Log.e("LOG", "##################################")
    Log.e("LOG", tbl.toString())
    val cursor = contentResolver.query(
        tbl, null, null, null, null)
    cursor.moveToFirst()
    while (!cursor.isAfterLast) {
        Log.e("LOG", "New entry:")
        for(name in cursor.columnNames) {
            val v = cursor.getString(
                cursor.getColumnIndex(name))
            Log.e("LOG","  > " + name + " = " + v)
        }
        cursor.moveToNext()
    }
}
...
showTable(ContactsContract.Contacts.CONTENT_URI)
showTable(ContactsContract.RawContacts.CONTENT_URI)
showTable(ContactsContract.Data.CONTENT_URI)
```

如果你使用 Android 预装的通讯录应用创建一个新联系人，在 `Contacts` 视图表中，你会找到以下新条目（仅显示重要列）：

```
_id = 1
display_name_alt = Mayer, Hugo
sort_key_alt = Mayer, Hugo
has_phone_number = 1
contact_last_updated_timestamp = 1518451432615
display_name = Hugo Mayer
sort_key = Hugo Mayer
times_contacted = 0
name_raw_contact_id = 1
```

在 `RawContacts` 表中，作为关联条目，你将找到如下内容（以及其他）：

```
_id = 1
account_type = com.google
contact_id = 1
display_name_alt = Mayer, Hugo
sort_key_alt = Mayer, Hugo
account_name = pmspaeth1111@gmail.com
display_name = Hugo Mayer
sort_key = Hugo Mayer
times_contacted = 0
account_type_and_data_set = com.google
```

显然，你也会在之前列出的 `Contacts` 视图中找到这些条目中的许多。在 `Data` 表中关联了零到多个条目（仅显示最重要的部分）：

```
Entry:
_id = 3
mimetype = vnd.android.cursor.item/phone_v2
raw_contact_id = 1
contact_id = 1
data1 = (012) 345-6789
Entry:
_id = 4
mimetype = vnd.android.cursor.item/phone_v2
raw_contact_id = 1
contact_id = 1
data1 = (098) 765-4321
Entry:
_id = 5
mimetype = vnd.android.cursor.item/email_v2
raw_contact_id = 1
contact_id = 1
data1 = null
Entry:
_id = 6
mimetype = vnd.android.cursor.item/name
raw_contact_id = 1
contact_id = 1
data3 = Mayer
data2 = Hugo
data1 = Hugo Mayer
Entry:
_id = 7
mimetype = vnd.android.cursor.item/nickname
raw_contact_id = 1
contact_id = 1
data1 = null
Entry:
_id = 8
mimetype = vnd.android.cursor.item/note
raw_contact_id = 1
contact_id = 1
data1 = null
```

你可以看到 `Data` 表中的行对应于图形界面中的编辑字段——你会看到两个电话号码、名和姓、无昵称和无电子邮件地址。

## `DocumentsContract`

这与我们这里看到的其他协议的意义不同——它对应于 `android.provider.DocumentsProvider`，后者是 `android.content.ContentProvider` 的子类。我们将在接下来的“文档提供器”部分处理文档提供器。

## `FontsContract`

这是一个处理可下载字体的协议，与内容提供器无关。

### `MediaStore`

媒体存储处理内部和外部存储设备上所有媒体相关文件的元数据。这包括音频文件、图像和视频。除此之外，它还以不区分用途的方式处理文件——这意味着媒体文件以及以任何方式与媒体文件相关的非媒体文件。根类 `android.provider.MediaStore` 本身不包含内容提供器特定的资产，但以下内部类包含：

* **`MediaStore.Audio`** – 音频文件。包含更多用于音乐专辑、艺术家、音频文件本身、流派和播放列表的内部类。
* **`MediaStore.Images`** – 图像。
* **`MediaStore.Videos`** – 视频。
* **`MediaStore.Files`** – 通用文件。

你可以通过浏览在线 API 文档来研究任何媒体存储表。对于你自己的实验，你可以从整体上研究这些表，方法是查找常量 `EXTERNAL_CONTENT_URI` 和 `INTERNAL_CONTENT_URI`，或者方法 `getContentUri()`，然后将它们传入我们之前使用的相同代码：

```
showTable(MediaStore.Audio.Media.getContentUri(
    "internal"))  //  " + name + " = " + v)
}
cursor.moveToNext()
}
}
```

## `Settings`

这是一个处理各种全局和系统级设置的内容提供器。以下是来自协议类的主要 URI 常量：

* **`Settings.Global.CONTENT_URI`** – 全局设置。所有条目都是三元组：
    * `_id`
    * `android.provider.Settings.NameValueTable.NAME`
    * `android.provider.Settings.NameValueTable.VALUE`

* **`Settings.System.CONTENT_URI`** – 全局系统级设置。所有条目都是三元组：
    * `_id`
    * `android.provider.Settings.NameValueTable.NAME`
    * `android.provider.Settings.NameValueTable.VALUE`

* **`Settings.Secure.CONTENT_URI`** – 安全系统设置——应用不允许修改它们。所有条目都是三元组：
    * `_id`
    * `android.provider.Settings.NameValueTable.NAME`
    * `android.provider.Settings.NameValueTable.VALUE`



