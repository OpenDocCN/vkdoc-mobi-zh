# 排版后内容

如果只有一个设备，可以省略 `-s` 开关。

进入 shell 后，可以使用几个命令获取权限信息。首先，可以通过以下命令列出所有已安装的包：

```
cmd package list package
```

要显示 *所有* 危险权限，或查看某个包的权限状态，或授予/撤销一个或多个权限，可以使用：

```
cmd package list permissions -d -g
dumpsys package 
pm [grant|revoke]  ...
```

注意：当前版本的 `dumpsys` 会同时显示 *请求的* 和 *已授予的* 权限——不要被关于此问题的旧博客文章混淆。

## 8. APIs

本章主题是作为应用基石的 API。这包括：

*   *数据库*
*   *调度*
*   *通知*
*   *联系人*
*   *搜索框架*
*   *位置和地图*
*   *偏好设置*

### 数据库

Android 提供了两种处理数据库的方式：使用 Android 操作系统自带的 *SQLite* 库，或者使用 *Room* 架构组件。推荐使用后者，因为它在数据库和客户端之间添加了一个抽象层，简化了 Kotlin 对象与数据库存储对象之间的映射。

你可以在在线文档中找到关于 SQLite 的详尽信息，并在网上找到大量示例——本书将讨论 Room，因为抽象层引入的关注点分离有助于编写更好的代码，同时 Room 还能避免样板代码，从而显著缩短数据库代码。

### 配置 Room 环境

由于 Room 是支持架构组件，因此必须在 Android Studio 构建脚本中进行配置。为此，请打开 *模块* 的 `build.gradle` 文件（不是项目级别的那个！），并在顶层（不在任何花括号内）写入：

```
apply plugin: 'kotlin-kapt'
```

这是 Kotlin 编译器插件，用于支持注解处理。在 “dependencies” 部分内，添加：

```
def room_version = "2.4.2"
implementation "androidx.room:" +
"room-runtime:$room_version"
kapt "androidx.room:" +
"room-compiler:$room_version"
```

### Room 架构

Room 的设计以易用性为目标——你主要处理三种对象：

*   **数据库**：数据库的持有者。用 SQL 语言术语来说，它包含多个表。从技术无关的角度看，数据库包含多个实体容器。

*   **实体**：在 SQL 世界中代表一个表。从技术无关的角度看，这是一个以用法为中心的字段集合。例如，公司内部的 *Employee* 或包含与人员/合作伙伴通信信息的 *Contact*。

*   **DAO 或数据访问对象**：包含从数据库检索数据的访问逻辑。因此，它在程序逻辑和数据库模型之间充当接口。通常每个实体类对应一个 DAO，但也可能有多个 DAO 用于各种组合。例如，可以为 Employee 和 Contact 两个实体分别创建 `EmployeeDao` 和 `ContactDao`，同时还可以创建一个合并员工及其联系信息的 `PersonDao`。

### 数据库

声明数据库的方法如下：

```
import androidx.room.*
@Database(entities =
arrayOf(Employee::class, Contact::class),
version = 1)
abstract class MyDatabase : RoomDatabase() {
abstract fun employeeDao(): EmployeeDao
abstract fun contactDao(): ContactDao
abstract fun personDao(): PersonDao
}
```

在 `@Database` 注解中，声明所有使用的实体类，并以抽象函数的形式提供 DAO 类的工厂方法。你无需实现这个抽象数据库类——Room 库会根据签名和注解自动为你提供实现！版本号将帮助你升级不同的数据模型版本——后续会对此进行更多讨论。

### 实体

接下来，我们实现实体类，这在 Kotlin 中极其简单：


```kotlin
@Entity
data class Employee(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
var firstName:String,
var lastName:String)

@Entity
data class Contact(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
var emailAddr:String)
```

你可以看到，每个实体都需要一个`Int`类型的主键。`autoGenerate = true`负责自动确保其唯一性。

这些实体类定义的数据库表中的列名与变量名匹配。如果你想更改这一点，可以添加另一个注解`@ColumnInfo`：

```kotlin
@Entity
data class Employee(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
@ColumnInfo(name = "first_name") var firstName:String,
@ColumnInfo(name = "last_name") var lastName:String)
```

这将导致使用`"first_name"`和`"last_name"`作为表列名。

同样，表名取自实体类名`"Employee"`和`"Contact"`。你也可以更改这一点：只需向`@Entity`注解添加一个`tableName`参数，如下所示：

```kotlin
@Entity(tableName = "empl")
data class Employee(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
@ColumnInfo(name = "first_name") var firstName:String,
@ColumnInfo(name = "last_name") var lastName:String)
```

虽然使用单个整数主键通常是一个好主意，但你也可以使用组合键。为此，`@Entity`中还有一个额外的注解参数可供使用：

```kotlin
@Entity(tableName = "empl",
primaryKeys = tableOf("first_name","last_name"))
data class Employee(
@ColumnInfo(name = "first_name") var firstName:String,
@ColumnInfo(name = "last_name") var lastName:String)
```

实体也可以拥有不会被持久化的字段。从设计角度来看，这可能不是一个好主意，但如果你需要这样的字段，可以添加它并使用`@Ignore`注解，如下所示：

