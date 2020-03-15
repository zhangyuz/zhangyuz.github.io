# A tour of the core libraries
原文参见 [A tour of the core libraries](https://dart.dev/guides/libraries/library-tour) 。

本文展示了 Dart 核心库的主要特性。本文只是概览，不保证连贯性。档需要了解某个类的更多信息，参考 [Dart API reference](https://api.dart.dev/stable) 。

[dart:core](https://dart.dev/guides/libraries/library-tour#dartcore---numbers-collections-strings-and-more)

内置类型、集合和其他核心功能。任何 Dart 程序都会自动导入此库。

[dart:async](https://dart.dev/guides/libraries/library-tour#dartasync---asynchronous-programming)

支持异步编程，包含了 Future 和 Stream 等类。

[dart:math](https://dart.dev/guides/libraries/library-tour#dartmath---math-and-random)

数学运算功能和常量，再加上一个随机数生成器。

[dart:convert](https://dart.dev/guides/libraries/library-tour#dartconvert---decoding-and-encoding-json-utf-8-and-more)

包含了在不同的数据表示之间进行转换的编解码器，包含 JSON 和 UTF-8。

[dart:html](https://dart.dev/guides/libraries/library-tour#darthtml)

DOM 和其他基于浏览器 App 的 API 。

[dart:io](https://dart.dev/guides/libraries/library-tour#dartio)

使用 Dart 虚拟机的 I/O 程序，包含 Flutter、服务器和命令行脚本。

本文只是概述，只包含了一些 dart:* 的库，不包含第三方库。

[pub.dev](https://pub.dev/) 和 [Dart web developer library guild](https://dart.dev/web/libraries) 包含了更多库的信息。 dart:* 库的 API 文档可以在 [Dart API reference](https://api.dart.dev/stable) , Flutter 的文档见 [Flutter API reference](https://api.flutter.dev/) 。

## dart:core - numbers, collection, strings, and more
dart:core 库体积很小，但是提供了 Dart 关键的内置功能。此库是自动导入每个 Dart 程序的。

### Printing to the console

顶层的 `print()` 方法只有一个参数（任意对象），它把对象的字符串的值（`toString()返回的字符串`）打印到终端。
```dart
print(anObject);
print('I drink $tea');
```
关于字符串和 `toString()` 函数，参考 [Strings](https://dart.dev/guides/language/language-tour#strings)

### Numbers
dart:core 中定义了 num, int 和 double 类，包含了使用数字类型的一些基本工具。

你可以调用 int 或 double 的 `parse()` 方法将字符串类型转换为整数或浮点数：
```dart
assert(int.parse('42') == 42);
assert(int.parse('0x42') == 66);
assert(double.parse('0.50') == 0.5);
```
或者你可以使用 num 的 parse() 方法，它会尽可能的返回整数类型，否则会返回 double 类型：
```dart
assert(num.parse('42') is int);
assert(num.parse('0x42' is int));
assert(num.parse('0.50') is double);
```
可以使用 `radix` 参数指定整数的进制（译注：二进制，十六进制）:
```dart
assert(int.parse('42', radix: 16) == 66);
```
是用 `toString()` 方法将整数或浮点数转换成字符串。使用 [toStringAsFixed](https://api.dart.dev/stable/dart-core/num/toStringAsFixed.html) 来指定小数点后数字的位数，使用 [toStringAsPrecision()](https://api.dart.dev/stable/dart-core/num/toStringAsPrecision.html) 来指定科学计数法的有效位数：
```dart
// 将 int 转换为字符串
assert(42.toString() == '42');
// 将 double 转换为字符串
assert(123.456.toStringAsFixed(2) == '123.46');
// 指定有效位数
assert(123.456.toStringAsPrecision(2) == '1.2e+2');
assert(double.parse('1.2e+2') == 120.0);
```
更多信息参考 API 文档， [int](https://api.dart.dev/stable/dart-core/int-class.html)，[double](https://api.dart.dev/stable/dart-core/double-class.html), [num](https://api.dart.dev/stable/dart-core/num-class.html), [dart:math](https://dart.dev/guides/libraries/library-tour#dartmath---math-and-random) 。

### Strings and regular expressions
Dart 中的字符串就是不可修改的 UTF-16 码值序列。Dart 语言教程中有更多关于 [strings](https://dart.dev/guides/language/language-tour#strings) 的信息。你可以使用正则表达式（RegExp 对象）搜索字符串、做字符串替换。

String 类定义了 `split()`, `contains()`, `startsWith()`, `endsWith()` 等方法。
#### Searching inside string
你可以在字符串中查找特定字符、确定是否以某个特定字符式样开始或结束：
```dart
// 检查字符串是否包含另一个字符串。
assert('Never odd or even'.contains('odd'));
// 是否以某一个字符串开始
assert('Never odd or even'.startsWith('Never'));
// 是否以某个字符串结尾
assert('Never odd or even'.endsWith('odd'));
// 查找子串的
assert('Nevenr odd or even'.indexOf('odd') = 6);
```
#### Extracting data from a string


## dart:async - asynchronous programming
异步编程经常使用回调函数，但是 Dart 提供了其他备选方案：[Future](https://api.dart.dev/stable/dart-async/Future-class.html) 和 [Stream](https://api.dart.dev/stable/dart-async/Stream-class.html) 。一个 Future 就像一个在未来某个时间返回数据的一个承诺。 Stream 是获取数据序列的一种方式，比如事件。关于 Future、Stream 等的更多文档参考 [dart:async API reference](https://api.dart.dev/stable/dart-async/dart-async-library.html) 。
> 很多时候你不需要自己直接使用 Future 和 Stream 的 API。Dart 语言支持使用 `async` 和 `await` 进行异步编程。参考 [asynchronous programming codelab](https://dart.dev/codelabs/async-await) 。

dart:async 库支持 web 和 命令行应用，导入后即可使用：
```dart
import dart:async;
```
### Future
对 Future 的使用遍布 Dart 的各种多，经常作为异步方法的返回值。当一个 future 运行完成后，它的值就可以使用了。
#### Using await
在直接使用 Future API 之前，要先考虑是否可以使用 `await` 代替。因为使用 `await` 表达式比使用 Future 的代码更容易理解。

看下边的代码。它使用了 Future 的 `then()` 方法来依次执行3个异步函数，等前一个执行完成后执行下一个。
```dart
runUsinngFuture() {
    // ...
    findEntryPoint().then((entryPoint) {
        return runExecutable(entryPoint, args);
    }).then(flushThenExit);
}
```
等效的 `await` 代码入下：
```dart
runUsingAsyncAwait() async {
    // ...
    var entryPoint = await findEntryPoint();
    var exitCode = await rumExecutable(entryPoint, args);
    await flushThenExit(exitCode);
}
```
`async` 函数可以处理 Future 的异常：
```dart
var entryPoint = await findEntryPoint();
try {
    var exitCode = await  runExecutable(entryPoint, args);
    await flushThenExit(exitCode);
} catch (e) {
    // Handle the error...
}
```
> 异步方法返回值类型是 Future 。如果不希望函数返回 Future 对象，你必须使用其他解决方案。比如调用 `async` 函数。

关于 `await` 的使用餐你考 [asynchronous programming codelab](https://dart.dev/codelabs/async-await) 。
#### Basic usage
使用 `then()` 来调度