---
title: Java学习总结之反射
date: 2017-10-14 14:32:15
tags: Java
---
　　反射库提供了一个非常丰富且精心设计的工具集，以便能够动态编写能够操纵Java代码的程序。这项功能被大量应用于JavaBeans中，它是Java组件的体系结构。  
### 1.什么是反射？  
　　**反射是可以让我们在运行时获取类的方法、属性、父类、接口等类的内部信息的机制。**也就是说，反射本质上是一个“反着来”的过程。我们通过new创建一个类的实例时，实际上是由Java虚拟机根据这个类的Class对象在运行时构建出来的，而反射是通过一个类的Class对象来获取它的定义信息，从而我们可以访问到它的属性、方法，知道这个类的父类、实现了哪些接口等信息。  
　　要想理解反射的原理，首先要了解什么是类型信息。Java让我们识别对象和类的信息，主要有2种方式：一种是传统的RTTI，它假定我们在编译时已经知道了所有的类型信息；另一种是反射机制，它允许我们在运行时发现和使用类的信息。
### 2.反射的作用  
　　1.在运行时分析类的能力，即查看类的方法、属性、父类、接口等类的内部信息的机制  
　　2.在运行时分析对象的数据域  
　　3.实现通用的数组操作代码  
　　4.利用Method对象，这个对象很像C++中的函数指针  

### 3.Class类  
　　我们知道使用javac能够将.java文件编译为.class文件，这个.class文件包含了我们对类的原始定义信息（父类、接口、构造器、属性、方法等）。Class 类的实例表示正在运行的 Java 应用程序中的类或接口。在 Java 中，每个 Class 都有一个相应的 Class 对象，即对于每一个类，.class文件在运行时会被ClassLoader加载到JVM中，当一个.class文件被加载后，JVM会为之生成一个Class对象，用于表示这个类的类型信息，我们在程序中通过new实例化的对象实际上是在运行时根据相应的Class对象构造出来的。确切的说，这个Class对象实际上是`java.lang.Class`类的一个实例，从中我们可以得出结论：万物皆对象，`任何类型(包括基本类型，引用类型，void关键字等).class`都是`java.lang.Class`的实例，简言之，class对象是Class泛型类的实例，它代表了一个类型。由于`java.lang.Class`类不存在公有构造器，它在每个类第一次被加载时由JVM自动调用，因此我们不能直接实例化这个类，我们可以通过以下方法获取一个Class对象。  
　　在下面的讲解中，我们将以People类和Student类为例：  
```java
public class People {
    private String name;
    private int age;
    
    public People(String name, int age) {
        this.name = name;
        this.age = age;
    }
    
    public int getAge() {
        return age;
    }

    public String getName() {
        return name;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public void setName(String name) {
        this.name = name;
    }
    
    public void speak() {
        System.out.println(getName() + " " + getAge());
    }
}
```

```java
public class Student extends People {
    private int grade;
    
    public Student(String name, int age) {
        super(name, age);
    }
    
    public Student(String name, int age, int grade) {
        super(name, age);
        this.grade = grade;
    }
    
    public int getGrade() {
        return grade;
    }

    public void setGrade(int grade) {
        this.grade = grade;
    }
  
    private void learn(String course) {
        System.out.println(name + " learn " + course);
    }
 }
 ```
 
#### 获取class对象有以下三种:    
 1) 可以通过`类名.class`得到相应类的Class对象，如:   
 
```java
  Class peopleClass = People.class;
```
 
2) 如果已知类的**全限定名称(包含包名)**，可以通过Class的forName静态方法得到类的Class对象，如：  

```java
Class peopleClass = Class.forName("cn.habitdiary.People");
//假设People类在cn.habitdiary包中
```

　　在使用forName时必须要保证传入的字符串是一个类名或接口名，否则会抛出一个ClassNotFoundException,这是一个必检异常，所以我们在使用该方法时必须提供一个异常处理器,例如:

```java
try{
	String name = "xxx";
    Class c1 = Class.forName(name);
    }
    catch(Exception e){
    e.printStackTrace();
    }
```

3) 可以通过`类的实例对象.getClass()`得到相应类的Class对象，如:

```java
People people = new People("Steven"， 20);
Class peopleClass = people.getClass();
```

