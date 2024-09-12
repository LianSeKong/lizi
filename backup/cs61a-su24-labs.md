# Cs61a Summer2024 Labs



## 1.  lab01

```python
def falling(n, k):
    """Compute the falling factorial of n to depth k.

    >>> falling(6, 3)  # 6 * 5 * 4
    120
    >>> falling(4, 3)  # 4 * 3 * 2
    24
    >>> falling(4, 1)  # 4
    4
    >>> falling(4, 0)
    1
    """
    i, count = 0, 1
    while i < k:
        count *= n - i
        i += 1
    return count

def divisible_by_k(n, k):
    """
    >>> a = divisible_by_k(10, 2)  # 2, 4, 6, 8, and 10 are divisible by 2
    2
    4
    6
    8
    10
    >>> a
    5
    >>> b = divisible_by_k(3, 1)  # 1, 2, and 3 are divisible by 1
    1
    2
    3
    >>> b
    3
    >>> c = divisible_by_k(6, 7)  # There are no integers up to 6 divisible by 7
    >>> c
    0
    """
    i, count = 1, 0
    while i <= n:
        if i % k == 0:
            count += 1
            print(i)
        i += 1
    return count

def sum_digits(y):
    """Sum all the digits of y.

    >>> sum_digits(10) # 1 + 0 = 1
    1
    >>> sum_digits(4224) # 4 + 2 + 2 + 4 = 12
    12
    >>> sum_digits(1234567890)
    45
    >>> a = sum_digits(123) # make sure that you are using return rather than print
    >>> a
    6
    """
    total = 0
    while y > 0:
        total += y % 10
        y //= 10
    return total

def double_eights(n):
    """Return true if n has two eights in a row.
    >>> double_eights(8)
    False
    >>> double_eights(88)
    True
    >>> double_eights(2882)
    True
    >>> double_eights(880088)
    True
    >>> double_eights(12345)
    False
    >>> double_eights(80808080)
    False
    """
    while n > 10:
        curr = n % 10
        prev = n // 10 % 10
        if prev == curr and prev == 8:
            return True
        n //= 10
    return False
```



## lab02

```python

def composite_identity(f, g):
    """
    Return a function with one parameter x that returns True if f(g(x)) is
    equal to g(f(x)). You can assume the result of g(x) is a valid input for f
    and vice versa.

    >>> add_one = lambda x: x + 1        # adds one to x
    >>> square = lambda x: x**2          # squares x [returns x^2]
    >>> b1 = composite_identity(square, add_one)
    >>> b1(0)                            # (0 + 1) ** 2 == 0 ** 2 + 1
    True
    >>> b1(4)                            # (4 + 1) ** 2 != 4 ** 2 + 1
    False
    """
    def helper(x):
        return f(g(x)) == g(f(x))
    return helper

def sum_digits(y):
    """Return the sum of the digits of non-negative integer y."""
    total = 0
    while y > 0:
        total, y = total + y % 10, y // 10
    return total

def is_prime(n):
    """Return whether positive integer n is prime."""
    if n == 1:
        return False
    k = 2
    while k < n:
        if n % k == 0:
            return False
        k += 1
    return True

def count_cond(condition):
    """Returns a function with one parameter N that counts all the numbers from
    1 to N that satisfy the two-argument predicate function Condition, where
    the first argument for Condition is N and the second argument is the
    number from 1 to N.

    >>> count_fives = count_cond(lambda n, i: sum_digits(n * i) == 5)
    >>> count_fives(10)   # 50 (10 * 5)
    1
    >>> count_fives(50)   # 50 (50 * 1), 500 (50 * 10), 1400 (50 * 28), 2300 (50 * 46)
    4

    >>> is_i_prime = lambda n, i: is_prime(i) # need to pass 2-argument function into count_cond
    >>> count_primes = count_cond(is_i_prime)
    >>> count_primes(2)    # 2
    1
    >>> count_primes(3)    # 2, 3
    2
    >>> count_primes(4)    # 2, 3
    2
    >>> count_primes(5)    # 2, 3, 5
    3
    >>> count_primes(20)   # 2, 3, 5, 7, 11, 13, 17, 19
    8
    """
    def helper(n):
        i, count = 1, 0
        while i <= n:
            if condition(n, i):
                count += 1
            i += 1
        return count
    return helper

def multiple(a, b):
    """Return the smallest number n that is a multiple of both a and b.

    >>> multiple(3, 4)
    12
    >>> multiple(14, 21)
    42
    >>> multiple(4, 8)
    8
    >>> multiple(3, 9)
    9
    """
    i = 1
    max_factory = 1
    while i <= min(a, b) and max_factory:
        if a % i == 0 and b % i == 0:
            max_factory = i
        i += 1

    return a * b // max_factory
    



def cycle(f1, f2, f3):
    """Returns a function that is itself a higher-order function.

    >>> def add1(x):
    ...     return x + 1
    >>> def times2(x):
    ...     return x * 2
    >>> def add3(x):
    ...     return x + 3
    >>> my_cycle = cycle(add1, times2, add3)
    >>> identity = my_cycle(0)
    >>> identity(5)
    5
    >>> add_one_then_double = my_cycle(2)
    >>> add_one_then_double(1)
    4
    >>> do_all_functions = my_cycle(3)
    >>> do_all_functions(2)
    9
    >>> do_more_than_a_cycle = my_cycle(4)
    >>> do_more_than_a_cycle(2)
    10
    >>> do_two_cycles = my_cycle(6)
    >>> do_two_cycles(1)
    19
    """

    def helper(n):
        def inner_helper(x):
            if n == 0:
                return x
            i = 1
            result = x
            while i <= n:
                if i % 3 == 1:
                    result = f1(result)
                elif i % 3 == 2:
                    result = f2(result)
                else:
                    result = f3(result)
                i += 1
            return result
        return inner_helper
    return helper
```



## lab03

```python

LAB_SOURCE_FILE=__file__


def double_eights(n):
    """ Returns whether or not n has two digits in row that
    are the number 8. Assume n has at least two digits in it.

    >>> double_eights(1288)
    True
    >>> double_eights(880)
    True
    >>> double_eights(538835)
    True
    >>> double_eights(284682)
    False
    >>> double_eights(588138)
    True
    >>> double_eights(78)
    False
    >>> from construct_check import check
    >>> # ban iteration
    >>> check(LAB_SOURCE_FILE, 'double_eights', ['While', 'For'])
    True
    """
    last, second_last = n % 10, n // 10 % 10
    if last == 8 and second_last == 8:
        return True
    elif n < 100:
        return False
    return double_eights(n // 10)


def make_onion(f, g):
    """Return a function can_reach(x, y, limit) that returns
    whether some call expression containing only f, g, and x with
    up to limit calls will give the result y.

    >>> up = lambda x: x + 1
    >>> double = lambda y: y * 2
    >>> can_reach = make_onion(up, double)
    >>> can_reach(5, 25, 4)      # 25 = up(double(double(up(5))))
    True
    >>> can_reach(5, 25, 3)      # Not possible
    False
    >>> can_reach(1, 1, 0)      # 1 = 1
    True
    >>> add_ing = lambda x: x + "ing"
    >>> add_end = lambda y: y + "end"
    >>> can_reach_string = make_onion(add_ing, add_end)
    >>> can_reach_string("cry", "crying", 1)      # "crying" = add_ing("cry")
    True
    >>> can_reach_string("un", "unending", 3)     # "unending" = add_ing(add_end("un"))
    True
    >>> can_reach_string("peach", "folding", 4)   # Not possible
    False
    """
    def can_reach(x, y, limit):
        if limit < 0:
            return False
        elif x == y:
            return True
        else:
            return can_reach(f(x), y, limit - 1) or can_reach(g(x), y, limit - 1)
    return can_reach


def mario_number(level):
    """Return the number of ways that Mario can perform a sequence of steps
    or jumps to reach the end of the level without ever landing in a Piranha
    plant. Assume that every level begins and ends with a space.

    >>> mario_number(' P P ')   # jump, jump
    1
    >>> mario_number(' P P  ')   # jump, jump, step
    1
    >>> mario_number('  P P ')  # step, jump, jump
    1
    >>> mario_number('   P P ') # step, step, jump, jump or jump, jump, jump
    2
    >>> mario_number(' P PP ')  # Mario cannot jump two plants
    0
    >>> mario_number('    ')    # step, jump ; jump, step ; step, step, step
    3
    >>> mario_number('    P    ')
    9
    >>> mario_number('   P    P P   P  P P    P     P ')
    180
    """
    length = len(level)
    def helper(i):
        if i == length - 1:
            return 1
        elif i == length:
            return 0
        if level[i] == 'P':
            return 0
        return helper(i + 1) + helper(i + 2)
    return helper(0)

def max_subseq(n, t):
    """
    Return the maximum subsequence of length at most t that can be found in the given number n.
    For example, for n = 2012 and t = 2, we have that the subsequences are
        2
        0
        1
        2
        20
        21
        22
        01
        02
        12
    and of these, the maxumum number is 22, so our answer is 22.

    >>> max_subseq(2012, 2)
    22
    >>> max_subseq(20125, 3)
    225
    >>> max_subseq(20125, 5)
    20125
    >>> max_subseq(20125, 6) # note that 20125 == 020125
    20125
    >>> max_subseq(12345, 3)
    345
    >>> max_subseq(12345, 0) # 0 is of length 0
    0
    >>> max_subseq(12345, 1)
    5
    """

    if t == 0:
        return 0
    if n == 0:
        return 0
    return max(n % 10 + 10 * max_subseq(n // 10 ,t - 1), max_subseq(n // 10 ,t))
     
    
def is_prime(n):
    """
    >>> is_prime(7)
    True
    >>> is_prime(10)
    False
    >>> is_prime(1)
    False
    """
    assert n > 0, "n must be larger than 0"
    
    if n == 1:
        return False

    def helper(i):
        if i == n:
            return True
        if n % i == 0:
            return False
        return helper(i + 1)

    return helper(2)
```