```kotlin
@Entity(tableName = "empl")
data class Employee(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
var firstName:String = "",
var lastName:String = "",
@Ignore var salary:Int)
```

由于 Room 实现方式的限制，如果你添加了这样的`@Ignore`注解，则所有字段都必须分配默认值，即使未使用。

## 关系

Room 在设计上不允许实体之间存在直接关系。例如，你不能将一个`Contact`实体列表添加为`Employee`实体的类成员。但是，可以声明外键关系，这有助于维护数据一致性。

> 注意：
> 另请参阅：`https://developer.android.com/training/data-storage/room/relationships`

为此，请添加一个`foreignKeys`注解属性，如下面的代码片段所示：

```kotlin
@Entity(
foreignKeys = arrayOf(
ForeignKey(entity = Employee::class,
parentColumns = arrayOf( "uid" ),
childColumns = arrayOf( "employeeId" ),
onDelete = ForeignKey.CASCADE,
onUpdate = ForeignKey.CASCADE,
deferred = true)),
indices = arrayOf(
Index("employeeId"))
)
@Entity
data class Contact(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
var employeeId:Int,
var emailAddr:String)
```

关于该构造的一些说明：

*   在 Java 中，你会编写`@Entity(foreignKeys = @ForeignKey( ... ))`。Kotlin 不允许注解内部嵌套注解。在这种情况下，使用构造函数作为替代，这归结为省略内部注解的`@`。
*   在 Java 中，注解属性值数组的写法是`name = { ..., ... }`。这在 Kotlin 中无法使用，因为花括号不能作为数组初始化器。相反，会使用`arrayOf(...)`库方法。
*   `childColumns`属性指向*本*实体中的引用键，即此处的`Contact.employeeId`。
*   `parentColumns`属性指向被引用的外键实体，此处为`Employee.uid`。
*   `onDelete`属性指定当父项被删除时该如何处理。值`ForeignKey.CASCADE`意味着也自动删除所有子项，即关联的`Contact`实体。所有可能的值如下：
    *   **CASCADE**：将所有操作传递到子-父关系树的根。
    *   **NO_ACTION**：不执行任何操作。这是默认值，如果因更新或删除操作导致关系破坏，则会引发异常。
    *   **RESTRICT**：类似于`NO_ACTION`，但会在删除或更新发生时立即进行检查。
    *   **SET_NULL**：当父项删除或更新发生时，所有子键列都设置为`null`。
    *   **SET_DEFAULT**：当父项删除或更新发生时，所有子键列都设置为其默认值。
*   `onUpdate`属性指定当父项被更新时该如何处理。值`ForeignKey.CASCADE`意味着也自动更新所有子项，即关联的`Contact`实体。可能的值与前述`onDelete`相同。
*   设置`deferred = true`会将一致性检查推迟到数据库事务提交时执行。例如，如果父项和子项在同一事务中创建，这可能很重要。
*   外键必须是相应索引的一部分。此处，`Contact.employeeId`获得了索引。更多关于索引的信息，请参见下文。

### 嵌套对象

虽然除了通过外键手动定义之外，无法定义对象间关系，但你可以在对象侧定义层次化对象的嵌套。例如，从`Employee`实体

```kotlin
@Entity
data class Employee(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
var firstName:String,
var lastName:String)
```

你可以提取出名字和姓氏，改为编写：

```kotlin
data class Name(var firstName:String, var lastName:String)
@Entity
data class Employee(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
@Embedded var name:Name)
```

注意，这对数据模型的数据库端没有影响。关联的表仍然会有`"uid"`、`"firstName"`和`"lastName"`列。由于此类嵌入对象的数据库标识与其字段名称绑定，当有多个相同嵌入类型的嵌入对象时，你必须使用`prefix`属性来消除名称歧义，如下所示：

```kotlin
data class Name(var firstName:String, var lastName:String)
@Entity
data class Employee(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
@Embedded var name:Name,
@Embedded(prefix="spouse_") var spouseName:Name)
```

这将使表拥有`"uid"`、`"firstName"`、`"lastName"`、`"spouse_firstName"`和`"spouse_lastName"`列。

如果愿意，你可以在可嵌入类内部使用 Room 注解，例如，使用`@ColumnInfo`注解来指定自定义列名：

```kotlin
data class Name(
@ColumnInfo(name = "first_name") var firstName:String,
@ColumnInfo(name = "last_name") var lastName:String)
@Entity
data class Employee(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
@Embedded var name:Name)
```

## 使用索引

为了提升数据库查询性能，你可以声明一个或多个索引，用于特定字段或字段组合。你不需要为唯一键执行此操作；这是自动完成的。但对于你想要定义的任何其他索引，请编写类似如下的内容：

```kotlin
@Entity(indices = arrayOf(
Index("employeeId"),
Index(value = arrayOf("country","city"))
)
)
data class Contact(
@PrimaryKey(autoGenerate = true) var uid:Int = 0,
var employeeId:Int,
var emailAddr:String,
var country:String,
var city:String)
```

