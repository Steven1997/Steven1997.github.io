---
title: Java学习总结之设计模式
date: 2018-01-30 11:01:50
tags: Java,软件工程
---
### 设计模式概述  
设计模式（Design pattern）代表了最佳的实践，通常被有经验的面向对象的软件开发人员所采用。设计模式是软件开发人员在软件开发过程中面临的一般问题的解决方案。这些解决方案是众多软件开发人员经过相当长的一段时间的试验和错误总结出来的。  
设计模式是**一套被反复使用、多数人知晓的、经过分类的、代码设计经验的总结。**  
### 使用设计模式的目的  
为了代码可重用性、让代码更容易被他人理解、保证代码可靠性。 设计模式使代码编写真正工程化；设计模式是软件工程的基石脉络，如同大厦的结构一样。  
### 提出者  
设计模式是由GOF(四人帮，Gang of Four)提出的。  
在 1994 年，由 Erich Gamma、Richard Helm、Ralph Johnson 和 John Vlissides 四人合著出版了一本名为 Design Patterns - Elements of Reusable Object-Oriented Software（中文译名：设计模式 - 可复用的面向对象软件元素） 的书，该书首次提到了软件开发中设计模式的概念。  
四位作者合称 GOF（四人帮，全拼 Gang of Four）。他们所提出的设计模式主要是基于以下的面向对象设计原则：
* 对接口编程而不是对实现编程。
* 优先使用对象组合而不是继承。    

### 设计模式的使用  
设计模式在软件开发中的两个主要用途。  
#### 开发人员的共同平台  
设计模式提供了一个标准的术语系统，且具体到特定的情景。例如，单例设计模式意味着使用单个对象，这样所有熟悉单例设计模式的开发人员都能使用单个对象，并且可以通过这种方式告诉对方，程序使用的是单例模式。  
#### 最佳的实践  
设计模式已经经历了很长一段时间的发展，它们提供了软件开发过程中面临的一般问题的最佳解决方案。学习这些模式有助于经验不足的开发人员通过一种简单快捷的方式来学习软件设计。  

### 设计模式的类型  
根据设计模式的参考书** Design Patterns - Elements of Reusable Object-Oriented Software（中文译名：设计模式 - 可复用的面向对象软件元素）** 中所提到的，总共有** 23 种**设计模式。这些模式可以分为三大类：**创建型模式（Creational Patterns）、结构型模式（Structural Patterns）、行为型模式（Behavioral Patterns）**。当然，我们还会讨论另一类设计模式：**J2EE 设计模式。**  
![fail](Java学习总结之设计模式/general1.png)
![fail](Java学习总结之设计模式/general2.png)  
![fail](Java学习总结之设计模式/general3.png)  
  
 下面用一个图片来整体描述一下设计模式之间的关系：  
 
