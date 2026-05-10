# 第 16 章 集合模式

## 通过寻址访问集合对象

可寻址对象的集合（数组和字典）也可以通过单独访问每个成员对象来进行遍历。这给了你最终的控制权，尽管其效率可能不如使用前面的某种枚举方法。

数组集合最容易以这种方式处理，并且其效率并不比使用枚举器差太多。清单 16-8 展示了一个典型示例。

**清单 16-8. 数组索引循环**

**Java**

```
ArrayList<Object> array = …

for (int i=0; i<array.size(); i++) {
    Object object = array.get(i);
    …
}
```

**Objective-C**

```
NSArray *array = …
NSUInteger i;
for (i=0; i<[array count]; i++) {
    id object = [array objectAtIndex:i];
    …
}
```

字典的值通过其键来寻址。通过键遍历字典需要结合使用枚举和集合寻址，如清单 16-9 所示。

**清单 16-9. 通过键枚举字典**

**Java**

```
HashMap<Object,Object> dictionary = …

for (Iterator<Object> i = dictionary.keySet().iterator(); i.hasNext(); ) {
    Object key = i.next();
    Object object = dictionary.get(key);
    …
}
```

**Objective-C**

```
NSDictionary *dictionary = …
NSEnumerator *e = [dictionary keyEnumerator];
id key;
while ( (key=[e nextObject])!=nil ) {
    id object = [dictionary objectForKey:key];
    …
}
```

---

[www.it-ebooks.info](http://www.it-ebooks.info/)

