---
title: Java学习总结之继承和多态
date: 2017-10-12 23:18:55
tags: Java
---
### 父类与子类  
在Java术语中，如果C1类扩展自另一个类C2，我们称C2为父类，也称超类或基类，称C1为子类，也称次类、扩展类、派生类。父类是更通用的类，子类是扩充父类得到的更特定的类。子类从它的父类中继承可访问的数据域和方法，还可以添加新的数据域和方法。从物理层面来看，为子类对象开辟的内存的前一部分存储从父类继承的成员(可访问)，后面再放置子类新扩展的成员。
### 继承  
面向对象的编程允许从已经存在的类中定义新的类，这称为继承。继承可以使得子类别具有父类别的各种属性和方法，而不需要再次编写相同的代码。在令子类别继承父类别的同时，可以重新定义某些属性，并重写某些方法，即覆盖父类别的原有属性和方法，使其获得与父类别不同的功能。另外，为子类别追加新的属性和方法也是常见的做法。继承通过extends关键字实现。  
#### 继承有如下几个关键点  
1.子类并不是父类的一个子集，实际上，一个子类通常比它的父类包含更多的信息和方法  
2.父类中的私有数据域在该类之外是不可访问的，故不能在子类中直接使用，如果父类中定义了公共访问器或修改器，可以在子类中调用它们  
3.不是所有的“是一种”(is-a)关系都该用继承来建模  
4.继承是用来为“是一种”关系(is-a)建模的，不要仅仅为了重用方法这个原因而盲目使用继承  
5.Java中类与类的继承是单一继承  
### super关键字  
super关键字有两种作用：  
**1.调用父类的构造方法**  
因为继承时父类的构造方法不会被继承，而子类的构造方法只能初始化子类新增加的数据域，所以要通过super关键字调用父类的构造方法来初始化从父类继承的数据域。  
形式是`super()`或`super(arguments)`，分别调用了父类的无参构造方法和相应的有参构造方法。super语句要写在子类构造方法的第一条，否则编译器会自动在构造方法开头插入一条`super()`    
有些人认为super与this引用在此处是类似的概念，实际上不太恰当，super不是一个对象的引用，也不能赋给另一个引用变量，他只是一个指示编译器调用超类方法的特殊关键字。  

**2.调用父类的方法**  
在继承时往往会进行重写，即覆盖父类的方法。此时如果要调用父类原有的未被覆盖的方法，就可以使用`super.方法名`来调用父类的方法。如果父类方法没有被覆盖，可以省略super关键字。只能调用一级父类的方法，不可以跨父类，`super.super.方法名`是不合法的。  

### 构造方法链  
在任何情况下，构造一个类的实例时，将会调用沿着继承链的所有父类的构造方法。当构造一个子类对象时，子类构造方法会在完成自己的任务之前，首先调用它的父类的构造方法。如果父类继承自其他类，那么父类构造方法又会在完成自己的任务之前，调用它自己的父类的构造方法。这个过程一直持续到这个继承体系结构的最后一个构造方法被调用为止。这就是构造方法链。比如下面的代码：  
```java
public class Faculty extends Employee{
	public static void main(String[] args){
       new Faculty();
    }
    public Faculty(){
    	System.out.println("(4) Performs Faculty's tasks");
    }
 }
class Employee extends Person{
	public Employee(){
		this("(2) Invoke Employee's overloaded constructor");
        System.out.println("(3) Perfoms Employee's tasks");
    }
    public Employee(String s){
	  System.out.println(s);
    }
 }
 class Person{
 	public Person() {
    	System.out.println("(1) Performs Person's tasks");
     }
 }
```  
打印结果为:  
(1) Performs Person's tasks  
(2) Invoke Employee's overloaded constructor  
(3) Performs Employee's tasks  
(4) Performs Faculty's tasks  

我们可以知道：子类的构造方法的第一条语句要么是**super语句(包括编译器隐式插入的)**,要么是**this语句**，它通过调用同一个类的另一个重载的构造方法，再调用该方法的super语句初始化父类  