![fail](Java学习总结之设计模式/设计模式之间的关系.png "设计模式之间的关系")   
### 设计模式的七大原则  
#### 1、单一职责原则（SRP：Single Responsibility Principle）
所谓职责是指类变化的原因。如果一个类有多于一个的动机被改变，那么这个类就具有多于一个的职责。而单一职责原则就是指一个类或者模块应该有且只有一个改变的原因。如果一个类承担的职责过多，就等于把这些职责耦合在一起了。一个职责的变化可能会削弱或者抑制这个类完成其他职责的能力。这种耦合会导致脆弱的设计，当发生变化时，设计会遭受到意想不到的破坏。而如果想要避免这种现象的发生，就要尽可能的遵守单一职责原则。此原则的核心就是解耦和增强内聚性。  
#### 2、开闭原则（OCP：Open Close Principle）  
开闭原则的意思是：对扩展开放，对修改关闭。在程序需要进行拓展的时候，不能去修改原有的代码，实现一个热插拔的效果。简言之，是为了使程序的扩展性好，易于维护和升级。想要达到这样的效果，我们需要使用接口和抽象类，后面的具体设计中我们会提到这点。  
#### 3、里氏代换原则（LSP：Liskov Substitution Principle）  
里氏代换原则是面向对象设计的基本原则之一。 里氏代换原则中说，任何基类可以出现的地方，子类一定可以出现。LSP 是继承复用的基石，只有当派生类可以替换掉基类，且软件单位的功能不受到影响时，基类才能真正被复用，而派生类也能够在基类的基础上增加新的行为。里氏代换原则是对开闭原则的补充。实现开闭原则的关键步骤就是抽象化，而基类与子类的继承关系就是抽象化的具体实现，所以里氏代换原则是对实现抽象化的具体步骤的规范。  
#### 4、依赖倒转原则（DIP：Dependence Inversion Principle）  
这个原则是开闭原则的基础，具体内容：针对接口编程，依赖于抽象而不依赖于具体。  
#### 5、接口隔离原则（ISP：Interface Segregation Principle）  
这个原则的意思是：使用多个隔离的接口，比使用单个接口要好。它还有另外一个意思是：降低类之间的耦合度。由此可见，其实设计模式就是从大型软件架构出发、便于升级和维护的软件设计思想，它强调降低依赖，降低耦合。  
#### 6、迪米特法则，又称最少知道原则（DP：Demeter Principle）  
最少知道原则是指：一个实体应当尽量少地与其他实体之间发生相互作用，使得系统功能模块相对独立。  
#### 7、合成复用原则（CRP：Composite Reuse Principle）  
合成复用原则是指：尽量使用合成/聚合的方式，而不是使用继承。  

#### 下面我们将通过Java实例来总结常用的设计模式。