　　`实例对象.getClass().getName()` 可以获取当前对象的类的全限定名称(包含包名)  
　　`实例对象.getClass().getSimpleName()`可以获取当前对象的类的底层类简称(不含包名)  
　　`实例对象.getClass().getCanonicalName()`大部分情况和getName()相同，但在表示数组或内部类时有所区别，比如对于String数组，getName返回的是[Ljava.lang.String之类的表现形式，而getCanonicalName返回的就是跟我们声明类似的形式。  
　　但在类加载的时候需要的是getName得到的那样的名字，而在根据类名字创建文件的时候最好使用getCanonicalName()  

**对于基本数据类型的封装类，还可以采用.TYPE来获取相对应的基本数据类型的 Class 实例**   

**三种方式的比较：**    
1.调用`Class.forName()`方法，如果类没有加载就加载，加载时执行static语句，找不到就抛出异常，也可以理解为手动加载类的一种方法，它会自动初始化Class对象。  

2.`getClass()`方法，在已经持有该类的对象时来获取Class引用。其Class对象已经被初始化。  

3.`.class`方式创建Class对象引用时，不会自动初始化Class对象。主要进行下面的步骤：    
1)加载，类加载器查找字节码（classpath）创建Class对象；   
2)链接，为静态域分配存储空间；   
3)初始化，其被延迟到静态方法或非常数静态域首次引用时。  

**总结：**Java获得Class对象的引用的方法中，`Class.forName()`方法会自动初始化Class对象，而`.class`方法不会，`.class`的初始化被延迟到静态方法或非常数静态域的首次引用。

**注意：**  
　　1.一个Class对象实际上表现的是一个类型，而这个类型未必一定是一种类。例如，int不是类，但int.class是一个Class对象。  
　　2.Class类实际上是一个泛型类。  `Class c = T.class`实际上是`Class<T> c = T.class`。`Class c = x.getClass()`实际上是`Class<? extends T> c = x.getClass()`(T的x的声明类型，x.getClass()获得的是x的实际类型的Class对象)。但有时候我们不能提前确定class对象的类型,如`Class c = Class.forName("T")`实际上是`Class<?> c = Class.forName("T")`。 
　　3.虚拟机为每个类型管理一个Class对象，可以用 == 运算符实现两个类对象比较的操作，这可以用来判断两个对象属不属于同一个类。   
　　4.getClass()方法返回的是对象实际类型的class对象，而不是声明类型的class对象。  
　　5.newInstance()方法可以返回一个Class对象对应类的新实例(返回值类型是Object)，**前提要有无参的构造方法，newInstance()方法是通过调用无参构造方法来创建对象的**。比如:

```java
String s = "java.util.Random";
Object m = Class.forName(s).newInstance();
```

　　**如果希望给构造器提供参数，就不能使用这种写法，而必须使用Constructor类中的newInstance方法。**   
  
**类的静态加载和动态加载**  
**静态加载：**在编译时就需要加载所有可能用到的类，比如new关键字就是静态加载类。  
**动态加载：**在运行时加载类。     
静态加载类的缺点是：比如用new创建了多个类的对象，其中某一个类不存在，则整个程序无法通过编译。而如果动态加载类，只要不使用不存在的类，其他类还可以正常使用。 

比如  
```java
class Office{
	public static void main(String[] args){
    	//静态加载类
    	if("Word".equals(args[0])){
        	Word w = new Word();
            w.start();
         }
         if("Excel".equals(args[0]){
         	Excel e = new Excel();
            e.start();
         }
 }
```
上面的程序通过new关键字创建对象，是静态加载类，所以如果Word类和Excel类中缺少一个，另一个类即使存在也无法通过编译。  

如果通过反射动态加载类可以解决这个问题，如下：  
```java
class OfficeBetter{
	public static void main(String[] args){
    	try{
        	//动态加载类,在运行时刻加载
        	Class c = Class.forName(args[0]);
            /*通过类类型,创建该类的对象,此时需要强制转换为Excel和Word的公有类型，
            所以可以定义OfficeAble接口，让Excel和Word实现这个接口*/
            OfficeAble oa = (OfficeAble)c.newInstance();
            oa.start();
            }
            catch(Exception e){
            e.printStackTrace();
            }
 }
```
由于是动态加载类，新增其他实现OfficeAble接口的类不必重新编译OfficeBetter类。

### 4.在运行时分析类的能力  
　　下面简要介绍一下反射机制最重要的内容 —— 检查类的结构。  
Java中为了支持反射机制主要提供了以下的类：  
java.lang.Class  
java.lang.reflect.Field   
java.lang.reflect.Constructor  
java.lang.reflect.Method  
java.lang.reflect.Modifier  

**java.lang.Class类的常用API如下：**  
* Field[] getFields()   
* Field[] getDeclaredFields()   
* Method[] getMethods()  
* Method[] getDeclaredMethods()      
* Constructor< ? >[] getConstructors()  
* Constructor< ? >[] getDeclaredConstructors() 
* Class< ? > getSupperClass() 
* Class< ? >[] getInterfaces()

