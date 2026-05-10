# 文本编辑行为

影响文本字段内编辑方式的属性多如牛毛。如果你在`UITextField`的文档中查找，会发现找不到任何与编辑相关的属性。这是因为这些属性都定义在`UITextInput`和`UITextInputTraits`协议中，而`UITextField`和`UITextView`都遵循了这两个协议。这些属性和选项的数量几乎令人眼花缭乱，因此我将其要点整理在表 10-3 中。

表 10-3. 重要的文本编辑属性

| 属性 | 说明 |
| --- | --- |
| `autocapitalizationType` | 控制自动大写模式：关闭，或对句子、单词、字符进行大写 |
| `autocorrectionType` | 开启或关闭自动纠正 |
| `spellCheckingType` | 开启或关闭拼写检查、建议和字典查询 |
| `keyboardType` | 选择使用的虚拟键盘（标准、URL、纯数字、电话拨号、电子邮件地址、Twitter 等） |
| `returnKeyType` | 如果键盘带有“执行”键，该属性决定其标签文字：前往、Google、加入、下一步、路线、搜索、发送、Yahoo!、完成或紧急呼叫。 |
| `secureEntry` | 用户输入时隐藏字符，防止旁人看到内容。对密码等敏感信息应开启此选项。 |

