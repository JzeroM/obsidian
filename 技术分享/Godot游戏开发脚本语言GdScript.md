
## 第1节：认识 GdScript
*godot 使用 GdScript 作为脚本语言，用代码来控制游戏对象*

- 因为是 GdScript 是弱类型语言，所以比较自由，但是我们可以约定一些原则让代码更加的规范：

```
类名必须与文件名相同，且为小写
尽量继承于 Node2D 节点，Node2D 节点中的Transform是我们用的最多的节点
```
- 常用函数内部执行顺序，_init _ready _process

```
默认定义了一些事件函数，例如，
  _init()  脚本初始化的时候调用，对象的构造器，类似于Java的构造函数construct
  _ready()  开始调用一次，可用于初始化脚本
  _process(delta) 每帧调用，帧间隔不等，可用于更新游戏
```

## 第2节：变量和数据类型
- 变量是用于存储信息的"容器"。

```
var x=5;
var y=6;
var z=x+y;

就像代数那样
x=5
y=6
z=x+y

在代数中，我们使用字母（比如 x）来保存值（比如 5）。
通过上面的表达式 z=x+y，我们能够计算出 z 的值为 11。
在 godot 中，这些字母被称为变量。
```

- Export 关键字可以让变量在编辑器中编辑

```
# 导出一个数字
@export var a = 1
# 导出一个节点路径
@export var b:NodePath
# 导出一个节点路径，不同的写法
@export(NodePath) var c
# 导出一个文件路径
@export(String, FILE) var e 
# 导出一个文件路径，以txt结尾
@export(String, FILE, "*.txt") var d
# 导出一个资源文件路径
@export(Resource) var f
# 导出一个颜色
@export(Color, RGB) var g
```

- Gds 数据类型分类
    - Bool，一个字节，默认为 false
    - Int (同 C++和 Java long)，8 个字节，默认为0
    - Float (同 C++和 Java double)，8 个字节，默认为0
    - String，默认为 null，字符串可以存储一系列字符，如 "John Doe"。
    - 数组
    - 对象
    - Null，变量没有被赋值，则默认为 null

## 第3节：函数
- 函数是可以简单的理解为当它被调用时执行的可重复使用的代码块。
- 函数就是包裹在花括号中的代码块，前面使用了关键词 func，当调用该函数时，会执行函数内的代码。
- 空函数需要使用 pass 关键字

```
func sayHello():
    # 执行代码
```

- 调用带参数的函数，在调用函数时，您可以向其传递值，这些值被称为参数。

```
func sayHello(param1， param2):
    # 执行代码
```

- 带有返回值的函数，有时，我们会希望函数将值返回调用它的地方，通过使用 return 语句就可以实现。
- return方法可以指定返回的类型

```
func sayHello(param1， param2):
    # 执行代码
    return x
```

## 第4节：变量的作用域
- 局部作用域，变量在函数内声明，变量为局部作用域，只能在函数内部访问

```
# 此处不能调用 carName 变量
func myFunction():
    var carName = "Volvo";
    # 函数内可调用 carName 变量
```

- 全局变量，变量在函数外定义，即为全局变量，整个脚本文件中都可以使用

```
var carName = " Volvo";
 
# 此处可调用 carName 变量
func myFunction():
    # 函数内可调用 carName 变量
```

## 第5节：运算符
- 算术运算符

```
+	加法	        x=y+2	7	5
-	减法	        x=y-2	3	5
*	乘法	        x=y*2	10	5
/	除法	        x=y/2	2.5	5
%	取模（余数）	x=y%2	1	5
```

- 赋值运算符，赋值运算符用于给 GdScript 变量赋值

```
=	x=y	 	x=5
+=	x+=y	x=x+y	x=15
-=	x-=y	x=x-y	x=5
*=	x*=y	x=x*y	x=50
/=	x/=y	x=x/y	x=2
%=	x%=y	x=x%y	x=0
```

- 比较运算符，比较运算符在逻辑语句中使用，以测定变量或值是否相等

```
==	 等于	x==8	false
!=	 不等于	x!=8	true
>	 大于	x>8	false
<	 小于	x<8	true
>=	 大于或等于	x>=8	false
<=	 小于或等于	x<=8	true
```

- 逻辑运算符，逻辑运算符用于测定变量或值之间的逻辑。

```
&&	and	(x < 10 && y > 1) 为 true
||	or	(x==5 || y==5) 为 false
!	not	!(x==y) 为 true
```