这将添加一个支持使用外键字段`"employeeId"`进行快速查询的索引，以及另一个支持在给定国家和城市条件下进行快速查询的索引。
```


# Room 数据库访问与查询优化

如果你将 `unique = true` 作为属性添加到 `@Index` 注解中，Room 将确保该表不能有两个具有相同索引值的条目。例如，我们为 `Employee` 添加一个 SSN（社会安全号码）字段，并为其定义唯一索引：

```kotlin
@Entity(indices = arrayOf(
    Index(value = arrayOf("ssn"), unique = true)
)
)
data class Employee(
    @PrimaryKey(autoGenerate = true) var uid: Int = 0,
    var ssn: String,
    @Embedded var name: Name
)
```

如果现在尝试向数据库添加两个具有相同 SSN 的员工，Room 将抛出异常。

## 数据访问：DAO

数据访问对象（Data Access Objects，简称 DAOs）提供访问数据库的逻辑。我们已经看到，在数据库声明内部，必须在工厂方法中列出所有 DAO，如下所示：

```kotlin
@Database(entities =
    arrayOf(Employee::class, Contact::class),
    version = 1)
abstract class MyDatabase : RoomDatabase() {
    abstract fun employeeDao(): EmployeeDao
    abstract fun contactDao(): ContactDao
    abstract fun personDao(): PersonDao
}
```

在此示例中，我们声明了三个 DAO 供 Room 使用。对于实际实现，我们不需要完整的 DAO 类。只需声明接口或抽象类即可；Room 将为我们完成其余工作。

例如，对于实体 `Employee`，其 DAO 类可能如下所示：

```kotlin
@Entity
data class Employee(
    @PrimaryKey(autoGenerate = true) var uid: Int = 0,
    @ColumnInfo(name = "first_name") var firstName: String,
    @ColumnInfo(name = "last_name") var lastName: String
)
```

```kotlin
@Dao
interface EmployeeDao {
    @Query("SELECT * FROM employee")
    fun getAll(): List<Employee>

    @Query("SELECT * FROM employee" +
           " WHERE uid IN (:uIds)")
    fun loadAllByIds(uIds: IntArray): List<Employee>

    @Query("SELECT * FROM employee" +
           " WHERE last_name LIKE :name")
    fun findByLastName(name: String): List<Employee>

    @Query("SELECT * FROM employee" +
           " WHERE last_name LIKE :lname AND " +
           "       first_name LIKE :fname LIMIT 1")
    fun findByName(lname: String, fname: String): Employee

    @Query("SELECT * FROM employee" +
           " WHERE uid = :uid")
    fun findById(uid: Int): Employee

    @Insert
    fun insert(vararg employees: Employee): LongArray

    @Update
    fun update(vararg employees: Employee)

    @Delete
    fun delete(vararg employees: Employee)
}
```

可以看到，我们在这里使用了接口，这是因为完整的访问逻辑完全由方法签名和注解定义。此外，对于插入、更新和删除操作，方法签名就是 Room 所需的一切——它只需查看签名就能向数据库发送正确的命令。对于各种查询方法，我们使用 `@Query` 注解来提供正确的数据库命令。可以看出，Room 足够智能，能够判断我们是想要返回对象列表还是单个对象。我们还可以通过使用 `:name` 标识符将方法参数传递到伪 SQL 中。

`@Insert` 注解允许添加属性 `onConflict = "<strategy>"`，用于指定当违反唯一键或主键约束时该怎么做。`<strategy>` 的可能值由常量给出：

- `OnConflictStrategy.ABORT`：中止事务
- `OnConflictStrategy.FAIL`：使事务失败
- `OnConflictStrategy.IGNORE`：忽略冲突
- `OnConflictStrategy.REPLACE`：直接替换实体并继续事务
- `OnConflictStrategy.ROLLBACK`：回滚事务

先前使用的示例实体中的其他 DAO 看起来将非常相似——`PersonDao` 可能会执行外连接来组合 `Employee` 和 `Contact` 实体：

```kotlin
@Dao
interface ContactDao {
    @Insert
    fun insert(vararg contacts: Contact)

    @Query("SELECT * FROM Contact WHERE uid = :uId")
    fun findById(uId: Int): List<Contact>

    @Query("SELECT * FROM Contact WHERE" +
           " employeeId = :employeeId")
    fun loadByEmployeeId(employeeId: Int): List<Contact>
}

data class Person(
    @Embedded var name: Name?,
    var emailAddr: String?
)

@Dao
interface PersonDao {
    @Query("SELECT * FROM empl" +
           " LEFT OUTER JOIN Contact ON" +
           "     empl.uid = Contact.employeeId" +
           " WHERE empl.uid = :uId")
    fun findById(uId: Int): List<Person>
}
```

## 可观察查询

除了执行查询并返回查询发生时的实体、实体列表或实体数组外，还可以检索查询结果，并注册一个观察者，该观察者在底层数据发生变化时被调用。

在 DAO 类中实现此目标的方法如下所示：

```kotlin
@Query("SELECT * FROM employee")
fun getAllSync(): LiveData<List<Employee>>
```

基本上，您将结果包装在 `LiveData` 类中，这可以应用于所有查询。

然而，这仅在添加了相应的架构组件时才可能实现。为此，请将以下内容添加到模块的 `build.gradle` 文件中：

```groovy
implementation "androidx.lifecycle:" +
"lifecycle-livedata-ktx:2.4.1"
```

现在，这个 `LiveData` 对象允许添加如下观察者：

```kotlin
val ld: LiveData<List<Employee>> =
    employeeDao.getAllSync()