**提示：**getFields、getMethods和getConstructors方法将分别返回类提供的public域、方法和构造器数组，其中包括超类的公有成员;而getDeclaredFields、getDeclaredMethods和getDeclaredConstructors方法将分别返回类中声明的全部域、方法和构造器，不论访问权限，但不包括超类的成员。getSupperClass()返回class对象对应类的超类的class对象，没有显式继承的类的超类是Object。getInterfaces返回class对象对应类的所有接口的class对象

**其中java.lang.reflect包中的三个类Field、Constructor和Method分别用于描述类的域、构造器、方法。  
这三个类的常用API如下：**  
* String getName() 返回一个用于描述域名、构造器或方法的字符串  
* Class< ? > getDeclaringClass() 返回一个用于描述类中定义的域、构造器或方法的Class对象  
* Class< ? >[] getExceptionTypes() (在Constuctor和Method类中)  
返回一个用于描述方法抛出异常类型的Class对象数组  
* int getModifiers() 返回一个描述域、构造器或方法的修饰符的整型数值。使用Modifier的toString静态方法可以分析这个返回值  
* Class< ? >[] getParameterTypes() (在Constructor和Method类中) 返回一个用于描述参数类型的Class对象数组  
* Class< ? > getReturnType() (在Method类中) 返回一个用于描述返回类型的Class对象  

**java.lang.reflect.Modifier类的常用API如下**  
* static String toString(int modifiers)   
返回修饰符对应的字符串描述
* static boolean isAbstract(int modifiers)  
* static boolean isFinal(int modifiers)
* static boolean isInterface(int modifiers)
* static boolean isNative(int modifiers)
* static boolean isPrivate(int modifiers)
* static boolean isProtected(int modifiers)
* static boolean isPublic(int modifiers)
* static boolean isStatic(int modifiers)
* static boolean isStrict(int modifiers)
* static boolean isSynchronized(int modifiers)
* static boolean isVolatile(int modifiers)  
上述方法检测修饰符是否是某一特定修饰符 
java.lang.reflect.Modifier类一般和Field、Constructor、Method类的getModifiers()方法配套使用，用于解析该方法返回的整型数值的含义   

**下面是一个检测类内部结构的例子**  

```java
import java.lang.reflect.*;
import java.lang.Class;
import java.util.Scanner;

public class ReflectionTest {

	public static void main(String[] args) {
		String name;
		if(args.length > 0) 
			name = args[0];
		else {
			Scanner in = new Scanner(System.in);
			System.out.println("Enter class name (e.g. java.util.Date)");
			name = in.next();
		}
		
		try {
			Class<?> c1 = Class.forName(name);
			Class<?> superc1 = c1.getSuperclass();
			String modifiers = Modifier.toString(c1.getModifiers());
			if (modifiers.length() > 0) 
				System.out.print(modifiers + " " );
			System.out.print("class " + name);
			if(superc1 != null && superc1 != Object.class)
				System.out.print("  extends "  +  superc1.getSimpleName());
			System.out.print("\n{\n");
			printFields(c1);
			System.out.println();
			printConstructors(c1);
			System.out.println();
			printMethods(c1);
			System.out.println("}");
				
		}
		catch(ClassNotFoundException e)
		{
			e.printStackTrace();
		}
		System.exit(0);
	}
	
	public static void printConstructors(Class<?> c1) {
		Constructor<?>[] constructors = c1.getDeclaredConstructors();
		
		for(Constructor<?> c : constructors) {
			String name = c.getName();
			System.out.print("     ");
			String modifiers = Modifier.toString(c.getModifiers());
			if(modifiers.length() > 0)
				System.out.print(modifiers + " ");
			System.out.print(name + "(");
			Class<?>[] paramTypes = c.getParameterTypes();
			for(int j = 0;j < paramTypes.length;j++) {
				if(j > 0)
					System.out.print(", ");
				System.out.print(paramTypes[j].getSimpleName());
			}
			System.out.println(");");
			
		}
	}
	
	public static void printMethods(Class<?> c1)
	{
		Method[] methods = c1.getDeclaredMethods();
		
		for(Method m :methods) {
			Class<?> retType = m.getReturnType();
			String name = m.getName();
			
			System.out.print("     ");
			String modifiers = Modifier.toString(m.getModifiers());
			if(modifiers.length() > 0)
				System.out.print(modifiers + " ");
			System.out.print(retType.getSimpleName() + " " + name + "(");
			Class<?>[] paramTypes = m.getParameterTypes();
			for(int j = 0;j < paramTypes.length;j++) {
				if(j > 0)
					System.out.print(", ");
				System.out.print(paramTypes[j].getSimpleName());
			}
			System.out.println(");");
		}
		
		
		}
	public static void printFields(Class<?>  c1)
	{
		Field[] Fields = c1.getDeclaredFields();
		
		for(Field f : Fields) {
			Class<?> type = f.getType();
			String name = f.getName();
			System.out.print("     ");
			String modifiers = Modifier.toString(f.getModifiers());
			if(modifiers.length() > 0)
				System.out.print(modifiers + "  ");
			System.out.println(type.getSimpleName() + " " + name + ";");
			}
	}

}
```
### 5.在运行时使用反射分析对象  
　　反射不仅可以查看类的域、构造器、方法等，还可以进一步查看某个对象指定数据域的值。  
　　查看对象域的关键方法是Field类中的get方法。如果f是一个Field类型的对象(例如，通过getDeclaredFields得到的对象)，obj是某个包含f域的类的对象，f.get(obj)将返回一个Object对象，其值为obj域的当前值。比如：
  
