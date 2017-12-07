---
title: Java学习总结之Java IO系统
date: 2017-11-28 22:07:13
tags: Java
---
### 概述
Java.io 包几乎包含了所有操作输入、输出需要的类。所有这些流类代表了输入源和输出目标。Java.io 包中的流支持很多种格式，比如：基本类型、对象、本地化字符集等等。一个流可以理解为一个数据的序列。输入流表示从一个源读取数据，输出流表示向一个目标写数据。Java 为 I/O 提供了强大的而灵活的支持，使其更广泛地应用到文件传输和网络编程中。本节讲述最基本的和流与 I/O 相关的功能。  
### File  
JAVA中IO操作有相应步骤，以文件操作为例，主要操作流程如下：  
1.使用File类打开一个文件  
2.通过字节流或字符流的子类，指定输出的位置  
3.进行读/写操作  
4.关闭输入/输出  

那么我们先来介绍一下File类  
Java文件类以**抽象**的方式代表文件名和目录路径名。该类主要用于**文件和目录**的**创建**、文件的**查找**和文件的**删除**等。  
File对象代表磁盘中实际存在的文件和目录。通过以下构造方法创建一个File对象。  

通过给定的**父抽象路径名**和**子路径名字符串**创建一个新的File实例。  
`File(File parent, String child)`  

通过将给定路径名字符串转换成抽象路径名来创建一个新 File 实例。  
`File(String pathname)`  

根据 parent 路径名字符串和 child 路径名字符串创建一个新 File 实例。  
`File(String parent, String child)`  

通过将给定的 file: URI 转换成一个抽象路径名来创建一个新的 File 实例。  
`File(URI uri)`  

**注意：**在各个操作系统中，路径的分隔符是不一样的，例如：Windows中使用反斜杠："`\`"，Linux|Unix中使用正斜杠："`/`"。在使用反斜杠时要写成"`\\`"的形式，因为反斜杠要进行转义。如果要让Java保持可移植性，应该使用File类的静态常量File.pathSeparator。

创建File对象成功后，可以使用以下列表中的方法操作文件。  
![fail](Java学习总结之Java-IO系统/File1.png)
![fail](Java学习总结之Java-IO系统/File2.png)
![fail](Java学习总结之Java-IO系统/File3.png)
![fail](Java学习总结之Java-IO系统/File4.png)  

下面给出一个使用File类的实例：  
```java
import java.io.File;
public class DirList {
   public static void main(String args[]) {
      String dirname = "/java";
      File f1 = new File(dirname);
      if (f1.isDirectory()) {
         System.out.println( "Directory of " + dirname);
         String s[] = f1.list();
         for (int i=0; i < s.length; i++) {
            File f = new File(dirname + "/" + s[i]);
            if (f.isDirectory()) {
               System.out.println(s[i] + " is a directory");
            } else {
               System.out.println(s[i] + " is a file");
            }
         }
      } else {
         System.out.println(dirname + " is not a directory");
    }
  }
}
```
### Java中的目录  
**创建目录：**  
File类中有两个方法可以用来创建文件夹：
* mkdir( )方法创建一个文件夹，成功则返回true，失败则返回false。失败表明File对象指定的路径已经存在，或者由于整个路径还不存在，该文件夹不能被创建。
* mkdirs()方法创建一个文件夹和它的所有父文件夹。
下面的例子创建 "/tmp/user/java/bin"文件夹：  

```java
import java.io.File;
 
public class CreateDir {
  public static void main(String args[]) {
    String dirname = "/tmp/user/java/bin";
    File d = new File(dirname);
    // 现在创建目录
    d.mkdirs();
  }
}
```
**mkdirs是递归创建文件夹，允许在创建某文件夹时其父文件夹不存在,从而一同创建;mkdir必须满足路径上的父文件夹全都存在**  
**注意：** Java 在 UNIX 和 Windows 自动按约定分辨文件路径分隔符。如果你在 Windows 版本的 Java 中使用分隔符 (/) ，路径依然能够被正确解析。  
**读取目录：**  
一个目录其实就是一个 File 对象，它包含其他文件和文件夹。  
如果创建一个 File 对象并且它是一个目录，那么调用 isDirectory() 方法会返回 true。  
可以通过调用该对象上的 list() 方法，来提取它包含的文件和文件夹的列表。
下面展示的例子说明如何使用 list() 方法来检查一个文件夹中包含的内容：

