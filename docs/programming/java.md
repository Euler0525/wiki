# Java

## `equal` 和 `==` 的区别

### `==`

```java
/**
 * 1. 基本数据类型(如int, char)
 */ 
int a = 5;
int b = 5;
System.out.println(a == b)  // true(值相同)

/**
 * 2. 引用类型（数组、对象，比较内存地址是否相同，即是否是同一个对象）
 */
String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1 == s2); // false（地址不同）

/**
 * 3. 字符串常量池优化
 */
String s1 = "hello";         // 存入常量池
String s2 = "hello";         // 直接引用常量池中的对象
System.out.println(s1 == s2); // true（地址相同）

/**
 * 4. 包装类缓存机制
 */
Integer a = 127;            // 缓存范围 -128~127
Integer b = 127;
System.out.println(a == b);  // true（复用缓存对象）

Integer c = 128;
Integer d = 128;
System.out.println(c == d); // false（超出缓存范围，新建对象，地址不同）
```

### `equal`

```java
/**
 * 1. 默认比较内存地址，但大多数类（如String, Integer重写此方法，改为比较内容）
 */
String s1 = new String("hello");
String s2 = new String("hello");
System.out.println(s1.equals(s2)); // true（内容相同）


/**
 * 2. 避免空指针
 */
String s1 = null;
String s2 = "hello";
// System.out.println(s1.equals(s2)); // 抛出 NullPointerException
System.out.println(Objects.equals(s1, s2)); // false（安全写法，Java 7+）
```

## 序列化

在 Java 中，序列化（Serialization） 是将对象转换为字节流的过程，以便存储、传输或持久化；反序列化（Deserialization） 则是将字节流重新转换为对象的过程。这是 Java 中实现对象持久化和跨网络传输的核心机制。

## `isBlank()` 和 `isEmpty()`

### `isEmpty()`

- 定义：用于检查字符串是否为空，指的是字符串的长度是否为零。
- 返回值：如果字符串的长度为 0，返回 true；否则，返回 false。
- 空字符串：空字符串 ""（即长度为 0 的字符串）被视为 "empty"。
- 包含空格：isEmpty()只会判断字符串的长度，不会考虑字符串是否包含空格或其他不可见字符。

```java
String str1 ="";       // 空字符串
String str2 =" ";      // 包含一个空格的字符串
String str3 ="Hello";  // 非空字符串

System.out.println(str1.isEmpty());  // true
System.out.println(str2.isEmpty());  // false
System.out.println(str3.isEmpty());  // false
```

### `isBlank()`

- 定义：用于检查字符串是否为空或仅由空白字符（如空格、制表符、换行符等）组成。
- 返回值：如果字符串为空（长度为 0）或只包含空白字符，返回 true；否则，返回 false。
- 空白字符：isBlank()会考虑所有类型的空白字符（如\n、\r、\t 和空格），因此即使字符串包含空格，isBlank()也会返回 true。

```java
String str1 = "";       // 空字符串
String str2 = " ";      // 单个空格
String str3 = "\t\n";   // 制表符和换行符
String str4 = "Hello";  // 非空白字符串

System.out.println(str1.isBlank());  // true
System.out.println(str2.isBlank());  // true
System.out.println(str3.isBlank());  // true
System.out.println(str4.isBlank());  // false
```
