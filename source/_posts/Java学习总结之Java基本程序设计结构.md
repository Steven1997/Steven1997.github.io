---
title: Java学习总结之Java基本程序设计结构
date: 2017-10-10 23:55:53
tags:
---
### 数据类型  
Java是一种**强类型**语言，即意味着必须为每一个变量声明一种类型。在Java中一共有8种基本数据类型，其中有4种整型、2种浮点类型、1种用于表示Unicode编码的字符单元的字符类型char和一种用于表示真值的boolean类型。  
#### 1) 整型  
整型用于表示没有小数部分的值，它允许是负数，Java提供了4种整型：  
类型　　　　存储需求　　　　　　　　取值范围  
byte　　　　 1字节　　　　　　　　　-2<sup>7</sup> ~ 2<sup>7</sup>-1  
short　　　　2字节　　　　　　　　　-2<sup>15</sup> ~ 2<sup>15</sup>-1  
int　　　　　4字节　　　　　　　　　-2<sup>31</sup> ~ 2<sup>31</sup>-1  
long　　　　 8字节　　　　　　　　　-2<sup>63</sup> ~ 2<sup>63</sup>-1  

在通常情况下,int类型最常用。如果要表示星球上居住的人数，就需要使用long类型了。byte和short类型用于特定的应用场合，比如底层的文件处理或者需要控制占用存储空间量的大数组。  
整型的直接量默认是int，即2表示int类型的2，为了表示一个long型的整型直接量，在其后追加字母L或l即可(建议使用L,以免和数字1混淆)。语句`byte b = 128`会造成编译错误，因为128是整型直接量且超过了byte的取值范围。  
默认情况下，整型直接量是一个十进制整数。要表示一个二进制整数直接量，使用0b或0B开头;表示一个八进制整数直接量，使用0开头;表示一个十六进制整数直接量，使用0x或0X开头。  
为了提高可读性,Java允许在数值直接量的两个数字之间使用下划线,例如`long ssn = 232_45_4519`  
**Java没有任何无符号(unsigned)形式的int、long、short或byte类型**
#### 2）浮点类型  
浮点类型用于表示有小数部分的数值，Java提供了2种浮点类型：  
类型　　　　存储需求　　　　　　　　取值范围  
float　　　　 4字节　　负数范围：-3.4028235E+38 ~ -1.4E-45     
　　　　　　　　　　　正数范围：  1.4E-45 ~ 3.4028235E+38  
           　　　　　　　　　　　　　　　　(有效位数为6~7位)  
double 　　　8字节　　负数范围：-1.7976931348623157E+308 　　　　　　　　　　　　　　　　~ -4.9E-324　　　
　   
      　　　　　　　　　　　正数范围： 4.9E-324 ~   
 　　　　　　　　　　　　　　　　1.7976931348623157E+308
　　　　　　　　　　　　　　　　(有效位数为15位)  
double表示这种类型的数值精度是float的两倍,float称为单精度型,double称为双精度型。通常情况下,应该使用double型,因为它比float型更精确。  
可以以E或e为指数用科学计数法表示浮点数,例如1.2345E+2,在十六进制中,则使用p表示指数而不是e，且尾数采用十六进制时指数的基数是2而不是10  
浮点数直接量是double型,即5.0被认为是double型的,可以通过追加f或F表示float型直接量,当然也可以在浮点数值后面添加后缀D或d表示double型(虽然没有必要多此一举)  
常量**Double.POSITIVE_INFINITY、Double.NEGATIVE_INFINITY和Double.NaN**(以及相应的Float类型的常量)分别表示正无穷大、负无穷大和不是一个数。例如，一个正整数除以0的结果为正无穷大。计算0/0或者负数的平方根结果为NaN。  
不能用如下的方式检测一个特定的值是否等于Double.NaN:  
```java
if(x == Double.NaN) // is never true
```
应该改成  
```java
if(Double.isNaN(x)) // check whether x is "not a number"
```
浮点数在运算时会产生一定误差,如果对精度有极高要求，应该使用BigDecimal类
#### 3）Unicode和char类型  
char类型原本用于表示单个字符。不过，现在情况已经有所变化。如今，有些Unicode字符可以用一个char值描述，另外一些则需要两个char值。  
char类型的字面量值要用单引号括起来。例如：'A'是编码值为65所对应的字符常量。它与"A"不同，"A"是包含一个字符A的字符串。char类型可以表示为十六进制值，范围从'\u0000'到'\uFFFF'  
有一些用于表示特殊字符的**转义序列**,如下：  
转移序列 　　　　　名称 　　　　　　　Unicode值  
　\b　　　　　　　 　退格　　　　　　　  \u0008　　  
　\t　　　　　　　　制表　　　　　　　\u0009  
 　\n　　　　　　　　换行　　　　　　　\u000a  
 　 \r　　　　　　　　回车　　　　　　　\u000d  
   　\"　　　　　　　　双引号   　　　　　　\u0022  
   　 \'　　　　　　　　单引号　　　　　　\u0027　　　　
  　　　 　\\\　　　　　　　　反斜杠　　　　　　　\u005c　　  
       
 **注释中的\u也会起转义作用,如下：**  
 ```java
 // \u00A0 is a newline 
   (\u00A0会被替换成换行符)
 
 // Look inside c:\users 
   (会产生编译错误,因为\u后面未跟着4个十六进制数)
 ```