## lab04



```python
LAB_SOURCE_FILE=__file__


def flatten(s):
    """Returns a flattened version of list s.

    >>> flatten([1, 2, 3])
    [1, 2, 3]
    >>> deep = [1, [[2], 3], 4, [5, 6]]
    >>> flatten(deep)
    [1, 2, 3, 4, 5, 6]
    >>> deep                                # input list is unchanged
    [1, [[2], 3], 4, [5, 6]]
    >>> very_deep = [['m', ['i', ['n', ['m', 'e', ['w', 't', ['a'], 't', 'i', 'o'], 'n']], 's']]]
    >>> flatten(very_deep)
    ['m', 'i', 'n', 'm', 'e', 'w', 't', 'a', 't', 'i', 'o', 'n', 's']
    """
    if type(s) != list:
      return [s]
    lst = []
    for i in s:
      lst += flatten(i)
    return lst


def merge(s, t):
    """Merges two sorted lists.

    >>> s1 = [1, 3, 5]
    >>> s2 = [2, 4, 6]
    >>> merge(s1, s2)
    [1, 2, 3, 4, 5, 6]
    >>> s1
    [1, 3, 5]
    >>> s2
    [2, 4, 6]
    >>> merge([], [2, 4, 6])
    [2, 4, 6]
    >>> merge([1, 2, 3], [])
    [1, 2, 3]
    >>> merge([5, 7], [2, 4, 6])
    [2, 4, 5, 6, 7]
    >>> merge([2, 3, 4], [2, 4, 6])
    [2, 2, 3, 4, 4, 6]
    >>> from construct_check import check
    >>> check(LAB_SOURCE_FILE, 'merge', ['While', 'For'])    # ban iteration
    True
    """
    if len(s) == 0:
      return t[:]
    elif len(t) == 0:
      return s[:]
    val = min(s[0], t[0])
    if (s[0] == val):
      return [val] + merge(s[1:], t)
    else:
      return [val] + merge(s, t[1:])

        



def size_of_tree(t):
    """Return the number of entries in the tree.
    >>> numbers = tree(1, [tree(2), tree(3, [tree(4), tree(5)]), tree(6, [tree(7)])])
    >>> print_tree(numbers)
    1
      2
      3
        4
        5
      6
        7
    >>> size_of_tree(numbers)
    7
    """
    if is_leaf(t):
      return 1
    return 1 + sum([size_of_tree(b) for b in branches(t)])


def replace_loki_at_leaf(t, lokis_replacement):
    """Returns a new tree where every leaf value equal to "loki" has
    been replaced with lokis_replacement.

    >>> yggdrasil = tree('odin',
    ...                  [tree('balder',
    ...                        [tree('loki'),
    ...                         tree('freya')]),
    ...                   tree('frigg',
    ...                        [tree('loki')]),
    ...                   tree('loki',
    ...                        [tree('sif'),
    ...                         tree('loki')]),
    ...                   tree('loki')])
    >>> laerad = copy_tree(yggdrasil)    # copy yggdrasil for testing purposes
    >>> print_tree(replace_loki_at_leaf(yggdrasil, 'freya'))
    odin
      balder
        freya
        freya
      frigg
        freya
      loki
        sif
        freya
      freya
    >>> laerad == yggdrasil    # Make sure original tree is unmodified
    True
    """
    if is_leaf(t):
        if label(t) == 'loki':
            return tree(lokis_replacement)
        return tree(label(t))
    return tree(label(t), [replace_loki_at_leaf(b, lokis_replacement) for b in branches(t)])



def divide(quotients, divisors):
    """Return a dictonary in which each quotient q is a key for the list of
    divisors that it divides evenly.

    >>> divide([3, 4, 5], [8, 9, 10, 11, 12])
    {3: [9, 12], 4: [8, 12], 5: [10]}
    >>> divide(range(1, 5), range(20, 25))
    {1: [20, 21, 22, 23, 24], 2: [20, 22, 24], 3: [21, 24], 4: [20, 24]}
    """
    return {i: [j for j in divisors if j % i == 0] for i in quotients}




# Tree Data Abstraction

def tree(label, branches=[]):
    """Construct a tree with the given label value and a list of branches."""
    for branch in branches:
        assert is_tree(branch), 'branches must be trees'
    return [label] + list(branches)

def label(tree):
    """Return the label value of a tree."""
    return tree[0]

def branches(tree):
    """Return the list of branches of the given tree."""
    return tree[1:]

def is_tree(tree):
    """Returns True if the given tree is a tree, and False otherwise."""
    if type(tree) != list or len(tree) < 1:
        return False
    for branch in branches(tree):
        if not is_tree(branch):
            return False
    return True

def is_leaf(tree):
    """Returns True if the given tree's list of branches is empty, and False
    otherwise.
    """
    return not branches(tree)

def print_tree(t, indent=0):
    """Print a representation of this tree in which each node is
    indented by two spaces times its depth from the root.

    >>> print_tree(tree(1))
    1
    >>> print_tree(tree(1, [tree(2)]))
    1
      2
    >>> numbers = tree(1, [tree(2), tree(3, [tree(4), tree(5)]), tree(6, [tree(7)])])
    >>> print_tree(numbers)
    1
      2
      3
        4
        5
      6
        7
    """
    print('  ' * indent + str(label(t)))
    for b in branches(t):
        print_tree(b, indent + 1)

def copy_tree(t):
    """Returns a copy of t. Only for testing purposes.

    >>> t = tree(5)
    >>> copy = copy_tree(t)
    >>> t = tree(6)
    >>> print_tree(copy)
    5
    """
    return tree(label(t), [copy_tree(b) for b in branches(t)])
```





## lab05

