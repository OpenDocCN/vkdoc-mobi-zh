# 移植规则引擎的步骤

本章将介绍如何在 Android 平台上移植主要的规则引擎。

#### CLIPS

使用 Android-ndk 和 Eclipse Helios 将 CLIPS 移植到 Android。最好使用 Linux 机器进行移植。以下是将 CLIPS 移植到 Android 的步骤：

* 从 Source Forge 下载 `CLIPSJNI` 源代码，并从源代码构建一个 Android 库项目。
  * 将库项目导出为 `CLIPSJNI.jar`。
* 创建一个虚拟的 Android 项目，并在项目目录下创建一个 JNI 目录。
* 将 CLIPS 中所有源文件（*.c）和头文件（*.h）复制到 JNI 目录。
* 在 `Android.mk` 中添加除 `main.c` 之外的所有源文件。你的 `Android.mk` 应该如下所示：

```
LOCAL_PATH := $(call my-dir)
include $(CLEAR_VARS)
LOCAL_MODULE    := CLIPSJNI
LOCAL_SRC_FILES := agenda.c \
analysis.c \
argacces.c \
.
.
.
CLIPSJNI_Environment.c
LOCAL_LDLIBS    := -lm -llog -ljnigraphics
include $(BUILD_SHARED_LIBRARY)
```

* 在 JNI 目录中搜索 `setlocale` 函数。在期望 `setlocale` 返回一个值的地方，将其硬编码为 `C`，并注释掉所有其他的 `setlocale` 调用，因为 Android 的 `setlocale` 返回硬编码的 `0`！
* 注释掉 main 函数（只是为了安全起见）。
* 运行 `ndk-build –b`。
* 将 `libCLIPSJNI.so` 复制到 `libs/armeabi` 和 `libs/armeabi-v7a` 目录。
* 将 `CLIPSJNI.jar` 作为外部 jar 添加到你的 Android 项目中。

#### JRuleEngine

以下是将 JRuleEngine 移植到 Android 的步骤：

* 下载 `jsr172.jar`。
  * 从此 jar 文件中移除除了 `java.rmi` 之外的所有包。
* 使用 `jarjar.jar` 工具重新打包该 jar，操作如下：
  * 创建包含以下行的 `rulefile.txt` 文件：`rule java.rmi.** com.<你的公司>.java.rmi.@1`
  * 在命令提示符下，运行 `java -jar jarjar.jar process rulefile.txt <输入 jar> <输出 jar>`
* 下载 `jsr94-1.1.jar`。
* 使用 `jarjar.jar` 工具重新打包该 jar。
  * 创建一个包含以下行的 `rulefile.txt` 文件：`rule java.rmi.** com.<你的公司>.java.rmi.@1`
  * 在命令提示符下，运行：`java -jar jarjar.jar process rulesfile.txt <输入 jar> <输出 jar>`
* 下载 Apache Harmony 的 `awt.jar`，并从该 jar 中移除所有 `java.*` 包。
* 下载内含源代码的 `jruleengine.jar`。
* 注释所有包含 `Component.getName()` 函数调用的 `else if` 语句；同时移除 `import java.awt.Component;` 语句。
* 使用 `jarjar.jar` 工具重新打包 `jruleengine.jar`。
* 创建包含以下规则的 `rulefile.txt` 文件：
  * `rule java.rmi.** com.<你的公司>.java.rmi.@1 rule java.awt.Component** org.apache.harmony.awt.ComponentInternals@1`
  * 运行 `java -jar jarjar.jar process rulefile.txt <输入 jar> <输出 jar>`
* 创建一个 Android 项目，并将所有这些 jar 添加到项目的构建路径中。
* 将包含规则的 XML 文件复制到模拟器中的 sdcard。

#### DTrules

jar 文件可以在 Android 中工作，但需要执行以下步骤才能在 Android 应用程序中使用 DTrules：

* 在 Excel 表中将规则编写为决策表。示例 Excel 表可在 `sampleprojects/<项目名>/DecisionTables` 中找到。
* 创建如下文件结构：

```
/DecisionTables/包含决策表的 excel 表.xls
/edd/包含 edd 的文件.xls
/xml/
/DTRules.xml
```

* 通过使用如下代码，将包含规则的 Excel 表转换为 XML：

```
Excel2XML.compile("根路径", "DTRules.xml", "规则集名称", "仓库路径");
```

要自动生成映射文件，可以使用类似以下的方法：

```
String [] maps = {"main" };      Excel2XML.compile(path,"DTRules.xml","","D:/XLS2XML/repository",maps);
```

* 创建一个 Android 项目。
* 将以下 jar 添加到构建路径：`java-cup-11a.jar`、`poi-3.6-20091214.jar`、`dtrules.jar`。
* 创建一个映射文件（如果未自动生成），用于将 XML 文件与数据映射到实体。
* 将所需的实体添加到初始化部分，这些实体需要在第一个决策表执行之前被推入实体栈。例如：
* 修改需要创建的每个实体的数量。例如：
* 在 sdcard 中创建如下文件结构：
  * `/sdcard/xml/映射文件.xml,edd 文件.xml,dt 文件.xml`
  * `/sdcard/repository/DTRules.xml`
  * `/sdcard/DTRules.xml`
  * `/sdcard/testfiles/测试用例.xml`
