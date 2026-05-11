# 目录

第 1 章：使用 Xamarin 进行移动开发 1  
什么是 Xamarin？ 1  
封装的本地 API 2  
开发环境 2  
UI 设计器 3  
熟悉的部分：C#和.NET 技术 3  
新增部分：移动开发技术 4  
移动 UI 4  
Xamarin.Forms 与平台特定 UI 5  
移动 UI 设计 5  
Xamarin.Forms 自定义渲染器 6  
数据访问层 6  
使用 SQLite 进行本地数据访问 7  
数据绑定 7  
跨平台开发 7  
小结 8  

第 2 章：构建移动用户界面 9  
理解 Xamarin.Forms 9  
Xamarin.Forms 解决方案架构 11  
理解平台特定 UI 方法 12  
平台特定 UI 解决方案架构 13  
Xamarin.Android 13  
Xamarin.iOS 14  
Windows Phone SDK 15  
选择 Xamarin.Forms 还是平台特定 UI 15  
结合两种方法使用自定义渲染器 16  
探索移动 UI 的元素 17  
使用 Xamarin.Forms UI 17  
页面 18  
布局 19  
视图 19  
创建 Xamarin.Forms 解决方案 20  
Xamarin.Forms 共享代码 22  
应用程序生命周期方法：OnStart、OnSleep 和 OnResume 23  
使用 ContentPage 构建页面 23  
Xamarin.Android 24  
Xamarin.iOS 24  
Windows Phone 应用程序 25  
核心库 26  
设置应用程序的主页面 26  
添加 Xamarin.Forms 视图 27  
标签视图 27  
使用 StackLayout 放置视图 28  
背景颜色和字体颜色 29  
使用字体 29  
使用平台特定字体 30  
按钮视图 31  
设置视图对齐方式和大小：HorizontalOptions 和 VerticalOptions 33  
使用 LayoutOptions 进行对齐 33  
AndExpand 用空格填充 33  
用于文本输入的 Entry 视图 34  
BoxView 35  
图像视图 36  
Source 属性 37  
本地图像 37  
图像大小调整：Aspect 属性 37  
使用 GestureRecognizer 使图像可点击 38  
最终确定 StackLayout 39  
ScrollView 39  
分配 ContentPage.Content 属性 40  
整个页面的内边距 40  
代码完成：添加 Xamarin.Forms 视图 41  
小结 44  

第 3 章：使用布局进行 UI 设计 45  
理解自定义控件 46  
使用 Xamarin.Forms 布局 47  
StackLayout 47  
整个布局的内边距 48  
垂直方向堆叠 49  
水平方向堆叠 50  
嵌套布局 52  
使用 LayoutOptions 扩展和内边距视图 52  
代码完成：StackLayout 53  
RelativeLayout 55  
设置视图位置和大小 56  
使用约束 56  
代码完成：RelativeLayout 62  
AbsoluteLayout 64  
使用 SetLayoutBounds 创建边界对象 66  
通过使用 SetLayoutFlags 绑定到边界对象 68  
代码完成：AbsoluteLayout 69  
Grid 71  
设置行和列的大小 73  
调整大小以适应视图 74  
设置精确大小 74  
扩展视图以填充可用空间 75  
按比例扩展视图 76  
创建多单元格视图 77  
单元格之间的内边距 79  
代码完成：Grid 79  
ContentView 81  
代码完成：ContentView 82  
Frame 83  
使用 Android 布局 85  
LinearLayout 86  
使用 Activity 显示布局 87  
在代码中创建布局 88  
处理嵌套布局 89  
RelativeLayout 90  
TableLayout 91  
GridLayout 93  
水平方向从左到右填充行 94  
垂直方向从上到下填充列 95  
指定行和列 96  
创建多单元格视图 97  
创建动态图像网格 98  
FrameLayout 98  
Fragment 99  
使用 iOS 布局 99  
使用 AutoLayout 100  
使用可视化格式语言添加约束 101  
使用 Frame 102  
小结 104  