#### 4) boolean类型  
boolean类型有两个值：false和true，用来判定逻辑条件。**不同于C/C++中0代表false，非0代表true，Java中整型值和布尔值之间不能进行相互转换。**  
比如,下面的语句在C/C++中是允许的,表示一个无限循环：  
```C++
while(1){
	do something
}
```
但是在Java中不允许这么做,可以改为：  
```java
while(true){
	do something
}
```
可以打印boolean类型的变量或者布尔表达式，会呈现true或者false  
### 数据类型转换 
将一个小范围类型变量转化为大范围类型的变量称为**拓宽类型**，反之称为**缩窄类型**。把小范围类型变量赋值给大范围类型的变量或小范围类型变量和大范围类型变量共同参与运算,Java会自动拓宽类型。而如果要缩窄类型，必须显式完成。  
**注意：  
1.类型转换不改变被转换的变量，例如，下面的代码中的d在类型转换之后值不变：**
```java
double d = 4.5;
int i = (int)d; // i becomes 4, but d is still 4.5
```
**2.x1 op= x2形式的增强赋值表达式的执行为x1 = (T)(x1 op x2),T是x1的类型。因此，下面代码是正确的：**  
```java
int sum = 0;
sum += 4.5;// 等价于sum = (int)(sum + 4.5)
```
**3.将一个int型变量赋值给short型或byte型的变量，必须显式地使用类型转换，如下面的语句会产生编译错误：**  
```java
int i = 1;
byte b = i; // Error because explicit casting is required
```
然而如果整型直接量在目标变量允许的范围内，那么将整型直接量赋给short型或byte型就不需要显式类型转换，如：  
```java
byte b = 1; 
```
### 变量  
变量用于表示在程序中可能被改变的值。**变量声明**告知编译器根据数据类型为变量分配合适的内存空间。变量声明的语法如下：
```java
datatype variableName;
```
如果几个变量为同一类型,允许一起声明它们：  
```java
datatype variable1, variable2, ..., variablen;
```
可以在声明变量的同时初始化，也可以先声明后初始化：  
```java
int count = 1; // 声明同时初始化变量

int count;
count = 1; //先声明，后初始化
```
### 常量  
常量是一个赋值后保持不变的量,用final关键字修饰，在类中定义的常量一般用static final修饰。常量必须在同一条语句中声明和赋值，不可先声明再赋值。  
使用常量有三个好处：  
1)不必重复输入同一个值  
2)如果必须修改常量的值，只需在源代码的一处改动  
3)给常量赋一个描述性的名字会提高程序的易读性

  
### 数值操作符  
数值数据类型的操作符包括标准的算术操作符：加号(+)、减号(-)、乘号(`*`)、除号( / )、求余号(%)  
**注意：  
1.只有当被除数是负数时,余数才是负的,如 -7 % 3 = -1, -26 % -8 = -2,20 % -13 = 7,即无论正负,商和除数乘积的绝对值不能超过被除数的绝对值  
2.整数除0会产生一个异常，而浮点数除0将会得到无穷大或NaN结果**  