ld.observeForever { l ->
    l?.forEach { empl ->
        Log.e("LOG", empl.toString())
        // do s.th. else with the employee
    }
}
```

这在观察者回调中更新 GUI 组件时特别有用。您的生产代码应该更好地进行正确的资源管理——如果使用了 `observeForever()` 方法，则应在代码中的适当位置调用 `ld.removeObserver(...)` 来取消注册观察者。

为了更好的资源管理方案，`LiveData` 对象还允许添加绑定到生命周期对象的观察者。操作如下：

```kotlin
val ld: LiveData<List<Employee>> =
    employeeDao.getAllSync()
val lcOwn : LifecycleOwner = ...
ld.observe(lcOwn, { l ->
    l?.forEach { empl ->
        Log.e("LOG", empl.toString())
        // do s.th. else with the employee
    }
})
```

例如，`AppCompatActivity` 是一个可能的 `LifecycleOwner`，因此如果您能获取活动引用，只需编写：

```kotlin
val lcOwn : LifecycleOwner = activity
```

有关生命周期对象的更多详细信息，请参阅 `androidx.compose.runtime.LiveData` 的在线 API 文档。

另一种类似但可能更全面的向数据库代码添加可观察性的方法是使用 *RxJava*/*RxKotlin*，它是 Java/Kotlin 平台的 *ReactiveX* 实现。这里我们不介绍 *ReactiveX* 编程，但要将其纳入查询中，只需将结果包装到 RxJava 对象中。虽然不是完整的示例，但为了展示如何操作，例如您可以编写：

```kotlin
@Query("SELECT * FROM employee" +
       " WHERE uid = :uid")
fun findByIdRx(uid: Int): Flowable<Employee> {
    [...] // Wrap query results into a Flowable
}
```

这将返回一个 `Flowable`，允许*观察者*以异步方式*响应*检索到的数据库行。

为此，您必须在构建文件中包含 RxJava 支持：

```groovy
// RxJava support for Room
implementation "androidx.room:room-rxjava3:2.4.2"
```

有关 RxKotlin 的更多详细信息，请查阅关于 ReactiveX 的一般在线资源，或关于 RxKotlin（ReactiveX 的 Kotlin 语言绑定）的资料。

随着 Android Room 现在与 Kotlin 协程集成，您还可以使用一种真正的技术来将数据库访问与非抢占式并发相结合。只需在 DAO 方法中添加 `suspend` 关键字将其转换为挂起函数，或为返回列表添加 `Flow<>` 类型限定符。


```java
@Dao
interface ContactDao {
    @Insert
    suspend fun insert(vararg contacts: Contact)
    @Query("SELECT * FROM Contact WHERE uid = :uId")
    suspend fun findById(uId: Int): List<Contact>
    @Query("SELECT * FROM Contact WHERE" +
            " employeeName like = '%' || :name || '%'")
    fun loadByEmployeeNameLike(name: String):
            Flow<List<Contact>
}
```

## 数据库客户端

要将 Room 实际集成到应用中，我们需要知道如何获取数据库和 DAO。为此，我们首先通过以下方式获取数据库的引用：

```kotlin
fun fetchDb() =
    Room.databaseBuilder(
        this, MyDatabase::class.java,
        "MyDatabase.db")
        .build()
val db = fetchDb()
```

这会创建一个基于文件的数据库。字符串参数是存储数据的文件名。要改为打开基于内存的数据库（例如用于测试目的，或者因为您更看重速度而不在意应用停止时的数据丢失），请使用：

```kotlin
fun fetchDb() =
    Room.inMemoryDatabaseBuilder(
        this, MyDatabase::class.java)
        .build()
val db = fetchDb()
```

构建器允许以流畅的构建器风格进行某些配置活动。表 8-1 展示了有趣的配置选项。您只需在最终的 `.build()` 调用之前将它们链式连接即可。在早期开发阶段，您可能经常使用的一个选项是通过以下方式放宽前台操作限制：

```kotlin
fun fetchDb() =
    Room.databaseBuilder(
        this, MyDatabase::class.java,
        "MyDatabase.db")
        .allowMainThreadQueries()
        .build()
