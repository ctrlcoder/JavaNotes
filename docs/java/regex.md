### 简单使用
java.util.regex 包主要包括以下三个类：
- Pattern 类：
pattern 对象是一个正则表达式的编译表示。Pattern 类没有公共构造方法。要创建一个 Pattern 对象，你必须首先调用其公共静态编译方法，它返回一个 Pattern 对象。该方法接受一个正则表达式作为它的第一个参数。
- Matcher 类：
Matcher 对象是对输入字符串进行解释和匹配操作的引擎。与Pattern 类一样，Matcher 也没有公共构造方法。你需要调用 Pattern 对象的 matcher 方法来获得一个 Matcher 对象。
- PatternSyntaxException：
PatternSyntaxException 是一个非强制异常类，它表示一个正则表达式模式中的语法错误。获得授权并注明出处。

```java
    String s = "agagga123141gjaljg";
    Pattern p = Pattern.compile("[0-9]{4}");
    Matcher m = p.matcher(s);
    if (m.find()) {
        System.out.println("验证码：" + m.group());
    } else {
        System.out.println("截取不到验证码！");
    }

    String content = "I am noob from runoob.com";
    String pattern = ".*runoob.*";
    boolean isMatch = Pattern.matches(pattern, content);
    System.out.println("字符串中是否包含了 'runoob' 子字符串? " + isMatch);
```

### 语法
>根据 Java Language Specification 的要求，Java 源代码的字符串中的反斜线被解释为 Unicode 转义或其他字符转义。因此必须在字符串字面值中使用两个反斜线，表示正则表达式受到保护，不被 Java 字节码编译器解释。例如，当解释为正则表达式时，字符串字面值 "\b" 与单个退格字符匹配，而 "\\b" 与单词边界匹配。字符串字面值 "\(hello\)" 是非法的，将导致编译时错误；要与字符串 (hello) 匹配，必须使用字符串字面值 "\\(hello\\)"。

在 Java 中正则表达式中则需要有两个反斜杠才能被解析为其他语言中的转义作用。也可以简单的理解在 Java 的正则表达式中，两个 `\\` 代表其他语言中的一个 `\`，这也就是为什么表示一位数字的正则表达式是 `\\d`，而表示一个普通的反斜杠是` \\\\`。

#### 元字符
| 代码 | 说明                         |
| ---- | ---------------------------- |
| .    | 匹配除换行符以外的任意字符   |
| \w   | 匹配字母或数字或下划线或汉字 |
| \d   | 匹配数字                     |
| \s   | 匹配任意的空白符             |
| ^    | 匹配字符串的开始             |
| $    | 匹配字符串的结束             |
| \b   | 匹配字符串的结束             |

#### 反义
| 代码/语法 | 说明                                       |
| --------- | ------------------------------------------ |
| \W        | 匹配任意不是字母，数字，下划线，汉字的字符 |
| \D        | 匹配任意非数字的字符                       |
| \S        | 匹配任意不是空白符的字符                   |
| \B        | 匹配不是单词开头或结束的位置               |

#### 字符类
| 代码/语法 | 说明                     |
| --------- | ------------------------ |
| x         | y                        |
| [abc]     | 匹配包含的任一字符       |
| [a-z]     | 匹配指定范围内的任何字符 |
| [^abc]    | 反向字符集               |
| [^a-z]    | 反向字符范围             |

#### 重复
| 代码/语法 | 说明             |
| --------- | ---------------- |
| *         | 重复零次或更多次 |
| +         | 重复一次或更多次 |
| ?         | 重复零次或一次   |
| {n}       | 重复n次          |
| {n,}      | 重复n次或更多次  |
| {n,m}     | 重复n到m次       |

#### 非贪心匹配
当`?`紧随任何其他限定符之后时，匹配模式是"非贪心的"。"非贪心的"模式匹配搜索到的、尽可能短的字符串，而默认的"贪心的"模式匹配搜索到的、尽可能长的字符串
例如，在字符串"oooo"中，"o+?"只匹配单个"o"，而"o+"匹配所有"o"。

#### 断言
| 代码/语法 | 说明                      |
| --------- | ------------------------- |
| (?=exp)   | 匹配exp前面的位置         |
| (?<=exp)  | 匹配exp后面的位置         |
| (?!exp)   | 匹配后面跟的不是exp的位置 |
| (?<!exp)  | 匹配前面不是exp的位置     |

### 捕获组
捕获组是把多个字符当一个单独单元进行处理的方法，它通过对括号内的字符分组来创建。
捕获组是通过从左至右计算其开括号来编号。例如，在表达式（（A）（B（C））），有四个这样的组：
- ((A)(B(C)))
- (A)
- (B(C))
- (C)
```java
      // 按指定模式在字符串查找
      String line = "This order was placed for QT3000! OK?";
      String pattern = "(\\D*)(\\d+)(.*)";
 
      // 创建 Pattern 对象
      Pattern r = Pattern.compile(pattern);
 
      // 现在创建 matcher 对象
      Matcher m = r.matcher(line);
      if (m.find( )) {
         System.out.println("Found value: " + m.group(0) );//This order was placed for QT3000! OK?
         System.out.println("Found value: " + m.group(1) );//This order was placed for QT
         System.out.println("Found value: " + m.group(2) );//3000
         System.out.println("Found value: " + m.group(3) ); //! OK?
      } else {
         System.out.println("NO MATCH");
      }
