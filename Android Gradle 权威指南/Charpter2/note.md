# 第二章 Groovy基础


## 1. 字符串
```groovy
// 单引号和双引号的字符串
task printStringClass << {
	def str1 = '单引号'
	def str2 = "双引号"

	println "单引号字符串类型: " + str1.getClass().name
	println "双引号字符串类型: " + str2.getClass().name
}
```

无论单引号还是双引号都是`java.lang.String` 类型, 但是单引号的字符串不能进行运算.

```
task printStringVar << {
	def name = "Jack"

	println '单引号 Name is ${name}'
	println "双引号 Name is ${name}"
}

// 单引号输出 : 单引号 Name is ${name}
// 双引号输出 : 双引号 Name is Jack
```

`${变量/表达式}` 就是前面说的计算能力.


## 2. 集合

常见的集合有List, Set, Map, Queue.

### 2.1 List
```
task printList << {
	// Groovy 可以直接定义List集合.
	def numList = [1,2,3,4,5]
	
	prinln "numList Type is : ${numList.getClass().name}"
	
	// 使用下标访问元素
	println numList[1]    // 访问第一个元素
	println numList[2]    // 访问第二个元素
	println numList[-1]   // 访问倒数第一个元素.
	println numList[-2]   // 访问倒数第二个元素.
	println numList[1..3] // 访问[1,3] 个元素

	// 使用each 迭代访问元素
	numList.each{
		println it
	}
}
```

在Groovy中可以使用 中括号直接定义一个List, 这种方式十分便利.

### 2.2 Map

```
task printMap << {
	// 定义
	def map1 = ["width":1024, 'height':768]
	println map1.getClass().name

	// 访问值
	println map1["width"]
	println map1.width
	
	// 迭代Entry实体
	map1.each{
		println "Key: ${it.key}, Value: ${it.value}"	
	} 
}
```
Grooov 更多的方法自行学习.


## 3. 方法
### 3.1 括号可以省略

```
// 在Groovy中调用符号 () 可以省略

task invokeMethod << {
	// 方法调用省略小括号.
	method1 p1, p2
	method1(p1, p2)
}

def method1(int a, int b){
	return a + b
}
```

### 3.2 return 可以省略

在Groovy中如果没有 return 则会默认将最后一行作为返回值.

```
task printMethodReturn << {
	def add1 = method2 1,2 
	def add2 = method2 3,5
	// 输出: add1: 2, add2: 5
	println "add1: ${add1} , add2: ${add2}"
}

def method2(int a, int b){
	if(a > b){
		a
	}else{
		b
	}
}
```

### 3.3 代码块可以作为函数参数
代码块, 就是一块使用大括号包裹的代码, 其实就是闭包. Groovy允许闭包作为函数的参数.

以each函数为例看看
```
// 完整写法
numList.each({println it})

// 格式化代码
numList.each({
	println it
})

// Groovy中规定, 如果闭包是最后一个参数, 那么可以将闭包放到方法的外面
numList.each(){
	println it
}

// Groovy中方法调用符号可以省略.
numList.each {
	println it
}
```
通过上面的一步步的简化, 最终得到了我们见到的each调用.


## 4 JavaBean

JavaBean 是一个非常好的概念, 现在看到的组件化, 插件化, 配置集成等都是基于JavaBean的. 在Java中为了范文JavaBean中的属性我们需要定getter和setter方法, 在Groovy中变得更加简单.

```
task helloJavaBean << {
	// 定义一个对象
	Person p = new Person()
	
	// 访问属性
	println "Name: ${p.name}"
	// 设置属性值
	p.name = "Jack"
	println "Name: ${p.name}" 
}

class Person {
	private String name
}
```
在Groovy 中并不是只有定义了成员才可以作为属性访问. 我们可以直接设置**setter/getter**方法, 也一样可以当做属性访问.

```
class Person {
	public int getAge{
		12
	}
}

task helloJavaBean << {
	// 定义对象
	Person p = new Person()
	
	// 访问属性
	println "Age: ${p.age}"
	
	// 不可以设置属性, 因为没有设置setter方法.	
}
```

## 5 闭包

