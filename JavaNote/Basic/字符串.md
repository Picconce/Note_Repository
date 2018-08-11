# 字符串

## 字符串的比较

字符串的比较大体上来说有两种方式：

- ==
- equals(）方法
  
equals方法默认比较两个对象的内存地址 如下代码将进行描述

```java
public class EqualsTest {

    public static void main(String[] args) {

        Car car1 = new Car();
        Car car2 = new Car();
        boolean flag = car1.equals(car2);
        System.out.println(flag);
    }
}
```

查看 `equals()` 方法的源码

```java
public boolean equals(Object obj) {
    return (this == obj);
}
```

可以看到默认是使用了 `==` 符号将两个对象进行地址上的比较

但是在 `String` 中  又对 `equals()` 方法进行了重写  代码如下

```java
public boolean equals(Object anObject) {
    if (this == anObject) {
        return true;
    }
    if (anObject instanceof String) {
        String anotherString = (String)anObject;
        int n = value.length;
        if (n == anotherString.value.length) {
            char v1[] = value;
            char v2[] = anotherString.value;
            int i = 0;
            while (n-- != 0) {
                if (v1[i] != v2[i])
                    return false;
                i++;
            }
            return true;
        }
    }
    return false;
}
```

可以看出重写后 将对两个字符串的内容进行比较 官方API中是这样描述 `equals()` 方法的

```java
    equals
    public boolean equals(Object anObject)
        将此字符串与指定的对象比较。当且仅当该参数不为 null，并且是与此对象表示相同字符序列的 String 对象时，结果才为 true。
    覆盖：
        类 Object 中的 equals
    参数：
        anObject - 与此 String 进行比较的对象。
    返回：
    如果给定对象表示的 String 与此 String 相等，则返回 true；否则返回 false。
```

## String API

字符串常用方法（ 全部方法请查阅官方 API ）

- `length()`

```java
public int length()
    返回此字符串的长度。长度等于字符串中 Unicode 代码单元的数量。
    指定者：
        接口 CharSequence 中的 length
    返回：
        此对象表示的字符序列的长度。
```

- `isEmpty()`

```java
public boolean isEmpty()
    当且仅当 length() 为 0 时返回 true。
    返回：
        如果 length() 为 0，则返回 true；否则返回 false。
```

- compareTo ( String anotherString )

```java
public int compareTo(String anotherString)
    按字典顺序比较两个字符串。该比较基于字符串中各个字符的 Unicode 值。
    按字典顺序将此 String 对象表示的字符序列与参数字符串所表示的字符序列进行比较
    如果按字典顺序此 String 对象位于参数字符串之前，则比较结果为一个负整数。
    如果按字典顺序此 String 对象位于参数字符串之后，则比较结果为一个正整数。
    如果这两个字符串相等，则结果为 0；compareTo 只在方法 equals(Object) 返回 true 时才返回 0。

    这是字典排序的定义。
    如果这两个字符串不同，那么它们要么在某个索引处的字符不同（该索引对二者均为有效索引），要么长度不同，或者同时具备这两种情况。
    如果它们在一个或多个索引位置上的字符不同，假设 k 是这类索引的最小值；则在位置 k 上具有较小值的那个字符串（使用 < 运算符确定），其字典顺序在其他字符串之前。
    在这种情况下，compareTo 返回这两个字符串在位置 k 处两个char 值的差，即值：

        his.charAt(k)-anotherString.charAt(k)

    如果没有字符不同的索引位置，则较短字符串的字典顺序在较长字符串之前。在这种情况下，compareTo 返回这两个字符串长度的差，即值：
        this.length()-anotherString.length()

    指定者：
        接口 Comparable<String> 中的 compareTo
    参数：
        anotherString - 要比较的 String。
    返回：
        如果参数字符串等于此字符串，则返回值 0；如果此字符串按字典顺序小于字符串参数，则返回一个小于 0 的值；如果此字符串按字典顺序大于字符串参数，则返回一个大于 0 的值。
```

## StringBuffer & StringBuilder 相关

### StringBuffer

- 线程安全的可变字符序列。
- 一个类似于 String 的字符串缓冲区，但不能修改。
- 虽然在任意时间点上它都包含某种特定的字符序列，但通过某些方法调用可以改变该序列的长度和内容。
- 可将字符串缓冲区安全地用于多个线程。
- 可以在必要时对这些方法进行同步，因此任意特定实例上的所有操作就好像是以串行顺序发生的，该顺序与所涉及的每个线程进行的方法调用顺序一致。
- StringBuffer 上的主要操作是 `append` 和 `insert` 方法，可重载这些方法，以接受任意类型的数据

### StringBulider

- 一个可变的字符序列。此类提供一个与 StringBuffer 兼容的 API，但不保证同步。
- 该类被设计用作 StringBuffer 的一个简易替换，用在字符串缓冲区被单个线程使用的时候（这种情况很普遍）。
- 如果可能，建议优先采用该类，因为在大多数实现中，它比 StringBuffer 要快。
- 在 StringBuilder 上的主要操作是 append 和 insert 方法，可重载这些方法，以接受任意类型的数据。

## 相关面试题 & 有趣的点

### JDK1.7 中有这样的一段注解 描述了 String 的拼接 “ + ” 号操作

```java

The Java language provides special support for the string           Java语言为字符串提供特殊支持

concatenation operator ( + ), and for conversion of                 连接运算符（+），以及转换

other objects to strings. String concatenation is implemented       其他对象到字符串。 字符串连接已实现

through the  StringBuilder (or  StringBuffer )                      通过StringBuilder（或StringBuffer）

class and its append method.                                        类及其 append 方法。

String conversions are implemented through the method               字符串转换通过 toString 方法实现

toString, defined by Object andinherited by all classes in Java.    toString 方法由 Object 定义，并由 Java 中的所有类继承。
```

例如：

```java
String a="a";  String b="b" ;
```

当执行

```java
String c=a+b
```

操作时  实际上是创建一个 `StringBuilder` 对象  再通过 `apend()` 进行拼接  最后调用 `toStirng()` 生成一个新的对象给 c

### String / StringBuffer / StringBuilder 的总结

- `String` 和 `StringBuffer` 、 `StringBuilder` 相比， `String` 是不可变的， `String` 的每次修改操作都是在内存中重新 `new` 一个对象出来，而 `StringBuffer` 、 `StringBuilder` 则不用，并且提供了一定的缓存功能，默认 16 个字节数组的大小，超过默认的数组长度时，则扩容为原来字节数组的长度*2+2。
- `StringBuffer` 和 `StringBuilder` 相比，`StringBuffer` 是 `synchronized` 的，是线程安全的，而 `StringBuilder` 是非线程安全的，单线程情况下性能更好一点；使用 `StringBuffer` 和 `StringBuilder` 时，可以适当考虑下初始化大小，较少扩容的次数，提高代码的高效性