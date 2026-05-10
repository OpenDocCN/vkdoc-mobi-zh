# 2. 为我们的需求定制 Espresso


```markdown

Espresso 是一个很好的测试框架，但不可能用预定义的方法和类覆盖所有测试自动化场景。就像 Android 的基础组件可以在应用程序开发过程中自定义一样，Espresso 使我们能够自定义其组件。工程师可以自由创建自己的操作、匹配器和失败处理程序，并将其插入到测试中。在本章中，我们将学习如何创建自定义视图、滑动和 RecyclerView 操作；理解如何构建不同类型的匹配器；以自定义方式处理测试失败；以及在失败时截取适当的屏幕截图。

## 编写自定义 ViewAction

`ViewAction` 是 Espresso 最常用的功能之一。Espresso 提供了大量的 `ViewAction`，但由于它们可能不适合我们的特定需求，我们需要更多。根据我的实践经验，大多数情况下，以下视图操作类型需要自定义：

*   滑动操作
*   RecyclerView 操作
*   `ViewAction`

我们还将讨论在本章中为特定情况自定义简单点击操作的示例。

## 适配 Espresso 滑动操作

在第 1 章中，我们提到了 Espresso 提供的四种滑动操作——`swipeUp()`、`swipeDown()`、`swipeLeft()` 和 `swipeRight()`。这是 `swipeUp()` 操作的实现方式：

```
public static ViewAction swipeUp() {
return actionWithAssertions(new GeneralSwipeAction(Swipe.FAST,
GeneralLocation.translate(GeneralLocation.BOTTOM_CENTER, 0, -EDGE_FUZZ_FACTOR),
GeneralLocation.TOP_CENTER, Press.FINGER));
}
```

正如你可能猜到的，`GeneralLocation.BOTTOM_CENTER` 和 `GeneralLocation.TOP_CENTER` 代表了我们要滑动的视图内部的 `from` 和 `to` 坐标。可以用于 `from` 和 `to` 坐标的完整位置列表包括：`TOP_LEFT`、`TOP_CENTER`、`TOP_RIGHT`、`CENTER_LEFT`、`CENTER`、`CENTER_RIGHT`、`BOTTOM_LEFT`、`BOTTOM_CENTER` 和 `BOTTOM_RIGHT`。`Swipe.FAST` 表示“快速”滑动应持续的时间长度，以毫秒为单位。目前，`Swipe` 有 `FAST`（100 毫秒）和 `SLOW`（1500 毫秒）两种滑动速度。`Press.FINGER` 返回一个大小为 16x16 毫米的触摸目标。其他按压选项包括 `PINPOINT`（1x1 毫米）和 `THUMB`（25x25 毫米）按压区域。`-EDGE_FUZZ_FACTOR` 值根据视图长度定义从边缘到滑动操作起始点的距离。当从精确边缘滑动可能导致不良行为（例如打开导航抽屉）时，这很有用。

其他三个滑动操作以类似方式执行，仅在 `from` 和 `to` 坐标上有所不同。

在某些情况下，这四个滑动操作可能不够用。你可能需要缓慢向左或向右滑动，或者从屏幕中间向上或向下滑动。在这种情况下，你可以创建自己的自定义滑动操作。

为了实现我们自己的操作，我们将遵循 Espresso 滑动操作（如 `swipeDown()`）的实现方式。首先，我们添加自己的 `CustomSwipe` 类型，并将其命名为 `CUSTOM`。这个 `enum` 类应该实现 Espresso 的 `Swiper` 接口，就像声明了 `FAST` 和 `SLOW` 滑动类型的 `Swipe` 枚举一样。

*chapter2.customswipe.CustomSwipe.java.*

```
public enum CustomSwipe implements Swiper {
CUSTOM{
@Override
public Status sendSwipe(UiController uiController,
float[] startCoordinates,
float[] endCoordinates,
float[] precision) {
return sendLinearSwipe(
uiController,
startCoordinates,
endCoordinates,
precision,
swipeCustomDuration);
}
};
/** 每次滑动发送的触摸事件数量。 */
private static final int SWIPE_EVENT_COUNT = 10;
/** 滑动的持续时间 */
private static int swipeCustomDuration = 0;
/**
* 为我们的自定义滑动操作设置持续时间
* @param duration 自定义滑动应持续的毫秒数。
*/
public void setSwipeDuration(int duration) {
swipeCustomDuration = duration;
}
private static Swiper.Status sendLinearSwipe(UiController uiController,
float[] startCoordinates,
float[] endCoordinates,
float[] precision,
int duration) {
...
}
private static float[][] interpolate(float[] start, float[] end, int steps) {
...
return res;
}
}
```

在我们的实现中，我们可以通过 `setSwipeDuration()` 方法设置滑动持续时间来控制滑动时长，该方法会修改静态变量 `swipeCustomDuration`。我们还必须从 Espresso 的 `Swipe` 枚举中复制 `interpolate()` 和 `sendLinearSwipe()` 方法，因为它们不是公共的。完整的源代码可在 `chapter2.customswipe.CustomSwipe.java` 类中找到。

至此，我们已经拥有一个完全可定制的滑动类型。现在我们添加 `swipeCustom()` 视图操作。

*chapter2.customactions.CustomSwipeActions.java.*

```
public class CustomSwipeActions {
/**
* 适用于任何需求的完全可定制的滑动操作
* @param duration 自定义滑动应持续的毫秒数。
* @param from 例如 [GeneralLocation.CENTER]
* @param to 例如 [GeneralLocation.BOTTOM_CENTER]
*/
public ViewAction swipeCustom(int duration, GeneralLocation from, GeneralLocation to) {
CustomSwipe.CUSTOM.setSwipeDuration(duration);
return actionWithAssertions(new GeneralSwipeAction(
CustomSwipe.CUSTOM,
translate(from, 0f, 0f),
to, Press.FINGER)
);
}
/**
* 按给定距离平移给定的坐标。
* 距离以视图大小为单位表示
* -- 1.0 意味着平移相当于视图长度的量。
*/
private static CoordinatesProvider translate(final CoordinatesProvider coords, final float dx, final float dy) {
return new CoordinatesProvider() {
@Override
public float[] calculateCoordinates(View view) {
float xy[] = coords.calculateCoordinates(view);
xy[0] += dx * view.getWidth();
xy[1] += dy * view.getHeight();
return xy;
}
};
}
}
```

`swipeCustom()` 方法首先设置滑动持续时间，然后使用我们的 `CUSTOM` 滑动类型执行 `GeneralSwipeAction`。同样，我们必须从 `GeneralSwipeAction` 类内部复制 `translate()` 方法，因为它无法从类外部访问。

## 练习 6

**编写一个使用自定义滑动操作的测试用例**

1.  编写一个测试用例，通过在 ID 为 `R.id.tasks_list` 的 TODO 列表视图上执行 `swipeDown()` 操作来刷新 TODO 列表。
2.  将第一个任务中的 `swipeDown()` 操作替换为 `swipeCustom()` 视图操作。

```