* 将生成的文件复制到 sdcard 的相应目录中。

#### Zilonis

Zilonis 的 jar 在 Android 中可以毫无问题地工作。以下是将 Zilonis 添加到你的 Android 项目的步骤：

* 创建一个 Android 项目。
* 将包含规则的 .clp 文件复制到一个文件夹中，例如模拟器中 sdcard 的 temp 文件夹。
* 将 `zilonis0.97b.jar` 和 `antlr.jar` 添加到项目的构建路径。

在为 Zilonis 编写规则文件（.clp）时，请确保以下几点：

* 在 .clp 文件中，一行只能添加一条语句，这与 CLIPS 不同。
* 行与行之间不应有空格。

#### Termware

在 Android 中移植 Termware 很简单。只需一步：

* 从属于 `TermWare.jar` 中的 `ua.gradsoft.termware` 和 `ua.gradsoft.termware.util` 的 Java 文件中，移除所有与调试存根相关的项。

#### Roolie

将 Roolie 移植到 Android 上无需任何额外工作——你只需将 jar 文件添加到 Android 项目中即可开始使用。

#### OpenRules

* 下载 `org.apache.commons.beanutils` 的源代码，重新编译，并在移除以下包后将其导出为 jar，以修复多重定义问题：

```
org.apache.commons.logging
org.apache.commons.logging.impl
```

* 然后，使用 `jarjar.jar` 工具重新打包它：
  * 创建包含以下规则的 `rulefile.txt` 文件：`rule java.beans.** com.googlecode.openbeans.@1`
  * 在命令提示符下运行以下命令进行重新打包：`java -jar jarjar-1.4.jar process rulefile.txt <输入 jar> <输出 jar>`
* 下载 `poi-3.6-20091214.jar` 用于处理 Excel 表。
* 下载 `openbeans-1.0.jar` 以使用 `com.googlecode.openbeans`，因为 OpenRules 广泛使用了 Java beans，而 Android 不支持此功能。
* 下载 `commons-lang-2.3.jar`，移除 `org.apache.commons.lang.enum` 包，然后重新编译。
* 创建一个 Android 项目，并将所有这些 jar 添加到项目的构建路径中。
* OpenRules 似乎硬编码了 `openrules.config dir` 的路径，模板文件需要存储在该目录下。在 sdcard 下创建一个 `openrules.config` 目录，并将规则文件和模板文件放在那里。



#### JxBRE

以下是将 JXBRE 移植到 Android 需要遵循的步骤：

- 下载 Xerces 1.4.4（XML 解析器）的源代码。
- 将包名`javax`改为其他名称。
- 重新编译源代码并构建项目。
- 将其导出为 jar 文件`xerces.jar`。
- 下载`jxbre.jar`和`ideaityUtil.jar`。
- 创建一个 Android 项目，并将所有这些 jar 添加到项目的构建路径中。
- 下载 XML Schema 文件`businessRules.xsd`。
- 将规则文件（`.xml`）和`businessRules.xsd`复制到模拟器中，以便在项目中访问它们。

#### JEOPS

JEOPS 可以按如下方式移植到 Android：

- 创建一个新的 Java 项目。
- 将 JavaBean 文件（声明所使用的变量及其访问器方法）添加到项目中。编译它，并从`bin`目录复制生成的`.class`文件。
- 创建一个新目录，并根据`.class`文件中指定的包名，将刚刚生成的`.class`文件粘贴到该目录下对应的文件夹中。
- 同时，将规则库文件（`.rules`）复制到此目录。
- 下载`JEOPS.jar`并将其放入该目录。
- 在命令提示符中，进入此新目录的路径位置，并执行以下命令以从规则库文件生成 Java 文件：

```bash
java –cp JEOPS.jar;. jeops.compiler.Main 
```

- 创建一个 Android 项目，并将生成的规则库 Java 文件添加到项目中。
- 使用 JavaBean 的`.java`文件创建一个新的 jar（通过编译并导出为 jar），并将其添加到 Android 项目的构建路径中。
- 在规则库 Java 文件的代码中添加以下行以访问`tell()`：

```java
_knowledgeBase.tell(f1);
_knowledgeBase.tell(f2);
private jeops.AbstractKnowledgeBase _knowledgeBase;
_knowledgeBase = knowledgeBase;
```

- 同时将`JEOPS.jar`添加到项目的构建路径中。

### 示例代码片段

此代码片段将帮助您理解如何将规则引擎与 Android 应用程序集成。



#### CLIPS

让我们使用 CLIPS 规则引擎构建一个智能应用，用于评估患者的腹泻症状。

图 2-1 展示了该应用的外观。

![A459602_1_En_2_Fig1_HTML.jpg](img/A459602_1_En_2_Fig1_HTML.jpg)

**图 2-1**  
应用外观

腹泻指南可以轻松地用 CLIPS 编码为`diarrhoea.clp`。