```java
import java.io.File;
 
public class DirList {
  public static void main(String args[]) {
    String dirname = "/tmp";
    File f1 = new File(dirname);
    if (f1.isDirectory()) {
      System.out.println( "目录 " + dirname);
      String s[] = f1.list();
      for (int i=0; i < s.length; i++) {
        File f = new File(dirname + "/" + s[i]);
        if (f.isDirectory()) {
          System.out.println(s[i] + " 是一个目录");
        } else {
          System.out.println(s[i] + " 是一个文件");
        }
      }
    } else {
      System.out.println(dirname + " 不是一个目录");
    }
  }
}
```
**删除目录或文件：**  
删除文件可以使用 java.io.File.delete() 方法。
以下代码会删除目录/tmp/java/，即便目录不为空。
测试目录结构：  
```java
/tmp/java/
|-- 1.log
|-- test
```
deleteFolder是一个递归函数，类似于DFS思想
```java
import java.io.File;
 
public class DeleteFileDemo {
  public static void main(String args[]) {
      // 这里修改为自己的测试目录
    File folder = new File("/tmp/java/");
    deleteFolder(folder);
  }
 
  //删除文件及目录
  public static void deleteFolder(File folder) {
    File[] files = folder.listFiles();
        if(files!=null) { 
            for(File f: files) {
                if(f.isDirectory()) {
                    deleteFolder(f);
                } else {
                    f.delete();
                }
            }
        }
        folder.delete();
    }
}
```
### 流  
在Java程序中所有的数据都是以**流**的方式进行**传输或保存**的，程序需要数据的时候要使用**输入流**读取数据，而当程序需要将一些数据保存起来的时候，就要使用**输出流**完成。程序中的输入输出都是以流的形式保存的，流中保存的实际上全都是**字节文件**。流涉及的领域很广：标准输入输出，文件的操作，网络上的数据流，字符串流，对象流，zip文件流等等。 
![fail](Java学习总结之Java-IO系统/Stream.png)
流具有**方向性**，至于是输入流还是输出流则是一个相对的概念，一般以程序为参考，如果数据的流向是程序至设备，我们成为输出流，反之我们称为输入流。  
可以将流想象成一个“水流管道”，水流就在这管道中形成了，自然就出现了方向的概念。  
![fail](Java学习总结之Java-IO系统/Information.jpg)
先上一个**Java IO流类层次图**，如前所述，一个流被定义为一个数据序列。输入流用于从源读取数据，输出流用于向目标写数据： 
![fail](Java学习总结之Java-IO系统/JavaIO流类层次图.png "Java IO流类层次图")  
是不是被吓到了？没关系，我们将通过一个个例子来学习这些功能。  
### IO流分类  
#### 1.按操作数据类型分：字符流和字节流  
**字符流：**Java中的字符流处理的最基本的单元是**2字节的Unicode码元(char)**，它通常用来处理文本数据，如字符、字符数组或字符串等。所谓Unicode码元，也就是一个Unicode代码单元，范围是0x0000~0xFFFF。在以上范围内的每个数字都与一个字符相对应，Java中的String类型默认就把字符以Unicode规则编码而后存储在内存中。然而与存储在内存中不同，存储在磁盘上的数据通常有着各种各样的编码方式。使用不同的编码方式，相同的字符会有不同的二进制表示。实际上字符流是这样工作的：  
* 输出字符流：把要写入文件的字符序列(实际上是Unicode码元序列)转为指定编码方式下的字节序列，然后再写入到文件中。
* 输入字符流：把要读取的字节序列按指定编码方式解码为相应字符序列(实际上是Unicode码元序列从)从而可以存在内存中。 

也就是说，所有的文件在**硬盘**或在**传输**时都是以**字节**的方式进行的，包括图片等都是**按字节的方式存储**的，而字符是只有在**内存**中才会形成。 

**字节流：**Java中的字节流处理的最基本单位为**单个字节(byte)**，它通常用来处理二进制数据,如果要得到字节对应的字符需要强制类型转换。  

**两者比较：**  
1.字符流是由Java虚拟机将字节转化为2个字节的Unicode字符为单位的字符而成的，所以它对多国语言支持性较好，如果要操作中文数据等，用字符流。  
2.字符流只用来处理文本数据，字节流还可以用来处理媒体数据，如视频、音频、图片等。  
3.字符流的两个抽象基类为Reader和Writer，字节流的两个抽象基类为InputStream和OutputStream。它们的具体子类名以基类名为后缀进行扩展。  
4.字节流在操作的时候不会用到缓冲区(内存)，是直接对文件本身操作的，而字符流在操作的时候使用缓冲区。  
![fail](Java学习总结之Java-IO系统/Compare.jpg)

以向一个文件输出"Hello world!"为例，我们分别使用字节流和字符流进行输出，且在使用完之后都不关闭流。

使用字节流不关闭执行：
```java
import java.io.File;
import java.io.FileOutputStream;
import java.io.IOException;
import java.io.OutputStream;


public class IOPractice {

	public static void main(String[] args) throws IOException {
		// 第1步：使用File类找到一个文件    
		     File f = new File("/home/xiejunyu/"+
             "桌面/text.txt");   
		// 第2步：通过子类实例化父类对象     
	         OutputStream out = new FileOutputStream(f); 
		// 通过对象多态性进行实例化    
		// 第3步：进行写操作    
		     String str = "Hello World!";      
		// 准备一个字符串    
		     byte b[] = str.getBytes();          
		// 字符串转byte数组    
		     out.write(b);                      
		// 将内容输出    
		 // 第4步：关闭输出流    
		    // out.close();                  
		// 此时没有关闭    
		        }    
}
```

![fail](Java学习总结之Java-IO系统/1.png)
此时没有关闭字节流操作，但是文件中也依然存在了输出的内容，证明字节流是直接操作文件本身的。  

使用字符流不关闭执行：  
```java
import java.io.File;
import java.io.FileWriter;
import java.io.IOException;
import java.io.Writer;


public class IOPractice {

	public static void main(String[] args) throws IOException {
		 // 第1步：使用File类找到一个文件    
        File f = new File("/home/xiejunyu/桌面/test.txt");
        // 第2步：通过子类实例化父类对象            
        Writer  out = new FileWriter(f);            
        // 第3步：进行写操作    
        String str = "Hello World!";      
        // 准备一个字符串    
        out.write(str);                    
        // 将内容输出    
        // 第4步：关闭输出流    
        // out.close();
        // 此时没有关闭    
    }    
}
```
![fail](Java学习总结之Java-IO系统/2.png)

程序运行后会发现文件中没有任何内容，这是因为字符流操作时使用了缓冲区，而在关闭字符流时会强制性地将缓冲区中的内容进行输出，但是如果程序没有关闭字符流，缓冲区中的内容是无法输出的，所以得出结论：**字符流使用了缓冲区，而字节流没有使用缓冲区。**如果想让缓冲区中的内容输出，要么关闭流强制刷新缓冲区，要么调用flush方法冲刷缓冲区。可以简单地把缓冲区理解为一段特殊的内存。某些情况下，如果一个程序频繁地操作一个资源(如文件或数据库)，则性能会很低，此时为了提升性能，就可以将一部分数据暂时读入到内存的一块区域之中，以后直接从此区域中读取数据即可，因为读取内存速度会比较快，这样可以提升程序的性能。  
在字符流的操作中，所有的字符都是在内存中形成的，在输出前会将所有的内容暂时保存在内存之中，所以使用了缓冲区暂存数据。  

