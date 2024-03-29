---
layout: post
categories: Java
title: Java语言RUNOOB教程
meta: Java语言RUNOOB教程
---
* content
{:toc}

## Java教程

### 简介

Java分为三个体系：
* JavaSE（J2SE）（Java2 Platform Standard Edition，java平台标准版）
* JavaEE（J2EE）（Java 2 Platform,Enterprise Edition，java平台企业版）
* JavaME（J2ME）（Java 2 Platform Micro Edition，java平台微型版）。

Java的专业术语：
* JDK（Java Development Kit ）：编写Java程序的程序员使用的软件
* JRE（Java Runtime Environment）：运行Java程序的用户使用的软件
* Server JRE （Java SE Runtime Environment）：服务端使用的 Java 运行环境
* SDK（Software Development Kit）：软件开发工具包，在Java中用于描述1998年~2006年之间的JDK
* DAO（Data Access Object）：数据访问接口，数据访问，顾名思义就是与数据库打交道
* MVC（Model View Controller）：模型(model)－视图(view)－控制器(controller)的缩写，
一种软件设计典范，用于组织代码用一种业务逻辑和数据显示分离的方法

### 开发环境配置

1、Windows 上安装开发环境

下载JDK

配置环境变量

**设置JAVA_HOME**：

一是为了方便引用，比如，JDK安装在C:\jdk1.6.0目录里，则设置JAVA_HOME为该目录路径, 
那么以后要使用这个路径的时候, 只需输入%JAVA_HOME%即可, 避免每次引用都输入很长的路径串;

二则是归一原则, 当JDK路径改变的时候, 仅需更改JAVA_HOME的变量值即可, 否则,就要更改任何用绝对路径引用JDK目录的文档, 
要是万一没有改全, 某个程序找不到JDK, 后果是可想而知的----系统崩溃!

三则是第三方软件会引用约定好的 JAVA_HOME 变量, 不然, 你不能正常使用该软件。

在系统环境变量那一栏中点 -> 新建 JAVA_HOME （JAVA_HOME指向的是JDK的安装路径）

**path 变量**

path 变量使得我们能够在系统中的任何地方运行java应用程序，比如 javac、java、javah 等等,这就要找到我们安装 JDK 的目录，

假设我们的JDK安装在 C:\jdk1.6.0 目录下,那么在 C:\jdk1.6.0\bin 目录下就是我们常用的 java 应用程序,
我们就需要把 C:\jdk1.6.0\bin 这个目录加到 path 环境变量里面。

**classpath 变量**

classpath 环境变量，是当我们在开发java程序时需要引用别人写好的类时，要让 java 解释器知道到哪里去找这个类。
通常，sun 为我们提供了一些额外的丰富的类包，一个是 dt.jar，一个是 tools.jar，
这两个 jar 包都位于 C:\jdk1.6.0\lib 目录下，
所以通常我们都会把这两个 jar 包加到我们的 classpath 环境变量中 
`set classpath=.;C:\jdk1.6.0\lib\tools.jar;C:\jdk1.6.0\lib\dt.jar`。

注意在完成配置环境变量后测试JDK是否安装成功时键入命令：`java -version`

2、Linux 上安装开发环境

3、安装 Eclipse 运行 Java

### 基础语法

编写 Java 程序时，应注意以下几点：
* 大小写敏感：Java 是大小写敏感的，这就意味着标识符 Hello 与 hello 是不同的。
* 类名：对于所有的类来说，类名的首字母应该大写。如果类名由若干单词组成，
那么每个单词的首字母应该大写，例如 `MyFirstJavaClass` 。
* 方法名：所有的方法名都应该以小写字母开头。如果方法名含有若干单词，则后面的每个单词首字母大写。
* 源文件名：源文件名必须和类名相同。当保存文件的时候，你应该使用类名作为文件名保存（切记 Java 是大小写敏感的），
文件名的后缀为 `.java`。（如果文件名和类名不相同则会导致编译错误）。
* 主方法入口：所有的 Java 程序由 `public static void main(String[] args)` 方法开始执行。


### 对象和类

Java作为一种面向对象语言。支持以下基本概念：
* 多态
* 继承
* 封装
* 抽象
* 类
* 对象
* 实例
* 方法
* 重载

构造方法的名称必须与类同名，一个类可以有多个构造方法。

源文件声明规则：
* 一个源文件中只能有一个 public 类
* 一个源文件可以有多个非 public 类
* 源文件的名称应该和 public 类的类名保持一致。例如：源文件中 public 类的类名是 Employee，那么源文件应该命名为Employee.java。
* 如果一个类定义在某个包中，那么 package 语句应该在源文件的首行。
* 如果源文件包含 import 语句，那么应该放在 package 语句和类定义之间。如果没有 package 语句，那么 import 语句应该在源文件中最前面。
* import 语句和 package 语句对源文件中定义的所有类都有效。在同一源文件中，不能给不同的类不同的包声明。


### 基本数据类型

Java 的两大数据类型:
* 内置数据类型
* 引用数据类型

#### 内置数据类型

内置数据类型，Java语言提供了八种基本类型。六种数字类型（四个整数型，两个浮点型），一种字符类型，还有一种布尔型。 

byte 数据类型是8位、有符号的，以二进制补码表示的整数

short 数据类型是 16 位、有符号的以二进制补码表示的整数

int 数据类型是32位、有符号的以二进制补码表示的整数

long 数据类型是 64 位、有符号的以二进制补码表示的整数

float 数据类型是单精度、32位、符合IEEE 754标准的浮点数

double 数据类型是双精度、64 位、符合 IEEE 754 标准的浮点数

char 类型是一个单一的 16 位 Unicode 字符

boolean数据类型表示一位的信息；只有两个取值：true 和 false；默认值是 false；

#### 引用类型

* 在Java中，引用类型的变量非常类似于C/C++的指针。引用类型指向一个对象，指向对象的变量是引用变量。变量一旦声明后，类型就不能被改变了。
* 对象、数组都是引用数据类型。
* 所有引用类型的默认值都是null。
* 一个引用变量可以用来引用任何与之兼容的类型。

#### 常量

* 常量在程序运行时是不能被修改的。
* 在 Java 中使用 final 关键字来修饰常量，声明方式和变量类似。
* 字面量可以赋给任何内置类型的变量。

#### 类型转换

* 自动类型转换：整型、实型（常量）、字符型数据可以混合运算。运算中，不同类型的数据先转化为同一类型，然后进行运算。
* 强制类型转换
* 隐含强制类型转换

### 变量类型

在Java语言中，所有的变量在使用前必须声明。声明变量的基本格式如下：
```
type identifier [ = value][, identifier [= value] ...] ;
```

Java语言支持的变量类型有：
* 类变量：独立于方法之外的变量，用 static 修饰。
* 实例变量：独立于方法之外的变量，不过没有 static 修饰。
* 局部变量：类的方法中的变量。

```
public class Variable{
    static int allClicks=0;    // 类变量
 
    String str="hello world";  // 实例变量
 
    public void method(){
 
        int i =0;  // 局部变量
 
    }
}
```

#### 局部变量

* 局部变量声明在方法、构造方法或者语句块中；
* 局部变量在方法、构造方法、或者语句块被执行的时候创建，当它们执行完成后，变量将会被销毁；
* 访问修饰符不能用于局部变量；
* 局部变量只在声明它的方法、构造方法或者语句块中可见；
* 局部变量是在栈上分配的。
* 局部变量没有默认值，所以局部变量被声明后，必须经过初始化，才可以使用。

#### 实例变量

* 实例变量声明在一个类中，但在方法、构造方法和语句块之外；
* 当一个对象被实例化之后，每个实例变量的值就跟着确定；
* 实例变量在对象创建的时候创建，在对象被销毁的时候销毁；
* 实例变量的值应该至少被一个方法、构造方法或者语句块引用，使得外部能够通过这些方式获取实例变量信息；
* 实例变量可以声明在使用前或者使用后；
* 访问修饰符可以修饰实例变量；
* 实例变量对于类中的方法、构造方法或者语句块是可见的。一般情况下应该把实例变量设为私有。
通过使用访问修饰符可以使实例变量对子类可见；
* 实例变量具有默认值。数值型变量的默认值是0，布尔型变量的默认值是false，引用类型变量的默认值是null。
变量的值可以在声明时指定，也可以在构造方法中指定；
* 实例变量可以直接通过变量名访问。但在静态方法以及其他类中，就应该使用完全限定名：ObjectReference.VariableName。

#### 类变量（静态变量）