## 创建自定义 RecyclerView 操作

`RecyclerViewActions` 类提供了可在 RecyclerView 或 RecyclerView 项内使用的有限操作。例如，点击待办事项 RecyclerView 中的整个待办事项项功能很有用，可用于打开项的详细信息。但是，如果我们需要点击复选框来标记待办事项为已完成，该怎么办？

当然，我们可以根据位置来执行此操作。作为掌握测试数据的工程师，我完全控制着每个待办事项的名称，并且可以使所有名称唯一。这使我能够根据名称识别每个待办事项项，然后将焦点缩小到待办事项项内的特定元素。在我们的案例中，我们希望点击复选框。看看此自定义 RecyclerView 视图操作在 `CustomRecyclerViewActions.java` 类的 `clickTodoCheckBoxWithTitle()` 方法中可能是怎样的。

*`chapter2.customactions.CustomRecyclerViewActions.java`*

```
class ClickTodoCheckBoxWithTitleViewAction implements CustomRecyclerViewActions {
private String toDoTitle;
public ClickTodoCheckBoxWithTitleViewAction(String toDoTitle) {
this.toDoTitle = toDoTitle;
}
public static ViewAction clickTodoCheckBoxWithTitle(final String toDoTitle) {
return actionWithAssertions(new ClickTodoCheckBoxWithTitleViewAction(toDoTitle));
}
@Override
public Matcher getConstraints() {
return allOf(isAssignableFrom(RecyclerView.class), isDisplayed());
}
@Override
public String getDescription() {
return "通过点击其复选框来完成任务。";
}
@Override
public void perform(UiController uiController, View view) {
try {
RecyclerView recyclerView = (RecyclerView) view;
RecyclerView.Adapter adapter = recyclerView.getAdapter();
if (adapter instanceof TasksFragment.TasksAdapter) {
int itemCount = adapter.getItemCount();
for (int i = 0; i < itemCount; i++) {
View taskItemView = recyclerView.getLayoutManager().findViewByPosition(i);
TextView textView = taskItemView.findViewById(R.id.title);
if (textView != null && textView.getText() != null) {
if (textView.getText().toString().equals(toDoTitle)) {
CheckBox completeCheckBox = taskItemView.findViewById(R.id.todo_complete);
completeCheckBox.performClick();
}
} else {
throw new RuntimeException(
"无法在位置 " + i + " 的待办事项项的子视图中找到 ID 为 R.id.todo_title 的视图");
}
}
}
uiController.loopMainThreadForAtLeast(ViewConfiguration.getTapTimeout());
} catch (RuntimeException e) {
throw new PerformException.Builder().withActionDescription(this.getDescription())
.withViewDescription(HumanReadables.describe(view)).withCause(e).build();
}
}
}
```