**建议：**  
1.虽然不关闭字节流不影响数据的输出，且后续JVM会自动回收这部分内存，但还是建议在使用完任何流对象之后关闭流。  
2.使用流对象都要声明或抛出IOException  
3.在创建一个文件时，如果目录下有同名文件将被覆盖  
4.在写文件时，如果文件不存在，会在创建输出流对象并绑定文件时自动创建文件，不必使用File的exists方法提前检测
4.在读取文件时，必须使用File的exists方法提前检测来保证该文件已存在，否则抛出FileNotFoundException

#### 2.按流向分：输入流和输出流  
输入流：程序从输入流读取数据源。数据源包括外界(键盘、文件、网络等)，即是将数据源读入到程序的通信通道。输入流主要包括两个抽象基类：InputStream(字节输入流)和Reader(字符输入流)及其扩展的具体子类。  
输出流：程序向输出流写入数据。将程序中的数据输出到外界（显示器、打印机、文件、网络等）的通信通道。输出流主要包括两个抽象基类：OutputStream(字节输出流)和Writer(字符输出流)及其扩展的具体子类。 

#### 3.按功能分：节点流和处理流
### 读取控制台输入  
在Java中，从控制台输入有三种方法：  
#### 1.使用标准输入流对象System.in  
System.in是System中内置的InputStream类对象，它的read方法一次只读入**一个字节**数据，返回0 ~ 255的一个int字节值,一般用来读取一个字符，需要强制类型转换为char类型，而我们通常要取得一个字符串或一组数字，故这种方法不常用。下面给出这种方法的一个例子：  
```java
public class CharTest{
public static void main(String[] args) {
		 try{   
		 System.out.print("Enter a Char:");   
		 char i = (char)System.in.read();   
		 System.out.println("Yout Enter Char is:" + i);  		  }   
		 catch(IOException e){   
		    e.printStackTrace();   
		 }   
	}
}
```
**使用这种方法必须提供try-catch块或者在main方法首部声明IOException异常**  
#### 2.使用Scanner类  
Scanner类功能十分强大，可以读入字符串、整数、浮点数、布尔类型值等等。下面是例子：  
```java
public class ScannerTest{
public static void main(String[] args){
    Scanner sc = new Scanner(System.in);   
    System.out.println("ScannerTest, Please Enter Name:");   
    String name = sc.nextLine();  //读取字符串型输入   
    System.out.println("ScannerTest, Please Enter Age:");   
    int age = sc.nextInt();    //读取整型输入   
    System.out.println("ScannerTest, Please Enter Salary:");   
    float salary = sc.nextFloat(); //读取float型输入   
    System.out.println("Your Information is as below:");   
    System.out.println("Name:" + name +"\n" + "Age:"+age 
    + "\n"+"Salary:"+salary);   
    }
 }   
```
**注意：**  
1.用nextXXX()读入XXX类型的数据，XXX可以是除了char外的所有基本数据类型，还可以是BigInteger或BigDecimal，其中凡是整型类型的数据还可以指定radix(进制)，可以用next()和nextLine()读取一个字符串或一行字符  
2.next()和nextLine()的区别：  
next()
* 一定要读取到有效字符后才可以结束输入。
* 对输入有效字符之前遇到的空白，next() 方法会自动将其去掉。
* 只有输入有效字符后才将其后面输入的空白作为分隔符或者结束符。
* next() 不能得到带有空格的字符串，除非用useDelimeter方法修改分隔符。  

nextLine()
* 以Enter为结束符,也就是说 nextLine()方法返回的是输入回车之前的所有字符。
* 可以获得空白。

3.可以用循环配合hasNextXXX方法判断输入是否继续  
4.Scanner类没有直接提供读取一个字符的方法，如果要读取一个字符，有三种方法，一是**读入一个字符串后取字符串的第一个字符**，二是使用**System.in的read方法**，三是使用**字符流**读入

