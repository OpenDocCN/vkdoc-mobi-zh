# 显示天气信息

现在我们已经准备好获取并显示天气状况所需的一切，接下来将 UI 层与 `WeatherServiceHelper` 结合起来。为了实现这一功能，我实现了 `ViewController` 类。首先，我定义了 `DisplayWeatherInfo` 方法，如代码清单 9-10 所示。该方法接收一个 `WeatherInfo` 类的实例（`weatherInfo` 参数），并在默认视图中显示其选定属性的值。此外，也可以为 `weatherInfo` 参数传递 `null`。在这种情况下，`DisplayWeatherInfo` 会显示替代符号（`--`）来代替温度、湿度、气压和描述的实际值。

```
private async Task DisplayWeatherInfo(WeatherInfo weatherInfo)
{
    var alternateSymbol = "--";
    var tempUnit = WeatherServiceHelper.GetTemperatureUnitString(temperatureUnit);
    var pressureUnit = "hPa";
    if (weatherInfo == null)
    {
        LabelTemperature.Text = $"Temperature: {alternateSymbol}";
        LabelHumidity.Text = $"Humidity: {alternateSymbol}";
        LabelPressure.Text = $"Pressure: {alternateSymbol}";
        LabelDescription.Text = $"Description: {alternateSymbol}";
    }
    else
    {
        LabelTemperature.Text = "Temperature: " + $"{weatherInfo.Main.Temp} {tempUnit}";
        LabelHumidity.Text = "Humidity: " + $"{weatherInfo.Main.Humidity} %";
        LabelPressure.Text = "Pressure: " + $"{weatherInfo.Main.Pressure} {pressureUnit}";
        LabelDescription.Text = "Description: " + $"{weatherInfo.Weather.FirstOrDefault().Description}";
        ImageViewWeatherIcon.Image = await IconHelper.GetIcon(weatherInfo);
    }
}
```

在标签中显示文本非常直接。我只需将 `WeatherInfo` 类实例的属性值写入标签的 `Text` 属性即可。而要显示图标，则需要多花一些功夫。OpenWeatherMap API 会返回图标标识符，你可以利用这些标识符获取实际 PNG 格式位图的 URL。此类 URL 的通用结构如下：

`http://openweathermap.org/img/w/` `<iconId>` `.png`

其中 `<iconId>` 是你请求 OpenWeatherMap API 提供所选城市的当前天气状况时获取的值。图标标识符存储在 `Weather` 对象的 `Icon` 属性中。

获得图标 URL 后，我必须下载该图标，然后将其转换为 `UIImage` 类的实例，以便在 Image View 控件中显示。为了实现这一功能，我向 HelloTV 项目添加了 `IconHelper` 类（参见 `Helpers` 文件夹下的 `IconHelper.cs` 文件）。`IconHelper` 类的完整定义见代码清单 9-11。如图所示，`IconHelper` 包含三个私有静态字段：`httpClient`、`baseAddress` 和 `iconExtension`。第一个字段存储对 `HttpClient` 类实例的引用，用于实际从 OpenWeatherMap 服务下载表示位图的字节数组；第二个字段 `baseAddress` 存储用于获取图标的 URL 主体部分；最后一个字段 `iconExtension` 是一个常量，存储 `.png` 扩展名。从 `baseAddress` 和 `iconExtension` 获取的值，连同从 `WeatherInfo` 类实例获取的图标标识符，组合成最终的 URL（参见代码清单 9-11 中的 `GetIconUrl` 方法）。生成的 URL 随后在 `GetIcon` 方法中使用，我将该 URL 作为 `HttpClient` 类实例的 `GetByteArrayAsync` 方法的参数传递。`GetByteArrayAsync` 返回一个表示图像数据的字节数组，我使用该数组通过静态方法 `LoadFromData` 实例化 `UIImage` 类（代码清单 9-11 中 `GetIcon` 方法的最后一条语句）。请注意，要将 `GetByteArrayAsync` 返回的字节数组转换为 `UIImage.LoadFromData` 接受的 `NSData` 对象，我必须使用 `NSData` 类的静态方法 `FromArray`。

