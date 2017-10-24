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

为了解决上述问题，引入了类型参数。ArrayList有一个类型参数用来指示元素类型：`ArrayList<T> files = new ArrayList<T>();`在Java SE 7及以后的版本，构造函数可以省略类型参数，即`ArrayList<T> files = new ArrayList<>();`  
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
Java不支持泛型类型的数组，但如果向参数个数可变的方法传递一个泛型类型的实例(即传递一个泛型类型对象的数组)这种规则有所放松，你只会得到一个警告，而不是错误。  
可以用`@SuppressWarnings("unchecked")`或用`@SafeVarags`标注addAll方法来抑制警告。  
