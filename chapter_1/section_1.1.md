# 1.1 The Elements of Programming

1.1. Below is a sequence of expressions. What is the result printed by the interpreter in response to each expression? Assume that the sequence is to be evaluated in the order in which it is presented.

Answer:
```
> 10
10  
> (+ 5 3 4)
12
> (- 9 1)
8
> (/ 6 2)
3
> (+ (* 2 4) (- 4 6))
6
> (define a 3)
a
> (define b (+ a 1))
b
> (+ a b (* a b))
19
> (= a b)
#f
> (if (and (> b a) (< b (* a b))) b a)
4
> (cond ((= a 4) 6) ((= b 4) (+ 6 7 a)) (else 25))
16
> (+ 2 (if (> b a) b a))
6
> (* (cond ((> a b) a) ((< a b) b) (else -1)) (+ a 1))
16
```
1.2. Translate the following expression into prefix form ![confusing quocient](https://mitpress.mit.edu/sites/default/files/sicp/full-text/book/ch1-Z-G-3.gif)

Answer:

`(/ (+ 5 4 (- 2 (- 3 (+ 6 (/ 4 3))))) (* 3 (- 6 2) (- 7 2)))`

1.3. Define a procedure that takes three numbers as arguments and returns the sum of the squares of the two larger numbers.

Answer:

```
(define (sum-squares-two-larger a b c)
	(+ (square (greatest a b c))
    	(square (- (+ a b c) (greatest a b c) (lowest a b c)))))

(define (greatest a b c)
	(cond ((and (>= a b) (>= a c)) a) ((>= b c) b) (else c)))

(define (lowest a b c) (- (greatest (-a) (-b) (-c))))

(define (>= a b) (not (< a b)))
```
1.4. Observe that our model of evaluation allows for combinations whose operators are compound expressions. Use this observation to describe the behavior of the following procedure:
```
(define (a-plus-abs-b a b)
  ((if (> b 0) + -) a b))
```

Answer:

The procedures uses an if statement to select which function to apply depending on the predicate.

1.5. Ben Bitdiddle has invented a test to determine whether the interpreter he is faced with is using applicative-order evaluation or normal-order evaluation. He defines the following two procedures:
```
(define (p) (p))

(define (test x y)
  (if (= x 0)
      0
      y))
```
Then he evaluates the expression
```
(test 0 (p))
```
What behavior will Ben observe with an interpreter that uses applicative-order evaluation? What behavior will he observe with an interpreter that uses normal-order evaluation? Explain your answer. (Assume that the evaluation rule for the special form if is the same whether the interpreter is using normal or applicative order: The predicate expression is evaluated first, and the result determines whether to evaluate the consequent or the alternative expression.)

Answer:

In applicative-order evaluation (as it is in Scheme) calling this procedure will result in an infinite loop because all the arguments will be evaluated before calling a function. The argument `(p)` is a call to a function the calls itself, because of this we have an infinite loop.

In normal-order evaluation (as in Haskell) no arguments will be evaluated before its value is necessary, so the `(test)` call will return 0 if x equals 0 and evaluate y otherwise.

1.6. Alyssa P. Hacker doesn't see why if needs to be provided as a special form. "Why can't I just define it as an ordinary procedure in terms of `cond`?" she asks. Alyssa's friend Eva Lu Ator claims this can indeed be done, and she defines a new version of if:
```
(define (new-if predicate then-clause else-clause)
  (cond (predicate then-clause)
        (else else-clause)))
```
Eva demonstrates the program for Alyssa:
```
> (new-if (= 2 3) 0 5)
5

> (new-if (= 1 1) 0 5)
0
```
Delighted, Alyssa uses new-if to rewrite the square-root program:
```
(define (sqrt-iter guess x)
  (new-if (good-enough? guess x)
          guess
          (sqrt-iter (improve guess x)
                     x)))
```
What happens when Alyssa attempts to use this to compute square roots? Explain.

Answer:

Making the conditional statement into a procedure have broken the special evaluation rule from the statement and exchanged it by the one of the language. It means infinite loop.

1.7. The good-enough? test used in computing square roots will not be very effective for finding the square roots of very small numbers. Also, in real computers, arithmetic operations are almost always performed with limited precision. This makes our test inadequate for very large numbers. Explain these statements, with examples showing how the test fails for small and large numbers. An alternative strategy for implementing good-enough? is to watch how guess changes from one iteration to the next and to stop when the change is a very small fraction of the guess. Design a square-root procedure that uses this kind of end test. Does this work better for small and large numbers?

Answer:

The counterexample:
```
> (sqrt (square 0.01))
0.0323
```

The solution:
```
(define (sqrt x) (sqrt-iter 1.0 2.0 x))

(define (sqrt-iter guess next-guess x)
	(if (good-enough? guess next-guess)
    	next-guess
        (sqrt-iter next-guess (improve next-guess x) x)))

(define (good-enough? guess next-guess)
	(< (abs (- guess next-guess)) 0.001))
```

There are no changes in the `improve` or `average` procedures.

1.8. Newton's method for cube roots is based on the fact that if y is an approximation to the cube root of x, then a better approximation is given by the value

![expression of the newton approximation for the cube root](https://mitpress.mit.edu/sites/default/files/sicp/full-text/book/ch1-Z-G-5.gif)

Use this formula to implement a cube-root procedure analogous to the square-root procedure. (In section 1.3.4 we will see how to implement Newton's method in general as an abstraction of these square-root and cube-root procedures.)

Answer:

```
(define (cube-root x) (cube-root-iter 1.0 2.0 x))

(define (cube-root-iter guess next-guess x) 
	(if (good-enough? guess next-guess)
    	next-guess
        (cube-root-iter next-guess (improve next-guess x) x)))

(define (improve guess x) 
	(/ (+ (/ x (square y)) (* 2 y)) 3))
```

With the `good-enough?` procedure defined as above.