```
public static class IconHelper
{
    private static HttpClient httpClient;
    private static string baseAddress = "http://openweathermap.org/img/w/";
    private static string iconExtension = ".png";

    static IconHelper()
    {
        httpClient = new HttpClient();
    }

    public static async Task<UIImage> GetIcon(WeatherInfo weatherInfo)
    {
        var iconUrl = GetIconUrl(weatherInfo);
        var imageData = await httpClient.GetByteArrayAsync(iconUrl);
        return UIImage.LoadFromData(NSData.FromArray(imageData));
    }

    private static string GetIconUrl(WeatherInfo weatherInfo)
    {
        var iconName = weatherInfo.Weather.FirstOrDefault().Icon;
        return $"{baseAddress}{iconName}{iconExtension}";
    }
}
```

随后，我在 `ViewDidLoad` 事件处理程序（代码清单 9-12）中使用 `null` 参数调用 `DisplayWeatherInfo`。这样做是为了将所有标签的 `Text` 属性设置为初始值。在 `ViewDidLoad` 事件处理程序中，我还将 `ButtonGetCurrentWeather_PrimaryActionTriggered` 方法与按钮的 `PrimaryActionTriggered` 事件关联起来。当用户轻点该按钮时，会触发此操作。

```
public async override void ViewDidLoad()
{
    base.ViewDidLoad();
    await DisplayWeatherInfo(null);
    ButtonGetCurrentWeather.PrimaryActionTriggered += ButtonGetCurrentWeather_PrimaryActionTriggered;
}
```

代码清单 9-13 展示了 `ButtonGetCurrentWeather_PrimaryActionTriggered` 方法的定义。该函数的工作流程如下：当用户轻点按钮时，我首先读取他在文本字段中输入的城市名称。如果城市名称为空，则显示一个带有以下消息的模态窗口：请输入城市名称。为了显示弹窗，我编写了 `DisplayOkAlert` 方法。如代码清单 9-14 所示，该方法使用了与之前在 iOS 应用中显示弹窗相同的类（`UIAlertController` 和 `UIAlertAction`）。不过，弹窗的外观有所不同（见图 9-5）。

![A453659_1_En_9_Fig5_HTML.jpg](img/A453659_1_En_9_Fig5_HTML.jpg)

尽管 tvOS 使用与 iOS 相同的类来显示弹窗，但相应的 tvOS 窗口外观不同（请回看第 1 章）。

```
private TemperatureUnit temperatureUnit = TemperatureUnit.Metric;

private async void ButtonGetCurrentWeather_PrimaryActionTriggered(object sender, EventArgs e)
{
    var cityName = TextFieldCityName.Text;
    if (string.IsNullOrEmpty(cityName))
    {
        DisplayOkAlert("Please enter the city name");
    }
    else
    {
        try
        {
            var weatherInfo = await WeatherServiceHelper.GetWeatherInfo(cityName, temperatureUnit);
            await DisplayWeatherInfo(weatherInfo);
        }
        catch (Exception ex)
        {
            DisplayOkAlert(ex.Message);
        }
    }
}
```

```
private void DisplayOkAlert(string message)
{
    var controller = UIAlertController.Create("HelloTV", message, UIAlertControllerStyle.Alert);
    var okAction = UIAlertAction.Create("OK", UIAlertActionStyle.Default, null);
    controller.AddAction(okAction);
    PresentViewController(controller, true, null);
}
```

如果用户提供了有效的城市名称，`ButtonGetCurrentWeather_PrimaryActionTriggered` 会使用 `WeatherServiceHelper.GetWeatherInfo` 方法向 OpenWeatherMap API 发送请求。该函数返回一个 `WeatherInfo` 类的实例，随后将其传递给 `DisplayWeatherInfo` 方法，以使用接收到的天气状况更新 UI。



