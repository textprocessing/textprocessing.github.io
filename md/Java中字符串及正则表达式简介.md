[TOC]

## Java中字符串及正则表达式简介

### 1. 字符串（String）简介

#### 1.1 创建字符串

方式1: String str1 = “abc”

方式2: String str2 = new String(“abc”)

其中new String()中的参数形式可以有多种 如:String(bytes[] byte) 接受一个字节数组 String(char[] value) 接受一个字符数组 String(String str) 接受一个字符串对象 使用字节数组或者字符数组都可以构建字符串对象

```java
String str1 = "abc";
String str2 = new String("abc");
byte[] bt = {97,98,99};
String str3 = new String(bt);
char[] ch = {'a','b','c'};
String str4 = new String(ch);
System.out.println("str1 = "+ str1 +" str2 = " +str2);
System.out.println("str3 = "+ str3 +" str4 = " +str4);
```

结果：

str1 = abc str2 = abc
str3 = abc str4 = abc

#### 1.2 基本操作

##### 1.2.1 判断两个字符串内容是否一致

1. str1 == str2 判别的是两个字符串的内存地址 
2. str1.equals(str2) 判别的是两个字符串的内容

```java
String str1 = "hello";
String str2 = new String("hello");
System.out.println("str1 == str2?" +(str1 ==str2));
System.out.println("str1，str2内容是否相同？"+(str1.equals(str2)));

```

结果：

str1 == str2?false
str1，str2内容是否相同？true

##### 1.2.2 获取方法

1. 获取字符串长度:int length() 长度即字符个数 
2. 获取特定位置的字符 char charAt(int index)
3. 获取特定字符的位置 int indexOf(String str) 返回子串第一次出现 的索引值,如果子串没出现在字符串中,则返回-1
4. 获取最后一个字符位置 int lastIndexOf(String str) 返回子串最后 一次出现的索引值,如果子串没出现在字符串中,则返回-1

```java
String str = "abc中国ab中国";
System.out.println("字符串的长度： " + str.length());
System.out.println("索引值3对应的字符： " + str.charAt(3));
System.out.println("子字符串第一次出现的位置： " + str.indexOf("中国"));
System.out.println("子字符串最后一次出现的位置： " + str.lastIndexOf("中国"));

```

结果：

字符串的长度： 9
索引值3对应的字符： 中
子字符串第一次出现的位置： 3
子字符串最后一次出现的位置： 7

##### 1.2.3判断方法

1. 是否以指定字符结束 boolean endsWith(String str) 
2. 是否长度为0 booleanisEmpty()
3. 是否包含指定序列 boolean contains(String str)
4. 是否内容一致 boolean equals(Object obj)
5. 忽略大小写是否内容一致booleanequalsIngoreCase(String str)

```java
String str = "Demo.java";
System.out.println("是否以指定字符串结束? " +str.endsWith("va"));
String str3 = "";
System.out.println("字符串是否为空？ " +str3.isEmpty());
System.out.println("字符串是否包含指定内容？ " +str.contains("java"));
System.out.println("字符串内容是否一致（忽略大小写）?" +str.equalsIgnoreCase("DEMO.JAVA"));

```

结果：

是否以指定字符串结束? true
字符串是否为空？ true
字符串是否包含指定内容？ true
字符串内容是否一致（忽略大小写）? true

##### 1.2.4 转换方法

1. 将字符串转换为字符数组 char[] toCharArray()


2. 将字符串转换为字节数组 byte[] getBytes()

```java
String str = "Demo.java";
char[] ch = str.toCharArray();
byte[] bt = str.getBytes();
System.out.println("字符数组内容： " +Arrays.toString(ch));
System.out.println("字节数组内容： " +Arrays.toString(bt));
```

结果：

字符数组内容： [D, e, m, o, ., j, a, v, a]
字节数组内容： [68, 101, 109, 111, 46, 106, 97, 118, 97]

##### 1.2.5 其它方法

1. 替换 String replace(String target, String replacement) 顺序替换
2. 切割 String[] split(String str)
3. 截取子串 String substring(int begin, int end) 取头舍尾
4. 转大写 String toUpperCase()
5. 转小写 String toLowerCase()
6. 去除空格 String trim() 去除字符串首尾的空格 (包括\t之类字符,但全角空格去不掉)

```java
String str = "abbbaabbaaa";
System.out.println("指定新内容替换旧内容： " +str.replace("aa", "cc"));
String str1 = "aa-cc-Ee-dd";
String str2 = "  sdasd\t";
String[] arr = str1.split("-");
System.out.println("字符串数组内容： " +Arrays.toString(arr));
System.out.println("从索引值处截取子串： " +str1.substring(3,5));
System.out.println("转大写： " +str1.toUpperCase());
System.out.println("转小写： " +str1.toLowerCase());
System.out.println("去除头尾空格： " +str2.trim());
```

