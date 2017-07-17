title: iOS关键知识点整理
date: 2017-07-17 10:32:44
tags:
category:
---

#### Coy/MutableCopy(深浅拷贝)
> 只有对不可变对象进行copy操作是指针复制（浅复制），其它情况都是内容复制（深复制）！

**这句话是说，只要是类似于NSArray/NSString这类不可变兑现的copy，都是指针不复制，而对带有Mutable前缀的例如NSMutableArray这类的不管是copy还是MutableCopy都是深拷贝，要重新在内存开辟空间***


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
