# 第 14 章：位置感知 Web 应用程序

### 使用 Google 地图将用户定位在地图上

为了使用位置 API 返回的数据，我们将创建一个示例，其中用户通过 Google 地图被定位在地图上。

Google 地图带有一个丰富的 API，在原生应用程序中，该 API 也被 Cocoa Touch 的`MapKit`框架所使用。它将为你带来与 iPhone 上的地图应用程序相同的功能。其优点包括免费、无需预先注册，并且使用 JavaScript 非常容易上手。

#### 显示地图

让我们从头开始：显示地图。我们的 Web 应用程序要做的是让用户在地球卫星视图上显示一个位置，其精度等级由一个圆来表示。

首先，基于你的 Web 应用程序模板创建一个新项目，并按如下方式修改`index.html`文件：

```html
...
<title>地理位置示例</title>
...
<body>
<div class="view">
  <div id="map"></div>
  <div class="header-wrapper">
    <h1>地理位置</h1>
  </div>
</div>
</body>
```

标识为`map`的`<div>`元素是容纳地图的区域。我们希望它占据可用空间，并有一个半透明的头部覆盖在其上方。以下是实现此效果的样式：

```html
<head>
...
<style>
  .header-wrapper {
    background-color: rgba(0,0,0,0.65);
    position: absolute;
    top: 0;
    width: 100%;
  }
  .view { height: 100%; }
  #map { min-height: 100%; }
</style>
</head>
```

## 第 14 章：位置感知 Web 应用程序

现在，我们可以调用 Google Maps API 在我们的容器内渲染地图。`sensor`参数告诉 API 设备是否具备定位功能；我们将其设置为`true`。

```html
<head>
  ...
  </style>
  <script src="http://maps.google.com/maps/api/js?sensor=true"></script>
  <script>
    var ns = google.maps; // 命名空间
    var map;

    function init() {
      var latlng = new ns.LatLng(0, 0);
      var options = {
        zoom: 2,
        center: latlng,
        disableDefaultUI: true,
        scaleControl: true,
      };
```