```
;=======================================
;;
;; ---- 腹泻管理------
;;
;=======================================
;=======================================
;--- 打印响应代码---
;; 如果确认了治疗方案(Rx)，则打印该方案。
;======================================
(defrule print_diarrhea_message2
(Rx_diarrhea_signs_code(code ?b))
=>
(bind ?*WHODecisionCode* (create$ ?*WHODecisionCode* ?b))
;(printout t ?*WHODecisionCode* crlf)
)
;; 评估患者状态并触发腹泻管理的规则
;=======================================
;--- 检查患者是否患有腹泻 ---
;; 如果患者数据有 (diarrhea yes) 则 (检查大便带血) 并 (分类脱水程度)
;; 如果患者数据有 (diarrhea no) 则 (检查其他疾病)
;======================================
(defrule check-diarrhea_yes
(diarrhea_data (diarrhea yes))  ;; 检查患者是否患有腹泻
=>
(assert (check blood_in_stool))         ;; 触发检查大便带血
(assert (classify dehydration)))        ;; 触发分类脱水程度
;;-------------------------------------------------------------
;;-------------------------------------------------------------
(defrule check-diarrhea_no
(diarrhea_data (diarrhea no))
=>
(assert (check other disease)) )
;=======================================
;--- 规则：检查大便带血 ---
;; 如果患者 (大便带血) 则患者状态为 (痢疾)。
;======================================
(defrule check-blood_in_stool-yes
(check blood_in_stool)
;; 检查该动作是否由腹泻触发
(diarrhea_data (blood_in_stool yes))
;; 检查患者是否大便带血
=>
(assert (Rx_diarrhea_signs_code (code "10"))))        ;; 确认决策代码：1 表示痢疾
;=======================================
;--- 规则：检查大便带血 ---
;; 如果患者 (大便不带血) 则患者状态为 (非痢疾)。
;======================================
(defrule check-blood_in_stool-no
(check blood_in_stool)                        ;; 检查该动作是否由腹泻规则系统触发
(diarrhea_data (blood_in_stool no))           ;; 检查患者是否大便不带血
=>)                        ;; 结束痢疾评估
;=======================================
;--- 评估治疗目标：重度脱水 ---
;======================================
(defrule classify-severe_dehydration
(classify dehydration)                        ;; 检查该动作是否由腹泻规则系统触发
(diarrhea_data(blood_in_stool no))
(diarrhea_data(severe_condition_count ?w&:(>= ?w 2)))
;; 如果满足以下至少两项 中度脱水 体征
(diarrhea_data(how_many_days ?x&:(
(assert (Rx_diarrhea_signs_code (code "5")))
;(assert (check-more_than_14days-severe_dehydration_case))
)
(defrule classify-severe_dehydration_child_able_to_drink
(Rx_diarrhea_signs_code (code "5"))
(diarrhea_data (not_able_to_drink_or_drinking_poorly no))
=>
(assert (Rx_diarrhea_signs_code (code "5A")))
)
(defrule classify-severe_dehydration_child_not_able_to_drink
(Rx_diarrhea_signs_code (code "5"))
(diarrhea_data (not_able_to_drink_or_drinking_poorly yes))
=>
(assert (Rx_diarrhea_signs_code (code "5B")))
)
;=======================================
;---  目标：中度脱水 ---
;======================================
(defrule classify-some_dehydration
(classify dehydration)                ;; 检查该动作是否由腹泻规则系统触发
(diarrhea_data (blood_in_stool no))
(diarrhea_data(some_condition_count ?w&:(>= ?w 2)))
(diarrhea_data (severe_condition_count ?y&:(
(assert (Rx_diarrhea_signs_code (code "6")))
;(assert (check-more_than_14days-some_dehydration_case))
)
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
(defrule classify-no_dehydration
(classify dehydration)                ;; 检查该动作是否由腹泻规则系统触发
(diarrhea_data (blood_in_stool no))
(diarrhea_data (how_many_days ?x&:(
(assert (Rx_diarrhea_signs_code (code "7")))
)
;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;;
;; 根据患者状态确定治疗目标的规则
;=======================================
;--- 评估迁延性腹泻的规则 ---
;--- 检查是否超过 14 天 ---
;======================================
;=======================================
;--- 检查是否超过 14 天的规则 - 患者被分类为重度或中度脱水 + 腹泻 >14 天
; 则为重度迁延性腹泻
;======================================
(defrule check-severe_persistent_diarrhea-severe_deh_case
;(check-more_than_14days-severe_dehydration_case)        ;; 检查是否触发了迁延性腹泻的评估
(or (diarrhea_data (some_condition_count ?w&:(>= ?w 2)))
(diarrhea_data (severe_condition_count ?y&:(>= ?y 2))))
(diarrhea_data (how_many_days ?x&:(> ?x 14)))
(diarrhea_data (blood_in_stool no))
;; 如果腹泻持续 14 天或以上
=>
(assert (Rx_diarrhea_signs_code(code "8"))) )        ;; 确认治疗目标：重度迁延性腹泻
;=======================================
;--- 检查是否超过 14 天的规则 - 患者未被分类为脱水，但腹泻 >14 天则为迁延性腹泻
;======================================
(defrule check-persistent_diarrhea-no_deh_case
;(check-more_than_14days-no_dehydration_case)        ;; 检查是否触发了迁延性腹泻的评估
(diarrhea_data (some_condition_count ?y&:( ?x 14)))
(diarrhea_data (blood_in_stool no))
;; 如果腹泻持续 14 天或以上
=>
(assert (Rx_diarrhea_signs_code (code "9"))) )       ;; 确认治疗目标：迁延性腹泻
```