val db = fetchDb()
```

**表 8-1** Room 构建器选项

| 选项 | 描述 |
|---|---|
| `addCallback(RoomDatabase.Callback)` | 为此数据库添加一个 `RoomDatabase.Callback`。例如，您可以用它在数据库创建或打开时执行一些代码。 |
| `allowMainThreadQueries()` | 使用此选项可禁用 Room 中的非主线程限制。如果您不使用此选项并尝试在主线程中执行数据库操作，Room 将抛出异常。Room 这样做有充分的理由——与 GUI 相关的线程不应因冗长的数据库操作而被阻塞。因此，在您的代码中，不应调用此方法——它仅对避免处理异步性的实验有意义。 |
| `addMigrations(vararg Migration)` | 添加迁移计划。迁移将在接下来的“迁移数据库”部分中更详细地介绍。 |
| `fallbackToDestructiveMigration()` | 如果缺少匹配的迁移计划——即，对于从数据库*内部*的数据版本到 `@Database` 注解中指定的版本的必要升级，找不到已注册的迁移计划——Room 通常会抛出异常。如果您希望清空当前数据库并从头开始为新版本构建数据库，请使用此方法。 |
| `fallbackToDestructiveMigration(vararg Int)` | 与前面的 `fallbackToDestructiveMigration()` 相同，但限制为特定的起始版本。对于所有其他版本，如果缺少迁移计划，将抛出异常。 |

然后，一旦有了数据库对象，只需以抽象方式调用我们在数据库类中定义的任何 DAO 工厂方法——Room 会自动提供实现。因此，例如，编写：

```kotlin
val db = ...
val employeeDao = db.employeeDao()
// 使用 DAO...
```

# 事务

Room 支持 `EXCLUSIVE` 模式的事务。这意味着如果事务 A 正在进行，则不允许其他进程或线程在事务 B 中访问数据库，直到事务 A 完成。更准确地说，事务 B 必须等待直到 A 完成。

要在 Kotlin 中在一组数据库操作的事务内运行，您可以编写：

```kotlin
val db = ...
db.runInTransaction { ->
    // 执行数据库工作...
}
```

如果闭包内的代码没有抛出任何异常，则事务标记为“成功”。否则，事务将被回滚。

### 迁移数据库

为了将数据库从应用的一个版本迁移到另一个版本，您在访问数据库时添加迁移计划，如下所示：

```kotlin
val migs = arrayOf(
    object : Migration(1,2) {
        override fun migrate(db: SupportSQLiteDatabase) {
            // 1->2 迁移的代码...
            // 这已经在事务内运行，
            // 不要在此处添加您自己的事务代码！
        }
    }, object : Migration(2,3) {
        override fun migrate(db: SupportSQLiteDatabase) {
            // 2->3 迁移的代码...
            // 这已经在事务内运行，
            // 不要在此处添加您自己的事务代码！
        }
    } // 更多迁移...
)
private fun fetchDb() =
    Room.databaseBuilder(
        this, MyDatabase::class.java,
        "MyDatabase.db")
        .addMigrations(*migs)
        .build()
```

在这里使用 DAO 类显然没有意义，因为您必须管理多个 DAO 变体，每个版本一个。这就是为什么在 `migrate()` 方法内部，您需要在较低级别访问数据库，例如，通过执行与 Kotlin 对象无绑定的 SQL 语句。例如，假设您有一个 `Employee` 表，并且通过从版本 1 升级到 2 需要添加一个“salary”列，并从版本 2 升级到 3 需要添加另一个“childCount”列。在前述代码的 `migs` 数组内部，您可以编写：

```kotlin
//...
object : Migration(1,2) {
    override fun migrate(db: SupportSQLiteDatabase) {
        db.execSQL("ALTER TABLE components "+
                "ADD COLUMN salary INTEGER DEFAULT 0;")
    }
}
//...
object : Migration(2,3) {
    override fun migrate(db: SupportSQLiteDatabase) {
        db.execSQL("ALTER TABLE components "+
                "ADD COLUMN childCount INTEGER DEFAULT 0;")
    }
}
//...
object : Migration(1,3) {
    override fun migrate(db: SupportSQLiteDatabase) {
        db.execSQL("ALTER TABLE components "+
                "ADD COLUMN salary INTEGER DEFAULT 0;")
        db.execSQL("ALTER TABLE components "+
                "ADD COLUMN childCount INTEGER DEFAULT 0;")
    }
}
//...
```

如果您提供了小步骤迁移以及大步骤迁移，后者将具有优先权。这意味着如果您有迁移计划 1 → 2、2 → 3 和 1 → 3，并且系统需要迁移 1 → 3，则计划 1 → 3 将运行，而不是链 1 → 2 → 3。

## 调度

考虑到用户体验，以异步方式运行任务是一个重要问题。确保没有冗长的操作干扰前端流程，避免给用户留下应用无法流畅运行的印象，这一点至关重要。然而，编写具有重要部分在后台运行的稳定应用并不容易。原因有很多：设备可能因需求或电池电量低而关机，或者用户可能启动了另一个优先级更高的更重要应用，期望以低优先级模式临时运行后台作业。此外，Android 操作系统可能因资源短缺或超时条件等其他原因决定中断或推迟后台作业。随着 Android 8 的出现，考虑执行后台任务的巧妙方式变得更加重要，因为该版本对程序部分的后台执行施加了重要限制。

对于以异步方式运行作业，存在几种技术，它们各有优缺点：

- **Java 线程** – Java 或 Kotlin 线程（请记住两者都针对相同的 Java 虚拟机）是一种非常低级的技术，用于在后台运行任务。在 Kotlin 中，您可以使用像这样简单的构造：

```kotlin
Thread{-> do s.th.}.start()
```

来在后台线程中处理程序部分。这是一种非常基础的方法，您可以期望后台执行任务具有高性能。然而，您完全在任何 Android 操作系统组件生命周期之外运行，因此当 Android 进程的生命周期状态发生变化时，您实际上无法很好地控制长时间运行的后台线程会发生什么。

- **Java 并发类**
```


