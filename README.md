#JAVA ABC: java中常见的小问题
注：JDKAPI文档（http://docs.oracle.com/javase/8/docs/api/index.html或从http://cf.pku.cn/tds/java下载chm格式的）
 JDK的源代码（一般在 C:\Program Files\Java\jdk1.8.0\src.zip）中

### TODO personal info

## -----java.lang.Object类-----
1. 其equals与==有没有差别？


	有区别

	==操作比较了两个变量的值是否相等。对于基本变量类型，其比较的是两个变量的值是否相等。对于引用变量类型，其返回两个引用是否相等，也即，其所指向的堆中的地址是否相同，也即栈中的内容是否相同。

	equals方法比较两个变量所引用的对象的内容是否相同，也即堆中的内容是否相同。

	对于基本变量类型，equals和==的结果没有区别。

2.	其hashCode及clone前面有个什么修饰语？

	`native`

	`native`关键字说明其修饰的方法是一个原生方法，方法的实现不是在当前文件，而是在用其他语言（如C、C++）中实现的。

3.	其toString()返回什么？

	`String` 字符串

	具体来说，Object中的`toString()`方法返回类名称和以16进制显示的该实例的hashCode
	
4.	其构造函数及finalize()做了什么？
	
	构造函数中什么也没做。

	`finalize()`中什么也没做。


##-----java.lang.Class类-----
5.	为什么说Class类不能被继承

	Class类的定义中有`final`关键字，该关键字使Class类不能被继承。
6.	为什么说我们不能 new Class()?

	*为什么呢*
	因为Class中仅有的构造方法`private Class(ClassLoader loader)`是一个私有方法，无法被调用。
7.	forName()中执行了什么流程？

	使用给定的class loader和类名称来得到相应的`Class` object。其中调用了`forName0()`这一私有native方法来完成任务。
8.	toString()返回什么？

	返回一个字符串，如果是接口，则会输出`“interface” `+`getName()`的返回值；如果是基本类型，则会直接返回`getName()`；如果是正常的类，则会返回`“class” `+`getName()`。
	`getName()`会返回Class类中的成员`name`

9.	newInstance()中执行了什么？

	`newInstance()`返回Class object所表示的类型的一个新实例。
	其执行步骤如下：
	首先，如果System.getSecurityManager()非空，则使用checkMemberAccess来检查成员的访问性。

	*这都是些什么鬼！！*

	然后，如果没有已经缓存过的构造器，则检查其是否恰好为Class.class，如果是的话，由于class在设计上就不能被构造，所以抛出异常`IllegalAccessException`。检查通过之后，就去获取member.declared的constructor，并将其存入cachedConstructor
	
	现在确保了已经将构造器缓存出来，然后利用反射机制进行检查，然后调用构造器的newInstance方法。
	
	

##-----java.lang.String类-----
10.	为什么说String是immutable的？为什么不要多次循环中使用+=？ 其value是加了哪个修饰语？其replace、append、trim等方法返回的值是什么？
	1. 为什么说String是immutable的？
		1. 不可变对象可以提高String Pool的效率和安性。如果一个对象不可变，那么需要复制内容的时候，也就只需要复制其地址，复制地址的效率通常是很高的。
		2. 不可变对象对于多线程是安全的
	2. 为什么循环中不能多次使用+=
	
		每一次+=，都需要先新建StringBuilder对象，并更改引用，从而增大系统开销。

		> 从编译的代码来看，String+的准确操作是： 
		> 
		> new StringBuilder() 
		> 
		> new String.valueof() 

		> StringBuilder.<init> 
		> 
		> StringBuilder.append() 
		> 
		> StringBuilder.toString() 
	3. value前增加的修饰语？
		其value系列函数前增加了`static`修饰语，表明返回的对象是不可变的
		
		*为什么要返回static string呢？*
	4. 其replace、append、trim等方法返回的值是什么？
		返回的是`String`类型的值。

		
