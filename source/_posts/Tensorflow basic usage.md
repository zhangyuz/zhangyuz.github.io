---
title: TensorFlow基本使用
date: 2016 12-25 15:01
categories:
    - TensorFlow
tags:
    - TensorFlow

---

本文为原文不完整翻译以及学习笔记。
[原文点这里。](https://www.tensorflow.org/get_started/basic_usage)

# TensorFlow Basic Usage


使用TensorFlow之前，你需要理解TensorFlow是如何：

- 使用图表示计算
- 在 `Sessions` 的上下文中执行图
- 使用  `Tensor` (张量)表示数据
- 使用 `Variables` (变量)维护状态的
- 在任意操作中使用 `feeds` 和 `fetches` 设置和获取数据

## 概述(Overview)

TensorFlow是一个使用图来表示计算的编程系统。图中的节点称为 `op`(译注: operations 的缩写)。`op` 的输入为0或多个 `Tensor`，进行计算，生成0或多个 `Tensor`。在TensorFlow的术语中，1个 `Tensor` 是一个包含类型约束的多维数组。例如你可以使用类似 `[batch, heigh, width, channels]` 的4维浮点数组表示一小批图片。

1个TensorFlow图就是==对计算的描述==。进行任何计算之前，都必须先在一个 `Session` 中生成图。`Session` 负责把图中的操作（`op`）分配给 `Devices`（比如CPU、GPU），并且提供执行这些操作的方法。这些方法返回操作（译注：`ops`）产生的张量（译注：`Tensor`）（比如 `numpy` 中的`ndarray` Python对象，C/C++中 `tensorflow::Tensor`实例）。

## 计算图(The computation graph)
TensorFlow程序一般结构上分为两个阶段：构造阶段，生成图；执行阶段，使用 session 来执行图中的操作。
比如，通常在构造阶段创建图来表示、训练神经网络，然后在执行阶段重复的执行图中的一系列训练操作。
TensorFlow支持C/C++和Python。目前，使用Python库来生成图相对来说更方便，因为Python的各种库包含大量的帮助函数，这是C/C++的库所没有的。
Session库对于三种语言都提供了相同的功能。

### 构造图(Building the graph)
我们可以利用无需输入的操作 `ops` （比如 `Constant`）来从最开始构造一个图（译注：`graph`），并把这些操作的输出传递给其他可以进行计算的操作 `ops`。
使用 Python 库提供的操作(`ops`) 构造函数返回构造好的操作(`ops`)对象。你可以把这些构造好的操作 (`ops`)作为输入传递给其他的操作(`ops`)构造函数。
TensorFlow 的 Python 库包含一个默认的图， 操作(`ops`) 构造函数生成的节点默认添加到其中。对于多数应用，这个默认的图已经足够。关于如何手动管理多个图参考 [Graph class](https://www.tensorflow.org/api_docs/python/framework/core_graph_data_structures#Graph)。
```Python
import tensorflow as tf

# 创建生成1*2矩阵的常量操作。
# 这个操作作为一个节点（译注：node）被添加到默认图中。
#
# 构造器的返回值表示常量操作的输出。
matrix1 = tf.constant([[3., 3.]])

# 创建生成2*1矩阵的另一个常量。
matrix2 = tf.constant([[2.],[2.]])

# 创建Matmul操作，把 ‘matrix1’ 和 ‘matrix2’ 作为输入。
# 返回值 'product' 表示矩阵乘法的结果。
product = tf.matmul(matrix1, matrix2)
```
现在默认的图包含了3个节点：两个 `contant()`操作和一个 `matmul()` 操作。为了实际进行矩阵乘法和获得乘法的结果，必须在 `session` 中启动图(`graph`)。

### 在 Session 中运行图(Launching the graph in a session)

在构造图之后就可以运行图了。运行图之前需要先创建 `Session` 对象。不需要参数的Session构造函数（译注：session constructor）就会运行默认图。
参考 [Session class](https://www.tensorflow.org/api_docs/python/client#session_management)查看完整的Session API文档。
```Python
# 启动默认图
sess = tf.Session()

# 调用session的run()函数并把 ‘product’（表示matmul操作的输出）作为传输传递给它来运行 matmul操作。
# 这表示我们想要 ‘matmul’ 操作的输出。
#
# 操作所需的参数由 session 自动运算并传递。
# 通常这些运算是并行运行的。
#
# 对 ‘run(product)’ 的调用使图中3个操作得以执行：2个常量和一个矩阵乘法。
#
# 矩阵乘法的输出 ‘numpy’ 的 ‘ndarray’ 对象保存在 ‘result’ 中。
result = sess.run(product)
print(result)
# ==> [[ 12.]]

# 完工，关闭session。
sess.close()
```
使用完后关闭 Session 释放资源。也可以用 `with` 语句块进入 `Session`中执行。`Session` 会在 `with` 代码块结束后自动关闭。
```Python
with tf.Session() as sess:
  result = sess.run([product])
  print(result)
```
TensorFlow把图定义翻译成可被分配到计算资源（CPU、GPU）的可执行操作。一般来说你不需要指定CPU或GPU。TensorFlow会使用你的第一个GPU进行尽可能多的操作。
如果你的机器包含不止一个GPU，为了使用其他的GPU，你必须手动分配操作到指定GPU。使用 `with...Device` 语句来为操作指定CPU或GPU设备：
```Python
with tf.Session() as sess:
  with tf.device("/gpu:1"):
    matrix1 = tf.constant([[3., 3.]])
    matrix2 = tf.constant([[2.],[2.]])
    product = tf.matmul(matrix1, matrix2)
    ...
```
设备是使用字符串指定。目前支持的设备包括：

- `"/cpu:0"`：你机器的CPU。
- `"/gpu:0"`：你机器的GPU。
- `"/gpu:1"`：你机器的第二块GPU。

参考[Using GPUs](https://www.tensorflow.org/how_tos/using_gpu/index)查看TensorFlow使用GPU的更多信息。

### 在分布式Session中启动图(Launching the graph in a distributed session)

为了创建 TensorFlow 集群，需要在集群中的每台机器上都启动 TensorFlow 服务器。在客户机上初始化 `Session` 的时候，把集群中某个机器的网络地址传递给 `Session()` 函数：
```Python
with tf.Session("grpc://example.org:2222") as sess:
  # Calls to sess.run(...) will be executed on the cluster.
  ...
```
这台机器就成为这个 session 的控制者（译注：master）。Master把图分配到集群中的其它机器上，与本地把图分配到本地计算资源非常类似。
你可以使用 `with tf.device()` 语句来为图的特定部分直接指定计算资源：
```Python
with tf.device("/job:ps/task:0"):
  weights = tf.Variable(...)
  biases = tf.Variable(...)
```
参考 [Distributed TensorFlow How To](https://www.tensorflow.org/how_tos/distributed/) 查看分布式 Session 和集群的更多信息。

## 交互式使用
上面Python的例子使用 `Session` 构造图，使用 `Session.run()` 来执行操作。
为了方便在交互式环境中使用TensorFlow，比如IPython，你可以使用 `InteractiveSession`类和 `Tensor.eval()` 和 `Operation.run()` 函数。这样可以避免使用变量来保存 session。
```Python
# Enter an interactive TensorFlow Session.
import tensorflow as tf
sess = tf.InteractiveSession()

x = tf.Variable([1.0, 2.0])
a = tf.constant([3.0, 3.0])

# Initialize 'x' using the run() method of its initializer op.
x.initializer.run()

# Add an op to subtract 'a' from 'x'.  Run it and print the result
sub = tf.sub(x, a)
print(sub.eval())
# ==> [-2. -1.]

# Close the Session when we're done.
sess.close()
```

## 张量(Tensors)
TensorFlow 程序使用张量数据结构表示所有的数据，在计算图的操作中只能传递张量数据类型。你可以把TensorFlow中的张量想象成一个n维数组或队列。一个张量使用固定的类型、行和形状。参考 [Rank, Shape, and Type](https://www.tensorflow.org/resources/dims_types) 获取 TensorFlow 如何处理这些概念的信息。

## 变量(Variables)
变量(Variables)在图的计算过程中维护状态。下面的例子展示作为计数器的变量。参考 [Variables](https://www.tensorflow.org/how_tos/variables/index) 了解更多细节。
```Python
# 创建一个变量，以后会被初始化为标量0。
state = tf.Variable(0, name="counter")

# 创建一个操作，为 ‘state’ 增加1。

one = tf.constant(1)
new_value = tf.add(state, one)
update = tf.assign(state, new_value)

# 必须在初始化图后运行 ‘init’ 操作才能初始化变量。
# 首先必须把 ‘init’ 操作添加到图中。
init_op = tf.global_variables_initializer()

# Launch the graph and run the ops.
with tf.Session() as sess:
  # Run the 'init' op
  sess.run(init_op)
  # Print the initial value of 'state'
  print(sess.run(state))
  # Run the op that updates 'state' and print 'state'.
  for _ in range(3):
    sess.run(update)
    print(sess.run(state))

# output:

# 0
# 1
# 2
# 3
```
上面代码中的 `assign()` 操作与 `add()` 操作一天都是是图表达式的一部分，直到 `run()` 函数运行它的时候才会实际执行这个赋值操作。
>==译注：所有的操作(op)都在run()中执行，变量都在初始化的时候初始化，用来生成图。==

典型的做法使用 `Variable` 的集合来表示静态模型的参数。例如，你应该把神经网络的权重参数作为张量保存在 `Variable`中。在训练过程中通过不断重复的训练这个图来更新这个张量。

## Fetches
获取操作的输出，调用 `Session.run()` 方法，把希望获取的张量传递进来作为参数就可以。在上边的例子中我们获取了节点状态，我们还可以获取更多张量的值：
```Python
input1 = tf.constant([3.0])
input2 = tf.constant([2.0])
input3 = tf.constant([5.0])
intermed = tf.add(input2, input3)
mul = tf.mul(input1, intermed)

with tf.Session() as sess:
  result = sess.run([mul, intermed])
  print(result)

# output:
# [array([ 21.], dtype=float32), array([ 7.], dtype=float32)]
```
> ==注意：所有需要产生请求结果的操作都只运行一次（译注：本例中intermed这个操作只运行了一次），而不是每次请求操作都运行一次。==

## Feeds
上面所有例子中输入给计算图的张量都是使用 `Constants` 和 `Variables` 存储的。TensorFlow 同时提供 Feed(填充) ==（译注：feed，就是喂数据的意思）==机制来直接修改图中任何操作 ` op` 的数据。
Feed 使用一个张量临时代替某个操作的输出。把填充数据作为参数传递给 `run()` 函数。填充数据仅仅用于当前传递的操作。最常见的例子就是使用 `tf.placeholder()` 来创建操作，并指定某个操作作为 Feed 操作。
```Python
input1 = tf.placeholder(tf.float32)
input2 = tf.placeholder(tf.float32)
output = tf.mul(input1, input2)

with tf.Session() as sess:
  print(sess.run([output], feed_dict={input1:[7.], input2:[2.]}))

# output:
# [array([ 14.], dtype=float32)]
```
对于`placeholder()` 操作，如果不提供 Feed 运行时会产生错误。参考[MNIST fully-connected feed tutorial](https://www.tensorflow.org/tutorials/mnist/tf/index) 查看更多种子的例子。