注意:如果没有显式在子类构造方法中定义super语句，编译器自动插入的super语句匹配的是父类的无参构造方法，如果父类没有无参构造方法，编译器会报错。所以建议为每个类都提供一个无参构造方法，以便于日后对该类进行扩展。  
### 方法重载与重写  
**重载**  
方法重载是指在一个类中定义多个同名的方法，但要求每个方法具有不同的参数的类型或参数的个数。重载的方法方法名相同，返回值类型和访问修饰符可以不同，参数列表必须不同(包括参数个数、类型和次序，有一个不同就算不同)  

**重写**  
方法重写是指在继承时子类覆盖父类中已有的方法并提供对其的新的实现(修改方法体)。重写的方法和原方法返回值类型、方法名、参数列表均相同，可见性修饰符范围不可缩小(要么相同要么扩大)。  
为了避免错误，可以使用重写注解，在要重写的方法前加上`@Override`，该注解会强制编译器检查是否重写了某方法，如果没有重写，编译器会报告一个错误。

**关于重写的几点注意**  
1.仅当实例方法是可访问的，它才能被覆盖  
2.静态方法可以被继承，但不能被覆盖。如果子类中重新定义了父类的静态方法，父类的静态方法会被隐藏，可以使用父类名.静态方法名调用隐藏的静态方法  
3.尽管重写一般用于方法，属性其实也可以重写，即子类可以定义和父类同名的属性，子类的属性会覆盖父类的属性。

方法重写发生在通过继承而相关的不同类中，方法重载可以发生在同一类中，也可以发生在由于继承关系而相关的不同类中。

### 多态  
Java允许把子类对象的引用赋给父类引用变量，即父类型变量可以引用子类型的对象。从物理层面理解，继承自父类的子类的内存先存储父类数据域再存储子类的新增数据域，所以子类对象既可以看成子类对象本身(看整段内存)也可以看成父类对象(看内存的前一部分)。  
注意：子类引用赋给父类变量之后，只能访问父类属性和方法或在子类中重写过的父类方法，不能访问子类的特有属性和方法，除非进行向下转型。
### 动态绑定
多态机制的底层实现技术是**动态绑定**，动态绑定是指JVM在执行期间（非编译期）判断所引用对象的实际类型，根据其实际的类型调用其相应的方法。程序运行过程中，把函数（或过程）调用与响应调用所需要的代码相结合的过程称为动态绑定。

对java来说，绑定分为静态绑定和动态绑定；或者叫做前期绑定和后期绑定。  

静态绑定(前期绑定)：
在程序执行前方法已经被绑定（也就是说在编译过程中就已经知道这个方法到底是哪个类中的方法），此时由编译器或其它连接程序实现。例如：C 。针对java简单的可以理解为程序编译期的绑定；这里特别说明一点，java当中的方法只有final，static，private和构造方法是前期绑定。

动态绑定(后期绑定)：在运行时根据具体对象的类型进行绑定。
若一种语言实现了后期绑定，同时必须提供一些机制，可在运行期间判断对象的类型，并分别调用适当的方法。也就是说，编译器此时依然不知道对象的类型，但方法调用机制能自己去调查，找到正确的方法主体。不同的语言对后期绑定的实现方法是有所区别的。但我们至少可以这样认为：它们都要在对象中安插某些特殊类型的信息。

简言之，一个方法可能在沿着继承链的多个类中实现，JVM在运行时动态绑定方法的实现。
### 理解方法调用  
假设下面要调用x.f(args)，方法调用的过程为：  
1） 编译器查看对象的声明类型和方法名，可能存在多个名字为f的方法，比如f(int)和f(String)。编译器会一一列举类中所有名为f的方法和其超类中可访问的名为f的方法，至此编译器已获取所有可能被调用的候选方法。  
2） 编译器将查看调用方法时提供的参数类型，如果所有f方法中存在一个与提供的参数类型完全匹配的方法，就选择这个方法，这个过程称为重载解析，如果没有完全匹配参数的方法，允许自动向上类型转换进行匹配，仍然没有合适的方法会报告编译错误。至此，编译器已获得需要调用的方法名字和参数类型。  
3） 如果是private方法、static方法、final方法或者构造器，编译器可以准确知道应该调用那个方法(即静态绑定),因为private方法、构造器不能被继承，更谈不上重写，而static方法和final方法虽然能被继承，但不能被重写。  
4) 当程序运行，并且采用动态绑定调用方法时，JVM一定调用与x所引用对象的实际类型最合适的类的方法，即在沿着继承链从最特殊的子类开始向更通用的父类查找目标方法，直到找到对该方法的实现就停止，调用该方法。  

