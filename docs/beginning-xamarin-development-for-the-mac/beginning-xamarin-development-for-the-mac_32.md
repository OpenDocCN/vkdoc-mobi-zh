# OpenWeatherMap API

UI 准备就绪后，我开始实现负责获取当前天气状况的逻辑。为此，我使用了 OpenWeatherMap Web 服务（[`openweathermap.org/`](https://openweathermap.org/)）公开的 API。OpenWeatherMap 提供免费和付费计划。免费计划限制为每分钟 60 次调用（[`openweathermap.org/price`](https://openweathermap.org/price)），并有一些其他特定限制，但对于实现 `HelloTV` 示例来说已经足够。要访问 OpenWeatherMap API，您需要注册一个免费账户。您将通过电子邮件收到您的应用标识符，用其访问 OpenWeatherMap 的 REST 服务。完成后，您可以通过在浏览器地址栏中输入以下 URL 来开始测试 OpenWeatherMap：

[`api.openweathermap.org/data/2.5/weather?q=`<城市名>`&appid=`<您的应用 ID>``](http://api.openweathermap.org/data/2.5/weather?q=%3ccityname%3e%26appid=%3cyour%20app%20id%3e)

因此，OpenWeatherMap 将返回一个 JSON 对象，其结构如代码清单 9-1 所示。即，共有十二个属性，其中包含有关气象站地理位置的信息，并报告了天气状况（`coord`）、实际天气情况（`weather`、`main`、`visibility`、`wind`、`clouds`）以及计算日期（`dt`）。

```
{
"coord": {
"lon": -74.01,
"lat": 40.71
},
"weather": [
{
"id": 800,
"main": "Clear",
"description": "clear sky",
"icon": "01d"
}
],
"base": "stations",
"main": {
"temp": 290.14,
"pressure": 1016,
"humidity": 77,
"temp_min": 287.15,
"temp_max": 292.15
},
"visibility": 16093,
"wind": {
"speed": 2.92,
"deg": 272.003
},
"clouds": {
"all": 1
},
"dt": 1504527300,
"sys": {
"type": 1,
"id": 1969,
"message": 0.0186,
"country": "US",
"sunrise": 1504520803,
"sunset": 1504567326
},
"id": 5128581,
"name": "New York",
"cod": 200
}
```

代码清单 9-1. OpenWeatherMap API 返回的 JSON 响应示例

为了在 `HelloTV` 应用中消费上述 JSON 响应，我需要将 JSON 对象映射到 C# 类。如第 7 章所述，我在 JSON Utils 服务（[`jsonutils.com/`](https://jsonutils.com/)）的帮助下完成此映射。具体来说，我将 JSON 响应粘贴到 JSON 测试或 URL 文本字段中，将类名设置为 `WeatherInfo`，勾选“帕斯卡命名法”，然后单击提交按钮。因此，会生成一组类。我将所有这些类保存在 `HelloTV` 应用的 `Models` 文件夹下的单独文件中（参见配套代码：`Chapter_09/HelloTV/Models`）。

无需详细描述每个自动生成的类。因此，我将仅描述最重要的元素。表示 JSON 响应顶级属性的主类是 `WeatherInfo`。此类的定义如代码清单 9-2 所示。因此，`WeatherInfo` 包含十二个自动生成的属性，您可以使用它们来读取天气状况。

```
public class WeatherInfo
{
public Coord Coord { get; set; }
public IList Weather { get; set; }
public string Base { get; set; }
public Main Main { get; set; }
public int Visibility { get; set; }
public Wind Wind { get; set; }
public Clouds Clouds { get; set; }
public int Dt { get; set; }
public Sys Sys { get; set; }
public int Id { get; set; }
public string Name { get; set; }
public int Cod { get; set; }
}
```

代码清单 9-2. `WeatherInfo` 类的定义



具体来说，在 `HelloTV` 应用中，我只使用了 `WeatherInfo` 类实例的两个属性：`Main` 和 `Weather`。其中第一个属性存储温度、气压和湿度（清单 9-3）。这些值随后会显示在 UI 的相应标签中。`Main` 类的实例还有另外两个属性：`TempMin` 和 `TempMax`。你可以用它们来获取最低和最高温度。它们表示与当前温度的偏差，根据 OpenWeatherMap 的文档，这在大城市中可能会出现。我即将使用的 `WeatherInfo` 类的第二个元素是名为 `Weather` 的集合。该集合中的每个元素都是 `Weather` 类的一个实例（清单 9-4）。`Weather` 类有四个属性：

* `Id` – 表示集合中元素的标识符
* `Main` – 天气参数组
* `Description` – 当前天气状况的文本描述
* `Icon` – 以图形方式说明天气情况的图标的标识符

在 `HelloTV` 应用中，我使用了最后两个；描述显示在最后一个标签中，而图标标识符则用于创建显示在温度正上方的图像（请参见图 9-1）。我将告诉你如何获取这个图像。

```
public class Main
{
public double Temp { get; set; }
public double Pressure { get; set; }
public double Humidity { get; set; }
public double TempMin { get; set; }
public double TempMax { get; set; }
}
清单 9-3.
Main 对象存储温度、气压和湿度读数
```

```
public class Weather
{
public int Id { get; set; }
public string Main { get; set; }
public string Description { get; set; }
public string Icon { get; set; }
}
清单 9-4.
Weather 对象存储描述和图标标识符
```

## 检索天气预报

在建立了 JSON 与 C# 对象之间的映射关系后，我继续实现向 OpenWeatherMap API 发送 HTTP `GET` 请求的类。这个类 `WeatherServiceHelper`（参见配套代码中 `HelloTV` 应用的 `Helpers` 子文件夹）使用了 `HttpClient` 对象，我在第 7 章对此进行过说明。为了在 `HelloTV` 项目中使用 `HttpClient` 类，我引用了 `System.Net.dll`，然后安装了 Newtonsoft.JSON NuGet 包。随后，我实现类构造函数来实例化 `HttpClient`，并将其 `BaseAddress` 属性指向 OpenWeatherMap URL（清单 9-5）。

```
private static HttpClient httpClient;
static WeatherServiceHelper()
{
httpClient = new HttpClient()
{
BaseAddress = new Uri("http://api.openweathermap.org/data/2.5/weather")
};
}
清单 9-5.
为 OpenWeatherMap API 创建 HTTP 客户端
```

配置 HTTP 客户端后，我可以开始发送天气预报请求了。为此，我在 `WeatherServiceHelper` 中实现了静态异步方法 `GetWeatherInfo`（清单 9-6）。该方法接受两个参数。第一个参数存储城市名称，第二个参数定义 OpenWeatherMap 返回天气状况时使用的单位。显然，OpenWeatherMap 可以使用不同的标度返回温度：开尔文、摄氏度或华氏度。要指定标度，你可以使用我在自定义枚举类型 `TemperatureUnit`（参见 `HelloTV` 应用中的 `Enums\TemperatureUnit.cs` 文件）中定义的值之一：

* `Default` – 表示应使用默认单位。在这种情况下，温度以开尔文表示（例如，参见清单 9-1）。
* `Metric` – 指示 OpenWeatherMap API 使用公制单位，这意味着温度将以摄氏度给出。
* `Imperial` – 表示英制单位，温度以华氏度表示。

你传递给 `GetWeatherInfo` 方法的值随后用于创建请求的统一资源标识符 (URI)（清单 9-7）。此请求随后被发送到 OpenWeatherMap API。在收到响应后，我分析其状态码（`CheckStatusCode` 方法）。与第 7 章类似，当状态码不是 200（OK）时，我会引发一个异常。否则，我将响应主体转换为 JSON 格式，并使用 `JsonConvert` 类对其进行反序列化。

```
public static async Task GetWeatherInfo(
string cityName, TemperatureUnit unit)
{
var getRequestUri = GetRequestUri(cityName, unit);
var response = await httpClient.GetAsync(getRequestUri);
CheckStatusCode(response.StatusCode);
var jsonString = await response.Content.ReadAsStringAsync();
return JsonConvert.DeserializeObject(jsonString);
}
private static void CheckStatusCode(HttpStatusCode statusCode)
{
if (statusCode != HttpStatusCode.OK)
{
throw new Exception($"意外的状态码: {statusCode}");
}
}
清单 9-6.
获取天气预报信息
```

构建请求 URI 需要额外的说明。如清单 9-7 所示，我在基地址后附加了一个包含三个参数的查询字符串：

* `appId` – 用于使用从 OpenWeatherMap 服务获取的标识符对请求进行身份验证
* `q` – 指明应返回天气状况的城市或位置
* `units` – 指定单位

第一个参数的值是从私有字段 `appId`（清单 9-7）获取的，因此你需要将此字段更新为你在注册 OpenWeatherMap 服务时获得的值。其他查询字符串参数则从 `GetRequestUri` 函数的参数中获得。请注意，我使用 `TemperatureUnit` 枚举来指定单位。为了将该类型的值转换为实际的查询字符串参数，我编写了一个辅助方法 `TemperatureUnitToQueryString`，如清单 9-8 所示。

```
private static string appId = "";
private static string GetRequestUri(string cityName, TemperatureUnit unit)
{
return $"?appId={appId}"
+ $"&q={cityName}"
+ $"&{TemperatureUnitToQueryString(unit)}";
}
清单 9-7.
准备请求 URI
```

```
private static string TemperatureUnitToQueryString(TemperatureUnit unit)
{
var queryString = "units=";
switch(unit)
{
case TemperatureUnit.Imperial:
queryString += "imperial";
break;
case TemperatureUnit.Metric:
queryString += "metric";
break;
}
return queryString;
}
清单 9-8.
创建单位查询字符串参数
```

`WeatherServiceHelper` 类的最后一个元素是 `GetTemperatureUnitString` 方法。它确定表示温度单位的文本表示形式。对于英制和公制单位，我分别使用 °F 和 °C，而对于默认标度，我使用 K 符号（清单 9-9）。

```
public static string GetTemperatureUnitString(TemperatureUnit unit)
{
var unitString = string.Empty;
var degSymbol = (char)176;
switch (unit)
{
case TemperatureUnit.Imperial:
unitString = $"{degSymbol}F";
break;
case TemperatureUnit.Metric:
unitString = $"{degSymbol}C";
break;
case TemperatureUnit.Default:
default:
unitString = "K";
break;
}
return unitString;
}
清单 9-9.
获取表示温度标度的符号
```