```java
Employee harry = new Empolyee("Harry Hacker",35000,10,1,1989);
Class<Employee> c1 = harry.getClass();
Field f = c1.getDeclaredField("name"); //返回某一个特定域
f.setAccessible(true); //由于name是私有域，必须先设置为可访问
Object v = f.get(harry);
```
  
  
　　上述的String可以作为Object返回，但如果某个域是基本数据类型，比如double，可以使用Field类的getDouble方法返回double类型数值，也可以使用get方法，反射机制会将其自动装箱成Double类型对象。f.set(obj,value) 可以把obj对象的f域设置为value  

**下面是一些相关API**  
在java.lang.reflect.Field中：  
* Object get(Object obj)  
 返回obj对象中用Field对象表示的域值
* xxx getXxx(Object obj)  
 返回obj对象的基本类型的域的值
* void set(Object obj,Object newValue)  
 用一个新值设置obj对象中Field对象表示的域    
 
在java.lang.Class中：  
* Field getField(String name)   
 返回指定名称的公有域  
* Field getDeclaredField(String name)  
* 返回指定名称的声明的域  

在java.lang.reflect.AccessibleObject中：  
* void setAccessible(boolean flag)  
 为反射对象设置可访问标志，flag为true表明屏蔽Java语言的访问检查，使得对象的私有属性也可以被查询和设置  
* boolean isAccessible()  
 返回反射对象的可访问标志的值  
* static void setAccessible(AccessibleObject[] array,boolean flag)  
 批量设置AccessibleObject(是Field、Constructor、Method的公共超类)数组的所有元素的可访问标志 
 
### 6.使用反射编写泛型数组代码  
　　java.lang.reflect包中的Array类允许动态地创建数组。例如，在Arrays类中有copyOf方法，可以扩展已经填满的数组。  
```java
Employee[] a = new Employee[100];
a = Arrays.copyOf(a,2 * a.length);
```
　　我们想要编写一个适用于所有数组类型的copyOf方法，下面是第一次尝试:  
```java
public static Object[] badCopyOf(Object[] a,int newLength){
	Object[] newArray = new Object[newLength];
    System.arraycopy(a,0,newArray,0,Math.min(a.length,newLength);
    return newArray;
}
```
　　上述代码存在一个错误，即使用了`new Object[newLength]`创建数组，这样会在运行时抛出ClassCastException,将一个Employee[]临时转换成Object[]，再把它转回来是可以的，但从一开始就是Object[]的数组永远不能转换成Employee[]数组。  
**为了解决这个问题，下面提供java.lang.reflect.Array中的API**  
* static Object get(Object array,int index)  
 返回对象数组某个位置上的元素
* static xxx getXxx(Object array,int index)  
 (xxx是基本数据类型)返回基本类型数组某个位置上的值 
* static void set(Object array,int index,Object newValue)  
 设置对象数组某个位置上的元素
* static void setXxx(Object array,int index,xxx newValue)    
 (xxx是基本数据类型)设置基本类型数组某个位置上的值 
* static int getLength(Object array)  
 返回数组的长度  
* static Object newInstance(Class componentType,int length)  
* static Object newInstance(Class componentType,int[] length)
 返回一个具有给定类型、给定维数的新数组  
 
 下面给出正确的代码实现：