### 增强赋值操作符  
x1 op= x2形式的增强赋值表达式的执行为x1 = (T)(x1 op x2),T是x1的类型。其中op可以是加号(+)、减号(-)、乘号(`*`)、除号( / )、求余号(%)  
### 自增和自减操作符  
分为++var、--var、var++、var--四种，加减号决定**给var加1还是减1**，前置还是后置符号决定**先更新后使用值还是先使用值后更新**
### 关系运算符  
Java中有一系列用于比较关系的运算符：==(等于)、!=(不等于)、<(小于)、>(大于)、<=(小于等于)、>=(大于等于)  
### 逻辑操作符 
逻辑操作符!(非)、&&(与)、||(或)和^(异或)可以用于产生复合型布尔表达式，逻辑操作符被称为**短路操作符或懒惰操作符**，即按顺序执行表达式，如果某一步已经可以判断整个表达式的真假，不再继续执行，否则继续执行。  
### 位运算符  
处理整型类型时，可以直接对组成整型数值的各个位完成操作。位运算符包括：&(按位与)、|(按位或)、^(按位异或)、~(按位取反)、<<(左移，高位舍弃，低位补0)、>>(右移，低位舍弃，高位用符号位填充)、>>>(右移，低位舍弃，高位0填充)  
**注意：  
1) 移位运算符的右操作数要完成模32的运算(除非左操作数是long类型，在这种情况下右操作数要模64),例如1`<<`35等价于1`<<`3  
2) & 和 | 也可以用在布尔表达式中，此时它们是逻辑操作符，但不会短路  
3) 可以使用掩码技术得到一个数二进制的某一位：  
int fourthBitFromRight = (n & 0b1000) / 0b1000  
上式将n的右数第4位保留，将其他位掩掉，再除以0b1000，就得到了该位上的数** 
### 选择  
Java中的选择语句类型有：单分支if语句、双分支if-else语句、嵌套if语句、多分支if-else语句、switch语句、条件表达式
#### 1) 单分支if语句  
```java
if(布尔表达式){
	语句(组);
 }```
 分支中如果只有单条语句可以省略外围花括号，否则不能省略
#### 2) 双分支if-else语句  
```java
if(布尔表达式){
	布尔表达式为真时执行的语句(组);
 }
 else{
    布尔表达式为假时执行的语句(组);
 }```
 嵌套层数没有限制，但为了程序可读性，尽量不要进行过多的嵌套，且嵌套语句应有适当的缩进保持层次关系。
#### 3) 嵌套的if语句和多分支的if-else语句  
下面是一个嵌套的if语句，if(j > k)嵌套在语句if(i > k)内  
```java
if(i > k) {
	if(j > k)
    	System.out.println("i and j greater than k");
}
else
	System.out.println("i is less than or equal to k");
```
下面是一个多分支的if-else语句  
```java
if(score >= 90.0)
	System.out.print("A");
else{
	if(score >= 80.0)
      System.out.print("B");
    else{
    	if(score >= 70.0)  
          System.out.print("C");
        else{
        	if(score >= 60.0)
             System.out.print("D");
            else
             system.out.print("E");
        }
    }
}
```
为了可读性，建议改写成如下形式：  
```java
if(score >= 90.0)
	System.out.print("A");
else if(score >= 80.0)  
	System.out.print("B");
else if(score >= 70.0)  
	System.out.print("C");
else if(score >= 60.0)
	System.out.print("D");
else
	System.out.print("E");
```
**注意：只有在前面的所有条件都为false才测试下一个条件** 
#### 4) switch语句  
switch语句常用于多分支的选择问题：  
```java
switch(switch表达式){
	case value1: 语句(组)1;
    			 break;
    case value2: 语句(组)2;
    			 break;
    ...
    case valueN: 语句(组)N;
    			 break;
    default: 当没有一个给出的case与switch表达式匹配时执行的语句(组)
}
```
**注意：  
1) switch表达式可以为char、byte、short、int或者String型值(整型中只有long不行),并且总要用括号括住  
2) value1,...,valueN必须与switch表达式具有相同的数据类型，且是常量表达式，不能出现变量，例如：x、y+1等  
3) 当switch表达式的值与case语句匹配时,执行从该case开始的语句，直到遇到一个break语句或到达switch语句的结束。break语句是可选的,会立即终止switch语句  
4) default语句是可选的，当没有一个给出的case与switch表达式匹配时执行default语句指定的操作，如果加default语句，就什么也不做**
#### 5) 条件表达式  
条件表达式基于一个条件计算表达式的值，它是Java中唯一的三元操作符。  
```java
boolean-expression ? expression1 : expression2;
//(布尔表达式? 表达式1:表达式2)
```
如果布尔表达式的值为true，则条件表达式的结果为表达式1;否则，结果为表达式2。  
例如：  
```java
System.out.println((num % 2 == 0) ? "num is even" : "num is odd");
```
注意冒号两边是**表达式**，不能是其他操作。