每次调用方法都要进行搜索，时间开销很大。因此，JVM预先为每个类创建了一个方法表，其中列出了所有方法的签名和实际调用的方法。这样调用方法时JVM只要提取对象实际类型的方法表，搜索定义该方法签名的类，再调用该方法就可以了。  
方法表示例(Manager继承自Employee)：  

Employee:	
getName() -> Employee.getName()  
getSalary() -> Employee.getSalary()  
getHireDay() -> Employee.getHireDay()  
raiseSalary(double) -> Employee.raiseSalary(double)  

Manager:  
getName() -> Employee.getName()//继承的方法  
getSalary() -> Manager.getSalary()//重写的方法  
getHireDay() -> Employee.getHireDay()//继承的方法  
raiseSalary(double) -> Employee.raiseSalary(double)//继承的方法  
setBonus(double) -> Manager.setBonus(double)//新增的方法  

### 内联优化  
带有final修饰符的类是不可派生的。在Java核心API中，有许多应用final的例子，例如java.lang.String，整个类都是 final的。为类指定final修饰符可以让类不可以被继承，为方法指定final修饰符可以让方法不可以被重写。如果指定了一个类为final，则该类所有的方法都是final的。Java编译器会寻找机会内联优化所有的final方法，内联对于提升Java运行效率作用重大，此举能够使性能平均提高50%。如果确定一个类不会被派生或一个方法不会被重写，建议使用final关键字修饰。  

### 对象类型转换  
和基本数据类型一样，对象可以自动进行向上转型(即多态)，比如Apple类继承自Fruit类，把Apple对象的引用赋给Fruit变量一定是合法的。但如果向下转型(目的是访问子类特有数据域和调用子类特有方法)，要进行强制类型转换，格式为：(子类名)对象名。此时要保证该对象的实际类型确实是要强制转换的子类型，比如fruit是一个引用了Apple对象的Fruit类型的变量，如果向下转型成Banana类是非法的，会抛出一个ClassCastException,而转成Apple类是合法的。  
我们可以通过`instanceof`运算符来检测一个对象是否是某个类或接口的实例，其返回值是boolean类型的。  
注意:  
1.对象成员访问运算符(.)优先于类型转换运算符。使用圆括号保证在点运算符(.)之前进行转换，例如：((Circle)object).getArea();  
2.转换基本类型值返回一个新的值，但转换一个对象引用不会创建一个新的对象。  
### Object类  
Object类是Java中所有类的祖先(Java的类层次是一个单根结构)，但不用显式写出`public class xxx extends Object`,在Java中只有基本数据类型不是对象，所有的数组类型，不管是对象数组还是基本类型数组都扩展了Object类。  
下面介绍Object类中的几个重要方法及重写规范：  
#### 1) equals方法  
Object类中的equals方法用于检测一个对象是否等于另外一个对象。在Object类中，这个方法将判断两个对象是否具有相同的引用，如果两个对象具有相同引用，它们一定是相等的。  

