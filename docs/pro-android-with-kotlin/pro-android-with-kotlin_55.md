# NFC 开发指南

为了编写合适的意图过滤器（intent filter），请参见第 3 章的“意图过滤器”部分，此外，对于“tech”风格的发现，你需要在`<activity>`内添加一个特定的`<meta-data>`元素，如下所示：

```xml
<meta-data android:name="android.nfc.action.TECH_DISCOVERED"
    android:resource="@xml/nfc_tech_filter" />
```

该元素指向`res/xml`内的`nfc_tech_filter.xml`文件，其中包含：

```xml
<resources>
    <tech-list>
        <tech>android.nfc.tech.IsoDep</tech>
        <tech>android.nfc.tech.NfcA</tech>
        <tech>android.nfc.tech.NfcB</tech>
        <tech>android.nfc.tech.NfcF</tech>
        <tech>android.nfc.tech.NfcV</tech>
        <tech>android.nfc.tech.Ndef</tech>
        <tech>android.nfc.tech.NdefFormatable</tech>
        <tech>android.nfc.tech.MifareClassic</tech>
        <tech>android.nfc.tech.MifareUltralight</tech>
    </tech-list>
</resources>
```

或者其中任意子集。

为了参与 NFC 调度流程，你需要向意图过滤器添加以下操作：

- 对于 NDEF 发现风格，使用`<action android:name="android.nfc.action.NDEF_DISCOVERED" />`
- 对于技术发现风格，使用`<action android:name="android.nfc.action.TECH_DISCOVERED" />`

```xml
...更多过滤器规格...
```

- 对于回退发现风格，使用`<action android:name="android.nfc.action.TAG_DISCOVERED" />`

```xml
...更多过滤器规格...
```

一旦 NFC 相关的意图被分发，匹配的 Activity 可以从意图中提取 NFC 信息。为此，可以通过以下一种或多种方式获取意图的额外数据：

- `NfcAdapter.EXTRA_TAG`：必需，返回一个`android.nfc.Tag`对象。
- `NfcAdapter.EXTRA_NDEF_MESSAGES`：可选，来自标签的 NDEF 消息。可以通过`intent.getParcelableArrayExtra(NfcAdapter.EXTRA_NDEF_MESSAGES)`检索它们。
- `NfcAdapter.EXTRA_ID`：可选，标签的低级 ID。

```kotlin
val rawMessages : Parcelable[] =
    intent.getParcelableArrayExtra(
        NfcAdapter.EXTRA_NDEF_MESSAGES
    )
```

如果你想写入 NFC 标签，相关流程在 Android 在线开发者文档的“NFC 基础”页面中有所描述。

## 点对点 NFC 数据交换

Android 允许通过其 Beam 技术在两台 Android 设备之间进行 NFC 通信。流程如下：让 NFC 设备的 Activity 继承`CreateNdefMessageCallback`并实现方法`createNdefMessage(event: NfcEvent): NdefMessage`。在此方法内部，创建并返回一个`NdefMessage`，如下所示：

```kotlin
val text = "A NFC message at " +
    System.currentTimeMillis().toString()
val msg = NdefMessage(arrayOf(
    NdefRecord.createMime(
        "application/vnd.com.example.android.beam",
        text.toByteArray()
    )
))
/*
 * 当设备接收到带有 Android 应用程序记录 (AAR) 的 NFC 消息时，
 * AAR 中指定的应用保证会运行。因此，AAR 会覆盖标签调度系统。
 */
//val msg = NdefMessage(arrayOf(
//      NdefRecord.createMime(
//           "application/vnd.com.example.android.beam",
//           text.toByteArray()),
//      NdefRecord.createApplicationRecord(
//           "com.example.android.beam")
//))
return msg
```

然后，一个 NFC 数据接收应用可以在其`onResume()`回调中检测是否由 NFC 发现操作启动：

```kotlin
override
fun onResume() {
    super.onResume()
    // 检查 Activity 是否因 Android Beam 事件而启动
    if (NfcAdapter.ACTION_NDEF_DISCOVERED ==
        intent.action) {
        processIntent(intent)
    }
}
```

## NFC 卡模拟

