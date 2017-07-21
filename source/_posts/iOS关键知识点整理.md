title: iOS关键知识点整理
date: 2017-07-17 10:32:44
tags: iOS
category:
---

** iOS关键知识点整理 ：** <Excerpt in index | 首页摘要\>
主要是关于iOS开发关键知识点整理
<!-- more -->
<The rest of contents | 余下全文\>

#### Coy/MutableCopy(深浅拷贝)
> 只有对不可变对象进行copy操作是指针复制（浅复制），其它情况都是内容复制（深复制）！

**这句话是说，只要是类似于NSArray/NSString这类不可变兑现的copy，都是指针复制，而对带有Mutable前缀的例如NSMutableArray这类的不管是copy还是MutableCopy都是深拷贝，要重新在内存开辟空间***


#### obejct = nil 真的像你想像的一样吗

整理copy时发现一个问题，比如：
```
NSMutableArray *ones = [[NSMutableArray alloc] initWithObjects:@"1", nil];
NSMutableArray *twos = ones;
NSLog(@"one: %@, twos: %@", ones, twos);
NSLog(@"&one:%x, &tows:%x", &ones, &twos);

log
(lldb) p &ones
(NSMutableArray **) $0 = 0x00007fff580809c8
(lldb) p &twos
(NSMutableArray **) $1 = 0x00007fff580809c0
(lldb) po ones
<__NSArrayM 0x60800004a590>(
1
)

(lldb) po twos
<__NSArrayM 0x60800004a590>(
1
)

```
这里面的这个**twos = ones**是copy呢还是strong，**strong**真像我们所说的只是简单的赋值吗？看样子这种简单的不复制也会产生一个新的指针地址

我又做了一个例子来验证这一理论


```
NextViewController *next = [[NextViewController alloc] init];
UIView *view = [UIView new];
NSLog(@"view %@, &view %x", view, &view);
next.nameView = view;
[self.navigationController pushViewController:next animated:YES];

log
(lldb) po view
<UIView: 0x7fb1afd07240; frame = (0 0; 0 0); layer = <CALayer: 0x61800023a860>>

(lldb) po &view
0x00007fff5d3f2608

在NextViewCotroller.h
@interface NextViewController : UIViewController
@property (nonatomic, strong) UIView *nameView;
@end
在NextViewCotroller.viewDidLoad
- (void)viewDidLoad {
    [super viewDidLoad];    
    NSLog(@"view %@, &view %x", _nameView, &_nameView);
}

log
(lldb) po _nameView
<UIView: 0x7fb1afd07240; frame = (0 0; 0 0); layer = <CALayer: 0x61800023a860>>

(lldb) po &_nameView
0x00007fb1afd03bb8

```
对比前后两次赋值的指针的地址变了，但是所指向的内存地址没有变，这也说明strong不是简单的赋值


下来我们传一个数据进来试试
```
NextViewController *next = [[NextViewController alloc] init];
NSArray *ones = @[@"name"];
NSLog(@"ones %@, &ones %x", ones, &ones);
next.names = ones;
[self.navigationController pushViewController:next animated:YES];

log
(lldb) po ones
<__NSSingleObjectArrayI 0x6180000100a0>(
name
)
(lldb) po &ones
0x00007fff5d7aa5f8

在NextViewCotroller.h
@interface NextViewController : UIViewController
@property (nonatomic, strong) NSArray *names;
@end
在NextViewCotroller.viewDidLoad
- (void)viewDidLoad {
    [super viewDidLoad];    
    NSLog(@"view %@, &view %x", _names, &_names);
}

log
(lldb) po _names
<__NSSingleObjectArrayI 0x6180000100a0>(
name
)
(lldb) po &_names
0x00007fec1a510dd0
```
> 发现还是一样的， 下来我们把strong 改成copy试试会发生什么

```
(lldb) po ones
<__NSSingleObjectArrayI 0x618000019330>(
name
)
(lldb) po &ones
0x00007fff51e3d5f8

(lldb) po _names
<__NSSingleObjectArrayI 0x618000019330>(
name
)
(lldb) po &_names
0x00007f92c710b958
```

> 最终发现也是一样的指针创建新的，指针指向的内存地址没变
苹果的做法是：我即使把一个初始化的好的变量赋值给其他变量时，都进行了一次指针拷贝，也就是我们所谓的浅拷贝，这样防止，一个指针值成nil，这个指针原来指向的内存还有其他指针指向，这部分内存也不会马上被释放掉，保证了内存数据安全。
copy在赋值上不同于strong，我们用的最多的就是给array copy，因为copy后的数组都是不可操作的，这样保证了我给一个对象传入的数据不被修改，如果想修改就出入一个NSMutable，并把属性改成strong，这样内外都可以修改。


*对于内存的处理，有一次我是在用realm里面遇到过一个bug*

> 当初是我把数据库里面的数据全部取出来，然后放到数组里面，然后把数据库里面这些数据删掉，然而我数组里的数据也变成了Invalid类型， 这不得不说realm关联的强大，realm应该是对从数据库取出来的数据进行了标记，当被释放掉后，我自己数组里面的存的其实就是一组野指针，没有任何指向。