用户输入可以建模为`diarrheaData`：

```java
public class diarrheaData  implements Parcelable {
int iNumberofDays=0;
String sBlood_In_Stool="no";
String sLethargic_Unconscious="no";
String sRestless_Irritable="no";
String sSunken_Eyes="no";
String sSkin_Pinch_Veryslow="no";
String sSkin_Pinch_Slow="no";
String sNot_Able_To_Drink_or_Drinking_Poorly="no";
String sDrinking_Eagerly_or_Thirsty="no";
String sOther_Severe_Disease="no";
String sTrained_nurse_for_iv_immediately="no";
String sIv_available_in_30min="no";
String sTrained_nurse_for_ng_tube_immediately="no";
}
static {
try {
System.loadLibrary("CLIPSJNI");
if(clips == null) {
clips = new Environment();
}
}
catch(UnsatisfiedLinkError ule) {
Log.e("JNI", "Could not load libCLIPSJNI.so!");
}
private  static Environment clips = null;
static {
try {
if(clips == null) {
clips = new Environment();
}
clips.clear();
clips.load("diarrhoea.clp");
String myassertString = "(diarrhea_data (diarrhea yes) " +
"(blood_in_stool "+mydiarrheaData.sBlood_In_Stool +") " +
"(how_many_days "+mydiarrheaData.iNumberofDays+") " +
"(lethargic_unconscious "+mydiarrheaData.sLethargic_Unconscious+") "+
"(restless_irritable "+mydiarrheaData.sRestless_Irritable+") " +
"(sunken_eyes "+ mydiarrheaData.sSunken_Eyes+") " +
"(skin_pinch_veryslow "+mydiarrheaData.sSkin_Pinch_Veryslow+") " +
"(skin_pinch_slow "+mydiarrheaData.sSkin_Pinch_Slow+") " +
"(not_able_to_drink_or_drinking_poorly "+mydiarrheaData.sNot_Able_To_Drink_or_Drinking_Poorly+") " +
"(drinking_eagerly_or_thirsty "+mydiarrheaData.sDrinking_Eagerly_or_Thirsty+") " +
"(other_severe_Vdisease " + otherSevereDisease + "))";
clips.assertString(myassertString);
clips.run();
MultifieldValue mv = (MultifieldValue) clips.eval("?*WHODecisionCode*");
String WHODecision;
List theList = mv.listValue();
for(Iterator itr = theList.iterator(); itr.hasNext();)
{
StringValue myValue = (StringValue) itr.next();
WHODecision = WHODecision + myValue.toString() + " ";
}
```

决策结果将存储于`WHODecision`变量中。

如果读者能理解上述 CLIPS 示例，那么后续的示例将很容易理解。



#### JRuleEngine

```
Class.forName( "org.jruleengine.RuleServiceProviderImpl" );
String path    = Environment.getExternalStorageDirectory().getAbsolutePath()+"/temp/example3.xml";
InputStream inStream = new FileInputStream( new File(path) );
// 从提供者管理器中获取规则服务提供者。
RuleServiceProvider serviceProvider = RuleServiceProviderManager.getRuleServiceProvider( "org.jruleengine" );
// 获取规则管理器
RuleAdministrator ruleAdministrator = serviceProvider.getRuleAdministrator();
System.out.println("\n 管理 API\n");
System.out.println( "已获取 RuleAdministrator: " + ruleAdministrator );
// 获取一个指向测试用 XML 规则集的输入流
// 该规则执行集是 TCK 的一部分。
//  InputStream inStream = new FileInputStream( "example3.xml" );
System.out.println("已获取指向 example3.xml 的 InputStream: " + inStream );
// 从 XML 文档解析规则集
RuleExecutionSet res1 = ruleAdministrator.getLocalRuleExecutionSetProvider(
null ).createRuleExecutionSet( inStream, null );
inStream.close();
System.out.println( "已加载 RuleExecutionSet: " + res1);
// 注册 RuleExecutionSet
String uri = res1.getName();
ruleAdministrator.registerRuleExecutionSet(uri, res1, null );
System.out.println( "已将 RuleExecutionSet 绑定到 URI: " + uri);
// 获取 RuleRuntime 并调用规则引擎。
System.out.println( "\n 运行时 API\n" );
RuleRuntime ruleRuntime = serviceProvider.getRuleRuntime();
System.out.println( "已获取 RuleRuntime: " + ruleRuntime );
// 创建一个 StatefulRuleSession
StatefulRuleSession statefulRuleSession =
(StatefulRuleSession) ruleRuntime.createRuleSession( uri,
new HashMap(),
RuleRuntime.STATEFUL_SESSION_TYPE );
System.out.println( "已获取有状态规则会话: " + statefulRuleSession );
// 添加一些子句...
ArrayList input = new ArrayList();
input.add(new Clause("苏格拉底是人"));
// 向 statefulRuleSession 添加一个对象
statefulRuleSession.addObjects( input );
System.out.println( "已对有状态规则会话调用 addObject: " + statefulRuleSession );
statefulRuleSession.executeRules();
System.out.println( "已调用 executeRules" );
// 从 statefulRuleSession 中提取对象
List results = statefulRuleSession.getObjects();
System.out.println( "调用 getObjects 的结果: " +
results.size() + " 条结果。" );
// 遍历结果
Iterator itr = results.iterator();
while(itr.hasNext()) {
Object obj = itr.next();
System.out.println("找到子句: "+obj.toString());
}
// 释放 statefulRuleSession
statefulRuleSession.release();
System.out.println( "已释放有状态规则会话。" );
```

