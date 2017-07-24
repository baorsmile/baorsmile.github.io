title: Swift学习笔记
date: 2017-07-13 16:51:12
tags: iOS
category:
comments: true
---

** Swift学习笔记 ：** <Excerpt in index | 首页摘要\>
主要是关于Swift学习笔记
<!-- more -->
<The rest of contents | 余下全文\>

### [open/public]和[private/fileprivate]

#### public

1.使用public修饰的类,属性或方法,可以被任何类访问
2.但在其他的module中不可以被override和继承,而在本module可以

>module内和module外的区别 ： module内是指不需要使用import引用文件，就可以创建对象，表示module内，而module外是指需要使用import文件才能创建对象才能使用

> 这里有个很好的module例子，比如说你项目中用到了**pods**,你要继承里面一个类，这时候public和open就起到作用，但是**如果你在工程里面，通俗的说就是基本上和open没有区别**

#### open
1.可以被任何人使用
2.也可以被override和继承,这是和public的区别

#### private
只在自己的类里面可以使用，在类外不能用，**就是说,不管另外一个类是否在这个文件里，都不能访问，包括自己的extension里面**

#### fileprivate
顾名思义，只要在这个文件里面，所有的类、extension都可以访问，比如说类A和B都在同一个swift文件里面，A里面初始化B，调用B里面属性方法都可以

### String格式化
1，下面是一个浮点类型的数字转成String字符串的例子

```
var f = 123.32342342
var s:String = "\(f)" //123.32342342
```


2，如果要保留两位小数
```
var f = 123.32342342
var s = String(format: "%.2f", f) //123.32
```
3，转成十六进制格式字符串
```
let i = 255
let s:String = String(format: "%x", i) //ff
```
4，不足六位前面补0
```
let i = 255
let s:String = String(format: "%06x", i) //0000ff
```

### Array和Dictionary
##### Array
* 遍历

```
let count: Int = 10

for i in 0..< count {
    // 0 - 9
}

for i in 0...count {
    // 0 - 10
}

```

#### Dictionary
* 遍历

```
let dict: Dictionary  = ["key":"value"]

for (key, value) in dict {

}

dict.forEach { (key, value) in

}

(dict as NSDictionary).enumerateKeysAndObjects({ (key, value, stop) in

})

```

#### Keyboard
```
func willShow(_ center: Notification) {
    self.handleKeyboard(center)
}

func willHide(_ center: Notification) {
    self.handleKeyboard(center)
}

func handleKeyboard(_ center: Notification) {

    if let nInfo = (center as NSNotification).userInfo, let value = nInfo[UIKeyboardFrameEndUserInfoKey] as? NSValue {

        let frame = value.cgRectValue

        let duration: CGFloat = nInfo[UIKeyboardAnimationDurationUserInfoKey] as! CGFloat

        let keyboardMoveY: CGFloat = frame.origin.y - kMainScreen_Height()

        UIView.animate(withDuration: TimeInterval(duration), animations: {
            self.transform = CGAffineTransform(translationX: 0, y: keyboardMoveY);
        })

    }

}
```