```java
 public static Object goodCopyOf(Object a,int newLength){
 {
 	Class c1 = a.getClass();//获取a数组的类对象
     if(!c1.isArray()) return null;//确认是一个数组
     Class componentType = c1.getComponentType();
     //获取数组类型
     int length = Array.getLength(a);
     Object newArray = Array.newInstance(componentType,newLength);
     System.arraycopy(a,0,newArray,0,Math.min(length,newLength));
     return newArray;   
 }
```
 这个CopyOf方法可以扩展任意类型的数组，不仅仅是对象数组使用示例:
 ```java
 int[] a = {1,2,3,4,5};
 a = (int[]) goodCopyOf(a,10);
```
 　　为了实现上述操作，应该将goodCopyOf的参数声明为Object类型，而不要声明为Object[],因为整数数组类型可以被转换为Object，但不能转换成Object[]类型 
### 7.调用任意方法  
　　通过反射还可以调用任意方法，这是通过Method类的invoke方法实现的，方法签名是:`Object invoke(Object obj,Object... args)`,Object obj表示调用方法的对象，Object...args表示方法的参数列表。  
如果方法是静态方法，将第一个参数设置为null;如果方法是非静态无参方法，第二个参数列表可以忽略。  
　　例如：`String n = (String)m1.invoke(harry);`(m1表示Employee类的getName方法)。如果方法m1的返回值是void，则invoke方法返回null，否则返回具体类型。如果返回值是基本类型，invoke方法会返回其包装器类型,可以利用自动开箱将其还原为基本数据类型。例如:`double s = (Double)m2.invoke(harry);`(m2表示Employee类的getSalary方法)
getMethods方法和getDeclaredMethods会返回一个Method对象列表，如果要得到特定的Method对象，可以调用Class类的getMethod方法，其签名是Method getMethod(String Methodname,Class...parameterTypes)。  
例如：  
```java
Method m1 = Employee.class.getMethod("getName");  
Method m2 = Employee.class.getMethod("raiseSalary",double.class);  
```
　　下面给出一个调用任意方法打印函数表的程序(以自定义的square和Math.sqrt方法为例)：  

```java
import java.lang.reflect.*;
public class MethodTableTest
{
   public static void main(String[] args)
   {
Method square =         			MethodTableTest.class.getMethod("square",double.class);
    Method sqrt = Math.class.getMethod("sqrt",double.class);
    
    printTable(1,10,10,square);
    printTable(1,10,10,sqrt);
    }
    
    public static double square(double x)
    {
    return x * x;
    }
    
    public static void printTable(double from,double to,int n,Method f)
    {
    System.out.println(f);
    double dx = (to - from) / (n - 1);
    
    for(double x = from;x <= to;x += dx)
    {
      try
      {
         double y = (Double)f.invoke(null,x);
         System.out.printf("%10.4f | %10/4f\n",x,y);
      }
      catch(Exception e)
      {
         e.printStackTrace();
      }
    }
 ```
 **invoke方法如果提供了错误的参数，会抛出一个异常，所以要提供一个异常处理器**  
 建议在有必要的时候才使用invoke方法，有如下原因:  
 1.invoke方法的参数和返回值必须是Object类型，意味着必须进行多次类型转换，这样会使编译器错过检查代码的机会，等到测试阶段才发现错误，找到并改正会更加困难  
 2.通过反射调用方法比直接调用方法要明显慢一些

**特别重申：**建议Java开发者不要使用Method对象的回调功能，使用接口进行回调会使代码的执行速度更快，更易于维护。    
### 8.通过反射了解泛型本质  
来看下面一段代码：  
```java
ArrayList list = new ArrayList();
ArrayList<String> list1 = new ArrayList<String>();
Class c1 = list.getClass();
Class c2 = list1.getClass();
System.out.println(c1 == c2); //true
/*反射的操作都是编译之后的操作，编译之后会发生类型擦除，即ArrayList<String>被擦除为ArrayList，所以c1 == c2结果为true*/
```
Java中集合的泛型，是防止错误输入的，只在编译阶段有效，编译之后就会发生类型擦除，所以绕过编译泛型就无效了
  
验证：我们可以通过方法的反射来操作，绕过编译  
```java
	try {
	Method m = c2.getMethod("add", Object.class);
	m.invoke(list1, 20);//绕过编译操作就绕过了泛型
	System.out.println(list1.size());
	System.out.println(list1);
	} catch (Exception e) {
		  e.printStackTrace();
    }
```
 推荐博客：  
* [Java核心技术点之反射](http://www.cnblogs.com/absfree/p/5277665.html)  
* [深入理解Java反射](http://www.cnblogs.com/luoxn28/p/5686794.html)
* [Java中的反射机制](http://blog.csdn.net/liujiahan629629/article/details/18013523)
* [Java反射教程](http://www.importnew.com/9078.html)
* [Java反射详解](http://www.cnblogs.com/rollenholt/archive/2011/09/02/2163758.html)