```python
def partial_reverse(s, start):
    """Reverse part of a list in-place, starting with start up to the end of
    the list.

    >>> a = [1, 2, 3, 4, 5, 6, 7]
    >>> partial_reverse(a, 2)
    >>> a
    [1, 2, 7, 6, 5, 4, 3]
    >>> partial_reverse(a, 5)
    >>> a
    [1, 2, 7, 6, 5, 3, 4]
    """
    def helper(i):
        if i < (len(s) - start) // 2:
            s[start + i], s[len(s) - i - 1] = s[len(s) - i - 1], s[start + i]
            helper(i + 1)

    return helper(0)




def group_by(s, fn):
    """Return a dictionary of lists that together contain the elements of s.
    The key for each list is the value that fn returns when called on any of the
    values of that list.

    >>> group_by([12, 23, 14, 45], lambda p: p // 10)
    {1: [12, 14], 2: [23], 4: [45]}
    >>> group_by(range(-3, 4), lambda x: x * x)
    {9: [-3, 3], 4: [-2, 2], 1: [-1, 1], 0: [0]}
    """
    grouped = {}
    for i in s:
        key = fn(i)
        if key in grouped:
            grouped[key] += [i]
        else:
            grouped[key] = [i]
    return grouped


from math import sqrt

def distance(city_a, city_b):
    """
    >>> city_a = make_city('city_a', 0, 1)
    >>> city_b = make_city('city_b', 0, 2)
    >>> distance(city_a, city_b)
    1.0
    >>> city_c = make_city('city_c', 6.5, 12)
    >>> city_d = make_city('city_d', 2.5, 15)
    >>> distance(city_c, city_d)
    5.0
    """
    return sqrt((get_lat(city_a) - get_lat(city_b)) ** 2 + (get_lon(city_a) - get_lon(city_b)) ** 2)

def closer_city(lat, lon, city_a, city_b):
    """
    Returns the name of either city_a or city_b, whichever is closest to
    coordinate (lat, lon). If the two cities are the same distance away
    from the coordinate, consider city_b to be the closer city.

    >>> berkeley = make_city('Berkeley', 37.87, 112.26)
    >>> stanford = make_city('Stanford', 34.05, 118.25)
    >>> closer_city(38.33, 121.44, berkeley, stanford)
    'Stanford'
    >>> bucharest = make_city('Bucharest', 44.43, 26.10)
    >>> vienna = make_city('Vienna', 48.20, 16.37)
    >>> closer_city(41.29, 174.78, bucharest, vienna)
    'Bucharest'
    """
    city_c =  make_city('city_c', lat, lon)
    distance_1 = distance(city_a, city_c)
    distance_2 = distance(city_b, city_c)

    if distance_1 < distance_2:
        return get_name(city_a)    
    return get_name(city_b)    


def check_city_abstraction():
    """
    There's nothing for you to do for this function, it's just here for the extra doctest
    >>> change_abstraction(True)
    >>> city_a = make_city('city_a', 0, 1)
    >>> city_b = make_city('city_b', 0, 2)
    >>> distance(city_a, city_b)
    1.0
    >>> city_c = make_city('city_c', 6.5, 12)
    >>> city_d = make_city('city_d', 2.5, 15)
    >>> distance(city_c, city_d)
    5.0
    >>> berkeley = make_city('Berkeley', 37.87, 112.26)
    >>> stanford = make_city('Stanford', 34.05, 118.25)
    >>> closer_city(38.33, 121.44, berkeley, stanford)
    'Stanford'
    >>> bucharest = make_city('Bucharest', 44.43, 26.10)
    >>> vienna = make_city('Vienna', 48.20, 16.37)
    >>> closer_city(41.29, 174.78, bucharest, vienna)
    'Bucharest'
    >>> change_abstraction(False)
    """

# Treat all the following code as being behind an abstraction layer,
# you shouldn't need to look at it.
def make_city(name, lat, lon):
    """
    >>> city = make_city('Berkeley', 0, 1)
    >>> get_name(city)
    'Berkeley'
    >>> get_lat(city)
    0
    >>> get_lon(city)
    1
    """
    if change_abstraction.changed:
        return {"name" : name, "lat" : lat, "lon" : lon}
    else:
        return [name, lat, lon]

def get_name(city):
    """
    >>> city = make_city('Berkeley', 0, 1)
    >>> get_name(city)
    'Berkeley'
    """
    if change_abstraction.changed:
        return city["name"]
    else:
        return city[0]

def get_lat(city):
    """
    >>> city = make_city('Berkeley', 0, 1)
    >>> get_lat(city)
    0
    """
    if change_abstraction.changed:
        return city["lat"]
    else:
        return city[1]

def get_lon(city):
    """
    >>> city = make_city('Berkeley', 0, 1)
    >>> get_lon(city)
    1
    """
    if change_abstraction.changed:
        return city["lon"]
    else:
        return city[2]

###############


def change_abstraction(change):
    """
    For testing purposes.
    >>> change_abstraction(True)
    >>> change_abstraction.changed
    True
    """
    change_abstraction.changed = change

change_abstraction.changed = False
```



## lab06



```python
def count_occurrences(t, n, x):
    """Return the number of times that x is equal to one of the
    first n elements of iterator t.

    >>> s = iter([10, 9, 10, 9, 9, 10, 8, 8, 8, 7])
    >>> count_occurrences(s, 10, 9)
    3
    >>> t = iter([10, 9, 10, 9, 9, 10, 8, 8, 8, 7])
    >>> count_occurrences(t, 3, 10)
    2
    >>> u = iter([3, 2, 2, 2, 1, 2, 1, 4, 4, 5, 5, 5])
    >>> count_occurrences(u, 1, 3)  # Only iterate over 3
    1
    >>> count_occurrences(u, 3, 2)  # Only iterate over 2, 2, 2
    3
    >>> list(u)                     # Ensure that the iterator has advanced the right amount
    [1, 2, 1, 4, 4, 5, 5, 5]
    >>> v = iter([4, 1, 6, 6, 7, 7, 6, 6, 2, 2, 2, 5])
    >>> count_occurrences(v, 6, 6)
    2
    """
    total = 0
    for i in range(n):
        y = next(t)
        if x == y:
            total += 1
    return total



def hailstone(n):
    """Yields the elements of the hailstone sequence starting at n.

    >>> for num in hailstone(10):
    ...     print(num)
    10
    5
    16
    8
    4
    2
    1
    """
    yield n
    if n == 1:
        return None
    if n % 2 == 0:
        yield from hailstone(n // 2)
    else:
        yield from hailstone(n * 3 + 1)


def merge(incr_a, incr_b):
    """Yield the elements of strictly increasing iterables incr_a and incr_b, removing
    repeats. Assume that incr_a and incr_b have no repeats. incr_a or incr_b may or may not
    be infinite sequences.

    >>> m = merge([0, 2, 4, 6, 8, 10, 12, 14], [0, 3, 6, 9, 12, 15])
    >>> type(m)
    <class 'generator'>
    >>> list(m)
    [0, 2, 3, 4, 6, 8, 9, 10, 12, 14, 15]
    >>> def big(n):
    ...    k = 0
    ...    while True: yield k; k += n
    >>> m = merge(big(2), big(3))
    >>> [next(m) for _ in range(11)]
    [0, 2, 3, 4, 6, 8, 9, 10, 12, 14, 15]
    """
    iter_a, iter_b = iter(incr_a), iter(incr_b)
    next_a, next_b = next(iter_a, None), next(iter_b, None)

    if next_a == None and next_b == None:
        yield None
    elif next_b == None:
        yield next_a
        yield from iter_a
    elif next_a == None:
        yield next_b
        yield from iter_b
    else:
        if next_a == next_b:
            yield next_a
            yield from merge(iter_a, iter_b)
        elif next_a < next_b:
            yield next_a
            yield from merge(iter_a, merge([next_b], iter_b))
        else:
            yield next_b
            yield from merge(merge([next_a], iter_a), iter_b)

def deep_map(f, s):
    """Replace all non-list elements x with f(x) in the nested list s.

    >>> six = [1, 2, [3, [4], 5], 6]
    >>> deep_map(lambda x: x * x, six)
    >>> six
    [1, 4, [9, [16], 25], 36]
    >>> # Check that you're not making new lists
    >>> s = [3, [1, [4, [1]]]]
    >>> s1 = s[1]
    >>> s2 = s1[1]
    >>> s3 = s2[1]
    >>> deep_map(lambda x: x + 1, s)
    >>> s
    [4, [2, [5, [2]]]]
    >>> s1 is s[1]
    True
    >>> s2 is s1[1]
    True
    >>> s3 is s2[1]
    True
    """
    for i in range(len(s)):
        if isinstance(s[i], list):
            deep_map(f, s[i])
        else:
            s[i] = f(s[i])



def buy(required_fruits, prices, total_amount):
    """Print ways to buy some of each fruit so that the sum of prices is amount.

    >>> prices = {'oranges': 4, 'apples': 3, 'bananas': 2, 'kiwis': 9}
    >>> buy(['apples', 'oranges', 'bananas'], prices, 12)
    [2 apples][1 orange][1 banana]
    >>> buy(['apples', 'oranges', 'bananas'], prices, 16)
    [2 apples][1 orange][3 bananas]
    [2 apples][2 oranges][1 banana]
    >>> buy(['apples', 'kiwis'], prices, 36)
    [3 apples][3 kiwis]
    [6 apples][2 kiwis]
    [9 apples][1 kiwi]
    """
    def add(fruits, amount, cart):
        if fruits == [] and amount == 0:
            print(cart)
        elif fruits and amount > 0:
            fruit = fruits[0]
            price = prices[fruit]
            for k in range(1, amount // price + 1):
                add(fruits[1:], amount - k * price, cart + display(fruit, k))
    add(required_fruits, total_amount, '')


def display(fruit, count):
    """Display a count of a fruit in square brackets.

    >>> display('apples', 3)
    '[3 apples]'
    >>> display('apples', 1)
    '[1 apple]'
    """
    assert count >= 1 and fruit[-1] == 's'
    if count == 1:
        fruit = fruit[:-1]  # get rid of the plural s
    return '[' + str(count) + ' ' + fruit + ']'

```