### 循环
#### 1) 块作用域  
块(即复合语句)是指由一对大括号括起来的若干条简单的Java语句。**块确定了变量的作用域，变量只在包含自己的块中可见。一个块可以嵌套在另一个块中。**例如：  
```java
public static void main(String[] args)
{
	int n;
    . . .
    {
       int k;
       . . .
    } // k is only defined up to here
}
```
但是不能在嵌套的两个块中声明同名变量，例如下面的语句会产生编译错误：  
```java
public static void main(String[] args)
{
	int n;
    . . .
    {
       int k;
       int n;//Erro -- can't redefine n in inner block
       . . .
    } 
}
```
这与C++不同，在C++中允许在嵌套的块中定义同名变量，内层变量会覆盖外层定义的变量。  
#### 2) while循环  
while循环在条件为真的情况下，重复地执行语句：  
```java
while(循环继续条件){
	//循环体  
    语句(组);
}
```
当程序不幸出现了死循环，如果是从命令窗口运行程序的，按CTRL+C键来结束  
#### 3) do-while循环
do-while循环和while循环基本一样，不同的是while循环是前测循环，即先检测循环条件后执行循环体;do-while是后测循环，即先执行循环体后检测循环条件。只有在一开始就不满足循环继续条件时，两种循环的执行次数不同，即while循环不执行，do-while循环执行一次。否则两种循环的执行次数相同。
```java
do{
	// 循环体;
    语句(组);
}while(循环继续条件);
```
**注意do-while循环后面的分号不要丢！**  
#### 4) for循环
for循环的基本语法为：
```java
for(初始操作;循环继续条件;每次迭代后的操作) {
	//循环体  
    语句(组);
}
```
初始动作可以是0个或是多个以逗号隔开的变量声明语句或赋值表达式：  
```java
int i = 0;
for(    ;i < 100;i++){
	System.out.println("Welcome to Java!");
}// 将初始动作省略，放在循环语句之前执行

for(int m = 0,n = 0; m + n < 10; m++,n++){
	System.out.println("Welcome to Java!");
}// 多个初始化动作
```
每次迭代后的操作也可以是0个或多个逗号隔开的语句：  
```java
for(int i = 0;i < 100;  ){
	System.out.println("Welcome to Java!");
    i++;
}// 将每次迭代后的操作放入循环体内  

for(int i = 1;i < 100;System.out.println(i),i++);
// 每次迭代后执行多个操作
```
下面的三种循环写法是等价的：  
```java
for(  ;  ;  ){
	//do something
}

for(  ;true;  ){
	//do something
}

while(true){
	//do something
}
```
#### 5) break和continue  
**continue跳出本次迭代进入下一次迭代，break跳出整个循环。**  
虽然不提倡使用goto语句，但偶尔使用goto语句跳出循环还是有益处的。Java中提供了类似于C++中goto语句的带标签的break语句和continue语句，用于跳出多重嵌套的循环语句。  
**标签必须放在希望跳出的最外层循环之前，并且必须紧跟一个冒号。另外要注意，只能跳出语句块，而不能跳入语句块。**  
下面的代码使用了带标签的break语句：  
```java
 int i;   
 label1:
 for( i = 0;i < 10;i ++) {
	if(i == 5)
	break label1;
 }
 System.out.println(i);// i = 5
```
上述代码中，i在等于5时跳出循环到循环首部的标签label1位置，由于是带标签的break语句，不再重新进入循环，而是跳过循环执行下面的打印语句。

如果把改成带标签的continue语句：  
```java
 int i;   
 label2:
 for( i = 0;i < 10;i ++) {
		if(i == 5)
		continue  label2;
 }
 System.out.println(i); // i = 10
```
上述代码中，i在等于5时也跳出循环到循环首部的标签label2位置，但由于是带标签的continue语句，会重新进入循环。
### 枚举类型    
### 输入输出
#### 1) 输入
从控制台读取"标准输入流"System.in，需要构造一个**Scanner**对象，并与"标准输入流"System.in关联。  
```java
Scanner in = new Scanner(System.in);
```
下面给出Scanner类的常用API：  
* Scanner(InputStream in)  
 用给定的输入流创建一个Scanner对象  
*  String nextLine()  
  读取输入的下一行内容(以回车作为分隔符)  
*  String next()   
   读取输入的下一个单词(以空白字符如空格、回车作为分割符)  
*  int nextInt()  
读取下一个int  
*  double nextDouble()  
读取下一个double()  
* boolean hasNext()    
检测输入是否还有单词  
* boolean hasNextInt()  
检测输入是否还有int  
* boolean hasDouble()  
检测输入是否还有double  

因为输入是可见的，所以Scanner类不使用于从控制台读取密码。Java SE 6特别引入了Console类实现不回显的输入。要想读取一个密码，可以采用下列代码：  
```java
Console cons = System.console();
String username = cons.readLine("User name: ");
char[] passwd = cons.readPassword("Password: ");
```
**几点注意：  
1.Console类不能用new构造对象，属于单例模式，构造方法被private修饰  
2.为了安全起见，返回的密码存放在一维字符数组中，而不是字符串中。在对密码进行处理后，应该马上用一个填充值覆盖数组元素。  
3.采用Console对象处理输入不如Scanner方便，每次只能读取一行输入，而没有能够读取一个单词或一个数值的方法**  