`clickTodoCheckBoxWithTitle()` 视图操作返回一个新的 `ClickTodoCheckBoxWithTitleViewAction` 类，其中 `getConstraints()` 方法过滤掉可分配给 `RecyclerView.class` 且在屏幕上可见的视图：

```
public Matcher getConstraints() {
return allOf(isAssignableFrom(RecyclerView.class), isDisplayed())
}
```

`getDescription()` 方法描述我们的 `ViewAction`。如果测试在 Espresso 异常跟踪中失败，您将看到此描述。

```
public String getDescription() {
return "通过点击其复选框来完成任务。";
}
```

`perform()` 方法在此处执行繁重的工作——我们已经可以依赖我们的视图是 `RecyclerView` 这一事实。然后我们从其中获取适配器，并确保适配器是 `TasksFragment.TasksAdapter` 类的实例。之后，我们遍历适配器中的每个项，并从 ID 为 `R.id.title` 的 `TextView` 中获取项标题。如果项的标题等于 `TaskItem` 中的标题，我们会搜索 ID 为 `R.id.todo_complete` 的 `CheckBox` 元素，并对其调用点击操作。最后，我们让主线程循环一小段时间，以便应用程序处理我们的点击事件。如果列表中不存在具有预期标题的待办事项，它将借助 Espresso 的 `PerformException` 类抛出异常。

*`chapter2.customactions.CustomRecyclerViewActions.java`*

```
public void perform(UiController uiController, View view) {
try {
RecyclerView recyclerView = (RecyclerView) view;
RecyclerView.Adapter adapter = recyclerView.getAdapter();
if (adapter instanceof TasksFragment.TasksAdapter) {
int itemCount = adapter.getItemCount();
for (int i = 0; i < itemCount; i++) {
View taskItemView = recyclerView.getLayoutManager().findViewByPosition(i);
TextView textView = taskItemView.findViewById(R.id.title);
if (textView != null && textView.getText() != null) {
if (textView.getText().toString().equals(toDoTitle)) {
CheckBox completeCheckBox = taskItemView.findViewById(R.id.todo_complete);
completeCheckBox.performClick();
}
} else {
throw new RuntimeException(
"未找到标题为 " + toDoTitle + " 的待办事项项");
}
}
}
uiController.loopMainThreadForAtLeast(ViewConfiguration.getTapTimeout());
} catch (RuntimeException e) {
throw new PerformException.Builder().withActionDescription(this.getDescription())
.withViewDescription(HumanReadables.describe(view)).withCause(e).build();
}
}
```

`CustomRecyclerViewActions.java` 类中的 `scrollToLastHolder()` 方法展示了 `RecyclerViewAction` 的另一个示例，并说明了如何在 `RecyclerView` 上实现滚动操作。由于 `getConstraints()` 和 `getDescription()` 方法相同，我们将不再赘述。至于 `perform()` 方法，您可以看到它从 `RecyclerView` 适配器检索项数，并使用 `scrollToPosition()` `RecyclerView` 方法滚动到最后一项：