## lab07



```python
passphrase = 'REPLACE_THIS_WITH_PASSPHRASE'

def midsem_survey(p):
    """
    You do not need to understand this code.
    >>> midsem_survey(passphrase)
    'c20d4e5854c4c9cdfd14626aad39bd5a1e2ed9bb30dca56d5643a3ad'
    """
    import hashlib
    return hashlib.sha224(p.encode('utf-8')).hexdigest()
    

class Account:
    """An account has a balance and a holder.
    >>> a = Account('John')
    >>> a.deposit(10)
    10
    >>> a.balance
    10
    >>> a.interest
    0.02
    >>> a.time_to_retire(10.25)  # 10 -> 10.2 -> 10.404
    2
    >>> a.balance                # Calling time_to_retire method should not change the balance
    10
    >>> a.time_to_retire(11)     # 10 -> 10.2 -> ... -> 11.040808032
    5
    >>> a.time_to_retire(100)
    117
    """
    max_withdrawal = 10
    interest = 0.02

    def __init__(self, account_holder):
        self.balance = 0
        self.holder = account_holder

    def deposit(self, amount):
        self.balance = self.balance + amount
        return self.balance

    def withdraw(self, amount):
        if amount > self.balance:
            return "Insufficient funds"
        if amount > self.max_withdrawal:
            return "Can't withdraw that amount"
        self.balance = self.balance - amount
        return self.balance

    def time_to_retire(self, amount):
        """Return the number of years until balance would grow to amount."""
        assert self.balance > 0 and amount > 0 and self.interest > 0
        "*** YOUR CODE HERE ***"


class FreeChecking(Account):
    """A bank account that charges for withdrawals, but the first two are free!
    >>> ch = FreeChecking('Jack')
    >>> ch.balance = 20
    >>> ch.withdraw(100)  # First one's free. Still counts as a free withdrawal even though it was unsuccessful
    'Insufficient funds'
    >>> ch.withdraw(3)    # Second withdrawal is also free
    17
    >>> ch.balance
    17
    >>> ch.withdraw(3)    # Ok, two free withdrawals is enough, as free_withdrawals is only 2
    13
    >>> ch.withdraw(3)
    9
    >>> ch2 = FreeChecking('John')
    >>> ch2.balance = 10
    >>> ch2.withdraw(3) # No fee
    7
    >>> ch.withdraw(3)  # ch still charges a fee
    5
    >>> ch.withdraw(5)  # Not enough to cover fee + withdraw
    'Insufficient funds'
    """
    withdraw_fee = 1
    free_withdrawals = 2

    "*** YOUR CODE HERE ***"


class Transaction:
    def __init__(self, id, before, after):
        self.id = id
        self.before = before
        self.after = after

    def changed(self):
        """Return whether the transaction resulted in a changed balance."""
        "*** YOUR CODE HERE ***"

    def report(self):
        """Return a string describing the transaction.

        >>> Transaction(3, 20, 10).report()
        '3: decreased 20->10'
        >>> Transaction(4, 20, 50).report()
        '4: increased 20->50'
        >>> Transaction(5, 50, 50).report()
        '5: no change'
        """
        msg = 'no change'
        if self.changed():
            "*** YOUR CODE HERE ***"
        return str(self.id) + ': ' + msg

class BankAccount:
    """A bank account that tracks its transaction history.

    >>> a = BankAccount('Eric')
    >>> a.deposit(100)    # Transaction 0 for a
    100
    >>> b = BankAccount('Erica')
    >>> a.withdraw(30)    # Transaction 1 for a
    70
    >>> a.deposit(10)     # Transaction 2 for a
    80
    >>> b.deposit(50)     # Transaction 0 for b
    50
    >>> b.withdraw(10)    # Transaction 1 for b
    40
    >>> a.withdraw(100)   # Transaction 3 for a
    'Insufficient funds'
    >>> len(a.transactions)
    4
    >>> len([t for t in a.transactions if t.changed()])
    3
    >>> for t in a.transactions:
    ...     print(t.report())
    0: increased 0->100
    1: decreased 100->70
    2: increased 70->80
    3: no change
    >>> b.withdraw(100)   # Transaction 2 for b
    'Insufficient funds'
    >>> b.withdraw(30)    # Transaction 3 for b
    10
    >>> for t in b.transactions:
    ...     print(t.report())
    0: increased 0->50
    1: decreased 50->40
    2: no change
    3: decreased 40->10
    """

    # *** YOU NEED TO MAKE CHANGES IN SEVERAL PLACES IN THIS CLASS ***

    def __init__(self, account_holder):
        self.balance = 0
        self.holder = account_holder

    def deposit(self, amount):
        """Increase the account balance by amount, add the deposit
        to the transaction history, and return the new balance.
        """
        self.balance = self.balance + amount
        return self.balance

    def withdraw(self, amount):
        """Decrease the account balance by amount, add the withdraw
        to the transaction history, and return the new balance.
        """
        if amount > self.balance:
            return 'Insufficient funds'
        self.balance = self.balance - amount
        return self.balance




class Email:
    """An email has the following instance attributes:

        msg (str): the contents of the message
        sender (Client): the client that sent the email
        recipient_name (str): the name of the recipient (another client)
    """
    def __init__(self, msg, sender, recipient_name):
        self.msg = msg
        self.sender = sender
        self.recipient_name = recipient_name

class Server:
    """Each Server has one instance attribute called clients that is a
    dictionary from client names to client objects.
    """
    def __init__(self):
        self.clients = {}

    def send(self, email):
        """Append the email to the inbox of the client it is addressed to.
            email is an instance of the Email class.
        """
        ____.inbox.append(email)

    def register_client(self, client):
        """Add a client to the clients mapping (which is a 
        dictionary from client names to client instances).
            client is an instance of the Client class.
        """
        ____[____] = ____

class Client:
    """A client has a server, a name (str), and an inbox (list).

    >>> s = Server()
    >>> a = Client(s, 'Alice')
    >>> b = Client(s, 'Bob')
    >>> a.compose('Hello, World!', 'Bob')
    >>> b.inbox[0].msg
    'Hello, World!'
    >>> a.compose('CS 61A Rocks!', 'Bob')
    >>> len(b.inbox)
    2
    >>> b.inbox[1].msg
    'CS 61A Rocks!'
    >>> b.inbox[1].sender.name
    'Alice'
    """
    def __init__(self, server, name):
        self.inbox = []
        self.server = server
        self.name = name
        server.register_client(____)

    def compose(self, message, recipient_name):
        """Send an email with the given message to the recipient."""
        email = Email(message, ____, ____)
        self.server.send(email)


def make_change(amount, coins):
    """Return a list of coins that sum to amount, preferring the smallest coins
    available and placing the smallest coins first in the returned list.

    The coins argument is a dictionary with keys that are positive integer
    denominations and values that are positive integer coin counts.

    >>> make_change(2, {2: 1})
    [2]
    >>> make_change(2, {1: 2, 2: 1})
    [1, 1]
    >>> make_change(4, {1: 2, 2: 1})
    [1, 1, 2]
    >>> make_change(4, {2: 1}) == None
    True

    >>> coins = {2: 2, 3: 2, 4: 3, 5: 1}
    >>> make_change(4, coins)
    [2, 2]
    >>> make_change(8, coins)
    [2, 2, 4]
    >>> make_change(25, coins)
    [2, 3, 3, 4, 4, 4, 5]
    >>> coins[8] = 1
    >>> make_change(25, coins)
    [2, 2, 4, 4, 5, 8]
    """
    if not coins:
        return None
    smallest = min(coins)
    rest = remove_one(coins, smallest)
    if amount < smallest:
        return None
    "*** YOUR CODE HERE ***"

def remove_one(coins, coin):
    """Remove one coin from a dictionary of coins. Return a new dictionary,
    leaving the original dictionary coins unchanged.

    >>> coins = {2: 5, 3: 2, 6: 1}
    >>> remove_one(coins, 2) == {2: 4, 3: 2, 6: 1}
    True
    >>> remove_one(coins, 6) == {2: 5, 3: 2}
    True
    >>> coins == {2: 5, 3: 2, 6: 1} # Unchanged
    True
    """
    copy = dict(coins)
    count = copy.pop(coin) - 1  # The coin denomination is removed
    if count:
        copy[coin] = count      # The coin denomination is added back
    return copy

class ChangeMachine:
    """A change machine holds a certain number of coins, initially all pennies.
    The change method adds a single coin of some denomination X and returns a
    list of coins that sums to X. The machine prefers to return the smallest
    coins available. The total value in the machine never changes, and it can
    always make change for any coin (perhaps by returning the coin passed in).

    The coins attribute is a dictionary with keys that are positive integer
    denominations and values that are positive integer coin counts.

    >>> m = ChangeMachine(2)
    >>> m.coins == {1: 2}
    True
    >>> m.change(2)
    [1, 1]
    >>> m.coins == {2: 1}
    True
    >>> m.change(2)
    [2]
    >>> m.coins == {2: 1}
    True
    >>> m.change(3)
    [3]
    >>> m.coins == {2: 1}
    True

    >>> m = ChangeMachine(10) # 10 pennies
    >>> m.coins == {1: 10}
    True
    >>> m.change(5) # takes a nickel & returns 5 pennies
    [1, 1, 1, 1, 1]
    >>> m.coins == {1: 5, 5: 1} # 5 pennies & a nickel remain
    True
    >>> m.change(3)
    [1, 1, 1]
    >>> m.coins == {1: 2, 3: 1, 5: 1}
    True
    >>> m.change(2)
    [1, 1]
    >>> m.change(2) # not enough 1's remaining; return a 2
    [2]
    >>> m.coins == {2: 1, 3: 1, 5: 1}
    True
    >>> m.change(8) # cannot use the 2 to make 8, so use 3 & 5
    [3, 5]
    >>> m.coins == {2: 1, 8: 1}
    True
    >>> m.change(1) # return the penny passed in (it's the smallest)
    [1]
    >>> m.change(9) # return the 9 passed in (no change possible)
    [9]
    >>> m.coins == {2: 1, 8: 1}
    True
    >>> m.change(10)
    [2, 8]
    >>> m.coins == {10: 1}
    True

    >>> m = ChangeMachine(9)
    >>> [m.change(k) for k in [2, 2, 3]]
    [[1, 1], [1, 1], [1, 1, 1]]
    >>> m.coins == {1: 2, 2: 2, 3: 1}
    True
    >>> m.change(5) # Prefers [1, 1, 3] to [1, 2, 2] (more pennies)
    [1, 1, 3]
    >>> m.change(7)
    [2, 5]
    >>> m.coins == {2: 1, 7: 1}
    True
    """
    def __init__(self, pennies):
        self.coins = {1: pennies}

    def change(self, coin):
        """Return change for coin, removing the result from self.coins."""
        "*** YOUR CODE HERE ***"
```