Java 和 Kotlin 允许使用`java.util.concurrency`包中的并发相关类。这是一种在后台运行任务的高级方法，具有改进的后台任务管理能力，但其缺点仍然是在任何 Android 组件生命周期控制之外运行。

* **Kotlin 协程**

Kotlin 协程引入了一种全新的软件设计模式来处理并发。函数可以转换为*挂起函数*，从而暂时将程序流交给其他挂起函数。

* **AlarmManager**

该功能最初设计用于在非常特定的时间运行任务，如果你需要在特定时刻向用户发送通知，可以使用它。它自 API 级别 1 就已存在。但从 API 级别 19（Android 4.4）开始，系统允许在某些条件下推迟闹钟。缺点是，你无法控制更一般的设备条件——当设备启动时，无论设备上发生什么，它都会自行决定触发闹钟事件。

* **SyncAdapter**

此方法是在 Android API 级别 5 中添加的，它对于同步任务特别有用。对于更一般的后台执行任务，你应该改用下文描述的*JobScheduler*。仅当你需要其提供的额外功能时，才使用`SyncAdapter`。

* **JobScheduler**

这是一个用于在 Android 操作系统上调度任务的集成库。它可以在 API 级别 21 及以上的任何设备上运行，这约占当前使用的 Android 设备的 98%。

更底层的方法将在第 11 章中讨论。本节剩余部分将介绍*JobScheduler*和*AlarmManager*。

### JobScheduler

`JobScheduler`是在 API 级别 21 及以上任何 Android 设备上调度和运行后台任务的专用方法。

> **注意**
>
> 此外，Android 8 的文档强烈建议使用`JobScheduler`来克服自 Android 8 以来强加的后台任务执行限制。

要开始使用`JobScheduler`，我们首先需要实现任务本身。为此，实现`android.app.job.JobService`类，例如：

```kotlin
class MyJob : JobService() {
    var jobThread: Thread? = null

    override fun onStartJob(params: JobParameters): Boolean {
        Log.i("LOG", "MyJob: onStartJob() : " + params.jobId)
        jobThread?.interrupt()
        jobThread = Thread {
            Log.i("LOG", "started job thread")
            // do job work...
            jobFinished(params, false)
            jobThread = null
            Log.i("LOG", "finished job thread")
        }
        jobThread.start()
        return true
    }

    override fun onStopJob(params: JobParameters): Boolean {
        Log.i("LOG", "MyJob: onStopJob()")
        jobThread?.interrupt()
        jobThread = null
        return true
    }
}
```

实现中最关键的部分是`onStartJob()`方法——在这里你需要输入任务实际应该执行的工作。请注意，我们将实际工作推入了一个线程。这一点很重要，因为`onStartJob()`在应用的主线程中运行，如果它停留时间过长，可能会阻塞其他重要工作。改为启动一个线程可以立即结束。同时我们返回`true`，表示任务在后台线程中继续工作。一旦任务完成，它必须调用`jobFinished()`；否则，系统将不知道任务已完成其工作。
重写的`onStopJob()`方法*不是*正常任务生命周期的一部分——它会在系统决定提前结束任务时被调用。我们让它返回`true`，以告诉系统，如果任务已相应配置，则允许其重新调度该任务。

要完成任务实现，我们仍然需要在`AndroidManifest.xml`中配置服务类。为此，添加：

```xml
<service
    android:name=".MyJob"
    android:permission="android.permission.BIND_JOB_SERVICE" />
```

这里配置的权限*不是*“危险”权限，因此你不必实现获取此权限的过程。但是，你必须在此处添加此权限；否则，任务将被忽略。

要实际调度由`JobScheduler`管理的任务，你首先需要获取一个`JobScheduler`对象作为系统服务。然后，你可以构建一个`JobInfo`对象，最后将其注册到`JobScheduler`：

```kotlin
val jsched = getSystemService(JobScheduler::class.java)
val JOB_ID: Int = 7766
val service = ComponentName(this, MyJob::class.java)
val builder = JobInfo.Builder(JOB_ID, service)
    .setMinimumLatency((1 * 1000).toLong())
    // wait at least 1 sec
    .setOverrideDeadline((3 * 1000).toLong())
    // maximum delay 3 secs
jsched.schedule(builder.build())
```

此示例调度任务最早在 1 秒后启动，最晚在 3 秒后启动。通过构造，它被分配了 ID `7766`——这是一个传递给任务实现中`onStartJob()`的值。该数字仅是一个示例——你可以使用任何唯一的数字作为 ID。

在构建`JobInfo`对象时，你可以设置各种任务特性，如表 8-2 所示。

**表 8-2** `JobInfo.Builder`选项

