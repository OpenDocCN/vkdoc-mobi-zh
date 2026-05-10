# 添加标题行
设置变量 [ $Body ; $Body & 情况 ( $Body ≠ "" ; "¶" ) & $Group & "¶" ]
设置变量 [ $Last.Group ; $Group]
结束如果