## lab08



```python

def cumulative_mul(t):
    """Mutates t so that each node's label becomes the product of its own
    label and all labels in the corresponding subtree rooted at t.

    >>> t = Tree(1, [Tree(3, [Tree(5)]), Tree(7)])
    >>> cumulative_mul(t)
    >>> t
    Tree(105, [Tree(15, [Tree(5)]), Tree(7)])
    >>> otherTree = Tree(2, [Tree(1, [Tree(3), Tree(4), Tree(5)]), Tree(6, [Tree(7)])])
    >>> cumulative_mul(otherTree)
    >>> otherTree
    Tree(5040, [Tree(60, [Tree(3), Tree(4), Tree(5)]), Tree(42, [Tree(7)])])
    """

    for b in t.branches:
        cumulative_mul(b)
        t.label *= b.label

def delete(t, x):
    """Remove all nodes labeled x below the root within Tree t. When a non-leaf
    node is deleted, the deleted node's children become children of its parent.

    The root node will never be removed.

    >>> t = Tree(3, [Tree(2, [Tree(2), Tree(2)]), Tree(2), Tree(2, [Tree(2, [Tree(2), Tree(2)])])])
    >>> delete(t, 2)
    >>> t
    Tree(3)
    >>> t = Tree(1, [Tree(2, [Tree(4, [Tree(2)]), Tree(5)]), Tree(3, [Tree(6), Tree(2)]), Tree(4)])
    >>> delete(t, 2)
    >>> t
    Tree(1, [Tree(4), Tree(5), Tree(3, [Tree(6)]), Tree(4)])
    >>> t = Tree(1, [Tree(2, [Tree(4), Tree(5)]), Tree(3, [Tree(6), Tree(2)]), Tree(2, [Tree(6),  Tree(2), Tree(7), Tree(8)]), Tree(4)])
    >>> delete(t, 2)
    >>> t
    Tree(1, [Tree(4), Tree(5), Tree(3, [Tree(6)]), Tree(6), Tree(7), Tree(8), Tree(4)])
    """
    new_branches = []
    for b in t.branches:
        delete(b, x)
        if b.label == x:
            new_branches.extend(b.branches)
        else:
            new_branches.append(b)
    t.branches = new_branches


def convert_link(link):
    """Takes a linked list and returns a Python list with the same elements.

    >>> link = Link(1, Link(2, Link(3, Link(4))))
    >>> lst = convert_link(link)
    >>> lst
    [1, 2, 3, 4]
    >>> convert_link(Link.empty)
    []
    """
    if link is Link.empty:
        return []
    return [link.first] + convert_link(link.rest)



def add_links(link1, link2):
    """Adds two Links, returning a new Link

    >>> l1 = Link(1, Link(2))
    >>> l2 = Link(3, Link(4, Link(5)))
    >>> new = add_links(l1, l2)
    >>> print(new)
    <1 2 3 4 5>
    >>> new2 = add_links(l2,l1)
    >>> print(new2)
    <3 4 5 1 2>
    """
    if link1 is Link.empty:
        return link2
    return Link(link1.first, add_links(link1.rest, link2))



def multiply_lnks(lst_of_lnks):
    """
    >>> a = Link(2, Link(3))
    >>> b = Link(5, Link(4))
    >>> p1 = multiply_lnks([a, b])
    >>> p1
    Link(10, Link(12))

    >>> c = Link(2, Link(3, Link(5)))
    >>> d = Link(6, Link(4, Link(2)))
    >>> e = Link(4, Link(1, Link(0, Link(2))))
    >>> p2 = multiply_lnks([c, d, e])
    >>> p2
    Link(48, Link(12, Link(0)))
    """
    product = 1
    for lst in lst_of_lnks:
        if lst is Link.empty:
            return Link.empty
        product *= lst.first
    lst_of_lnks_rests = [lst.rest for lst in lst_of_lnks]
    return Link(product, multiply_lnks(lst_of_lnks_rests))


class Tree:
    """
    >>> t = Tree(3, [Tree(2, [Tree(5)]), Tree(4)])
    >>> t.label
    3
    >>> t.branches[0].label
    2
    >>> t.branches[1].is_leaf()
    True
    """
    def __init__(self, label, branches=[]):
        for b in branches:
            assert isinstance(b, Tree)
        self.label = label
        self.branches = list(branches)

    def is_leaf(self):
        return not self.branches

    def __repr__(self):
        if self.branches:
            branch_str = ', ' + repr(self.branches)
        else:
            branch_str = ''
        return 'Tree({0}{1})'.format(self.label, branch_str)

    def __str__(self):
        def print_tree(t, indent=0):
            tree_str = '  ' * indent + str(t.label) + "\n"
            for b in t.branches:
                tree_str += print_tree(b, indent + 1)
            return tree_str
        return print_tree(self).rstrip()


class Link:
    """A linked list.

    >>> s = Link(1)
    >>> s.first
    1
    >>> s.rest is Link.empty
    True
    >>> s = Link(2, Link(3, Link(4)))
    >>> s.first = 5
    >>> s.rest.first = 6
    >>> s.rest.rest = Link.empty
    >>> s                                    # Displays the contents of repr(s)
    Link(5, Link(6))
    >>> s.rest = Link(7, Link(Link(8, Link(9))))
    >>> s
    Link(5, Link(7, Link(Link(8, Link(9)))))
    >>> print(s)                             # Prints str(s)
    <5 7 <8 9>>
    """
    empty = ()

    def __init__(self, first, rest=empty):
        assert rest is Link.empty or isinstance(rest, Link)
        self.first = first
        self.rest = rest

    def __repr__(self):
        if self.rest is not Link.empty:
            rest_repr = ', ' + repr(self.rest)
        else:
            rest_repr = ''
        return 'Link(' + repr(self.first) + rest_repr + ')'

    def __str__(self):
        string = '<'
        while self.rest is not Link.empty:
            string += str(self.first) + ' '
            self = self.rest
        return string + str(self.first) + '>'


```