11.	hashCode使用了什么公式，试比较一下它与身份证验证码、ISBN（国际标准书号）、银行卡号的验证码的生成算法有何不同？

	hashCode使用的公式如下： `s[0]*31^(n-1) + s[1]*31^(n-2) + ... + s[n-1]`
	其中`n`为字符串长度，`s[i]`是字符串中第i个字符。
	* 身份证校验码：身份证校验码是身份证号的最后一位，其计算方法如下：
		1、将前面的身份证号码17位数分别乘以不同的系数。从第一位到第十七位的系数分别为：7 9 10 5 8 4 2 1 6 3 7 9 10 5 8 4 2 ；
		2、将这17位数字和系数相乘的结果相加；
		3、用加出来和除以11，看余数是多少；
		4、余数只可能有0 1 2 3 4 5 6 7 8 9 10这11个数字。其分别对应的最后一位身份证的号码为1 0 X 9 8 7 6 5 4 3 2；
	* ISBN计算方法：
		1.假设某13位ISBN号码前15位(包含‘-’符号）是：987-7-309-04547；      
		2.位置为123-4-567-89(10)(11)(12)      
		3.计算加权和S：S＝9×1＋8×3＋7×1＋7×3＋3×1＋0×3＋9×1＋0×3＋4×1＋5×3＋4×1＋7×3 = 117；      
		4.计算S÷10的余数M：M = 117 mod 10 = 7；      
		5.计算10－M的差N：N = 10 ? 7 = 3     (如果10－M的值为10则校验码取0) 
	* 银行卡号的验证码
	
	不同点：String的hashCode的计算没有明显地取余数，而是直接利用了int类型数值的范围？？
	String的hashCode 的计算利用了乘方运算，比其它的计算方式更为复杂，更不容易出现相等的情况。
	*这里写的不好*
12.	equals方法是判断内容相等还是引用相等？compareTo是比较什么？

	首先判断引用是否相同，如果引用相同，则返回`true`，否则，如果对象是String类型的，则进一步比较内容是否相同，相同的情况下，就返回`true`，否则返回`false`。
	compareTo按照字典顺序进行比较。

13.	intern()方法的注释表明什么样的字符串一定是放到一个池子中的？

	所有“literal string”都在String Pool中
	"literal"代表字符串，数值等值等于本身。常量表达式计算出来的是“literal”。
14.	其indexOf使用了什么样的算法流程？

	首先判断起始位置fromIndex是否在合法的范围，然后，从起始位置开始逐个遍历字符串（内容实际上存储在了char[]中），依次检查是否为所查找的字符，如果是，则返回其下标，如果到最后都没有发现，就返回-1。此外，如果所查找的字符ch>=Character.MIN_SUPPLEMENTARY_CODE_POINT,则其为增补字符，调用indexOfSupplementary(int ch, int fromIndex)进行查找。该函数中将字符分为高位和低位进行匹配。

	*为什么要有这个MIN_SUPPLEMENTARY_CODE_POINT呢？*
15.	substring函数会抛出什么样的异常，这个异常是不是一定要捕获？
	
	`IndexOutOfBoundsException`。这个异常是`RuntimeException`的子类，所以可以不捕获。

	> ###补充：关于java中的异常
	> [点击以查看引用源](http://blog.csdn.net/hguisu/article/details/6155636)
	> 
	> `Throwable`有两个重要的子类：Exception和Error。
	> 
	> * Error（错误）:是程序无法处理的错误，表示运行应用程序中较严重问题。大多数错误与代码编写者执行的操作无关，而表示代码运行时 JVM（Java 虚拟机）出现的问题。例如，Java虚拟机运行错误（Virtual MachineError），当 JVM 不再有继续执行操作所需的内存资源时，将出现 OutOfMemoryError。这些异常发生时，Java虚拟机（JVM）一般会选择线程终止。这些错误表示故障发生于虚拟机自身、或者发生在虚拟机试图执行应用时，如Java虚拟机运行错误（Virtual MachineError）、类定义错误（NoClassDefFoundError）等。这些错误是不可查的，因为它们在应用程序的控制和处理能力之 外，而且绝大多数是程序运行时不允许出现的状况。对于设计合理的应用程序来说，即使确实发生了错误，本质上也不应该试图去处理它所引起的异常状况。在 Java中，错误通过Error的子类描述。
	> *  Exception（异常）:是程序本身可以处理的异常。Exception 类有一个重要的子类 RuntimeException。RuntimeException 类及其子类表示“JVM 常用操作”引发的错误。
	> 
	> Java的异常(包括Exception和Error)还分为可查的异常（checked exceptions）和不可查的异常（unchecked exceptions）。
	> 
    > * 可查异常（编译器要求必须处置的异常）：正确的程序在运行中，很容易出现的、情理可容的异常状况。可查异常虽然是异常状况，但在一定程度上它的发生是可以预计的，而且一旦发生这种异常状况，就必须采取某种方式进行处理。 除了RuntimeException及其子类以外，其他的Exception类及其子类都属于可查异常。这种异常的特点是Java编译器会检查它，也就是说，当程序中可能出现这类异常，要么用try-catch语句捕获它，要么用throws子句声明抛出它，否则编译不会通过。

    > * 不可查异常(编译器不要求强制处置的异常):包括运行时异常（RuntimeException与其子类）和错误（Error）
    > 
    > Exception还可以分为运行时异常和非运行时异常（编译异常）。
    > 
    > *  运行时异常：都是RuntimeException类及其子类异常，如NullPointerException(空指针异常)、IndexOutOfBoundsException(下标越界异常)等，这些异常是不检查异常，程序中可以选择捕获处理，也可以不处理。这些异常一般是由程序逻辑错误引起的，程序应该从逻辑角度尽可能避免这类异常的发生。 运行时异常的特点是Java编译器不会检查它，也就是说，当程序中可能出现这类异常，即使没有用try-catch语句捕获它，也没有用throws子句声明抛出它，也会编译通过。
    > *  非运行时异常 （编译异常）：是RuntimeException以外的异常，类型上都属于Exception类及其子类。从程序语法角度讲是必须进行处理的异常，如果不处理，程序就不能编译通过。如IOException、SQLException等
	

16.	concat函数使用了哪个类的什么方法，这个方法中最终又是调用了什么类的什么方法？

	使用了`Arrays.copyOf()`方法（最终调用了Array.newInstance, new Object[]）和`String.getChars()`方法，后者调用了System.arraycopy()方法
	
17.	replaceAll中借用了什么类来实现？valueOf及format呢？

	* 借用了`Matcher`类来实现，而Matcher又是通过Pattern类的compile()方法获得的。
	* valueOf使用了object类的toString()方法以及String类的诸多构造函数。
	* format类使用了Formatter类的format方法
##-----java.lang.StringBuilder 及 AbstractStringBuilder类-----

18.	StringBuilder的初始内存是多少字符？

	16
19.	expandCapacity()方法是在原来的基础上扩展多少？

	旧容量 + 2，也即expandCapacity()将容量扩展为（旧容量 + 1）*2
20.	insert()、append()、delete()等方法的返回值是什么，这有什么好处？

	返回StringBuffer。好处：确保运算过程中类型的统一，StringBuffer为可变类，使用StringBuffer类也有助于降低系统的开销。

	*但是还不是真正明白*

##-----java.lang.Integer类-----
21.	value字段前面加了什么修饰词？为什么说Integer是immutable的？
	
	`static`。因为其成员都是final类型。

	*似乎要从如何判断immutable入手，如何回答呢？*
22.	其常数如MAX_VALUE前面用了什么修饰词？

	`@Native public static final`。static表明其为静态量，final表明其在生命之后就不能修改。@Native 表明应该从native code 的形式得到其值。
23.	toString(int i)用了什么办法加快其计算的速度？

	这个方法并没有直接调用toString(int i, int radix)，而是另行编写了算法。关键在于调用getChars函数，当i大于等于65536时，每次处理两位，当i较小时，采用另外的快速算法。为了进一步加快速度，预先还将char类型的'0'到‘9’存储了起来。
24.	parseInt函数会抛出什么样的异常，这个异常是不是一定要捕获？

	`NumberFormatException`。其是`RuntimeException`的子类，所以可以不捕获。
25.	IntegerCache是起什么作用的？valueOf(int i)表明什么范围内的int在boxing后会缓存？
	* IntegerCache会将`IntegerCache.low`到`IntegerCache.high`（包括端点）之间的整数对象预先存储起来，以节省内存，降低创建和销毁对象的开销。
	* 一般来说，在-128到127范围内的整数会被缓存。准确地说，是在`IntegerCache.low`到`IntegerCache.high`之间的数值。
26.	hashCode是怎样算的？

	直接返回整数的数值。

##-----java.lang.Math类-----
27.	举几个方法是native的方法。

	如`sin()`,`cos()`,`tan()`等，`sqrt()`,`cbrt()`等。
##-----java.util.Random类-----
28.	构造函数用什么进行了初始化？
	
	构造函数使用长整数类型的种子进行了初始化，具体来说，在调用无参数构造函数时，使用`seedUniquifier() ^ System.nanoTime()`来获得种子。
29.	随机数的公式是用了什么?

	产生新的随机数时，会使用下面的值更新种子：`(seed * 0x5DEECE66DL + 0xBL) & ((1L << 48) - 1)`，然后返回` (int)(seed >>> (48 - bits))`作为所产生的随机数。

##-----java.math.BigInteger类-----
30.	判断一个大数是否可能为素数，有两个passXXX函数，分别是什么？
	* `private boolean passesMillerRabin(int iterations, Random rnd)`
	* `private boolean passesLucasLehmer()` 

31.	内部是用什么来表示大整数的

	整数数组
	*是吗？*
	
##-----java.util.Arrays类-----
32.	sort(int[])是使用什么排序方法？

	内部调用了`DualPivotQuicksort.sort()`，其算法为Yaroslavskiy, Jon Bentley, and Joshua Bloch提出的Dual-Pivot Quicksort算法。对于普通的（one-pivot）Quicksort算法，很多数据集会导致其复杂度增加到O(n^2)，但大部分情况下该算法仍保持O(n log(n)) 的复杂度。
33.	sort(Object[] a)是使用什么排序方法？

	为稳定，适应性，递归的归并排序算法。	并且，在输入序列基本有序时，算法仅需要大约n次比较。

	适合于将两个已经排序的数组进行归并。

	内部调用：分为两种情况，如果用户要求使用LegacyMergeSort，则调用`legacyMergeSort()`，否则就使用`ComparableTimSort.sort()`。
##-----java.util.ArrayList类-----
34.	grow()的代码中可以看出每次容量增加多少倍？

	增加到原来的1.5倍。
##-----java.util.Vector类-----
35.	grow()的代码中可以看出每次容量增加多少倍？

	如果未指定`capacityIncrement`，则每次增加到原来的两倍。否则，每次增加`capacityIncrement`个空间。

	*为什么vector和arraylist的增长倍数不同呢？*
##-----java.util.Stack类-----
36.	Stack类继承了什么类？

	`Vector`类
37.	push()与pop()进行了什么操作？

	* `push()`直接调用了Vector的`addElement()`方法。
	* `pop()`则先将`Vector.peek()`保存留待最后返回，然后调用`removeElementAt(len - 1)`方法。
##-----java.util.Hashtable类-----
38.	其内部类class Entry含有哪4个字段？

	```java    
		final int hash;
        final K key;
        V value;
        Entry<K,V> next;
	```

##-----java.util.TreeMap类-----
39.	containsKey()的代码中可以看出要找到对象的条件是什么？如果一个Key对象的hashCode是变化的（如随着内容而算出来的），这个Key对象还会找到吗？

	`getEntry(key)`的返回不是`null`。而在`getEntry()`中，区分了两种情况：如果存在comparator，则调用getEntryUsingComparator(key)，否则，依次使用k.compareTo()进行比较，直到找到相等的key值，如果不能找到，就返回null。getEntryUsingComparator中也是类似的算法，不过其中调用的比较函数是compare(T,T)。
	
	是否能找到取决于有关的比较函数中是否依据hashCode进行比较。由于TreeMap以有序链表*需要确认*的形式存储数据，所以如果hashCode发生变化，会导致原来的排序失效，可能就找不到本来存在的元素了。

	*这里的解释正确吗？*
##-----java.util.TreeSet类-----
40.	TreeSet的底层是用什么对象来实现的？

	TreeMap
##-----java.util.Timer类-----
41.	Timer的底层是如何实现重复执行的？

	调用了sched()私有方法，在该方法中使用了一个队列，通过在队列中添加和提取任务执行时间来实现重复执行。

	*还没看懂*
##-----javax.swing.Timer类-----
42.	Timer的底层是在哪里调用SwingUtilities.invokeLater的？

	在`void post() `中调用。
## 补充问题

java.util.Arrays

43. 从copyOf方法来看，如何实现带泛型的数组的实例化?

	```java
    @SuppressWarnings("unchecked")
    public static <T> T[] copyOf(T[] original, int newLength) {
        return (T[]) copyOf(original, newLength, original.getClass());
    }

    public static <T,U> T[] copyOf(U[] original, int newLength, Class<? extends T[]> newType) {
        @SuppressWarnings("unchecked")
        T[] copy = ((Object)newType == (Object)Object[].class)
            ? (T[]) new Object[newLength]
            : (T[]) Array.newInstance(newType.getComponentType(), newLength);
        System.arraycopy(original, 0, copy, 0,
                         Math.min(original.length, newLength));
        return copy;
    }
	```
	需要借助强制类型转换。具体来说，在创建数组时，进行比较，(Object)newType == (Object)Object[].class，如果相同，则直接将new得到的数组转换为T[]，否则就调用 Array.newInstance (newType.getComponentType(), newLength),然后再做强制类型转换。

	*不懂*