#### DTrules

```
String path = Environment.getExternalStorageDirectory().getAbsolutePath()+"/";
String decisionTable = "Compute_Eligibility";
//String decisionTable = "Calculate Individual Income";
RulesDirectory rd = new RulesDirectory(
path,
"DTRules.xml");
RuleSet rs = rd.getRuleSet("KidAid");
IRSession session;
try {
Excel2XML.compile(path,"DTRules.xml","KidAid","sdcard");
session = rs.newSession();
Mapping mapping = session.getMapping();
mapping.loadData(session, path+"testfiles/"+"TestCase_001.xml");
session.execute(decisionTable);
printReport(session, System.out);
} catch (RulesException e) {
// TODO 自动生成的 catch 块
e.printStackTrace();
} catch (Exception e) {
// TODO 自动生成的 catch 块
e.printStackTrace();
}
}
```

#### Zilonis

```
String fileName = "你的规则文件";
FileInputStream fstream = new FileInputStream(fileName);
int lineCount = getLines(fileName);
System.out.println("行数为 "+ lineCount);
DataInputStream dis = new DataInputStream(fstream);
BufferedReader br = new BufferedReader(new InputStreamReader(dis));
ZilonisLexer lexer = new ZilonisLexer(dis);
ZilonisParser parser = new ZilonisParser(lexer);
GenericEventHandler geh = new GenericEventHandler(rete);
parser.setEventHandler(geh);
try {
while(lineCount-- > 0) {
parser.statement();
}
} catch (RecognitionException e) {
// TODO 自动生成的 catch 块
e.printStackTrace();
} catch (TokenStreamException e) {
// TODO 自动生成的 catch 块
e.printStackTrace();
}
} catch (FileNotFoundException e3) {
// TODO 自动生成的 catch 块
e3.printStackTrace();
} catch (IOException e) {
// TODO 自动生成的 catch 块
e3.printStackTrace();
}
```

#### Termware

```
String[] args = {"iReduce"};
TermWare.getInstance().init(args);
ITermRewritingStrategy strategy=new FirstTopStrategy();
IFacts facts=new DefaultFacts();
TermSystem termSystem=new TermSystem(strategy,facts,TermWare.getInstance());
termSystem.addRule("x->y");
termSystem.addRule("y->z");
Term inputTerm=TermWare.getInstance().getTermFactory().createAtom("x");
Term outputTerm=termSystem.reduce(inputTerm);
if(outputTerm.getName().equals("z")){
Log.d("iReduce Termware","成功");
}
}}catch(TermWareException ex){
Log.e("iReduce Termware", "错误:"+ex.getMessage());
ex.printStackTrace();
}}
```

#### Roolie

```
public class AbdominalRuleArgs extends RuleArgs{
public enum ArgField
{
用户
, 右下腹
, 左下腹
, 疼痛 _ 恶心
, 便血
, 尿血
};
public String getUser()
{
return getString(ArgField.用户);
}
public void setUser(String user)
{
setString(ArgField.用户, user);
}
}
public abstract class AbdominalRule implements Rule{
@Override
public boolean passes(RuleArgs ruleArgs) {
// TODO 自动生成的方法存根
if (false == (ruleArgs instanceof AbdominalRuleArgs))
{
Log.msg("ruleArgs 必须是 AbdominalRuleArgs 的实例");
return false;
}
// 将 RuleArgs 强制转换为 AbdominalRuleArgs 并进行验证
AbdominalRuleArgs abArgs = (AbdominalRuleArgs) ruleArgs;
// 必须设置所有参数
if (false == abArgs.isright_lower_abdomenSet()
|| false == abArgs.isleft_lower_abdomenSet()
|| false == abArgs.isUserSet()
|| false == abArgs.ispain_nauseaSet()
|| false == abArgs.isblood_in_stoolSet()
|| false == abArgs.isblood_in_urineSet()
)
{
Log.msg("AbdominalRuleArgs 中并非所有参数都已设置.");
return false;
}
// 如果所有参数都存在，则让子类进行评估
return passes(abArgs);
}
public void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
// 将配置文件作为 InputStream 获取
InputStream is =
Main.class.getClassLoader().getResourceAsStream(
"roolie/abdominal/roolie-config.xml");
RulesEngine rules = new RulesEngine(is);
// 创建一些规则参数（即“事实”）以针对某些用户进行测试
List abdominalRuleArgsList = createRuleArgsToTest();
// 检查每个创建的 BankingRuleArgs 的规则是否通过。
for (AbdominalRuleArgs ruleArgs : abdominalRuleArgsList)
{
msg("\n* 评估 " + ruleArgs.getUser()+" 的健康状况：\n");
boolean isUltrasound =rules.passesRule("超声波", ruleArgs);
boolean isCTScan =rules.passesRule("CT 扫描", ruleArgs);
boolean isStoolTest1 =rules.passesRule("粪便检测 1", ruleArgs);
boolean isStoolTest2 =rules.passesRule("粪便检测 2", ruleArgs);
boolean isStoolTest3 =rules.passesRule("粪便检测 3", ruleArgs);
boolean isNothing =rules.passesRule("无需检测", ruleArgs);
}
}
```