更多Scanner的用法之前已经在[Java学习总结之Java基本程序设计结构](http://habitdiary.cn/2017/10/10/Java%E5%AD%A6%E4%B9%A0%E6%80%BB%E7%BB%93%E4%B9%8BJava%E5%9F%BA%E6%9C%AC%E7%A8%8B%E5%BA%8F%E8%AE%BE%E8%AE%A1%E7%BB%93%E6%9E%84/)中总结过了，不再赘述。
#### 3.使用BufferedReader对象  
可以把 System.in 包装在一个 BufferedReader 对象中来创建一个**字符流**。
下面是创建 BufferedReader 的基本语法：  
```java
BufferedReader br = new BufferedReader(new 
                      InputStreamReader(System.in));
```
其中，System.in是一个InputStream对象(字节流)，使用InputStreamReader作为桥梁，将字节流转换为字符流，然后再使用BufferedReader进行进一步包装。  
BufferedReader 对象创建后，我们便可以使用 read() 方法从控制台读取一个字符(读入一个用0~65535之间的整数表示的字符，需要强制类型转换为char类型，如果已到达流末尾，则返回 -1)，或者用 readLine() 方法读取一个字符串。下面是例子：  
```java
public static void main(String[] args){
//必须要处理java.io.IOException异常
  BufferedReader br = new BufferedReader(new InputStreamReader
  (System.in ));
  //java.io.InputStreamReader继承了Reader类
  String read = null;
  System.out.print("输入数据：");
  try {
   read = br.readLine();
  } catch (IOException e) {
   e.printStackTrace();
  }
  System.out.println("输入数据："+read);
 }
```
下面的程序示范了用 read() 方法从控制台不断读取字符直到用户输入 "q"。
```java
// 使用 BufferedReader 在控制台读取字符
 
import java.io.*;
 
public class BRRead {
  public static void main(String args[]) throws IOException
  {
    char c;
    // 使用 System.in 创建 BufferedReader 
    BufferedReader br = new BufferedReader(new 
                       InputStreamReader(System.in));
    System.out.println("输入字符, 按下 'q' 键退出。");
    // 读取字符
    do {
       c = (char) br.read();
       System.out.println(c);
    } while(c != 'q');
  }
}
```
下面的程序读取和显示字符行直到你输入了单词"end"。
```java
// 使用 BufferedReader 在控制台读取字符
import java.io.*;
public class BRReadLines {
  public static void main(String args[]) throws IOException
  {
    // 使用 System.in 创建 BufferedReader 
    BufferedReader br = new BufferedReader(new
                            InputStreamReader(System.in));
    String str;
    System.out.println("Enter lines of text.");
    System.out.println("Enter 'end' to quit.");
    do {
       str = br.readLine();
       System.out.println(str);
    } while(!str.equals("end"));
  }
}
```
**在ACM等算法竞赛中，我们常常也会使用Java，在输入数据时有以下几点注意：**  
1.hasXXX等价于C++中读到文件末尾(EOF)  
2.使用BufferedReader输入会比Scanner输入快十倍左右!
### 控制台输出
控制台的输出由 print() 和 println() 完成。这些方法都由类 PrintStream 定义，System.out 是该类的一个对象。
PrintStream 继承了 OutputStream类，并且实现了方法 write()。这样，write() 也可以用来往控制台写操作。
PrintStream 定义 write() 的最简单格式如下所示：
`void write(int byteval)`该方法将 byteval 的低八位字节写到流中,即System.out的write方法一次只能写一个字节(类比System.in的read方法一次只能读取一个字节)。
下面的例子用 write() 把字符 "A" 和紧跟着的换行符输出到屏幕：  
```java
import java.io.*;
 
// 演示 System.out.write().
public class WriteDemo {
   public static void main(String args[]) {
      int b; 
      b = 'A';//向上类型转换
      System.out.write(b);
      System.out.write('\n');
   }
}
```
**注意：write() 方法不经常使用，因为 print() 和 println() 方法用起来更为方便。**
### 字节流(OutputStream、InputStream)  
字节流主要是操作byte类型的数据，以byte数组为准，主要操作类是OutputStream、InputStream。  
由于文件读写最为常见，我们先讨论两个重要的字节流 FileInputStream(文件输入流) 和 FileOutputStream(文件输出流)，分别是抽象类InputStream和OutputStream的具体子类：

**FileInputStream**  
该流用于从文件读取数据，它的对象可以用关键字 new 来创建。  
有多种构造方法可用来创建对象。  
可以使用字符串类型的文件名来创建一个输入流对象来读取文件：  
```java
InputStream f = new FileInputStream("C:/java/hello");
```
也可以使用一个文件对象来创建一个输入流对象来读取文件。我们首先得使用 File() 方法来创建一个文件对象：  
```java
File f = new File("C:/java/hello");
InputStream out = new FileInputStream(f);
```
创建了InputStream对象，就可以使用下面的方法来读取流或者进行其他的流操作。
![fail](Java学习总结之Java-IO系统/InputStream.png "InputStream")
下面是一个例子：
```java
public static void main(String[] args) throws IOException{
	InputStream f  = new FileInputStream
	("/home/xiejunyu/桌面/test.txt");
	int c;
	while((c =  f.read()) != -1) 
    //这里也可以先用available方法得到可读的字节数
	System.out.println((char)c);
}
```
当我们需要创建一个byte[]来保存读取的字节时，如果数组太小，无法完整读入数据，如果太大又会造成内存浪费。**可以使用File类的length方法得到文件的数据字节数，从而有效确定byte数组的大小。**

**注意:** 每调用一次read方法,当前读取在文件中的位置就会向后移动一个字节，已经到文件末尾会返回-1，可以通过read方法返回-1判断是否读到文件末尾，也可以使用available方法返回下一次可以不受阻塞读取的字节数来读取。**FileInputStream不支持mark和reset方法进行重复读取。**  
  
**FileOutputStream**  
该类用来创建一个文件并向文件中写数据。  
**如果该流在打开文件进行输出前，目标文件不存在，那么该流会创建该文件。**  
有两个构造方法可以用来创建 FileOutputStream 对象。  
使用字符串类型的文件名来创建一个输出流对象：  
```java
OutputStream f = new FileOutputStream("C:/java/hello")
```
也可以使用一个文件对象来创建一个输出流来写文件。我们首先得使用File()方法来创建一个文件对象：  
```java
File f = new File("C:/java/hello");
OutputStream f = new FileOutputStream(f);
```
之前的所有操作中，如果重新执行程序，则肯定会覆盖文件中的已有内容，那么此时就可以通过FileOutputStream向文件中追加内容，FileOutputStream的另外一个构造方法：  
```java
public FileOutputStream(File file,boolean append) 
```
在构造方法中，如果将append的值设置为true，则表示在文件的末尾追加内容。程序代码如下： 
```java
File f = new File("C:/java/hello");
OutputStream f = new FileOutputStream(f,true);
```
创建OutputStream 对象完成后，就可以使用下面的方法来写入流或者进行其他的流操作。  
![fail](Java学习总结之Java-IO系统/FileOutputStream.png "FileOutputStream")  
当有一个字符串时，可以用getBytes方法转为byte数组用于字节流的输出。  

下面是一个演示 InputStream 和 OutputStream 用法的例子： 
```java
import java.io.*;
 
public class FileStreamTest{
  public static void main(String args[]){
    try{
      byte bWrite [] = {'A','B','C'};
      OutputStream os = new FileOutputStream("/home/xiejunyu/桌面/test.txt");
      for(int x=0; x < bWrite.length ; x++){
      os.write(bWrite[x] ); // writes the bytes
    }
    os.close();
 
    InputStream is = new FileInputStream("/home/xiejunyu/桌面/test.txt");
    int size = is.available();
 
    for(int i=0; i< size; i++){
      System.out.print((char)is.read() + "  ");
    }
      is.close();
    }catch(IOException e){
      System.out.print("Exception");
    }  
  }
}
```
上面的程序首先创建文件test.txt，并把给定的数字以二进制形式写进该文件，同时输出到控制台上。  
以上代码由于是二进制写入，可能存在乱码，你可以使用以下代码实例来解决乱码问题：
```java
import java.io.*;
 
public class fileStreamTest2{
  public static void main(String[] args) throws IOException {
    
    File f = new File("a.txt");
    FileOutputStream fop = new FileOutputStream(f);
    // 构建FileOutputStream对象,文件不存在会自动新建
    
    OutputStreamWriter writer = new OutputStreamWriter(fop, "UTF-8");
    // 构建OutputStreamWriter对象,参数可以指定编码,默认为操作系统默认编码,windows上是gbk
    
    writer.append("中文输入");
    // 写入到缓冲区
    
    writer.append("\r\n");
    //换行
    
    writer.append("English");
    // 刷新缓冲区,写入到文件,如果下面已经没有写入的内容了,直接close也会写入
    
    writer.close();
    //关闭写入流,同时会把缓冲区内容写入文件,所以上面的注释掉
    
    fop.close();
    // 关闭输出流,释放系统资源
 
    FileInputStream fip = new FileInputStream(f);
    // 构建FileInputStream对象
    
    InputStreamReader reader = new InputStreamReader(fip, "UTF-8");
    // 构建InputStreamReader对象,编码与写入相同
 
    StringBuffer sb = new StringBuffer();
    while (reader.ready()) {
      sb.append((char) reader.read());
      // 转成char加到StringBuffer对象中
    }
    System.out.println(sb.toString());
    reader.close();
    // 关闭读取流
    
    fip.close();
    // 关闭输入流,释放系统资源
 
  }
}
```
以上例子证明：在对多国语言的支持上，字符流表现更优，此时应使用字符流而不是字节流。  

### 字符流(Writer、Reader)  
Java提供了两个操作字符的字符流基类，分别是Writer和Reader。先来了解两个用于读写文件的字符流FileReader(字符输入流)和FileWriter(字符输出流)：  

**FileReader**  
FileReader类从InputStreamReader类继承而来。该类按字符读取流中数据。可以通过以下几种构造方法创建需要的对象。  
在给定从中读取数据的 File 的情况下创建一个新 FileReader。
```java
FileReader(File file)
```
在给定从中读取数据的 FileDescriptor 的情况下创建一个新 FileReader。  
```java
FileReader(FileDescriptor fd) 
```
在给定从中读取数据的文件名的情况下创建一个新 FileReader。  
```java
FileReader(String fileName) 
```
创建FIleReader对象成功后，可以参照以下列表里的方法操作文件。  
![fail](Java学习总结之Java-IO系统/FileReader.png "FileReader")  


**FileWriter**  
FileWriter 类从 OutputStreamWriter 类继承而来。该类按字符向流中写入数据。可以通过以下几种构造方法创建需要的对象。  
在给出 File 对象的情况下构造一个 FileWriter 对象。  
```java
FileWriter(File file)
```
在给出 File 对象的情况下构造一个 FileWriter 对象。
```java
 FileWriter(File file, boolean append)
```
构造与某个文件描述符相关联的 FileWriter 对象。
```java
FileWriter(FileDescriptor fd)
```
在给出文件名的情况下构造 FileWriter 对象，它具有指示是否挂起写入数据的 boolean 值。  
```java
FileWriter(String fileName, boolean append)
```
创建FileWriter对象成功后，可以参照以下列表里的方法操作文件。
![fail](Java学习总结之Java-IO系统/FileWriter.png "FileWriter")  
**字符流的操作比字节流操作方便一点，就是可以直接输出字符串。不在用再像之前那样进行字节转换操作了。使用字符流默认情况下依然是覆盖已有的文件，如果想追加的话，则直接在FileWriter上增加一个可追加的标记即可。**

下面的例子演示了FileReader和FileWriter的使用  
```java
import java.io.*;
public class FileRead{
   public static void main(String args[])throws IOException{
      File file = new File("Hello1.txt");
      // 创建文件
      file.createNewFile();
      // creates a FileWriter Object
      FileWriter writer = new FileWriter(file); 
      // 向文件写入内容
      writer.write("This\n is\n an\n example\n"); 
      writer.flush();
      writer.close();
      //创建 FileReader 对象
      FileReader fr = new FileReader(file); 
      char [] a = new char[50];
      fr.read(a); // 从数组中读取内容
      for(char c : a)
          System.out.print(c); // 一个个打印字符
      fr.close();
   }
}
```
### 字节-字符转换流(OutputStreamWriter、InputStreamReader)  
在整个IO包中，实际上就是分为字节流和字符流，但是除了这两个流之外，还存在了一组字节流-字符流的转换类。  

**InputStreamReader**  
InputStreamReader是字节流通向字符流的桥梁，它使用指定的charset读取字节并将其解码为字符。它拥有一个InputStream类型的变量，并继承了Reader，使用了对象的适配器模式，如图所示：  
![fail](Java学习总结之Java-IO系统/InputStreamReader.jpg) 
根据InputStream的实例创建InputStreamReader的方法有4种：  
1.根据默认字符集创建  
InputStreamReader(InputStream in)  
2.使用给定字符集创建   
InputStreamReader(InputStream in, Charset cs)  
3.使用给定字符集解码器创建  
InputStreamReader(InputStream in, CharsetDecoder dec)  
4.使用指定字符集名字符串创建   
InputStreamReader(InputStream in, String charsetName)

后面的3个构造函数都制定了一个字符集，最后一个是最简单的，可以直接指定字符集的名称来创建，例如UTF-8等。 

每次调用InputStreamReader中的一个read()方法都会导致从底层输入流读取一个或多个字节。要启用从字节到字符的有效转换，可以提前从底层流读取更多的字节，使其超过满足当前读取操作所需的字节。共有3个可用的read()方法：  

int read(); //读取单个字符  
int read(char[] cbuf, int offset, int length);          
//将字符读入数组中的某一部分  
boolean ready(); //判断此流是否已经准备好用于读取 

使用字符流的形式读取字节流的文件，代码如下：  
```java

import java.io.* ;
public class InputStreamReaderDemo01{
	public static void main(String args[]) throws Exception{
		File f = new File("d:" + File.separator + 
        "test.txt") ;	
		Reader reader = new InputStreamReader
        (new FileInputStream(f)) ;	
        // 将字节流变为字符流
		char c[] = new char[1024] ;
		int len = reader.read(c) ;	// 读取
		reader.close() ;	// 关闭
		System.out.println(new String(c,0,len)) ;
	}
};
```
**OutputStreamWriter**
OutputStreamWriter是**字符流**通向**字节流**的桥梁，可使用指定的charset将要写入流中的字符编码成字节。因此，它拥有一个OutputStream类型的变量，并继承了Writer，使用对象的适配器模式,如图所示：  
![fail](Java学习总结之Java-IO系统/OutputStreamWriter.jpg "OutputStreamWriter")  
根据OutputStream的实例创建OutputStreamWriter的方法有4种：  

1.根据默认字符集创建  
OutputStreamReader(OutputStream out)  

2.使用给定字符集创建  
OutputStreamReader(OutputStream out, Charset cs)       

3.使用给定字符集解码器创建  
OutputStreamReader(OutputStream out, CharsetDecoder dec)  

4.使用指定字符集名字符串创建   
OutputStreamReader(OutputStream out, Stroutg charsetName)        

后面的3个构造函数都制定了一个字符集，最后一个是最简单的，可以直接指定字符集的名称来创建，例如UTF-8等。  

每次调用write()方法都会导致在给定字符（或字符集）上调用编码转换器。在写入底层输出流之前，得到的这些字节将在缓冲区中累积。可以指定此缓冲区的大小，不过，默认的缓冲区对多数用途来说已足够大。注意，传递给write()方法的字符没有缓冲。共有3个可用的write()方法：  
void write(char[] cbuf, int off, int len); //写入字符数组的某一部分  
void write(int c); //写入单个字符  
void write(String str, int off, int len); //写入字符串的某一部分 

例如：将字节的文件输出流，以字符的形式输出。代码如下：
```java
import java.io.* ;
public class OutputStreamWriterDemo01{
	public static void main(String args[]) throws Exception{	
		File f = new File("d:" + File.separator + "test.txt");
		Writer out = new OutputStreamWriter
        (new FileOutputStream(f)) ;	// 字节流变为字符流
	    out.write("hello world!!") ;	
           // 使用字符流输出
		out.close() ;
	}
};
```
**特别说明：**OutputStreamWriter是字符流到字节流的桥梁，这不表示OutputStreamWriter接收一个字符流并将其转换为字节流，恰恰相反，其接收的OutputStream是一个字节流，而它本身是一个字符流。**那为什么说它是字符流到字节流的桥梁呢？**  
我们以文件操作为例，之前已经提到，在内存中数据是以字符形式存在的，而在文件中数据是以字节形式保存的。所以在**内存**中的**字符数据**需要通过OutputStreamWriter变为**字节流**才能保存在文件之中，读取的时候需要将读入的**字节流**通过InputStreamReader变为**字符**流。  
但OutputStreamWriter和InputStreamReader都是字符流，也就是说，OutputStreamWriter以字符输出流形式操作了字节的输出流，但实际上还是以字节的形式输出。而InputStreamReader，虽然以字符输入流的形式操作，但实际上还是使用的字节流输入，也就是说，传输或者是从文件中读取数据的时候，文件中真正保存的数据永远是字节。
![fail](Java学习总结之Java-IO系统/change.png)  

**FileWriter和FileReader的说明**  
从JDK文档中可以知道FileOutputStream是OutputStream的直接子类，FileInputStream也是InputStream的直接子类，但是在字符流文件的两个操作类却有一些特殊，FileWriter并不直接是Writer的子类，而是转换流OutputStreamWriter的子类，而FileReader也不直接是Reader的子类，而是转换流InputStreamReader的子类，那么从这两个类的继承关系就可以清楚的发现，不管是是使用字节流还是字符流实际上最终都是以字节形式操作输出流的。

### 缓冲字符流(BufferedReader、BufferedWriter) 
**BufferedReader**  
BufferedReader是一个包装类，是为了提高读效率提供的，其可以接收一个Reader,然后用readLine()逐行读入字符流，直到遇到换行符为止（相当于反复调用Reader类对象的read()方法读入多个字符）。BufferedReader提供一个缓冲区，先将数据读入缓冲区，等缓冲区满了或者调用flush方法，才将缓冲区数据读入内存。如果没有缓冲，则Reader每次调用 read() 都会导致从文件中读取字节，并将其转换为字符后返回，而这是极其低效的。  
**因此，建议用 BufferedReader 包装所有其 read() 操作可能开销很高的 Reader（如 FileReader 和 InputStreamReader),如：**   

