---
title: Java学习总结之泛型程序设计
date: 2017-10-22 09:09:10
tags: Java
---
### 泛型程序设计  
泛型程序设计意味着编写的代码可以被很多不同类型的对象所重用。例如ArrayList类可以聚集任何类型的对象，这是一个泛型程序设计的实例。实际上，在Java增加泛型类之前已经有一个ArrayList类，下面来研究泛型程序设计机制是如何演变的。  
### 类型参数的好处  
在Java增加泛型类之前，泛型程序设计是用继承实现的。ArrayLists类只维护一个Object引用的数组：  
```java
public class ArrayList
{
	private Object[] elementData;
    . . .
    public Object get(int i) { . . . }
    public void add(Object o) { . . . }
}
```
这种方法有两个问题，我们以一个保存文件名的files数组列表为例：  
1) 当获取一个值时必须进行强制类型转换：  
```java
ArrayList files = new ArrayList();
. . .
String filename = (String)files.get(0);
```
2) 当添加一个值时不进行类型检查，可以添加任何类的对象： 
```java
files.add(new File(". . ."));
```
此调用在编译和运行时都不会出错，但如果将get的结果强制转换为String会产生一个错误  

为了解决上述问题，引入了类型参数。ArrayList有一个类型参数用来指示元素类型：`ArrayList<T> files = new ArrayList<T>();`，**注意前后两个T必须一致，不能是子类和父类！**在Java SE 7及以后的版本，构造函数可以省略类型参数，即`ArrayList<T> files = new ArrayList<>();`  
这种方法有三个好处：  
1)get得到的值不必进行强制类型转换  
2)向数组列表中添加对象时会进行类型检查，如果不符合类型，无法通过编译，出现编译错误比类在运行时出现类的强制转换异常要好得多  
3)程序具有更好的可读性，比如`ArrayList<String> files`一看就是聚集了String对象的数组列表  
### 定义简单的泛型类  
一个泛型类就是具有一个或多个类型变量的类，下面使用一个简单的Pair类作为例子：  
```java
public class Pair<T>
{
	private T first;
    private T second;
    
    public Pair() {first = null; second = null}
    public Pair(T first,T second) {this.first = first; this.second = second}
    
    public void setFirst(T newValue) {first = newValue;}
    public void setSecond(T newValue) {second = newValue;}
 }
```
Pair类引入了一个类型变量T，用尖括号括起来，并放在类名的后面。**注意这里的T只是指定了一种类型，不代表类只有一个属性成员，而表示接下来类中出现的T都和尖括号里的类型保持一致,即类定义的类型变量指定方法返回类型以及域和局部变量的类型。**  
泛型类可以有多个类型变量，如可以定义两个域类型不同的Pair类`public class Pair<T,U> {. . .}`  
**类型变量命名规则：**E表示集合的元素类型，K和V分别表示表的关键字和值的类型，T(需要时还可以用临近的字母U和S)表示“任意类型”。  
实际使用中将类定义尖括号里的T替换为具体类型，类中的T也会进行相应的替换，所以，**泛型类可以看作普通类的工厂**  

### 泛型方法  
前面介绍了如何定义一个泛型类，下面我们来定义一个带有类型参数的简单泛型方法：  
```java
class ArrayAlg
{
	public static <T> T getMiddle(T...a)
    {
    	return a[a.length / 2];
    }
}
```
**注意：**  
1)类型变量放在修饰符(这里是public static)的后面，返回类型的前面  
2)泛型方法可以定义在普通类中，也可以定义在泛型类中  
3)当调用一个泛型方法时，在方法名前的尖括号中放入具体的类型：  
```java
String middle = ArrayAlg.<String>getMiddle("John","Q.","Public");
```
**我们注意到上面的泛型方法的参数必须是T类型，如果想要传入T类型的子类怎么办呢？我们可以把`<T>`改为`<? extends T>`，这样参数就可以接受T类型以及它的子类。类似的`<? super T>`则表示参数可以为T类型以及它的超类。**
### 类型变量的限定  
有时，类或方法需要对类型变量加以约束。下面是一个典型的例子，我们要计算数组中的最小元素：  
```java
class ArrayAlg
{
	public static <T> T min(T[] a)
    {
    	if(a == null || a.length == 0) return null;
        T smallest = a[0];
        for(int i = 1; i < a.length;i++)
        	if(smallest.compareTo(a[i]) > 0)
            	smallest = a[i];
        return smallset;
    }
}
```
我们会发现一个问题，smallest的类型为T，既可以是任何一个类的对象，如何保证T类型有compareTo方法呢？**解决方法是限制T为实现了Comparable接口的类**，即将泛型方法定义改为`public static <T extends Comparable> T min(T[] a)`  
**注意：**  
1)限定类时如果实现的是接口也用extends关键字，如上面的`T extends Comparable`  
2)只需要在尖括号里限定，之后的T不用再加限定  

