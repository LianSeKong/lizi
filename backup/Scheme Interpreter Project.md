# Scheme Interpreter 



> CS61A  Project4 Summer 2024



## Problem 1 

> Frame类是用于存储当前执行环境中绑定的变量，每个Frame实例具有一个存储变量的字典和父Frame

1. define函数用于存储一个symbol和对应的值
2. lookup用于寻找Frame中的symbol对应的值（frame链作为一个链表结构，首元素为全局Frame）



```python
def define(self, symbol, value):
    """Define Scheme SYMBOL to have VALUE."""
    # BEGIN PROBLEM 1
    self.bindings[symbol] = value
    # END PROBLEM 1

def lookup(self, symbol):
    """Return the value bound to SYMBOL. Errors if SYMBOL is not found."""
    # BEGIN PROBLEM 1
    env = self
    while env is not None:
        if symbol in env.bindings.keys():
            return env.bindings[symbol]
        env = env.parent
    # END PROBLEM 1
    raise SchemeError('unknown identifier: {0}'.format(symbol))
```



## Problem 2

> scheme中存在内置BuiltinProcedure和LambdaProcedure，MuProcedure三种进程

首先实现内置进程的处理

```python
 if isinstance(procedure, BuiltinProcedure):
        # BEGIN PROBLEM 2
        py_args = []
        while args is not nil:
            py_args.append(args.first)
            args = args.rest
        if procedure.need_env:
            py_args.append(env)
        # END PROBLEM 2
        try:
            # BEGIN PROBLEM 2      
            return procedure.py_func(*py_args)

            # END PROBLEM 2
        except TypeError as err:
            raise SchemeError('incorrect number of arguments: {0}'.format(procedure))
```



## Problem 3 

>eval一个scheme表达式

Scheme表达式具有三种形式

1.  2
2. (+ 2 3 4 5 6)
3. a
4. (+ (sub expression) (sub expression))

既可以是一个可计算的数字、bool和symbol

也可以是一个可调用的程序

可调用的程序也可以包含可计算的字面量也可以是表达式或者symbol

```python
def scheme_eval(expr, env, _=None): # Optional third argument is ignored
    """Evaluate Scheme expression EXPR in Frame ENV.

    >>> expr = read_line('(+ 2 2)')
    >>> expr
    Pair('+', Pair(2, Pair(2, nil)))
    >>> scheme_eval(expr, create_global_frame())
    4
    """
    # Evaluate atoms
    if scheme_symbolp(expr):    # 处理定义的symbol
        return env.lookup(expr)
    elif self_evaluating(expr): # 处理数字和布尔值
        return expr

    # All non-atomic expressions are lists (combinations)
    if not scheme_listp(expr):  # 处理非法表达式格式
        raise SchemeError('malformed list: {0}'.format(repl_str(expr)))
    first, rest = expr.first, expr.rest  
    # 处理特殊的格式
    if scheme_symbolp(first) and first in scheme_forms.SPECIAL_FORMS:
        return scheme_forms.SPECIAL_FORMS[first](rest, env)
    else:
        # BEGIN PROBLEM 3
        if (isinstance(first, Pair)): #处理类似((if #t - +) 2 1)的情况 
            first = scheme_eval(first, env)
        elif not isinstance(first, BuiltinProcedure): #处理非内置程序
            first = env.lookup(first)
      	# 先执行 operand expression 
        rest = rest.map(lambda first: scheme_eval(first, env))
        # 等待 operand expression执行过后，再执行
        return scheme_apply(first, rest, env)

        # END PROBLEM 3

```



## Problem 4

> 处理(define x subexpression)情况

```python
    if scheme_symbolp(signature):
        # assigning a name to a value e.g. (define x (+ 1 2))
        validate_form(expressions, 2, 2) 
        # BEGIN PROBLEM 4
        env.define(signature, scheme_eval(expressions.rest.first, env))
        return signature
        # END PROBLEM 4
```



## Problem 5

> 直接返回quote后面的表达式即可，无需计算

```python
def do_quote_form(expressions, env):
    """Evaluate a quote form.

    >>> env = create_global_frame()
    >>> do_quote_form(read_line("((+ x 2))"), env) # evaluating (quote (+ x 2))
    Pair('+', Pair('x', Pair(2, nil)))
    """
    validate_form(expressions, 1, 1)
    # BEGIN PROBLEM 5
    return expressions.first
    # END PROBLEM 5
```



## Problem 6