equals方法的原型是public boolean equals(Object obj),默认实现是:  
```java
public boolean equals(Object obj){
	return (this == obj);
}
```    
调用它的语法是`object1.equals(object2)`，作用和直接使用==判等相同, 但这在大多数情况下没什么意义，我们一般通过判断对象的某些数据域是否相等来判断对象是否相等，此时我们需要重写equals方法。    
比如类Employee定义了数据域：private String name,private double salary,private LocalDate hireDay  
equals方法重写如下：  
```java 
public boolean equals(Object obj){
	if(this == obj) return true;
    //快速检测引用是否相等，相等返回ture
    
    if(obj == null) return false;
    //检测引用是否为空，为空返回false  
    
    if(getClass() != obj.getClass())  
    	return false;
     //检测是否属于同一个类，不是返回false  
        
    Empolyee other = (Employee) obj;//向下转型  
    
    return name.equals(other.name)
      && salary == other.salary
      && hireDay.equals(other.hireDay);
      //逐一比较数据域,有一个不等返回就false，否则返回true
```  
**进一步改进：**  
*改进一*  
上述的第4步检测，可以改为  
```java
return Objects.equals(name,other.name)
   && salary == other.salary
   && Objects.equals(hireDay,other.hireDay);
```  
其中Objects.equals方法可以防备name 或 hireDay 可能为null的情况，如果两个参数都为null，Objects(a,b)返回true;如果其中一个为null，返回false;如果两个参数都不为null,调用a.equals(b)。Objects类在java.util包中。  

在子类中定义equals方法时，首先调用超类的equals，如果检测失败，对象就不可能相等。如果超类中的域相等，只需要比较子类中新增的数据域就可以。  
比如Manager类继承自Employee，在父类的基础上增加了private double bonus：  
```java
	public boolean equals(Object obj){
    	if(!super.equals(obj)) return false;
        Manager other = (Manager) obj;
        return bonus == other.bonus;
    }
```  
*改进二*  
上述代码的第3步使用了getClass检测，这适用于子类拥有自己相等概念的情况，比如雇员和经理，只要对应的姓名、薪水和雇佣日期相等，而奖金不相等，就认为是不同的，可以使用getCalss检测。但是如果超类决定相等的概念，就可以使用instanceof进行检测，比如雇员的ID作为相等的概念，就可以用xxx instanceof Employee进行检测，并将Empolyee.equals声明为final。  

**equals方法要满足下面的特性**  
1. 自反性: 对于任何非空引用，x.equals(x)应该返回true  
2. 对称性：对于任何引用x和y, x.equals(y)的结果应该和y.equals(x)的结果相同  
3. 传递性：对于任何引用x、y和z,如果x.equals(y)返回true，y.equals(z)返回true，那么x.equals(z)也应该返回true  
4. 一致性: 如果x和y引用的对象没有发生变化，反复调用x.equals(y)应该返回同样的结果  
5. 对于任何非空引用x,x.equals(null)应该返回false

**下面我们给出编写一个完美的equals方法的建议**：  
1) 先快速检测引用是否相等，如果相等两个对象一定相等，不用继续检测  
2) 检测引用是否为空，如果为空，不必再检测，直接返回不等  
3) 如果equals语义在每个子类中有所改变(子类决定相等的概念)，用getClass检测:`if(getClass() != obj.getClass()) return false`;如果所有子类都拥有统一的语义(父类决定相等)，就使用instanceof检测：`if(!(obj instanceof ClassName) return false)`  
4) 将obj向下转型为相应类的类型变量  
5) 逐一比较数据域，注意基本数据类型用 == 检测，引用类型用equals方法检测    
**数组对象用静态的Arrays.equals方法判等**

