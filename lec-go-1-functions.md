% Part 1: Go Basics + Higher-order functions + Lambda calculus
% Martin Sulzmann  
 

# Overview


* Introduction to Go (basics)
* Higher-order functions
* Lambda calculus (a bit of theory)


# Hello World

~~~~~~~~~~~{.go}
package main

import "fmt"

var x int

func hi(y int) {
        fmt.Printf("hi %d\n",y)
}

func main() {
    x= 1
    hi(x)
    fmt.Printf("hello, world\n")
}
~~~~~~~~~~~~~~~~~~~~~~

* Type declarations in 'proper' order
    * `var varName varType`
    * Variable `varName` of Type `varType`

# Go Toolchain

* Comand line:
    * `go run hello.go`

    * `gofmt hello.go`
        * "pretty printer"
        * Per Default to standard I/O
        * Or bei overwriting content `gofmt -w hello.go` 

    * Choose your editor (emacs, ...)

* IDE:
    [http://code.google.com/p/liteide/](IDE Go)


In most cases, our programs consist of a single file.

# Simple form of type inference

~~~~~~~~~{.go}
package main

import "fmt"

func main() {
    var x int
	x = 1
	y := x + 1
	fmt.Printf("y = %d", y)

}
~~~~~~~~~~~~~

The type of `y` is inferred by the right-hand side.

Pretty convenient!

Languages like Haskell support *full* type inference
where the types of functions can be inferred.


# Control structures - for loop 

The only control structure.

~~~~~~~~{.go}
	for i := 0; i < 5; i++ {
		fmt.Printf("Value of i is now: %d \n", i)
	}
~~~~~~~~~~~~~~~~~

Infinite loop with `break`

~~~~~~~~{.go}
	for {
		if j > 5 {
			break
		}
		fmt.Printf("Value of j is now: %d \n", j)
		j++

	}
~~~~~~~~~~~~~~~~~~~


## Complete example

~~~~~~{.go}
package main

import "fmt"

func main() {

	for i := 0; i < 5; i++ {
		fmt.Printf("Value of i is now: %d \n", i)
	}

	j := 0
	for {
		if j > 5 {
			break
		}
		fmt.Printf("Value of j is now: %d \n", j)
		j++

	}

}
~~~~~~~~~~~~


# Strings

Unlike in C, Go strings are not null terminated.
In Go, strings are represented by a struct
consisting of

1. The length of the string.

2. A pointer to the first byte.

These implementation details are hidden from the user.
The user can make use of array notation
to access a specific position in the string.
There is a primitive `len` that computes the length of a string.
Strings are immutable. This means they cannot be updated (but copied of course).

Next, we discuss a few examples that involve strings.


Printing a string.

~~~~~{.go}
	var x string = "Hello"
	y := "Hi"

	fmt.Println(x)
	fmt.Printf("%s \n", y)
~~~~~~~~~


Several ways to iterate over a string.

~~~~~{.go}
	for i := 0; i < len(x); i++ {
		fmt.Printf("%c", x[i])

	}

	for i, _ := range x {
		fmt.Printf("%c", x[i])

	}

	for _, e := range x {
		fmt.Printf("%c", e)

	}
~~~~~~~~

Via `range` we obtain current position and element.
The notation `_` indicates that we don't care about the value.

 
# Arrays

Out of bounds check.

~~~~~~~{.go}
	var s1 [3]string
	s1[0] = "one"
	s1[1] = "two"
	s1[2] = "three"
	// s1[3] = "four"
~~~~~~~~~~~~~~~~~

Short-hand array initialization

~~~~~~~~{.go}
	s2 := [3]string{"one", "two", "three"}
~~~~~~~~~~~~~~~~~~~

Iteration

~~~~~~~{.go}
	for index, elem := range s1 {
		fmt.Printf("%d %s \n", index, elem)
	}
~~~~~~~~~~~~~~~~~~


## Complete example

~~~~~~~~{.go}
package main

import "fmt"

func main() {

	var s1 [3]string
	s1[0] = "one"
	s1[1] = "two"
	s1[2] = "three"

	for index, elem := range s1 {
		fmt.Printf("%d %s \n", index, elem)
	}

	s2 := [3]string{"one", "two", "three"}

	fmt.Printf("%s", s2[0])
}
~~~~~~~~~~~~~~~~~~~

# Slices

More flexible arrays (length may change)

~~~~~~~{.go}
	s1 := make([]string, 3)
~~~~~~~~~~~~~~~~~

Short-hand

~~~~~~{.go}
	s2 := []string{"a", "b"}
~~~~~~~~~~


Functions on slices, e.g. `append`

~~~~~~~{.go}
	s2 := []string{"a", "b"}
	s3 := append(s2, "c", "d", "e")
~~~~~~~~~~~~~~~~

## Complete example

~~~~~~{.go}
package main

import "fmt"

func printSlice(s []string) {
	for _, elem := range s {
		fmt.Printf("%s \n", elem)
	}

}

func main() {

	s1 := make([]string, 3)

	s1[0] = "one"
	s1[1] = "two"
	s1[2] = "three"

	printSlice(s1)

	s2 := []string{"a", "b"}

	s3 := append(s2, "c", "d", "e")

	printSlice(s3)
}
~~~~~~~~~~~~~

# Functions - Return values 

* Return types in 'proper' order

~~~~~~~{.go}
func inc(i int) int
~~~~~~~~~~~~~~~~~~~

* Multiple return values

~~~~~~~{.go}
func myDiv2(x int, y int) (int, bool)
~~~~~~~~~~~~~~~~~~~

* Complete example


~~~~~~~~{.go}
package main

import "fmt"

func inc(i int) int { return i + 1 }

// Direct reference to return values by name
func myDiv(x int, y int) (res int, status bool) {
	status = false
	if y == 0 {
		return
	}
	res = x / y
	status = true
	return
}

func myDiv2(x int, y int) (int, bool) {
	if y == 0 {
		return 0, false
	}
	return x / y, true
}

func main() {
	var res int
	var status bool
	res, status = myDiv(inc(3), 2)
	fmt.Printf("Result = %d \n", res)
	fmt.Printf("Status = %t \n", status)

	res, status = myDiv2(1, 0)
	fmt.Printf("Result = %d \n", res)
	fmt.Printf("Status = %t \n", status)
}
~~~~~~~~~~~~~~~~~~~~~~~~

# Strings again

We write a function to split a string into a prefix and a suffix
once we see given byte.

~~~~~{.go}
func splitAt(x string, b byte) (string, string) {
	var r1, r2 []byte
	suffix := false

	for i, _ := range x {
		if !suffix {
			if x[i] == b {
				suffix = true
			} else {
				r1 = append(r1, x[i])
			}

		} else {
			r2 = append(r2, x[i])
		}
	}

	s1 := (string)(r1)
	s2 := (string)(r2)

	return s1, s2

}
~~~~~~~~~~~

We use slices r1 and r2 to collect the prefix and suffix.
We check if we have seen the given element b yet,
and then append elements in the original string to either r1 or 2.
The element b is not added.

We require slices as append only operates on slices.
As we wish to retrieve the result as strings,
we need to include a type conversion.

Function splitAt returns two results, the prefix and the suffix.
Multiple return results can be retrieved as follows.

~~~~~~{.go}
	r1, r2 := splitAt("hel:lo", ':')

	fmt.Printf("%s %s", r1, r2)
~~~~~~~~~~~



# Higher-order functions


Simple (increment) function.

~~~~~~~~{.go}
func inc(i int) int { return i + 1 }
~~~~~~~~~~~~~~~~~


Functions can be arguments.

~~~~~{.go}
func apply(i int, f func(int) int) int {
	return f(i)
	}


func mapInt(f func(int) int, xs []int) []int {
	for i, e := range xs {
		xs[i] = f(e)
	}

	return xs
}
~~~~~~~~~~

Functions can be return values.

~~~~~~~~~~{.go}
func hello2(x int) func() {
	return func() { fmt.Printf("Hello %d \n", x) }
}
~~~~~~~~~~~~~~~~~~~


Point to note. We introduce here an annonymous function (a "lambda")
via the keyword `func`.


## Complete example

~~~~~~~~~~{.go}
package main

import "fmt"

func inc(i int) int { return i + 1 }

func apply(i int, f func(int) int) int {
	return f(i)
}

func execute(f func()) {
	f()
}

func hello() {
	fmt.Print("Hello \n")
}

func hello2(x int) func() {
	return func() { fmt.Printf("Hello %d \n", x) }
}

func mapInt(f func(int) int, xs []int) []int {
	for i, e := range xs {
		xs[i] = f(e)
	}

	return xs
}

func main() {
	execute(hello)

	execute(hello2(2))

	fmt.Printf("%d \n", apply(1, inc))

	xs := []int{1, 2, 3}
	ys := mapInt(inc, xs)
	zs := mapInt(func(i int) int { return i + 1 }, xs)

	fmt.Printf("%d %d \n", ys[0], zs[0])

}

~~~~~~~~~~~~~~~~~~

 


# Partial function application

Consider

~~~~~~{.go}
func plus(x int, y int) int {
	return x+y
}
~~~~~~~~~~~~~

The arguments to `plus` must be both present.

In Go, it's possible to 'incrementally' supply addition with its arguments.

~~~~~~~~{.go}
func add(x int) func(int) int {
	return func(y int) int { return x + y }
}
~~~~~~~~~~~~~

Function `add` expects an integer argument (left operand)
and yields a function. This function expects
another integer argument (right operand) and then
yields the expected result.

Being able to supply function arguments incrementally gives
us more flexibility. Here is a neat way to define
the increment function.

~~~~~~~~{.go}
func inc(x int) int {
	return add(1)(x)
}
~~~~~~~~~~~~~~


* `add(1) yields a function from integer to integer

* `add(1)(x)` then yields the incremented `x` value


Slight variation of the above.

~~~~~~~~{.go}
	plus := func(x int) func(int) int {
		return func(y int) int { return x + y }
	}


	inc := plus(1)
~~~~~~~~~~~~~~~~


## Connection to OO

In Go functions are first-class (that is, they can appear anywwhere).
This is similar to OO where objects are first-class.

For example, in an OO language, we can call a method `m1` on
some object `o1`. The result is an object on which
we call another method `m2`.

~~~~~~~~~~~{.go}
o1.m1().m2()
~~~~~~~~~~~~~~~

## Currying

Is there a difference?

~~~~~{.go}
func plus1(x int, y int) int {
	return x + y
}

func plus2(x int) func(int) int {
	return func(y int) int {
		return x + y
	}
}
~~~~~~~~~~~~~


How to transform one function into the other?

### Complete source code

~~~~~{.go}
package main

import "fmt"

func plus1(x int, y int) int {
	return x + y
}

func plus2(x int) func(int) int {
	return func(y int) int {
		return x + y
	}
}

func curry(f func(int, int) int) func(int) func(int) int {
	return func(x int) func(int) int {
		return func(y int) int {
			return f(x, y)
		}
	}

}

func uncurry(g func(int) func(int) int) func(int, int) int {
	return func(x int, y int) int {
		return (g(x))(y)
	}
}

func main() {

	x1 := plus1(1, 2)
	x2 := (plus2(1))(2)
	p := uncurry(plus2)
	x3 := p(1, 2)
	p2 := curry(plus1)
	x4 := (p2(1))(2)

	fmt.Printf("%d %d %d %d", x1, x2, x3, x4)

}
~~~~~~~~~

 

# Function closures

What's the output of the following program?

~~~~~~~{.go}
package main

import "fmt"

type Type0 func()

func main() {
	var fn Type0
	var x int = 2

	fn = func() { fmt.Printf("%d \n", x) }

	fn()

	x = 3
	fn()
}
~~~~~~~~~~~~~~~

For each call to `fn` there is a different result!

Unlike *pure* functional programming languages, the Go language
is *impure*. Pure means that for each function call and the same
input arguments, we obtain the same output.
This is also referred to as [referential transparency](https://en.wikipedia.org/wiki/Referential_transparency).


# Type definitions and structs (named types)

## Type definitions

~~~~~{.go}
type myint int
~~~~~~~~~~

Introduces a new type named `myint`.
Values of type `int` and `myint` cannot be mixed.

The type `myint` is structurally isomorphic to `int`,
so we can (type) convert one into the other.

Here is the complete example.

~~~~{.go}
package main

import "fmt"

type myint int

func main() {
	var i int
	var j myint

	j = 1

	i = (int)(j)
	// i = j
        // yields a type error

	j = (myint)(i)

	fmt.Printf("%d", i)
}
~~~~~~~~~~

## Structs
 
~~~~~~~~~~~~~~{.go}
type rectangle struct {
	length int
	width  int
}
~~~~~~~~~~~~~~~~~~~~~

Defines a named type `rectangle` that is structurally
isomorphic to a struct of two ints where both ints can be accessed
via field labels.


Construction of values.

~~~~~~~~~~{.go}
	var r1 rectangle = rectangle{1, 2}
	var r2 rectangle = rectangle{width: 2, length: 1}
	r3 := rectangle{width: 2, length: 1}
~~~~~~~~~~~~~~~~

Either by position or by field reference.



# Intermediate Summary


* Control structures

* Arrays, slices

* Functions
    * Higher-order
    * Multiple return values
* Structs
    * OO style programming

# Some (simple) exercises 

Play with the examples provided.

## any, map, filter, ...

Implement the following functions. Sample solutions below.

~~~~~{.go}
// Yields the first element which satisfies the predicate.
// Otherwise, we return a dummy element.
// The first component of the resulting tuple indicates
// if any element has satisfied the predicate.
func any(p func(int) bool,xs []int) (bool,int)


// Map f over a list of integer values which then yields
// a list of booleans
func map(f func(int) bool,xs []int) []bool


// Filter out all elemements which satisfy the predicate.
func filter(p func(int) bool,xs []int) []int
~~~~~~~~~~~~


### Sample solution

~~~~~~~~{.go}
package main

import "fmt"

// Yields the first element which satisfies the predicate.
// Otherwise, we return a dummy element.
// The first component of the resulting tuple indicates
// if any element has satisfied the predicate.
func any(p func(int) bool, xs []int) (bool, int) {
	for _, x := range xs {
		if p(x) {
			return true, x
		}
	}
	return false, -1
}

// Map f over a list of integer values which then yields
// a list of booleans
// 'map' predefined so use mapMy
func mapMy(f func(int) bool, xs []int) []bool {
	var ys []bool

	for _, x := range xs {
		ys = append(ys, f(x))

	}
	return ys
}

// Filter out all elemements which satisfy the predicate.
func filter(p func(int) bool, xs []int) []int {
	var ys []int

	for _, x := range xs {
		if p(x) {
			ys = append(ys, x)
		}
	}
	return ys
}

func main() {
	b, x := any(func(x int) bool { return x > 1 }, []int{0, 1, 2, 3})

	fmt.Printf("%b %d \n", b, x)

	xs := filter(func(x int) bool { return x > 1 }, []int{0, 1, 2, 3})

	for _, x := range xs {
		fmt.Printf("%d \n", x)
	}

}
~~~~~~~~~~~~~~~~

 

#  Lambda calculus

The lambda calculus represents a minimal core language just consisting
of functions.

Functions are first-class (can appear as arguments and as return values).
Think of first-class objects!

## Lambda calculus: Syntax and semantics

Terms (expressions/programs) in the lambda calculus are defined
by the following (abstract) EBNF syntax.

$$
 t \ \ ::= \ \ x \ \ \mid \ \ \lambda x.t \ \ \mid \ \ t \ t 
$$

where

* $x$ refers to a variable
* $\lambda x.t$ to a lambda abstraction
* $t \ t$ refers to a function application

The above EBNF is ambiguous.
For example, consider expression $\lambda x.x \ x$.
Based on the above EBNF rules there are two possible interpretations.

1. A lambda function with formal parameter $x$ where $x$ is applied to itself
in the function body.

2. The identity function $\lambda x.x$ applied to $x$.


The first interpretation can be explained via the following (parse tree) derivation
$$
t \rightarrow \lambda x.t \rightarrow \lambda x. t \ t \rightarrow \lambda x. x \ t \rightarrow \lambda x.x \ x
$$

whereas for the second derivation we find
$$
t \rightarrow t \ t \rightarrow (\lambda x. t) \ t \rightarrow (\lambda x.x) \ t \rightarrow (\lambda x.x) \ x
$$

In the above derivation, parantheses "(..)" are meta-symbols to highlight the difference among
the two derivations.

In examples, we therefore may make use of parantheses to avoid ambiguities,
for example $(\lambda x.x) \ x$.





The operational semantics of the lambda calculus is defined
via a single rule, commonly referred to as $\beta$-reduction.


$$
 (\lambda x. t_1) \ t_2 \rightarrow [t_2/x]t_1
$$


In $[t_2/x]t_1$, we apply the substitution $[t_2/x]$:
In the body $t_1$, replace each occurence of the formal parameter $x$
  by the argument $t_2$.

Commonly, we refer to $\lambda x.e$ as a lambda-abstraction
and to $\lambda x$ as the lambda-binding.

For example, $(\lambda x. (\lambda y. y)) \ z$ reduces
to $\lambda y.y$.

As it is common in programming languages, we can reuse
the same variable names and apply the common rule that
each use of a variable name refers to the closest binding site.
For example, consider $\lambda x. (\lambda x. x)$ where the
innermost occurence of $x$ refers to the inner lambda-binding.

Via some simply renaming we can always guarantee that 
variables are distinct.
For example, $\lambda x. (\lambda x. x)$
can be renamed to $\lambda x. (\lambda y. y)$.

We generally assume that lambda-bindings always introduce
fresh, distinct variables. 

## Details

### Syntactic sugar 

Function application is left associative.
Hence, the term $(\lambda u.u) \ z \ x$ is a short-hand
for $(((\lambda u.u) \ z) \ x)$.

The body of a lambda abstractions extends to the right as long as possible. Hence, the term $\lambda z. (\lambda u.u) \ z \ x$
is a short-hand for $\lambda z. (((\lambda u.u) \ z) \ x)$.

### Free variables

$fv(t)$ computes all free variables in $t$, i.e. those variables
which are not bound by a lambda abstraction.

The definition by induction over the structure of $t$ is as follows:

* $fv(x) = \{ x \} $
* $fv(\lambda x.t) = fv(t) - \{ x \}$
* $fv(t_1 \ t_2) = fv(t_1) \cup fv(t_2)$


In case of name clashes we must rename bound variables.
Consider $[y \ (\lambda x.x)/x] \lambda y.(\lambda x.x) \ y \ x$:

1. Renaming yields: $[y \ (\lambda v.v)/x] \lambda z. (\lambda u.u) \ z \ x$
2. Substitution without name clashes yields: $\lambda z. (\lambda u.u) \ z(y \ (\lambda v.v))$


### Substitution

\newcommand{\ba}{\begin{array}}
\newcommand{\ea}{\end{array}}
\newcommand{\bda}{\[\ba}
\newcommand{\eda}{\ea\]}

Examples (where $e$ refers to a term/expression)

* $[ e_1/x ] x  =  e_1$
* $[ e_1/x ] y  =  y$ if   $y \not=  x$
* $[ e_1 / x ] e_2 \ e_3  =  ([ e_1 /x ] e_2)  \ ([ e_1 /x ] e_3)$
* $[ e_1 / x ] \lambda y.e_2  =  \lambda y. [ e_1 /x ] e_2$
  if $y \not = x$ and $y\not\in fv(e_1)$


### Evaluation strategies

There are two principles ways how to evaluate terms:

* Innermost first
* Outermost first

#### Innermost 

* Look for a redex innermost, leftmost
* Also called Applicative Order Reduction (AOR)
* Redex = reducible expression of the form 
 `(\x-> ...) e`


*Call-by value (CBV)* = AOR with the exception that we don't evaluate
under "lambda" (i.e. inside function bodies)

#### Outermost

Outermost, leftmost, also called Normal Order Reduction (NOR)

*Call-by name (CBN)* = NOR with the exception that we don't evaluate
under "lambda" (i.e. inside function bodies)

#### Examples

We consider evaluation of $(\lambda f.\lambda x. f \ (f \ x)) ((\lambda x.x) (\lambda x.x))$
where $\rightarrow$ denotes an evaluation step

Call-by-name (CBN)

$(\lambda f.\lambda x. f \ (f \ x)) ((\lambda x.x) (\lambda x.x))$

$\rightarrow \lambda x.(\lambda x.x)(\lambda x.x)((\lambda x.x)(\lambda x.x)x)$

$\rightarrow  \lambda x.(\lambda x.x)((\lambda x.x)x)$

$\rightarrow \lambda x.(\lambda x.x)x$

$\rightarrow  \lambda x.x$

Call-by-value (CBV):

$(\lambda f.\lambda x. f \ (f \ x)) ((\lambda x.x) (\lambda x.x))$

$\rightarrow (\lambda f.\lambda x. f \ (f \ x)) (\lambda x.x)$

$\rightarrow \lambda x.(\lambda x.x) ((\lambda x.x) x)$

$\rightarrow \lambda x.(\lambda x.x)x$

$\rightarrow \lambda x.x$

#### (Non)Termination

There are lambda terms whose evaluation will not terminate:

$(\lambda x. (x \ x)) (\lambda x. (x \ x))$

$\rightarrow (\lambda x. (x \ x)) (\lambda x. (x \ x))$

$\rightarrow (\lambda x. (x \ x)) (\lambda x. (x \ x))$

$\rightarrow ...$

Termination under CBN is more likely than termination under CBV.


Let's abbreviate $(\lambda x. (x \ x)) (\lambda x. (x \ x))$ by $\Omega$.
Then, the lambda term $(\lambda x. \lambda y. x) \Omega$
terminates under CBN but *not* under CBV.


#### Short summary

* CBN seems rather inefficient
* But has plenty of applications
    * More code reuse
    * More declarative code 
    * Infinite, circular data structures
    * ...

More details in some upcoming exercises.

### Further examples

Consider $(\lambda x. x \ y) \ (\lambda x. x) \ (\lambda x. x)$.

Function application is left-associative, therefore, the meaning
of the above lambda term is $((\lambda x. x \ y) \ (\lambda x. x)) \ (\lambda x. x)$.

Let's carry out the evaluation steps.


$((\lambda x. x \ y) \ (\lambda x. x)) \ (\lambda x. x)$

$=_{Renaming} \ \ ((\lambda x_1. x_1 \ y) \ (\lambda x_2. x_2)) \ (\lambda x_3. x_3)$

$\rightarrow \ \ ( (\lambda x_2. x_2) \ y) (\lambda x_3. x_3)$
because $(\lambda x_1. x_1 \ y) \ (\lambda x_2. x_2) \ \rightarrow \ [(\lambda x_2. x_2) / x_1 ] (x_1 \ y) \ = \  (\lambda x_2. x_2) \ y$

$\rightarrow \ \ y \ (\lambda x_3. x_3)$ because $(\lambda x_2. x_2) \ y \ \rightarrow \ [y / x_2] x_2 \ = \ y$

On the other hand, the lambda term $(\lambda x. x \ y) \ ((\lambda x. x) \ (\lambda x. x))$
evaluates to $y$. Briefly, the evaluation steps are

$(\lambda x. x \ y) \ ((\lambda x. x) \ (\lambda x. x))$

$-_{Renaming} \ (\lambda x_1. x_1 \ y) \ ((\lambda x_2. x_2) \ (\lambda x_3. x_3))$

$\rightarrow \ (\lambda x_1. x_1 \ y) \ (\lambda x_3. x_3)$

$\rightarrow \ (\lambda x_3. x_3) \ y$

$\rightarrow \ y$



### Expressiveness/Church Encoding

How expressive is the lambda calculus? As expressive as a Turing machine?

Yes! But how? The lambda calculus looks rather simple.

Indeed, but as we will show next, we can encode data types (booleans, ...), conditional expressions, recursion.

The idea of the Church encoding (named after Alonzo Church):

* Encode behavior not structure

#### Boolean operations

The idea:

* not x = if x then false else true

* x or y = if x then true else y

* x and y = if x then y else false

The actual encoding:

* true = $\lambda x. \lambda y.x$

* false = $\lambda x. \lambda y. y$

* if $e_1$ then $e_2$ else $e_3$ = $e_1  \ e_2 \ e_3$ 

  which written in 'lambda notation' is 

  ite = $\lambda e_1. \lambda e_2. \lambda e_3. e_1 \ e_2 \ e_3$


Example: if true then $e_2$ else $e_3$ equals 
 $(\lambda x. \lambda y. x)  \ e_2  \ e_3$


$(\lambda x. \lambda y. x)  \ e_2  \ e_3$

$\rightarrow e_2$

In detail:

$(\lambda x. \lambda y. x)  \ e_2  \ e_3$

= $((\lambda x. \lambda y. x)  \ e_2)  \ e_3$

by adding parentheses (recall that function application is left associative)

$\rightarrow (\lambda y.e_2) \ e_3$

$\rightarrow e_2$

##### Boolean operations example

We evaluate  $ite \ true \ x \ y$.
The expect result is $x$.
Let's see!

1. Replace short-hands by actual definitions.

$ite \ true \ x \ y = (\lambda x. \lambda y. \lambda z. x \ y \ z) \ (\lambda x. \lambda y. x) \ x \ y$

2. Rename variables such that all bound variables are distinct

$(\lambda x_1. \lambda y_1. \lambda z_1. x_1 \ y_1 \ z_1) \ (\lambda x_2. \lambda y_2. x_2) \ x \ y$

3. Introduce parantheses

$(((\lambda x_1. \lambda y_1. \lambda z_1. (x_1 \ y_1) \ z_1) \ (\lambda x_2. \lambda y_2. x_2)) \ x) \ y$

4. Carry out evaluation steps

$(((\lambda x_1. \lambda y_1. \lambda z_1. (x_1 \ y_1) \ z_1) \ (\lambda x_2. \lambda y_2. x_2)) \ x) \ y$

$\rightarrow (([(\lambda x_2. \lambda y_2. x_2) / x_1] (\lambda y_1. \lambda z_1. (x_1 \ y_1) \ z_1)) \ x) \ y$

$= (((\lambda y_1. \lambda z_1. ((\lambda x_2. \lambda y_2. x_2) \ y_1) \ z_1)) \ x) \ y$

Several redexes. We choose the outermost redex

$\rightarrow ([x / y_1] (\lambda z_1. ((\lambda x_2. \lambda y_2. x_2) \ y_1) \ z_1)) \ y$

$= (\lambda z_1. ((\lambda x_2. \lambda y_2. x_2) \ x) \ z_1) \ y$

$\rightarrow [y / z_1] (((\lambda x_2. \lambda y_2. x_2) \ x) \ z_1)$

$= ((\lambda x_2. \lambda y_2. x_2) \ x) \ y$

$\rightarrow ([x / x_2] (\lambda y_2. x_2)) \ y$

$= (\lambda y_2. x) \ y$

$\rightarrow [y / y_2] x$

$= x$

#### Recursion

What about recursion?

~~~~~~~~{.haskell}
     factorial n = if n == 0 then 1
                   else n * (factorial (n-1))
~~~~~~~~~~~~~~~~~


Idea:

* Fixpointcombinator
    * $Y  =  \lambda F.(\lambda y.F \ (y \ y)) \ (\lambda x.F \ (x \ x))$
    * We find that $Y \ F = F \ (Y F)$


* $Fac = \lambda fac. \lambda n. if \  (n==0) \ \ then \ 1 \ else \ n*(fac \ (n-1))$ 

* $factorial = Y \ Fac$

This works!

$factorial \ 1$

$\rightarrow_{def} \  (Y \ Fac) \ 1$

$\rightarrow_{fix} \  (Fac \ (Y \ Fac)) \ 1$

$\rightarrow_{def} \ 
     ((\lambda fac. \lambda n. if \ (n == 0) \ then \ 1 \ else \ n*(fac \(n-1)))
       \ (Y \Fac)) \ 1$

$\rightarrow \ 
     (\lambda n. if \ (n==0) \ then \ 1 \ else \ n*((Y \ Fac) \ (n-1))) \ 1$

$\rightarrow \
     1 * ((Y \ Fac) \ 0)$

$\rightarrow_{fix} \  1 * ((Fac \ (Y \ Fac)) \ 0)$

$\rightarrow \ 1 * ((\lambda n. if \ (n == 0) \ then \ 1 \ else \ n*((Y \ Fac) \ (n-1))) \ 0)$

$\rightarrow \ 1 * 1$

$\rightarrow \ 1$

More encodings (pairs, natural numbers) are possible.
See [Church encoding](https://en.wikipedia.org/wiki/Church_encoding).