![fail](Java学习总结之Java-IO系统/Buffer.png)    
![fail](Java学习总结之Java-IO系统/BufferedReader1.png)  
![fail](Java学习总结之Java-IO系统/BufferedReader2.png) 

**BufferedWriter**  
同理建议用BfferedWriter包装所有其write()操作可能开销很高的Writer(如FileWriter和OutputStreamWriter)  
![fail](Java学习总结之Java-IO系统/BufferedWriter.png "BufferedWriter")    

### 打印流(PrintStream、PrintWriter)  
在整个IO包中，打印流是**输出信息最方便**的类，主要包含字节打印流(PrintStream)和字符打印流(PrintWriter)。打印流提供了非常方便的打印功能，可以打印任何的数据类型，例如：小数、整数、字符串等等。  
相较OutputStream在输出时的各种麻烦(比如要将String转为byte[]才能输出)打印流中可以方便地进行输出。  

**PrintStream**  
1、public PrintStream(File file) throws FileNotFoundException   
//构造方法 通过一个File对象实例化PrintStream类  

2、public PrintStream(OutputStream out)  
//构造方法 接收OutputStream对象，实例化PrintStream类  

3、public PrintStream printf(Locale l, String format, Object ...arg)  
//普通方法 根据指定的Locale进行格式化输出

