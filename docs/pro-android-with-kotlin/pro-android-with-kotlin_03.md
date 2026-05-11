# 从 Java 到 Kotlin 的过渡

为了激发你的兴趣，我们先来看一个非常简单的应用，它缺少你在更复杂、更专业的应用中会看到的许多功能，然后比较其 Java 和 Kotlin 两种版本。

如果你启动 *Android Studio* 并进入项目创建向导，系统会要求你选择一个模板。选择“Basic Activity”，将最低 SDK 设为 23，并同时禁用旧版支持库和 Kotlin 支持。创建完成后，会出现一个 `MainActivity` 类。其 Java 代码如下（注释已移除）：

```java
package book.andrkotlpro.frontjava;
import android.os.Bundle;
import com.google.android.material.snackbar.Snackbar;
import androidx.appcompat.app.AppCompatActivity;
import android.view.View;
import androidx.navigation.NavController;
import androidx.navigation.Navigation;
import androidx.navigation.ui.AppBarConfiguration;
import androidx.navigation.ui.NavigationUI;
import book.andrkotlpro.frontjava.databinding.ActivityMainBinding;
import android.view.Menu;
import android.view.MenuItem;
public class MainActivity extends AppCompatActivity {
private AppBarConfiguration appBarConfiguration;
private ActivityMainBinding binding;
@Override
protected void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
binding = ActivityMainBinding.inflate(getLayoutInflater());
setContentView(binding.getRoot());
setSupportActionBar(binding.toolbar);
NavController navController = Navigation.findNavController(this, R.id.nav_host_fragment_content_main);
appBarConfiguration = new AppBarConfiguration.Builder(navController.getGraph()).build();
NavigationUI.setupActionBarWithNavController(this, navController, appBarConfiguration);
binding.fab.setOnClickListener(new View.OnClickListener() {
@Override
public void onClick(View view) {
Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
.setAction("Action", null).show();
}
});
}
@Override
public boolean onCreateOptionsMenu(Menu menu) {
getMenuInflater().inflate(R.menu.menu_main, menu);
return true;
}
@Override
public boolean onOptionsItemSelected(MenuItem item) {
int id = item.getItemId();
if (id == R.id.action_settings) {
return true;
}
return super.onOptionsItemSelected(item);
}
@Override
public boolean onSupportNavigateUp() {
NavController navController = Navigation.findNavController(this, R.id.nav_host_fragment_content_main);
return NavigationUI.navigateUp(navController, appBarConfiguration) || super.onSupportNavigateUp();
}
}
```

关于这段 Java 代码的几点说明：类和几乎所有方法前面的 `public` 表示它们从任何地方都可见。这里不能省略，否则框架和应用程序的其余部分将无法使用该类和方法。`setContentView()` 因其“set”特性，是一种非常常见的结构，以至于人们可能会想到允许用 `contentView = s.th.` 这样的写法来代替。一些竞争语言允许这种语法。对于 `setOnClickListener()` 也是如此，可能希望使用 `.onClickListener = s.th.` 代替。`setOnClickListener()` 的参数是一个匿名内部类的对象——这已经是先声明、再实例化并使用的一种简化形式。它甚至可以进一步简化为：

```java
binding.fab.setOnClickListener(
view -> {
Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
.setAction("Action", null).show();
}
);
```

这是因为该接口只有一个方法。向导只是忽略了这种简化形式。

对于各种 `.getSomething()`，我们也可以写成 `.something` 之类的形式，表达相同含义，但更简洁。

一个实现相同功能但*启用* Kotlin 支持的姊妹项目，会生成相应的 Kotlin 代码如下：

```kotlin
package book.andrkotlpro.frontkotlin
import android.os.Bundle
import com.google.android.material.snackbar.Snackbar
import androidx.appcompat.app.AppCompatActivity
import androidx.navigation.findNavController
import androidx.navigation.ui.AppBarConfiguration
import androidx.navigation.ui.navigateUp
import androidx.navigation.ui.setupActionBarWithNavController
import android.view.Menu
import android.view.MenuItem
import book.andrkotlpro.frontkotlin.databinding.ActivityMainBinding
class MainActivity : AppCompatActivity() {
private lateinit var appBarConfiguration: AppBarConfiguration
private lateinit var binding: ActivityMainBinding
override fun onCreate(savedInstanceState: Bundle?) {
super.onCreate(savedInstanceState)
binding = ActivityMainBinding.inflate(layoutInflater)
setContentView(binding.root)
setSupportActionBar(binding.toolbar)
val navController = findNavController(R.id.nav_host_fragment_content_main)
appBarConfiguration = AppBarConfiguration(navController.graph)
setupActionBarWithNavController(navController, appBarConfiguration)
binding.fab.setOnClickListener { view ->
Snackbar.make(view, "Replace with your own action", Snackbar.LENGTH_LONG)
.setAction("Action", null).show()
}
}
override fun onCreateOptionsMenu(menu: Menu): Boolean {
menuInflater.inflate(R.menu.menu_main, menu)
return true
}
override fun onOptionsItemSelected(item: MenuItem): Boolean {
return when (item.itemId) {
R.id.action_settings -> true
else -> super.onOptionsItemSelected(item)
}
}
override fun onSupportNavigateUp(): Boolean {
val navController = findNavController(R.id.nav_host_fragment_content_main)
return navController.navigateUp(appBarConfiguration) || super.onSupportNavigateUp()
}
}
```

更深入地审视 Kotlin 代码，可以发现以下几点观察：

*   我们不需要“;”分隔符——Kotlin 会根据换行符判断语句是否结束，或者是否需要包含下一行。
*   我们不需要在类和方法前面加 `public` —— 在 Kotlin 中，“public”是默认的。
*   我们只需用“`:`”代替 `extends`，从而略微提高可读性。
*   如果函数不返回任何值，我们无需指定 `void` 作为返回类型。Kotlin 可以推断出来。
*   遗憾的是，我们不能像之前建议的那样写成 `contentView = s.th.` —— 例如 Groovy 语言就支持这种写法。在 Kotlin 中无法这样做的原因是，`contentView = s.th.` 这种结构暗示必须存在一个名为 `contentView` 的类字段，而实际情况并非如此。编译器可以检查是否存在名称合适的方法，然后允许这种语法，但 Kotlin 开发者决定施加此限制，如果字段不存在则禁止这种结构。同样的情况也适用于 `setOnClickListener`，因为也不存在 `onClickListener` 字段。
*   我们可以使用函数式结构 `view -> ...` 来代替匿名内部类。只要所调用的类（本例中为监听器）只包含一个方法（例如此处基础接口中的 `void onClick( View v )`），就总是可以采用这种写法。Kotlin 编译器知道它必须使用监听器类的那个特定方法。虽然在 Java 中，此类 lambda 表达式在 JDK 8 之前不可用，但在 Kotlin 中从一开始就支持。

比较的结论是：1117 个字符（省略了导入和空格）的 Kotlin 代码实现了与 1329 个字符的 Java 代码相同的功能。这节省了 17% 的字符量。通常，对于更复杂的类，你可以预期节省 20–30% 的字符量。

尽管语法与 Java 不同，但 Kotlin 编译器会将其源代码转换为与 Java 相同的虚拟机字节码，因此 Kotlin 可以使用现有的海量 Java 库，而转向或同时使用 Kotlin 的 Java 开发者也不会错过这些库。




