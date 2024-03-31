# Static Semantics Continued

## Scoping continued
### Static and Dynamic Scopes
* **Static Scope:** 
    * The scope of a declaration is determined by looking at the program text, without running it
* **Dynamic Scope:**
    * The scope of a declaration is determined at run-time
    * Usually, an applied occurrence of an identifier is associated with the declaration in the most recent caller in the execution path
    * Dynamically scoped languagesL APL, SNOBAL, earlier versions of LISP, Perl

```c
int i = 3;
void f() {
    int i = 2;
    g():
}

void g() {
    printIntLn(i);
}

int main() {
    int i = 1;
    g();    // prints 1
    f();    // prints 2
}
```

## Static Semantics: Type Checking

### Type Checking
* Data type: set of values plus set of operations on the values
* Typical Checks:
    * Type checks: expressions are well typed using **type rules**
    * Flow-of-control check: break & continue are contained within a loop, etc
    * Uniqueness checks: The labels in a switch statement are distinct, etc
* **Type rules:** the rules to infer the type of each language construct and decide whether or not the construct has a valid type
* **Type checking:** applying the language's **type rules** to infer the type of each construct and comparing that type with the expected type and throws an error if the types do not match up

### Type Checking in VC
* VC is statically type checked
* Lack of structured types means checks are simple:
    * Expressions: an operator applied to compatible operands
    * Statements:
        * break and continue must be contained in a loop
        * The type of the expression in a return statement in a function must be assignment-compatible with the result type of the function
        * unreachable statements
        * Every function whose result type is int or float must have a return statement (optional)

### Type Checking Expressions in VC
* The type of a binary operator would have a type of T<sub>1</sub> X T <sub>2</sub> -> T <sub>3</sub>
    * For example the type of the function `int f(int i, float f)` would have a type of `int x float -> int`
* The compiler infers that the expression *E* has some type *T* or *E* is ill-typed. If *E* occurs in a context where *T'* is expected, then the compiler checks if *T* is equivalent to *T'*


### Bottom-Up Computation of type in an Expression AST
* Literal: its type is immediately known
* Identifier: is type obtained from the inherited attribute `Decl` which is associated in every identifier node
* In binary applications ...
    * If we have an Operation *O* that has the type *T*<sub>1</sub> x *T*<sub>2</sub> -> *T*<sub>3</sub>
    * An expression *E*<sub>1</sub> *O* *E*<sub>2</sub> can be inferred to have a type of *T*<sub>3</sub> iff *E*<sub>1</sub> is equivalent to *T*<sub>1</sub> and *E*<sub>2</sub> is equivalent to *T*<sub>2</sub>. Otherwise, an error is thrown
    * N-ary operator applications are dealt with similarly

### Type Coercions
* There are two types of operations at the hardware level on say, _
    * integer additions hwne both operands are integers
    * floating point addition when both operands are reals
* **Type coercion:** the compiler implicitly converts int to float whenever necessary, when an expression is evaluated
* Each overloaded operator is associated two non-overloaded operator: one for integer operation and the other for floating-point operation

### Error Detection, Reporting and Recovery
* Detection: based on type rules
* Reporting: prints meaningful error messages
* Recovery: continue checking types in the presence of errors:
    * Must avoid a cascade of spurious errors
    * Ill-typed expression given the `StdEnvironment.errorType` if a type canot by inferred due to the presence of an error
    * Do not need to report an error if any of its sub-expression has a type of `StdEnvironment.errorType`