一个类型变量或通配符可以有多个限定，用'&'分隔限定类型，用逗号分隔类型变量，例如：`T extends Comparable & Serializable` **可以有多个接口限定和至多一个类限定，将标记接口(空接口)放在限定列表的末尾，如果用一个类作为限定，它必须是限定列表中的第一个**  

### 类型擦除  
**对于虚拟机来说没有泛型类型，只有普通类和方法。无论何时定义一个泛型类型，都自动提供了一个相应的原始类型。在编译时泛型类型会被转化为原始类型。**原始类型就是删去类型参数后的泛型类型名。擦除类型变量，并替换为限定类型(无限定类型的变量替换为Object)。  
例如，`Pair<T>`的原始类型如下：  
```java
public class Pair
{
	private Object first;
    private Object second;
    
    public Pair() {first = null; second = null}
    public Pair(Object first,Object second) {this.first = first; this.second = second}
    
    public void setFirst(Object newValue) {first = newValue;}
    public void setSecond(Object newValue) {second = newValue;}
 }
```
因为T是一个无限定的参数变量，所以直接用Object替换
如果是T是一个限定类型的参数变量，用限定列表中的第一个类型替换，比如`T extends Comparable & Serializable`被替换为`Comparable`  

### 翻译泛型表达式  
当泛型表达式编译时，编译器会将其编译为原始类型并加入强制类型转换。比如：  
```java
Pair<Employee> buddies = . . . ;
Employee buddy = buddies.getFirst();
```
编译器会把这个方法调用翻译为两条虚拟机指令：  
* 对原始方法Pair.getFirst的调用  
* 将返回的Object类型强制转换为Employee类型  

当存入一个泛型域时编译器也会自动在字节码中插入强制类型转换。  

