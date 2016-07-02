title: UITextView心得
date: 2016-07-02 23:26:43
tags: iOS
category:
---

# UITextView

### 最近开发输入框这个鬼东西，输入自动调整高度，网上也有各种添加placeholder的，感觉都不太好用，自己着手去搞一下，最近很困扰我一个问题，由于要添加placeholder有时候和光标对不齐，最后用reveal查看了一下，如下
```
    // font = 0  h = 16  selectionh = 1.5
    // font = 0.5  h = 17  selectionh = 2.09668
    // font = 1  h = 18  selectionh = 2.69336 // (width = 6, height = 1.193359375)
    // font = 1.5  h = 18  selectionh = 3.29004
    // font = 2  h = 19  selectionh = 3.88672   2.386719
    // font = 3  h = 20  selectionh = 5.08008
    // font = 4  h = 21  selectionh = 6.27344
    // font = 5  h = 22  selectionh = 7.4668
    // font = 10  h = 28  selectionh = 13.4336  contentOffset = { 0, 3 } 25
    // font = 11  h = 30  selectionh = 14.627
    // font = 100  h = 136  selectionh = 120.836  contentOffset = { 0, 110.5 }  25.5
    // font = 1000  h = 1210  selectionh = 1194.86  contentOffset = { 0, 1184.5 } 25.5
    // font = 10000  h = 11950  selectionh = 11935.1  contentOffset = { 0, 11924.5 }  25.5
    // font = 100000  h = 119352  selectionh = 119337  contentOffset = { 0, 119327 }  25.5
  ```
  这是测试的一些font，就想知道到底textView是怎么计算contenSize的，一直从光标高度算起，最终没算出来，但是搞定了光标的高度  min：1.5，光标**height=text.size.height+1.5**

  光标的**point = {4, 7}**, 这是一开始没有文字输入的时候，如果设置**placeholderLabel**的坐标可以通过point来确定

## 实际上contentSize的高度是这样来的
**contentSizeHeight = round(text.size.height + 16)（四舍五入）**这样能解释为什么当font=0时候，初始化高度为16了，其实和光标没有任何关系。
