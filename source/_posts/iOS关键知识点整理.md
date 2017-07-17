title: iOS关键知识点整理
date: 2017-07-17 10:32:44
tags:
category:
---

#### Coy/MutableCopy(深浅拷贝)
> 只有对不可变对象进行copy操作是指针复制（浅复制），其它情况都是内容复制（深复制）！

**这句话是说，只要是类似于NSArray/NSString这类不可变兑现的copy，都是指针不复制，而对带有Mutable前缀的例如NSMutableArray这类的不管是copy还是MutableCopy都是深拷贝，要重新在内存开辟空间***