```
public void perform(UiController uiController, View view) {
RecyclerView recyclerView = (RecyclerView) view;
int itemCount = recyclerView.getAdapter().getItemCount();
try {
recyclerView.scrollToPosition(itemCount - 1);
uiController.loopMainThreadUntilIdle();
} catch (RuntimeException e) {
throw new PerformException.Builder().withActionDescription(this.getDescription())
.withViewDescription(HumanReadables.describe(view)).withCause(e).build();
}
}
```

## 练习 7

**编写一个自定义 RecyclerView 操作**

1. 基于 `clickTodoCheckBoxWithTitle()` 操作，实现一个验证待办事项项不在列表中的 `RecyclerView` 操作。提示：在 `perform()` 方法中使用一个 `JUnit assert` 方法。最终用法可能如下所示：

```
onView(withId(R.id.tasks_list)).perform(assertNotInTheListTodoWithTitle("title"))
```

## 编写自定义匹配器

Espresso 匹配器是强大的工具，有助于在应用程序布局中定位或验证元素。Espresso 视图匹配器可能无法完全满足您的用例或需求。在这种情况下，您可以创建自定义匹配器。

### 为简单 UI 元素创建自定义匹配器

我们将从简单的匹配器作为介绍开始。以下用例将用作示例：

* 添加一个没有标题和描述的新待办事项，结果，待办事项标题字段的提示颜色应变为红色。

在这种情况下，`BoundedMatcher` 是理想的选择，因为它返回 `Matcher<View>` 类型，但仅对 `EditText` 类型的元素执行操作。请参考 `CustomViewMatchers.java` 类，该类包含 `withHintColor()` 匹配器实现，该实现匹配 `EditText` 提示的颜色。

*`chapter2.custommatchers.CustomViewMatchers.java`*

```
public static Matcher withHintColor(final int expectedColor) {
return new BoundedMatcher(EditText.class) {
@Override
protected boolean matchesSafely(EditText editText) {
return expectedColor == editText.getCurrentHintTextColor();
}
@Override
public void describeTo(Description description) {
description.appendText("待办事项标题: " + expectedColor);
}
};
}
```



此处，`BoundedMatcher` 使我们能够匹配作为 Android `View` 类型子类型的 `EditText` 视图，并返回 `Matcher<View>` 类型的最终对象。当在屏幕上识别到 `EditText` 元素时，其提示颜色会与预期颜色进行比较，返回一个 `true` 或 `false` 值。每当返回 `true` 时，就意味着找到了具有预期提示颜色的 `EditText`。

以下是在真实测试用例中使用 `withHintColor()` 匹配器的方式（详情请参考 `CustomViewMatchers.java` 类）。

*chapter2.custommatchers.CustomViewMatchersTest.java.*

```
@Test
public void addsNewToDoError() {
// 添加新的待办事项
onView(withId(R.id.fab_add_task)).perform(click());
onView(withId(R.id.fab_edit_task_done)).perform(click());
onView(withId(R.id.add_task_title))
.check(matches(hasErrorText("标题不能为空！")))
.check(matches(withHintColor(Color.RED)));
}
```

### 实现自定义 RecyclerView 匹配器

在我看来，`RecyclerView` 匹配器是 Espresso 中最隐蔽的部分。Android 文档并未解释如何实现它们，但根据本书之前的示例，您可能猜到可以使用 `BoundedMatcher` 类来创建它们。

我们将参考示例应用程序，创建一个 `RecyclerView` 匹配器，用于根据待办事项列表中的标题来匹配待办事项。同样，由于我们可以完全控制测试数据，因此假设标题是唯一的。

*chapter2.custommatchers.RecyclerViewMatchers.java.*

```
public static Matcher withTitle(final String taskTitle) {
Checks.checkNotNull(taskTitle);
return new BoundedMatcher(
TasksAdapter.ViewHolder.class) {
@Override
protected boolean matchesSafely(TasksAdapter.ViewHolder holder) {
final String holderTaskTitle = holder.getHolderTask().getTitle();
return taskTitle.equals(holderTaskTitle);
}
@Override
public void describeTo(Description description) {
description.appendText("任务标题: " + taskTitle);
}
};
}
```

这里重要的是理解被测应用程序，并知道使用哪个 `ViewHolder`。在示例中，我们将 `TasksFragment.TasksAdapter.ViewHolder` 作为第二个参数放入 `BoundedMatcher`。当我们的匹配器在屏幕上识别出具有该类型的元素时，我们会从 holder 中检索标题，并将其与我们作为匹配器参数提供的标题进行比较。

