# Jasmin Assembly Language and JVM Continued

## Arithmetic Instructions
* add: iadd, fadd
* subtract: isub, fsub
* multiply: imul, fmul
* divide: idiv, fdiv
* negative: ineg, fneg
* comparison: fcmpg, fcmpl
* ...

## Load and Store Instructions
* Loading a local variable into the operand stack:
```
iload iload_0, ..., iload_3
fload fload_0, ..., fload_3
aload aload_0, ..., aload_3 // for array and object refs
iaload                      // load from an int array
faload                      // load from an float array
```
* Storing a value from the operand stack into the local variable:
```
istore istore_0, ..., istore_3
fstore fstore_0, ..., fstore_3
astore astore_0, ..., astore_3  // for array and object refs
iastore                         // store from an int array
fastore                         // store from an float array
```

* Load a constant into the operand stack
```
bipush, sipush, ldc, iconst_m1, iconst_0, ..., iconst_5,
fconst_0, ..., fconst2
```

### Example: Load and Store
1. Integral Expression:
$$1 + 100 * 200 + 40000$$

2. Jasmin Code:
```
    iconst_1
    bipush 100
    sipush 200
    imul
    iadd
    ldc 40000
    iadd
```
3. bipush: $-2^7$ to $2^7 - 1$
4. sipush: $-2^{15}$ to $2^{15} - 1$
5. ldc val, where val is an int, float or string

### Example: Load and Store for Arrays
1. Array Operations:
``` java
a[0] = 100;
i = a[0];
```
2. Jasmin Code:
```
    // a[0] = 100;
    aload_1
    iconst_0
    bipush 100
    iastore

    // i = a[0]
    aload_1
    iconst_0
    iload
    istore_2
```

## Control Transfer Instructions
* Unconditional: `goto`
* Instructions on int:
```
    ifeq, ifne, ifle, iflt, ifne, ifge
    if_icmpeq, if_icmpne, if_icmple
    if_icmplt, if_icmpge, if_icmpgt
```
* For floating-point operands, use first
    * `fcmpg` or `fcmpl`
    * and then
    * `ifeq ifne ifle iflt ifne ifge`

## Type Conversion Instructions
1. Source:
```java
    int i = 1;      // index 1
    float f = i;    // index 2
```

2. Jasmin code:
```
    iconst_1
    istore_1
    iload_1
    i2f
    fstore 2
```
3. Only `i2f` is used in the VC compiler
4. Others are not used 
    * i.e. `i2c, i2b, f2i`

## Operand Stack Management Instructions
* Instructions:
    * dup: duplicate the stack top operand
    * pop: remove the stack top operand
    * swap: swap the top two operands
    * others: pop2, dup2, etc
* Only the first two will be used in the VC compiler:
    * dup: for translating `a = b = ...`
    * pop: for translating expression statements such as `1;`

### Example: `dup` in translating cascading assignment expression
1. Assignment expressions:
```java
    int i;
    int j;
    int k;
    i = j = k = 1;
```

2. Jasmin code:
```
    iconst_1
    dup
    istore_3
    dup
    istore_2
    istore_1
```

## Method Invocation Instructions
* Method calls:
```
    invokestatic
    invokevirtual
    invokespecial   // also known as invokenonvirutal
        -- the instance initialisation method <init>
        -- a private method of "this"
        -- a method in a super class of "this"
    invokeinterface
```
* Method returns:
```
    return
    ireturn
    freturn
    ...
```
### Syntax for Method Invocation Instructions
* The syntax for invokestatic/virtual/special
    * `invokexx method-spec`
    * where method-spec consists of a classname, a method name and a descriptor
* To call invokestatic
    1. Push all the arguments onto the stack
    2. Push the static invocation instruction
    3. Result will be ontop of the stack
* To call invokevirtual and invokespecial
    1. push an objref onto the stack
    2. same as above

### Example: Static Method Invocation
1. Met1.java
```java
public class Met1{
    static int add(int i1, int i2) {
        return i1 + i2;
    }
    
    public static void main(String argv[]) {
        add(1, 2);
    }
}
```

2. Met1.j
```j
.method static add(II)I .limit stack 2 .limit locals 2
    iload_0
    iload_1
    iadd
    ireturn
.end_method
.method public static main([Ljava/lang/String;)V .limit stack 2 .limit locals 1
    iconst_1
    iconst_2
    invokestatic Met1/add(II)I
    pop
    return
.end method
```

### Example: Instance Method Invocation
1. Met2.java
```java
public class Met2{
    static int add(int i1, int i2) {
        return i1 + i2;
    }
    
    public static void main(String argv[]) {
        Met2 m = new Met2();
        m.add(1, 2);
    }
}
```

2. Met2.j
```j
.method static add(II)I .limit stack 2 .limit locals 3
    iload_1
    iload_2
    iadd
    ireturn
.end_method
.method public static main([Ljava/lang/String;)V .limit stack 2 .limit locals 1
    new Met2
    dup
    invokespecial Met2/<init>() V
    astore_1
    iconst_1
    iconst_2
    invokevirtual Met2.add(II)I
    return
.end method
```

### Example: Polymorphism, invokevirtual and invokespecial
```java
public class Fruit {
    public static void main(String argv[]) {
        Apple apple = new Apple();
        Fruit fruit = apple;
        fruit.whoAmI();
    }

    void whoAmI() {
        System.out.println("This is a fruit.");
    }
}

class Apple extends Fruit {
    @Override
    void whoAmI() {
        System.out.println("This is an apple");
        super.whoAmI();
    }
}
```

* invokevirtual looks for the correct pointer to the correct dynamic type then invoke the method in that subclass
* invokespecial ignores the dynamic type and just invokes the method in the super class.

## Object Creation and Manipulation
* Create a new class instance: new
* Access field of classes: getstatic, putstatic, getfield, putfield
* Create a new array: newarray, anewarray, multianewarray
* Load an array component onto the operand stack: iaload, faload, baload, caload, ..
* Store a value from the operand stack as an array component: iastore, fastore, bastore, castore

### Syntax for field instructions
* Syntax:
    * `putstatic/getstatic field-spec type-descriptor`
    \
    where field-spec consists of a classname followed by a field name.
* Example:
```
getstatic java/lang/System/out Ljava/io/PrintStream;
getstatic StaticField/i I
```

### Array Creations
* Java Statements
```java
int a[] = new int[10];
a[1] = a[2] + 1;
```

* Bytecode:
```
    bipush 10
    newarray int
    astore_1

    aload_1
    iconst_1
    aload_1
    iconst_2
    iaload
    iconst_1
    iadd
    iastore
```