## 第6节：条件语句
- 通常在写代码时，您总是需要为不同的决定来执行不同的动作。您可以在代码中使用条件语句来完成该任务。
    
- if 语句 - 只有当指定条件为 true 时，使用该语句来执行代码
    

```
if (condition):
    当条件为 true 时执行的代码
```

- if...else 语句 - 当条件为 true 时执行代码，当条件为 false 时执行其他代码

```
if (condition):
    当条件为 true 时执行的代码
else:
    当条件不为 true 时执行的代码
```

- if...else if....else 语句- 使用该语句来选择多个代码块之一来执行

```
if (condition1):
    当条件 1 为 true 时执行的代码
elif (condition2):
    当条件 2 为 true 时执行的代码
else:
  当条件 1 和 条件 2 都不为 true 时执行的代码
```

- match(switch) 语句 - 使用该语句来选择多个代码块之一来执行
## 第7节：循环语句
- 循环可以将代码块执行指定的次数，如果您希望一遍又一遍地运行相同的代码，并且每次的值都不同，那么使用循环是很方便的
- for
- while
- break 语句跳出循环后，会继续执行该循环之后的代码（如果有的话）
- continue 语句中断循环中的迭代，如果出现了指定的条件，然后继续循环中的下一个迭代
## 第8节：数组和字典的遍历
- 数组遍历

```
func arrayIterator():
	# range等价于for(int i = 0; i < 20; i++)
	print("数组遍历方法1：")
	for i in range(3):
		print(i)
	print("数组遍历方法2：")
	for ele in arr:
		print(ele)
	print("数组遍历方法3：")
	for index in range(arr.size()):
		print(arr[index])
```

- 字典遍历

```
func dictionaryIterator():
	print("字典遍历方法1：")
	for key in dict:
		print("key:" + key as String)
		print("value:" + dict[key] as String)
		
	print("字典遍历方法2：")
	for key in dict.keys():
		print("key:" + key as String)
		print("value:" + dict[key] as String)
		
	print("字典遍历方法3：")
	for value in dict.values():
		print("value:" + value as String)	
```

## 第9节： 静态变量和静态方法
- const变量（静态变量）

```
const ANSWER = 42
```

- 静态方法

```
static func getAnswer():
	return ANSWER
```

## 第10节：对象
- 真实生活中的对象，属性和方法
- 真实生活中，一辆汽车是一个对象。对象有它的属性，如重量和颜色等，方法有启动停止等

```
# Inner class，默认继承Object
class Animal:
	extends Object  # 如果不指定继承的类，默认基础Object
	const STATIC_FIELD = "静态变量"
	# 属性
	var height: int
	
	func _init():
		print("Animal 构造方法")
		
	func move():
		print("animal，移动")
	
	static func staticFuction():
		pass
```

## 第11节：节点的获取方式
```
# 获取当前节点
var currentNode1 = $"."
var currentNode2 = self

# 获取父节点
var parentNode1 = get_parent()
var parentNode2 = $"../"

# 获取子节点
var subNode1 = $SubNode2
var subNode2 = $"SubNode2"
var subNode3 = get_node("SubNode2")

# 根节点查找法，会返回节点树从上到下找到的第一个节点
var subNode4 = get_tree().root.find_node("SubNode2", true, false)
```

## 第12节：信号signal
- 信号是用来完成模块或功能之间通信的媒介，其实就是约定了一些方法的回调形式
    
- 设计模式上叫做观察者设计模式

```
1. 观察者和被观察者是抽象耦合的，解耦模块 
2. 建立一套统一的触发机制
```

- Godot引擎官方建议在你的游戏开发中更多的使用信号来完成模块或功能间的通信
    
- 第一种使用方法

```
# 第一种信号接受方法，通过在场景中配置信号的接收方法
func _on_Button1_pressed():
	print("hello button1")
```
- 第二种使用方法

```
# 第二种信号接受方法，通过代码控制信号的接收，更加的灵活，比较推荐方式
func _ready():
	$Button2.connect("pressed", self, "onButton2")
	
func onButton2():
	print("button2 pressed")
```

## 第13节：自定义信号
- 自定义信号

```
signal mySignal(a, b)
```
- 发送信号

```
emit_signal("mySignal", 1, 2)
```
- 解除绑定信号

```
disconnect("mySignal", 1, 2)
```