下面给出Console类的常用API：  
* static Console console()   
  返回一个Console对象  
* static char[] readPassword(String prompt, Object...args)  
显示提示字符串prompt并读取用户输入，直到输入行结束,结果存放在字符数组中。args参数用来提供输入格式。  
* static String readLine(String prompt,Object...args)  
显示提示字符串prompt并读取用户输入，直到输入行结束，结果存放在字符串中。args参数用来提供输入格式。  

  
#### 2) 输出  

#### 3) 重定向语法
Java使用System.out(系统类的输出流对象)来表示标准输出设备，默认情况下是显示器，而用System.in(系统类的输入流对象)来表示标准输入设备，默认情况下是键盘。  
有时，我们需要从文件而不是键盘读入数据，并希望将程序输出结果保存到文件中而不是打印在控制台上。此时，可以在命令行利用Shell的重定向语法将任意文件关联到System.in和System.out，例如下面的语句实现了从input.txt文件输入并将程序输出结果输入output.txt：  
```
java MyProg < input.txt > output.txt
```
### 方法  
#### 1) 定义方法  
下面先来看一个方法的定义，其作用是返回两个整数中的较大者：
```java
public static int max(int num1,int num2){
	int result;
    if(num1 > num2)
    	result = num1;
    else
    	result = num2;
    return result;
}
```
其中花括号前的部分是**方法头**，花括号包括的部分是**方法体**。方法头包括**修饰符(public static)、返回值类型(int)、方法名(max)、形式参数(int num1、int num2)**。形式参数构成**参数列表**，方法名和参数列表构成**方法签名**。  
对带返回值的方法而言，return语句是必需的，且如果存在多个选择分支，要确保任何分支都有返回值。对于void方法，也可以在方法中使用return结束方法调用。
#### 2) 调用方法  
如调用上述max方法：  
```java
int z = max(x,y);
```
其中x,y是实际参数。

每当调用一个方法时，系统会创建一个**活动记录**(也称为活动框架)，用于保存方法中的参数和变量。活动记录置于一个内存区域中，称为**调用堆栈(call stack)**。调用堆栈也称为执行堆栈、运行时堆栈，或者一个机器堆栈，常简称为"堆栈"。当一个方法调用另一个方法时，调用者的活动记录保持不动，一个新的活动记录被创建用于被调用的新方法,一个新的记录"入栈"。一个方法结束返回到调用者时，相应的活动记录被释放，其中的参数和变量被销毁，栈顶的记录"出栈"。堆栈的最底层活动记录是main方法的记录，当整个程序结束，main方法记录"出栈",栈为空，程序结束。  

当调用带参数的方法时，如果参数是基本数据类型，实参的值传递给形参(即拷贝)，这个过程称为**按值传递**，无论形参在方法中是否改变，实参都不受影响;如果参数是引用类型，则传递的是引用值，可以理解为**传共享**，形参和实参指向同一对象，此时实际对象可能会被改变。  

main方法也有参数，是一个String数组，可以在命令行里给main方法传递字符串参数。例如，下面的命令行用三个字符串arg0、arg1、arg2启动程序TestMain：  
```
java TestMain arg0 arg1 arg2
```
其中arg0、arg1、arg2都是字符串，但是在命令行中出现时不需要双引号，但如果字符串包含空格，就必须用双引号括住。  
当调用main方法时，Java解释器会创建一个数组存储命令行参数，然后将该数组的引用传递给args。例如，如果调用有n个命令行参数的程序，Java解释器创建一个如下所示的数组：  
```java
args = new String[n];
```
然后Java解释器传递参数args去调用main方法  
**注意：如果命令行没有传递参数，那么使用new String[0]创建数组。在这种情况下args引用了一个长度为0的空数组，它的值不是null，但是args.length是0**
### 数组


### 零敲碎打  
* 一个Java源文件内只能有一个public类,且该类名称必须与文件名相同  
* main方法是程序的入口，JVM将从指定类中的main方法开始执行。根据Java语言规范，main方法必须声明为`public static void main(String[] args)`
* 在源代码中，字符串常量不能跨行，因此，下面的语句会造成编译错误：  
```java
System.out.println("Welcome to the
world of Java!");
```
为了改正错误，可以将该字符串分成几个单独的子串，然后再用连接符(+)将它们组合起来：  
```java
System.out.println("Welcome to the“ +
”world of Java!");
```