4、public PrintStream printf(String format,Object ... arg)   
//普通方法 根据本地环境进行格式化输出

5、public void print(boolean b)   
//普通方法 此方法被重载很多次，输出任意数据

6、public void println(boolean b)   
//普通方法 此方法被重载很多次，输出任意数据后换行  

**打印流的好处：**在PrintStream中定义的构造方法中可以清楚的发现有一个构造方法可以直接接收OutputStream类的实例，这是因为与OutputStream相比起来，PrintStream可以更加方便的输出数据，这就好比将OutputStream重新包装了一下，使之输出更加方便。  

**PrintWriter**  
**构造方法**  

//使用指定文件创建不具有自动行刷新的新 PrintWriter
public PrintWriter(File file);

//创建具有指定文件和字符集且不带自动刷行新的新 PrintWriter
public PrintWriter(File file,String csn);

//根据现有的 OutputStream 创建不带自动行刷新的新PrintWriter
public PrintWriter(OutputStream out);

//通过现有的 OutputStream 创建新的 PrintWriter(具有自动行刷新)  
public PrintWriter(OutputStream out,boolean autoFlush);

//创建具有指定文件名称且不带自动行刷新的新PrintWriter  
public PrintWriter(String fileName);

//创建具有指定文件名称和字符集且不带自动行刷新的PrintWriter  
public PrintWriter(String fileName,String csn);