## lab09



```scheme
(define (over-or-under num1 num2) 
      (cond ((= num1 num2) 0)
            ((< num1 num2) -1)
            ((> num1 num2) 1)
      )
)
(define (make-adder num) (lambda (incr) (+ num incr)))

(define (composed f g) (lambda (x) (f (g x))))

(define (repeat f n) (lambda (x) (if (= n 0) x ((repeat f (- n 1)) (f x)))))

(define (max a b)
  (if (> a b)
      a
      b))

(define (min a b)
  (if (> a b)
      b
      a))

(define (gcd a b) (if (= 0 (modulo (max a b) (min a b))) (min a b) (gcd (min a b) (modulo (max a b) (min a b)))))

(define (duplicate lst) 
  (if (null? lst)
    lst
    (cons (car lst)
          (cons (car lst) 
                (duplicate (cdr lst))
          )
    )
  )
)

(expect (duplicate '(1 2 3)) (1 1 2 2 3 3))

(expect (duplicate '(1 1)) (1 1 1 1))

(define (deep-map fn s) 
    (cond ((null? s) s) 
          ((list? (car s)) (cons (deep-map fn (car s)) (deep-map fn (cdr s)))) 
          (else (cons (fn (car s)) (deep-map fn (cdr s)))) 
))


```



## lab10

```python
############## You do not need to understand any of this code!
import base64
ob = "CmRlZiBhZGRpdGlvbihleHByKToKICAgIGRpdmlkZW5kID0gZXhwci5maXJzdAogICAgZXhwciA9IGV4cHIucmVzdAogICAgd2hpbGUgZXhwciAhPSBuaWw6CiAgICAgICAgZGl2aXNvciA9IGV4cHIuZmlyc3QKICAgICAgICBkaXZpZGVuZCArPSBkaXZpc29yCiAgICAgICAgZXhwciA9IGV4cHIucmVzdAogICAgcmV0dXJuIGRpdmlkZW5kCgpkZWYgc3VidHJhY3Rpb24oZXhwcik6CiAgICBkaXZpZGVuZCA9IGV4cHIuZmlyc3QKICAgIGV4cHIgPSBleHByLnJlc3QKICAgIHdoaWxlIGV4cHIgIT0gbmlsOgogICAgICAgIGRpdmlzb3IgPSBleHByLmZpcnN0CiAgICAgICAgZGl2aWRlbmQgLT0gZGl2aXNvcgogICAgICAgIGV4cHIgPSBleHByLnJlc3QKICAgIHJldHVybiBkaXZpZGVuZAoKZGVmIG11bHRpcGxpY2F0aW9uKGV4cHIpOgogICAgZGl2aWRlbmQgPSBleHByLmZpcnN0CiAgICBleHByID0gZXhwci5yZXN0CiAgICB3aGlsZSBleHByICE9IG5pbDoKICAgICAgICBkaXZpc29yID0gZXhwci5maXJzdAogICAgICAgIGRpdmlkZW5kICo9IGRpdmlzb3IKICAgICAgICBleHByID0gZXhwci5yZXN0CiAgICByZXR1cm4gZGl2aWRlbmQKCmRlZiBkaXZpc2lvbihleHByKToKICAgIGRpdmlkZW5kID0gZXhwci5maXJzdAogICAgZXhwciA9IGV4cHIucmVzdAogICAgd2hpbGUgZXhwciAhPSBuaWw6CiAgICAgICAgZGl2aXNvciA9IGV4cHIuZmlyc3QKICAgICAgICBkaXZpZGVuZCAvPSBkaXZpc29yCiAgICAgICAgZXhwciA9IGV4cHIucmVzdAogICAgcmV0dXJuIGRpdmlkZW5kCg=="
exec(base64.b64decode(ob.encode("ascii")).decode("ascii"))
##############

def calc_eval(exp):
    """
    >>> calc_eval(Pair("define", Pair("a", Pair(1, nil))))
    'a'
    >>> calc_eval("a")
    1
    >>> calc_eval(Pair("+", Pair(1, Pair(2, nil))))
    3
    """
    if isinstance(exp, Pair):
        operator = exp.first # UPDATE THIS FOR Q2, e.g (+ 1 2), + is the operator
        operands = exp.rest # UPDATE THIS FOR Q2, e.g (+ 1 2), 1 and 2 are operands
        if operator == 'and': # and expressions
            return eval_and(operands)
        elif operator == 'define': # define expressions
            return eval_define(operands)
        else: # Call expressions
            return calc_apply(calc_eval(operator), operands.map(calc_eval)) # UPDATE THIS FOR Q2, what is type(operator)?
    elif exp in OPERATORS:   # Looking up procedures
        return OPERATORS[exp]
    elif isinstance(exp, int) or isinstance(exp, bool):   # Numbers and booleans
        return exp
    elif bindings.get(exp, False): # CHANGE THIS CONDITION FOR Q4 where are variables stored?
        return bindings[exp] # UPDATE THIS FOR Q4, how do you access a variable?

def calc_apply(op, args):
    return op(args)

def floor_div(args):
    """
    >>> floor_div(Pair(100, Pair(10, nil)))
    10
    >>> floor_div(Pair(5, Pair(3, nil)))
    1
    >>> floor_div(Pair(1, Pair(1, nil)))
    1
    >>> floor_div(Pair(5, Pair(2, nil)))
    2
    >>> floor_div(Pair(23, Pair(2, Pair(5, nil))))
    2
    >>> calc_eval(Pair("//", Pair(4, Pair(2, nil))))
    2
    >>> calc_eval(Pair("//", Pair(100, Pair(2, Pair(2, Pair(2, Pair(2, Pair(2, nil))))))))
    3
    >>> calc_eval(Pair("//", Pair(100, Pair(Pair("+", Pair(2, Pair(3, nil))), nil))))
    20
    """

    result = args.first
    divisors = args.rest
    while divisors != nil:
        divisor = divisors.first
        result //= divisor
        divisors = divisors.rest
    return result


scheme_t = True   # Scheme's #t
scheme_f = False  # Scheme's #f

def eval_and(expressions):
    """
    >>> calc_eval(Pair("and", Pair(1, nil)))
    1
    >>> calc_eval(Pair("and", Pair(False, Pair("1", nil))))
    False
    >>> calc_eval(Pair("and", Pair(1, Pair(Pair("//", Pair(5, Pair(2, nil))), nil))))
    2
    >>> calc_eval(Pair("and", Pair(Pair('+', Pair(1, Pair(1, nil))), Pair(3, nil))))
    3
    >>> calc_eval(Pair("and", Pair(Pair('-', Pair(1, Pair(0, nil))), Pair(Pair('/', Pair(5, Pair(2, nil))), nil))))
    2.5
    >>> calc_eval(Pair("and", Pair(0, Pair(1, nil))))
    1
    >>> calc_eval(Pair("and", nil))
    True
    """

    val = True
    while expressions is not nil:
        val = calc_eval(expressions.first)
        if val is scheme_f:
            return scheme_f        
        expressions = expressions.rest
        
    return val

bindings = {}

def eval_define(expressions):
    """
    >>> eval_define(Pair("a", Pair(1, nil)))
    'a'
    >>> eval_define(Pair("b", Pair(3, nil)))
    'b'
    >>> eval_define(Pair("c", Pair("a", nil)))
    'c'
    >>> calc_eval("c")
    1
    >>> calc_eval(Pair("define", Pair("d", Pair("//", nil))))
    'd'
    >>> calc_eval(Pair("d", Pair(4, Pair(2, nil))))
    2
    """
    symbol, value = expressions.first, calc_eval(expressions.rest.first)
    bindings[symbol] = value
    return symbol

OPERATORS = { "//": floor_div, "+": addition, "-": subtraction, "*": multiplication, "/": division }

class Pair:
    """A pair has two instance attributes: first and rest. rest must be a Pair or nil

    >>> s = Pair(1, Pair(2, nil))
    >>> s
    Pair(1, Pair(2, nil))
    >>> print(s)
    (1 2)
    >>> print(s.map(lambda x: x+4))
    (5 6)
    """
    def __init__(self, first, rest):
        self.first = first
        self.rest = rest

    def __repr__(self):
        return 'Pair({0}, {1})'.format(repr(self.first), repr(self.rest))

    def __str__(self):
        s = '(' + str(self.first)
        rest = self.rest
        while isinstance(rest, Pair):
            s += ' ' + str(rest.first)
            rest = rest.rest
        if rest is not nil:
            s += ' . ' + str(rest)
        return s + ')'

    def __len__(self):
        n, rest = 1, self.rest
        while isinstance(rest, Pair):
            n += 1
            rest = rest.rest
        if rest is not nil:
            raise TypeError('length attempted on improper list')
        return n

    def __eq__(self, p):
        if not isinstance(p, Pair):
            return False
        return self.first == p.first and self.rest == p.rest

    def map(self, fn):
        """Return a Scheme list after mapping Python function FN to SELF."""
        mapped = fn(self.first)
        if self.rest is nil or isinstance(self.rest, Pair):
            return Pair(mapped, self.rest.map(fn))
        else:
            raise TypeError('ill-formed list')

class nil:
    """The empty list"""

    def __repr__(self):
        return 'nil'

    def __str__(self):
        return '()'

    def __len__(self):
        return 0

    def map(self, fn):
        return self

nil = nil() # Assignment hides the nil class; there is only one instance
```