拓展：[Integer判断相等，到底该用==还是equals](http://www.jianshu.com/p/9cb9c61b0986)  

当我们在方法里调用equals方法时，建议写字符串常量.equals(参数)的形式。比如：  
```java
public static boolean function(String str){
	return "hello world".equals(str);
}
```
这样可以防止str为null而抛出空指针异常，而根据equals()方法的对称性，这种调换并不会影响方法的功能。
#### 2) hashCode方法  
散列码(hash code)是由对象导出的一个整型值。散列码是没有规律的，如果x和y是两个不同的对象,x.hashCode()与y.hashCode()基本上不会相同。  
String类使用下列算法计算散列码：  
```java
int hash = 0;
for(int i = 0;i < length();i++){
	hash = 31 * hash + charAt(i);
}
```  
由于hashCode方法定义在Object类中，方法原型是`public int hashCode()`,因此每个对象都有一个默认的散列码，其值为对象的存储地址。内容相同的字符串的散列码相同，因为字符串的散列码是由内容导出的，而内容相同的StringBuilder对象的散列码不同，因为StringBuilder类中没有重写hashCode方法，它的散列码是Object类定义的默认hashCode方法导出的对象存储地址。  
**如果重写了equals方法，就必须重写hashCode方法，以便用户可以将对象插入到散列表中。**  
hashCode方法应该返回一个整型数值(可以是负数),**并合理地组合实例域的散列码**，以便能让各个不同的对象产生的散列码更均匀。  
例如，下面是Employee类的hashCode方法  
```java
public int hashCode(){
	return 7 * name.hashCode()
      + 11 * new Double(salary).hashCode()
      + 13 * hireDay.hashCode();
```  
**进一步改进**  
```java
public int hashCode(){
	return 7 * Objects.hashCode(name)
      + 11 * Double.hashCode(salary)
      + 13 * Objects.hashCode(hireDay);
```  
**从上面的代码我们可以发现，在重写hashCode方法时，我们要充分组合类的实例域，其中引用类型用Objects.hashCode()，基本类型用相应包装类的hashCode()**  
其中Objects.hashCode()是null安全的，如果参数为null，返回值是0,否则返回调用参数调用hashCode的结果。另外，使用静态方法Double.hashCode来避免创建Double对象    

**还有更简单的做法**  
```java
public int hashCode(){
	return Objects.hash(name,salary,hireDay);
 }
```  
**Equals和hashCode的定义必须一致,如果X.equals(y)返回true,那么x.hashCode()就应该等于y.hashCode(),用于组合散列的实例域equals中用于比较的实例域，比如equals比较的是雇员的ID，就需要散列ID**  
#### 3) toString方法  
方法原型是`public String toString()`,用于返回一个描述当前对象的字符串，Object类提供的默认实现是返回一个形式为：`类名@对象十六进制内存地址`的字符串，我们需要重写toString方法来返回更清晰的描述。数组对象则返回一个类似`[I@1a46e30`的字符串(前缀[I表明是一个整型数组,@后面是数组对象的十六进制内存地址)，修正方法是调用Arrays.toString方法，如果是多维数组需要调用Arrays.deepToString方法。  
toString不仅可以显式调用，也会在需要一个描述对象的字符串时隐式调用，比如用System.out.println语句打印一个对象，相当于打印对象的toString方法返回的字符串;又比如通过操作符"+"连接字符串时连接一个对象会调用它的toString方法。  
当重写toString时，如果返回的字符串涉及到类名,不要硬加入类名，可以通过getClass().getName()获得类名字符串，这样子类如果要调用父类的toString只用super.toString()就可以得到带有父类名的完整字符串描述。  
### 泛型数组列表  
在许多程序设计语言中，特别是在C++中，必须在编译时就确定整个数组的大小。而在Java中，允许在运行时确定数组大小。比如：  
```java
int actualSize = . . .;
Employee[] staff = new Employee[actualSize];
```  
但这还是没有完全解决运行时动态更改数组的问题，于是我们引入了一个ArrayList类(在Java.util包中)，它使用起来有点像数组，但在添加或删除元素时具有自动调节数组容量的功能。  
ArrayList是一个采用**类型参数**的**泛型类**,声明方式为`ArrayList<E> arrayList = new ArrayList<E>()`,从Java SE 7开始，可以省去右边的类型参数，即`ArrayList<E> arrayList = new ArrayList<>()`  
在Java SE 5以前的版本没有提供泛型类，只有一个ArrayList类，保存Object类型的元素，等价于`ArrayList<Object>`,可放入任何类型的对象。其提供的get方法只能返回Object对象，需要进行强制类型转换;且add方法和set方法不检查参数类型，具有一定的危险性。  
ArrayList管理这对象引用的一个内部数组，如果数组满了，ArrayList会自动创建一个更大的数组，并将所有的对象从较小的数组拷贝到较大的数组中。  

#### ArrayList类的常用API  
* `ArrayList<E>()` 构造一个初始容量为10的空列表  
* `ArrayList<E>(int initialCapacity)`构造一个具有指定初始容量的空列表  
* boolean add(E e) 将指定的元素添加到此列表的尾部，永远返回true  
* void add(int index, E element) 将指定的元素插入此列表中的指定位置。 
* int size() 返回此列表中的元素数。  
* void ensureCapacity(int minCapacity) 如果可以预先确定要插入的元素个数，使用此方法一次性扩容到位，否则通过add方法需要多次拷贝扩容，大大降低效率。  
* E get(int index) 返回此列表中指定位置上的元素。  
* E remove(int index) 移除此列表中指定位置上的元素并返回该元素。  
* boolean remove(Object o) 移除此列表中首次出现的指定元素（如果存在）。  
* void set(int index, E element) 用指定的元素替代此列表中指定位置上的元素。  
* boolean isEmpty() 如果此列表中没有元素，则返回 true  
* boolean contains(Object o) 如果此列表中包含指定的元素，则返回 true。  
* void clear() 移除此列表中的所有元素。  
* Object clone() 返回此 ArrayList 实例的浅表副本。  
* trimToSize() 将此 ArrayList 实例的容量调整为列表的当前大小。  
  

#### 对于数组列表有用的方法  
**1.数组和ArrayList互相转换**  

数组转ArrayList:  
```java
String[] array = {"red","green","blue"};
ArrayList<String> list = new ArrayList<>(Arrays.asList(array));
```  
ArrayList转数组:  
```java
String[] array = new String[list.size()];
list.toArray(array);
```  
#### 2.Collections类  
Collections类中有很多适用于ArrayList的静态方法，比如max和min方法返回列表中的最大和最小元素，sort方法对列表排序，shuffle方法随机打乱列表元素。

**ArrayList的元素只能是非基本数据类型的，所以如果要容纳基本类型的元素，我们需使用相应的包装类，且此时ArrayList的使用支持自动装箱和自动开箱。**  

### final关键字总结  
final关键字在不同语句中有不同的作用：  
1) **修饰类变量或成员变量** 如果是基本数据类型，表示该变量的值不可改变;如果是引用类型，表示该变量不能再指向其它对象，即引用值不可变。只能在定义时、构造方法中或构造代码块中赋值。  
2）**修饰方法的局部变量，即常量** final修饰的局部变量只要在使用前被赋值即可，不要求在定义时即赋值，但赋值后不可改变。  
3) **修饰方法** 表示该方法不能被重写，注意final不能修饰构造方法。  
4) **修饰类** 表示该类不能被扩展