| 方法 | 描述 |
| --- | --- |
| `setMinimumLatency(minLatencyMillis: Long)` | 此任务应延迟指定的时间或更长时间。 |
| `setOverrideDeadline(maxExecutionDelayMillis: Long)` | 任务可以被延迟的最大时间。 |
| `setPeriodic(intervalMillis: Long)` | 使任务重复并设置重复间隔。实际间隔可能更长，但不会更短。 |
| `setPeriodic(intervalMillis: Long, flexMillis: Long)` | 使任务重复并设置重复间隔和灵活性窗口。因此实际间隔将在 *intervalMillis* − 0.5 · *flexMillis* 和 *intervalMillis* + 0.5 · *flexMillis* 之间。两个数字的最小可能值分别被限制为 `getMinPeriodMillis()` 和 MAX(`getMinFlexMillis()`, 0.05 * intervalMillis)。 |
| `setBackoffCriteria(initialBackoffMillis: Long, backoffPolicy: Int)` | 当你在任务实现中编写 `jobFinished(params, true)` 时，可能会发生退避。这里指定了在这种情况下会发生什么。`backoffPolicy` 的可能值由以下常量给出：<br> • `JobInfo.BACKOFF_POLICY_LINEAR`：退避以间隔 *initialBackoffMillis* · *retry* − *number* 发生。<br> • `JobInfo.BACKOFF_POLICY_EXPONENTIAL`：退避以间隔 *initialBackoffMillis* · 2^(*retry* − *number*) 发生。 |
| `setExtras(extras: PersistableBundle)` | 设置可选的附加数据。这些附加数据会被传递给任务实现中的 `onStartJob()`。 |
| `setTransientExtras(extras: Bundle)` | 仅适用于 API 级别 26 及更高版本。设置可选的未持久化附加数据。这些附加数据会被传递给任务实现中的 `onStartJob()`。 |
| `setPersisted(isPersisted: Boolean)` | 任务是否在设备重启后持久化。需要权限 `android.Manifest.permission.RECEIVE_BOOT_COMPLETED`。 |
| `setRequiredNetworkType(networkType: Int)` | 指定任务运行需要满足的附加条件。可能的参数值为：<br> • `JobInfo.NETWORK_TYPE_NONE`<br> • `JobInfo.NETWORK_TYPE_ANY`<br> • `JobInfo.NETWORK_TYPE_UNMETERED`<br> • `JobInfo.NETWORK_TYPE_NOT_ROAMING`<br> • `JobInfo.NETWORK_TYPE_METERED` |
| `setRequiresBatteryNotLow(batteryNotLow: Boolean)` | 仅适用于 API 级别 26 及更高版本。指定任务运行需要满足的附加条件，即电池电量不能低。False 表示不关心。 |
| `setRequiresCharging(requiresCharging: Boolean)` | 指定任务运行需要满足的附加条件，即设备必须已插入电源。False 表示不关心。 |
| `setRequiresDeviceIdle(requiresDeviceIdle: Boolean)` | 指定任务运行需要满足的附加条件，即设备必须处于空闲状态。False 表示不关心。 |



`setRequiresStorageNotLow(storageNotLow: Boolean)` |
仅适用于 API 级别 26 及以上。指定任务运行需要满足的额外条件，即设备内存不得处于低水平。`False` 表示重置为不关心此条件。 |

`addTriggerContentUri(uri: JobInfo.TriggerContentUri)` |
仅适用于 API 级别 24 及以上。添加一个内容 URI 以监听其变化。如果内容发生变化，任务将被执行。 |

`setTriggerContentUpdateDelay(durationMs: Long)` |
仅适用于 API 级别 24 及以上。设置从检测到内容变化到任务被调度之间的最小延迟时间（毫秒）。 |

`setTriggerContentMaxDelay(durationMs: Long)` |
仅适用于 API 级别 24 及以上。设置从首次检测到内容变化到任务被调度之间允许的最大总延迟时间（毫秒）。 |

`setClipData(clip: ClipData, grantFlags: Int)` |
仅适用于 API 级别 26 及以上。设置与此任务关联的 `ClipData`。`grantFlags` 的可能取值包括 `FLAG_GRANT_READ_URI_PERMISSION`、`FLAG_GRANT_WRITE_URI_PERMISSION` 和 `FLAG_GRANT_PREFIX_URI_PERMISSION`（均为 `Intent` 类中的常量）。 |

## AlarmManager

如果你需要在特定时间执行操作，无论相关组件是否正在运行，`AlarmManager` 就是你可以用于此类任务的系统服务。

关于 `AlarmManager` 的相关事宜，你的设备可能处于以下状态之一：

- **设备唤醒**：设备正在运行。通常这也意味着屏幕亮起，但无法保证如果屏幕关闭，设备就不再处于唤醒状态。不过，通常当屏幕关闭后，设备很快会离开唤醒状态。具体细节取决于硬件和设备的软件配置。如果设备处于唤醒状态，`AlarmManager` 可以正常工作，但唤醒状态并非 `AlarmManager` 触发事件的必要条件——请参见下文的其他状态。

- **设备锁定**：设备已锁定，用户需要解锁后才能再次操作。设备锁定*可能*会导致设备进入休眠状态；然而，锁定本身是一种安全措施，对 `AlarmManager` 的功能没有直接影响。

- **设备休眠**：屏幕关闭，设备以低功耗模式运行。由 `AlarmManager` 触发的事件能够唤醒设备，然后触发相应操作，但这需要明确指定。

- **设备关机**：`AlarmManager` 停止工作，并且只有在下一次设备开机时才会恢复工作。设备关机时，闹钟事件会丢失——这里没有重试功能。

闹钟事件可以是以下之一：

- 触发一个 `PendingIntent`。由于 `PendingIntent` 可以针对服务、Activity 或广播，因此闹钟事件可以启动一个 Activity、一个服务或发送一条广播。