## lab11



```scheme
(define (cadr lst) (car (cdr lst)))

(define (make-kwlist1 keys values)
  (cons keys (cons values nil))
)

(define (get-keys-kwlist1 kwlist) (car kwlist))

(define (get-values-kwlist1 kwlist)
  (cadr kwlist)
)

(define (make-kwlist2 keys values)
  (if (null? keys) 
    nil 
    (cons (cons 
            (car keys) 
            (cons (car values) 
                  nil
            )
          ) 
          (make-kwlist2 
            (cdr keys)
            (cdr values)
          )
    )
  ) 
)

(define (get-keys-kwlist2 kwlist) 
  (if (null? kwlist) nil (cons (car (car kwlist)) (get-keys-kwlist2 (cdr kwlist))))
)

(define (get-values-kwlist2 kwlist)
  (if (null? kwlist) nil (cons (cadr (car kwlist)) (get-values-kwlist2 (cdr kwlist))))
)

(define (add-to-kwlist kwlist key value)
  (make-kwlist (append (get-keys-kwlist kwlist) (list key))  (append (get-values-kwlist kwlist) (list value)))
  
)

(define (get-first-from-kwlist kwlist key)
 (let
      (
        (values (get-values-kwlist kwlist))
        (keys (get-keys-kwlist kwlist))
        (find (lambda (f ks vs) 
                      (cond ((null? ks) nil) 
                            ((equal? (car ks) key) (car vs))
                            (else (f 
                                    f
                                    (cdr ks)
                                    (cdr vs)
                                  )
                            )
                      )
              )
        )
      )
      (find find keys values)
  )      
)
```



```sqlite
CREATE TABLE finals AS
  SELECT "RSF" AS hall, "61A" as course UNION
  SELECT "Wheeler"    , "61A"           UNION
  SELECT "Pimentel"   , "61A"           UNION
  SELECT "Li Ka Shing", "61A"           UNION
  SELECT "Stanley"    , "61A"           UNION
  SELECT "RSF"        , "61B"           UNION
  SELECT "Wheeler"    , "61B"           UNION
  SELECT "Morgan"     , "61B"           UNION
  SELECT "Wheeler"    , "61C"           UNION
  SELECT "Pimentel"   , "61C"           UNION
  SELECT "Soda 310"   , "61C"           UNION
  SELECT "Soda 306"   , "10"            UNION
  SELECT "RSF"        , "70";

CREATE TABLE sizes AS
  SELECT "RSF" AS room, 900 as seats    UNION
  SELECT "Wheeler"    , 700             UNION
  SELECT "Pimentel"   , 500             UNION
  SELECT "Li Ka Shing", 300             UNION
  SELECT "Stanley"    , 300             UNION
  SELECT "Morgan"     , 100             UNION
  SELECT "Soda 306"   , 80              UNION
  SELECT "Soda 310"   , 40              UNION
  SELECT "Soda 320"   , 30;

CREATE TABLE sharing AS
  SELECT a.course, COUNT(DISTINCT a.hall) AS shared FROM finals as a, finals as b 
    where a.course != b.course and a.hall = b.hall
  GROUP BY a.course;

CREATE TABLE pairs AS
  SELECT a.room || " and " || b.room || " together have " || (a.seats + b.seats) || " seats"AS rooms
  FROM sizes AS a, sizes AS b WHERE a.room < b.room AND a.seats + b.seats >= 1000
  ORDER BY a.seats + b.seats DESC;

CREATE TABLE big AS
  SELECT course FROM finals, sizes WHERE hall = room GROUP BY course HAVING sum(seats) >= 1000;

CREATE TABLE remaining AS
  SELECT course, SUM(seats) - MAX(seats) AS remaining
    FROM finals, sizes WHERE hall = room GROUP BY course;


```

## lab12


``` sqlite
.read data.sql

CREATE TABLE number_of_options AS
  SELECT count(DISTINCT(meat)) FROM main_course;


CREATE TABLE calories AS
  SELECT COUNT(*) FROM pies as a, main_course as b where (a.calories + b.calories) < 2500;


CREATE TABLE healthiest_meats AS
  SELECT meat,  MIN(m.calories + p.calories) as total 
  FROM main_course as m, pies as p 
  GROUP BY meat
  HAVING MAX(m.calories + p.calories) < 3000
  ORDER BY total ASC;


```

``` scheme

(define (no-repeats s) 
    (if (null? s) 
        s
        (cons (car s) 
            (no-repeats 
                (filter (lambda (x) (not (= x (car s)))) (cdr s))
            )
        )
    )
)

(define (without-duplicates lst) (no-repeats lst))

(define (reverse lst) 
    (
        if (null? lst) 
        lst
        (append (reverse (cdr lst)) (list (car lst)))
    )
)




```