### 翻译泛型方法  
类型擦除会发生一些问题，比如一个类subPair继承自类`Pair<String>`：  
```java
class SubPair extends Pair<String> //继承一个泛型类
 {
     public void setFirst(String newValue){....}//重写了父类的方法
 }
```
Pair在编译的时候被类型擦除，Pair的setFirst方法变为了setFirst(Object newValue)，这样SubPair的setFirst(Stirng newValue)方法就无法覆盖父类中的setFirst(Object newValue)方法，因为参数不同，不是同一个方法。 
SubPair中会出现两个方法：  
```java
public void setFirst(String newValue){....}
public void setFirst(Object newValue){....}
```
此时如果有如下测试代码：  
```java
SubPair p = new SubPair(. . .);
Pair<String> pair = p;
p.setFirst("Hello world");
```
按理来说，这段测试代码应该不能通过编译，因为要实现多态的话，所调用的方法必须在子类中重写，但是在这里SubPair类并没有重写Pair类中的setFirst方法，只是单纯的继承而已，并且新加了一个参数不同的同名方法。
但结果可以运行，因为为了解决这种**类型擦除和多态的冲突**，编译器会在SubPair类中生成一个桥方法，上述两个方法被编译器变成了这样：  
```java
public void setFirst(String newValue){....} //子类定义的方法不变

//编译器生成的桥方法 
public void setFirst(Object newValue)
{
   setFirst((String) newValue);
   //桥内部调用的是子类中定义的setFirst(String newValue)方法
}
```
可以看出，这个桥方法实际上就是对超类中setFirst(Object newValue)的重写。这样做的原因是，当程序员在子类中写下setFirst(String newValue)方法时，本意是对超类中的同名方法进行重写，但因为超类发生了类型擦除，所以实际上并没有重写成功，因此加入了桥方法的机制来避免**类型擦除与多态发生冲突。**
详见博客：[Java中的类型擦除与桥方法](http://www.cnblogs.com/xz816111/p/7643323.html)  

### 约束与局限性  
在使用Java泛型时需要考虑一些限制。大多数限制都是由类型擦除引起的。  
#### 1.不能用基本类型实例化类型参数  
类型参数不能为基本类型。比如，没有`Pair<double>`只有`Pair<Double>`,其原因是类型擦除，因为擦除之后，Pair类含有Object类型的域，Object不能存储double值。  
#### 2.运行时类型查询只适用于原始类型  
使用instanceof查询一个对象是否属于某个泛型类会产生编译错误，比如：  
```java
if(a instanceof Pair<String>)//Error
```
同样的道理，getClass方法总是返回原始类型。例如：  
```java
Pair<String> stringPair = . . .;
Pair<Employee> employeePair = . . .;
if(stringPair.getClass() == employeePair.getClass()) //equal
```
其比较结果是true，因为两次调用getClass结果都返回Pair.class  
#### 3.不能创建参数化类型的数组  
不能实例化参数化类型的数组，例如：  
```java
Pair<String>[] table = new Pair<String> [10];//Error
```
最安全而有效的方法是使用ArrayList，即`ArrayList<Pair<String>>`  
#### 4.Varargs警告     
考虑下面的方法，它有可变长的泛型类型参数ts：  
```java
public static<T> void addAll(Collection<T> coll,T...ts){
	for(t : ts) coll.add(t);
 }
```
现在如果调用：  
```java
Collection<Pair<String>> table = . . .;
Pair<String> pair1 = . . .;
Pair<String> pair2 = . . .;
addAll(table,pair1,pair2);
```
JVM会为了可变长参数建立一个Pair<String>数组，这违反了前面的规则。  
**这里特别注意**：Java不支持泛型类型的数组，但如果向参数个数可变的方法传递一个泛型类型的实例(即传递一个泛型类型对象的数组)这种规则有所放松，你只会得到一个警告，而不是错误。可以用`@SuppressWarnings("unchecked")`或用`@SafeVarags`标注addAll方法来抑制警告。  
即如下：  
```java
@SafeVarargs
public static<T> void addAll(Collection<T> coll,T...ts)
```
现在就可以提供泛型类型来调用这个方法了。

#### 5.不能实例化类型变量  
不能使用像new T(...),new T[...]或T.class这样的表达式中的类型变量。例如，下面的Pair`<T>`构造器是非法的：   
```java
public Pair() {first = new T();second = new T();}//Error
```
类型擦除将T改变成Object，而且本意肯定不希望调用new Object()。在Java SE 8之后，最好的解决方法是让调用者提供一个构造器表达式。例如：  
```java
Pair<String> p = Pair.makePair(String::new);
```
makePair方法接受一个Supplier`<T>`，这是一个函数式接口，表示一个无参数而且返回类型为T的函数：   
```java
public static <T> Pair<T> makePair(Supplier<T> constr)
{
	return new Pair<> (constr.get(),constr.get());
}
```
比较传统的解决方法是通过反射调用Class.newInstance方法来构造泛型对象。  
遗憾的是，细节有点复杂。不能调用：  
```java
first = T.class.newInstance();//Error
```
表达式T.class是不合法的，因为它会被类型擦除为Object.class。必须像下面这样设计API以便得到一个Class对象：   
```java
public static <T> Pair<T> makePair(Class<T> c1)
{
	try {return new Pair<> (c1.newInstance(),c1.newInstance())};
    catch(Exception ex) {return null;}
}
```
这个方法可以按照下列方式调用：   
```java
Pair<String> p = Pair.makePair(String.class);
```
注意，Class类本身是泛型。例如,String.class是一个Class`<String>`的实例(事实上，它是唯一的实例)。因此,makePair方法能够推断除pair的类型。  
#### 6.不能构造泛型数组  
就像不能实例化一个泛型实例一样，也不能实例化数组。不过原因有所不同，毕竟数组会填充null值，构造时看上去是安全的。不过，数组本身也有类型，用来监控存储在虚拟机中的数组。这个类型会被擦除。例如，考虑下面的例子：   
```java
public static <T extends Comparable> T[] minmax(T[] a) {T[] mm = new T[2];}//Error
```
类型擦除会让这个方法永远构造Comparable[2]数组。  
如果数组仅仅作为一个类的私有实例域，就可以将这个数组声明为Object[],并且在屈原素时进行类型转换。例如，ArrayList可以这样实现:  
```java
public class ArrayList<E>
{
	private Object[] elements;
    . . .
    @SuppressWarnings("unchecked")
    public E get(int i) { return (E) elements[i];}//取元素时强制类型转换
    public void set(int i,E e) { elements[i] = e;}//改变元素时不必强转
}
```
实际的实现没有那么清晰：  
```java
public class ArrayList<E>
{
	private E[] elements;
    . . .
    public ArrayList() {elements = (E[]) new Object[10];}
}
```
这里的强制类型转换E[]是一个假象，而类型擦除使其无法察觉。  
由于 minmax 方法返回 T[] 数组，使得这一技术无法施展， 如果掩盖这个类型会有运行时错误结果。假设实现代码：  
```java
public static <T extends Comparable> T[] minmax(T... a)
{
Object[] mm = new Object[2];
. . .
return (T[]) mm; // compiles with warning
}
```
调用 String[] ss = ArrayAlg.minmax("Tom", "Dick", "Harry");  
编译时不会有任何警告。当 Object[] 引用赋给 Comparable[] 变量时，将会发生 ClassCastException异常。  
在这种情况下， 最好让用户提供一个数组构造器表达式：
```java
String[] ss = ArrayAlg.minmax (String[]::new，"Tom", "Dick", "Harry");
```
构造器表达式 String::new 指示一个函数，给定所需的长度，会构造一个指定长度的
String数组。  
minmax方法使用这个参数生成一个有正确类型的数组：
```java
public static <T extends Comparable〉T[] minmax(IntFunction<T[]> constr, T... a)
{
	T[] mm = constr.apply(2);
}
```
比较老式的方法是利用反射， 调用 Array.newInstance:  
```java
public static <T extends Comparable〉T[] minmaxfT... a)
{
T[] mm = (T[]) Array.newlnstance (a.getClass().getComponentType() , 2);
 . . .
}
```
ArrayList 类的 toArray 方法就没有这么幸运。它需要生成一个 T[] 数组， 但没有成分类型。因此， 有下面两种不同的形式：
```java
Object[] toArray()
T[] toArray(T[] result)
```
第二个方法接收一个数组参数。如果数组足够大,就使用这个数组。否则,用 result 的成分类型构造一个足够大的新数组。  
#### 7.泛型类的静态上下文中类型变量无效  
不能在静态域或方法中引用类型变量。例如， 下列高招将无法施展：  
```java
public class Singleton<T>
{
	private static T singlelnstance; // Error
	public static T getSinglelnstanceO // Error
	{
	if (singleinstance == null) //construct new instance of T
	return singlelnstance;
	}
}
```

#### 8.不能抛出或捕获泛型类的实例  
既不能抛出也不能捕获泛型类对象。实际上， 甚至泛型类扩展 Throwable 都是不合法的。  
例如， 以下定义就不能正常编译：  
```java
public class Problem<T> extends Exception { /* . . . */ } 
// Error can't extend Throwable
```
catch 子句中不能使用类型变量。例如， 以下方法将不能编译：
```java
public static <T extends Throwable〉void doWork(Class<T> t)
{
	try
	{
		do work
	}
	catch (T e) // Error can 't catch type variable
	{
		Logger.global.info(...)
	}
}
```
不过，在异常规范中使用类型变量是允许的。以下方法是合法的：
```java
public static <T extends Throwable> void doWork(T t) throws T // OK
{
	try
	{
		do work
	}
	catch (Throwable real Cause)
	{
		t.initCause(real Cause);
		throw t;
	}
	
}
```
#### 9.可以消除对受查异常的检查  
Java 异常处理的一个基本原则是， 必须为所有受查异常提供一个处理器。不过可以利用泛型消除这个限制。关键在于以下方法：  
```java
@SuppressWamings("unchecked")
public static <T extends Throwable> void throwAs(Throwable e) throws T
{
throw (T) e;
}
```
假设这个方法包含在类 Block 中，如果调用`Block.<RuntimeException>throwAs(t);`  
编译器就会认为 t 是一个非受查异常。 以下代码会把所有异常都转换为编译器所认为的非受查异常：  
```java
try
{
	do work
}
catch (Throwable t)
{
	Block.<RuntimeException>throwAs(t) ;
}
```
下面把这个代码包装在一个抽象类中。用户可以覆盖 body 方法来提供一个具体的动作。调用 toThread 时， 会得到 Thread 类的一个对象， 它的 run 方法不会介意受查异常。  
```java
public abstract class Block
{
	public abstract void body() throws Exception;
	public Thread toThrea()
	{
		return new Thread()
		{
		public void run()
		{
			try
			{
				body();
            }
			catch (Throwable t)
			{
            Block.<RuntimeException> throwAs(t);
            }
        }
    };
}
@SuppressWamings("unchecked")
public static <T extends Throwable> void throwAs(Throwable e) throws T
{
throw (T) e;
}

```
例如， 以下程序运行了一个线程， 它会拋出一个受查异常。  
```java
public class Test
{
	public static void main(String[] args)
	{
	new Block()
	{
		public void body() throws Exception
		{
		Scanner in = new Scanner(new File("ququx") ,"UTF-8");
		while (in.hasNext())
		System.out.println(in.next());
		}
	 } 
   .toThread() .start();
 }
}
```
运行这个程序时， 会得到一个栈轨迹， 其中包含一个FileNotFoundException ( 当然,假设你没有提供一个名为 ququx 的文件)。  
这有什么意义呢？ 正常情况下， 你必须捕获线程 run 方法中的所有受查异常， 把它们"包装"到非受查异常中， 因为 run 方法声明为不抛出任何受查异常。  
不过在这里并没有做这种"包装"。我们只是抛出异常， 并"哄骗"编译器， 让它认为这不是一个受查异常。
通过使用泛型类、 擦除和 @SuppressWarnings 注解， 就能消除 Java 类型系统的部分基本限制。  
#### 注意擦除后的冲突  
当泛型类型被擦除时，无法创建引发冲突的条件。下面是一个示例。假定像下面这样将equals 方法添加到 Pair 类中：  
```java
public class Pair<T>
{
public boolean equals(T value) { return first.equals(value) && second.equals(value); }
}
```
考虑一个Pair`<String>`。从概念上讲，它有两个equals方法： 
```java
boolean equals(String) // defined in Pair<T>
boolean equals(Object) // inherited from Object
```
但是，直觉把我们引入歧途。方法擦除`boolean equals(T)
`，就是`boolean equals(Object)`  
与 Object.equals 方法发生冲突。
当然，补救的办法是重新命名引发错误的方法。
泛型规范说明还提到另外一个原则:"要想支持擦除的转换， 就需要强行限制一个类或类型变量不能同时成为两个接口类型的子类，而这两个接口是同一接口的不同参数化。"例如，
下述代码是非法的：
```java
class Employee implements Coinparab1e<Emp1oyee> { . . . }
class Manager extends Employee implements Comparable<Hanager> 
{ . . . } // Error
```
Manager 会实现 Comparable`<Employee>` 和 Comparable`<Manager>`, 这是同一接口的不同参数化。
这一限制与类型擦除的关系并不十分明确。毕竟，下列非泛型版本是合法的。  
```java
class Employee implements Comparable { . . . }
class Manager extends Employee implements Comparable { . . . }
```
其原因非常微妙， 有可能与合成的桥方法产生冲突。实现了 Comparable`<X>` 的类可以获得一个桥方法：  
```java
public int compareTo(Object other) { return compareTo((X) other); }
```
对于不同类型的 X 不能有两个这样的方法。
### 泛型类型的继承规则














