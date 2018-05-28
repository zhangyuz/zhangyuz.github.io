---
Title: 逻辑回归
Date: 2016-12-31 19:48
Tags: MachineLearning,机器学习,LogisticRegression,逻辑回归
Slug: LogisticRegression
Summary: 吴恩达机器学习笔记

---

[原址](https://www.coursera.org/learn/machine-learning/home/week/3)
# Classification and Representation

逻辑回归是把数据分类为离散数据的一种方法。比如区分垃圾邮件。

## Classification 分类
分类在本质上并不是线性回归。

分类问题与回归问题的的区别是我们要预测的值仅仅是有限的离散值。目前集中讨论二值分类的问题（比如分类的结果仅为1和0）。比如我们想构建一个垃圾邮件分配器，$x^{(i)}$是邮件的一些特征，y=1表示垃圾邮件，y=0表示非垃圾邮件，因此y∈{0,1}。0也称为负类，1成为正类。有时使用"-"和"+"表示。对于指定的$x^{(i)}$，$y^{(i)}$也称为训练的标签（label）。

## Hypothesis Representation 假设函数的表示

![假设函数]({filename}HypothesisRepresentation01.png)

我们想要的结果是 $0≤h_θ≤1$。
$g(z)=\frac{1}{1+e^{-z}}$ 取值区间为0到1的S型曲线，称为SigmoidFunction或者LogisticFunction。如上图右下角图形所示。
逻辑回归的表达式为$h_θ(x) = θ^Tx$，为了让$h_θ$的值位于0到1之间，所以使$$h_θ(x) = g(θ^Tx) = \frac{1}{1+e^{-θ^Tx}}$$
接下来就是用参数θ来拟合我们的数据集。

![对假设函数的解释]({filename}HypothesisRepresentation02.png)
上图是对假设函数的理解：
$h_θ(x)$ 的意义是输入为x的时候y=1的概率。例如在某个情况下$h_θ(x) = 0.7$ 我们说在输入x为tumorSize的情况下，y为1的概率是0.7。
$h_θ(x) = P(y=1|x;θ)$


## Decision Boundary

![假设函数使用]({filename}DecisionBoundary01.png)
假设函数的值大于等于0.5的时候我们预测y等于1，小于0.5预测y等于0。
观察S型曲线，发现：
z大于等于0的时候，$g(z)≥0.5$，y=1，既$θ^Tx ≥ 0$.
z小于0的时候，$g(z)＜0.5$，y=0，既$θ^Tx ＜ 0$.


### 线性决策边界
![DecisionBoundary]({filename}DecisionBoundary02.png)
假设$h_θ(x) = g(θ_0 + θ_1x_1 + θ_2x_2)$，$θ_0 = -3$,$θ_1 = 1$, $θ_2 =1$： 
如果预测y=1，那么$-3 + x_1 + x_2 ≥ 0$ 既 $x_1 + x_2 ≥3$
上图左上角坐标系，洋红色直线为$h_θ(x) = 0.5$，是y预测为1或0的分界线。直线上方，y=1，直线下方y=1。这条直线就成为DecisionBoundary，决策边界。

**DecisionBoundary是假设函数的特性而不是数据集的属性。**

### 非线性决策边界

![Non-LinearDecisionBoundary]({filename}DecisionBoundary03.png)
假设$h_θ(x) = g(θ_0 + θ_1x_1 + θ_2x_2 + θ_3x_1^2 + θ_4x_2^2)$，
$θ_0=-1$,$θ_1=0$,$θ_2=0$,$θ_3=1$,$θ_4=1$
那么如果$-1 + x_1^2 + x_2^2 ≥ 0$ 预测为y=1。那么决策边界就是一个圆形。
假设$h_θ(x) = g(θ_0 + θ_1x_1 + θ_2x_2 + θ_3x_1^2 + θ_4x_2^2 + θ_5x_1^3 + θ_6x_2^3 + ...)$，那么它的决策边界可能是各种奇形怪状的。



¹²³≈≡≠＝≤≥＜＞≮≯∷±∓＋－×÷／
∫∮∝∞∧∨∑∏∪∩∈∵∴⊥∥∠⌒⊙≌∽√
αβγδεζηθικλμνξοπρστυφχψω ΑΒΓΔΕΖΗΘΙΚΛΜΝΞΟΠΡΣΤΥΦΧΨΩ
§№☆★○●◎◇◆□℃‰■△▲※→←↑↓↖↗↘↙
〓¤°＃＆＠＼︿＿￣―♂♀～Δ
㈠㈡㈢㈣㈤㈥㈦㈧㈨㈩①②③④⑤⑥⑦⑧⑨⑩

# Logistic Regression Model

## Cost Function

## Simplified Cost Function and Gradient Descent

## Advanced Optimization

# Multiclass Classification