*chapter2.custommatchers.RecyclerViewMatchers.java.*

```
public static Matcher withTask(final TaskItem taskItem) {
Checks.checkNotNull(taskItem);
return new BoundedMatcher(
TasksAdapter.ViewHolder.class) {
@Override
protected boolean matchesSafely(TasksAdapter.ViewHolder holder) {
final String holderTaskTitle = holder.getHolderTask().getTitle();
final String holderTaskDesc = holder.getHolderTask().getDescription();
return taskItem.getTitle().equals(holderTaskTitle)
&& taskItem.getDescription().equals(holderTaskDesc);
}
@Override
public void describeTo(Description description) {
description.appendText("任务标题: " + taskItem.getTitle()
+ " 和描述: " + taskItem.getDescription());
}
};
}
public static Matcher withTaskTitleFromTextView(final String taskTitle) {
Checks.checkNotNull(taskTitle);
return new BoundedMatcher(
TasksAdapter.ViewHolder.class) {
@Override
protected boolean matchesSafely(TasksAdapter.ViewHolder holder) {
final TextView titleTextView = (TextView) holder.itemView.findViewById(R.id.title);
return taskTitle.equals(titleTextView.getText().toString());
}
@Override
public void describeTo(Description description) {
description.appendText("任务标题: " + taskTitle);
}
};
}
}
```

## 使用自定义 FailureHandler 处理错误

Espresso 测试框架非常灵活且可定制，错误处理也不例外。Espresso 提供了一个名为 `FailureHandler` 的接口，可以在自定义失败处理器中实现该接口，以管理测试执行期间发生的失败。

实现自定义 `FailureHandler` 的原因可能是为了减少异常文本，或保存屏幕截图或其他应用程序数据，例如保存设备转储等。例如，示例待办事项应用程序代码库包含一个 `CustomFailureHandler`。

*chapter2.customfailurehandler.CustomFailureHandler.java.*

```
public class CustomFailureHandler implements FailureHandler{
private final FailureHandler delegate;
public CustomFailureHandler(Context targetContext) {
delegate = new DefaultFailureHandler(targetContext);
}
@Override
public void handle(Throwable error, Matcher viewMatcher) {
try {
delegate.handle(error, viewMatcher);
} catch (NoMatchingViewException e) {
// 例如保存设备转储、截屏等。
throw e;
}
}
}
```

您可以在 `handle()` 方法中看到 `try...catch` 块。这就是我们捕获错误并可以对其执行任何操作的地方。通常在完成所有必要步骤后，异常会继续向外传播。

为了让 Espresso 使用 `CustomFailureHandler` 拦截每个测试失败，在测试类或基测试类中注册它非常重要，如 `BaseTest.java` 类所示：

```
@Before
public void setUp() throws Exception {
setFailureHandler(new CustomFailureHandler(
InstrumentationRegistry.getInstrumentation().getTargetContext()));
}
```

如果您在基测试类中注册它，请不要忘记在测试类中调用 `super.setUp()`：

```
@Before
public void setUp() throws Exception {
super.setUp();
}
```

## 练习 8

**将 `CustomFailureHandler` 应用于新的测试类**

1.  创建一个新的测试类，其中包含一个每次运行都会失败的测试方法。将 `CustomFailureHandler` 应用于它。

## 测试失败时截取并保存屏幕截图

运行测试很重要，但获得适当且描述性的测试结果同样重要，尤其是当测试失败时，这样才能轻松分析。`AndroidJUnitRunner` 使用的 JUnit 报告器以老旧、简单的原始文本格式报告测试结果。工程师随后需要根据自身需求对其进行调整。当然，其中一项需求就是在测试失败时创建屏幕截图。有许多第三方库和工具可以在测试失败时截取屏幕截图。Square 的 Spoon 就是一个很好的例子。但在这里，我们将讨论 JUnit 和 Espresso 自带的原生解决方案。

让我们确定在测试运行流程中想要实现的目标：

1.  识别测试失败的时刻。

2.  截取屏幕截图并恰当地命名。

3.  将屏幕截图保存在给定的设备或模拟器上。

从 4.9 版本开始，JUnit 库提供了一种 `TestWatcher` 机制，允许我们监控并记录通过的测试和失败的测试。它是一个扩展了 `TestRule` 的抽象类，使我们能够对以下测试状态做出反应：