#### UIScrollView或者其他Offset偏移量
* 位移转化成缩放比例

```
// offset 位移偏量
// scaleMargin 从小变大、从大变小偏量（不是比例） MAX(startSize, finalSize) - finalSize或者 MAX(startSize, finalSize) - startSize
// finalSize 最终size
// startSize 最大size MAX(startSize, finalSize)
// actualOffset 实际真实运行大小

CGFloat scale = (offset * (scaleMargin/actualOffset) + finalSize)/startSize;
```

# KVO
## 坑
* 可以对同一个属性就行多次添加，在监听回掉里里面会接受到多个添加的kepath, 也就是说添加了几个回调几个；在rmove时候也是，添加了几个就remvoe几个，不管是否是同一个属性
* 如果你添加一个属性，但是你remove了两次，就会crash，**这里强调一点，如果你对一个属性添加了多次，你就要remove多次，这是不会crash的**
* 防止remove掉父类的你需要在add时候添加一个上下文context，来进行标记是否是自己添加，再在remove的时候remove掉自己的就行了

## 实现方式
* 当你观察一个对象时，一个新的类会动态被创建。这个类继承自该对象的原本的类，并重写了被观察属性的 setter 方法。自然，重写的 setter 方法会负责在调用原 setter 方法之前和之后，通知所有观察对象值的更改。最后把这个对象的 isa 指针 ( isa 指针告诉 Runtime 系统这个对象的类是什么 ) 指向这个新创建的子类，对象就神奇的变成了新创建的子类的实例。

# GCG
## dispatch_apply，循环多少次
### 应用场景
* 要处理大数据时候使用apply会比多开线程来的好
* 如果我们从服务器获取一个数组的数据，那么我们可以使用该方法从而快速的批量字典转模型。
```
NSArray *dictArray = nil;//存放从服务器返回的字典数组

    dispatch_queue_t queue = dispatch_queue_create("queue", DISPATCH_QUEUE_CONCURRENT);
    dispatch_async(queue, ^{

        dispatch_apply(dictArray.count, queue,  ^(size_t index){
            //字典转模型

        });
        dispatch_async(dispatch_get_main_queue(), ^{
            NSLog(@"主线程更新");
        });
    });
```

> 作者：lltree
转自：http://www.jianshu.com/p/3b12bb90bb15

### dispatch_barrier_async/dispatch_barrier_sync，屏障
* DISPATCH_QUEUE_CONCURRENT 队列中才起作用,在全局并发队列 dispatch_get_global_queue(DISPATCH_QUEUE_PRIORITY_DEFAULT, 0) 中无效,也就是说起不到屏障的作用

* 放到子线程里面做dispatch_barrier_sync，发现不起作用，也不会打印block里面的内容，但是用dispatch_barrier_async是可以的

```
dispatch_queue_t concurrent_queue = dispatch_queue_create("concurrent_queue", DISPATCH_QUEUE_CONCURRENT);

dispatch_async(concurrent_queue, ^(){
    NSLog(@"task-1--%@",[NSThread currentThread]);
});
dispatch_async(concurrent_queue, ^(){
    NSLog(@"task-2--%@",[NSThread currentThread]);
});
dispatch_sync(concurrent_queue, ^(){
    NSLog(@"task-3--%@",[NSThread currentThread]);

    dispatch_async(concurrent_queue, ^(){
        NSLog(@"task-5--%@",[NSThread currentThread]);
    });
    dispatch_async(concurrent_queue, ^(){
        NSLog(@"task-6--%@",[NSThread currentThread]);
    });

    dispatch_barrier_async(concurrent_queue, ^(){
        NSLog(@"dispatch_barrier_async--%@",[NSThread currentThread]);
    });

    dispatch_async(concurrent_queue, ^(){
        NSLog(@"task-4--%@",[NSThread currentThread]);
    });
});

2017-07-17 19:08:17.998 BackBlock[32724:28690837] task-3--<NSThread: 0x618000066040>{number = 1, name = main}
2017-07-17 19:08:17.998 BackBlock[32724:28691040] task-2--<NSThread: 0x61000006bbc0>{number = 4, name = (null)}
2017-07-17 19:08:17.998 BackBlock[32724:28691038] task-1--<NSThread: 0x618000068980>{number = 3, name = (null)}
2017-07-17 19:08:17.999 BackBlock[32724:28691042] task-5--<NSThread: 0x6180000690c0>{number = 5, name = (null)}
2017-07-17 19:08:17.999 BackBlock[32724:28691057] task-6--<NSThread: 0x60800006c340>{number = 6, name = (null)}
2017-07-17 19:08:17.999 BackBlock[32724:28691057] dispatch_barrier_async--<NSThread: 0x60800006c340>{number = 6, name = (null)}
2017-07-17 19:08:17.999 BackBlock[32724:28691057] task-4--<NSThread: 0x60800006c340>{number = 6, name = (null)}
```