* 类变量也称为静态变量，在类中以 static 关键字声明，但必须在方法之外。
* 无论一个类创建了多少个对象，类只拥有类变量的一份拷贝。
* 静态变量除了被声明为常量外很少使用，静态变量是指声明为 public/private，final 和 static 类型的变量。静态变量初始化后不可改变。
* 静态变量储存在静态存储区。经常被声明为常量，很少单独使用 static 声明变量。
* 静态变量在第一次被访问时创建，在程序结束时销毁。
* 与实例变量具有相似的可见性。但为了对类的使用者可见，大多数静态变量声明为 public 类型。
* 默认值和实例变量相似。数值型变量默认值是 0，布尔型默认值是 false，引用类型默认值是 null。
变量的值可以在声明的时候指定，也可以在构造方法中指定。此外，静态变量还可以在静态语句块中初始化。
* 静态变量可以通过：ClassName.VariableName的方式访问。
* 类变量被声明为 public static final 类型时，类变量名称一般建议使用大写字母。
如果静态变量不是 public 和 final 类型，其命名方式与实例变量以及局部变量的命名方式一致。


### 修饰符

Java语言提供了很多修饰符，主要分为以下两类：
* 访问修饰符
* 非访问修饰符

#### 访问控制修饰符

* default (即默认，什么也不写）: 在同一包内可见，不使用任何修饰符。使用对象：类、接口、变量、方法。
* private : 在同一类内可见。使用对象：变量、方法。 注意：不能修饰类（外部类）
* public : 对所有类可见。使用对象：类、接口、变量、方法
* protected : 对同一包内的类和所有子类可见。使用对象：变量、方法。 注意：不能修饰类（外部类）。

#### 非访问修饰符

* static 修饰符，用来修饰类方法和类变量。
* final 修饰符，用来修饰类、方法和变量，final 修饰的类不能够被继承，修饰的方法不能被继承类重新定义，
修饰的变量为常量，是不可修改的。
* abstract 修饰符，用来创建抽象类和抽象方法。
* synchronized，主要用于线程的编程，同一时间只能被一个线程访问。
* volatile 修饰符，主要用于线程的编程，在任何时刻，两个不同的线程总是看到某个成员变量的同一个值。

### 运算符

* 算术运算符
* 关系运算符
* 位运算符
* 逻辑运算符
* 赋值运算符
* 条件运算符（?:）
* instanceof 运算符

### 循环结构

Java中有三种主要的循环结构：
* while 循环
* do…while 循环
* for 循环

增强 for 循环：
````
for(声明语句 : 表达式)
{
   //代码句子
}
````

break 关键字

continue 关键字

### 条件语句

if...else

if...else if...else 语句

嵌套的 if…else 语句

### switch case

switch case 语句语法格式如下：
```
switch(expression){
    case value :
       //语句
       break; //可选
    case value :
       //语句
       break; //可选
    //你可以有任意数量的case语句
    default : //可选
       //语句
}
```

### Number & Math 类

**Number 类**

在实际开发过程中，我们经常会遇到需要使用对象，而不是内置数据类型的情形。
为了解决这个问题，Java 语言为每一个内置数据类型提供了对应的包装类。 

    包装类     基本数据类型
    Boolean     boolean
    Byte     byte
    Short     short
    Integer     int
    Long     long
    Character     char
    Float     float
    Double     double

![]({{site.baseurl}}/images/20220720/20220720112456.png)

这种由编译器特别支持的包装称为装箱，所以当内置数据类型被当作对象使用的时候，编译器会把内置类型装箱为包装类。
相似的，编译器也可以把一个对象拆箱为内置类型。Number 类属于 java.lang 包。 

Java 中 int 和 Integer 的区别：

1、 int 是基本数据类型，int 变量存储的是数值。Integer 是引用类型，实际是一个对象，Integer 存储的是引用对象的地址。

2、因为 new 生成的是两个对象，其内存地址不同。
```
Integer i = new Integer(100);
Integer j = new Integer(100);
System.out.print(i == j);  // false
```

3、int 和 Integer 所占内存比较：

Integer 对象会占用更多的内存。Integer 是一个对象，需要存储对象的元数据。但是 int 是一个原始类型的数据，所以占用的空间更少。

4、非 new 生成的 Integer 变量与 new Integer() 生成的变量比较，结果为 false。
```
/**
 * 比较非new生成的Integer变量与new生成的Integer变量
 */
public class Test {
    public static void main(String[] args) {
        Integer i= new Integer(200);
        Integer j = 200;
        System.out.print(i == j);
        //输出：false
    }
}
```

因为非 new 生成的 Integer 变量指向的是 java 常量池中的对象，
而 new Integer() 生成的变量指向堆中新建的对象，两者在内存中的地址不同。所以输出为 false。

5、两个非 new 生成的 Integer 对象进行比较，如果两个变量的值在区间 `[-128,127]` 之间，比较结果为 true；否则，结果为 false。
```
/**
 * 比较两个非new生成的Integer变量
 */
public class Test {
    public static void main(String[] args) {
        Integer i1 = 127;
        Integer ji = 127;
        System.out.println(i1 == ji);//输出：true
        Integer i2 = 128;
        Integer j2 = 128;
        System.out.println(i2 == j2);//输出：false
    }
}
```

java 在编译 `Integer i1 = 127` 时，会翻译成 `Integer i1 = Integer.valueOf(127)`。

6、Integer 变量(无论是否是 new 生成的)与 int 变量比较，只要两个变量的值是相等的，结果都为 true。
```
/**
 * 比较Integer变量与int变量
 */
public class Test {
    public static void main(String[] args) {
        Integer i1 = 200;
        Integer i2 = new Integer(200);
        int j = 200;
        System.out.println(i1 == j);//输出：true
        System.out.println(i2 == j);//输出：true
    }
}
```

包装类 Integer 变量在与基本数据类型 int 变量比较时，Integer 会自动拆包装为 int，然后进行比较，
实际上就是两个 int 变量进行比较，值相等，所以为 true。


**Math 类**

Java 的 Math 包含了用于执行基本数学运算的属性和方法，如初等指数、对数、平方根和三角函数。

Math 的方法都被定义为 static 形式，通过 Math 类可以在主函数中直接调用。

### Character 类

Character 类用于对单个字符进行操作。

Character 类在对象中包装一个基本类型 char 的值

### String 类

字符串广泛应用 在 Java 编程中，在 Java 中字符串属于对象，Java 提供了 String 类来创建和操作字符串。 

**创建字符串**

String 创建的字符串存储在公共池中，而 new 创建的字符串对象在堆上：
```
String s1 = "Runoob";              // String 直接创建
String s2 = "Runoob";              // String 直接创建
String s3 = s1;                    // 相同引用
String s4 = new String("Runoob");   // String 对象创建
String s5 = new String("Runoob");   // String 对象创建
```

![]({{site.baseurl}}/images/20220720/20220720113955.png)

**String、StringBuffer 和 StringBuilder 的区别**

String：字符串常量，字符串长度不可变。Java中String 是immutable（不可变）的。
用于存放字符的数组被声明为final的，因此只能赋值一次，不可再更改。

StringBuffer：字符串变量（Synchronized，即线程安全）。如果要频繁对字符串内容进行修改，
出于效率考虑最好使用 StringBuffer，如果想转成 String 类型，可以调用 StringBuffer 的 toString() 方法。
Java.lang.StringBuffer 线程安全的可变字符序列。在任意时间点上它都包含某种特定的字符序列，
但通过某些方法调用可以改变该序列的长度和内容。可将字符串缓冲区安全地用于多个线程。

StringBuilder：字符串变量（非线程安全）。在内部 StringBuilder 对象被当作是一个包含字符序列的变长数组。

基本原则：
* 如果要操作少量的数据用 String ；
* 单线程操作大量数据用StringBuilder ；
* 多线程操作大量数据，用StringBuffer。

### StringBuffer

当对字符串进行修改的时候，需要使用 StringBuffer 和 StringBuilder 类。

和 String 类不同的是，StringBuffer 和 StringBuilder 类的对象能够被多次的修改，并且不产生新的未使用对象。 

![]({{site.baseurl}}/images/20220720/20220720114820.png)

### 数组

**声明数组变量**

建议使用 `dataType[] arrayRefVar` 的声明风格声明数组变量。

**创建数组**

Java语言使用new操作符来创建数组，语法如下：`arrayRefVar = new dataType[arraySize];`

上面的语法语句做了两件事：
1. 使用 `dataType[arraySize]` 创建了一个数组。
2. 把新创建的数组的引用赋值给变量 `arrayRefVar`。

数组变量的声明，和创建数组可以用一条语句完成，如下所示：
```
dataType[] arrayRefVar = new dataType[arraySize];
```

还可以使用如下的方式创建数组：
```
dataType[] arrayRefVar = {value0, value1, ..., valuek};
```

数组的元素是通过索引访问的。数组索引从 0 开始，所以索引值从 0 到 `arrayRefVar.length - 1`。

**处理数组**

数组的元素类型和数组的大小都是确定的，所以当处理数组元素时候，我们通常使用基本循环或者 For-Each 循环。

基本循环：
```
public class TestArray {
   public static void main(String[] args) {
      double[] myList = {1.9, 2.9, 3.4, 3.5};
 
      // 打印所有数组元素
      for (int i = 0; i < myList.length; i++) {
         System.out.println(myList[i] + " ");
      }
      
      // 计算所有元素的总和
      double total = 0;
      for (int i = 0; i < myList.length; i++) {
         total += myList[i];
      }
      System.out.println("Total is " + total);
      
      // 查找最大元素
      double max = myList[0];
      for (int i = 1; i < myList.length; i++) {
         if (myList[i] > max) max = myList[i];
      }
      System.out.println("Max is " + max);
   }
}
```

For-Each 循环：
```
public class TestArray {
   public static void main(String[] args) {
      double[] myList = {1.9, 2.9, 3.4, 3.5};
 
      // 打印所有数组元素
      for (double element: myList) {
         System.out.println(element);
      }
   }
}
```

**数组作为函数的参数**

**数组作为函数的返回值**

```
public static int[] reverse(int[] list) {
  int[] result = new int[list.length];
 
  for (int i = 0, j = result.length - 1; i < list.length; i++, j--) {
    result[j] = list[i];
  }
  return result;
}
```

#### 多维数组

多维数组可以看成是数组的数组，比如二维数组就是一个特殊的一维数组，其每一个元素都是一个一维数组。

**多维数组的动态初始化（以二维数组为例）**

1、直接为每一维分配空间，格式如下： 
```
type[][] typeName = new type[typeLength1][typeLength2];
```

type 可以为基本数据类型和复合数据类型，typeLength1 和 typeLength2 必须为正整数，typeLength1 为行数，typeLength2 为列数。

如，一个两行三列的数组：
```
int[][] a = new int[2][3];
```

2、从最高维开始，分别为每一维分配空间，例如： 
```
String[][] s = new String[2][];
s[0] = new String[2];
s[1] = new String[3];
s[0][0] = new String("Good");
s[0][1] = new String("Luck");
s[1][0] = new String("to");
s[1][1] = new String("you");
s[1][2] = new String("!");
```

`s[0]=new String[2]` 和 `s[1]=new String[3]` 是为最高维分配引用空间，
也就是为最高维限制其能保存数据的最长的长度，然后再为其每个数组元素单独分配空间。

#### Arrays 类

java.util.Arrays 类能方便地操作数组，它提供的所有方法都是静态的。

具有以下功能：
* 给数组赋值：通过 fill 方法。
* 对数组排序：通过 sort 方法,按升序。
* 比较数组：通过 equals 方法比较数组中元素值是否相等。
* 查找数组元素：通过 binarySearch 方法能对排序好的数组进行二分查找法操作。

```
import java.util.Arrays;

public class TestArrays {
    public static void output(int[] array) {
        if (array != null) {
            for (int i = 0; i < array.length; i++) {
                System.out.print(array[i] + " ");
            }
        }
        System.out.println();
    }

    public static void main(String[] args) {
        int[] array = new int[5];
        // 填充数组
        Arrays.fill(array, 5);
        System.out.println("填充数组：Arrays.fill(array, 5)：");
        TestArrays.output(array);
        
        // 将数组的第2和第3个元素赋值为8
        Arrays.fill(array, 2, 4, 8);
        System.out.println("将数组的第2和第3个元素赋值为8：Arrays.fill(array, 2, 4, 8)：");
        TestArrays.output(array);
        
        int[] array1 = { 7, 8, 3, 2, 12, 6, 3, 5, 4 };
        // 对数组的第2个到第6个进行排序进行排序
        Arrays.sort(array1, 2, 7);
        System.out.println("对数组的第2个到第6个元素进行排序进行排序：Arrays.sort(array,2,7)：");
        TestArrays.output(array1);
        
        // 对整个数组进行排序
        Arrays.sort(array1);
        System.out.println("对整个数组进行排序：Arrays.sort(array1)：");
        TestArrays.output(array1);
        
        // 比较数组元素是否相等
        System.out.println("比较数组元素是否相等:Arrays.equals(array, array1):" + "\n" + Arrays.equals(array, array1));
        int[] array2 = array1.clone();
        System.out.println("克隆后数组元素是否相等:Arrays.equals(array1, array2):" + "\n" + Arrays.equals(array1, array2));
        
        // 使用二分搜索算法查找指定元素所在的下标（必须是排序好的，否则结果不正确）
        Arrays.sort(array1);
        System.out.println("元素3在array1中的位置：Arrays.binarySearch(array1, 3)：" + "\n" + Arrays.binarySearch(array1, 3));
        
        // 如果不存在就返回负数
        System.out.println("元素9在array1中的位置：Arrays.binarySearch(array1, 9)：" + "\n" + Arrays.binarySearch(array1, 9));
    }
}
```

输出结果：
```
填充数组：Arrays.fill(array, 5)：
5 5 5 5 5 
将数组的第2和第3个元素赋值为8：Arrays.fill(array, 2, 4, 8)：
5 5 8 8 5 
对数组的第2个到第6个元素进行排序进行排序：Arrays.sort(array,2,7)：
7 8 2 3 3 6 12 5 4 
对整个数组进行排序：Arrays.sort(array1)：
2 3 3 4 5 6 7 8 12 
比较数组元素是否相等:Arrays.equals(array, array1):
false
克隆后数组元素是否相等:Arrays.equals(array1, array2):
true
元素3在array1中的位置：Arrays.binarySearch(array1, 3)：
1
元素9在array1中的位置：Arrays.binarySearch(array1, 9)：
-9
```


### 日期时间

java.util 包提供了 Date 类来封装当前的日期和时间。 Date 类提供两个构造函数来实例化 Date 对象。

第一个构造函数使用当前日期和时间来初始化对象。`Date( )`

第二个构造函数接收一个参数，该参数是从 1970 年 1 月 1 日起的毫秒数。 `Date(long millisec)`

SimpleDateFormat 是一个以语言环境敏感的方式来格式化和分析日期的类。
SimpleDateFormat 允许你选择任何用户自定义日期时间格式来运行。
SimpleDateFormat 类有一些附加的方法，特别是parse()，它试图按照给定的SimpleDateFormat 对象的格式化存储来解析字符串。

printf 方法可以很轻松地格式化时间和日期。

Java 休眠(sleep)

sleep()使当前线程进入停滞状态（阻塞当前线程），让出CPU的使用、目的是不让当前线程独自霸占该进程所获的CPU资源，
以留一定时间给其他线程执行的机会。

#### Calendar类

Calendar类是一个抽象类，在实际使用时实现特定的子类的对象，创建对象的过程对程序员来说是透明的，只需要使用getInstance方法创建即可。

Calendar类实现了公历日历，GregorianCalendar是Calendar类的一个具体实现。 

```
import java.util.*;
  
public class GregorianCalendarDemo {
 
   public static void main(String[] args) {
      String months[] = {
      "Jan", "Feb", "Mar", "Apr",
      "May", "Jun", "Jul", "Aug",
      "Sep", "Oct", "Nov", "Dec"};
      
      int year;
      // 初始化 Gregorian 日历
      // 使用当前时间和日期
      // 默认为本地时间和时区
      GregorianCalendar gcalendar = new GregorianCalendar();
      // 显示当前时间和日期的信息
      System.out.print("Date: ");
      System.out.print(months[gcalendar.get(Calendar.MONTH)]);
      System.out.print(" " + gcalendar.get(Calendar.DATE) + " ");
      System.out.println(year = gcalendar.get(Calendar.YEAR));
      System.out.print("Time: ");
      System.out.print(gcalendar.get(Calendar.HOUR) + ":");
      System.out.print(gcalendar.get(Calendar.MINUTE) + ":");
      System.out.println(gcalendar.get(Calendar.SECOND));
      
      // 测试当前年份是否为闰年
      if(gcalendar.isLeapYear(year)) {
         System.out.println("当前年份是闰年");
      }
      else {
         System.out.println("当前年份不是闰年");
      }
   }
}
```

### 正则表达式

java.util.regex 包主要包括以下三个类：

Pattern 类：

    pattern 对象是一个正则表达式的编译表示。Pattern 类没有公共构造方法。要创建一个 Pattern 对象，你必须首先调用其公共静态编译方法，它返回一个 Pattern 对象。该方法接受一个正则表达式作为它的第一个参数。
    
Matcher 类：

    Matcher 对象是对输入字符串进行解释和匹配操作的引擎。与Pattern 类一样，Matcher 也没有公共构造方法。你需要调用 Pattern 对象的 matcher 方法来获得一个 Matcher 对象。
    
PatternSyntaxException：

    PatternSyntaxException 是一个非强制异常类，它表示一个正则表达式模式中的语法错误。



### 方法

Java方法是语句的集合，它们在一起执行一个功能。

    方法是解决一类问题的步骤的有序组合
    方法包含于类或对象中
    方法在程序中被创建，在其他地方被引用

方法的优点

    1. 使程序变得更简短而清晰。
    2. 有利于程序维护。
    3. 可以提高程序开发的效率。
    4. 提高了代码的重用性。

方法的命名规则

    1.方法的名字的第一个单词应以小写字母作为开头，后面的单词则用大写字母开头写，不使用连接符。例如：addPerson。
    2.下划线可能出现在 JUnit 测试方法名称中用以分隔名称的逻辑组件。一个典型的模式是：test<MethodUnderTest>_<state>，例如 testPop_emptyStack。 
    
#### 方法的定义
    
一般情况下，定义一个方法包含以下语法：
```
修饰符 返回值类型 方法名(参数类型 参数名){
    ...
    方法体
    ...
    return 返回值;
}
```

方法包含一个方法头和一个方法体。下面是一个方法的所有部分：

    修饰符：修饰符，这是可选的，告诉编译器如何调用该方法。定义了该方法的访问类型。
    返回值类型 ：方法可能会返回值。returnValueType 是方法返回值的数据类型。有些方法执行所需的操作，但没有返回值。在这种情况下，returnValueType 是关键字void。
    方法名：是方法的实际名称。方法名和参数表共同构成方法签名。
    参数类型：参数像是一个占位符。当方法被调用时，传递值给参数。这个值被称为实参或变量。参数列表是指方法的参数类型、顺序和参数的个数。参数是可选的，方法可以不包含任何参数。
    方法体：方法体包含具体的语句，定义该方法的功能。

   
#### 方法的重载 

一个类的两个方法拥有相同的名字，但是有不同的参数列表。

Java编译器根据方法签名判断哪个方法应该被调用。

方法重载可以让程序更清晰易读。执行密切相关任务的方法应该使用相同的名字。

重载的方法必须拥有不同的参数列表。你不能仅仅依据修饰符或者返回类型的不同来重载方法。 

#### 变量作用域

变量的范围是程序中该变量可以被引用的部分。

方法内定义的变量被称为局部变量。

局部变量的作用范围从声明开始，直到包含它的块结束。

局部变量必须声明才可以使用。

方法的参数范围涵盖整个方法。参数实际上是一个局部变量。

for循环的初始化部分声明的变量，其作用范围在整个循环。

但循环体内声明的变量其适用范围是从它声明到循环体结束。

你可以在一个方法里，不同的非嵌套块中多次声明一个具有相同的名称局部变量，但你不能在嵌套块内两次声明局部变量。 

**命令行参数的使用**

#### 构造方法

当一个对象被创建时候，构造方法用来初始化该对象。构造方法和它所在类的名字相同，但构造方法没有返回值。

通常会使用构造方法给一个类的实例变量赋初值，或者执行其它必要的步骤来创建一个完整的对象。

不管你是否自定义构造方法，所有的类都有构造方法，因为 Java 自动提供了一个默认构造方法，
默认构造方法的访问修饰符和类的访问修饰符相同(类为 public，构造函数也为 public；类改为 protected，构造函数也改为 protected)。

一旦你定义了自己的构造方法，默认构造方法就会失效。 

**可变参数**

方法的可变参数的声明如下所示：
```
typeName... parameterName
```

一个方法中只能指定一个可变参数，它必须是方法的最后一个参数。任何普通的参数必须在它之前声明。 

```
public class VarargsDemo {
    public static void main(String[] args) {
        // 调用可变参数的方法
        printMax(34, 3, 3, 2, 56.5);
        printMax(new double[]{1, 2, 3});
    }
 
    public static void printMax(double... numbers) {
        if (numbers.length == 0) {
            System.out.println("No argument passed");
            return;
        }
 
        double result = numbers[0];
 
        for (int i = 1; i <  numbers.length; i++){
            if (numbers[i] >  result) {
                result = numbers[i];
            }
        }
        System.out.println("The max value is " + result);
    }
}
```

以上实例编译运行结果如下：
```
The max value is 56.5
The max value is 3.0
```

#### finalize() 方法

Java 允许定义这样的方法，它在对象被垃圾收集器析构(回收)之前调用，这个方法叫做 finalize( )，它用来清除回收对象。

例如，你可以使用 finalize() 来确保一个对象打开的文件被关闭了。

在 finalize() 方法里，你必须指定在对象销毁时候要执行的操作。 

finalize() 一般格式是：
```
protected void finalize()
{
   // 在这里终结代码
}
```

关键字 protected 是一个限定符，它确保 `finalize()` 方法不会被该类以外的代码调用。

当然，Java 的内存回收可以由 JVM 来自动完成。如果你手动使用，则可以使用上面的方法。 

```
public class FinalizationDemo {  
  public static void main(String[] args) {  
    Cake c1 = new Cake(1);  
    Cake c2 = new Cake(2);  
    Cake c3 = new Cake(3);  
      
    c2 = c3 = null;  
    System.gc(); //调用Java垃圾收集器
  }  
}  
 
class Cake extends Object {  
  private int id;  
  public Cake(int id) {  
    this.id = id;  
    System.out.println("Cake Object " + id + "is created");  
  }  
    
  protected void finalize() throws java.lang.Throwable {  
    super.finalize();  
    System.out.println("Cake Object " + id + "is disposed");  
  }  
}
```

运行以上代码，输出结果如下：
```
$ javac FinalizationDemo.java 
$ java FinalizationDemo
Cake Object 1is created
Cake Object 2is created
Cake Object 3is created
Cake Object 3is disposed
Cake Object 2is disposed
```
   
### Stream、File、IO

流(Stream)、文件(File)和IO

Java.io 包几乎包含了所有操作输入、输出需要的类。所有这些流类代表了输入源和输出目标。

Java.io 包中的流支持很多种格式，比如：基本类型、对象、本地化字符集等等。

一个流可以理解为一个数据的序列。输入流表示从一个源读取数据，输出流表示向一个目标写数据。

Java 为 I/O 提供了强大的而灵活的支持，使其更广泛地应用到文件传输和网络编程中。 

**读取控制台输入**

Java 的控制台输入由 System.in 完成。 

为了获得一个绑定到控制台的字符流，你可以把 System.in 包装在一个 BufferedReader 对象中来创建一个字符流。 

```
BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
```

BufferedReader 对象创建后，我们便可以使用 read() 方法从控制台读取一个字符，或者用 readLine() 方法读取一个字符串。 

```
// 使用 BufferedReader 在控制台读取字符

import java.io.*;

public class BRRead {
    public static void main(String[] args) throws IOException {
        char c;
        // 使用 System.in 创建 BufferedReader
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        System.out.println("输入字符, 按下 'q' 键退出。");
        // 读取字符
        do {
            c = (char) br.read();
            System.out.println(c);
        } while (c != 'q');
    }
}
```

```
// 使用 BufferedReader 在控制台读取字符

import java.io.*;

public class BRReadLines {
    public static void main(String[] args) throws IOException {
        // 使用 System.in 创建 BufferedReader
        BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
        String str;
        System.out.println("Enter lines of text.");
        System.out.println("Enter 'end' to quit.");
        do {
            str = br.readLine();
            System.out.println(str);
        } while (!str.equals("end"));
    }
}
```

**控制台输出**

控制台的输出由 print() 和 println() 完成。这些方法都由类 PrintStream 定义，System.out 是该类对象的一个引用。
PrintStream 继承了 OutputStream类，并且实现了方法 write()。这样，write() 也可以用来往控制台写操作。 

```
import java.io.*;
 
// 演示 System.out.write().
public class WriteDemo {
    public static void main(String[] args) {
        int b;
        b = 'A';
        System.out.write(b);
        System.out.write('\n');
    }
}
```

注意：write() 方法不经常使用，因为 print() 和 println() 方法用起来更为方便。 

**读写文件**

下图是一个描述输入流和输出流的类层次图。

![]({{site.baseurl}}/images/20220720/20220720115120.png)

FileInputStream

该流用于从文件读取数据，它的对象可以用关键字 new 来创建。
有多种构造方法可用来创建对象。
可以使用字符串类型的文件名来创建一个输入流对象来读取文件：
```
InputStream f = new FileInputStream("C:/java/hello");
```

也可以使用一个文件对象来创建一个输入流对象来读取文件。我们首先得使用 File() 方法来创建一个文件对象： 
```
File f = new File("C:/java/hello");
InputStream in = new FileInputStream(f);
```

创建了InputStream对象，就可以使用下面的方法来读取流或者进行其他的流操作。

    序号     方法及描述
    1     public void close() throws IOException{}
          关闭此文件输入流并释放与此流有关的所有系统资源。抛出IOException异常。
    2     protected void finalize()throws IOException {}
          这个方法清除与该文件的连接。确保在不再引用文件输入流时调用其 close 方法。抛出IOException异常。
    3     public int read(int r)throws IOException{}
          这个方法从 InputStream 对象读取指定字节的数据。返回为整数值。返回下一字节数据，如果已经到结尾则返回-1。
    4     public int read(byte[] r) throws IOException{}
          这个方法从输入流读取r.length长度的字节。返回读取的字节数。如果是文件结尾则返回-1。
    5     public int available() throws IOException{}
          返回下一次对此输入流调用的方法可以不受阻塞地从此输入流读取的字节数。返回一个整数值。

FileOutputStream

该类用来创建一个文件并向文件中写数据。
如果该流在打开文件进行输出前，目标文件不存在，那么该流会创建该文件。
有两个构造方法可以用来创建 FileOutputStream 对象。

使用字符串类型的文件名来创建一个输出流对象：
```
OutputStream f = new FileOutputStream("C:/java/hello")
```

也可以使用一个文件对象来创建一个输出流来写文件。我们首先得使用File()方法来创建一个文件对象： 
```
File f = new File("C:/java/hello");
OutputStream fOut = new FileOutputStream(f);
```

创建OutputStream 对象完成后，就可以使用下面的方法来写入流或者进行其他的流操作。 

    序号     方法及描述
    1     public void close() throws IOException{}
          关闭此文件输入流并释放与此流有关的所有系统资源。抛出IOException异常。
    2     protected void finalize()throws IOException {}
          这个方法清除与该文件的连接。确保在不再引用文件输入流时调用其 close 方法。抛出IOException异常。
    3     public void write(int w)throws IOException{}
          这个方法把指定的字节写到输出流中。
    4     public void write(byte[] w)
          把指定数组中w.length长度的字节写到OutputStream中。

下面是一个演示 InputStream 和 OutputStream 用法的例子： 
```
import java.io.*;
 
public class fileStreamTest {
    public static void main(String[] args) {
        try {
            byte bWrite[] = { 11, 21, 3, 40, 5 };
            OutputStream os = new FileOutputStream("test.txt");
            for (int x = 0; x < bWrite.length; x++) {
                os.write(bWrite[x]); // writes the bytes
            }
            os.close();
 
            InputStream is = new FileInputStream("test.txt");
            int size = is.available();
 
            for (int i = 0; i < size; i++) {
                System.out.print((char) is.read() + "  ");
            }
            is.close();
        } catch (IOException e) {
            System.out.print("Exception");
        }
    }
}
```

**文件和I/O**

还有一些关于文件和I/O的类，我们也需要知道：

    File Class(类)
    FileReader Class(类)
    FileWriter Class(类)

**Java中的目录**

创建目录

File类中有两个方法可以用来创建文件夹：
* mkdir( )方法创建一个文件夹，成功则返回true，失败则返回false。失败表明File对象指定的路径已经存在，
或者由于整个路径还不存在，该文件夹不能被创建。
* mkdirs()方法创建一个文件夹和它的所有父文件夹。

读取目录

删除目录或文件

删除文件可以使用 java.io.File.delete() 方法。



### Scanner 类

java.util.Scanner 是 Java5 的新特征，我们可以通过 Scanner 类来获取用户的输入。 

下面是创建 Scanner 对象的基本语法：
```
Scanner s = new Scanner(System.in);
```

通过 Scanner 类的 next() 与 nextLine() 方法获取输入的字符串，
在读取前我们一般需要 使用 hasNext 与 hasNextLine 判断是否还有输入的数据。

```
import java.util.Scanner;
 
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        // 从键盘接收数据
 
        // nextLine方式接收字符串
        System.out.println("nextLine方式接收：");
        // 判断是否还有输入
        if (scan.hasNextLine()) {
            String str2 = scan.nextLine();
            System.out.println("输入的数据为：" + str2);
        }
        scan.close();
    }
}
```

**next() 与 nextLine() 区别**

next():
1. 一定要读取到有效字符后才可以结束输入。
2. 对输入有效字符之前遇到的空白，next() 方法会自动将其去掉。
3. 只有输入有效字符后才将其后面输入的空白作为分隔符或者结束符。
4. next() 不能得到带有空格的字符串。

nextLine()：
1. 以Enter为结束符,也就是说 nextLine()方法返回的是输入回车之前的所有字符。
2. 可以获得空白。

如果要输入 int 或 float 类型的数据，在 Scanner 类中也有支持，
但是在输入之前最好先使用 hasNextXxx() 方法进行验证，再使用 nextXxx() 来读取：
```
import java.util.Scanner;
 
public class ScannerDemo {
    public static void main(String[] args) {
        Scanner scan = new Scanner(System.in);
        // 从键盘接收数据
        int i = 0;
        float f = 0.0f;
        System.out.print("输入整数：");
        if (scan.hasNextInt()) {
            // 判断输入的是否是整数
            i = scan.nextInt();
            // 接收整数
            System.out.println("整数数据：" + i);
        } else {
            // 输入错误的信息
            System.out.println("输入的不是整数！");
        }
        System.out.print("输入小数：");
        if (scan.hasNextFloat()) {
            // 判断输入的是否是小数
            f = scan.nextFloat();
            // 接收小数
            System.out.println("小数数据：" + f);
        } else {
            // 输入错误的信息
            System.out.println("输入的不是小数！");
        }
        scan.close();
    }
}
```

处理接受整数和小数的逻辑时，需要在 else 的分支逻辑里把非整数的输入接收走，否则会影响后续 hasNextFloat 判断和接受小数的逻辑。
```
Scanner scan = new Scanner(System.in);
// 从键盘接收数据
int i = 0;
float f = 0.0f;
System.out.print("输入整数：");
if (scan.hasNextInt()) {
    // 判断输入的是否是整数
    i = scan.nextInt();
    // 接收整数
    System.out.println("整数数据：" + i);
} else {
    scan.nextLine();  // 没接收到整数，把其他类型收掉，否则会影响到后边接受小数的逻辑
    // 输入错误的信息
    System.out.println("输入的不是整数！");
}
```

Scanner 不仅能从输入流中读取，也能从文件中读取：
```
import java.io.File;
import java.io.FileNotFoundException;
import java.util.Scanner;

public class Fileio {
    public static void main(String[] args) throws FileNotFoundException {
        int[] arr=new int[10];
        int i=0;
        Scanner sc=new Scanner(new File("test.txt"));
        while(sc.hasNextInt()) {
            arr[i]=sc.nextInt();
            i++;
        }
        sc.close();
        System.out.printf("读取了 %d 个数\n",i);
        for(int j=0;j<i;j++) {
            System.out.println(arr[j]);
        }
    }
}
```

### 异常处理

异常发生的原因有很多，通常包含以下几大类：
* 用户输入了非法数据。
* 要打开的文件不存在。
* 网络通信时连接中断，或者JVM内存溢出。

这些异常有的是因为用户错误引起，有的是程序错误引起的，还有其它一些是因为物理错误引起的。

要理解Java异常处理是如何工作的，你需要掌握以下三种类型的异常：
* 检查性异常：最具代表的检查性异常是用户错误或问题引起的异常，这是程序员无法预见的。
例如要打开一个不存在文件时，一个异常就发生了，这些异常在编译时不能被简单地忽略。
* 运行时异常： 运行时异常是可能被程序员避免的异常。与检查性异常相反，运行时异常可以在编译时被忽略。
* 错误： 错误不是异常，而是脱离程序员控制的问题。错误在代码中通常被忽略。
例如，当栈溢出时，一个错误就发生了，它们在编译也检查不到的。

**Exception 类的层次**

![]({{site.baseurl}}/images/20220720/20220720115220.png)

所有的异常类是从 java.lang.Exception 类继承的子类。 

![]({{site.baseurl}}/images/20220720/20220720115226.png)

**Java 内置异常类**



**异常方法**

下面的列表是 Throwable 类的主要方法:

    序号     方法及说明
    1     public String getMessage()
          返回关于发生的异常的详细信息。这个消息在Throwable 类的构造函数中初始化了。
    2     public Throwable getCause()
          返回一个 Throwable 对象代表异常原因。
    3     public String toString()
          返回此 Throwable 的简短描述。
    4     public void printStackTrace()
          将此 Throwable 及其回溯打印到标准错误流。。
    5     public StackTraceElement [] getStackTrace()
          返回一个包含堆栈层次的数组。下标为0的元素代表栈顶，最后一个元素代表方法调用堆栈的栈底。
    6     public Throwable fillInStackTrace()
          用当前的调用栈层次填充Throwable 对象栈层次，添加到栈层次任何先前信息中。

**捕获异常**

使用 try 和 catch 、finally 关键字可以捕获异常。

**throws/throw 关键字**

如果一个方法没有捕获到一个检查性异常，那么该方法必须使用 throws 关键字来声明。throws 关键字放在方法签名的尾部。
也可以使用 throw 关键字抛出一个异常，无论它是新实例化的还是刚捕获到的。

下面方法的声明抛出一个 RemoteException 异常：
```
import java.io.*;

public class className
{
  public void deposit(double amount) throws RemoteException
  {
    // Method implementation
    throw new RemoteException();
  }
  //Remainder of class definition
}
```

一个方法可以声明抛出多个异常，多个异常之间用逗号隔开。

例如，下面的方法声明抛出 RemoteException 和 InsufficientFundsException： 
```
import java.io.*;

public class className
{
   public void withdraw(double amount) throws RemoteException, InsufficientFundsException
   {
       // Method implementation
   }
   //Remainder of class definition
}
```

**try-with-resources**

JDK7 之后，Java 新增的 try-with-resource 语法糖来打开资源，并且可以在语句执行完毕后确保每个资源都被自动关闭 。

JDK7 之前所有被打开的系统资源，比如流、文件或者 Socket 连接等，都需要被开发者手动关闭，否则将会造成资源泄露。

```
try (resource declaration) {
  // 使用的资源
} catch (ExceptionType e1) {
  // 异常块
}
```

以上的语法中 try 用于声明和实例化资源，catch 用于处理关闭资源时可能引发的所有异常。

注意：try-with-resources 语句关闭所有实现 AutoCloseable 接口的资源。

```
import java.io.*;

public class RunoobTest {
    public static void main(String[] args) {
        String line;
        try(BufferedReader br = new BufferedReader(new FileReader("test.txt"))) {
            while ((line = br.readLine()) != null) {
                System.out.println("Line =>"+line);
            }
        } catch (IOException e) {
            System.out.println("IOException in try block =>" + e.getMessage());
        }
    }
}
```

在 try-with-resources 语句中声明和实例化 BufferedReader 可确保语句执行完毕后释放资源，
不需要考虑 try 语句是正常执行还是抛出异常。如果发生异常，可以使用 catch 来处理异常。

再看下不使用 try-with-resources 而改成 finally 来关闭资源，整体代码量多了很多，而且更复杂繁琐了：
```
import java.io.*;

class RunoobTest {
    public static void main(String[] args) {
        BufferedReader br = null;
        String line;

        try {
            System.out.println("Entering try block");
            br = new BufferedReader(new FileReader("test.txt"));
            while ((line = br.readLine()) != null) {
            System.out.println("Line =>"+line);
            }
        } catch (IOException e) {
            System.out.println("IOException in try block =>" + e.getMessage());
        } finally {
            System.out.println("Entering finally block");
            try {
                if (br != null) {
                    br.close();
                }
            } catch (IOException e) {
                System.out.println("IOException in finally block =>"+e.getMessage());
            }
        }
    }
}
```

**try-with-resources 处理多个资源**

try-with-resources 语句中可以声明多个资源，方法是使用分号 ; 分隔各个资源：
```
import java.io.*;
import java.util.*;

class RunoobTest {
    public static void main(String[] args) throws IOException{
        try (Scanner scanner = new Scanner(new File("testRead.txt"));
            PrintWriter writer = new PrintWriter(new File("testWrite.txt"))) {
            while (scanner.hasNext()) {
                writer.print(scanner.nextLine());
            }
        }
    }
}
```

以上实例使用 Scanner 对象从 testRead.txt 文件中读取一行并将其写入新的 testWrite.txt 文件中。
多个声明资源时，try-with-resources 语句以相反的顺序关闭这些资源。 在本例中，PrintWriter 对象先关闭，然后 Scanner 对象关闭。

**声明自定义异常**

在 Java 中你可以自定义异常。编写自己的异常类时需要记住下面的几点。
* 所有异常都必须是 Throwable 的子类。
* 如果希望写一个检查性异常类，则需要继承 Exception 类。
* 如果你想写一个运行时异常类，那么需要继承 RuntimeException 类。

**通用异常**

在Java中定义了两种类型的异常和错误。
* JVM(Java虚拟机) 异常：由 JVM 抛出的异常或错误。
例如：NullPointerException 类，ArrayIndexOutOfBoundsException 类，ClassCastException 类。
* 程序级异常：由程序或者API程序抛出的异常。例如 IllegalArgumentException 类，IllegalStateException 类。


## Java 面向对象课程 

![]({{site.baseurl}}/images/20220720/20220720211226.png)

### 继承

#### 继承的概念

继承是java面向对象编程技术的一块基石，因为它允许创建分等级层次的类。

继承就是子类继承父类的特征和行为，使得子类对象（实例）具有父类的实例域和方法，或子类从父类继承方法，使得子类具有父类相同的行为。 

**继承类型**

![]({{site.baseurl}}/images/20220720/20220720211221.png)

需要注意的是 Java 不支持多继承，但支持多重继承。

#### 继承的特性

* 子类拥有父类非 private 的属性、方法。
* 子类可以拥有自己的属性和方法，即子类可以对父类进行扩展。
* 子类可以用自己的方式实现父类的方法。
* Java 的继承是单继承，但是可以多重继承，单继承就是一个子类只能继承一个父类，多重继承就是，
例如 B 类继承 A 类，C 类继承 B 类，所以按照关系就是 B 类是 C 类的父类，A 类是 B 类的父类，
这是 Java 继承区别于 C++ 继承的一个特性。
* 提高了类之间的耦合性（继承的缺点，耦合度高就会造成代码之间的联系越紧密，代码独立性越差）。

#### 继承关键字 

继承可以使用 extends 和 implements 这两个关键字来实现继承，而且所有的类都是继承于 java.lang.Object，
当一个类没有继承的两个关键字，则默认继承object（这个类在 java.lang 包中，所以不需要 import）祖先类。

**extends关键字**


**implements关键字**


**super 关键字**

super关键字：我们可以通过super关键字来实现对父类成员的访问，用来引用当前对象的父类。

super 表示使用它的类的父类。super 可用于：

* 调用父类的构造方法；
* 调用父类的方法（子类覆盖了父类的方法时）；
* 访问父类的数据域（可以这样用但没有必要这样用）。 

调用父类的构造方法语法：`super();` 或 `super(参数列表);`

注意：super 语句必须是子类构造方法的第一条语句。不能在子类中使用父类构造方法名来调用父类构造方法。 
父类的构造方法不被子类继承。调用父类的构造方法的唯一途径是使用 super 关键字，如果子类中没显式调用，
则编译器自动将 `super();` 作为子类构造方法的第一条语句。这会形成一个构造方法链。

静态方法中不能使用 super 关键字。

调用父类的方法语法：`super.方法名(参数列表);` 

如果是继承的方法，是没有必要使用 super 来调用，直接即可调用。但如果子类覆盖或重写了父类的方法，
则只有使用 super 才能在子类中调用父类中的被重写的方法。 

**this 关键字**

this关键字：指向自己的引用。

this 关键字表示当前对象。可用于：

* 调用当前类的构造方法，并且必须是方法的第一条语句。如：`this();` 调用默认构造方法。`this(参数);` 调用带参构造方法。
* 限定当前对象的数据域变量。一般用于方法内的局部变量与对象的数据域变量同名的情况。
如 `this.num = num`。`this.num` 表示当前对象的数据域变量 num，而 num 表示方法中的局部变量。

**final 关键字**

final 含义为 "最终的"。

使用 final 关键字声明类，就是把类定义定义为最终类，不能被继承，或者用于修饰方法，该方法不能被子类重写。

#### 构造器

子类是不继承父类的构造器（构造方法或者构造函数）的，它只是调用（隐式或显式）。
如果父类的构造器带有参数，则必须在子类的构造器中显式地通过 super 关键字调用父类的构造器并配以适当的参数列表。

如果父类构造器没有参数，则在子类的构造器中不需要使用 super 关键字调用父类构造器，系统会自动调用父类的无参构造器。 

### Override/Overload

#### 重写(Override)

重写是子类对父类的允许访问的方法的实现过程进行重新编写, 返回值和形参都不能改变。即外壳不变，核心重写！

重写的好处在于子类可以根据需要，定义特定于自己的行为。 也就是说子类能够根据需要实现父类的方法。

重写方法不能抛出新的检查异常或者比被重写方法申明更加宽泛的异常。

**方法的重写规则**

**Super 关键字的使用**

#### 重载(Overload)

重载(overloading) 是在一个类里面，方法名字相同，而参数不同。返回类型可以相同也可以不同。

每个重载的方法（或者构造函数）都必须有一个独一无二的参数类型列表。

最常用的地方就是构造器的重载。

重载规则:

* 被重载的方法必须改变参数列表(参数个数或类型不一样)；
* 被重载的方法可以改变返回类型；
* 被重载的方法可以改变访问修饰符；
* 被重载的方法可以声明新的或更广的检查异常；
* 方法能够在同一个类中或者在一个子类中被重载。
* 无法以返回值类型作为重载函数的区分标准。

#### 重写与重载之间的区别

**总结**

方法的重写(Overriding)和重载(Overloading)是java多态性的不同表现，
重写是父类与子类之间多态性的一种表现，重载可以理解成多态的具体表现形式。

* (1)方法重载是一个类中定义了多个方法名相同,而他们的参数的数量不同或数量相同而类型和次序不同,则称为方法的重载(Overloading)。
* (2)方法重写是在子类存在方法与父类的方法的名字相同,而且参数的个数与类型一样,返回值也一样的方法,就称为重写(Overriding)。
* (3)方法重载是一个类的多态性表现,而方法重写是子类与父类的一种多态性表现。

![]({{site.baseurl}}/images/20220720/20220720211229.png)


### 多态

多态是同一个行为具有多个不同表现形式或形态的能力。

多态就是同一个接口，使用不同的实例而执行不同操作。

多态性是对象多种表现形式的体现。

多态的优点
1. 消除类型之间的耦合关系
2. 可替换性
3. 可扩充性
4. 接口性
5. 灵活性
6. 简化性

多态存在的三个必要条件
* 继承
* 重写
* 父类引用指向子类对象：`Parent p = new Child();`

![]({{site.baseurl}}/images/20220720/20220720211235.jpg)

```
class Shape {
    void draw() {}
}
 
class Circle extends Shape {
    void draw() {
        System.out.println("Circle.draw()");
    }
}
 
class Square extends Shape {
    void draw() {
        System.out.println("Square.draw()");
    }
}
 
class Triangle extends Shape {
    void draw() {
        System.out.println("Triangle.draw()");
    }
}
```

当使用多态方式调用方法时，首先检查父类中是否有该方法，如果没有，则编译错误；如果有，再去调用子类的同名方法。

多态的好处：可以使程序有良好的扩展，并可以对所有类的对象进行通用处理。

以下是一个多态实例的演示，详细说明请看注释：
```
public class Test {
    public static void main(String[] args) {
        show(new Cat());  // 以 Cat 对象调用 show 方法
        show(new Dog());  // 以 Dog 对象调用 show 方法
                
        Animal a = new Cat();  // 向上转型  
        a.eat();               // 调用的是 Cat 的 eat
        Cat c = (Cat)a;        // 向下转型  
        c.work();        // 调用的是 Cat 的 work
    }  
            
    public static void show(Animal a)  {
        a.eat();  
        // 类型判断
        if (a instanceof Cat)  {  // 猫做的事情 
            Cat c = (Cat)a;  
            c.work();  
        } else if (a instanceof Dog) { // 狗做的事情 
            Dog c = (Dog)a;  
            c.work();  
        }  
    }  
}
 
abstract class Animal {  
    abstract void eat();  
}  
  
class Cat extends Animal {  
    public void eat() {  
        System.out.println("吃鱼");  
    }  
    public void work() {  
        System.out.println("抓老鼠");  
    }  
}  
  
class Dog extends Animal {  
    public void eat() {  
        System.out.println("吃骨头");  
    }  
    public void work() {  
        System.out.println("看家");  
    }  
}
```

执行以上程序，输出结果为：
```
吃鱼
抓老鼠
吃骨头
看家
吃鱼
抓老鼠
```

#### 虚函数

虚函数的存在是为了多态。

Java 中其实没有虚函数的概念，它的普通函数就相当于 C++ 的虚函数，动态绑定是Java的默认行为。
如果 Java 中不希望某个函数具有虚函数特性，可以加上 final 关键字变成非虚函数。

#### 多态的实现方式

方式一：重写： 

这个内容已经在上一章节详细讲过，就不再阐述，详细可访问：[Java 重写(Override)与重载(Overload)](##overrideoverload)

方式二：接口

1. 生活中的接口最具代表性的就是插座，例如一个三接头的插头都能接在三孔插座中，
因为这个是每个国家都有各自规定的接口规则，有可能到国外就不行，那是因为国外自己定义的接口类型。
2. java中的接口类似于生活中的接口，就是一些方法特征的集合，但没有方法的实现。具体可以看 [java接口](#接口) 这一章节的内容。

方式三：抽象类和抽象方法

详情请看 [Java抽象类](#抽象类) 章节。
    
### 抽象类

在面向对象的概念中，所有的对象都是通过类来描绘的，但是反过来，并不是所有的类都是用来描绘对象的，
如果一个类中没有包含足够的信息来描绘一个具体的对象，这样的类就是抽象类。

抽象类除了不能实例化对象之外，类的其它功能依然存在，成员变量、成员方法和构造方法的访问方式和普通类一样。

由于抽象类不能实例化对象，所以抽象类必须被继承，才能被使用。也是因为这个原因，通常在设计阶段决定要不要设计抽象类。

父类包含了子类集合的常见的方法，但是由于父类本身是抽象的，所以不能使用这些方法。

在 Java 中抽象类表示的是一种继承关系，一个类只能继承一个抽象类，而一个类却可以实现多个接口。

#### 抽象类

在 Java 语言中使用 abstract class 来定义抽象类。

#### 抽象方法

如果你想设计这样一个类，该类包含一个特别的成员方法，该方法的具体实现由它的子类确定，那么你可以在父类中声明该方法为抽象方法。

Abstract 关键字同样可以用来声明抽象方法，抽象方法只包含一个方法名，而没有方法体。

抽象方法没有定义，方法名后面直接跟一个分号，而不是花括号。

```
public abstract class Employee
{
   private String name;
   private String address;
   private int number;
   
   public abstract double computePay();
   
   //其余代码
}
```

声明抽象方法会造成以下两个结果：
* 如果一个类包含抽象方法，那么该类必须是抽象类。
* 任何子类必须重写父类的抽象方法，或者声明自身为抽象类。

继承抽象方法的子类必须重写该方法。否则，该子类也必须声明为抽象类。
最终，必须有子类实现该抽象方法，否则，从最初的父类到最终的子类都不能用来实例化对象。

#### 抽象类总结规定

1. 抽象类不能被实例化(初学者很容易犯的错)，如果被实例化，就会报错，编译无法通过。只有抽象类的非抽象子类可以创建对象。
2. 抽象类中不一定包含抽象方法，但是有抽象方法的类必定是抽象类。
3. 抽象类中的抽象方法只是声明，不包含方法体，就是不给出方法的具体实现也就是方法的具体功能。
4. 构造方法，类方法（用 static 修饰的方法）不能声明为抽象方法。
5. 抽象类的子类必须给出抽象类中的抽象方法的具体实现，除非该子类也是抽象类。

### 封装

在面向对象程式设计方法中，封装（英语：Encapsulation）是指一种将抽象性函式接口的实现细节部分包装、隐藏起来的方法。

封装可以被认为是一个保护屏障，防止该类的代码和数据被外部类定义的代码随机访问。

要访问该类的代码和数据，必须通过严格的接口控制。

封装最主要的功能在于我们能修改自己的实现代码，而不用修改那些调用我们代码的程序片段。

适当的封装可以让程式码更容易理解与维护，也加强了程式码的安全性。

封装的优点
1. 良好的封装能够减少耦合。
2. 类内部的结构可以自由修改。
3. 可以对成员变量进行更精确的控制。
4. 隐藏信息，实现细节。

#### 实现Java封装的步骤

1、修改属性的可见性来限制对属性的访问（一般限制为private），例如
```
public class Person {
    private String name;
    private int age;
}
```

这段代码中，将 name 和 age 属性设置为私有的，只能本类才能访问，其他类都访问不了，如此就对信息进行了隐藏。 

2、对每个值属性提供对外的公共方法访问，也就是创建一对赋取值方法，用于对私有属性的访问，例如：
```
public class Person{
    private String name;
    private int age;
​
    public int getAge(){
      return age;
    }
​
    public String getName(){
      return name;
    }
​
    public void setAge(int age){
      this.age = age;
    }
​
    public void setName(String name){
      this.name = name;
    }
}
```

采用 this 关键字是为了解决实例变量（private String name）和局部变量（setName(String name)中的name变量）之间发生的同名的冲突。


### 接口

接口（英文：Interface），在JAVA编程语言中是一个抽象类型，是抽象方法的集合，接口通常以interface来声明。
一个类通过继承接口的方式，从而来继承接口的抽象方法。

接口并不是类，编写接口的方式和类很相似，但是它们属于不同的概念。类描述对象的属性和方法。接口则包含类要实现的方法。

除非实现接口的类是抽象类，否则该类要定义接口中的所有方法。

接口无法被实例化，但是可以被实现。一个实现接口的类，必须实现接口内所描述的所有方法，否则就必须声明为抽象类。
另外，在 Java 中，接口类型可用来声明一个变量，他们可以成为一个空指针，或是被绑定在一个以此接口实现的对象。

接口与类相似点：
* 一个接口可以有多个方法。
* 接口文件保存在 .java 结尾的文件中，文件名使用接口名。
* 接口的字节码文件保存在 .class 结尾的文件中。
* 接口相应的字节码文件必须在与包名称相匹配的目录结构中。

接口与类的区别：
* 接口不能用于实例化对象。
* 接口没有构造方法。
* 接口中所有的方法必须是抽象方法，Java 8 之后 接口中可以使用 default 关键字修饰的非抽象方法。
* 接口不能包含成员变量，除了 static 和 final 变量。
* 接口不是被类继承了，而是要被类实现。
* 接口支持多继承。

接口特性
* 接口中每一个方法也是隐式抽象的,接口中的方法会被隐式的指定为 public abstract（只能是 public abstract，其他修饰符都会报错）。
* 接口中可以含有变量，但是接口中的变量会被隐式的指定为 public static final 变量（并且只能是 public，用 private 修饰会报编译错误）。
* 接口中的方法是不能在接口中实现的，只能由实现接口的类来实现接口中的方法。

抽象类和接口的区别
1. 抽象类中的方法可以有方法体，就是能实现方法的具体功能，但是接口中的方法不行。
2. 抽象类中的成员变量可以是各种类型的，而接口中的成员变量只能是 public static final 类型的。
3. 接口中不能含有静态代码块以及静态方法(用 static 修饰的方法)，而抽象类是可以有静态代码块和静态方法。
4. 一个类只能继承一个抽象类，而一个类却可以实现多个接口。

- 注：JDK 1.8 以后，接口里可以有静态方法和方法体了。
- 注：JDK 1.8 以后，接口允许包含具体实现的方法，该方法称为"默认方法"，默认方法使用 default 关键字修饰。更多内容可参考 Java 8 默认方法。
- 注：JDK 1.9 以后，允许将方法定义为 private，使得某些复用的代码不会把方法暴露出去。更多内容可参考 Java 9 私有接口方法。

#### 接口的声明

```
[可见度] interface 接口名称 [extends 其他的接口名] {
        // 声明变量
        // 抽象方法
}
```

#### 接口的实现

当类实现接口的时候，类要实现接口中所有的方法。否则，类必须声明为抽象的类。

```
...implements 接口名称[, 其他接口名称, 其他接口名称..., ...] ...
```

#### 接口的继承

一个接口能继承另一个接口，和类之间的继承方式比较相似。接口的继承使用extends关键字，子接口继承父接口的方法。

#### 标记接口

最常用的继承接口是没有包含任何方法的接口。

标记接口是没有任何方法和属性的接口.它仅仅表明它的类属于一个特定的类型,供其他代码来测试允许做一些事情。

标记接口作用：简单形象的说就是给某个对象打个标（盖个戳），使对象拥有某个或某些特权。

没有任何方法的接口被称为标记接口。标记接口主要用于以下两种目的：

* 建立一个公共的父接口：

正如EventListener接口，这是由几十个其他接口扩展的Java API，你可以使用一个标记接口来建立一组接口的父接口。
例如：当一个接口继承了EventListener接口，Java虚拟机(JVM)就知道该接口将要被用于一个事件的代理方案。

* 向一个类添加数据类型：

这种情况是标记接口最初的目的，实现标记接口的类不需要定义任何接口方法(因为标记接口根本就没有方法)，
但是该类通过多态性变成一个接口类型。

### 枚举

Java 枚举是一个特殊的类，一般表示一组常量。

Java 枚举类使用 enum 关键字来定义，各个常量使用逗号 , 来分割。

enum 定义的枚举类默认继承了 java.lang.Enum 类，并实现了 java.lang.Serializable 和 java.lang.Comparable 两个接口。

values(), ordinal() 和 valueOf() 方法位于 java.lang.Enum 类中：
* values() 返回枚举类中所有的值。
* ordinal()方法可以找到每个枚举常量的索引，就像数组索引一样。
* valueOf()方法返回指定字符串值的枚举常量。

### 包(package)

为了更好地组织类，Java 提供了包机制，用于区别类名的命名空间。

包的作用

* 1、把功能相似或相关的类或接口组织在同一个包中，方便类的查找和使用。
* 2、如同文件夹一样，包也采用了树形目录的存储方式。同一个包中的类名字是不同的，不同的包中的类的名字是可以相同的，
当同时调用两个不同包中相同类名的类时，应该加上包名加以区别。因此，包可以避免名字冲突。
* 3、包也限定了访问权限，拥有包访问权限的类才能访问某个包中的类。

Java 使用包（package）这种机制是为了防止命名冲突，访问控制，提供搜索和定位类（class）、接口、枚举（enumerations）和注释（annotation）等。 

包语句的语法格式为：
```
package pkg1[．pkg2[．pkg3…]];
```

一个包（package）可以定义为一组相互联系的类型（类、接口、枚举和注释），为这些类型提供访问保护和命名空间管理的功能。

以下是一些 Java 中的包：
* java.lang-打包基础的类
* java.io-包含输入输出功能的函数

开发者可以自己把一组类和接口等打包，并定义自己的包。而且在实际开发中这样做是值得提倡的，
当你自己完成类的实现之后，将相关的类分组，可以让其他的编程者更容易地确定哪些类、接口、枚举和注释等是相关的。

由于包创建了新的命名空间（namespace），所以不会跟其他包中的任何名字产生命名冲突。
使用包这种机制，更容易实现访问控制，并且让定位相关类更加简单。 

#### 创建包

创建包的时候，你需要为这个包取一个合适的名字。之后，如果其他的一个源文件包含了这个包提供的类、接口、枚举或者注释类型的时候，都必须将这个包的声明放在这个源文件的开头。

包声明应该在源文件的第一行，每个源文件只能有一个包声明，这个文件中的每个类型都应用于它。

如果一个源文件中没有使用包声明，那么其中的类，函数，枚举，注释等将被放在一个无名的包（unnamed package）中。

#### import 关键字

为了能够使用某一个包的成员，我们需要在 Java 程序中明确导入该包。使用 "import" 语句可完成此功能。

在 java 源文件中 import 语句应位于 package 语句之后，所有类的定义之前，可以没有，也可以有多条，其语法格式为：
```
import package1[.package2…].(classname|*);
```

如果在一个包中，一个类想要使用本包中的另一个类，那么该包名可以省略。 

#### package 的目录结构 

类放在包中会有两种主要的结果：
* 包名成为类名的一部分，正如我们前面讨论的一样。
* 包名必须与相应的字节码所在的目录结构相吻合。

通常，一个公司使用它互联网域名的颠倒形式来作为它的包名.例如：互联网域名是 runoob.com，所有的包名都以 com.runoob 开头。
包名中的每一个部分对应一个子目录。 

类目录的绝对路径叫做 class path。设置在系统变量 CLASSPATH 中。
编译器和 java 虚拟机通过将 package 名字加到 class path 后来构造 .class 文件的路径。

#### 设置 CLASSPATH 系统变量

用下面的命令显示当前的CLASSPATH变量：
* Windows 平台（DOS 命令行下）：`C:\> set CLASSPATH`
* UNIX 平台（Bourne shell 下）：`# echo $CLASSPATH`

删除当前CLASSPATH变量内容：
* Windows 平台（DOS 命令行下）：`C:\> set CLASSPATH=`
* UNIX 平台（Bourne shell 下）：`# unset CLASSPATH; export CLASSPATH`

设置CLASSPATH变量:
* Windows 平台（DOS 命令行下）： `C:\> set CLASSPATH=C:\users\jack\java\classes`
* UNIX 平台（Bourne shell 下）：`# CLASSPATH=/home/jack/java/classes; export CLASSPATH`

## Java 高级教程

内容 详见 <https://ibaiyang.github.io/blog/java/2022/07/09/Java语言-高级教程.html>

## 其他

学习流程：Spring  ->  SpringMVC  ->  MyBatis   ->   SpringBoot   ->   SpringCloud   

【尚硅谷】SpringMVC教程 <https://www.bilibili.com/video/BV1Ry4y1574R>

Spring 官网 <https://spring.io>





<br/><br/><br/><br/><br/>
## 参考资料

Java语言RUNOOB教程 <https://www.runoob.com/java/java-tutorial.html>

Java教程 <https://www.runoob.com/java/java-intro.html>

Java 面向对象课程 <https://www.runoob.com/java/java-inheritance.html>

Java 高级课程 <https://www.runoob.com/java/java-data-structures.html>

Java 测验 <https://www.runoob.com/quiz/java-quiz.html>

廖雪峰Java教程 <https://www.liaoxuefeng.com/wiki/1252599548343744>

重学Java设计模式 <https://blog.51cto.com/u_14943622/2838632>

北京尚学堂-百战程序员-Java 视频教程全集-80 小时从入门到精通 <https://www.bilibili.com/video/BV1Jt411g7g4>

马士兵教育-价值11980的Java零基础入门全套视频 <https://www.bilibili.com/video/BV1RK4y1g7A5>

狂神说Java-SpringBoot最新教程IDEA版通俗易懂 <https://www.bilibili.com/video/BV1PE411i7CV>

kuangstudy <https://www.kuangstudy.com/>

使用Docker搭建Java环境的步骤方法 <http://www.yinxi.net/doc/show_10717.html>