``` python
def insert_into_all(item, nested_list):
    """Return a new list consisting of all the lists in nested_list,
    but with item added to the front of each. You can assume that
     nested_list is a list of lists.

    >>> nl = [[], [1, 2], [3]]
    >>> insert_into_all(0, nl)
    [[0], [0, 1, 2], [0, 3]]
    """
    return [[item] + lst  for lst in nested_list]

# fn(s) = fn(s - 1) + g(fn (s - 1), s)


def subseqs(s):
    """Return a nested list (a list of lists) of all subsequences of S.
    The subsequences can appear in any order. You can assume S is a list.

    >>> seqs = subseqs([1, 2, 3])
    >>> sorted(seqs)
    [[], [1], [1, 2], [1, 2, 3], [1, 3], [2], [2, 3], [3]]
    >>> subseqs([])
    [[]]
    """
    if len(s) == 0:
        return [[]]
    else:
        lst = subseqs(s[1:])
        return insert_into_all(s[0], lst) + lst

# fn(s, p) = fn(s - 1, p) + g(fn (s - 1, s), s)


def non_decrease_subseqs(s):
    """Assuming that S is a list, return a nested list of all subsequences
    of S (a list of lists) for which the elements of the subsequence
    are strictly nondecreasing. The subsequences can appear in any order.

    >>> seqs = non_decrease_subseqs([1, 3, 2])
    >>> sorted(seqs)
    [[], [1], [1, 2], [1, 3], [2], [3]]
    >>> non_decrease_subseqs([])
    [[]]
    >>> seqs2 = non_decrease_subseqs([1, 1, 2])
    >>> sorted(seqs2)
    [[], [1], [1], [1, 1], [1, 1, 2], [1, 2], [1, 2], [2]]
    """
    def subseq_helper(s, prev):
        if not s:
            return [[]]
        elif s[0] < prev:
            return subseq_helper(s[1:], prev)
        else:
            a = subseq_helper(s[1:], s[0]) 
            b = subseq_helper(s[1:], prev)
            return insert_into_all(s[0], a) + b
    return subseq_helper(s, -1)


def num_trees(n):
    """Returns the number of unique full binary trees with exactly n leaves. E.g.,

    1   2        3       3    ...
    *   *        *       *
       / \      / \     / \
      *   *    *   *   *   *
              / \         / \
             *   *       *   *

    >>> num_trees(1)
    1
    >>> num_trees(2)
    1
    >>> num_trees(3)
    2
    >>> num_trees(8)
    429

    """
    if n == 1 or n == 2:
        return 1
    return sum(num_trees(k) * num_trees(n - k) for k in range(1, n))

    
def partition_gen(n):
    """
    >>> partitions = [sorted(p) for p in partition_gen(4)]
    >>> for partition in sorted(partitions): # note: order doesn't matter
    ...     print(partition)
    [1, 1, 1, 1]
    [1, 1, 2]
    [1, 3]
    [2, 2]
    [4]
    """
    def yield_helper(num, segment):
        if num == 0:
            yield []
        elif num > 0 and segment > 0:
            for small_part in yield_helper(num - segment, segment):
                yield [segment] + small_part
            yield from yield_helper(num, segment - 1)
    yield from yield_helper(n, n)


class CucumberGame:
    """Play a round and return all winners so far. Cards is a list of pairs.
    Each (who, card) pair in cards indicates who plays and what card they play.
    >>> g = CucumberGame()
    >>> g.play_round(3, [(3, 4), (0, 8), (1, 8), (2, 5)])
    >>> g.winners
    [1]
    >>> g.play_round(1, [(3, 5), (1, 4), (2, 5), (0, 8), (3, 7), (0, 6), (1, 7)])
    It is not your turn, player 3
    It is not your turn, player 0
    The round is over, player 1
    >>> g.winners
    [1, 3]
    >>> g.play_round(3, [(3, 7), (2, 5), (0, 9)]) # Round is never completed
    It is not your turn, player 2
    >>> g.winners
    [1, 3]
    """
    def __init__(self):
        self.winners = []
        
    def play_round(self, starter, cards):
        r = Round(starter)
        for who, card in cards:
            try:
                r.play(who, card)
            except AssertionError as e:
                print(e)
        if r.winner != None:
            self.winners.append(r.winner)


class Round:
    players = 4

    def __init__(self, starter):
        self.starter = starter
        self.next_player = starter
        self.highest = -1
        self.winner = None

    def play(self, who, card):
        assert not self.is_complete(), f'The round is over, player {who}'
        assert who == self.next_player, f'It is not your turn, player {who}'
        self.next_player = (self.next_player + 1) % Round.players
        if card >= self.highest:
            self.highest = card
            self.temp = who
        if self.next_player == self.starter:
            self.winner = self.temp

            

    def is_complete(self):
        """ Checks if a game could end. """
        return self.winner is not None


def trade(first, second):
    """Exchange the smallest prefixes of first and second that have equal sum.

    >>> a = [1, 1, 3, 2, 1, 1, 4]
    >>> b = [4, 3, 2, 7]
    >>> trade(a, b) # Trades 1+1+3+2=7 for 4+3=7
    'Deal!'
    >>> a
    [4, 3, 1, 1, 4]
    >>> b
    [1, 1, 3, 2, 2, 7]
    >>> c = [3, 3, 2, 4, 1]
    >>> trade(b, c)
    'No deal!'
    >>> b
    [1, 1, 3, 2, 2, 7]
    >>> c
    [3, 3, 2, 4, 1]
    >>> trade(a, c)
    'Deal!'
    >>> a
    [3, 3, 2, 1, 4]
    >>> b
    [1, 1, 3, 2, 2, 7]
    >>> c
    [4, 3, 1, 4, 1]
    >>> d = [1, 1]
    >>> e = [2]
    >>> trade(d, e)
    'Deal!'
    >>> d
    [2]
    >>> e
    [1, 1]
    """
    m, n = 1, 1

    equal_prefix = lambda: sum(first[:m]) == sum(second[:n])
    while m <= len(first) and n <= len(second) and not equal_prefix():
        if sum(first[:m]) < sum(second[:n]):
            m += 1
        else:
            n += 1

    if equal_prefix():
        first[:m], second[:n] = second[:n], first[:m]
        return 'Deal!'
    else:
        return 'No deal!'

def card(n):
    """Return the playing card numeral as a string for a positive n <= 13."""
    assert type(n) == int and n > 0 and n <= 13, "Bad card n"
    specials = {1: 'A', 11: 'J', 12: 'Q', 13: 'K'}
    return specials.get(n, str(n))

def shuffle(cards):
    """Return a shuffled list that interleaves the two halves of cards.

    >>> shuffle(range(6))
    [0, 3, 1, 4, 2, 5]
    >>> suits = ['H', 'D', 'S', 'C']
    >>> cards = [card(n) + suit for n in range(1,14) for suit in suits]
    >>> cards[:12]
    ['AH', 'AD', 'AS', 'AC', '2H', '2D', '2S', '2C', '3H', '3D', '3S', '3C']
    >>> cards[26:30]
    ['7S', '7C', '8H', '8D']
    >>> shuffle(cards)[:12]
    ['AH', '7S', 'AD', '7C', 'AS', '8H', 'AC', '8D', '2H', '8S', '2D', '8C']
    >>> shuffle(shuffle(cards))[:12]
    ['AH', '4D', '7S', '10C', 'AD', '4S', '7C', 'JH', 'AS', '4C', '8H', 'JD']
    >>> cards[:12]  # Should not be changed
    ['AH', 'AD', 'AS', 'AC', '2H', '2D', '2S', '2C', '3H', '3D', '3S', '3C']
    """
    assert len(cards) % 2 == 0, 'len(cards) must be even'
    half = len(cards) // 2
    shuffled = []
    for i in range(half):
        shuffled.append(cards[i])
        shuffled.append(cards[i + half])
    return shuffled


def link_pop(lnk, index=-1):
    '''Implement the pop method for a Linked List.
    
    >>> lnk = Link(1, Link(2, Link(3, Link(4, Link(5)))))
    >>> removed = link_pop(lnk)
    >>> print(removed)
    5
    >>> print(lnk)
    <1 2 3 4>
    >>> link_pop(lnk, 2)
    3
    >>> print(lnk)
    <1 2 4>
    >>> link_pop(lnk)
    4
    >>> link_pop(lnk)
    2
    >>> print(lnk)
    <1>
    '''
    if index == -1:
        while not lnk.rest.rest is Link.empty:
            lnk = lnk.rest
        removed = lnk.rest.first
        lnk.rest = lnk.rest.rest
    else:
        while index > 1:
            lnk = lnk.rest
            index -= 1
        removed = lnk.rest.first
        lnk.rest = lnk.rest.rest
    return removed
        

def deep_len(lnk):
    """ Returns the deep length of a possibly deep linked list.

    >>> deep_len(Link(1, Link(2, Link(3))))
    3
    >>> deep_len(Link(Link(1, Link(2)), Link(3, Link(4))))
    4
    >>> levels = Link(Link(Link(1, Link(2)), Link(3)), Link(Link(4), Link(5)))
    >>> print(levels)
    <<<1 2> 3> <4> 5>
    >>> deep_len(levels)
    5
    """
    if lnk is Link.empty:
        return 0
    elif not isinstance(lnk, Link):
        return 1
    else:
        return deep_len(lnk.first) + deep_len(lnk.rest)


def every_other(s):
    """Mutates a linked list so that all the odd-indiced elements are removed
    (using 0-based indexing).

    >>> s = Link(1, Link(2, Link(3, Link(4))))
    >>> every_other(s)
    >>> s
    Link(1, Link(3))
    >>> odd_length = Link(5, Link(3, Link(1)))
    >>> every_other(odd_length)
    >>> odd_length
    Link(5, Link(1))
    >>> singleton = Link(4)
    >>> every_other(singleton)
    >>> singleton
    Link(4)
    """
    if s is Link.empty or s.rest is Link.empty:
        return
    s.rest = s.rest.rest
    every_other(s.rest)


class Link:
    """A linked list.

    >>> s = Link(1)
    >>> s.first
    1
    >>> s.rest is Link.empty
    True
    >>> s = Link(2, Link(3, Link(4)))
    >>> s.first = 5
    >>> s.rest.first = 6
    >>> s.rest.rest = Link.empty
    >>> s                                    # Displays the contents of repr(s)
    Link(5, Link(6))
    >>> s.rest = Link(7, Link(Link(8, Link(9))))
    >>> s
    Link(5, Link(7, Link(Link(8, Link(9)))))
    >>> print(s)                             # Prints str(s)
    <5 7 <8 9>>
    """
    empty = ()

    def __init__(self, first, rest=empty):
        assert rest is Link.empty or isinstance(rest, Link)
        self.first = first
        self.rest = rest

    def __repr__(self):
        if self.rest is not Link.empty:
            rest_repr = ', ' + repr(self.rest)
        else:
            rest_repr = ''
        return 'Link(' + repr(self.first) + rest_repr + ')'

    def __str__(self):
        string = '<'
        while self.rest is not Link.empty:
            string += str(self.first) + ' '
            self = self.rest
        return string + str(self.first) + '>'



```