#### OpenRules

```java
UserInput userInput = new UserInput("no", "yes", "yes", "no", "yes");
Response response = new Response();
String fileName = "file:sdcard/openrules.config/DecisionOneOrTwo.xls";
Decision decision = new Decision("DecisionAbdominalPain", fileName);
decision.put("userInput", userInput);
decision.put("response", response);
decision.execute();

public class UserInput {
    String right_lower_abdomen;
    String left_lower_abdomen;
    String pain_nausea;
    String blood_in_stool;
    String blood_in_urine;

    public UserInput(String rla, String lla, String pn, String bis, String biu) {
        this.right_lower_abdomen = rla;
        this.left_lower_abdomen = lla;
        this.pain_nausea = pn;
        this.blood_in_stool = bis;
        this.blood_in_urine = biu;
    }

    public String getRight_lower_abdomen() {
        return right_lower_abdomen;
    }

    public void setRight_lower_abdomen(String right_lower_abdomen) {
        this.right_lower_abdomen = right_lower_abdomen;
    }
}

public class Response {
    String comment;

    public Response() {
        this.comment = "Helllllp";
    }

    public String getComment() {
        return comment;
    }

    public void setComment(String s) {
        comment = s;
    }

    String[] products;

    public String[] getProducts() {
        if (products == null)
            products = new String[0];
        return products;
    }

    public void setProducts(String[] strings) {
        products = strings;
    }

    public String toString() {
        StringBuffer buf = new StringBuffer(2500);
        buf.append("Offered Products:").append("\n");
        for (int i = 0; i < getProducts().length; ++i) {
            buf.append("\t").append(getProducts()[i]).append("\n");
        }
        if (comment != null)
            buf.append("Comment: ").append(comment).append("\n");
        return buf.toString();
    }
}
```

#### JxBRE

