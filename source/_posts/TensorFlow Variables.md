---
title: Variables:Creation, Initialization, Saving, and Loading
date: 2017-01-15 12:12
categories: TensorFlow
tags: TensorFLow

---

# Variables: Creation, Initialization, Saving, and Loading

在训练模型的过程中，我们使用 `Variable` 来保存和更新参数。`Variable` 是用来保存 Tensor[^1] 的内存缓冲区，你必须明确声明它们。无论是在训练的过程中或者之后，你都可以把它们保存到磁盘中。之后你可以从磁盘读取之前保存的内容来继续训练或者分析模型。

本文使用了下面的类，打开下面的链接查看详细文档：

- [tf.Variable](https://www.tensorflow.org/api_docs/python/state_ops/variables#Variable)
- [tf.train.Saver](https://www.tensorflow.org/api_docs/python/state_ops/saving_and_restoring_variables#Saver)

## Creation

创建 Variable 时你需要给它的构造函数 `tf.Variable()` 传递一个 Tensor[^1] 作为它的初始值。TensorFlow 提供了一系列使用常量或随机值来生成 Tensor 的操作。

你要知道这些操作都需要你指定生成的 Tensor 的形状，这个形状就自动成为了生成的 Variable 的形状。一般 Variable 的形状都是固定的，但是 TensorFlow 提供了修改 Variable 形状的高级机制。

```python
# Create two variables.
weights = tf.Variable(tf.random_normal([784, 200], stddev=0.35),
                      name="weights")
biases = tf.Variable(tf.zeros([200]), name="biases")
```

通过调用 `tf.Variable()` 函数，你对图添加了如下操作：

- 一个 `variable` 操作，用来保存变量的值。
- 一个初始化操作，设置变量的初始值，实际上是使用 `tf.assign` 操作来实现的。
- 把所有初始化操作都添加到图中，比如上面例子中 `biases` 的 `zeros` 操作。

`tf.Variable()` 的返回值是 Python 的 `tf.Variable` 类型的实例。

## Device placement

创建变量时，可以使用 `with tf.device(...):` 为变量分配特定的设备。

```python
# Pin a variable to CPU. 分配变量到CPU。
with tf.device("/cpu:0"):
  v = tf.Variable(...)

# Pin a variable to GPU.  分配变量到GPU。
with tf.device("/gpu:0"):
  v = tf.Variable(...)

# Pin a variable to a particular parameter server task. 分配变量到特定参数服务器任务。
with tf.device("/job:ps/task:7"):
  v = tf.Variable(...)
```

**注意：** 修改变量的操作(比如 [`v.assign()`](https://www.tensorflow.org/api_docs/python/state#Variable.assign))和 参数更新(比如  [`tf.train.Optimizer`](https://www.tensorflow.org/api_docs/python/train/optimizers#Optimizer) ) 操作都必须与相应的变量运行在相同设备上，否则在创建这些操作时，错误的设备分配指令会被忽略掉。

在设置重复的代码中，设备分配尤其重要。参考[`tf.train.replica_device_setter()`](https://www.tensorflow.org/api_docs/python/train/distributed_execution#replica_device_setter) 查看详细信息。

## Initialization

变量的初始化必须在你模型中任何其他操作运行之前明确(explicitly)的运行。最简单的办法就是增加一个初始化所有初始化函数的操作，并在你使用模型之前运行这个操作。

你还可以选择重 checkpoint 文件中恢复变量，这一点后文会详细介绍。

使用 `tf.global_variables_initializer()` 来增加运行初始化函数的操作，并在只在模型构造完成启动 Session 之后运行这个初始化操作。

```python
# Create two variables.
weights = tf.Variable(tf.random_normal([784, 200], stddev=0.35),
                      name="weights")
biases = tf.Variable(tf.zeros([200]), name="biases")
...
# Add an op to initialize the variables.
init_op = tf.global_variables_initializer()

# Later, when launching the model
with tf.Session() as sess:
  # Run the init operation.
  sess.run(init_op)
  ...
  # Use the model
  ...
```

### Initialization from another Variable

有时你需要使用一个变量来初始化另一个变量。由于 `tf.global_variables_initializer()` 添加的初始化操作是并行运行的，所以这时你要谨慎对待。

你需要使用一个变量的 `initialized_value()` 属性来初始化另一个变量。你既可以直接使用变量的值也可以把变量的值与其他 Tensor 做运算后的值作为新变量的初始值。

```python
# Create a variable with a random value.
weights = tf.Variable(tf.random_normal([784, 200], stddev=0.35),
                      name="weights")
# Create another variable with the same value as 'weights'.
w2 = tf.Variable(weights.initialized_value(), name="w2")
# Create another variable with twice the value of 'weights'
w_twice = tf.Variable(weights.initialized_value() * 2.0, name="w_twice")
```

### Custom Initialization

`tf.global_variables_initializer()` 这个方便的操作会向模型中增加初始化所有变量的操作。你也可以传递一个初始化列表来指定需要初始化的变量。参考[Variables Documentation](https://www.tensorflow.org/api_docs/python/state_ops)， 其中也包含了如何检查变量是否已经初始化过。

## Saving and Restoring

保存和回复模型最简单的方法就是使用 `tf.train.Saver` 对象。它的构造函数会为图中多有变脸或指定的变量增加 `save` 和 `restore` 操作。`Saver` 对象提供了执行这些操作以及指定 checkpoint 文件的方法。

### Checkpoint Files

变量保存在二进制文件中，这个文件基本包含了从变量名字到对应 Tensor 的映射。

创建 `Saver` 的时候，你可以指定需要存储的变量的名字，默认使用 [`Variable.name`](https://www.tensorflow.org/api_docs/python/state_ops/variables#Variable.name) 作为变量的名字。

你可以使用  [`inspect_checkpoint`](https://www.tensorflow.org/code/tensorflow/python/tools/inspect_checkpoint.py) 库中的 `print_tensors_in_checkpoint_file` 来获取 checkpoint 文件中包含的变量信息。

### Saving Variables

使用 `tf.train.Saver()` 创建 `Saver` 对象来管理模型中的所有变量。

```python
# Create some variables.
v1 = tf.Variable(..., name="v1")
v2 = tf.Variable(..., name="v2")
...
# Add an op to initialize the variables.
init_op = tf.global_variables_initializer()

# Add ops to save and restore all the variables.
saver = tf.train.Saver()

# Later, launch the model, initialize the variables, do some work, save the
# variables to disk.
with tf.Session() as sess:
  sess.run(init_op)
  # Do some work with the model.
  ..
  # Save the variables to disk.
  save_path = saver.save(sess, "/tmp/model.ckpt")
  print("Model saved in file: %s" % save_path)
```

### Restoring Variables

使用同一个 `Saver` 对象来恢复变量。注意，从文件中恢复变量时，你不需要事前先初始化这些变量。

```python
# Create some variables.
v1 = tf.Variable(..., name="v1")
v2 = tf.Variable(..., name="v2")
...
# Add ops to save and restore all the variables.
saver = tf.train.Saver()

# Later, launch the model, use the saver to restore variables from disk, and
# do some work with the model.
with tf.Session() as sess:
  # Restore variables from disk.
  saver.restore(sess, "/tmp/model.ckpt")
  print("Model restored.")
  # Do some work with the model
  ...
```



### Choosing which Variables to Save and Restore

如果你不向 `tf.train.Saver()` 传递任何参数，它会默认处理图中的所有变量。每个变量都保存在创建时指定的名字下。

有时明确指定保存在 checkpoint 文件中变量的名字是非常有用的。比如你训练的模型中包含一个叫 `weights` 的变量，而你想把它恢复到叫做 `params` 的新变量中。

在你只想保存和恢复现有变量的子集的时候页非常有用。比如你之前训练了一个五层的神经网络，现在你想训练一个六层的神经网络，那么你可以恢复之前训练好的的五层神经网络的参数到新模型的前五层。

你还可以传递一个字典给 `tf.train.Saver()` 来指定需要保存的变量名字和变量，字典的 key 是名字，value 是变量。

注意：

- 你可以按需创建多个 `Saver` 对象，同一个变量可以被多个 `Saver` 操作，只有在调用 `saver.restore()` 的时候变量的值才会改变。
- 如果你只在 Session 开始的时候恢复模型中的部分变量，那么你必须运行初始化操作来初始化其他的变量。参考 [`tf.initialize_variables()`](https://www.tensorflow.org/api_docs/python/state_ops/exporting_and_importing_meta_graphs#initialize_variables) 。



```python
# Create some variables.
v1 = tf.Variable(..., name="v1")
v2 = tf.Variable(..., name="v2")
...
# Add ops to save and restore only 'v2' using the name "my_v2"
saver = tf.train.Saver({"my_v2": v2})
# Use the saver object normally after that.
...
```

[^1]: [张量](https://www.wikiwand.com/zh-hans/%E5%BC%B5%E9%87%8F)