//创建新 PrintWriter(具有自动行刷新)  
public PrintWriter(Writer out,boolean autoFlush)   

**常用方法**  
//打印boolean值  
public void print(boolean b)  
//打印 boolean 值，然后终止该行  
public void println(boolean x)

//打印字符  
public void print(char c)  
//打印字符，然后终止该行  
public void println(char x)


//打印字符数组  
public void print(char[] s)  
//打印字符数组，然后终止该行  
public void println(char[] x)

//打印 double 精度浮点数  
public void print(double d)  
//打印 double 精度浮点数,然后终止该行  
public void println(double x)

//打印一个浮点数  
public void print(float f)  
//打印浮点数，然后终止该行  
public void println(float x)  


//打印整数  
public void print(int i)  
//打印整数，然后终止该行  
public void println(int x)

//打印 long 整数
public void print(long l)  
//打印 long 整数，然后终止该行  
public void println(long x)

//打印对象
public void print(Object obj)  
//打印 Object，然后终止该行
public void println(Object x)  

//打印字符串。如果参数为 null，则打印字符串 "null"  
public void print(String s)  
//打印 String，然后终止该行
public void println(String x)

//通过写入行分隔符字符串终止当前行  
public void println()

//使用指定格式字符串和参数将一个格式化字符串写入此 writer 中。如果启用自动刷新，则调用此方法将刷新输出缓冲区  
public PrintWriter format(Locale l,String format,Object... args)  

//使用指定格式字符串和参数将一个格式化字符串写入此 writer 中。如果启用自动刷新，则调用此方法将刷新输出缓冲区  
public PrintWriter format(String format,Object... args)  

//将指定字符添加到此 writer  
public PrintWriter append(char c)
//将指定的字符序列添加到此 writer  
public PrintWriter append(CharSequence csq)
//将指定字符序列的子序列添加到此 writer  
public PrintWriter append(CharSequence csq,int start,int end)

//写入字符数组  
public void write(char[] buf)
//写入字符数组的某一部分  
public void write(char[] buf,int off,int len)  
//写入单个字符  
public void write(int c)  
//写入字符串  
public void write(String s)  
//写入字符串的某一部分  
public void write(String s,int off,int len)

**提示：**由于BufferedWriter没有PrintWriter使用灵活，所以在实际的操作中，我们往往会使用**PrinterWriter/BufferedReader**这种组合。  

### 内存操作流(ByteArrayInputStream、ByteArrayOutputStream)  
之前的程序中，输出输入都是从文件中来的，当然，也可以将输出的位置设置在内存之上。此时就要使用ByteArrayInputStream(内存输入流)、ByteArrayOutputStream(内存输出流)来完成输入、输出的功能了。
ByteArrayInputStream的主要功能是完成将内容写入到内存之中，而ByteArrayOutputStream的主要功能是将内存中的数据输出。  
![fail](Java学习总结之Java-IO系统/test.png)  