结果：

从索引值处截取子串： cc
转大写： AA-CC-EE-DD
转小写： aa-cc-ee-dd
去除头尾空格： sdasd

#### 1.3 重要特性

字符串是常量,它们的值在创建后不能修改,字符串的内容一旦发生变化,那么马上会创建一个新的对象。

字符串的内容不适宜频繁修改,因为一旦修改马上就会创建一个新的对象。

如果需要频繁修改字符串的内容,建议使用字符串缓冲类(StringBuffer)。StringBuffer 其实就是一个存储字符的容器

```java
String str3 = "Hello";
String str4 = str3 + "World";
System.out.println("Str3和Str4在相同的存储位置吗？ " + (str3 == str4));

```

结果：

Str3和Str4在相同的存储位置吗？ false

#### 1.4 StringBuffer类

##### 1.4.1 构造对象:

StringBuffer sb = new StringBuffer() 

StringBuffer 底层是依赖了一个字符数组才能存储字符数据的,该字符数组默认的初始容量是16,如果字符数组的长度不够使用时,自动增长一倍。 

##### 1.4.2 基本操作:

增加 

append(xxx) 可添加任意类型的数据

Insert(int offset, xxx) 可插入任意类型的数据-String类没有该方法 

删除

delete(int start, int end) 根据指定的头尾索引值删除对应内容 - String类没有该方法

deleteCharAt(int index) 根据指定索引值删除一个字符

修改

replace(int start, int end, String str) 根据指定头尾索引值替换成指定内容

reverse() 翻转字符串缓冲类的内容

setCharAt(int index) 把指定索引值的字符替换指定字符 

substring(int start, int end) 根据指定索引值截取子串

查找 

indexOf(String str, int index)

capacity() 查看字符数组的长度(非已存储的字符 串长度)

length() 查看存储字符的个数lcharAt() 查看字符串指定位置的字符

toString() 把字符串缓冲类的内容转换成字符串返回 

总结：若对字符串有较多增删操作,使stringbuffer类更好

```java
StringBuffer sb = new StringBuffer();
sb.append("abc");
sb.append(true);
sb.append(3.14);
System.out.println("字符串缓冲类里的内容： " + sb);
sb.insert(5, "INS");
System.out.println("字符串缓冲类里的内容： " + sb);
sb.delete(2, 4);
System.out.println("字符串缓冲类里的内容： " + sb);
sb.deleteCharAt(3);
System.out.println("字符串缓冲类里的内容： " + sb);
sb.replace(3,5, "Nature");
System.out.println("字符串缓冲类里的内容： " + sb);
sb.reverse();
System.out.println("字符串缓冲类里的内容： " + sb);
System.out.println("字符串缓冲类里的内容： " + sb.substring(3,7));
int index = sb.indexOf("rba",2);
System.out.println("索引值为： " + index);
System.out.println("字符数组的长度： " + sb.capacity());
System.out.println("存储字符的长度： " + sb.length());
System.out.println("索引值指定的字符： " + sb.charAt(3));
```

结果：

字符串缓冲类里的内容： abctrue3.14
字符串缓冲类里的内容： abctrINSue3.14
字符串缓冲类里的内容： abrINSue3.14
字符串缓冲类里的内容： abrNSue3.14
字符串缓冲类里的内容： abrNatureue3.14
字符串缓冲类里的内容： 41.3euerutaNrba
字符串缓冲类里的内容： 3eue
索引值为： 12
字符数组的长度： 16
存储字符的长度： 15
索引值指定的字符： 3

#### 1.5 其它字符串缓冲类

StringBuilder类 与 StringBuffer类

相同点: 两个类都是字符串缓冲类,两个类方法都是一致的

不同点: StringBuffer是线程安全的,操作效率低 StringBuilder 是线程非安全的,操作效率高

### 2 正则表达式

正则表达式是用于操作字符串的一个规则,正则 表达式的规则使用了特殊的符号表示

#### 2.1 表示法则

##### 2.1.1 预定义字符类

.  任意字符

\D 非数字:［^0-9］

\d 数字: [0-9]

\s 空白字符:[\t\n\x0B\f\r] 

\S 非空白字符: ［^\s］

\w 单词字符: [a-zA-Z_0-9] 

\W 非单词字符: ［^\w］