让 Android 设备充当带有 NFC 芯片的智能卡需要复杂的设置和编程任务。这在考虑安全性时尤其有意义——某些 Android 设备可能包含一个安全元件，该元件在硬件基础上执行与读卡器的通信。其他设备可能应用基于主机的卡模拟，让设备 CPU 执行通信。本书不涵盖 NFC 卡模拟所有细节的详尽描述，但你可以在网络上找到信息，或者访问 Android 在线开发者指南中的“基于主机的卡模拟”页面。

尽管如此，我们描述了开始进行基于主机的卡模拟的基本工件。该示例基于 Android 开发者指南提供的 HCE 示例，但已转换为 Kotlin 并精简为仅与 NFC 相关的重要方面（该示例在 Apache 许可下运行——请参见[www.apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)）。代码如下：

```kotlin
/**
 * 这是一个示例 APDU 服务，演示如何
 * 与 Android 4.4 KitKat 中新增的
 * 卡模拟支持进行交互。
 *
 * 本示例用字符串 "Hello World" 回复
 * 任何发送的请求。在实际场景中，你
 * 需要修改此代码以实现你期望的
 * 通信协议。
 *
 * 任何选择 AIDs 为 0xF11111111、0xF22222222 或
 * 0xF33333333 的终端都会调用本示例。
 * 详见 src/main/res/xml/aid_list.xml。
 *
 * 注意：这是一个低级接口。与许多开发者
 * 在实现 Android Beam 应用时熟悉的 NdefMessage
 * 不同，卡模拟仅提供基于字节数组的通信
 * 通道。开发者需要根据需要实现
 * 更高级别的协议支持。
 */
class CardService : HostApduService() {
```

当与 NFC 卡的连接丢失时，会调用`onDeactivated()`回调，以便让应用了解断开连接的原因（链接丢失或读卡器选择了另一个 AID）：

```kotlin
/**
 * 当与 NFC 卡的连接丢失时调用。
 * @param reason 可以是 DEACTIVATION_LINK_LOSS 或
 *     DEACTIVATION_DESELECTED
 */
override fun onDeactivated(reason: Int) {}
```

当收到命令 APDU 时，会调用`processCommandApdu()`方法。通过在此方法中返回一个字节数组，可以直接提供响应 APDU。通常，响应 APDU 必须尽可能快地发送，因为用户可能会在调用此方法时将设备放在 NFC 读卡器上。如果有多个服务在其元数据条目中注册了相同的 AID，则只有在用户明确选择你的服务时（无论是作为默认服务还是仅针对下一次点击），你才会被调用。此方法在你的应用程序的主线程中运行。如果你不能立即返回响应 APDU，请返回`null`并使用`sendResponseApdu()`方法稍后发送：

```kotlin
/**
 * 当从远程设备接收到命令 APDU 时，
 * 将调用此方法。
 *
 * @param commandApdu 从远程设备接收到的 APDU
 * @param extras 包含额外数据的包。可能
 *     为 null。
 * @return 包含响应 APDU 的字节数组，
 *     如果此时无法发送响应 APDU，
 *     则返回 null。
 */
override
fun processCommandApdu(commandApdu: ByteArray,
                       extras: Bundle): ByteArray {
    Log.i(TAG, "Received APDU: " +
        byteArrayToHexString(commandApdu))
    // 如果 APDU 匹配此服务的 SELECT AID 命令，
    // 则发送会员卡账号，后跟 SELECT_OK 状态尾
    // (0x9000)。
    if (Arrays.equals(SELECT_APDU, commandApdu)) {
        val account = AccountStorage.getAccount(this)
        val accountBytes = account!!.toByteArray()
        Log.i(TAG, "Sending account number: $account")
        return concatArrays(accountBytes, SELECT_OK_SW)
    } else {
        return UNKNOWN_CMD_SW
    }
}
```

伴生对象包含一些常量和工具函数。