第 4 章：使用控件进行用户交互 105  
Xamarin.Forms 视图 106  
Picker 107  
DatePicker 108  
TimePicker 110  
Stepper 112  
Slider 113  
Switch 114  
缩放、旋转、透明度、可见性和焦点 115  
代码完成：Xamarin.Forms 视图 115  
Android 控件 119  
Spinner 119  
代码完成：Spinner 122  
DatePicker 123  
使用 DatePickerDialog 创建模态 DatePicker 124  
代码完成：DatePickerExample 126  
TimePicker 127  
SeekBar 128  
CheckBox 129  
Switch 130  
自定义标题、Switch 文本和状态 131  
RadioButton 131  
代码完成：Android 控件 133  
iOS 控件 135  
UIPickerView 135  
将 UIPickerView 制作为弹出窗口 138  
代码完成：UIPickerView 140  
UIDatePicker 142  
将 UIDatePicker 制作为弹出窗口 143  
指定要显示的字段 145  
代码完成：UIDatePicker 145  
UIStepper 146  
UISlider 147  
CheckBox：使用 UISwitch 或 MonoTouch.Dialog 148  
UISwitch 149  
代码完成：iOS 控件 150  
小结 152  

第 5 章：创建可滚动列表 153  
数据适配器 153  
Xamarin.Forms ListView 153  
绑定到字符串列表 154  
选择项 155  
绑定到数据模型 157  
代码完成：绑定到数据模型 159  
添加图像 160  
自定义列表行 162  
代码完成：自定义列表行 165  
添加按钮 167  
使用 Button 视图 167  
使用上下文操作 169  
分组标题 171  
自定义组标题 174  
创建跳转列表 177  
ListView 自动滚动 178  
优化性能 179  
Android ListView 180  
使用 ListActivity 180  
绑定到字符串数组 180  
选择项 181  
多选 182  
绑定到数据模型 183  
数据模型 183  
适配器 183  
Activity 184  
优化性能 185  
使用内置行视图 186  
自定义列表行 188  
在自定义行中选择项 190  
代码完成：自定义列表行 190  
分组标题 192  
iOS UITableView 195  
绑定到字符串数组 195  
选择项 197  
多选 198  
绑定到数据模型 198  
数据模型 199  
适配器 199  
视图控制器 200  
使用内置行视图 201  
单元格分隔符 203  
自定义列表行 204  
分组标题 206  
代码完成：分组适配器 209  
代码完成：分组视图控制器 210  
使用表格样式高亮分组 211  
为列表行添加附件 212  
选择附件 214  
优化性能 214  
列表的替代方法：UITableViewContr oller 215  
小结 215  

第 6 章：导航 217  
导航模式 217  
层级式 218  
模态 218  
状态管理 219  
Xamarin.Forms 导航 220  
使用 NavigationPage 进行层级导航 220  
在导航栈上压入和弹出屏幕 223  
设置页面标题 224  
自定义导航栏 224  
处理返回按钮 225  
创建下拉菜单 225  
模态 226  
使用 NavigationPage 实现全屏模态 226  
使用警报进行用户通知 226  
使用操作表显示弹出菜单 227  
管理状态 228  
将数据传递到页面参数中 228  
使用 Properties 字典进行磁盘持久化 229  
使用静态全局类 229  
使用 Application 对象上的静态属性 230  
下钻列表 231  
按项使用 ListView 231  
代码完成：下钻列表 233  
按页面使用 ListView 234  
使用 TableView 进行页面分组 235  
使用 MasterDetailPage 的导航抽屉 238  
使用 TabbedPage 的选项卡 241  
创建数据绑定选项卡 242  
在 TabbedPage 中放置 NavigationPage 244  
Springboard 245  
使用手势识别器使图标可点击 247  
使用 CarouselPage 的轮播 247  
Android 导航 249  
使用 Intent 启动新 Activity 249  
使用工具栏进行层级导航 250  
处理向上按钮 255  
添加弹出菜单 256  
自定义工具栏 257  
使用导航栏 259  
处理返回按钮 259  
Fragment 260  
模态导航 263  
使用 DialogFragment 创建模态 263  
使用 DialogFragment 创建警报 265  
使用 AlertDialog 创建模态布局 267  
PopupMenu 268  
使用 Bundle 管理状态 269  
传递字符串 270  
传递对象 270  
创建 Bundle 271  
使用静态全局类和 StartActivityForResult 271  
下钻列表 272  
按页面使用 ListView 272  
按项使用 ListView 273  
结合工具栏使用 ListView 273  
导航抽屉 274  
使用 ActionBar 的选项卡 274  
代码完成：TabMenuActivity.cs 278  
iOS 导航 279  
使用 Storyboard、Scene 和 Segue 280  
使用 Nib 281  
[层级导航](#A978-1-4842-0214-2_6_