```
可以通过调用`matcher`对象的`groupCount`方法来查看表达式有多少个分组。`groupCount`方法返回一个`int` 值，表示`matcher`对象当前有多个捕获组。
还有一个特殊的组`（group(0)）`，它总是代表整个表达式。该组不包括在`groupCount`的返回值中。

### String类方法
String类中有几种方法可以使用正则表达式：



| 方法                                           | 返回类型 | 功能                                                         | 示例                                                         |
| ---------------------------------------------- | -------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| matches()                                      | boolean  | 告知此字符串是否匹配给定的正则表达式。                       | "-1234".matches("^-?\\d+$") => true                          |
| replaceAll(String regex, String replacement)   | String   | 使用给定的 replacement 替换此字符串所有匹配给定的正则表达式的子字符串。 | "a1b2c3".replaceAll("[a-zA-z]", "") => 123                   |
| replaceFirst(String regex, String replacement) | String   | 使用给定的 replacement 替换此字符串匹配给定的正则表达式的第一个子字符串。 | "Hello World! Hello Everyone!".replaceFirst("\\s", "") => HelloWorld! Hello Everyone! |
| split(String regex)                            | String[] | 根据给定正则表达式的匹配拆分此字符串。                       | "boo:and:foo".split(":") => { "boo", "and", "foo" }          |
| split(String regex, int limit)                 | String[] | 根据给定正则表达式的匹配拆分此字符串。                       | "boo:and:foo".split(":", 5) => { "boo", "and", "foo" }       |


split(String regex, int limit)方法中limit 参数控制模式应用的次数，因此影响所得数组的长度。如果该限制 n 大于 0，则模式将被最多应用 n - 1 次，数组的长度将不会大于 n，而且数组的最后一项将包含所有超出最后匹配的定界符的输入。如果 n 为非正，那么模式将被应用尽可能多的次数，而且数组可以是任何长度。如果 n 为 0，那么模式将被应用尽可能多的次数，数组可以是任何长度，并且结尾空字符串将被丢弃。

例如，字符串 "boo:and:foo" 使用这些参数可生成以下结果：



| Regex | Limit | 结果                          |
| ----- | ----- | ----------------------------- |
| :     | 2     | { "boo", "and:foo" }          |
| :     | 5     | { "boo", "and", "foo" }       |
| :     | -2    | { "boo", "and", "foo" }       |
| o     | 5     | { "b", "", ":and:f", "", "" } |
| o     | -2    | { "b", "", ":and:f", "", "" } |
| o     | 0     | { "b", "", ":and:f" }         |

### java.util.regex
在regex包中，包括了两个类，Pattern(模式类)和Matcher(匹配器类)。`Pattern`类是用来表达和陈述所要搜索模式的对象，`Matcher`类是真正影响搜索的对象。另加一个新的例外类，`PatternSyntaxException`，当遇到不合法的搜索模式时，会抛出例外。

### Pattern类方法
- Pattern.complie(String regex)：创建一个正则表达式
- Pattern.matcher(String regex,CharSequence input)：用于快速匹配字符串,该方法适合用于只匹配一次,且匹配全部字符串.，返回true或false
- Pattern.split(CharSequence input)：和String的方法相同

### Matcher方法
**通过`Pattern.matcher(CharSequence input)`返回一个Matcher对象**

#### 索引方法
索引方法提供了有用的索引值，精确表明输入字符串中在哪能找到匹配：
- public int start() 返回以前匹配的初始索引。
- public int start(int group) 返回在以前的匹配操作期间，由给定组所捕获的子序列的初始索引
- public int end() 返回最后匹配字符之后的偏移量。
- public int end(int group) 返回在以前的匹配操作期间，由给定组所捕获子序列的最后字符之后的偏移量。

#### 匹配方法
- public boolean matches() 尝试将整个区域与模式匹配，**只要有一个字符不匹配则返回false。**
- public boolean lookingAt()  尝试将从区域开头开始的输入序列与该模式匹配。**不需要整句都匹配，但是需要从第一个字符开始匹配**
- public boolean find() 尝试查找与该模式匹配的输入序列的下一个子序列。**匹配到的字符串可以在任何位置**
- public boolean find(int start）重置此匹配器，然后尝试查找匹配该模式、从指定索引开始的输入序列的下一个子序列。

#### 替换方法
替换方法是替换输入字符串里文本的方法：
- public String replaceAll(String replacement)  替换模式与给定替换字符串相匹配的输入序列的每个子序列。**替换所有匹配**
- public String replaceFirst(String replacement) 替换模式与给定替换字符串匹配的输入序列的第一个子序列。**替换首次匹配**
- public Matcher appendReplacement(StringBuffer sb, String replacement)实现非终端添加和替换步骤。
- public StringBuffer appendTail(StringBuffer sb) 实现终端添加和替换步骤。
```java
        Pattern p = Pattern.compile("cat");
        Matcher m = p.matcher("one cat two cats in the yard");
        StringBuffer sb = new StringBuffer();
        while (m.find()) {
            m.appendReplacement(sb, "dog");
        }
        m.appendTail(sb);
        System.out.println(sb.toString());//one dog two dogs in the yard
/*
```
- public static String quoteReplacement(String s) 返回指定字符串的字面替换字符串。这个方法返回一个字符串，就像传递给Matcher类的appendReplacement 方法一个字面字符串一样工作。