```kotlin
companion object {
    private val TAG = "CardService"
    // 我们的会员卡服务对应的 AID。
    private val SAMPLE_LOYALTY_CARD_AID = "F222222222"
    // 用于选择 AID 的 ISO-DEP 命令头。
    // 格式：[类别 | 指令 | 参数 1 |
    //          参数 2]
    private val SELECT_APDU_HEADER = "00A40400"
    // 响应 SELECT AID 命令时发送的"OK"状态字 (0x9000)
    private val SELECT_OK_SW =
        hexStringToByteArray("9000")
    // 响应无效 APDU 命令时发送的"UNKNOWN"状态字 (0x0000)
    private val UNKNOWN_CMD_SW =
        hexStringToByteArray("0000")
    private val SELECT_APDU =
        buildSelectApdu(SAMPLE_LOYALTY_CARD_AID)
    /**
     * 为 SELECT AID 命令构建 APDU。此命令
     * 指示读取器有兴趣与哪个服务
     * 进行通信。参见
     * ISO 7816-4。
     *
     * @param aid 要选择的应用程序 ID (AID)
     * @return 用于 SELECT AID 命令的 APDU
     */
    fun buildSelectApdu(aid: String): ByteArray {
        // 格式：[CLASS | INSTRUCTION |
        //          PARAMETER 1 | PARAMETER 2 |
        //          LENGTH | DATA]
        return hexStringToByteArray(
            SELECT_APDU_HEADER +
            String.format("%02X",
                          aid.length / 2) +
            aid)
    }
    /**
     * 将字节数组转换为十六进制字符串的
     * 工具方法。
     */
    fun byteArrayToHexString(bytes: ByteArray):
        String {
        val hexArray = charArrayOf('0', '1', '2', '3',
                                   '4', '5', '6', '7', '8', '9', 'A', 'B',
                                   'C', 'D', 'E', 'F')
        val hexChars = CharArray(bytes.size * 2)
        var v: Int
        for (j in bytes.indices) {
            v = bytes[j].toInt() and 0xFF
            // 将 bytes[j] 转换为 int，视为
            // 无符号值
            hexChars[j * 2] = hexArray[v.ushr(4)]
            // 从高四位选择十六进制字符
            hexChars[j * 2 + 1] = hexArray[v and 0x0F]
            // 从低四位选择十六进制字符
        }
        return String(hexChars)
    }
    /**
     * 将十六进制字符串转换为字节字符串的
     * 工具方法。
     *
     * 输入字符串包含非十六进制字符时的行为
     * 是未定义的。
     */
    fun hexStringToByteArray(s: String): ByteArray {
        val len = s.length
        if (len % 2 == 1) {
            // TODO, 抛出异常
        }
        val data = ByteArray(len / 2)
        var i = 0
        while (i < len) {
            // 将每个字符转换为一个整数
            //  (基数为 16)，然后通过移位放入适当位置
            data[i / 2] =
                ((Character.digit(s[i], 16) shl 4) +
                 Character.digit(s[i + 1], 16)).
                toByte()
            i += 2
        }
        return data
    }
    /**
     * 连接两个字节数组的工具方法。
     */
    fun concatArrays(first: ByteArray,
                     vararg rest: ByteArray): ByteArray {
        var totalLength = first.size
        for (array in rest) {
            totalLength += array.size
        }
        val result =
            Arrays.copyOf(first, totalLength)
        var offset = first.size
        for (array in rest) {
            System.arraycopy(array, 0,
                             result, offset, array.size)
            offset += array.size
        }
        return result
    }
}
```

`AndroidManifest.xml` 中相应的服务声明内容如下：

并且我们需要在 `res/xml` 下放置一个 `aid_list.xml` 文件：

服务类还依赖于对象 `AccountStorage`，例如，其内容如下：

```kotlin
/**
 * 用于将账户号码持久化到磁盘的工具类。
 *
 * 使用默认的 SharedPreferences 实例作为
 * 后端存储。为了性能，值会在内存中缓存。
 */
object AccountStorage {
    private val PREF_ACCOUNT_NUMBER = "account_number"
    private val DEFAULT_ACCOUNT_NUMBER = "00000000"
    private val TAG = "AccountStorage"
    private var sAccount: String? = null
    private val sAccountLock = Any()

    fun setAccount(c: Context, s: String) {
        synchronized(sAccountLock) {
            Log.i(TAG, "设置账户号码：$s")
            val prefs = PreferenceManager.
            getDefaultSharedPreferences(c)
            prefs.edit().
            putString(PREF_ACCOUNT_NUMBER, s).
            commit()
            sAccount = s
        }
    }

    fun getAccount(c: Context): String? {
        synchronized(sAccountLock) {
            if (sAccount == null) {
                val prefs = PreferenceManager.
                getDefaultSharedPreferences(c)
                val account = prefs.getString(
                    PREF_ACCOUNT_NUMBER,
                    DEFAULT_ACCOUNT_NUMBER)
                sAccount = account
            }
            return sAccount
        }
    }
}
```