```java
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    bre = new BREImpl();
    args = new String[]{ "/data/data/" + packageName + "/files/abdominal.xml" };
    //args="-s D:\\android_training\\rules\\discount.xml";
    setContentView(R.layout.main);
    copyCLPFiles("abdominal.xml");
    copyCLPFiles("businessRules.xsd");
    AbdominalMainLoad(args);
    Inputs order = new Inputs();
    getTotal(order);
}

private void copyCLPFiles(String fileName) {
    try {
        File file = getFileStreamPath(fileName);
        if (file.exists()) {
            return;
        } else {
            InputStream myInput = getAssets().open(fileName);
            OutputStream myOutput = new FileOutputStream(
                "/data/data/" + getPackageName() + "/files/" + fileName);
            byte[] buffer = new byte[1024];
            int length;
            while ((length = myInput.read(buffer)) > 0) {
                myOutput.write(buffer, 0, length);
            }
            myOutput.flush();
            myOutput.close();
            myInput.close();
        }
    } catch (FileNotFoundException e) {
        e.printStackTrace();
    } catch (IOException e) {
        e.printStackTrace();
    }
}

public void AbdominalMainLoad(String[] args) {
    try {
        Document doc = loadFile(args[0]);
        ((BREImpl) bre).addListener(this);
        ((BREImpl) bre).init(doc);
    } catch (Exception e) {
        System.err.println("Could not create document");
        e.printStackTrace();
    }
}

public void getTotal(Inputs aOrder) {
    inp = aOrder;
    BRERuleContext aBRC = bre.getRuleContext();
    aBRC.setFactory(BLOOD_IN_URINE, new BRERuleFactory() {
        public Object executeRule(BRERuleContext aBrc, Map aMap, Object aStep) {
            return inp.getBlood_in_urine();
        }
    });
    aBRC.setFactory(BLOOD_IN_STOOL, new BRERuleFactory() {
        public Object executeRule(BRERuleContext aBrc, Map aMap, Object aStep) {
            return inp.getBlood_in_stool();
        }
    });
    aBRC.setFactory(RIGHT_LOWER_ABDOMEN, new BRERuleFactory() {
        public Object executeRule(BRERuleContext aBrc, Map aMap, Object aStep) {
            return inp.getRight_lower_abdomen();
        }
    });
    aBRC.setFactory(LEFT_LOWER_ABDOMEN, new BRERuleFactory() {
        public Object executeRule(BRERuleContext aBrc, Map aMap, Object aStep) {
            return inp.getLeft_lower_abdomen();
        }
    });
    aBRC.setFactory(PAIN_NAUSEA, new BRERuleFactory() {
        public Object executeRule(BRERuleContext aBrc, Map aMap, Object aStep) {
            return inp.getPain_nausea();
        }
    });
    aBRC.setFactory(RECCTEST_BLOODURINE, new BRERuleFactory() {
        public Object executeRule(BRERuleContext aBrc, Map aMap, Object aStep) {
            return DecisionString.getDecisionString(new String((String)aMap.get(TEST1)));
        }
    });
    aBRC.setFactory(RECCTEST_RIGHTLOWER, new BRERuleFactory() {
        public Object executeRule(BRERuleContext aBrc, Map aMap, Object aStep) {
            return DecisionString.getDecisionString(new String((String)aMap.get(TEST2)));
        }
    });
    aBRC.setFactory(RECCTEST_LEFTLOWER, new BRERuleFactory() {
        public Object executeRule(BRERuleContext aBrc, Map aMap, Object aStep) {
            return DecisionString.getDecisionString(new String((String)aMap.get(TEST3)));
        }
    });
    aBRC.setFactory(NOTHING, new BRERuleFactory() {
        public Object executeRule(BRERuleContext aBrc, Map aMap, Object aStep) {
            return DecisionString.getDecisionString(new String((String)aMap.get(TEST4)));
        }
    });
    bre.process("SET1");
    bre.process("SET2");
    bre.process("SET3");
    bre.process("SET4");
    if ((aBRC.getResult(RECCTEST_BLOODURINE)) != null) {
        System.out.println((String)aBRC.getResult(RECCTEST_BLOODURINE).getResult());
    }
    if ((aBRC.getResult(RECCTEST_RIGHTLOWER)) != null) {
        System.out.println((String)aBRC.getResult(RECCTEST_RIGHTLOWER).getResult());
    }
    if ((aBRC.getResult(RECCTEST_LEFTLOWER)) != null) {
        System.out.println(aBRC.getResult(RECCTEST_LEFTLOWER).getResult());
    }
    if (aBRC.getResult(NOTHING) != null) {
        System.out.println(aBRC.getResult(NOTHING).getResult());
    }
}
```

#### JEOPS

```java
for (int i = 0; i < 100; i++) {
    Fibonacci f = new Fibonacci(i);
    FibonacciBase kb = new FibonacciBase(new PriorityConflictSet());
    kb.tell(f);
    kb.run();
    System.out.println(f.getN() + " the number of the fibonacci series = " + f.getValue());
}
```

### 移植规则引擎时遇到的问题

在尝试使用以下每个规则引擎编写规则时，我们发现了一些问题。读者应该留意这些问题。

- **Jruleengine**：不支持 `OR` 运算符。
- **Zilonis**：与 CLIPS 不同，它不支持 `OR`、`defglobal` 或 `bind` 关键字。
- **DTRules**：事实必须通过 XML 文件提供，因此在需要在运行时动态提供事实的环境中运行规则，既复杂又繁琐。
- **Termware**：规则必须在代码本身中编写。
- **Roolie**：需要开发过多的规则文件，并且每条规则都必须在单独的 Java 文件中编码。
- **JxBRE**：`ElseIf` 在 XML 文件中无法工作（可以使用 `Set` 替代）。在规则文件 (`.xml`) 的逻辑部分中，只有一个 `If` 能正常工作。在 `If` 元素之前，需要存在 `{Rule, Log, Logic, While, InvokeSet, ForEach, Retract}` 中的任意一个。
- **JEOPS**：用作函数调用参数的变量，必须在规则文件本身中（通过调用相应的 setter 方法）进行初始化。
- **OpenRules**：没有说明如何编写规则的文档或自述文件。



### 其他规则引擎的移植问题

读者在移植其他规则引擎时可能会遇到以下问题：

* **Drools**：转换为 dalvik 格式时，Eclipse 内存溢出（500 MB）。增加 Eclipse 的内存未能解决该问题。读者可尝试使用高端机器（RAM > 4 GB）。
* **JLisa**：在 Android 上运行时，会抛出堆栈溢出问题。读者可联系 JLisa 支持团队寻求解决方案。
* **Take**：运行时需要 Java 编译器。读者可联系 Take 支持团队获取 Android 编译器支持。
* **Jess**：开发许可证费用约为 15,000 美元。
* **OpenRules**：
  * `poi-3.6-20091214.jar` 中的方法 `org.apache.poi.hssf.usermodel.HSSFSheet.autoSizeColumn` 使用了类 `java.awt.font.FontRenderContext`，而该类在 Android 中不可用。
  * 方法 `com.googlecode.openbeans.StandardBeanInfo.getIcon` 使用了类 `java.awt.image`，而该类在 Android 中不可用。
  * 在处理决策时，垃圾回收会运行多次，表明内存使用量巨大。
  * 未收到 OpenRules 公司关于其商业许可证类型及费用的任何回复。

