---
title: A Tour of the Dart Language
date: 2018-06-01 14:15
updated: 2020-01-09 19:30
categories:
- Dart
tags:
- Dart
- Flutter

---

> 原文见：[A Tour of the Dart Language](https://www.dartlang.org/guides/language/language-tour)

本文将为你展示Dart语言的主要特性，包括从变量、操作符到类、库等一系列内容。本文默认你已经有了其他编程语言基础。

学习更多关于Dart核心库的内容可以参考[A Tour of the Dart Libraries](https://www.dartlang.org/guides/libraries/library-tour)。关于Dart语言更多的详细内容可以参考[Dart Language Specification](https://www.dartlang.org/guides/language/spec)。

## Dart基本程序

下面的代码展示了很多Dart语言最基础的特性：

```dart
// Define a function
printInteger(int aNumber) {
  print('The number is $aNumber.'); // Print to console.
}

// This is where the app starts executing.
main() {
  var number = 42; // Declare and initialize a variable.
  printInteger(number); // Call a function.
}
```

下面解释下上方代码展示的特性（适用几乎所有的Dart 应用程序）：

` // 注释`

单行注释，Dart也支持多行注释和文档注释，参考[Comments](https://www.dartlang.org/guides/language/language-tour#comments)。

` int`

一种数据类型，其他[内置数据类型](https://www.dartlang.org/guides/language/language-tour#built-in-types)还有` String`，`List`和`bool`。

`print()`

显示输出的一种简单方式。

`'...'`和`"..."`

字符串常量。

`$variableName`或`${expression}`

字符串插值：字符串常量内包含一个变量或表达式的字符串形式，参考 [Strings](https://www.dartlang.org/guides/language/language-tour#strings)。

`main()`

可执行APP的入口，必需。参考 [The main() function](https://www.dartlang.org/guides/language/language-tour#the-main-function)。

`var`

不指定类型声明变量的一种方式。

> 本文代码风格遵守 [Dart style guide](https://www.dartlang.org/guides/language/effective-dart/style)。

## 重要概念

学习 Dart 语言，要时刻牢记以下事实与概念：

- 所有变量可以保存的内容都是*object*。所有的 object 都是 *class* 的实例。数字、函数、`null` 都是 object。所有的 object 都继承自 [Object](https://api.dartlang.org/dev/dart-core/Object-class.html) 。
- 虽然 Dart 是强类型语言，但是类型声明是可选的，因为 Dart 可以推断类型。在上边的代码中，`number` 被推断为 `int` 类型。如果需要明确说明任何类型都不需要，可以使用特殊的类型 [dynamic](https://www.dartlang.org/guides/language/effective-dart/design#do-annotate-with-object-instead-of-dynamic-to-indicate-any-object-is-allowed) 。
- Dart 支持泛型，比如 `List<int>` 表示整数列表， `List<Dynamic>` 表示任何类型对象的列表。
- Dart 支持顶层函数，比如 `main()` ，还支持与 类 、对象关联的函数（即static函数和实例方法）。还支持在函数中创建函数（嵌套函数或者本地函数）。
- 类似的，Dart 支持顶层变量，也支持类、对象关联变量（static 或实例变量）。实例变量也称为域或者属性。
- 与 Java 不同，Dart 没有 `public` 、`protected` 和 `private` 这些关键字，如果某个标识符以下杠 `_` 开头，那么那就是库私有的，详细信息参考[Libraries and visibility](https://www.dartlang.org/guides/language/language-tour#libraries-and-visibility) 。
- *标识符* 可以以字母或者下杠开头，后边跟热议字符和数字。
- 有时候，某些东西究竟是 *expression* 还是 *statement* 很重要，他可以帮助精确的区分这两个单词。
- Dart 工具可以报告两类问题：警告与错误。警告仅仅警示代码可能无法工作，但是不会妨碍你运行程序。错误包括编译时错误和运行时错误。编译错误倒是代码完全无法运行。运行时错误会在运行时抛出[异常](https://www.dartlang.org/guides/language/language-tour#exceptions)。

## 关键字

下面是 Dart 会特殊处理的关键字列表。

| abstract 1  | do           | import 1    | super     |
| ----------- | ------------ | ----------- | --------- |
| as 1        | dynamic 1    | in          | switch    |
| assert      | else         | interface 1 | sync* 2   |
| async 2     | enum         | is          | this      |
| async* 2    | export 1     | library 1   | throw     |
| await 2     | external 1   | mixin 1     | true      |
| break       | extends      | new         | try       |
| case        | factory 1    | null        | typedef 1 |
| catch       | false        | operator 1  | var       |
| class       | final        | part 1      | void      |
| const       | finally      | rethrow     | while     |
| continue    | for          | return      | with      |
| covariant 1 | get 1        | set 1       | yield 2   |
| default     | if           | static 1    | yield* 2  |
| deferred 1  | implements 1 |             |           |

上标为 ^1^ 的关键字是内置标识符，不要使用内置标识符作为标识符使用。如果使用内置标识符作为类名或类型名会造成编译时错误。

上标为 ^2^ 的关键字是 Dart1.0 之后增加的仅仅同步相关保留字。在任何被 `async` 、`async*`或 `sync*` 标记的函数中无法使用 `async` 、`await` 或者 `yield` 作为标识符。参考 [Asynchrony support](https://www.dartlang.org/guides/language/language-tour#asynchrony-support) 。

上表中的其他关键字都是保留字，禁止把它们作为标识符使用。

## 变量

下面的例子创建并初始化了一个变量：

```dart
var name = 'Bob';
```

变量存储的是引用。名字为 `name` 的变量包含了一个值为"Bob" 的 `String` 对象的引用。

`name` 的类型被推断为`String` 。不能通过指定类型类改变他的类型。如果对象的类型不限制为某一类型，那么根据[设计指南](https://www.dartlang.org/guides/language/effective-dart/design#do-annotate-with-object-instead-of-dynamic-to-indicate-any-object-is-allowed)，可以将它的类型指定为 `Object` 或 `dynamic` 。

```dart
dynamic name = 'Bob';
```

另一种方式是显示的声明它可以被推断出的类型：

```dart
String name = 'Bob';
```

> 注意，本文遵守[style guide recommendation](https://www.dartlang.org/guides/language/effective-dart/design#types) 对本地变量使用 var 而不指定类型的建议。

### 默认值

未经初始化的变量的默认值是 `null` 。即使数字类型也初始化为空，因为数字在 Dart 中也是对象。

```dart
int lineCount;
assert(lineCount == null)
```

> 在生产代码中，`assert()` 调用会被忽略掉。在开发中，`assert(condition)` 在*condition* 为非 `true` 时抛出异常。参考 [Assert](https://www.dartlang.org/guides/language/language-tour#assert)。

### final 与 const

对于一个你永远都不会改变的变量，使用 `final` 或 `const` 来标识，可以用来它们来代替关键字 `var` 或者在指定的类型之前。一个 final 限定的变量只可以被赋值一次；使用const限定的变量是编译时常量（const限定的变量默认就是final的）。final限定的顶层或类变量在第一次被使用时初始化。

> 注意：实例变量可以用final但是不能用const限定。

下面的例子创建并设置 final 的变量：

```dart
final name = 'Bob'； // 无变量类型标识
// name = 'Alice';	 // 去掉注释，本行会引起错误
final String nickname = 'Bobby'；
```

使用 `const` 来限定你需要的编译时常量（compile-time constants）。如果 const 变量是类级别的，将它标记为 `static const` 。在你声明变量并赋值为编译时常量比如数字、字符串或者数字运算的结果时，声明为 const  变量：

```dart
const bar = 10000000; // 压力单元(dynes/cm2)
const double atm - 1.01325 * bar; //标准气压
```

`const` 关键字不仅仅能声明常量，你还可以用来创建常量值，也可以用来声明构造函数。任何变量都可以包含一个常量值。

```dart
// 注意: []创建空列表
// const [] 创建一个空、不可变的列表(empty,immutable list, EIL)
var foo = const []; // foo是一个 EIL
final bar = const []; // bar永远都是一个 EIL
const baz = const []; // baz是编译时的常量 EIL

// 可以修改 非final、非const变量，即使它曾经被赋值一个const值；
foo = [];

// 但是无法修改final或const变量的值。
// bar = []; // Unhandled exception
// baz = []; // Unhandled exception
```

使用 `const`  来创建常量值，参考 [Lists](https://www.dartlang.org/guides/language/language-tour#lists) 、[Maps](https://www.dartlang.org/guides/language/language-tour#maps) 和 [Classes](https://www.dartlang.org/guides/language/language-tour#classes) 。

## 内置类型

Dart 语言对以下类型有特别支持：

- numbers
- strings
- booleans
- lists(也就是 arrays)
- maps
- runes(在字符串中展示 Unicode 字符)
- symbols

你可以用常量初始化以上任意类型的对象。比如 `this is a string` 是字符串常量， `true` 是布尔型常量。

由于 Dart 中任何的变量都引用一个对象，你可以使用构造函数来时初始化变量。一些内置类型也有它们自己的构造函数。比如你可以用 `Map()` 来创建一个map，代码可能是 `new Map()` 。

### 数字

Dart 只有两种类型的数字：

[int](https://api.dartlang.org/dev/dart-core/int-class.html)

依赖平台，不大于64 bit 长度 。在Dart VM上，数值范围：-2^63^ - 2^63^ - 1。

[double](https://api.dartlang.org/dev/dart-core/double-class.html)。

64位浮点数，符合IEEE754标准。

`int` 和 `double` 都是[num](https://api.dartlang.org/dev/dart-core/num-class.html) 的子类，基本的数学运算符+、-、*、/ 都适用这些数字类型，还包括 `abs()` 、`ceil()` 和 `floor()`方法（位操作运算符都定义在 `int` 类型中）。如果 num 及其子类不包含你需要的操作，可以看下 `dart:math` 库，可能会包含你需要的函数。

整数就是没有小数点的数字：

```dart
int x = 1;
int hex = 0xDEADBEEF;
```

double类型就是包含小数点的数字：

```dart
double y = 1.1;
double exponents = 1.42e5;
```

下面的例子展示了如何在数字与字符串之间数据类型转换：

```dart
// String -> int
var one = int.parse('1');
assert(one == 1);

// String -> double
var onePointOne = double.parse('1.1');
assert(ontPointOne == 1.1)
  
// int -> String
String oneAsString = 1.toString();
assert(oneAsString == '1');

// double -> String
String piAsString = 3.14159.toStringAsFixed(2);
assert(piAsString == '3.14');
```

整型制定了传统的位位移操作（<<, >>），与 (&) 和或 (|)：

```dart
assert((3 << 1) == 6);  // 0011 << 1 = 0110
assert((3 >> 1) == 1);  // 0011 >> 1 == 0001
assert((3 | 4) == 7);   // 0011 | 0100 = 0111
```

原始数字就是编译时常量。很多数学表达式只要他操作数都是编译时常量，那么它也是编译时常量：

```dart
const msPerSecond = 1000;
const secondsUtilRetry = 5;
const msUntilRetry = secondsUtilRetry * secondsUtilRetry;
```

### Strings

Dart 语言中的字符串就是 UTF-16 编码的序列。可以使用单引号或双引号创建字符串：

```dart
var s1 = 'Single quotes work well for string literals.';
var s2 = "Double quotes work just as well.";
var s3 = 'It\'s easy to escape the string delimiter.';
var s4 = "It's even easier to use the other delimiter.";
```

可以利用 `${expression}` 将表达式的值放到字符串中。如果表达式是变量，可以省略 `{}` 符号。Dart 调用对象的 `toString()` 方法来获取对象的字符串表示。

```dart
var s = 'string interpolation';
assert('Dart has $s, which is very handy.' ==
      'Dart has string interpolation,' +
      'which is very handy.');
assert('That deserves all caps. ' +
       '${s.toUpperCase()} is very handy!' ==
       'That deserves all caps. ' +
       'STRING INTERPOLATION is very handy!');
```

> == 操作符用来比较两个对象是否相等。包含相同 UTF-16 码的序列的字符创是相等的。

两个相邻的字符串会自动合并成一个字符串，也可以用 `+` 来合并字符串：

```dart
var s1 = 'String '
  'concatenation'
  ' works event over line breaks.';
assert (s1 ==
        'String concatenation works even over '
        'line breaks.');

var s2 = 'The + operator ' + "works, as well.";
assert(s2 == 'The + operator works, as well.');
```

使用三引号创建多行字符串：

```dart
var s1 = '''
You can creat multi-line strings like this one.
'''
var s2 = """ This is also a 
multi-line string.""";
```

你可以使用 `r` 作为前缀生成 'raw'字符串（译注：指的是所见即所得的字符串，没有任何转义）：

```dart
var s = r"In a raw string, event \n isn't special.";
```

关于如何在字符串中显示 Unicode ，参考 [Runes](https://www.dartlang.org/guides/language/language-tour#runes) 。

Literal strings are compile-time constants, as long as any interpolated expression is a compile-time constant that evaluates to null or a numeric, string, or boolean value.

```dart
// Theses work in a const string.
const aConstNum = 0;
const aConstBool = true;
const aConstStrng = 'a constant string';

// These do NOT work in a const string
var aNum = 0;
var aBool = true;
var sString = 'a string';
const aConstList = const [1, 2, 3];

const validConstString = '$aConstNum $aConstBool $aConstString';
// const invalidConstString = '$aNum $aBool $aString $aConstList';
```

对String的更多用法，参考 [Strings and regular expressions](https://www.dartlang.org/guides/libraries/library-tour#strings-and-regular-expressions )。

### Booleans

为了表示布尔型，Dart 定义了数据类型 `bool` 。bool 类型只有两个常量对象 `true` 和 `false` ，都是编译时常量。

Dart 语言的类型安全指的是 你不能写这样的代码，比如 `if (nonbooleanValue)` 或者 `assert(nonbooleanValue)` 。相反，显示的检查变量的值：

```dart
// Check for an empty string.
var fullName = '';
assert(fullName.isEmpty);

// Check for zero.
var hitPoints = 0;
assert(hitPoints <= 0);

// Check for null.
var unicorn;
assert(unicorn == null);

// Check for NaN.
var iMeantToDoThis = 0 / 0;
assert(iMeantToDoThis.isNaN);
```

### Lists

几乎所有编程语言都包含的集合类型就是数组了。在Dart中，数组就是 [List](https://api.dartlang.org/dev/dart-core/List-class.html) 对象，多数人称其为列表。

Dart 列表的字符表示与JavaScript的数组表示类似：

```dart
var list = [1, 2, 3];
```

> Dart 分析器会将上边的 list 类型推断为 List<int> 。如果尝试向其中添加非int类型数据，分析器或运行时会抛出错误。详细信息参考 [typr inference](https://www.dartlang.org/guides/language/sound-dart#type-inference) 。

List 的下标从0开始，下标为0的元素就是第一个元素，下标为 `list.length - 1` 的元素就最后一个。你可以与JavaScript语言一样获取列表的长度和获取列表中的元素：

```dart
var list = [1, 2, 3];
assert(list.length == 3);
assert(list[1] == 2);

list[1] = 1;
assert(list[1] == 1);
```

在 list 的字符表示前增加 `const` 来创建编译时常量 list：

```dart
var constantList = const [1, 2, 3];
// constantList[1] = 1;  // Uncommenting this cause an error
```

List 类型有很多操作list的使用方法。更多用法参考 [Generics](https://www.dartlang.org/guides/language/language-tour#generics) 和 [Collections](https://www.dartlang.org/guides/libraries/library-tour#collections) 。

### Maps

通常，一个的映射就是一个相关联的键值的对象。key 和 value 可以是任意类型的对象。在一个映射中，每个key只能出现一次，但是value可以重复。Dart 对映射的支持是由 [Map](https://api.dartlang.org/dev/dart-core/Map-class.html) 类型和 map 的字符形式提供的：

```dart
var gifts = {
    // Key:    Value
    'first': 'partridge',
    'second': 'turtleedoves',
    'fifth': 'golden rings'
};

var nobleGases = {
    2: 'helium',
    10: 'neon',
    18: 'argon'
};
```

> 上边代码中，分析器会将  `gifts` 被推断为 `Map<String, String>` 类型。`bobleGases` 的类型被推断为 `Map<int, String>` 。如果你想向其中增加错误类型的数据，分析器或运行时会抛出错误。更多信息，参考 [type inference](https://www.dartlang.org/guides/language/sound-dart#type-inference) 。

你可以用Map的构造函数创建相同对象：

```dart
var gifts = new Map();
gifts['first'] = 'partridge';
gifts['second'] = 'turtledoves';
gifts['fifth'] = 'golden rings';

var nobleGases = new Map();
nobleGases[2] = 'helium';
nobleGases[10] = 'neom';
nobleGases[18] = 'argon';
```

向已有map中添加新的键值对：

```dart
var gifts = {'first': 'partridges'};
gifts['forth'] = 'calling birds';  // Add a key-value pair
```

从 map 中获取值：

```dart
var gifts = {'first': 'partridge'};
assert(gifts['first'] == 'partridge');
```

如果从map中获取不存在的key的值，反馈 `null` ：

```dart
var gifts = {'first': 'partridge'};
assert(gifts['fifth'] == null);
```

使用 `.length` 获取map中键值对的数量：

```dart
var gifts = {'first': 'partridge'};
gifts['fourth'] = 'calling birds';
assert(gifts.length == 2);
```

创建编译时常量：

```dart
final constantMap = const {
  2: 'helium',
  10: 'neon',
  18: 'argon',
};

// constantMap[2] = 'Helium'; // Uncommenting this causes an error.
```

关于 map 的更多信息，参考 [Generics](https://www.dartlang.org/guides/language/language-tour#generics) 和 [Maps](https://www.dartlang.org/guides/libraries/library-tour#maps) 。

### Runes

> 译注：此处的runes和符号指的是各类特殊符号，emoji等。

Dart 中，各种符号都是字符串的 UTF-32 码。

Unicode 为全世界所有书写系统的字母、数字、符号都定义了唯一的数字值。由于 Dart 中使用 UTF-16 表示字符串，为了表示 UTF-32 的符号，需要使用特别的语法。

表示 Unicode 编码的常用方法是这样的：`\uXXXX` ， XXXX表示4个16进制数字。比如心形符号♥ 的Unicode是 `\u2665` 。为了表示多或少于4位16进制数字，把Unicode 放在大括号中，😆 是 `\u{1f600}` 。

`String` 类的某些属性可以帮你获取关于符号的信息。`codeUnitAt` 和 `codeUnit` 这两个属性获取16位的unicode 码。使用 `runes` 属性获取字符串中的符号。

下面的代码展示了符号、16位码、32位码的关系：

```dart
main() {
  var clapping = '\u{1f44f}';
  print(clapping);
  print(clapping.codeUnits);
  print(clapping.runes.toList());

  Runes input = new Runes(
      '\u2665  \u{1f605}  \u{1f60e}  \u{1f47b}  \u{1f596}  \u{1f44d}');
  print(new String.fromCharCodes(input));
}
```

> 使用list运算符操作符号的时候要特别注意。这种处理方式很容易失败，它依赖于语言、字符集和具体的运算符。更多信息参考 [Hwo do I reverse a String in Dart?](http://stackoverflow.com/questions/21521729/how-do-i-reverse-a-string-in-dart) 。

### Symbols

[Sumbol](https://api.dartlang.org/dev/dart-core/Symbol-class.html) 对象表示Dart中的一个操作符和标识符。你可能永远都不会用到 Symbol，因为对于使用名字应用的标识符来说，Symbol是没有值的。'because minification changes identifier names but not identifier symbols ' （译注：没懂什么意思）。

使用 `#` 来获取标识符的 symbol：

```dart
#radix
#bar
```

Symbol 的字面标识是编译时常量。

## Functions

Dart 是真正面向对象的语言，所以即使函数也是对象，并且属于 [Function](https://api.dartlang.org/dev/dart-core/Function-class.html) 类型。这意味着函数可以用来赋值给变量或者作为参数传递给其他函数。对于函数类的对象，你可以直接调用这个对象，详细信息参考[Callable classes](https://www.dartlang.org/guides/language/language-tour#callable-classes) 。

函数：

```dart
bool isNoble(int atomicNumber) {
    return _nobleGases[atomicNumber] == null;
}
```

虽然Dart建议显示的标明函数的返回值类型，但是如果你省略返回值类型它依然可用：

```dart
isNoble(atomicNumber) {
  return _nobleGases[atomicNumber] != null;
}
```

对于只有一个表达式的函数，可用使用一种简短的语法：

```dart
bool isNoble(int atomicNumber) => _nobleGases[atomicNumber] != null;
```

 `=>expr` 是 `return expr` 的简短写法。`=>` 有事被称为胖箭头（*fat arrow*）语法。

> 注意：只有表达式可以出现在胖箭头语句中，其他语句不可以。比如你不可以在其中编写 [if](https://www.dartlang.org/guides/language/language-tour#if-and-else) 语句，但是可以使用[条件表达式](https://www.dartlang.org/guides/language/language-tour#conditional-expressions)。

### Optional parameters

可选参数可以通过位置或名字确定，但不能同时通过两者确定。

Optional named parameters

调用函数的的时候可以用 `paramName:value` 指定参数值：

```dart
enableFlags(bold: true, hiden: false);
```

定义函数时。使用 `{param1, param2}` 来指定参数名字：

```dart
// Sets the [bold] and [hiden] flags ...
void enableFlags({bool bold, bool hiden}) {
    // ......
}
```

#### Optional positional parameters

使用 `[]` 包含的参数列表来标识 positional 参数：

```dart
String say(String from, String msg, [String device]) {
    var result = '$from says $msg';
    if (device != null) {
        result = '$result with a $device';
    }
    return result;
}
```

调用上面的函数：

```dart
// Without the optional parameter
assert(say('Bob', 'Howdy') == 'Bob says Howdy');
// With the optional parameter
assert(say('Bob', 'Howdy', 'smoke signal') == 'Bob says Howdy with a smoke signal');
```

#### Default parameter values

使用 `=` 来为可选参数设置默认值。默认值必须是编译时常量。默认的默认值是 `null` ：

```dart
/// Sets the [bold] and [hidden] flags ...
void enableFlags({bool bold = false, bool hidden = false}) {
  // ...
}

// bold will be true; hidden will be false.
enableFlags(bold: true);
```

可选参数的默认值可以如下设置：

```dart
String say(String from, String msg,
    [String device = 'carrier pigeon', String mood]) {
  var result = '$from says $msg';
  if (device != null) {
    result = '$result with a $device';
  }
  if (mood != null) {
    result = '$result (in a $mood mood)';
  }
  return result;
}

assert(say('Bob', 'Howdy') ==
    'Bob says Howdy with a carrier pigeon');
```

也可以使用 list 和 map 作为参数默认值：

```dart
void doStuff(
    {List<int> list = const [1, 2, 3],
    Map<String, String> gifts = const {
      'first': 'paper',
      'second': 'cotton',
      'third': 'leather'
    }}) {
  print('list:  $list');
  print('gifts: $gifts');
}
```

### The main() function

每个APP都必须有个顶层的 `main()` 函数作为APP的入口。`main()` 函数默认返回 `void` , 参数为可选的 `List<String>` 类型。

下面是一个 web 应用的 `main()` 函数：

```dart
void main() {
    querySelector('#sample_text_id')
    ..text = 'Click me'
    ..onClick.listen(reverseText);
}
```

> 上面代码中的 `..` 语法称为 [cascade](https://www.dartlang.org/guides/language/language-tour#cascade-notation-) （级联）。使用 cascade 你可以对某个对象的多个成员进行多个操作。

下面是一个包含命令行参数的命令行程序：

```dart
// Run the app like this: dart args.dart 1 test
void main(List<String> arguments) {
  print(arguments);

  assert(arguments.length == 2);
  assert(int.parse(arguments[0]) == 1);
  assert(arguments[1] == 'test');
}
```

你可以使用 [args library](https://pub.dartlang.org/packages/args) 来定义和解析命令行参数。

#### Functions as first-class objects

函数可以作为参数传递给另一个函数，比如：

```dart
void printElement(int element) {
    print(element);
}

var list = [1, 2, 3];

// 将 printElement 作为参数传递
list.forEach(printElement);
```

函数可以作为值分配给变量：

```dart
var louldify = (msg) => '!!! ${msg}.toUpperCase() !!!';
louldify('hello')
```

#### Anonymous functions 匿名函数

匿名函数也可以称为 *lambda* 或 *closure*。定义如下：

```
([[Type] param1[, …]]) { 
  codeBlock; 
}; 
```

与普通函数定义相似，0个或多个参数，可选的参数累心。如果函数只有一个语句，可以简写成 `([[Type] param1[, …]]) => oneLineCodeBlock;  ` 。

#### Lexical Scope 词法范围

Dart 语言变量的作用域是静态确定的，简单的说就是由你的代码结构决定的。花括号表示变量的作用域。

```dart
bool topLevel = true;

void main() {
  var insideMain = true;

  void myFunction() {
    var insideFunction = true;

    void nestedFunction() {
      var insideNestedFunction = true;

      assert(topLevel);
      assert(insideMain);
      assert(insideFunction);
      assert(insideNestedFunction);
    }
  }
}
```

> 注意：最内层嵌套的函数可以访问外层的所有变量。

#### Lexical Closure

闭包可以访问他的词法范围内的变量，即使闭包在他原始访问范围外访问也可以。

#### Test functions for equality 函数比较

```dart
void foo() {}  // 顶层函数
class A {
    static void bar() {}    // 类静态函数
    void baz() {}           // 实例函数
}

void main() {
    // 顶层函数比较
    var x = foo;
    // 同一个顶层函数，相等
    assert(x == foo);
    // 静态函数比较
    // 同一个静态函数，相等
    x = A.bar;
    assert(x == A.bar);

    // 实例函数比较
    var v = A();
    var w = A();
    var y = w;
    x = w.baz;

    // 都是实例w的函数，所以相等
    assert(y.bzd == x)
    // 非同一个实例的函数，不相等
    assert(v.baz != w.baz)
}
```

#### Return values 返回值

所有的函数都是返回值，如果代码不指定返回值，默认的 `return null;` 会被增加到函数的结尾。

```dart
foo() {}
assert(foo() == null);
```

## Operators 操作符

Dart 的多数运算符都可以[覆写](https://www.dartlang.org/guides/language/language-tour#overridable-operators)。

|描述|运算符|
|--------------------|:----------------------------|
|后缀一元运算符|`expr++ expr-- () [] . ?.`|
|前缀一元运算符|`-expr !expr ~expr ++expr --expr`|
|乘法|`* / % ~/`|
|加法|`+ -`|
|移位操作|`<< >>`|
|按位与|`&`|
|按位异或|`^`|
|按位或|`|`|
|关系与类型比较|`> >= <= < as is is!`|
|相等性比较|`== ！=`|
|逻辑与|`&&`|
|逻辑或|`||`|
|if null|`??`|
|条件运算符|`expr1?expr2:expr3`|
|cascade级联操作|`..`|
|赋值|`=  *=  /=  ~/=  %=  +=  -=  <<=  >>=  &=  ^=`|
|||

> 表中上方操作符的优先级都高于下方操作符的优先级。如果运算符适用于两侧不同类型的数据，那么使用左侧数据类型的方式进行运算。

### Arithmetic operators 算数运算符

| 运算符  | 意义                               |
| ------- | ---------------------------------- |
| `+`     | 加法                               |
| `-`     | 减法                               |
| `-expr` | 负数                               |
| `*`     | 乘法                               |
| `/`     | 除法                               |
| `~/`    | 除法，去整数结果                   |
| `%`     | 取模                               |
| `++var` | var = var +1, 表达式的值是var+1    |
| `var++` | var = var + 1，表达式的值是var     |
| `--var` | var = var - 1，表达式的值是var - 1 |
| `var--` | var = var - 1, 表达式的值是var     |

### Equality and relational operators 相等与关系运算符

| 运算符 | 意义                                                         |
| ------ | ------------------------------------------------------------ |
| `==`   | 用来判断两个对象是否表示同一个事物（如果操作符两边的对象都是null，返回true，其中一个是null，返回false；如果都不是null，调用x.==(y)） |
| `!=`   | 不相等                                                       |
| `>`    | 大于                                                         |
| `<`    | 小于                                                         |
| `>=`   | 大于等于                                                     |
| `<=`   | 小于等于                                                     |

> 如果判断x、y是否是同一个对象，使用 `identical()` 函数。



### Type test operators 类型检测运算符

`as`、 `is`、 `is!` 用来在运行时检查对象类型。

| 运算符 | 意义                                            |
| ------ | ----------------------------------------------- |
| `as`   | 类型转换（`(emp as Person).firstName = 'Bob'`） |
| `as`   | True 如果对象是指定类型                         |
| `is!`  | False如果对象是指定类型                         |

### Assignment operators 赋值运算符

> `??=` 是一特殊的赋值符号，只有当被赋值的变量为null的时候 才会赋值。

### Logical operators

| 运算符  | 意义     |
| ------- | -------- |
| `!expr` | 非，取反 |
| `||`    | 逻辑或   |
| `&&`    | 逻辑与   |

### Bitwise and shift operators 位与移位操作符

| 运算符  | 意义              |
| ------- | ----------------- |
| `&`     | 按位与            |
| `|`     | 按位或            |
| `^`     | 按位异或          |
| `~expr` | 按位取反          |
| `<<`    | 左移，右边用0补充 |
| `>>`    | 右移，左边用0补充 |

### Conditional Expressions 条件表达式

`condition ? expr1 : expr2`： 如果 condition 为 Ture，返回 expr1，否则返回 expr2。

```dart
var visibility = isPublic ? 'public': private;
```

`expr1 ?? expr2`： 如果 expr1 非null，返回 expr1，否则返回 expr2 。

```dart
String playerName(String name) => name ?? 'Guest';
```

### Cascade notation (级联操作)

`..` 级联操作符允许开发者对同一个对象进项一系列操作，不仅仅可以调用对象的函数，还可以直接访问对象的数据成员。

```dart
final addressBook = (AddressBookBuilder()
      ..name = 'jenny'
      ..email = 'jenny@example.com'
      ..phone = (PhoneNumberBuilder()
            ..number = '415-555-0100'
            ..label = 'home')
          .build())
    .build();
```

### Other Operators 其他运算符

| 运算符 | 名字         | 意义                                    |
| ------ | ------------ | --------------------------------------- |
| `()`   | 函数调用     | 表示一个函数调用                        |
| `[]`   | 列表访问     | 列表中指定下标的元素的值                |
| `.`    | 成员访问     | 访问表达试的属性                        |
| `?.`   | 条件成员访问 | 与 `.` 类似，当时左边的表达式可以为null |

## Control flow statements 控制流语句

### If and else

略。

### For loops For 循环

```dart
for(var i = 0; i < 5; i++) {}
```

对于 `Iterable` 类型的对象，可以使用 `forEach()` 函数遍历。

```dart
condidates.forEach((candidate) => candidate.interview())
```

Iterable 类也支持 `for-in` 循环。

```dart
var collection = [1,2,3];
for (var x in collection) {
    print(x);
}
```
### While and do-while while 循环
`while `循环在循环开始前评估循环的条件。
`do-while` 循环在一次循环结束后评估循环的条件。

### Break and continue 语句
使用 `break` 来停止循环，使用 `continue` 来跳过循环之后的语句，继续下一个循环。
```
candidates
    .where((c) => c.yearsExperience >= 5)
        .forEach((c) => c.interview());
```

### Switch and case 语句
Dart 中的 switch 语句可以应用于整数、字符创、或对象编译时常量（使用 == 来比较，类不能覆写 == 函数）。每个非空的 case 语句都必须包含 break 结尾，或者用contine、return、throw 来结束case，否则编译报错。
对于空 case，执行其下边的case代码（译注：类似java）。
```dart
var command = 'CLOSED';
switch (command) {
  case 'CLOSED':
      executeClosed();
      continue nowClosed;
        // Continues executing at the nowClosed label.

  nowClosed:
  case 'NOW_CLOSED':
    // Runs for both CLOSED and NOW_CLOSED.
    executeNowClosed();
    break;
  }
```
### Assert 断言
使用 `assert(condition)` 来中断正常执行的代码流程，在 `condition` 为 False 的时候中断正常流程，抛出 `AssertionError` 异常。
> assert 语句在生产代码中是无效的,在 dart 和 dart2js 命令中增加 `--enable-asserts` 来启用 assert 。

## Exceptions 异常
Dart 可以抛出和捕获异常，如果异常不被捕获，抛出异常的代码会被暂停，通常它所在的进程会被终止。
与 Java 不同，Dart 不要求函数声明抛出异常，也不要求调用者捕获函数可能抛出的异常。Dart 提供了 `Exception` 和 `Error` 两种异常类型，同时也支持抛出任意非null对象作为异常。

### Throw
```dart
throw FormatException('Expected at least 1 section');
throw 'Out of llamas';
void distanceTo(Point other) => throw UnimplementedError();

```
> 通常代码都将 `Exception` 或  `Error` 的子类作为异常抛出。

### Catch
捕获异常可以阻止异常继续传播，给开发者一个处理异常的机会。
```dart
try {
  breeMoreLlamas();
} on OutOfLlamasException{
  buyMoreLlamas();
} on Exception catch(e) {
  print('Exception details:\n$e');
} catch(e, s) {
  print('Exception details:\n$e');
  print('StackTrace:\n$s');
  // 抛出异常给调用者
  rethrow;
}
```

### Finally
保证无论是否抛出异常，`finally` 中的代码的代码都会执行。
```dart
try {
  breedMoreLlamas();
} catch (e) {
  print('Error: $e'); // Handle the exception first.
} finally {
  cleanLlamaStalls(); // Then clean up.
}
```

## Classes 类

Dart 是面向对象的语言，同时支持混合继承。所有的对象都是某个类的实例，所有的类都继承自 `Object` 。混合继承的意思是虽然每个类都只有一个直接的父类，但是一个类可以在多个继承中使用。

### Using class memebers 使用类成员
对象包含数据和函数成员，使用 `.` 来访问对象成员，使用 `?.` 来避免对象空指针的问题。
```dart
var p = Point(2, 3);
p.y = 3;
assert(p.y == 3);
num distance = p.distanceTo(Point(4, 4));
p.y? = 4;
```

### Using  constructors 构造函数
构造函数的名称可以是 `ClassName` 也可以是其他函数。
```dart
// new 关键字是可选的
var p1 = new Point(2, 2);
var p2 = new Point.fromJson({'x': 1, 'y': 2});
```
为了创建编译时常量，有些类提供了常量构造函数，在调用构造函数之前增加 `const` 关键字就可以创建编译时常量了。

构造两个相同的编译时常量，编译器只会生成一个实例，两个常量引用这同一个实例。
```dart
var a = const ImmutablePoint(1, 1);
var b = const ImmutablePoint(1, 1);
var c = ImmutablePoint(1, 1);
assert(identical(a, b)); // a b 是同一个实例
assert(!identical(1, c)); // a c不是同一个实例
```

在同一个 `const` 作用域范围内所有变量都是 `const` 的，不需要挨个指定。


### Getting an object's type 获取对象类型
使用对象的 `runtimeType` 属性在获取运行时获取对象的类型， 它返回一个 `Type` 类型的对象。
```dart
print('The type of a is ${a.runtimeType}');
```

### Instance variables 实例变量
```dart
class Point {
  num x;    // 声明实例变量，默认初始未 null
  num y;    // 声明y，初始为 null
  num z = 0; // 声明z，初始为 0
}
```
所有未初始化的数据成员为null。所有非 final 的数据成员会被默认创建 getter/setter函数。
```dart
class Point {
  num x;
  num y;
}

void main() {
  var point = Point();
  point.x = 4;              // 使用setter函数设置x的值
  assert(point.x == 4);     // 使用getter函数获取x的值
  assert(point.y == null);  // 默认值为 null
}
```

### Constructors 构造函数
默认使用与类型名字相同的函数作为构造函数，Dart 中构造函数是不被继承的，如果子类没有生命构造函数，它只有一个无参的默认构造函数，所有成员都是默认值。

#### Named constructors 命名构造函数
使用命名的构造函数来实现多个构造函数，使代码更清晰：
```dart
class Point {
  num x, y;
  Point(this.x, this.y);

  // 命名构造函数
  Point.origin() {
    x = 0;
    y = 0;
  }
}
```
子类如果想用父类的命名构造函数，也必须实现它。

#### Involing a non-default superclass constructor 调用父类非默认构造函数
默认子类调用父类非命名无参构造函数。子类也可以在构造函数的开始调用父类的构造函数。如果指定初始化列表，初始化列表会在父类构造函数之前执行，构造函数的执行顺序如下：
1. 初始化列表
2. 父类的无参数构造函数
3. 主类的无参构造函数

如果父类没有无参非命名构造函数，开发者必须手动调用父类的构造函数，使用 `:` 在构造函数体之前指定父类构造函数。
```dart
class Person {
  String firstName;

  Person.fromJson(Map data) {
    print('in Person');
  }
}

class Employee extends Person {
  // 调用父类指定构造函数
  Employee.formJson(Map data): super.fromJson(data) {
    print('in Employee')
  }
}

main() {
  var emp = new Employee.fromJson({});
  if (emp is Person) {
    emp.firstName = 'Bob';
  }
  (emp as Person).firstName = 'Bob';
}
```

#### Initializer list 初始化列表
可以在初始化列表中初始化成员变量，在运行构造函数前初始化实例初始化成员，使用逗号分隔列表。
```dart
Point.fromJson(Map<String, num> json) : x = json['x'], y = json['y'] {
  print('In Point.fromJson(): ($x, $y)');
}
```
在初始化 `final` 数据成员的时候初始化列表格外有用。
```dart
import 'dart:math';

class Point {
  final num x;
  final num y;
  final num distanceFromOrigin;

  Point(x, y) : x = x, y = y, distanceFromOrigin = sqrt(x * x + y * y);
}

main() {
  var p = new Point(2, 3);
  print(p.distanceFromOrigin);
}
```

##### Redirecting constructors 重定向构造函数
有时某些构造函数的作用就是调用其他构造函数。重定向构造函数的函数体是空的，使用 `:` 分隔。
```dart
class Point {
 num x, y;
 // the main constructor
 Point(this.x, this.y);

 Point.alongXAxis(num x): this(x, 0);
}
```

#### Constant constructors 常量构造函数
使用 `const` 构造函数来创建编译时常量，并且其中所有成员变量都是 `final` 类型的。
```dart
class ImmutablePoint {
  static final ImmutablePoint origin = 
    const ImmutablePoint(0, 0);

  final num x, y;

  const ImmutablePoint(this.x, this.y);
}
```
> 常量构造函数并非每次都创建常量。

#### Factory constructors 工厂构造函数
使用 `factory` 关键字来实现工厂构造函数，工厂构造函数不需要每次都创建新的对象，比如它可以从cache中返回一个重用的对象或者返回一个子类的对象。
```dart
class Logger {
 final String name;
 bool mute = false;

 // 使用 _ 使成员变量编程私有成员
 static final Map(String, Logger) _cache =
    <String, Logger>{};

  factory Logger(String name) {
    if (_cache.containsKey(name)) {
      return _cache[name];
    } else {
      final logger = Logger._internal(name) {
        _cache[name] = logger;
        return logger;
      }
    }
  }

  Logger._internal(this.name);

  void log(String msg) {
    if (!mute) print(msg);
  }
}
```
> 工厂构造函数没有权限访问 `this` 。
工厂构造函数的使用与普通构造函数的使用一致。
```dart
var logger = Logger('UI');
logger.log('Button clicked');
```

### Methonds 函数

#### Instance methods 实例函数

实例函数可以访问类的成员变量和 `this` 。

```dart
import 'dart:math';
class Point {
    num x, y;
    Point(this.x, this.y);

    num distanceTo(Point other) {
        var dx = x - other.x;
        var dy = y - other.y;
        return sqrt(dx * dx + dy * dy);
    }
}
```

#### Getters and setters

Getter 和 Setter 提供了对类成员读写的方式，你也可以使用 `get` 和 `set` 关键词实现自定义的 `getter` 和 `setter` 。
```dart
class Rectangle {
  num left, top, width, height;

  Rectangle(this.lest, this.top, this.width, this.height);

  num get right => left + width;
  set right(num value) => left = value - width;
  num get bottom => top + height;
  set bottom(num value) => top = value - height;
}

void main() {
  var rec = Rectangle(3, 4, 20, 15);
  assert(rect.left == 3);
  rect.right = 12;
  assert(rect.left == -1);
}
```
你可以在初始的时候使用实例变量，如果之后改成函数的实现方式，你就不需要修改客户端的代码而直接使用了。
> 无论是否明确定义了 `getter` 函数，运算符都可以按照预期工作。为了避免任何非预期的副作用，运算符都只调用 `getter` 一次，然后把值存在临时变量中。

#### Abstract methods
Instance、getter 和 setter 方法都可以是抽象方法，定义好接口，然后把接口的实现留给其他类来做。只有抽象类才能包含抽象方法。

只要在函数体除使用分号（;）就可以定义抽象方法了。
```dart
// abstract method
abstract class Doer {
  void doSomething();  //抽象方法
}

class EffectiveDoer extends Doer {
  void doSomething() {
    // 在此具体实现此函数
  }
}
```

#### Abstract classes
使用 `abstract` 来定义不能实例化的抽象类。抽象类主要用来定义接口，通常会包含某些实现。如果你希望抽象类可以被实例化，那么你可以定义一个工厂构造函数([FactoryConstructor](https://dart.dev/guides/language/language-tour#factory-constructors))。

抽闲类通常包含抽象方法，例如：
```dart
// 声明为抽象类，无法实例化的类
abstract class AbstractContainer {
  // 
}
```

#### Implicit Interface
每个类都是一个包含了它所有实例成员和它实现的接口的隐式接口（译注：每个class都是一个interface，都可以被其他类implement）。如果你想创建一个类A，并让类A支持类B的 API，但又不想让A继承B，那么A应该实现（implement）B。

类可以通过 `implements` 语句来实现一个或多个接口，然后实现接口的方法，比如：
```dart
// Person 的类定义，隐含了接口 greet()
class Person {
  // 包含在接口中，但是只对此类可见
  final _name;
  // 不包含在接口中，因为这是构造方法
  Person(this._name);
  // 包含在接口中
  String greet(String who) => 'Hello $who. I am $_name';
}
// 实现了 Person 接口的类
class Impostor implements Persion {
  get _name => ''; // 注意这，实现了 _name 接口
  String greet(String who) => 'Hi, $who. Do you know who I am?';
}

String greetBob(Person person) => person.greet('Bob');

void main() {
  print(greetBob(Persion('Kathy')));
  print(greetBob(Impostor()));
}
```
> 译注： 上边代码输出如下：
``` bash
Hello, Bob. I am Kathy
Hello, Bob. Do you know who I am ?
```
下边是实现多个接口的例子：
```dart
class Point implements Comparable, Location {...}
```

#### Extending a class
使用 `extends` 关键字来实现类的继承， `super` 来引用父类。
```dart
class Television {
  void turnOn() {
    _illuminateDisplay();
    _activateIrSensor();
  }
  // ...
}

class SmartTelevision extends Television {
  void turnOn() {
    super.turnOn();
    _bootNetworkInterface();
    _initializeMemory();
    _upgradeApps();
  }
  // ...
}
```

#### Overriding members
子类可以覆写实例方法、getter 和 setter。使用 `@override` 标注来标识你是故意覆写一个成员方法：
```dart
class SmartTelevision extends Television {
  @override
  void turnOn() {...}
  // ...
}
```
To narrow the type of a method parameter or instance variable in code that is type safe, you can use the covariant keyword.（译注：暂时没看懂啥意思，明白了再来翻译）

#### Overridable operators
以下运算符可以覆写：
| <  | +  | ^  | [] |
|----|----|----|----|
| >  | /  | \| | []=|
| <= | ~/ | &  | ~  |
| >= | *  | << | == |
| -  | %  | >> |    |
> 注意：`!=` 不是可覆写的运算符，因为 `e1 != e2` 只是 `!(e1 == e2)` 的语法糖。

下边的例子覆写了 `+ -` 两个运算符：
```dart
class Vector {
  final int x, y;
  Vector(this.x, this.y);
  Vector operator +(Vector v) => Vector(x + v.x, y + v.y);
  Vector operator -(Vector v) => Vector(x - v.x, y - v.y);
}

void main() {
  final v = Vector(2, 3);
  final w = Vector(2, 2);

  assert(v + w == Vector(4, 5));
  assert(v - w == Vector(0, 1));
}
```
如果你覆写了 `==`，你需要同时覆写它的 `hashCode` getter，参考[Implementing map keys](https://dart.dev/guides/libraries/library-tour#implementing-map-keys) 和 [Extending a class](https://dart.dev/guides/language/language-tour#extending-a-class)

#### noSuchMethod()
问了检测和响应代码对不存在的方法和数据成员的调用，你可以覆写 `noSuhMethod()` 方法：
```dart
class A {
  // 如果不覆写 noSuchMethod 方法，调用不存在的类成员会导致 NoSuchMethodError。
  @override
  void noSuchMethod(Invocation invocation) {
    print('You tried to use a non-existing member:' +
    '${invoation.memberName}');
  }
}
```
除下列情况外，你是无法调用不存在的方法的：
- 调用 `dynamic` 类型实例的方法
- 调用确定类型实例的未实现方法（比如抽象类的方法），并且实例的动态类型自己实现了 `noSuchMethod` 方法（译注：这句话有点绕，个人理解，比如你继承了一个抽象类，被继承的抽象类实现了 `noSuchMethod` 方法）。

更多信息参考 [noSuchMethod forwarding specification](https://github.com/dart-lang/sdk/blob/master/docs/language/informal/nosuchmethod-forwarding.md)。

#### Extension methods
扩展方法是 Dart2.7 引入的新特性，是向已有库中增加功能的一种方法。在不知情的情况下，你看能已经使用了扩展方法。比如在你使用IDE编写代码的时候，它会不区分常规方法和扩展方法给你代码提示。

下边的例子，使用了 `string_api.dart` 中扩展的 `String` 类的 `parseInt` 方法。
```dart
import 'string_api.dart';

print('42'.padLeft(5));
print('42'.parseInt());
```
关于扩展方法的使用与实现方式，参考 [extension methods page](https://dart.dev/guides/language/extension-methods)。

#### Enumerated types
枚举类型是特别的类，是用来表示一组固定常量。

##### Using enums
使用 `enum` 关键字来声明枚举类型：
```dart
enum Color {red, green, blue}
```
> 译注：注意上边Color的定义，必须在代码的顶层，并且代码没有以分号结束。

枚举中的每个值都包含一个叫 `index` 的 getter，它会返回枚举中定义的的这个值的下标（从0开始）：
```dart
assert(Color.red.index == 0);
assert(Color.green.index == 1);
assert(Color.blue.index == 2);
```
使用枚举的 `values` 常量来获取枚举中定义的所有值：
```dart
List<Color> colors = Color.values;
assert(colors[2] == Color.blue);
```
枚举值可以用作 switch 语句的 case 值，你必须处理枚举中的所有值，否则会产生一个警告：
```dart
var aColor = Color.blue;
switch (aColor) {
  case Color.red:
    print('Red as roses');
    break;
  case Color.green:
    print('Green as grass');
    break;
  default:
    // 没有此语句会产生警告
    print(aColor); // 打印 Color.blue
}
```
枚举值有以下限制：
- 不能继承、实现、mix in 枚举类型
- 不能显示的实例化枚举类型

详细信息参考 [Dart language specification](https://dart.dev/guides/language/spec)。

#### Adding features to a class: mixins
Mixin是在多继承中复用代码的一种方式。
使用 `with` 关键字后面跟一个或多个 mixin 类型：
```dart
class Musician extends Performer with Musical {
  // ...
}

class Maestro extends Person with Musical, Aggressive, Demented {
  Maestro(String maestroName) {
    name = meastroName;
    canConduct = true;
  }
}
```
为了实现一个 *mixin* 类型，你只要创建一个继承字 `Object` 的类并且不要声明构造方法即可。除非你希望 *mixin* 可以被当做普通的类来使用，否则都用 `mixin` 关键字代替 `class` 关键字既可:
```dart
mixin Musical {
  bool canPlayPiano = false;
  bool canCompose = false;
  bool canConduct = false;
  void entertainMe() {
    if (canPlayPiano) {
      print('Playing piano');
    } else if (canConduct) {
      print('Waving hands');
    } else {
      print(Humming to self');
    }
  }
}
```
为了限制mixin的使用范围，你可以使用 `on` 关键字来指定可以使用它的类，这样这个mixin就可以调用它没有定义的方法了。
```dart
mixin MusicalPerformer on Musican {
  // ...
}
```

#### Class variables and methods
使用 `static` 关键字来定义类变量和方法。

##### Static  variables
静态变量（类变量）适用于类型级别的状态和常量：
```dart
class Queue {
  static const initialCapacity = 16;
  // ...
}

void main() {
  assert(Queue.initialCapacity == 16);
}
```
静态变量只有在使用的时候才会初始化。

##### Static methods
静态方法（类方法）不能使用 `this` ，所以不能操作自己的实例成员。
```dart
import 'dart:math';
class Point {
  num x, y;
  Point(this.x, this.y);
  static num distanceBetween(Point a, Point b) {
    var dx = a.x - b.x;
    var dy = a.y - b.y;
    return sqrt(dx * dx + dy * dy);
  }
}

void main() {
  var a = Point(2, 2);
  var b = Point(4, 4);
  var distance = Point.distanceBetween(a, b);
  assert(2.8 < distance && distance < 2.9);
  print(distance);
}
```
你可以使用静态方法作为编译时常量。比如可以把静态方法作为常量构造函数的参数。

## Generics
如果你看过API文档中基本类型的定义，`List` 的定义是 `List<E>` , <> 符号将 List 标记为泛型，也就是包含类型参数的类型。根据惯例，多数的泛型使用单个字母作为类型名字，比如 E、T、S、K、V。

### Why use generics？
通常使用泛型是为了保证类型安全，除此外，还有以下益处：
- 恰当明确的泛型有益于更好的代码质量
- 减少代码的重复

如果你只项想创建包含字符串类型的列表,那么你应该声明一个 `List<String>` 类型的列表。这样，你自己、你的同事和代码工具在你尝试分配非字符串类型给列表的时候可以发现这可能是一个错误：
```dart
var names = List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
names.add(42);  // 错误
```
使用泛型的另一个原因是减少代码的重复。泛型可以让你在不影响使用代码静态分析工具的情况下，在多种不同类型之间共享同样的接口和接口实现，比如下边这个缓存的工具：
```dart
abstract class ObjectCache {
  Object getByKey(String key);
  void setByKey(String key, Object value);
}
```
后来你发现需要上边接口的字符串版本，你可以创建另一个接口：
```dart
abstract class StringCache {
  String getByKey(String key);
  void setByKey(String key, String value);
}
```
过了一段时间，你发现你需要个数字类型的缓存版本。。。
使用泛型可以帮你省去每个需求都重新写代码的麻烦了。你只要创建同一个接口，并且接受类型参数即可：
```dart
abstract class Cache<T> {
  T getByKey(String key);
  void setByKey(String key, T value);
}
```
在上边的代码中，T 只是一个类型的占位符。你可以认为他是你以后使用到的任何类型。

### Using collection literals
列表、集合、映射类型的都可以使用泛型。类型参数化的常量就和和见过的其他常量一样，除了增加了 `<type>`(list、set) 、 `<keyType, valueType>` (map) 以外:
```dart
var names = <String>['Seth', 'Kathy', 'Lars'];
var uniqueNames = <String>{'Seth', 'Kathy', 'Lars'};
var pages = <String, String>{
  'index.html':'HomePage',
  'robots.txt':'Hits for web robots',
  'humans.txt':'We are people, not machines'
};
```
### Using parameterized types with constructors
To specify one or more types when using a constructor, put the types in angle brackets (<...>) just after the class name. For example:
```dart
var nameSet = Set<String>.from(names);
var views = Map<int, View>();
```

### Generic collections and the types they contain
Dart 的泛型是*具体的*(*reified*)，这意味着在运行时保存着它的类型信息。比如，你可以检测集合的类型：
```dart
var names = List<String>();
names.addAll(['Seth', 'Kathy', 'Lars']);
print(names is List<String>); // true
```
> 相反的，Java 中使用类型擦除，泛型的类型参数在运行时是不存在的。在 Java 中你可以确定一个对象是不是 List，但不能检测他是不是 `List<String>`。

### Restricting the parameterized type
在实现泛型类的时，你看能需要限制类型，你可以用 `extends` 来实现：
```dart
class Foo<T extends SomeBaseClass> {
  String toString() => "Instace of 'Foo<$T>'";
}
class Extender extends SomeBaseClass {...}
// 可以这样使用
var someBaseClassFoo = Foo<SomeBaseClass>();
var extenderFoo = Foo<Extender>();
// 你也可以不指定类型参数，使用默认类型
var foo = Foo();
print(foo);  // Foo<SomeBaseClass> 的实例
var foo = Foo<Object>();  // 指定非 SomeBaseClass 的类型参数会引起错误
```

### Using generic methods
最开始，Dart 对泛型的支持仅限于泛型类。之后才加入了泛型方法，允许类型参数的方法和函数：
```dart
T first<T>(List<T> ts) {
  T tmp = ts[0];
  return tmp;
}
```

泛型参数你可以用在多个地方：
- 函数的返回类型（`T`）
- 参数的类型(`List<T>`)
- 局部变量的类型(`T tmp`)

## Libraries and visibility
`import` 和 `library` 指令可以帮你创建模块化可共享的代码。库不仅仅可以提供 API，而且还是一个隐私单元（a unit of privacy）:以下杠开头的标识符都只在库内可见。及时没有使用 `library` 指令，每个 Dart 应用程序也都是一个库。

库（Library） 可是通过 [package](https://dart.dev/guides/packages) 分发出去。

### Using libraries
使用 `import` 来在一个库中指定如果使用另一个库中的命名空间。
```dart
import 'dart:html';
```
`import` 只需要一个地址来确定使用的库。对于 Dart 内置的库，库地址的 scheme 是 `dart:`。对于其他库，你可以使用文件系统中路径或 `package:` 来指定。`package:` 用来指定包管理器（比如 pub 工具）提供的库：
```dart
import package:test/test.dart;
```

#### Specifying a library prefix
如果你引入的两个包命名有冲突，那么你可以未其中一个或两个库都指定前缀。比如，library1 和 library2 都包含名为 Element 的类，那么你可以这样写代码：
```dart
import 'package:lib1/lib1.dart';
import 'package:lib2/lib2.darr' as lib2;
// 使用 lib1 中的 Element
Element element1 = Element();
// 使用 lib2 中的 Element
lib2.Element element2 = lib2.Element();
```

#### Importing only part of a library
如果你只想使用某个库的一部分，你可以有选择性的导入库：
```dart
// 只导入 foo。
import 'package:lib1/lib1.dart' show foo;
// 导出 foo 以外的其他所有内容
import 'package:lib2/lib2.dart' hide foo;
```

#### Lazily loading library
延迟加载（懒加载） 使网页应用在需要的时候按需加载库。你可能在以下情况下使用延迟加载：
- 降低网页初始的启动时间
- 执行 A/B 测试，比如测试对比同一个算法的不同实现方式
- 载入极少使用的功能，比如设置页面和对话框

> 注意，只有 dart2js 支持延迟加载

使用 `deferred as` 来指定延迟加载的库，然后在需要使用这个库的时候，调用他的 `loadLibrary()`方法：
```dart
import `package:greetings/hello.dart` deffered as hello;

Future greet)() async {
  await hello.loadLibrary();
  hello.printGreeting();
}
```
上边的代码中， `await` 关键字暂停了代码的执行，直到库加载完毕。关于 `await` 和 `async` 的更多信息，参考 [asynchrony](https://dart.dev/guides/language/language-tour#asynchrony-support) 。
你可以多次调用同一个库 `loadLibrary()`,这个库只会载入一次。
记住以下几条：
- 延迟加载的库中的常量在导入的文件中不是常量，只有库载入了之后才会存在这些常量
- 在导入文件中，不能使用延迟加载的库中的数据类型。你可以把接口类型定义放到两个库都需要导入的第三个库中。
- Dart 会隐式的向你使用 `deferrred as namespace` 的namespace 中插入 `loadLibrary()` 方法。`loadLibrary()` 方法返回 [Future](https://dart.dev/guides/libraries/library-tour#future) 对象。

### Implementing libraries
关于如何创建库，参考[Create Library Packages](https://dart.dev/guides/libraries/create-library-packages),内容包括：
- 库中源码的组织结构
- 如何使用 `export` 指令
- 何时使用 `part` 指令
- 何时使用 `library` 指令
- 如何使用条件导出和导入从而实现跨平台的库

## Asynchrony support
Dart 的库中有无数的函数返回 [Future](https://api.dart.dev/stable/dart-async/Future-class.html) 或 [Stream](https://api.dart.dev/stable/dart-async/Stream-class.html) 类型的对象。这些函数都是 *异步的*。这些函数可能执行一些消耗时间的操作（比如 I/O 操作），但不用等到操作结束才返回。

`async` 和 `await` 关键词支持异步编程，让你编写的异步代码看起来和同步代码很相似。

### Handling Futures
当你需要获取执行完成的 Future 的结果的时候，你有两种做法：
- 使用 `async` 和 `await`
- 使用 Future API，如 [这里](https://dart.dev/guides/libraries/library-tour#future) 所述

使用 `async` 和 `await` 的代码是异步的，但是看起来非常像同步代码。下面的代码使用 `await` 等待异步函数的执行结果：
```dart
await lookUpVersion();
```
如果使用了 `await` 关键字，它必须在 `async` 函数中才能使用：
```dart
Future checkVersion() async {
  var version = await lookUpVersion();
  // ...
}
```
在 `async` 函数中可以多次使用 `await`。比如下面的代码等待了三次：
```dart
var entrypoint = await findEntrypoint();
var exitCode = await runExecutable(entrypoint, args);
await flushThenExit(exitCode);
```
在 `await`  *expression* 中，*expression*的值通常是一个 Future；如果不是，*expression*的值会自动被包装成一个 Future。Future 对象表示一个返回对象的承诺。`await` *expression* 的值就是返回的对象。`await` 表达式使代码的执行暂停到返回值可用。

如果在使用 `await` 的过程中发生编译异常，收线要确定 `await` 是被包含在 `async` 函数中。如果在 `main()` 函数中使用 `await`,你需要先把 `main()` 标记成 `async`:
```dart
Future main() async {
  checkVersion();
  print('In main: version is ${await lookUpVersion()}');
}
```

#### Declaring async functions
`async` 函数指的是函数体被 `async` 关键字标记的函数。
被 `async` 标记的函数的返回值类型是 Future 。比如下边的函数返回 String ：
```dart
String lookUpVersion() => '1.0.0';
```
如果把上边的函数改为 `async` 函数，由于 future 类型的的实现对时间的消耗较多，它的返回值类型是 Future:
```dart
Future<String> lookUpVersion() async => '1.0.0';
```
需要注意的是函数体本身并不需要使用 Future 的 API。Dart 会在必要时创建 Future 对象。如果函数没有返回值，就让他默认返回 `Future<void>` 类型。
对于 `async` 和 `await` 的交互式教程，参考 [asynchronous programming codelab](https://dart.dev/codelabs/async-await)。

#### Handling Streams
当你需要从 Stream 中获取数据的时候，你有两种方法：
- 使用 `async` 和异步的 for 循环(`await for`)
- 使用 Stream API，描述见 [library tour](https://dart.dev/guides/libraries/library-tour#stream)

> 在使用 `await for` 之前，一定要想清楚，`await for` 是否可以使你的代码更加整洁，你是否真的需要等待Stream 中的每个结果。比如，对于 UI 事件的监听，不应该使用 `await for` ,因为 UI 框架的事件流会发生无尽的事件。

异步 for 循环代码如下：
```dart
await for (varOrType identifier in expression) {
  // 流中每发生一次事件就会执行一次这里的代码
}
```
上边代码中 `expression` 的值必须是 Stream 类型，代码的执行流程如下：
1. 等待流中发出事件。
2. 执行循环体的代码，事件被赋值给 identifier。
3. 重复以上两步，一直到 Stream 关闭。

可以使用 `break` 或 `return` 语句退出循环，来结束对 Stream 的监听。

如果在编写异步 for 循环代码时发生编译错误，你可以检查代码，确保 `await for` 语句是被包含在 `async` 函数中。比如为了在 `main()` 函数中使用异步 for 循环，`main()` 必须使用 `async` 标记：
```dart
Future main() async {
  // ...
  await for (var request in requestServer) {
    handleRequest(request);
  }
  // ...
}
```
异步编程的更多信息吗，参考 [dart:sync](https://dart.dev/guides/libraries/library-tour#dartasync---asynchronous-programming) 。

## Generators
在你需要懒生成（lazily produce）一系列的数据的时候，你可以考虑使用 *生成器函数* 来实现。Dart 内置了两种对生成器函数的支持：
- 同步生成器：返回 [Iterable](https://api.dart.dev/stable/dart-core/Iterable-class.html) 对象。
- 异步生成器：返回 [Stream](https://api.dart.dev/stable/dart-async/Stream-class.html) 对象。

使用 `sync *` 标记函数体，并使用 `yield` 语句来输出数据，这样就定义了一个同步的生成器函数：
```dart
Iterable<int> naturalsTo(int n) sync * {
  int k = 0;
  while (k < n) yield k++;
}
```
使用 `async *` 来标记函数体，并用 `yield` 来输出数据，这样就定义了一个异步生成器：
```dart
Strng<int> asynchronousNaturalsTo(int n) async* {
  int k = 0;
  while (k < n) yield k++;
}
```
如果你的生成器是一个递归函数，可以使用 `yield*` 来提升他的性能：
```dart
Iterable<int> naturalsDownFrom(int n) sync* {
  if (n > 0) {
    yield n;
    yield* natutalsDownFrom(n - 1);
  }
}
```

## Callable classes
通过实现 `call()` 方法，可以让实例对象像函数一样被调用。
在下边的例子中，`WannableFunnctionn` 类定义了 call() 函数，接受3个字符串类型的参数以空格建个把他们连接在一起并增加一个感叹号结尾：
```dart
class WannableFunction {
  call(String a, String b, String c) => '$a $b $c!';
}

main()  {
  var wf = new WannableFunction();
  var out = wf("Hi", "there", "gang");
  print('$out');
}
```

## Isolates
多数的计算机系统都有多核CPU。为了充分利用多核优势，开发者通常使用多线程共享内存来并行运行。然而，同步共享状态不仅仅容易发生错误，而且也让代码更加复杂，

与线程不同，Dart 代码运行在 *isolates* 中。每个 *isolate* 有它自己的内存栈区，保证从外部无法访问到 *isolate* 的内部。

更多信息参考：
- [Dart asynchronous programming: Isolates and event loops](https://medium.com/dartlang/dart-asynchronous-programming-isolates-and-event-loops-bffc3e296a6a)
- [dart:isolate API reference](https://api.dart.dev/stable/dart-isolate) ，包括 [Isolate.spawn()](https://api.dart.dev/stable/dart-isolate/Isolate/spawn.html) 和 [Transferable TypedData](https://api.dart.dev/stable/dart-isolate/TransferableTypedData-class.html)
- [Background parsing](https://flutter.dev/docs/cookbook/networking/background-parsing) 指南
- [Isolate sample app](https://github.com/flutter/samples/tree/master/isolate_example)

## Typedefs
在 Dart 中，函数也是对象，是和字符串、数字等一样的对象。*typedef* 或者 *function-type alias*，给函数类型一个名字，你可以在声明数据、函数返回值时使用这个名字。当函数被赋值给变量时候， typedef 包含了函数类型信息。 

看下边不使用 typedef 的代码：
```dart
class SortedCollection {
  Function compare;

  SortedCollection(int f(Object a, Object b)) {
    compare = f;
  }
}

// 默认的实现
int sort(Object a, Object b) => 0;

void main() {
  SortedCollection coll = SortedCollection(sort);
  //我们只知道 compare 是一个函数，但函数的类型是什么？
  assert(coll.compare is Function);
}
```
上边的代码在把 `f` 赋值给 `compare` 的时候已经丢失了函数的类型信息。`f` 的类型是 `(Object, Object) → int` (→ 表示返回值)， `compare` 的类型是 Function。如果我们改变代码使用命名来保存类型信息，那么开发者和开发工具就都可以利用这个信息了：
```dart
typedef Compare = int Function(Object a, Object b);

class SortedCollection {
  Compare compare;
  SortedColloection(this.compare);
}

int sort(Object a, Object b) => 0;

void main() {
  SortedCollection coll = SortedCollectionn(sort);
  assert(coll.compare is Function);
  assert(coll.compare is Compare);
}
```
由于 typedef 仅仅是别名而已，他们只是提供了检查对函数类型的检查：
```dart
typedef Compare<T> = int Functionn(T a, T b);

int sort(int a, int b) => a - b;

void main() {
  assert(sort is Compare<int>); // true
}
```

## Metadata
通过 metadata 来给出代码的更多信息。 metadata 使用 `@` 来标注，后面紧跟一个编译时常量（比如 `deprecated`） 或者调用一个常量构造函数。

有两个标注是适用于所有 Dart 代码的： `@deprecated` 和 `@override` 。关于 `@override` 的使用，参考 [Extending a class](https://dart.dev/guides/language/language-tour#extending-a-class) 。下边是使用 `deprecated` 的例子：
```dart
class Television {
  /// _Deprecated: Use [tuenOn] instead .
  @deprecated
  void activate() {
    turnOn();
  }
  /// Turns the TV's power on.
  void turnOn() {...}
}
```
你也可以自定义标注，下边定义的 @todo 标注，它有两个参数：
```dart
library todo;

class Todo {
  final String who;
  finnal String what;
  const Todo(this.who, this.what);
}
```
Metadata 可以出现在 library, class, typedef, type parameter, constructor, factory, field, parameter 或 variable declaration 之前，也可以在 import 和 export 指令之前。你可以通过反射在运行时获取 metadata 。

## Comments
Dart 支持单行注释，多行注释和文档注释。

### Single-line commennts
单行注释以 `//` 开头，在 `//` 到行尾的任何内容都会被 Dart 编译器忽略掉。
```dart
void main() {
  // TODO: refactor into an AbstractLlamaGreetingFacyoty?
  print('Welcome to my Llama farm!');
}
```

### Multi-linne comments
多行注释以 `/*` 开头， 以 `*/` 结尾。Dart 编译器会忽略`/* */` 之间（文档注释除外，建下文）的所有内容。多行注释内部可能包含多行注释。
```dart
void main() {
  /*
   * This is a lot of work. Consider raising chikens.

   Llama larry = Llama();
   larry.feed();
   larry.exercise();
   larry.clean();
  */
}
```

### Documentation comments
文档注释可以是以 `///` 开头的单行注释或是以 `/**` 开头的多行注释。连续使用多行 `///` 与多行文档注释效果相同。

在文档注释中，Dart 编译器会忽略所有文字（在括号中间的除外）。你可以使用引号来引用类、方法、顶层变量、函数和参数。The names in brackets are resolved in the lexical scope of the documented program element.
下边的文档注释引用了其他类和参数：
```dart
/// A domesticated South Ameracan camelid (Lama glama).
///
/// Andean cultures have used llamas as meat and pack
/// animals since pre-Hispanic times.
class Llama {
  String name;
  /// Feeds yout llama [Food].
  ///
  /// The typical llama eats one bale of hay per week.
  void feed(Food food) {
    // ...
  }

  /// 
}
```
在生成的文档中， `[Food]` 就变成了指向Food 类的API 文档的链接。

你可以使用 Dart SDK 中的[文档工生成具](https://github.com/dart-lang/dartdoc#dartdoc)分析 Dart 代码来生成网页格式的文档。关于生成文档的例子，参考[Dart API documentation](https://api.dart.dev/stable)。关于如何编写注释的指南，见 [Guildelines for Dart Doc Comments](https://dart.dev/guides/language/effective-dart/documentation) 。

## Summary
本文简述了 Dart 语言中常用的特性。还有更多的特性正在实现中，我们希望新特性不会破坏与当前特性的兼容性。更多信息参考 [Dart language specification](https://dart.dev/guides/language/spec) 和 [Effective Dart](https://dart.dev/guides/language/effective-dart) 。

关于 Dart 核心库的更多内容，参考 [A Tour of the Dart Libraries](https://dart.dev/guides/libraries/library-tour)。




















