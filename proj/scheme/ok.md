# Part I: Reader

## Problem 1
>>> read_line("'x")
Pair('quote', Pair('x', nil))

>>> read_line("(quote x)")
Pair('x', nil)

>>> read_line("'(a b)")
Pair('quote', Pair(Pair('a', Pair('b', nil)), nil))

>>> read_line("'((a))")
Pair('quote', Pair(Pair(Pair('a', nil), nil), nil))

>>> read_line("(a (b 'c))")
Pair('a', Pair(Pair('b', Pair(Pair('quote', Pair('c', nil)), nil)), nil))

## Problem 2
>>> read_line("(a . b)")
Pair('a', 'b')

>>> read_line("(a b . c)")
Pair('a', Pair('b', 'c'))

>>> read_line("(a b . c d)")
SyntaxError

>>> read_line("(a . (b . (c . ())))")
Pair('a', Pair('b', Pair('c', nil)))

>>> read_line("(a . ((b . (c)))))")
Pair('a', Pair(Pair('b', Pair('c', nil)), nil))

# Part II: The Evaluator

<code>python3 ok -q eval_apply -u</code>
Q: When does scheme_apply call scheme_eval?
For a user-defined procedure only

Q: What expression in the body of scheme_eval computes
the value of a symbol
env.lookup(expr)

Q: What exception will be raised for the expression (1)
SchemeError("cannot call: 1")

## Problem 3
>>> from scheme import *
>>> global_frame = create_global_frame()
>>> global_frame.define("x", 3)
>>> global_frame.parent is None
True
>>> global_frame.lookup("x")
3
>>> global_frame.lookup("foo")
SchemeError

<hr>

>>> from scheme import *
>>> first_frame = create_global_frame()
>>> first_frame.define("x", 3)
>>> second_frame = Frame(first_frame)
>>> second_frame.parent == first_frame
True
>>> second_frame.lookup("x")
3

## Problem 4
>>> from scheme import *
>>> env = create_global_frame()
>>> twos = Pair(2, Pair(2, nil))
>>> plus = PrimitiveProcedure(scheme_add) # + procedure
>>> scheme_apply(plus, twos, env)
4

>>> oddp = PrimitiveProcedure(scheme_oddp) # odd? procedure
>>> scheme_apply(oddp, twos, env)
SchemeError

>>> two = Pair(2, nil)
>>> eval = PrimitiveProcedure(scheme_eval, True) # eval procedure
>>> scheme_apply(eval, two, env) # be sure to check use_env
2

>>> from scheme import *
>>> env = create_global_frame()
>>> args = nil
>>> def make_scheme_counter():
...     x = 0
...     def scheme_counter():
...         nonlocal x
...         x += 1
...         return x
...     return scheme_counter
>>> counter = PrimitiveProcedure(make_scheme_counter()) # counter
>>> scheme_apply(counter, args, env) # only call procedure.fn once!
1

## Problem 5A
Q: What is the structure of the expressions argument to do_define_form?
Pair(A, Pair(B, nil)), where:
       A is the symbol being bound,
       B is an expression whose value should be bound to A

Q: What method of a Frame instance will bind a value to a symbol in that frame?
define

scm> (define size 2)
size

scm> size
2

scm> (define x (+ 2 3))
x

scm> x
5

scm> (define x (+ 2 7))
? x

scm> x
? 9

scm> (eval (define tau 6.28))
? 6.28

## Problem 7
>>> from scheme import *
>>> env = create_global_frame()
>>> eval_all(Pair(2, nil), env)
? 2
>>> eval_all(Pair(4, Pair(5, nil)), env)
? 5
>>> eval_all(nil, env) # return okay (meaning undefined)
? okay

scm> (begin (+ 2 3) (+ 5 6))
? 11

scm> (begin (define x 3) x)
? 3

scm> (begin 30 '(+ 2 2))
(+ 2 2)

scm> (define x 0)
? x

scm> (begin 42 (define x (+ x 1)))
? x

## Problme 8
scm> (lambda (x y) (+ x y))
(lambda (x y) (+ x y))

scm> (lambda (x) (+ x) (+ x x))
(lambda (x) (+ x) (+ x x))

scm> (lambda () 2)
(lambda () 2)

## Problem 9 
scm> (define f (lambda (x) (* x 2)))
f

scm> (begin (define (f x y) (+ x y)) f)
(lambda (x y) (+ x y))

scm> (define (f x) (* x 2))
f

## Problem 10 
>>> from scheme import *
>>> global_frame = create_global_frame()
>>> frame = global_frame.make_child_frame(Pair('a', Pair('b', Pair('c', nil))), Pair(1, Pair(2, Pair(3, nil))))
>>> frame.parent
<Global Frame>
>>> global_frame.lookup('a')
>>> frame.lookup('a')
 1

## Problem 11
scm> (lambda (h e l l o) 'world)
SchemeError

## Problem 12
scm> ((lambda (x) (list x (list (quote quote) x))) (quote (lambda (x) (list x (list (quote quote) x)))))
((lambda (x) (list x (list (quote quote) x))) (quote (lambda (x) (list x (list (quote quote) x)))))

---------------------------
(define (square x) (* x x))

>>> expr = read_line("(define (square x) (* x x))")
>>> expr
Pair('define', Pair(Pair('square', Pair('x', nil)), Pair(Pair('*', Pair('x', Pair('x', nil))), nil)))