**ByteArrayInputStream**  
字节数组输入流在内存中创建一个字节数组缓冲区，从输入流读取的数据保存在该字节数组缓冲区中。创建字节数组输入流对象有以下几种方式。  
接收字节数组作为参数创建：  
```java
ByteArrayInputStream bArray = 
new ByteArrayInputStream(byte [] a);
```
另一种创建方式是接收一个字节数组，和两个整型变量 off、len，off表示第一个读取的字节，len表示读取字节的长度,**即将字节数组中从off开始的len个字节读入该输入流**。
```java
ByteArrayInputStream bArray = new
ByteArrayInputStream(byte []a,int off,int len)
```
成功创建字节数组输入流对象后，可以参见以下列表中的方法，对流进行读操作或其他操作。  
![fail](Java学习总结之Java-IO系统/ByteArrayInputStream.png "ByteArrayInputStream")  

**ByteArrayOutputStream**   
字节数组输出流在内存中创建一个字节数组缓冲区，所有发送到输出流的数据保存在该字节数组缓冲区中。创建字节数组输出流对象有以下几种方式。  
下面的构造方法创建一个32字节（默认大小）的缓冲区。
```java
OutputStream bOut = new ByteArrayOutputStream();
```
另一个构造方法创建一个大小为n字节的缓冲区。
```java
OutputStream bOut = new ByteArrayOutputStream(int a)
```
成功创建字节数组输出流对象后，可以参见以下列表中的方法，对流进行写操作或其他操作。  
![fail](Java学习总结之Java-IO系统/ByteArrayOutputStream.png "ByteArrayOutputStream")   
下面的例子演示了ByteArrayInputStream 和 ByteArrayOutputStream的使用：  
```java
import java.io.*;
public class ByteStreamTest {
   public static void main(String args[])throws IOException {
      ByteArrayOutputStream bOutput = new ByteArrayOutputStream(12);
      while( bOutput.size()!= 10 ) {
         // 获取用户输入
         bOutput.write(System.in.read()); 
      }
      byte b [] = bOutput.toByteArray();
      System.out.println("Print the content");
      for(int x= 0 ; x < b.length; x++) {
         // 打印字符
         System.out.print((char)b[x]  + "   "); 
      }
      System.out.println("   ");
      int c;
      ByteArrayInputStream bInput = new ByteArrayInputStream(b);
      System.out.println("Converting characters to Upper case " );
      for(int y = 0 ; y < 1; y++ ) {
         while(( c= bInput.read())!= -1) {
            System.out.println(Character.toUpperCase((char)c));
         }
         bInput.reset(); 
      }
   }
}
```

### 数据操作流(DataInputStream、DataOutputStream)
**DataInputStream**  
数据输入流允许应用程序以与机器无关方式从底层输入流中读取基本 Java 数据类型。
下面的构造方法用来创建数据输入流对象。  
```java
DataInputStream dis = new DataInputStream(InputStream in);
```
另一种创建方式是接收一个字节数组，和两个整形变量 off、len，off表示第一个读取的字节，len表示读取字节的长度。  
```java
DataInputStream dis = new DataInputStream(byte[] a,int off,int len);
```
![fail](Java学习总结之Java-IO系统/DataInputStream.png "DataInputStream")
**DataOutputStream**  
数据输出流允许应用程序以与机器无关方式将Java基本数据类型写到底层输出流。
下面的构造方法用来创建数据输出流对象。
```java
DataOutputStream out = new DataOutputStream(OutputStream  out);
```
创建对象成功后，可以参照以下列表给出的方法，对流进行写操作或者其他操作。  
![fail](Java学习总结之Java-IO系统/DataOutputStream.png "DataOutputStream")  
下面的例子演示了DataInputStream和DataOutputStream的使用，该例从文本文件test.txt中读取5行，并转换成大写字母，最后保存在另一个文件test1.txt中。
```java
import java.io.*;

public class Test{
   public static void main(String args[])throws IOException{

      DataInputStream d = new DataInputStream(new
                               FileInputStream("test.txt"));

      DataOutputStream out = new DataOutputStream(new
                               FileOutputStream("test1.txt"));

      String count;
      while((count = d.readLine()) != null){
          String u = count.toUpperCase();
          System.out.println(u);
          out.writeBytes(u + "  ,");
      }
      d.close();
      out.close();
   }
}
```
### 选择合适的IO流  
1.首先，明确IO流中有两个主要的体系，即  InputStream、OutputStream和Reader、Writer。其次，明确数据的来源和数据将要到达的目的地。  

2.明确将要操作的数据是否是纯文本数据。如果数据源是纯文本数据选Reader;数据源不是纯文本数据选择InputStream。如果数据目的地是纯文本数据就选择Writer;如果不是则选择OutputStream。  

3.明确具体的设备。即数据源是从哪个设备来的：是硬盘就加File;是键盘用System.in(是一个InputStream对象);是内存用数组;是网络用Socket流。同样目的是哪个设备：是硬盘就加File;是键盘用System.out(是一个PrintStream对象);是内存用数组;是网络用Socket流。    

4.明确是否还需要其他额外功能呢，例如：  
①是否需要较高的效率，即是否需要使用缓冲区，是就加上Buffered;  
②是否需要转换，是就使用转换流，InputStreamReader 和OutputStreamWriter。  

例子：  
![fail](Java学习总结之Java-IO系统/1.jpg)  
![fail](Java学习总结之Java-IO系统/2.jpg)   

### System类对IO的支持(out、err、in)  
System类的常量  
System表示系统类，实际上在Java中也对IO给予了一定的支持  
1、public static final PrintStream out     
//常量  对应系统标准输出，一般是显示器  
2、public static final PrintStream err  
//常量 错误信息输出  
3、public static final InputStream in    
//常量 对应标准输出，一般是键盘

使用static final关键字声明的变量是全局常量，只要是常量，则所有的单词字母必须全部大写，按照现在的标准：  
System.OUT —> System.out