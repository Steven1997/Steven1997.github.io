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
我们知道使用javac能够将.java文件编译为.class文件，这个.class文件包含了我们对类的原始定义信息（父类、接口、构造器、属性、方法等）。.class文件在运行时会被ClassLoader加载到JVM中，当一个.class文件被加载后，JVM会为之生成一个Class对象，我们在程序中通过new实例化的对象实际上是在运行时根据相应的Class对象构造出来的。确切的说，这个Class对象实际上是`java.lang.Class<T>`泛型类的一个实例，比如`Class<MyClass>`对象即为一个封装了MyClass类的定义信息的`Class<T>`实例，从中我们可以得出结论：万物皆对象，`任何类型(包括基本类型和引用类型).class`都是`java.lang.Class<T>`的实例，简言之，class对象是Class泛型类的实例，它代表了一个类型。由于`java.lang.Class<T>`类不存在公有构造器，因此我们不能直接实例化这个类，我们可以通过以下方法获取一个Class对象。  
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
  Class<People> peopleClass = People.class;
```
 
2) 如果已知类的**全限定名称(包含包名)**，可以通过Class的forName静态方法得到类的Class对象，如：  

```java
Class<People> peopleClass = Class.forName("cn.habitdiary.People");
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
Class<People> peopleClass = people.getClass();
```

`实例对象.getClass().getName()` 可以获取当前对象的类的全限定名称(包含包名)  
`实例对象.getClass().getSimpleName()`可以获取当前对象的类的底层类简称(不含包名)  
`实例对象.getClass().getCanonicalName()`大部分情况和getName()相同，但在表示数组或内部类时有所区别，比如对于String数组，getName返回的是[Ljava.lang.String之类的表现形式，而getCanonicalName返回的就是跟我们声明类似的形式。  
但在类加载的时候需要的是getName得到的那样的名字，而在根据类名字创建文件的时候最好使用getCanonicalName()  

**注意：  
1.一个Class对象实际上表现的是一个类型，而这个类型未必一定是一种类。例如，int不是类，但int.class是一个Class对象  
2.Class类是一个泛型类，但有时候我们不能提前确定class对象的类型,就可以用`Class<?>`来代替，即上面代码中的`Class<People> peopleClass`可以写成`Class<?> peopleClass`  
3.虚拟机为每个类型管理一个Class对象，可以用 == 运算符实现两个类对象比较的操作  
4.newInstance()方法可以返回一个Class对象对应类的新实例(返回值类型是Object)，比如:**

```java
String s = "java.util.Random";
Object m = Class.forName(s).newInstance();
```

**如果希望给构造器提供参数，就不能使用这种写法，而必须使用Constructor类中的newInstance方法。**  

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

**提示：**getFields、getMethods和getConstructors方法将分别返回类提供的public域、方法和构造器数组，其中包括超类的公有成员;而getDeclaredFields、getDeclaredMethods和getDeclaredConstructors方法将分别返回类中声明的全部域、方法和构造器，其中包括私有、受保护和默认成员，但不包括超类的成员。getSupperClass()返回class对象对应类的超类的class对象，没有显式继承的类的超类是Object。getInterfaces返回class对象对应类的所有接口的class对象

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