- 调用一个*处理器（handler）*。这是将闹钟事件直接发送到发出闹钟的同一组件的一种方式。

为了调度闹钟，你需要首先以如下方式获取 `AlarmManager` 作为系统服务：

```kotlin
val alrm = getSystemService(AlarmManager::class.java)
```

然后，你可以通过多种方法发出闹钟，如表 8-3 所示。如果对于 API 级别 24 及更高版本，你选择使用监听器接收闹钟事件，关于如何使用相关处理器的详细信息将在第 11 章的“多线程”部分介绍。如果目标是 Intent，则所有对应的方法都有一个 `type: Int` 参数，其可能取值如下：

### 表 8-3 发出闹钟

| 方法 | 描述 |
| --- | --- |
| `set(type: Int, triggerAtMillis: Long, operation: PendingIntent): Unit` | 调度一个闹钟。根据提供的类型和时间参数触发一个 Intent。从 API 级别 19 开始，闹钟事件的传递可能不精确，以优化系统资源使用。如果你需要精确传递，请使用某个 "setExact" 方法。 |
| `set(type: Int, triggerAtMillis: Long, tag: String, listener: AlarmManager.OnAlarmListener, targetHandler: Handler): Unit` | 需要 API 级别 24 或更高。这是 `set(Int, Long, PendingIntent)` 的直接回调版本。Handler 参数可以为 `null`，表示在应用的主 looper 上调用监听器。否则，监听器的调用将在提供的处理器内部执行。 |
| `setAlarmClock(info: AlarmManager.AlarmClockInfo, operation: PendingIntent): Unit` | 需要 API 级别 21 或更高。调度一个由闹钟表示的闹钟。闹钟信息对象允许添加一个 Intent，该 Intent 能够描述触发器。系统可能会选择向用户显示此闹钟的信息。除此之外，此方法类似于 `setExact(Int, Long, PendingIntent)`，但隐含了 `RTC_WAKEUP` 触发类型。 |
| `setAndAllowWhileIdle(type: Int, triggerAtMillis: Long, operation: PendingIntent): Unit` | 需要 API 级别 23 或更高。类似于 `set(Int, Long, PendingIntent)`，但此闹钟即使系统处于低功耗空闲模式也允许执行。 |
| `setExact(type: Int, triggerAtMillis: Long, operation: PendingIntent): Unit` | 需要 API 级别 19 或更高。调度一个在指定时间精确传递的闹钟。 |
| `setExact(type: Int, triggerAtMillis: Long, tag: String, listener: AlarmManager.OnAlarmListener, targetHandler: Handler): Unit` | 需要 API 级别 24 或更高。这是 `setExact(Int, Long, PendingIntent)` 的直接回调版本。Handler 参数可以为 `null`，表示在应用的主 looper 上调用监听器。否则，监听器的调用将在提供的处理器内部执行。 |
| `setExactAndAllowWhileIdle(type: Int, triggerAtMillis: Long, operation: PendingIntent): Unit` | 需要 API 级别 23 或更高。类似于 `setExact(Int, Long, PendingIntent)`，但此闹钟即使系统处于低功耗空闲模式也允许执行。 |
| `setInexactRepeating(type: Int, triggerAtMillis: Long, intervalMillis: Long, operation: PendingIntent): Unit` | 调度一个具有不精确触发时间要求的重复闹钟，例如，每小时重复一次的闹钟，但不一定在每个整点时刻触发。 |
| `setRepeating(type: Int, triggerAtMillis: Long, intervalMillis: Long, operation: PendingIntent): Unit` | 调度一个重复闹钟。从 API 级别 19 开始，此方法等同于 `setInexactRepeating()`。 |
| `setWindow(type: Int, windowStartMillis: Long, windowLengthMillis: Long, operation: PendingIntent): Unit` | 调度一个在指定时间窗口内传递的闹钟。 |
| `setWindow(int type: Int, windowStartMillis: Long, windowLengthMillis: Long, tag: String, listener: AlarmManager.OnAlarmListener, targetHandler: Handler): Unit` | 需要 API 级别 24 或更高。这是 `setWindow(int, long, long, PendingIntent)` 的直接回调版本。Handler 参数可以为 `null`，表示在应用的主 looper 上调用监听器。否则，监听器的调用将在提供的处理器内部执行。 |

`type` 参数可以是以下常量之一：

- **`AlarmManager.RTC_WAKEUP`**：时间参数为 UTC 挂钟时间（自 1970 年 1 月 1 日 00:00:00 以来的毫秒数）。必要时将唤醒设备。
- **`AlarmManager.RTC`**：时间参数为 UTC 挂钟时间（自 1970 年 1 月 1 日 00:00:00 以来的毫秒数）。如果设备处于休眠状态，事件将被丢弃，不会触发闹钟。
- **`AlarmManager.ELAPSED_REALTIME_WAKEUP`**：时间参数为自上次启动以来经过的时间（毫秒），包括休眠时间。必要时将唤醒设备。
- **`AlarmManager.ELAPSED_REALTIME`**：时间参数为自上次启动以来经过的时间（毫秒），包括休眠时间。不保证唤醒设备。