## 4\. 移动平台规则引擎比较

本章包含用于移动平台开发的规则引擎的比较。

### 规则引擎总结

在所有为 Android 评估的九个规则引擎中，**CLIPS** 无疑是最出色的，因为它速度最快、免费，并且支持自己的规则编程语言。其次是 **OpenRules**，其规则可以轻松地在 Excel 表中编写；然后是 **JEOPS**，因为算法可以很容易地表示。接下来是 **Termware**，因为将规则与应用程序集成非常直接。由于其可移植性、可扩展性和低成本，CLIPS 已被政府、私营企业和大学广泛使用。CLIPS 使得将人工智能嵌入到各种计算环境中的广泛应用成为可能。

### 规则引擎比较

表 4-1 显示了规则引擎的比较。

**表 4-1** 本书中规则引擎的比较

| 规则引擎 | 许可证 | 语言 | 规则语法 | 内存 | 推理 | 多线程 | 可扩展性 | 评分（1 到 5） |
| --- | --- | --- | --- | --- | --- | --- | --- | --- |
| CLIPS | 公共领域 | C | 类似 LISP | 83 MB | Rete | 否 | 是 | 4 |
| Jruleengine | LGPL | Java | *条件-动作*模式 | .03 MB | Rete | 否 | 是 | 3 |
| DTRules | Apache 2.0 | Java | Excel 表 | .54 MB | 专有 | 是 | 否 | 4 |
| Zilionis | GPL | Java | CLIPS | .68 MB | Rete | 是 | 是 | 3 |
| Termware | 特殊 | Java | Java | .19 MB | 模式匹配 | 是 | 是 | 4 |
| Roolie | LGPL | Java | XML | .59 MB | 专有 | 否 | 是 | 3 |
| OpenRules | GPL/商业 GPL | Java | Excel 表 | 2 MB | 专有 | 是 | 否 | 4 |
| JxBRE | GPL | Java | XML | 1.44 MB | 专有 | 否 | 是 | 3 |
| JEOPS | LGPL | Java | *条件-动作*模式 | .03 MB | Rete | 否 | 是 | 4 |

## 5\. 知识应用开发中的需求与挑战

知识管理被定义为为一个系统或组织创建、共享、使用和管理信息。

在本章中，我们将讨论两个知识管理系统——**SmartAppGen** 和 **AutoQuiz** 的需求、挑战、设计与实现。

### 介绍 SmartAppGen 和 AutoQuiz

SmartAppGen 能够从表示为 XML、Excel 表、PPT 等形式的结构化知识中自动生成相应的知识应用。例如，假设一名卫生工作者需要接受为期数周的培训。在培训中，他们需要阅读数百页的知识材料。如果能够利用现有知识自动构建一个知识应用并安装到他们的智能手机上，会怎么样？培训时间可以大幅缩短；此外，卫生工作者也不必记住数百页的文档。他们也无需时不时地翻阅打印的指南来执行日常工作。因此，很明显，知识应用可以提高医疗服务的效率和准确性。

并非所有的知识都有良好的格式，以至于可以自动开发并安装到智能手机上。因此，人们仍然需要接受培训并记住培训中学到的东西。当知识（例如培训材料、演示文稿等）以文本格式提供时，AutoQuiz 就派上了用场。它可以从非结构化的知识中生成有意义的测验。在任何培训或演示结束时，可以自动生成一个测验，并要求所有参与者参加。然后，他们的分数会立即计算出来。这达到了三个目的：人们会在培训中更加警觉，培训师可以立即获得关于其培训效果的反馈，并且培训师/经理可以知道哪些人在理解上有所欠缺，并采取适当措施让他们达到标准。本章将帮助您自动化公司或机构的知识管理。

### 开发知识应用

要设计、开发和部署一个知识应用，需要执行图 5-1 中所示的步骤。

![A459602_1_En_5_Fig1_HTML.jpg](img/A459602_1_En_5_Fig1_HTML.jpg)

*图 5-1 知识应用开发*

以下是知识应用相关的核心需求与挑战：

* 将知识表示为数字化格式并将其转换为规则是一个繁琐且耗时的过程。
* 用户界面可能因用户画像而异。例如，对于精通技术的用户，用户界面可以是基于文本的；对于半文盲用户，可以是基于图标的；对于文盲用户，可以是基于语音的。
* 数据持久化和维护很繁琐，如果管理不当，可能导致应用程序中断服务。
* 同一个应用程序可能需要为多个手机平台开发，例如 Android、iOS 等。
* 常见功能在这种移动应用程序中一再被重复实现，浪费了数千小时的开发时间。
* 可能需要支持多种语言。
* 可能需要根据用户画像定制应用程序的安装包。
* 知识应用应能通过无线方式进行升级。

让我们在下一章中看看 SmartAppGen 如何自动生成知识应用。我们还将探讨知识应用开发者面临的挑战。