>处理类似于begin这种语句， 计算所有子表达式，返回最后一个表达式的值

```python
def eval_all(expressions, env):
    """Evaluate each expression in the Scheme list EXPRESSIONS in
    Frame ENV (the current environment) and return the value of the last.

    >>> eval_all(read_line("(1)"), create_global_frame())
    1
    >>> eval_all(read_line("(1 2)"), create_global_frame())
    2
    >>> x = eval_all(read_line("((print 1) 2)"), create_global_frame())
    1
    >>> x
    2
    >>> eval_all(read_line("((define x 2) x)"), create_global_frame())
    2
    """
    # BEGIN PROBLEM 6
    
    while not expressions is nil:
        val = scheme_eval(expressions.first, env)
        expressions = expressions.rest
        if expressions is nil:
            return val
    # END PROBLEM 6
```



## Problem 7 

> 根据内置定义，去创建一个lambda程序的实例



```python
def do_lambda_form(expressions, env):
    """Evaluate a lambda form.

    >>> env = create_global_frame()
    >>> do_lambda_form(read_line("((x) (+ x 2))"), env) # evaluating (lambda (x) (+ x 2))
    LambdaProcedure(Pair('x', nil), Pair(Pair('+', Pair('x', Pair(2, nil))), nil), <Global Frame>)
    """
    validate_form(expressions, 2)
    formals = expressions.first
    validate_formals(formals)
    # BEGIN PROBLEM 7
    body = expressions.rest
    return LambdaProcedure(formals, body, env)

    # END PROBLEM 7

```



## Problem 8

> lambda程序的执行会创建一个局部的frame，根据参数symbol和参数值在局部的frame的bindings中建立映射



```python
def make_child_frame(self, formals, vals):
    """Return a new local frame whose parent is SELF, in which the symbols
    in a Scheme list of formal parameters FORMALS are bound to the Scheme
    values in the Scheme list VALS. Both FORMALS and VALS are represented
    as Pairs. Raise an error if too many or too few vals are given.

    >>> env = create_global_frame()
    >>> formals, expressions = read_line('(a b c)'), read_line('(1 2 3)')
    >>> env.make_child_frame(formals, expressions)
    <{a: 1, b: 2, c: 3} -> <Global Frame>>
    """
    if len(formals) != len(vals):
        raise SchemeError('Incorrect number of arguments to function call')
    # BEGIN PROBLEM 8
    frame = Frame(self)
    while not formals is nil:
        val = vals.first
        key = formals.first
        frame.define(key, val)
        formals = formals.rest
        vals = vals.rest
    return frame

    # END PROBLEM 8
```



## Problem 9 



>执行一个lambda程序，首先创建一个局部frame，然后执行lambda的body里的全部表达式
>
>此时调用eval_all而不是scheme_eval的原因是: lambda程序的body中会具有多个子表达式，lambda会逐个执行，返回最后一个表达式执行后的结果



```python
elif isinstance(procedure, LambdaProcedure):
    # BEGIN PROBLEM 9
    frame = procedure.env.make_child_frame(procedure.formals, args)
    return eval_all(procedure.body, frame)
    # END PROBLEM 9
```



## Problem 10

>之前实现的define定义symbol的情况，现在来处理定义一个procedure的情况 
>
>1. 首次提取procedure的symbol、arguments和body
>2. 验证arguments是否符合规则
>3. 创建一个lambda表达式
>4. 向frame的bindings中建立映射

```python
    elif isinstance(signature, Pair) and scheme_symbolp(signature.first):
        # defining a named procedure e.g. (define (f x y) (+ x y))
        # BEGIN PROBLEM 10
        symbol = signature.first
        formals = signature.rest
        body = expressions.rest

        validate_formals(formals)

        val = LambdaProcedure(formals, body, env)
        env.define(symbol, val)
        return symbol
        # END PROBLEM 10

```



## Problem 11

>do_mu_form： 执行时的父frame不取决于定义procedure的的frame，而取决于执行时的frame



```python
def do_mu_form(expressions, env):
    """Evaluate a mu form."""
    validate_form(expressions, 2)
    formals = expressions.first
    validate_formals(formals)
    # BEGIN PROBLEM 11
    body = expressions.rest
    return MuProcedure(formals, body)
    # END PROBLEM 11
```



执行mu程序时，根据当前的frame去创建局部frame