### 1、工厂模式  
### 2、抽象工厂模式  
### 3、单例模式  
单例模式（Singleton Pattern）是 Java 中最简单的设计模式之一。这种类型的设计模式属于**创建型模式**，它提供了一种创建对象的最佳方式。  
这种模式涉及到一个**单一的类**，该类负责**创建自己的对象，同时确保只有单个对象被创建**。这个类提供了一种访问其唯一的对象的方式，**可以直接访问，不需要实例化该类的对象**。  
**注意：**  
1、单例类只能有**一个实例**。  
2、单例类必须**自己创建自己的唯一实例**，而不是在外部随意地new对象。  
3、单例类必须给所有其他对象**提供**这一实例。  
#### 介绍
**意图：**保证一个类仅有一个实例，并提供一个访问它的全局访问点。  
**主要解决：**一个全局使用的类频繁地创建与销毁。  
**何时使用：**当想控制实例数目，节省系统资源的时候。  
**如何解决：**判断系统是否已经有这个单例，如果有则返回，如果没有则创建。  
**关键代码：**构造函数是私有的。  
**应用实例：** 1、一个党只能有一个主席。 2、Windows 是多进程多线程的，在操作一个文件的时候，就不可避免地出现多个进程或线程同时操作一个文件的现象，所以所有文件的处理必须通过唯一的实例来进行。 3、一些设备管理器常常设计为单例模式，比如一个电脑有两台打印机，在输出的时候就要处理不能两台打印机打印同一个文件。  
**优点：** 1、在内存里只有一个实例，减少了内存的开销。2、避免频繁的创建和销毁实例，提高性能（比如管理学院首页页面缓存）。 2、避免对资源的多重占用（比如写文件操作）。   
**缺点：**1、扩展比较困难，没有接口，不能继承，与单一职责原则冲突，一个类应该只关心内部逻辑，而不关心外面怎么样来实例化。2、如果实例化后的对象长期不利用，系统将默认为垃圾进行回收，造成对象状态丢失。  
**使用场景：** 1、当多个实例存在可能引起程序逻辑错误，如要求生产唯一序列号。 2、对系统内资源要求统一读写，如读写配置信息，又如WEB 中的计数器，不用每次刷新都在数据库里加一次，用单例先缓存起来。 3、创建的一个对象需要消耗的资源过多，但同时又需要用到该对象，比如 I/O 与数据库的连接等。  
**注意事项：**getInstance() 方法中需要使用同步锁 synchronized (Singleton.class) 防止多线程同时进入造成 instance 被多次实例化。  
**实现**  
我们将创建一个 SingleObject 类。SingleObject 类有它的私有构造函数和本身的一个静态实例。  
SingleObject 类提供了一个静态方法，供外界获取它的静态实例。SingletonPatternDemo，我们的演示类使用 SingleObject 类来获取 SingleObject 对象。  
![fail](Java学习总结之设计模式/Singleton Pattern.jpg)  
**步骤 1**
创建一个 Singleton 类。  
```java
SingleObject.java
public class SingleObject {

   //创建 SingleObject 的一个对象
   private static SingleObject instance = new SingleObject();

   //让构造函数为 private，这样该类就不会被实例化
   private SingleObject(){}

   //获取唯一可用的对象
   public static SingleObject getInstance(){
      return instance;
   }

   public void showMessage(){
      System.out.println("Hello World!");
   }
}
```
**步骤 2**  
从 singleton 类获取唯一的对象。
```java
SingletonPatternDemo.java
public class SingletonPatternDemo {
   public static void main(String[] args) {

      //不合法的构造函数
      //编译时错误：构造函数 SingleObject() 是不可见的
      //SingleObject object = new SingleObject();

      //获取唯一可用的对象
      SingleObject object = SingleObject.getInstance();

      //显示消息
      object.showMessage();
   }
}
```
**步骤 3**  
验证输出。
```java
Hello World!
```
#### 单例模式的几种实现方式  
单例模式的实现有多种方式，如下所示：  
**1、懒汉式，线程不安全**  
**是否 Lazy 初始化：**是  
**是否多线程安全：**否  
**实现难度：**易  
**描述：**这种方式是最基本的实现方式，这种实现最大的问题就是不支持多线程。因为没有加锁 synchronized，所以严格意义上它并不算单例模式。
这种方式 lazy loading 很明显，不要求线程安全，在多线程不能正常工作。  
**代码实例：**
```java
public class Singleton {  
    private static Singleton instance;  
    private Singleton (){}  
  
    public static Singleton getInstance() {  
    if (instance == null) {  /*懒汉式标志：Lazy 初始化,
    在外部第一次请求使用该类对象时才实例化，是时间换空间的模式*/
        instance = new Singleton();  
    }  
    return instance;  
    }  
}  
```
**接下来介绍的几种实现方式都支持多线程，但是在性能上有所差异。**  
**2、懒汉式，线程安全**  
**是否 Lazy 初始化：**是  
**是否多线程安全：**是  
**实现难度：**易  
**描述：**这种方式具备很好的 lazy loading，能够在多线程中很好的工作，但是，效率很低，99% 情况下不需要同步。  
**优点：**第一次调用才初始化，避免内存浪费。  
**缺点：**必须加锁 synchronized 才能保证单例，但加锁会影响效率。  
getInstance() 的性能对应用程序不是很关键（该方法使用不太频繁）。  
**代码实例：**
```java
public class Singleton {  
    private static Singleton instance;  
    private Singleton (){}  
    public static synchronized Singleton getInstance() {  
    //加同步锁
    if (instance == null) {   
        instance = new Singleton();  
    }  
    return instance;  
    }  
} 
```
**3、饿汉式**    
**是否 Lazy 初始化：**否  
**是否多线程安全：**是  
**实现难度：**易  
**描述：**这种方式比较常用，但容易产生垃圾对象。  
**优点：**没有加锁，执行效率会提高。  
**缺点：**类加载时就初始化，浪费内存。  
它基于 classloder 机制避免了多线程的同步问题，不过，instance 在类装载时就实例化，虽然导致类装载的原因有很多种，在单例模式中大多数都是调用 getInstance 方法， 但是也不能确定有其他的方式（或者其他的静态方法）导致类装载，这时候初始化 instance 显然没有达到 lazy loading 的效果。  
**代码实例：**  
```java
public class Singleton {  
    private static Singleton instance = new Singleton();  
                           /*饿汉式标志：在类加载时直接初始化，
                           是空间换时间的模式*/
    private Singleton (){}  
    public static Singleton getInstance() {  
    return instance;  
    }  
}  
```
**4、双检锁/双重校验锁（DCL，即 double-checked locking）**    
**JDK 版本：**JDK1.5 起  
**是否 Lazy 初始化：**是    
**是否多线程安全：**是  
**实现难度：**较复杂  
**描述：**这种方式采用双锁机制，安全且在多线程情况下能保持高性能。  
getInstance() 的性能对应用程序很关键。  
**代码实例：**  
```java
public class Singleton {  
    private volatile static Singleton singleton;  
    private Singleton (){}  
    public static Singleton getSingleton() {  
    if (singleton == null) {  
        synchronized (Singleton.class) {  
        if (singleton == null) {  
            singleton = new Singleton();  
        }  
        }  
    }  
    return singleton;  
    }  
}  
```
**5、登记式/静态内部类**  
**是否 Lazy 初始化：**是  
**是否多线程安全：**是  
**实现难度：**一般  
**描述：**这种方式能达到双检锁方式一样的功效，但实现更简单。对静态域使用延迟初始化，应使用这种方式而不是双检锁方式。这种方式只适用于静态域的情况，双检锁方式可在实例域需要延迟初始化时使用。  
这种方式同样利用了 classloder 机制来保证初始化 instance 时只有一个线程，它跟第 3 种方式不同的是：第 3 种方式只要 Singleton 类被装载了，那么 instance 就会被实例化（没有达到 lazy loading 效果），而这种方式是 Singleton 类被装载了，instance 不一定被初始化。因为 SingletonHolder 类没有被主动使用，只有通过显式调用 getInstance 方法时，才会显式装载 SingletonHolder 类，从而实例化 instance。想象一下，如果实例化 instance 很消耗资源，所以想让它延迟加载，另外一方面，又不希望在 Singleton 类加载时就实例化，因为不能确保 Singleton 类还可能在其他的地方被主动使用从而被加载，那么这个时候实例化 instance 显然是不合适的。这个时候，这种方式相比第 3 种方式就显得很合理。  
**代码实例：**
```java
public class Singleton {  
    private static class SingletonHolder {  
    private static final Singleton INSTANCE = new Singleton();  
    }  
    private Singleton (){}  
    public static final Singleton getInstance() {  
    return SingletonHolder.INSTANCE;  
    }  
}   
```
**6、枚举**  
**JDK 版本：**JDK1.5 起  
**是否 Lazy 初始化：**否  
**是否多线程安全：**是  
**实现难度：**易  
**描述：**这种实现方式还没有被广泛采用，但这是实现单例模式的最佳方法。它更简洁，自动支持序列化机制，绝对防止多次实例化。  
这种方式是 Effective Java 作者 Josh Bloch 提倡的方式，它不仅能避免多线程同步问题，而且还自动支持序列化机制，防止反序列化重新创建新的对象，绝对防止多次实例化。不过，由于 JDK1.5 之后才加入 enum 特性，用这种方式写不免让人感觉生疏，在实际工作中，也很少用。  
不能通过 reflection attack 来调用私有构造方法。  
**代码实例：**
```java
public enum Singleton {  
    INSTANCE;  
    public void whateverMethod() {  
    }  
}  
```
**经验之谈：**一般情况下，不建议使用第 1 种和第 2 种懒汉方式，建议使用第 3 种饿汉方式。只有在要明确实现 lazy loading 效果时，才会使用第 5 种登记方式。如果涉及到反序列化创建对象时，可以尝试使用第 6 种枚举方式。如果有其他特殊的需求，可以考虑使用第 4 种双检锁方式。  

参考资料：菜鸟教程之设计模式