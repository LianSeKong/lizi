---
url: https://cs61a.org/hw/hw01/
title: Homework 1 
date: 2024-07-17 16:08:34
tag: 
banner: "https://images.unsplash.com/photo-1719749937847-ab76d3e0dbb7?crop=entropy&cs=srgb&fm=jpg&ixid=M3w0Njc1ODd8MHwxfHJhbmRvbXx8fHx8fHwxfHwxNzIxMjAzNjE1fA&ixlib=rb-4.0.3&q=85&fit=crop&w=624&max-h=540"
banner_icon: 🔖
---
sr-annote { all: unset; }

_Due by 11:59pm on Wednesday, June 26  
截止日期为 6 月 26 日星期三晚上 11：59_

## Instructions 指示

Download [hw01.zip](https://cs61a.org/hw/hw01/hw01.zip). 下载hw01.zip。

**Submission:** When you are done, submit the assignment by uploading all code files you've edited to Gradescope. You may submit more than once before the deadline; only the final submission will be scored. Check that you have successfully submitted your code on Gradescope. See [Lab 0](https://cs61a.org/lab/lab00#task-c-submitting-the-assignment) for more instructions on submitting assignments.  
提交：完成后，通过将已编辑的所有代码文件上传到 Gradescope 来提交作业。您可以在截止日期前提交不止一次;只有最终提交的作品才会被打分。检查您是否已在 Gradescope 上成功提交代码。有关提交作业的更多说明，请参阅实验 0。

**Using Ok:** If you have any questions about using Ok, please refer to [this guide.](https://cs61a.org/articles/using-ok)  
使用 Ok：如果您对使用 Ok 有任何疑问，请参阅本指南。

**Readings:** You might find the following references useful:  
阅读材料：您可能会发现以下参考资料很有用：

*   [Section 1.1 第1.1节](https://www.composingprograms.com/pages/11-getting-started.html)
*   [Section 1.2 第1.2节](https://www.composingprograms.com/pages/12-elements-of-programming.html)
*   [Section 1.3 第1.3节](https://www.composingprograms.com/pages/13-defining-new-functions.html)
*   [Section 1.4 第1.4节](https://www.composingprograms.com/pages/14-designing-functions.html)
*   [Section 1.5 第 1.5 节](https://www.composingprograms.com/pages/15-control.html)

**Grading:** Homework is graded based on correctness. Each incorrect problem will decrease the total score by one point. **This homework is out of 2 points.**  
评分：家庭作业根据正确性进行评分。每道题错，总分就会降低一分。这个作业满分2分。  

### Q1: A Plus Abs B  
Q1：A +  B的绝对值

Python's `operator` module contains two-argument functions such as `add` and `sub` for Python's built-in arithmetic operators. For example, `add(2, 3)` evalutes to 5, just like the expression `2 + 3`.  
Python `operator` 的模块包含两个参数函数，例如 `add` 和 `sub` for Python 的内置算术运算符。例如， `add(2, 3)` 求值为 5，就像表达式 `2 + 3` .

Fill in the blanks in the following function for adding `a` to the absolute value of `b`, without calling `abs`. You may **not** modify any of the provided code other than the two blanks.  
在以下函数中填空以将 的绝对值相加 `a` `b` ，而不调用 `abs` 。除两个空白之外，您不能修改任何提供的代码。

```
def a_plus_abs_b(a, b):
    """Return a+abs(b), but without calling abs.

    >>> a_plus_abs_b(2, 3)
    5
    >>> a_plus_abs_b(2, -3)
    5
    >>> a_plus_abs_b(-1, 4)
    3
    >>> a_plus_abs_b(-1, -4)
    3
    """
    if b < 0:
        f = _____
    else:
        f = _____
    return f(a, b)

```

Use Ok to test your code:  
使用“确定”测试代码：

```
python3 ok -q a_plus_abs_b

```

Use Ok to run the local syntax checker (which checks that you didn't modify any of the provided code other than the two blanks):  
使用 Ok 运行本地语法检查器（检查除两个空格之外是否未修改任何提供的代码）：

```shell
python3 ok -q a_plus_abs_b_syntax_check
```



:penguin:Simple problem

```python
def a_plus_abs_b(a, b):
    if b < 0:
        f = sub
    else:
        f = add
    return f(a, b)
```



### Q2: Two of Three Q2：三分之二

Write a function that takes three _positive_ numbers as arguments and returns the sum of the squares of the two smallest numbers. **Use only a single line for the body of the function.**  
编写一个函数，该函数将三个正数作为参数，并返回两个最小数的平方和。仅对函数的正文使用一行。

```python
def two_of_three(i, j, k):
    """Return m*m + n*n, where m and n are the two smallest members of the
    positive numbers i, j, and k.

    >>> two_of_three(1, 2, 3)
    5
    >>> two_of_three(5, 3, 1)
    10
    >>> two_of_three(10, 2, 8)
    68
    >>> two_of_three(5, 5, 5)
    50
    """
    return _____


```

**Hint:** Consider using the `max` or `min` function:  
提示：考虑使用 `max` or `min` 函数：

```python
>>> max(1, 2, 3)
3
>>> min(-1, -2, -3)
-3
```

Use Ok to test your code:  
使用“确定”测试代码：

```
python3 ok -q two_of_three
```

Use Ok to run the local syntax checker (which checks that you used only a single line for the body of the function):  
使用 Ok 运行本地语法检查器（检查是否仅对函数主体使用了一行）：

```
python3 ok -q two_of_three_syntax_check
```

​	:penguin:  

```python
def two_of_three(i, j, k):
    # return min(i * i + j * j, j * j + k * k, i * i + k * k)
    return i * i + j * j + k * k - max(i, j, k) * max(i, j, k)
```



### Q3: Largest Factor Q3：最大因素

Write a function that takes an integer `n` that is **greater than 1** and returns the largest integer that is smaller than `n` and evenly divides `n`.  
编写一个函数，该函数接受一个大于 1 的整数 `n` ，并返回小于 `n` 且平均除法的最大 `n` 整数。

```python
def largest_factor(n):
    """Return the largest factor of n that is smaller than n.

    >>> largest_factor(15) # factors are 1, 3, 5
    5
    >>> largest_factor(80) # factors are 1, 2, 4, 5, 8, 10, 16, 20, 40
    40
    >>> largest_factor(13) # factor is 1 since 13 is prime
    1
    """
    "*** YOUR CODE HERE ***"
```

**Hint:** To check if `b` evenly divides `a`, use the expression `a % b == 0`, which can be read as, "the remainder when dividing `a` by `b` is 0."  
提示：要检查是否 `b` 均匀除 `a` 以，请使用表达 `a % b == 0` 式，可以理解为“除 `a` 以 `b` 时的余数为 0”。

Use Ok to test your code:  
使用“确定”测试代码：

```python
python3 ok -q largest_factor
```

​	

​	:penguin:  

```python
def largest_factor(n):
    i = n - 1
    while i >= 1:
        if n % i == 0: 
            return i
        i = i -1
```

### Q4: Hailstone Q4：冰雹

Douglas Hofstadter's Pulitzer-prize-winning book, _Gödel, Escher, Bach_, poses the following mathematical puzzle.  
道格拉斯·霍夫施塔特（Douglas Hofstadter）的普利策奖获奖著作《哥德尔、埃舍尔、巴赫》提出了以下数学难题。

1.  Pick a positive integer `n` as the start.  
    选择一个正整数 `n` 作为开始。
2.  If `n` is even, divide it by 2.  
    如果 `n` 是偶数，则将其除以 2。
3.  If `n` is odd, multiply it by 3 and add 1.  
    如果 `n` 是奇数，则将其乘以 3 并加 1。
4.  Continue this process until `n` is 1.  
    继续此过程，直到 `n` 为 1。

The number `n` will travel up and down but eventually end at 1 (at least for all numbers that have ever been tried -- nobody has ever proved that the sequence will terminate). Analogously, a hailstone travels up and down in the atmosphere before eventually landing on earth.  
这个数字 `n` 会上下移动，但最终以 1 结束（至少对于所有曾经尝试过的数字——没有人证明序列会终止）。类似地，冰雹在最终降落在地球上之前在大气中上下移动。

This sequence of values of `n` is often called a Hailstone sequence. Write a function that takes a single argument with formal parameter name `n`, prints out the hailstone sequence starting at `n`, and returns the number of steps in the sequence:  
的这个值 `n` 序列通常称为冰雹序列。编写一个函数，该函数采用具有正式参数名称 `n` 的单个参数，打印出从 开始 `n` 的冰雹序列，并返回序列中的步骤数：

```python
def hailstone(n):
    """Print the hailstone sequence starting at n and return its
    length.

    >>> a = hailstone(10)
    10
    5
    16
    8
    4
    2
    1
    >>> a
    7
    >>> b = hailstone(1)
    1
    >>> b
    1
    """
    "*** YOUR CODE HERE ***"
```

Hailstone sequences can get quite long! Try 27. What's the longest you can find?  
冰雹序列可能会很长！尝试 27.你能找到的最长是多少？

Note that if `n == 1` initially, then the sequence is one step long.  
请注意，如果 `n == 1` 最初是，则序列长为一步。  
**Hint:** If you see 4.0 but want just 4, try using floor division `//` instead of regular division `/`.  
提示：如果您看到 4.0 但只想要 4，请尝试使用地板划分 `//` 而不是常规划分 `/` 。

Use Ok to test your code:  
使用“确定”测试代码：

```sh
python3 ok -q hailstone
```

**Curious about hailstones or hailstone sequences? Take a look at these articles:  
对冰雹或冰雹序列感到好奇吗？请看这些文章：**

*   Check out [this article](https://www.nationalgeographic.org/encyclopedia/hail/) to learn more about how hailstones work!  
    查看这篇文章以了解有关冰雹如何工作的更多信息！
*   In 2019, there was a major [development](https://www.quantamagazine.org/mathematician-terence-tao-and-the-collatz-conjecture-20191211/) in understanding how the hailstone conjecture works for most numbers!  
    2019 年，在理解冰雹猜想如何适用于大多数数字方面取得了重大进展！

​	:honeybee:

```python
def hailstone(n):
    length = 1
    while n != 1:
        print(n)
        if n % 2 == 0:
            n = n //2
        else:
            n = 3 * n + 1
        length = length + 1
    print(n)
    return length
```



## Check Your Score Locally 在本地查看您的分数

You can locally check your score on each question of this assignment by running  
您可以通过运行

```
python3 ok 

```

**This does NOT submit the assignment!** When you are satisfied with your score, submit the assignment to Gradescope to receive credit for it.  
这不会提交作业！当您对自己的分数感到满意时，请将作业提交给 Gradescope 以获得学分。

Submit this assignment by uploading any files you've edited **to the appropriate Gradescope assignment.** [Lab 00](https://cs61a.org/lab/lab00/#submit-with-gradescope) has detailed instructions.  
通过将您编辑的任何文件上传到相应的 Gradescope 作业来提交此作业。实验室 00 有详细说明。

If you completed all problems correctly, you should see that your score is 6.0 in the autograder output by Gradescope. Each homework assignment counts for 2 points, so in this case you will receive the full 2 points for homework. Remember that every incorrect question costs you 1 point, so a 5.0/6.0 on this assignment will translate to a 1.0/2.0 homework grade for this assignment.  
如果您正确完成了所有问题，您应该会在 Gradescope 的自动评分器输出中看到您的分数为 6.0。每份家庭作业计入 2 分，因此在这种情况下，您将获得完整的 2 分。请记住，每个错误的问题都会花费您 1 分，因此此作业的 5.0/6.0 将转换为此作业的 1.0/2.0 家庭作业成绩。