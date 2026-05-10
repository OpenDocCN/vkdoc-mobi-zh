# 扩展公式以支持多个匹配字段

前面的示例假设用户的筛选条件是州。但是，可以扩展筛选公式以检测跨多个字段的匹配。以下示例添加了条件，用于在“名”、“姓”、“城市”或“州”中查找匹配项：

```
Company::公司联系人门户筛选 = "" or
PatternCount (
Company | Contact::联系人名 ;
Company::公司联系人门户筛选
) > 0 or
PatternCount (
Company | Contact::联系人生 ;
Company::公司联系人门户筛选
) > 0 or
PatternCount (
Company | Contact::联系地址城市 ;
Company::公司联系人门户筛选
) > 0 or
PatternCount (
Company | Contact::联系地址州 ;
Company::公司联系人门户筛选
) > 0
```