### 继承的设计技巧  
#### 1.将公共操作和域放在超类  
#### 2.不要使用受保护的域  
protected机制并不能够带来更好的保护，其原因主要有两点:  
第一，子类集合是无限制的，任何一个人都能够由某个类派生一个子类，并编写代码以直接访问protected的域，从而破坏了封装性。  
第二，同一个包中的所有类都可以访问protected的域，而不管它是否为这个类的子类。  
不过，protected方法对于指示那些不提供一般用途而应在子类中重新定义的方法很有用，比如Object提供的clone方法就是protected的  
#### 3.使用继承实现 "is-a" 关系  
#### 4.除非所有继承的方法都有意义，否则不要使用继承   
比如在使用ArrayList类设计一个Stack类时，不应该使用继承，因为栈只能在栈顶操作元素，而ArrayList中的方法可以在任意位置插入、删除和访问任意位置的元素，这显然不合适。此时应该使用组合代替继承。  
#### 5.在覆盖方法时，不要改变预期的行为  
#### 6.使用多态，而非类型信息   
即尽量面向父类泛化编程，把不同子类的类似行为的方法定义在父类里，并在子类里覆盖该行为
#### 7.不要过多地使用反射  
反射机制使得人们可以通过在运行时查看域和方法，让人们编写出更具有通用性的程序。这种功能对于编写系统程序极为实用，但不适于编写应用程序。反射是很脆弱的，即编译器很难帮助人们发现程序中的错误，因此只有在运行时才发现错误并导致异常。