*   `succeeded(Description description)` — 在测试成功时调用。
*   `failed(Throwable e, Description description)` — 在测试失败时调用。
*   `skipped(AssumptionViolatedException e, Description description)` — 在测试因假设失败而跳过时调用。
*   `starting(Description description)` — 在测试即将开始时调用。
*   `finished(Description description)` — 在测试方法完成时调用（无论是通过还是失败）。

这里我们对 `failed()` 方法感兴趣，它将覆盖 `BaseTest` 类（不过，其他方法在很多情况下也可能有用）。这解决了我们的第一点（识别测试失败的时刻）。



Android 测试支持库提供了 `Screenshot` 和 `ScreenshotCapture` 类，用于在 Android 设备或模拟器上进行仪表测试期间捕获位图格式的屏幕截图：

```
private void captureScreenshot(final String name) throws IOException {
    ScreenCapture capture = Screenshot.capture();
    capture.setFormat(Bitmap.CompressFormat.PNG);
    capture.setName(name);
    capture.process();
}
```

至于截图名称，我们需要借助 JUnit 4.7 版本中提供的 `TestName()` JUnit 规则。`TestName` 规则使得当前测试名称在测试内部可用。它通过 `getMethodName()` 函数返回当前正在运行的测试方法名称：

```
@Rule
public TestName testName = new TestName();
```

第二点也已经解决（截取屏幕截图并为其命名）。

实际上，这几乎已经解决了，因为我们需要授予以下权限，才能让 `Screenshot` 类将截图保存到外部存储位置：

* `android.Manifest.permission.READ_EXTERNAL_STORAGE`
* `android.Manifest.permission.WRITE_EXTERNAL_STORAGE`

幸运的是，Android 测试支持库提供了 `GrantPermissionRule` 来在运行时执行此操作。唯一的限制是它只能从 Android M（API 级别 23）开始使用：

```
@Rule
public GrantPermissionRule mRuntimePermissionRule = GrantPermissionRule
    .grant(android.Manifest.permission.WRITE_EXTERNAL_STORAGE,
           android.Manifest.permission.READ_EXTERNAL_STORAGE);
```

至此，所有三点都已解决（最后一点是将截图保存在给定的设备或模拟器上），这就是它在 `BaseTest.class` 中的样子。

`com.example.android.architecture.blueprints.todoapp.test.BaseTest.java.`

```
@RunWith(AndroidJUnit4.class)
public class BaseTest {
    ......
    @Rule
    public GrantPermissionRule mRuntimePermissionRule = GrantPermissionRule
        .grant(android.Manifest.permission.WRITE_EXTERNAL_STORAGE,
               android.Manifest.permission.READ_EXTERNAL_STORAGE);
    @Rule
    public TestName testName = new TestName();
    public class ScreenshotWatcher extends TestWatcher {
        @Override
        protected void succeeded(Description description) {
            // 一切正常，通知所有人
        }
        @Override
        protected void failed(Throwable e, Description desc) {
            try {
                captureScreenshot(testName.getMethodName());
            } catch (IOException e1) {
                e1.printStackTrace();
            }
        }
        private void captureScreenshot(final String name) throws IOException {
            ScreenCapture capture = Screenshot.capture();
            capture.setFormat(Bitmap.CompressFormat.PNG);
            capture.setName(name);
            capture.process();
        }
    }
}
```

最后一点——截图将保存在 `sdcard/Pictures/screenshots` 目录中。在 Android 模拟器上，路径是 `/storage/emulated/0/Pictures/screenshots`。

## 练习 9

**使一个测试失败并观察截图**

1.  修改其中一个测试，使其失败。运行测试。测试运行后，借助 `adb` 命令，在设备或模拟器上启动 shell 会话，并导航到包含截图的文件夹。
2.  将从步骤 1 中获取的截图从设备拉到您的硬盘上。

## 总结

如您所见，Android 的 Espresso 是一个灵活且可定制的框架，允许我们创建自定义类和方法来满足特定的测试需求。当然，也存在一些限制，例如缺少 `RecyclerView` 匹配器。这些限制可以通过使用自定义的 `ViewAction` 来缓解。创建自定义的 `ViewActions`、`ViewMatchers` 以及其他方法和类，对于有经验的 Espresso 用户来说是必备知识，有时甚至是必须掌握的技能。除此之外，您还可以完全自定义 UI 错误处理，并在每次测试错误时执行所需操作。

