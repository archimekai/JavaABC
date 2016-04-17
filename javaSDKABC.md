#阅读JDK文档及源代码并简要回答以下问题
注：JDKAPI文档（http://docs.oracle.com/javase/8/docs/api/index.html或从http://cf.pku.cn/tds/java下载chm格式的）
 JDK的源代码（一般在 C:\Program Files\Java\jdk1.8.0\src.zip）中

# TODO personal info

## -----java.lang.Object类-----
1. 其equals与==有没有差别？

	【doc中未能找到==，而且也看不出equals和==有没有明显的区别】

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
	返回一个字符串，如果是接口，则会输出`interface `+`getName()`的返回值；如果是基本类型，则会直接返回`getName()`；如果是正常的类，则会返回`class `+`getName()`。
	`getName()`会返回Class类中的成员`name`
9.	newInstance()中执行了什么？
	`newInstance()`返回Class object所表示的类型的一个新实例。
	其执行步骤如下：
	首先，如果System.getSecurityManager()非空，则使用checkMemberAccess来检查成员的访问性。
	*这都是些什么鬼！！*
	然后，如果已经有缓存过的构造器，则...
	
	

-----java.lang.String类-----
10.	为什么说String是immutable的？为什么不要多次循环中使用+=？ 其value是加了哪个修饰语？其replace、append、trim等方法返回的值是什么？
	1. 为什么说String是immutable的？
		1. 不可变对象可以提高String Pool的效率和安性。如果一个对象不可变，那么需要复制内容的时候，也就只需要复制其地址，复制地址的效率通常是很高的。
		2. 不可变对象对于多线程是安全的
	2. 为什么循环中不能多次使用+=
		每一次+=，都需要先新建对象，并更改引用，从而增大系统开销。
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
	String的hashCode 的计算利用了乘方运算，（能说更为安全吗？）
	*这里写的不好*
12.	equals方法是判断内容相等还是引用相等？compareTo是比较什么？
	首先判断引用是否相同，如果引用相同，则返回`true`，否则，如果对象是String类型的，则进一步比较内容是否相同，相同的情况下，就返回`true`，否则返回`false`。
	compareTo按照字典顺序进行比较。
13.	intern()方法的注释表明什么样的字符串一定是放到一个池子中的？
	所有“literal string”都在String Pool中
	"literal"代表字符串，数值等值等于本身。常量表达式计算出来的是“literal”。
14.	其indexOf使用了什么样的算法流程？
	首先判断起始位置fromIndex是否在合法的范围，然后，从起始位置开始逐个遍历字符串（内容实际上存储在了char[]中），依次检查是否为所查找的字符，如果是，则返回其下标，如果到最后都没有发现，就返回-1。此外，如果所查找的字符ch>=Character.MIN_SUPPLEMENTARY_CODE_POINT,则调用indexOfSupplementary(int ch, int fromIndex)进行查找。该函数中将字符分为高位和低位进行匹配。
	*为什么要有这个MIN_SUPPLEMENTARY_CODE_POINT呢？*
15.	substring函数会抛出什么样的异常，这个异常是不是一定要捕获？
	
	`IndexOutOfBoundsException`。这个异常需要捕获。
	*为什需要捕获？*
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

	返回StringBuffer。好处：确保运算过程中类型的统一，使用StringBuffer类也有助于降低系统的开销。
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
25.	IntegerCache是起什么作用的？valueOf(int i)表明什么范围内的int在boxing后会缓存？
26.	hashCode是怎样算的？

-----java.lang.Math类-----
27.	举几个方法是native的方法。

-----java.util.Random类-----
28.	构造函数用什么进行了初始化？
29.	随机数的公式是用了什么?

-----java.math.BigInteger类-----
30.	判断一个大数是否可能为素数，有两个passXXX函数，分别是什么？
31.	内部是用什么来表示大整数的
-----java.util.Arrays类-----
32.	sort(int[])是使用什么排序方法？
33.	sort(Object[] a)是使用什么排序方法？
-----java.util.ArrayList类-----
34.	grow()的代码中可以看出每次容量增加多少倍？
-----java.util.Vector类-----
35.	grow()的代码中可以看出每次容量增加多少倍？
-----java.util.Stack类-----
36.	Stack类继承了什么类？
37.	push()与pop()进行了什么操作？
-----java.util.Hashtable类-----
38.	其内部类class Entry含有哪4个字段？
-----java.util.TreeMap类-----
39.	containsKey()的代码中可以看出要找到对象的条件是什么？如果一个Key对象的hashCode是变化的（如随着内容而算出来的），这个Key对象还会找到吗？
-----java.util.TreeSet类-----
40.	TreeSet的底层是用什么对象来实现的？
TreeMap
-----java.util.Timer类-----
41.	Timer的底层是如何实现重复执行的？
-----javax.swing.Timer类-----
42.	Timer的底层是在哪里调用SwingUtilities.invokeLater的？


补充问题
java.util.Arrays
从copyOf方法来看，如何实现带泛型的数组的实例化


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

