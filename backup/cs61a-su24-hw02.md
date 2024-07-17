## Instructions

Download [hw02.zip](https://cs61a.org/hw/hw02/hw02.zip). Inside the archive, you will find a file called [hw02.py](https://cs61a.org/hw/hw02/hw02.py), along with a copy of the `ok` autograder.

**Submission:** When you are done, submit the assignment by uploading all code files you've edited to Gradescope. You may submit more than once before the deadline; only the final submission will be scored. Check that you have successfully submitted your code on Gradescope. See [Lab 0](https://cs61a.org/lab/lab00#task-c-submitting-the-assignment) for more instructions on submitting assignments.

**Using Ok:** If you have any questions about using Ok, please refer to [this guide.](https://cs61a.org/articles/using-ok)

**Readings:** You might find the following references useful:

*   [Section 1.6](https://www.composingprograms.com/pages/16-higher-order-functions.html)
*   [Section 1.7](https://www.composingprograms.com/pages/17-recursive-functions.html)

**Grading:** Homework is graded based on correctness. Each incorrect problem will decrease the total score by one point. **This homework is out of 2 points.**

Several doctests refer to these functions:

```
from operator import add, mul

square = lambda x: x * x

identity = lambda x: x

triple = lambda x: 3 * x

increment = lambda x: x + 1

```

## Higher-Order Functions

### Q1: Product

Write a function called `product` that returns the product of the first `n` terms of a sequence. Specifically, `product` takes in an integer `n` and `term`, a single-argument function that determines a sequence. (That is, `term(i)` gives the `i`th term of the sequence.) `product(n, term)` should return `term(1) * ... * term(n)`.

```
def product(n, term):
    """Return the product of the first n terms in a sequence.

    n: a positive integer
    term:  a function that takes one argument to produce the term

    >>> product(3, identity)  # 1 * 2 * 3
    6
    >>> product(5, identity)  # 1 * 2 * 3 * 4 * 5
    120
    >>> product(3, square)    # 1^2 * 2^2 * 3^2
    36
    >>> product(5, square)    # 1^2 * 2^2 * 3^2 * 4^2 * 5^2
    14400
    >>> product(3, increment) # (1+1) * (2+1) * (3+1)
    24
    >>> product(3, triple)    # 1*3 * 2*3 * 3*3
    162
    """
    "*** YOUR CODE HERE ***"


```

Use Ok to test your code:

```
python3 ok -q product

```

:bear: <u>比较简单，就不解释了</u>

```python
def product(n, term):
    i, count = 1, 1
    while i <= n:
        count, i = mul(count, term(i)), add(i, 1)
    return count
```

### Q2: Accumulate

Let's take a look at how `product` is an instance of a more general function called `accumulate`, which we would like to implement:

```
def accumulate(fuse, start, n, term):
    """Return the result of fusing together the first n terms in a sequence 
    and start.  The terms to be fused are term(1), term(2), ..., term(n). 
    The function fuse is a two-argument commutative & associative function.

    >>> accumulate(add, 0, 5, identity)  # 0 + 1 + 2 + 3 + 4 + 5
    15
    >>> accumulate(add, 11, 5, identity) # 11 + 1 + 2 + 3 + 4 + 5
    26
    >>> accumulate(add, 11, 0, identity) # 11 (fuse is never used)
    11
    >>> accumulate(add, 11, 3, square)   # 11 + 1^2 + 2^2 + 3^2
    25
    >>> accumulate(mul, 2, 3, square)    # 2 * 1^2 * 2^2 * 3^2
    72
    >>> # 2 + (1^2 + 1) + (2^2 + 1) + (3^2 + 1)
    >>> accumulate(lambda x, y: x + y + 1, 2, 3, square)
    19
    """
    "*** YOUR CODE HERE ***"


```

`accumulate` has the following parameters:

*   `fuse`: a two-argument function that specifies how the current term is fused with the previously accumulated terms
*   `start`: value at which to start the accumulation
*   `n`: a non-negative integer indicating the number of terms to fuse
*   `term`: a single-argument function; `term(i)` is the `i`th term of the sequence

Implement `accumulate`, which fuses the first `n` terms of the sequence defined by `term` with the `start` value using the `fuse` function.

For example, the result of `accumulate(add, 11, 3, square)` is

```
add(11,  add(square(1), add(square(2),  square(3)))) =
    11 +     square(1) +    square(2) + square(3)    =
    11 +     1         +    4         + 9            = 25

```

Assume that `fuse` is commutative, `fuse(a, b) == fuse(b, a)`, and associative, `fuse(fuse(a, b), c) == fuse(a, fuse(b, c))`.

Then, implement `summation` (from lecture) and `product` as one-line calls to `accumulate`.

**Important:** Both `summation_using_accumulate` and `product_using_accumulate` should be implemented with a single line of code starting with `return`.

```
def summation_using_accumulate(n, term):
    """Returns the sum: term(1) + ... + term(n), using accumulate.

    >>> summation_using_accumulate(5, square) # square(0) + square(1) + ... + square(4) + square(5)
    55
    >>> summation_using_accumulate(5, triple) # triple(0) + triple(1) + ... + triple(4) + triple(5)
    45
    >>> # This test checks that the body of the function is just a return statement.
    >>> import inspect, ast
    >>> [type(x).__name__ for x in ast.parse(inspect.getsource(summation_using_accumulate)).body[0].body]
    ['Expr', 'Return']
    """
    return ____

def product_using_accumulate(n, term):
    """Returns the product: term(1) * ... * term(n), using accumulate.

    >>> product_using_accumulate(4, square) # square(1) * square(2) * square(3) * square()
    576
    >>> product_using_accumulate(6, triple) # triple(1) * triple(2) * ... * triple(5) * triple(6)
    524880
    >>> # This test checks that the body of the function is just a return statement.
    >>> import inspect, ast
    >>> [type(x).__name__ for x in ast.parse(inspect.getsource(product_using_accumulate)).body[0].body]
    ['Expr', 'Return']
    """
    return ____


```

Use Ok to test your code:

```
python3 ok -q accumulate
python3 ok -q summation_using_accumulate
python3 ok -q product_using_accumulate

```

:bear: `accumulate： 对product的每次相乘行为替换为抽象的函数`

```python
def accumulate(fuse, start, n, term):
    i, count = 1, start
    while i <= n:
        start, i = merger(fuse, term(i)), add(i + 1)
    return count
```

:bear: `summation_using_accumulate: product的变体`

```python
def summation_using_accumulate(n, term):
    return accumulate(add, term(0), n, term)
```

:bear: `sproduct_using_accumulate: product的变体`

```python
def product_using_accumulate(n, term):
 	return accumulate(mul, 1, n, term)
```



### Q3: Make Repeater

Implement the function `make_repeater` which takes a one-argument function `f` and a positive integer `n`. It returns a one-argument function, where `make_repeater(f, n)(x)` returns the value of `f(f(...f(x)...))` in which `f` is applied `n` times to `x`. For example, `make_repeater(square, 3)(5)` squares 5 three times and returns 390625, just like `square(square(square(5)))`.

```
def make_repeater(f, n):
    """Returns the function that computes the nth application of f.

    >>> add_three = make_repeater(increment, 3)
    >>> add_three(5)
    8
    >>> make_repeater(triple, 5)(1) # 3 * (3 * (3 * (3 * (3 * 1))))
    243
    >>> make_repeater(square, 2)(5) # square(square(5))
    625
    >>> make_repeater(square, 3)(5) # square(square(square(5)))
    390625
    """
    "*** YOUR CODE HERE ***"


```

Use Ok to test your code:

```
python3 ok -q make_repeater

```

:bear:  

```python
def make_repeater(f, n):
    def helper(x):
        i = n;
        while i:
            x = f(x)
            i = i - 1;
        return x
    return helper
```



## Recursion

### Q4: Digit Distance

For a given integer, the _digit distance_ is the sum of the absolute differences between consecutive digits. For example:

*   The digit distance of `6` is `0`.
*   The digit distance of `61` is `5`, as the absolute value of `6 - 1` is `5`.
*   The digit distance of `71253` is `12` (`6 + 1 + 3 + 2`).

Write a function that determines the digit distance of a given positive integer. You must use recursion or the tests will fail.

**Hint:** There are multiple valid ways of solving this problem! If you're stuck, try writing out an iterative solution first, and then convert your iterative solution into a recursive one.

```
def digit_distance(n):
    """Determines the digit distance of n.

    >>> digit_distance(3)
    0
    >>> digit_distance(777)
    0
    >>> digit_distance(314)
    5
    >>> digit_distance(31415926535)
    32
    >>> digit_distance(3464660003)
    16
    >>> from construct_check import check
    >>> # ban all loops
    >>> check(HW_SOURCE_FILE, 'digit_distance',
    ...       ['For', 'While'])
    True
    """
    "*** YOUR CODE HERE ***"


```

Use Ok to test your code:

```
python3 ok -q digit_distance

```

:bear:

```python
def digit_distance(n):
    if n < 10:
        return 0
    else:
        return abs(sub(n % 10, n // 10 % 10)) + digit_distance(n // 10)
```



### Q5: Interleaved Sum

Write a function `interleaved_sum`, which takes in a number `n` and two one-argument functions: `odd_func` and `even_func`. It applies `odd_func` to every odd number and `even_func` to every even number from 1 to `n` _inclusive_ and returns the sum.

For example, executing `interleaved_sum(5, lambda x: x, lambda x: x * x)` returns `1 + 2*2 + 3 + 4*4 + 5 = 29`.

**Important:** Implement this function without using any loops or directly testing if a number is odd or even -- aka modulos (`%`) are not allowed! Instead of directly checking whether a number is even or odd, start with 1, which you know is an odd number.

**Hint:** Introduce an inner helper function that takes an odd number `k` and computes an interleaved sum from `k` to `n` (including `n`).

```
def interleaved_sum(n, odd_func, even_func):
    """Compute the sum odd_func(1) + even_func(2) + odd_func(3) + ..., up
    to n.

    >>> identity = lambda x: x
    >>> square = lambda x: x * x
    >>> triple = lambda x: x * 3
    >>> interleaved_sum(5, identity, square) # 1   + 2*2 + 3   + 4*4 + 5
    29
    >>> interleaved_sum(5, square, identity) # 1*1 + 2   + 3*3 + 4   + 5*5
    41
    >>> interleaved_sum(4, triple, square)   # 1*3 + 2*2 + 3*3 + 4*4
    32
    >>> interleaved_sum(4, square, triple)   # 1*1 + 2*3 + 3*3 + 4*3
    28
    >>> from construct_check import check
    >>> check(HW_SOURCE_FILE, 'interleaved_sum', ['While', 'For', 'Mod']) # ban loops and %
    True
    >>> check(HW_SOURCE_FILE, 'interleaved_sum', ['BitAnd', 'BitOr', 'BitXor']) # ban bitwise operators, don't worry about these if you don't know what they are
    True
    """
    "*** YOUR CODE HERE ***"


```

Use Ok to test your code:

```
python3 ok -q interleaved_sum

```

:bear:`interleaved_sum`

```python
def interleaved_sum(n, odd_func, even_func):
    if n == 0:
        return 0
    elif n % 2 == 1:
        return odd_func(n) + interleaved_sum(n - 1, odd_func, even_func)
    else:
        return even_func(n) + interleaved_sum(n - 1, odd_func, even_func)

```



### Q6: Count Coins

Given a positive integer `total`, a set of coins makes change for `total` if the sum of the values of the coins is `total`. Here we will use standard US Coin values: 1, 5, 10, 25. For example, the following sets make change for `15`:

*   15 1-cent coins
*   10 1-cent, 1 5-cent coins
*   5 1-cent, 2 5-cent coins
*   5 1-cent, 1 10-cent coins
*   3 5-cent coins
*   1 5-cent, 1 10-cent coin

Thus, there are 6 ways to make change for `15`. Write a **recursive** function `count_coins` that takes a positive integer `total` and returns the number of ways to make change for `total` using coins.

You can use _either_ of the functions given to you:

*   `next_larger_coin` will return the next larger coin denomination from the input, i.e. `next_larger_coin(5)` is `10`.
*   `next_smaller_coin` will return the next smaller coin denomination from the input, i.e. `next_smaller_coin(5)` is `1`.
*   Either function will return `None` if the next coin value does not exist

There are two main ways in which you can approach this problem. One way uses `next_larger_coin`, and another uses `next_smaller_coin`. It is up to you which one you want to use!

**Important:** Use recursion; the tests will fail if you use loops.

**Hint:** Refer to the [implementation](https://www.composingprograms.com/pages/17-recursive-functions.html#example-partitions) of `count_partitions` for an example of how to count the ways to sum up to a final value with smaller parts. If you need to keep track of more than one value across recursive calls, consider writing a helper function.

```
def next_larger_coin(coin):
    """Returns the next larger coin in order.
    >>> next_larger_coin(1)
    5
    >>> next_larger_coin(5)
    10
    >>> next_larger_coin(10)
    25
    >>> next_larger_coin(2) # Other values return None
    """
    if coin == 1:
        return 5
    elif coin == 5:
        return 10
    elif coin == 10:
        return 25

def next_smaller_coin(coin):
    """Returns the next smaller coin in order.
    >>> next_smaller_coin(25)
    10
    >>> next_smaller_coin(10)
    5
    >>> next_smaller_coin(5)
    1
    >>> next_smaller_coin(2) # Other values return None
    """
    if coin == 25:
        return 10
    elif coin == 10:
        return 5
    elif coin == 5:
        return 1

def count_coins(total):
    """Return the number of ways to make change using coins of value of 1, 5, 10, 25.
    >>> count_coins(15)
    6
    >>> count_coins(10)
    4
    >>> count_coins(20)
    9
    >>> count_coins(100) # How many ways to make change for a dollar?
    242
    >>> count_coins(200)
    1463
    >>> from construct_check import check
    >>> # ban iteration
    >>> check(HW_SOURCE_FILE, 'count_coins', ['While', 'For'])
    True
    """
    "*** YOUR CODE HERE ***"


```

```
python3 ok -q count_coins
```

:bear:

```python
# 边界情况： 硬币为0，方案正确。 硬币为负， 方案失败。
# 有四种硬币 25、10、5、1 
# 总硬币数： total
# 每次使用硬币有四种情况 total - 25, total - 10, total - 5, total - 1
# total： 10 ->
# 10
# 5 5
# 5 1 1 1 1 1 
# 1 1 1 1 1 1 1 1 1 1 
# 观察可发现， 按照25-10-5-1这样递减。
def count_coins(total):                                            
    def helper(total, coin):
        if total == 0:
            return 1
        if total < 0 or not coin :
            return 0
        return helper(total - coin,coin) + helper(total, next_smaller_coin(coin))
    return helper(total, 25)
```