闭包是Groovy的一个非常重要的特性. 可以说他是DSL的基础. 它使得代码更加灵活, 轻量, 可复用, 不用再像Java一样动不动就要用一个类了.

### 5.1 初识闭包

闭包其实就是一段代码, 下面我们以each为例子, 一步一步的实现闭包功能, 分析it的由来.

```
task helloClosure << {
	// 使用自己的闭包
	customEach{
		println it
	}
}
// 定义自己的each
def customEach(closure) {
	// 模拟一个10个元素的集合, 开始迭代
	for (int i in 1..10){
		closure(i)
	}
}
```
我们定义的方法可以正常工作, 它只有一个参数就是一个闭包, 用来接收代码块, 这个闭包可以使用函数调用器 "()" 来执行. it就是我们穿进去的参数.

### 5.2 向闭包传递参数

```
task helloClosure << {
	// 多个参数
	eachMap {k, v ->
		println "Key: ${k}, Value: ${v}"
	}
}

// 传递参数到闭包
def eachMap(closure){
	def map1 = ["name":"Jack", "Age": 12]
	map1.each{
		closure(it.key, it.value)
	}
}
```
我们传递了两个参数, 此时我们使用k, v 来定义两个参数, 使用 **->** 闭包体隔开.


### 5.3 闭包委托

Groovy的闭包有: **thisObject**, **owner** 和 **delegate**三个属性. 当你在闭包内调用方法时, 由他们来决定由哪个对象来处理, 默认情况下delegate和owner时相等的, 当时delegate可以修改, 这个功能非常强大,Gradle中的闭包很多功能都是通过这个来实现的.

```
// 定义Delegate类.
class Delegate {
    def method1() {
        // 打印该方法的处理对象
		println "Delegate this: ${this.getClass()} in Delegate"
		println "method1 in Delegate"
    }

    def test(Closure<Delegate> closure){
        closure(this)
    }
}

def method1(){
    println "Delegate this: ${this.getClass()} in root"
    println "method1 in root"
}

task helloDelegate << {
	println "Root this : ${this.getClass()}"
	
    new Delegate().test{
		// root
        println "thisObject:${thisObject.getClass()}"
		// owner和delegate相等.
		println "owner:${owner.getClass()}"
		println "delegate:${delegate.getClass()}"
		method1()
		it.method1()
    }
} 
```
输出
```
Root this : class build_mfunawdrcsxrne3r4rrwcg1j
thisObject:class build_mfunawdrcsxrne3r4rrwcg1j
owner:class build_mfunawdrcsxrne3r4rrwcg1j$_run_closure1
delegate:class build_mfunawdrcsxrne3r4rrwcg1j$_run_closure1
Delegate this: class build_mfunawdrcsxrne3r4rrwcg1j in root
method1 in root
Delegate this: class Delegate in Delegate
method1 in Delegate
```

- thisObject的优先级最高, 默认情况下优先使用thisObject的方法来处理闭包中的方法调用,有则调用.从输出中我们可以看出thisObject其实就是构建脚本的上下文, 他和脚本中的this对象是一样的.

- owner和delegate默认情况下是一样的, 同时owner的优先级要大于delegate. owner其实就是闭包对象.
- 所以闭包的处理顺序是: **thisObject** >>> **owner** >>> **delegate**

在DSL中我们一般设置delegate为当前it, 这样我们就可以在闭包中对it进行配置了.或者调用它的方法.

```
class Person {
	String pName
	int pAge

	def dumpPerson(){
		println "name is ${pName}, age is ${pAge}"
	}
}

def person(Clousure<Person> closure){
	Person p = new Person()
	closure.delegate = p
	// 设置委托优先调用
	closure.setResolveStrategy(Closure.DELEGATE_FIRST)
	// 调用
	closure(p)
}

task configClosure << {
	person {
		// 设置delegate属性
		pName = "Jack"
		pAge = 12
		// 调用delegate的方法
		dumpPerson()
	}
}

```

## 6 DSL 领域特定语言


专门关注莫一领域的语言, 可以查看Martin Fowler 的<领域特定语言> 这本书.




 




