```java
System.out.println("Any:"+"a".matches("."));
System.out.println("Digit:"+"1".matches("\\d"));
System.out.println("Nondigit:"+"a".matches("\\D"));
System.out.println("Blank:"+"\n".matches("\\s"));
System.out.println("NonBlank:"+"\n".matches("\\s"));
System.out.println("Word:"+"w".matches("\\w"));
System.out.println("Nonword:"+"#".matches("\\W"));
```

结果：

Any:true
Digit:true
Nondigit:true
Blank:true
NonBlank:true
Word:true
Nonword:true

##### 2.1.2 数量词

X?0 次或一次 

X+至少出现一次

X* 0次或者多次 

X{n} n 次

X{n,} n 次及以上

X{n,m} n~m 次

```java
System.out.println("<=1, ?:"+"a".matches("\\w?"));
System.out.println("0or>1, *:"+"".matches("\\d*")+"22".matches("\\d*"));
System.out.println(">=1, +:"+"a".matches("\\D+"));
System.out.println("n, {n}"+"\n\r\n".matches("\\s{3}"));
System.out.println("n~m, {n,m}:"+"1232132".matches("\\d{1,9}"));
System.out.println(">=n, {n,}:"+"www".matches("\\w{2,}"));
```

结果：

<=1, ?:true

0or>1, *: true true

=1, +:true

n, {n}true

n~m, {n,m}:true

=n, {n,}:true

##### 2.1.3 范围词

[abc] a,b或者c

[^abc] 除a,b,c任意字符 

[a-zA-Z] a-z或A-Z的任字符 

[a-d[m-p]] a到d或m到p 

[a-z&&[def]] d,e或f(交集) 

范围词内无论多长, 都只匹配一个字符而已

```java
System.out.println("[xxx]"+"a".matches("[abv]"));
System.out.println("[x-x]"+"3".matches("[1-9]"));
System.out.println("[^xx]"+"a".matches("[^bc]"));
```

结果：

[xxx]true
[x-x]true
[^xx]true

#### 2.2 主要应用

正则表达式对字符串操作的主要应用 

匹配 matches()

切割 split()

替换 replaceAll(String regex, String replacement)

查找 查找需要使用的对象:Pattern(正则对象) Matcher(匹配器对象)

##### 2.2.1 匹配

需求:编写正则表达式匹配手机号 第一位:只能1 开头,第二位:3 4 5 7 8,长度:11位

```java
public static void teleChk(String telephone) {		System.out.print(telephone+":"＋(telephone.matches("0\\d{2,3}-[1-9]\\d{6,7}")?"Valid":"Invalid"));}
```

需求:编写正则表达式匹配固话 形式 区号-主机号 区号首位是0,长度3- 4位 主机号:首位非0,长度7-8位

```java
public static void phoneChk(String phone) {
System.out.print(phone+":"＋(phone.matches("1[3578]\\d{9}")?"Valid":"Invalid"));}
```

##### 2.2.2 切割

需求:字符串各个字符之间多个空格,需分割字符

```java
public static void splitWd(String ran){
	    String[] res=ran.split(" +");
	    System.out.println(ran+Arrays.toString(res));
	}
```

需求:根据重叠词进行切割 

```java
public static void splitDupWd(String dupwd){
		String[] resp=dupwd.split("(.)\\1+");
		System.out.println(dupwd+Arrays.toString(resp));
	}
```

()分组：如果正则的内容需要被复用,那么需要对正则的内容进行分组,分组的目的就是为了提高正则的复用性。

组号不能指定,组号从1开始

例((A)(B(C))) 组1:((A)(B(C))) 组2 (A) 组3 (B(C)) 组4 (C)

##### 2.2.3 替换

需求:去除文本中的广告电话

```java
public static void replaceWd(String org)
{
  String reg = "1[3,4,5,7,8][0-9]{9}";
  String ret = org.replaceAll(reg, "****");
  System.out.println(ret);
}
```

需求:还原重复输入的话l如需要在方法正则的外部引用组内容,那么使用 “$组号”

##### 2.2.4 查找

典型调用顺序 

Pattern p= Pattern.compile(“正则” ) 

Matcher m = p.matcher(“aaaab”)

查找需要的对象

1. Pattern 
2. Matcher 

匹配器要使用的方法

1. find() 通知匹配器去查找,查找符合规则的字串
2. group() 获取符合规则的字符串

需求：查找字符序列中长度为3的单词

```java
public static void findWd2(String org)
{
  String reg = "\\b[a-zA-Z]{3}\\b";
  Pattern p = Pattern.compile(reg);
  Matcher m = p.matcher(org);
  while(m.find())
  {
    System.out.println(m.group());
  }
}
```

\b代表单词边界,不匹配任何字符 用group方法时,一定先要调用find方法

版权声明：本文为复旦nlp组施展根据课堂内容整理的原创文章，转载请注明出处。