```python
    elif isinstance(procedure, MuProcedure):
        # BEGIN PROBLEM 11
        frame = env.make_child_frame(procedure.formals, args)
        return eval_all(procedure.body, frame)
```





## Problem 12

> 处理and和if，规则在项目开始文件中已经描述的很清楚了



```python
def do_and_form(expressions, env):
    """Evaluate a (short-circuited) and form.

    >>> env = create_global_frame()
    >>> do_and_form(read_line("(#f (print 1))"), env) # evaluating (and #f (print 1))
    False
    >>> # evaluating (and (print 1) (print 2) (print 4) 3 #f)
    >>> do_and_form(read_line("((print 1) (print 2) (print 3) (print 4) 3 #f)"), env)
    1
    2
    3
    4
    False
    """
    # BEGIN PROBLEM 12
    while not expressions is nil:
        val = scheme_eval(expressions.first, env)
        if is_scheme_false(val):
            return False
        if expressions.rest is nil:
            return val
        expressions = expressions.rest
    return True
    # END PROBLEM 12

def do_or_form(expressions, env):
    """Evaluate a (short-circuited) or form.

    >>> env = create_global_frame()
    >>> do_or_form(read_line("(10 (print 1))"), env) # evaluating (or 10 (print 1))
    10
    >>> do_or_form(read_line("(#f 2 3 #t #f)"), env) # evaluating (or #f 2 3 #t #f)
    2
    >>> # evaluating (or (begin (print 1) #f) (begin (print 2) #f) 6 (begin (print 3) 7))
    >>> do_or_form(read_line("((begin (print 1) #f) (begin (print 2) #f) 6 (begin (print 3) 7))"), env)
    1
    2
    6
    """
    # BEGIN PROBLEM 12
    while not expressions is nil:
        val = scheme_eval(expressions.first, env)
        if is_scheme_true(val):
            return val
        expressions = expressions.rest
    return False
    # END PROBLEM 12
```



## Problem 13 

> do_cond_form:  具有多个子表达式（包含else）,如果是else则直接返回表达式执行的结果。
>
> 若是子表达式中条件表达式执行为真，则返回条件后面表达式执行的结果，若是条件表达式后面为空，则返回条件表达式执行的结果

```python
def do_cond_form(expressions, env):
    """Evaluate a cond form.

    >>> do_cond_form(read_line("((#f (print 2)) (#t 3))"), create_global_frame())
    3
    """
    while expressions is not nil:
        clause = expressions.first
        validate_form(clause, 1)
        if clause.first == 'else':
            test = True
            if expressions.rest != nil:
                raise SchemeError('else must be last')
        else:
            test = scheme_eval(clause.first, env)
        if is_scheme_true(test):
            # BEGIN PROBLEM 13
                    
            body = clause.rest
            if body is nil:
                return test
            return eval_all(body, env)

            # END PROBLEM 13
        expressions = expressions.rest
```



## Problem 14

> make_let_frame:
>
> 1. 验证每个子表达式是否符合规范
> 2. 创建一个局部frame
> 3. 遍历执行每个子表达式（不包含需要返回执行结果的表达式）



```python
def make_let_frame(bindings, env):
    """Create a child frame of Frame ENV that contains the definitions given in
    BINDINGS. The Scheme list BINDINGS must have the form of a proper bindings
    list in a let expression: each item must be a list containing a symbol
    and a Scheme expression."""
    if not scheme_listp(bindings):
        raise SchemeError('bad bindings list in let form')
    names = vals = nil
    # BEGIN PROBLEM 14
    validate_formals(bindings.map(lambda x: x.first))
    let_env = Frame(env)
    while not bindings is nil:
        current = bindings.first
        validate_form(current.rest, 1, 1)
        let_env.define(current.first, scheme_eval(current.rest.first, env))
        bindings = bindings.rest
    return let_env
    # END PROBLEM 14

```



## Problem 15

```scheme
(define (enumerate2 s)
  (begin 
    (define it (lambda (s i) 
          (if (null? s) nil (cons (cons i (cons (car s) nil)) (it (cdr s) (+ i 1))))
        )
    )   
   (it s 0)
  )
)
```



## Problem 16



```scheme
(define (merge ordered? s1 s2)
  (
    cond 
      ((null? s1) s2)
      ((null? s2) s1)
      ((ordered? (car s1) (car s2)) (cons (car s1) (merge ordered? (cdr s1) s2)))
      (else (cons (car s2) (merge ordered? s1 (cdr s2))))
    
  )
)
```





## EC Problem (pending)