## Android 与蓝牙

Android 允许添加自定义的蓝牙功能。本书的范围无法详尽描述所有满足蓝牙需求的方法，但要学习：

* 如何扫描可用的本地蓝牙设备（如果你有多个设备）
* 如何扫描已配对的远程蓝牙设备
* 如何扫描远程设备提供的服务
* 如何建立通信渠道
* 如何在本地和远程设备之间传输数据
* 关于配置文件的使用
* 关于 Android 设备上的蓝牙服务器

请参阅 Android 蓝牙的在线文档，网址为 [`https://developer.android.com/guide/topics/connectivity/bluetooth`](https://developer.android.com/guide/topics/connectivity/bluetooth)。这里我们将描述如何实现一个 RfComm 通道，用于在您的智能手机和外部蓝牙服务之间传输串行数据。有了这个用例，您就拥有了一个强大的蓝牙通信工具——例如，您可以用它来控制机器人或智能家居小工具。

### 一个蓝牙 RfComm 服务器

令人惊讶的是，在网上很难找到关于设置蓝牙服务器的有价值信息。然而，在开发过程中，为了实现一个测试用的 Android 应用，实现一个蓝牙服务器是必要的。这样的测试服务器也可能为您可能想到的真实场景提供基础。

一个良好的蓝牙服务器技术候选是 *BlueCove*，这是一个开源项目。其部分代码采用 Apache 许可证 V2.0 授权，其他部分采用 GPL 授权，因此，虽然它很容易集成到您自己的项目中，但您需要检查其许可证是否适用于您的商业项目。在接下来的段落中，我将描述如何在 Linux 上使用 *BlueCove* 和 *Groovy* 设置一个 RfComm 蓝牙服务器。对于 Windows，您需要修改启动脚本并使用 DLL 库代替。

首先，下载并安装 Groovy。任何现代版本都应该可以。接下来，下载 *BlueCove*。我测试的版本是 2.1.0，但您也可以尝试更新的版本。您需要以下文件：`bluecove-2.1.0.jar`、`bluecove-emu-2.1.0.jar` 和 `bluecove-gpl-2.1.0.jar`。临时将这些 JAR 文件作为 zip 文件解压到某个位置，并创建一个文件夹结构：

```plaintext
libbluecove.jnilib
startRfComm.sh
libbluecove.so
libbluecove_x64.so
libs/
    bluecove-2.1.0.jar
    bluecove-emu-2.1.0.jar
    bluecove-gpl-2.1.0.jar
scripts/
    rfcomm.groovy
```

**注意**：根据您使用的 Linux 发行版，您可能需要添加一个额外的符号链接，如下所示：
```bash
cd /usr/lib/x86_64-linux-gnu/
ln -s libbluetooth.so.3 libbluetooth.so
```
您必须以 root 身份执行此操作。此外，仍以 root 身份执行：
```bash
mkdir /var/run/sdb
chmod 777 /var/run/sdp
```
另外，为了解决兼容性问题，您必须修改蓝牙服务器进程：
```bash
cd /etc/systemd/system/bluetooth.target.wants/
```
在 `bluetooth.service` 内部更改：
```
ExecStart=/usr/lib/bluetooth/bluetoothd→
ExecStart=/usr/lib/bluetooth/bluetoothd –C
```
然后执行：
```bash
systemctl daemon-reload
systemctl restart bluetooth
```

文件 `startRfComm.sh` 是启动脚本。创建它并在其中写入：

```bash
#!/bin/bash
export JAVA_HOME=/opt/jdk8
export GROOVY_HOME=/opt/groovy
$GROOVY_HOME/bin/groovy \
-cp libs/bluecove-2.1.0.jar:libs/bluecove-emu-2.1.0.jar:libs/bluecove-gpl-2.1.0.jar \
-Dbluecove.debug=true \
-Djava.library.path=. \
scripts/rfcomm.groovy
```

并相应地修正路径。

服务器代码位于 `scripts/rfcomm.groovy` 中。创建它并使其包含以下内容：


```groovy
import javax.bluetooth.*
import javax.obex.*
import javax.microedition.io.*
import groovy.transform.Canonical
// 必须以 root 身份运行服务器！
// 设置服务器以监听连接
// 获取本地蓝牙设备对象
LocalDevice local = LocalDevice.getLocalDevice()
local.setDiscoverable(DiscoveryAgent.GIAC)
UUID uuid = new UUID(80087355)
String url = "btspp://localhost:" + uuid.toString() +
";name=RemoteBluetooth"
println("URI: " + url)
StreamConnectionNotifier notifier = Connector.open(url)
// 等待连接
while(true) {
println("等待连接...")
StreamConnection connection = notifier.acceptAndOpen()
InputStream inputStream = connection.openInputStream()
println("等待输入")
while (true) {
int command = inputStream.read()
if(command == -1) break
println("命令: " + command)
}
}
```

服务器必须以 root 身份启动。当您在安装了蓝牙适配器的系统上调用 `sudo ./startRfComm.sh` 时，移除时间戳后的输出应类似于：

```
Java 1.4+ 检测到：1.8.0_60；Java HotSpot(TM) 64-Bit Server VM；Oracle Corporation
...
localDeviceID 0
...
BlueCove 版本 2.1.0，基于 bluez
URI: btspp://localhost:04c6093b00001000800000805f9b34fb;name=RemoteBluetooth
使用 BlueCove javax.microedition.io.Connector 打开
...
正在连接 btspp://localhost:04c6093b00001000800000805f9b34fb;name=RemoteBluetooth
...
已创建 SDPSession 139982379587968
...
检测到 BlueZ 主版本 4
...
已调用 bluez 主版本 4 的 sdp_extract_pdu 函数
...
正在等待连接...
```

## 一个 Android RfComm 客户端

在前一节中的 RfComm 蓝牙服务器进程运行之后，我们现在为 Android 平台开发客户端。它应该：

* 提供一个 Activity，用于选择要连接的远程蓝牙设备。
* 提供另一个 Activity，用于发起连接并向蓝牙 RfComm 服务器发送消息。

从新项目开始，别忘了添加 Kotlin 支持。将文件 `AndroidManifest.xml` 修改为以下内容：

接下来，在 `res/layout` 中创建三个布局文件。第一个文件 `activity_main.xml` 包含一个状态行和两个按钮：

注意：为简单起见，我以字面量形式添加了文本——在生产环境中，您当然应该使用字符串资源。

下一个布局文件 `device_list.xml` 用于远程设备选择器 Activity：

最后一个文件 `device_name.xml` 用于设备列表 Activity 中列表项的布局：

`DeviceListActvity` 类是 Android 开发者文档中蓝牙聊天示例的设备列表 Activity 的改编版本：

```
/**
 * 此 Activity 以对话框形式出现。它会列出所有
 * 已配对设备以及在发现过程中检测到的设备。
 * 当用户选择了某个设备时，该设备的
 * MAC 地址会通过结果 Intent 发送回父
 * Activity。
 */
class DeviceListActivity : Activity() {
companion object {
private val TAG = "DeviceListActivity"
var EXTRA_DEVICE_ADDRESS = "device_address"
}
private var mBtAdapter: BluetoothAdapter? = null
private var mNewDevicesArrayAdapter:
ArrayAdapter? = null
```

`OnItemClickListener` 是 Kotlin 中实现*单方法接口*的示例：

```
private val mDeviceClickListener =
AdapterView.OnItemClickListener {
av, v, arg2, arg3 ->
// 取消发现，因为其成本高昂且我们即将连接
mBtAdapter!!.cancelDiscovery()
// 获取设备 MAC 地址，即 View 中的最后 17 个字符
val info = (v as TextView).text.toString()
val address = info.substring(info.length - 17)
// 创建结果 Intent 并包含 MAC 地址
val intent = Intent()
intent.putExtra(EXTRA_DEVICE_ADDRESS, address)
// 设置结果并结束此 Activity
setResult(Activity.RESULT_OK, intent)
finish()
}
```

`BroadcastReceiver` 用于监听已发现的设备，并在发现完成时更改标题：

```
/**
 * 监听已发现的设备。
 */
private val mReceiver = object : BroadcastReceiver() {
override
fun onReceive(context: Context, intent: Intent) {
val action = intent.action
// 当发现找到设备时
if (BluetoothDevice.ACTION_FOUND == action) {
// 从 Intent 中获取 BluetoothDevice 对象
val device = intent.
getParcelableExtra(
BluetoothDevice.EXTRA_DEVICE)
// 如果已配对，则跳过因为它已经列出
if (device.bondState !=
BluetoothDevice.BOND_BONDED) {
mNewDevicesArrayAdapter!!.add(
device.name + "\n" +
device.address)
}
// 当发现完成时，更改 Activity 标题
} else if (BluetoothAdapter.
ACTION_DISCOVERY_FINISHED == action) {
setProgressBarIndeterminateVisibility(
false)
setTitle("选择设备")
if (mNewDevicesArrayAdapter!!.count
== 0) {
val noDevices = "没有设备"
mNewDevicesArrayAdapter!!.add(
noDevices)
}
}
}
}
```

像往常一样，`onCreate()` 回调方法用于设置用户界面：

```
override fun onCreate(savedInstanceState: Bundle?) {
super.onCreate(savedInstanceState)
// 设置窗口
requestWindowFeature(Window.
FEATURE_INDETERMINATE_PROGRESS)
setContentView(R.layout.activity_device_list)
// 如果用户退出，设置结果为 CANCELED
setResult(Activity.RESULT_CANCELED)
// 初始化用于执行设备发现的按钮
button_scan.setOnClickListener { v ->
doDiscovery()
v.visibility = View.GONE
}
// 初始化数组适配器。一个用于已配对设备，一个用于新发现的设备
val pairedDevicesArrayAdapter =
ArrayAdapter(this,
R.layout.device_name)
mNewDevicesArrayAdapter =
ArrayAdapter(this,
R.layout.device_name)
// 查找并设置已配对设备的 ListView
val pairedListView = paired_devices as ListView
pairedListView.adapter = pairedDevicesArrayAdapter
pairedListView.onItemClickListener =
mDeviceClickListener
// 查找并设置新发现设备的 ListView
val newDevicesListView = new_devices as ListView
newDevicesListView.adapter =
mNewDevicesArrayAdapter
newDevicesListView.onItemClickListener =
mDeviceClickListener
// 注册广播，监听设备被发现
var filter =
IntentFilter(BluetoothDevice.ACTION_FOUND)
this.registerReceiver(mReceiver, filter)
// 注册广播，监听发现完成
filter = IntentFilter(BluetoothAdapter.
ACTION_DISCOVERY_FINISHED)
this.registerReceiver(mReceiver, filter)
// 获取本地蓝牙适配器
mBtAdapter = BluetoothAdapter.getDefaultAdapter()
// 获取当前已配对设备的集合
val pairedDevices = mBtAdapter!!.bondedDevices
// 如果存在已配对设备，则逐个添加到 ArrayAdapter
if (pairedDevices.size > 0) {
title_paired_devices.visibility = View.VISIBLE
for (device in pairedDevices) {
pairedDevicesArrayAdapter.add(
device.name + "\n" + device.address)
}
} else {
val noDevices = "没有设备"
pairedDevicesArrayAdapter.add(noDevices)
}
}
```

而 `onDestroy()` 回调方法用于清理资源。最后，`doDiscovery()` 方法执行实际的发现工作：

```
override fun onDestroy() {
super.onDestroy()
// 确保我们不再进行发现
if (mBtAdapter != null) {
mBtAdapter!!.cancelDiscovery()
}
// 注销广播监听器
this.unregisterReceiver(mReceiver)
}
/**
 * 使用 BluetoothAdapter 开始设备发现
 */
private fun doDiscovery() {
Log.d(TAG, "doDiscovery()")
// 在标题中指示正在扫描
setProgressBarIndeterminateVisibility(true)
setTitle("正在扫描")
// 打开新设备的副标题
title_new_devices.visibility = View.VISIBLE
// 如果已经在发现中，则停止它
if (mBtAdapter!!.isDiscovering) {
mBtAdapter!!.cancelDiscovery()
}
// 通过 BluetoothAdapter 请求发现
mBtAdapter!!.startDiscovery()
}
}
```

`MainActivity` 类负责检查和获取权限，并构造一个 `BluetoothCommandService`，我们将在后面进行描述。

```kotlin
class MainActivity : AppCompatActivity() {
companion object {
val REQUEST_ENABLE_BT = 42
val REQUEST_QUERY_DEVICES = 142
}
var mBluetoothAdapter: BluetoothAdapter? = null
var mCommandService:BluetoothCommandService? = null
```

`onCreate()` 回调用于在 Activity 中设置用户界面并注册蓝牙适配器：

```kotlin
override fun onCreate(savedInstanceState: Bundle?) {
super.onCreate(savedInstanceState)
setContentView(R.layout.activity_main)
val permission1 = ContextCompat.
checkSelfPermission(
this, Manifest.permission.BLUETOOTH)
val permission2 = ContextCompat.
checkSelfPermission(
this, Manifest.permission.BLUETOOTH_ADMIN)
if (permission1 !=
PackageManager.PERMISSION_GRANTED ||
permission2 !=
PackageManager.PERMISSION_GRANTED)
{
ActivityCompat.requestPermissions(this,
arrayOf(
Manifest.permission.BLUETOOTH,
Manifest.permission.BLUETOOTH_ADMIN),
642)
}
mBluetoothAdapter =
BluetoothAdapter.getDefaultAdapter()
if (mBluetoothAdapter == null) {
Toast.makeText(this,
"Bluetooth is not supported",
Toast.LENGTH_LONG).show()
finish()
}
if (!mBluetoothAdapter!!.isEnabled()) {
val enableIntent = Intent(
BluetoothAdapter.ACTION_REQUEST_ENABLE)
startActivityForResult(
enableIntent, REQUEST_ENABLE_BT)
}
}
```

`scanDevices()` 方法用于调用系统的蓝牙设备扫描器：

```kotlin
/**
* 启动 DeviceListActivity 以查看设备并进行扫描
*/
fun scanDevices(v:View) {
val serverIntent = Intent(
this, DeviceListActivity::class.java)
startActivityForResult(serverIntent,
REQUEST_QUERY_DEVICES)
}
```

`rfComm` 和 `sendMessage()` 方法用于处理蓝牙消息的发送：

```kotlin
fun rfComm(v: View) {
sendMessage("The message")
}
/**
* 发送一条消息。
*
* @param message 要发送的文本字符串。
*/
private fun sendMessage(message: String) {
if (mCommandService?.mState !==
BluetoothCommandService.Companion.
State.CONNECTED)
{
Toast.makeText(this, "Not connected",
Toast.LENGTH_SHORT).show()
return
}
// 检查是否真的有内容需要发送
if (message.length > 0) {
val send = message.toByteArray()
mCommandService?.write(send)
}
}
```

与设备的实际连接在 `connectDevice()` 方法中完成：

```kotlin
private
fun connectDevice(data: Intent, secure: Boolean) {
val macAddress = data.extras!!
.getString(
DeviceListActivity.EXTRA_DEVICE_ADDRESS)
mBluetoothAdapter?.
getRemoteDevice(macAddress)?.run {
val device = this
mCommandService =
BluetoothCommandService(
this@MainActivity, macAddress).apply {
addStateChangeListener { statex ->
runOnUiThread {
state.text = statex.toString()
}
}
connect(device)
}
}
}
private fun fetchUuids(device: BluetoothDevice) {
device.fetchUuidsWithSdp()
}
```

回调方法 `onActivityResult()` 处理从系统设备选择器返回的结果——这里我们只执行连接到所选设备的操作：

```kotlin
override
fun onActivityResult(requestCode: Int,
resultCode: Int, data: Intent) {
when (requestCode) {
REQUEST_QUERY_DEVICES -> {
if (resultCode == Activity.RESULT_OK) {
connectDevice(data, false)
}
}
}
}
}
```

类 `BluetoothCommandService` 尽管名称如此，但它并不是一个 Android 服务。它负责处理与蓝牙服务器的通信并读取数据：

```kotlin
class BluetoothCommandService(context: Context,
val macAddress:String) {
companion object {
// 此应用程序的唯一 UUID
private val MY_UUID_INSECURE = UUID.fromString(
"04c6093b-0000-1000-8000-00805f9b34fb")
// 指示当前连接状态的常量
enum class State {
NONE,       // 当前无操作
LISTEN,     // 监听传入连接
CONNECTING, // 正在发起传出连接
CONNECTED   // 已连接到远程设备
}
}
private val mAdapter: BluetoothAdapter
private var createSocket: CreateSocketThread? = null
private var readWrite: SocketReadWrite? = null
var mState: State = State.NONE
private var stateChangeListeners =
mutableListOf<(State)->Unit>()
fun addStateChangeListener(l:(State)->Unit) {
stateChangeListeners.add(l)
}
init {
mAdapter = BluetoothAdapter.getDefaultAdapter()
changeState(State.NONE)
}
```

它的公共方法用于连接、断开连接以及写入数据：

```kotlin
/**
* 发起与远程设备的连接。
*
* @param device 要连接的 BluetoothDevice
*/
fun connect(device: BluetoothDevice) {
stopThreads()
// 启动线程以连接指定设备
createSocket = CreateSocketThread(device).apply {
start()
}
}
/**
* 停止所有线程
*/
fun stop() {
stopThreads()
changeState(State.NONE)
}
/**
* 以非同步方式写入 ConnectedThread
*
* @param out 要写入的字节数组
* @see ConnectedThread.write
*/
fun write(out: ByteArray) {
if (mState != State.CONNECTED) return
readWrite?.run { write(out) }
}
```

它的私有方法用于处理连接线程：

```kotlin
/////////////////////////////////////////////////////
/////////////////////////////////////////////////////
/**
* 启动 ConnectedThread 以开始管理
* 蓝牙连接
*
* @param socket 建立连接所使用的 BluetoothSocket
* @param device 已经连接上的 BluetoothDevice
*/
private fun connected(socket: BluetoothSocket,
device: BluetoothDevice) {
stopThreads()
// 启动线程进行数据传输
readWrite = SocketReadWrite(socket).apply {
start()
}
}
private fun stopThreads() {
createSocket?.run {
cancel()
createSocket = null
}
readWrite?.run {
cancel()
readWrite = null
}
}
/**
* 指示连接尝试失败。
*/
private fun connectionFailed() {
changeState(State.NONE)
}
/**
* 指示连接丢失。
*/
private fun connectionLost() {
changeState(State.NONE)
}
```

连接套接字的处理线程本身是一个专门的 `Thread` 实现：

```kotlin
/**
* 此线程在尝试与设备建立传出连接时运行。
* 它会一直执行到底；连接要么成功，要么失败。
*/
private inner
class CreateSocketThread(
private val mmDevice: BluetoothDevice) :
Thread() {
private val mmSocket: BluetoothSocket?
init {
// 获取用于与指定 BluetoothDevice 建立连接的 BluetoothSocket
mmSocket = mmDevice.
createInsecureRfcommSocketToServiceRecord(
MY_UUID_INSECURE)
changeState(Companion.State.CONNECTING)
}
override fun run() {
name = "CreateSocketThread"
// 始终取消设备发现，因为它会减慢连接速度
mAdapter.cancelDiscovery()
// 尝试连接到 BluetoothSocket
try {
// 这是一个阻塞调用，只有在成功连接或抛出异常时才会返回
mmSocket!!.connect()
} catch (e: IOException) {
Log.e("LOG","Connection failed", e)
Log.e("LOG", "Maybe device does not " +
" expose service " +
MY_UUID_INSECURE)
// 关闭套接字
mmSocket!!.close()
connectionFailed()
return
}
// 重置线程，因为任务已完成
createSocket = null
// 启动已连接的线程
connected(mmSocket, mmDevice)
}
fun cancel() {
mmSocket!!.close()
}
}
```

为了从连接套接字读取数据以及向它写入数据，我们使用另一个线程：



