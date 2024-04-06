# Java Byte Code Generation 

## Code Template
* $[[X]]:$ the code generated for construct $X$
* Code template: a specification of $[[X]]$ in terms of the codes ofr its syntatic components
* A code template specifies the translation of a construct independently of the context in which it is used
    * Compiled code always executes in some context
    * Optimisation is the art of capitalising on context
    * Lack of context => fully general (i.e. slow) code
* Thus, ineffecient code may be generated; it can be optimised later by compiler backend

### Example 1
```java
int i = 2;
int j = 4;
int gcd(int a, int b) {
    if (b == 0)
        return a;
    else
        return gcd(b, a - (a/b) *b);
}
int main() {
    putIntLn(gcd(i, j));
    return 0; // optional in VC or C/C++
}
```
should be interpreted by VC as
```java
public class gcd {
    static int i = 2;
    static int j = 4;

    public gcd() { } // the default constructor
        int gcd(int a, int b) {
        if (b == 0)
            return a;
        else
            return gcd(b, a - (a/b) *b);
    }

    void main(String argv[]) {
        gcd vc$;
        vc$ = new gcd();
        System.putIntLn(vc$.gcd(i, j));
        return;
    }
}
```

* `int main()` is assumed to be: \
    `public static void main(String argv[]) { ... }`
    * visitFuncDec: a return is always emitted just in case no "return expr" was present in the main function of a VC program
    * visitReturnStmt: emit a RETURN rather than IRETURN even if a return statement, e.g. "return expr" is present in the main of a VC program
* All VC functions are assumed to be instance methods with the package access
* All global variables are assumed to be static field variables with the package access
* All built-in VC functions are static

## Expressions

### Integer Literals
* Code Template: $[[IntLiteral]]:$ `emitICONST(IntLiteral.value)`
* Visitor method: 
    ```java
    visitIntLiteral(IntLiteral ast, Object o) {
        Frame frame = (Frame) o;
        emitICONST(Float.parseInt(ast.spelling));
        ...
        return null;
    }
    ```
### Arithmetic Expression - Addition
* Code Template:
$[[E_1 i + E_2]]:$ $[[E_1]]$ $[[E_2]]$  `emit("iadd")`
* Visitor Method:
```java
public Object visitBinaryExpr(BinaryExpr ast, Object o) {
    Frame frame = (Frame) o;
    String op = ast.O.spelling;
    ast.E1.visit(this, o);
    ast.E2.visit(this, o);
    ...
    else if (op.equals("i+")) {
    emit(JVM.IADD);
    ...
}
```

### visitFuncDecl: Frame Objects
* A new frame object created each time visitFuncDecl is called
* 
```java
public Object visitFuncDecl(FuncDecl ast, Object o) {
    ...
    frame = new Frame(true) // for main
    frame = new Frame(false) // otherwise
    ...
}
```
* The fram object passed as the 2nd arg and available at all child nodes
* The constructor of the class Frame:
```java
public Frame(boolean _main) {
    this._main = _main;
    label = 0;
    localVarIndex = 0;
    currentStackSize = 0;
    maximumStackSize = 0;
    conStack = new Stack<String>();
    brkStack = new Stack<String>();
    scopeStart = new Stack<String>();
    scopeEnd = new Stack<String>();
}
```

## End of Notes
* This lecture was kind of straight forward and none of the theory was new to me so I might just end it here.