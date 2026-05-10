# 排版后的文档

由于不同设备的性能和精度有所差异，并且只要不发生错误（例如用户拒绝请求），无论使用何种设备（无论是旧款 iPhone、iPod touch 还是 iPad），第一种方法 `properties` 都会返回一个值。但是，正如你所见，并非所有数据都必然可用。例如，`Heading` 和 `speed` 显然要求已记录下足够精确的多个位置点，因此需要持续跟踪才能返回值，我们稍后将对此进行说明。根据所使用的设备及其所用的定位技术，并非所有数据都能获取。

此外，由于使用 GPS 通常需要时间才能连接到卫星，设备往往会推迟使用这种定位方法，这虽然能提升性能，但返回的数据精度会降低。这种方法将用于跟踪，因为对位置信息的请求是重复发出的，从而为 GPS 信号获取留出了足够的时间。

在测试你的 Web 应用程序时，你通常需要到室外进行，尤其是在依赖 GPS 精度的情况下，因为 GPS 在室内几乎无法工作（即便能工作）。

因此，无论你是期望从基本属性还是更高级的属性中获取信息，你都应该始终处理可能出现的错误和近似情况。

## 第 14 章：位置感知 Web 应用程序

### 处理请求中的错误

幸运的是，`getCurrentPosition()` 接受另一个可选参数，该参数能让你访问所发生错误的代码和描述。这为你理解和处理错误提供了一定的灵活性。以下是一个操作示例：

```
/* 请求用户的位置 */
window.navigator.geolocation.getCurrentPosition(successCallback, failureCallback);

function successCallback(position) {
  /* 对位置对象数据进行处理 */
}

function failureCallback(error) {
  /* 对错误对象进行处理 */
}
```

错误对象有两个属性，如表 14-2 所示。

**表 14-2.** 错误对象的属性

| 属性 | 描述 |
| --- | --- |
| `error.code` | 以下错误代码之一：<br>`error.UNKNOWN_ERROR` (0)<br>`error.PERMISSION_DENIED` (1)<br>`error.POSITION_UNAVAILABLE` (2)<br>`error.TIMEOUT` (3) |
| `error.message` | 描述发生情况的说明信息。 |

尽管 `error.code` 属性提供固定的错误代码，但 `error.message` 属性可能因运行该函数的设备不同而有很大差异。

显然，一个错误代码可能对应多种实际错误。例如，当用户拒绝请求，或者设备上的定位服务被禁用时，都会返回 `PERMISSION_DENIED`。然而，错误说明信息应该能阐明该代码的详情，这就是这两个属性一起使用很有用的原因。请注意，`error.message` 属性仅用于调试目的；它绝不应该展示给最终用户。

**警告：** 如果成功回调函数内部发生异常，则会以 `UNKNOWN_ERROR` 代码调用失败回调函数。请务必仔细检查你所遇到的错误究竟是定位错误还是来自你自己的代码。

### 精度、超时和缓存位置

W3C 规范提供了三个选项，通过使用一个对象类型参数以及你的回调函数来优化你的请求。你可以指定请求成功所需的最长时间、请求之前缓存位置信息的有效时长，以及是否需要更高的精度。这些选项的调用方式如下：

```
window.navigator.geolocation.getCurrentPosition(successCallback, failureCallback, {
  timeout: 0,
  maximumAge: 60000,
  enableHighAccuracy: false
});
```

在很多情况下，并不需要获取用户的最新位置。例如，对于一个根据用户当前位置返回热门餐厅列表的应用程序，用户很可能常常处于相同的地理范围内。在这种情况下，你或许希望使用用户设备上的缓存数据。



