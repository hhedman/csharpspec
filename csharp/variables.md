# Variables

Variables represent storage locations. Every variable has a type that determines what values can be stored in the variable. C# is a type-safe language, and the C# compiler guarantees that values stored in variables are always of the appropriate type. The value of a variable can be changed through assignment or through use of the `++` and ```` operators.

A variable must be *__definitely assigned__* (§5.3) before its value can be obtained.

As described in the following sections, variables are either *__initially assigned__* or *__initially unassigned__*. An initially assigned variable has a well-defined initial value and is always considered definitely assigned. An initially unassigned variable has no initial value. For an initially unassigned variable to be considered definitely assigned at a certain location, an assignment to the variable must occur in every possible execution path leading to that location.

## Variable categories

C# defines seven categories of variables: static variables, instance variables, array elements, value parameters, reference parameters, output parameters, and local variables. The sections that follow describe each of these categories.

In the example

```csharp
class A
{
    public static int x;
    int y;

    void F(int[] v, int a, ref int b, out int c) {
        int i = 1;
        c = a + b++;
    }
}
```

`x` is a static variable, `y` is an instance variable, `v[0]` is an array element, `a` is a value parameter, `b` is a reference parameter, `c` is an output parameter, and `i` is a local variable.

### Static variables

A field declared with the `static` modifier is called a *__static variable__*. A static variable comes into existence before execution of the static constructor (§10.12) for its containing type, and ceases to exist when the associated application domain ceases to exist.

The initial value of a static variable is the default value (§5.2) of the variable's type.

For purposes of definite assignment checking, a static variable is considered initially assigned.

### Instance variables

A field declared without the `static` modifier is called an *__instance variable__*.

#### Instance variables in classes

An instance variable of a class comes into existence when a new instance of that class is created, and ceases to exist when there are no references to that instance and the instance's destructor (if any) has executed.

The initial value of an instance variable of a class is the default value (§5.2) of the variable's type.

For the purpose of definite assignment checking, an instance variable of a class is considered initially assigned.

#### Instance variables in structs

An instance variable of a struct has exactly the same lifetime as the struct variable to which it belongs. In other words, when a variable of a struct type comes into existence or ceases to exist, so too do the instance variables of the struct.

The initial assignment state of an instance variable of a struct is the same as that of the containing struct variable. In other words, when a struct variable is considered initially assigned, so too are its instance variables, and when a struct variable is considered initially unassigned, its instance variables are likewise unassigned.

### Array elements

The elements of an array come into existence when an array instance is created, and cease to exist when there are no references to that array instance.

The initial value of each of the elements of an array is the default value (§5.2) of the type of the array elements.

For the purpose of definite assignment checking, an array element is considered initially assigned.

### Value parameters

A parameter declared without a `ref` or `out` modifier is a *__value parameter__*.

A value parameter comes into existence upon invocation of the function member (method, instance constructor, accessor, or operator) or anonymous function to which the parameter belongs, and is initialized with the value of the argument given in the invocation. A value parameter normally ceases to exist upon return of the function member or anonymous function. However, if the value parameter is captured by an anonymous function (§7.15), its life time extends at least until the delegate or expression tree created from that anonymous function is eligible for garbage collection.

For the purpose of definite assignment checking, a value parameter is considered initially assigned.

### Reference parameters

A parameter declared with a `ref` modifier is a *__reference parameter__*.

A reference parameter does not create a new storage location. Instead, a reference parameter represents the same storage location as the variable given as the argument in the function member or anonymous function invocation. Thus, the value of a reference parameter is always the same as the underlying variable.

The following definite assignment rules apply to reference parameters. Note the different rules for output parameters described in §5.1.6.

-  A variable must be definitely assigned (§5.3) before it can be passed as a reference parameter in a function member or delegate invocation.
-  Within a function member or anonymous function, a reference parameter is considered initially assigned.

Within an instance method or instance accessor of a struct type, the `this` keyword behaves exactly as a reference parameter of the struct type (§7.6.7).

### Output parameters

A parameter declared with an `out` modifier is an *__output parameter__*.

An output parameter does not create a new storage location. Instead, an output parameter represents the same storage location as the variable given as the argument in the function member or delegate invocation. Thus, the value of an output parameter is always the same as the underlying variable.

The following definite assignment rules apply to output parameters. Note the different rules for reference parameters described in §5.1.5.

-  A variable need not be definitely assigned before it can be passed as an output parameter in a function member or delegate invocation.
-  Following the normal completion of a function member or delegate invocation, each variable that was passed as an output parameter is considered assigned in that execution path.
-  Within a function member or anonymous function, an output parameter is considered initially unassigned.
-  Every output parameter of a function member or anonymous function must be definitely assigned (§5.3) before the function member or anonymous function returns normally.

Within an instance constructor of a struct type, the `this` keyword behaves exactly as an output parameter of the struct type (§7.6.7).

### Local variables

A *__local variable__* is declared by a *local-variable-declaration*, which may occur in a *block*, a *for-statement*, a *switch-statement* or a *using-statement*; or by a *foreach-statement* or a *specific-catch-clause* for a *try-statement*.

The lifetime of a local variable is the portion of program execution during which storage is guaranteed to be reserved for it. This lifetime extends at least from entry into the *block*, *for-statement*, *switch-statement*, *using-statement*, *foreach-statement*, or *specific-catch-clause* with which it is associated, until execution of that *block*, *for-statement*, *switch-statement*, *using-statement*, *foreach-statement*, or *specific-catch-clause* ends in any way. (Entering an enclosed *block* or calling a method suspends, but does not end, execution of the current *block*, *for-statement*, *switch-statement*, *using-statement*, *foreach-statement*, or *specific-catch-clause*.) If the local variable is captured by an anonymous function (§7.15.5.1), its lifetime extends at least until the delegate or expression tree created from the anonymous function, along with any other objects that come to reference the captured variable, are eligible for garbage collection.

If the parent *block*, *for-statement*, *switch-statement*, *using-statement*, *foreach-statement*, or *specific-catch-clause* is entered recursively, a new instance of the local variable is created each time, and its *local-variable-initializer*, if any, is evaluated each time.

A local variable introduced by a *local-variable-declaration* is not automatically initialized and thus has no default value. For the purpose of definite assignment checking, a local variable introduced by a *local-variable-declaration* is considered initially unassigned. A *local-variable-declaration* may include a *local-variable-initializer*, in which case the variable is considered definitely assigned only after the initializing expression (§5.3.3.4).

Within the scope of a local variableintroduced by a *local-variable-declaration*, it is a compile-time error to refer to that local variable in a textual position that precedes its *local-variable-declarator*. If the local variable declaration is implicit (§8.5.1), it is also an error to refer to the variable within its *local-variable-declarator*.

A local variable introduced by a *foreach-statement* or a *specific-catch-clause* is considered definitely assigned in its entire scope.

The actual lifetime of a local variable is implementation-dependent. For example, a compiler might statically determine that a local variable in a block is only used for a small portion of that block. Using this analysis, the compiler could generate code that results in the variable's storage having a shorter lifetime than its containing block.

The storage referred to by a local reference variable is reclaimed independently of the lifetime of that local reference variable (§3.9).

## Default values

The following categories of variables are automatically initialized to their default values:

-  Static variables.
-  Instance variables of class instances.
-  Array elements.

The default value of a variable depends on the type of the variable and is determined as follows:

-  For a variable of a *value-type*, the default value is the same as the value computed by the *value-type*'s default constructor (§4.1.2).
-  For a variable of a *reference-type*, the default value is `null`.

Initialization to default values is typically done by having the memory manager or garbage collector initialize memory to all-bits-zero before it is allocated for use. For this reason, it is convenient to use all-bits-zero to represent the null reference.

## Definite assignment

At a given location in the executable code of a function member, a variable is said to be *__definitely assigned__* if the compiler can prove, by a particular static flow analysis (§5.3.3), that the variable has been automatically initialized or has been the target of at least one assignment. Informally stated, the rules of definite assignment are:

-  An initially assigned variable (§5.3.1) is always considered definitely assigned.
-  An initially unassigned variable (§5.3.2) is considered definitely assigned at a given location if all possible execution paths leading to that location contain at least one of the following:

A simple assignment (§7.17.1) in which the variable is the left operand.

An invocation expression (§7.6.5) or object creation expression (§7.6.10.1) that passes the variable as an output parameter.

For a local variable, a local variable declaration (§8.5.1) that includes a variable initializer.

The formal specification underlying the above informal rules is described in §5.3.1, §5.3.2, and §5.3.3.

The definite assignment states of instance variables of a *struct-type* variable are tracked individually as well as collectively. In additional to the rules above, the following rules apply to *struct-type* variables and their instance variables:

-  An instance variable is considered definitely assigned if its containing *struct-type* variable is considered definitely assigned.
-  A *struct-type* variable is considered definitely assigned if each of its instance variables is considered definitely assigned.

Definite assignment is a requirement in the following contexts:

-  A variable must be definitely assigned at each location where its value is obtained. This ensures that undefined values never occur. The occurrence of a variable in an expression is considered to obtain the value of the variable, except when

the variable is the left operand of a simple assignment,

the variable is passed as an output parameter, or

the variable is a *struct-type* variable and occurs as the left operand of a member access.

-  A variable must be definitely assigned at each location where it is passed as a reference parameter. This ensures that the function member being invoked can consider the reference parameter initially assigned.
-  All output parameters of a function member must be definitely assigned at each location where the function member returns (through a `return` statement or through execution reaching the end of the function member body). This ensures that function members do not return undefined values in output parameters, thus enabling the compiler to consider a function member invocation that takes a variable as an output parameter equivalent to an assignment to the variable.
-  The `this` variable of a *struct-type* instance constructor must be definitely assigned at each location where that instance constructor returns.

### Initially assigned variables

The following categories of variables are classified as initially assigned:

-  Static variables.
-  Instance variables of class instances.
-  Instance variables of initially assigned struct variables.
-  Array elements.
-  Value parameters.
-  Reference parameters.
-  Variables declared in a `catch` clause or a `foreach` statement.

### Initially unassigned variables

The following categories of variables are classified as initially unassigned:

-  Instance variables of initially unassigned struct variables.
-  Output parameters, including the `this` variable of struct instance constructors.
-  Local variables, except those declared in a `catch` clause or a `foreach` statement.

### Precise rules for determining definite assignment

In order to determine that each used variable is definitely assigned, the compiler must use a process that is equivalent to the one described in this section.

The compiler processes the body of each function member that has one or more initially unassigned variables. For each initially unassigned variable *v*, the compiler determines a *__definite assignment state__* for *v* at each of the following points in the function member:

-  At the beginning of each statement
-  At the end point (§8.1) of each statement
-  On each arc which transfers control to another statement or to the end point of a statement
-  At the beginning of each expression
-  At the end of each expression

The definite assignment state of *v* can be either:

-  Definitely assigned. This indicates that on all possible control flows to this point, *v* has been assigned a value.
-  Not definitely assigned. For the state of a variable at the end of an expression of type `bool`, the state of a variable that isn't definitely assigned may (but doesn't necessarily) fall into one of the following sub-states:

Definitely assigned after true expression. This state indicates that *v* is definitely assigned if the boolean expression evaluated as true, but is not necessarily assigned if the boolean expression evaluated as false.

Definitely assigned after false expression. This state indicates that *v* is definitely assigned if the boolean expression evaluated as false, but is not necessarily assigned if the boolean expression evaluated as true.

The following rules govern how the state of a variable *v* is determined at each location.

#### General rules for statements

-  *v *is not definitely assigned at the beginning of a function member body.
-  *v* is definitely assigned at the beginning of any unreachable statement.
-  The definite assignment state of *v* at the beginning of any other statement is determined by checking the definite assignment state of *v* on all control flow transfers that target the beginning of that statement. If (and only if) *v* is definitely assigned on all such control flow transfers, then *v* is definitely assigned at the beginning of the statement. The set of possible control flow transfers is determined in the same way as for checking statement reachability (§8.1).
-  The definite assignment state of *v* at the end point of a block, `checked`, `unchecked`, `if`, `while`, `do`, `for`, `foreach`, `lock`, `using`, or `switch` statement is determined by checking the definite assignment state of *v* on all control flow transfers that target the end point of that statement. If *v* is definitely assigned on all such control flow transfers, then *v* is definitely assigned at the end point of the statement. Otherwise; *v* is not definitely assigned at the end point of the statement. The set of possible control flow transfers is determined in the same way as for checking statement reachability (§8.1).

#### Block statements, checked, and unchecked statements

The definite assignment state of *v* on the control transfer to the first statement of the statement list in the block (or to the end point of the block, if the statement list is empty) is the same as the definite assignment statement of *v* before the block, `checked`, or `unchecked` statement.

#### Expression statements

For an expression statement *stmt* that consists of the expression *expr*:

-  *v* has the same definite assignment state at the beginning of *expr* as at the beginning of *stmt*.
-  If *v* if definitely assigned at the end of *expr*, it is definitely assigned at the end point of *stmt*; otherwise; it is not definitely assigned at the end point of *stmt*.

#### Declaration statements

-  If *stmt* is a declaration statement without initializers, then *v* has the same definite assignment state at the end point of *stmt* as at the beginning of *stmt*.
-  If *stmt* is a declaration statement with initializers, then the definite assignment state for *v* is determined as if *stmt* were a statement list, with one assignment statement for each declaration with an initializer (in the order of declaration).

#### If statements

For an `if` statement *stmt* of the form:

```csharp
if ( *expr* ) *then-stmt* else *else-stmt*
```

-  *v* has the same definite assignment state at the beginning of *expr* as at the beginning of *stmt*.
-  If *v* is definitely assigned at the end of *expr*, then it is definitely assigned on the control flow transfer to *then-stmt* and to either *else-stmt* or to the end-point of *stmt* if there is no else clause.
-  If *v* has the state "definitely assigned after true expression" at the end of *expr*, then it is definitely assigned on the control flow transfer to *then-stmt*, and not definitely assigned on the control flow transfer to either *else-stmt* or to the end-point of *stmt* if there is no else clause.
-  If *v* has the state "definitely assigned after false expression" at the end of *expr*, then it is definitely assigned on the control flow transfer to *else-stmt*, and not definitely assigned on the control flow transfer to *then-stmt*. It is definitely assigned at the end-point of *stmt* if and only if it is definitely assigned at the end-point of *then-stmt*.
-  Otherwise, *v* is considered not definitely assigned on the control flow transfer to either the *then-stmt* or *else-stmt*, or to the end-point of *stmt* if there is no else clause.

#### Switch statements

In a `switch` statement *stmt* with a controlling expression *expr*:

-  The definite assignment state of *v* at the beginning of *expr* is the same as the state of *v* at the beginning of *stmt*.
-  The definite assignment state of *v* on the control flow transfer to a reachable switch block statement list is the same as the definite assignment state of *v* at the end of *expr*.

#### While statements

For a `while` statement *stmt* of the form:

```csharp
while ( *expr* ) *while-body*
```

-  *v* has the same definite assignment state at the beginning of *expr *as at the beginning of *stmt*.
-  If *v* is definitely assigned at the end of *expr*, then it is definitely assigned on the control flow transfer to *while-body* and to the end point of *stmt*.
-  If *v* has the state "definitely assigned after true expression" at the end of *expr*, then it is definitely assigned on the control flow transfer to *while-body*, but not definitely assigned at the end-point of *stmt*.
-  If *v* has the state "definitely assigned after false expression" at the end of *expr*, then it is definitely assigned on the control flow transfer to the end point of *stmt*, but not definitely assigned on the control flow transfer to *while-body*.

#### Do statements

For a `do` statement *stmt* of the form:

```csharp
do *do-body* while ( *expr* ) ;
```

-  *v* has the same definite assignment state on the control flow transfer from the beginning of *stmt* to *do-body* as at the beginning of *stmt*.
-  *v* has the same definite assignment state at the beginning of *expr* as at the end point of *do-body*.
-  If *v* is definitely assigned at the end of *expr*, then it is definitely assigned on the control flow transfer to the end point of *stmt*.
-  If *v* has the state "definitely assigned after false expression" at the end of *expr*, then it is definitely assigned on the control flow transfer to the end point of *stmt*.

#### For statements

Definite assignment checking for a `for` statement of the form:

```csharp
for ( *for-initializer* ; *for-condition* ; *for-iterator* ) *embedded-statement*
```

is done as if the statement were written:

```csharp
{
*for-initializer* ;
    while ( *for-condition* ) {
*embedded-statement* ;
*for-iterator* ;
    }
}
```

If the *for-condition* is omitted from the `for` statement, then evaluation of definite assignment proceeds as if *for-condition* were replaced with `true` in the above expansion.

#### Break, continue, and goto statements

The definite assignment state of *v* on the control flow transfer caused by a `break`, `continue`, or `goto` statement is the same as the definite assignment state of *v* at the beginning of the statement.

#### Throw statements

For a statement *stmt* of the form

```csharp
throw *expr* ;
```

The definite assignment state of *v* at the beginning of *expr* is the same as the definite assignment state of *v* at the beginning of *stmt*.

#### Return statements

For a statement *stmt* of the form

```csharp
return *expr* ;
```

-  The definite assignment state of *v* at the beginning of *expr* is the same as the definite assignment state of *v* at the beginning of *stmt*.
-  If *v* is an output parameter, then it must be definitely assigned either:

after *expr*

or at the end of the `finally` block of a `try`-`finally` or `try`-`catch`-`finally` that encloses the `return` statement.

For a statement stmt of the form:

```csharp
return ;
```

-  If *v* is an output parameter, then it must be definitely assigned either:

before *stmt*

or at the end of the `finally` block of a `try`-`finally` or `try`-`catch`-`finally` that encloses the `return` statement.

#### Try-catch statements

For a statement *stmt* of the form:

```csharp
try *try-block*
catch(...) *catch-block-1*
...
catch(...) *catch-block-n*
```

-  The definite assignment state of *v* at the beginning of *try-block* is the same as the definite assignment state of *v* at the beginning of *stmt*.
-  The definite assignment state of *v* at the beginning of *catch-block-i* (for any *i*) is the same as the definite assignment state of *v* at the beginning of *stmt*.
-  The definite assignment state of *v* at the end-point of *stmt* is definitely assigned if (and only if) *v* is definitely assigned at the end-point of *try-block* and every *catch-block-i* (for every *i* from 1 to *n*).

#### Try-finally statements

For a `try` statement *stmt* of the form:

```csharp
try *try-block* finally *finally-block*
```

-  The definite assignment state of *v* at the beginning of *try-block* is the same as the definite assignment state of *v* at the beginning of *stmt*.
-  The definite assignment state of *v* at the beginning of *finally-block* is the same as the definite assignment state of *v* at the beginning of *stmt*.
-  The definite assignment state of *v* at the end-point of *stmt* is definitely assigned if (and only if) at least one of the following is true:

*v* is definitely assigned at the end-point of *try-block*

*v* is definitely assigned at the end-point of *finally-block*

If a control flow transfer (for example, a `goto` statement) is made that begins within *try-block*, and ends outside of *try-block*, then *v* is also considered definitely assigned on that control flow transfer if *v* is definitely assigned at the end-point of *finally-block*. (This is not an only if—if *v* is definitely assigned for another reason on this control flow transfer, then it is still considered definitely assigned.)

#### Try-catch-finally statements

Definite assignment analysis for a `try`-`catch`-`finally` statement of the form:

```csharp
try *try-block *
catch(...) *catch-block-1*
...
catch(...) *catch-block-n*
finally *finally-block*
```

is done as if the statement were a `try`-`finally` statement enclosing a `try`-`catch` statement:

```csharp
try {
    try *try-block*
    catch(...) *catch-block-1*
    ...
    catch(...) *catch-block-n*
}
finally *finally-block*
```

The following example demonstrates how the different blocks of a `try` statement (§8.10) affect definite assignment.

```csharp
class A
{
    static void F() {
        int i, j;
        try {
            goto LABEL;
            // neither i nor j definitely assigned
            i = 1;
            // i definitely assigned
        }

        catch {
            // neither i nor j definitely assigned
            i = 3;
            // i definitely assigned
        }

        finally {
            // neither i nor j definitely assigned
            j = 5;
            // j definitely assigned
        }
        // i and j definitely assigned
      LABEL:;
        // j definitely assigned

    }
}
```

#### Foreach statements

For a `foreach` statement *stmt* of the form:

```csharp
foreach ( *type identifier* in *expr* ) *embedded-statement*
```

-  The definite assignment state of *v* at the beginning of *expr* is the same as the state of *v* at the beginning of *stmt*.
-  The definite assignment state of *v* on the control flow transfer to *embedded-statement* or to the end point of *stmt* is the same as the state of *v* at the end of *expr*.

#### Using statements

For a `using` statement *stmt *of the form:

```csharp
using ( *resource-acquisition* ) *embedded-statement*
```

-  The definite assignment state of *v* at the beginning of *resource-acquisition* is the same as the state of *v* at the beginning of *stmt*.
-  The definite assignment state of *v* on the control flow transfer to *embedded-statement* is the same as the state of *v* at the end of *resource-acquisition*.

#### Lock statements

For a `lock` statement *stmt *of the form:

```csharp
lock ( *expr* ) *embedded-statement*
```

-  The definite assignment state of *v* at the beginning of *expr* is the same as the state of *v* at the beginning of *stmt*.
-  The definite assignment state of *v* on the control flow transfer to *embedded-statement* is the same as the state of *v* at the end of *expr*.

#### Yield statements

For a `yield``return` statement *stmt* of the form:

```csharp
yield return *expr* ;
```

-  The definite assignment state of *v* at the beginning of *expr* is the same as the state of *v* at the beginning of *stmt*.
-  The definite assignment state of *v* at the end of *stmt* is the same as the state of *v* at the end of *expr*.
-  A `yield``break` statement has no effect on the definite assignment state.

#### General rules for simple expressions

The following rule applies to these kinds of expressions: literals (§7.6.1), simple names (§7.6.2), member access expressions (§7.6.4), non-indexed base access expressions (§7.6.8), `typeof` expressions (§7.6.11), and default value expressions (§7.6.13).

-  The definite assignment state of *v* at the end of such an expression is the same as the definite assignment state of *v* at the beginning of the expression.

#### General rules for expressions with embedded expressions

The following rules apply to these kinds of expressions: parenthesized expressions (§7.6.3), element access expressions (§7.6.6), base access expressions with indexing (§7.6.8), increment and decrement expressions (§7.6.9, §7.7.5), cast expressions (§7.7.6), unary `+`, `-`, `~`, `*` expressions, binary `+`, `-`, `*`, `/`, `%`, `<<`, `>>`, `<`, `<=`, `>`, `>=`, `==`, `!=`, `is`, `as`, `&amp;`, `|`, `^` expressions (§7.8, §7.9, §7.10, §7.11), compound assignment expressions (§7.17.2), `checked` and `unchecked` expressions (§7.6.12), plus array and delegate creation expressions (§7.6.10).

Each of these expressions has one or more sub-expressions that are unconditionally evaluated in a fixed order. For example, the binary `%` operator evaluates the left hand side of the operator, then the right hand side. An indexing operation evaluates the indexed expression, and then evaluates each of the index expressions, in order from left to right. For an expression *expr*, which has sub-expressions *expr*<sub>1</sub>, *expr*<sub>2</sub>, ..., *expr*<sub>n</sub>, evaluated in that order:

-  The definite assignment state of *v* at the beginning of *expr*<sub>1</sub> is the same as the definite assignment state at the beginning of *expr*.
-  The definite assignment state of *v* at the beginning of *expr*<sub>i</sub> (*i* greater than one) is the same as the definite assignment state at the end of *expr*<sub>i-1</sub>.
-  The definite assignment state of *v* at the end of *expr* is the same as the definite assignment state at the end of *expr*<sub>n</sub>.

#### Invocation expressions and object creation expressions

For an invocation expression *expr* of the form:

```csharp
*primary-expression* ( *arg*<sub>1</sub> , *arg*<sub>2</sub> , … , *arg*<sub>n</sub> )
```

or an object creation expression of the form:

```csharp
new *type* ( *arg*<sub>1</sub> , *arg*<sub>2</sub> , … , *arg*<sub>n</sub> )
```

-  For an invocation expression, the definite assignment state of *v* before *primary-expression* is the same as the state of *v* before *expr*.
-  For an invocation expression, the definite assignment state of *v* before *arg*<sub>1</sub> is the same as the state of *v* after *primary-expression*.
-  For an object creation expression, the definite assignment state of *v* before *arg*<sub>1</sub> is the same as the state of *v* before *expr*.
-  For each argument *arg*<sub>i</sub>, the definite assignment state of *v* after *arg*<sub>i</sub> is determined by the normal expression rules, ignoring any `ref` or `out` modifiers.
-  For each argument *arg*<sub>i</sub> for any *i* greater than one, the definite assignment state of *v* before *arg*<sub>i</sub> is the same as the state of *v* after *arg*<sub>i-1</sub>.
-  If the variable *v* is passed as an `out` argument (i.e., an argument of the form "`out`*v*") in any of the arguments, then the state of *v* after *expr* is definitely assigned. Otherwise; the state of *v* after *expr* is the same as the state of *v* after *arg*<sub>n</sub>.
-  For array initializers (§7.6.10.4), object initializers (§7.6.10.2), collection initializers (§7.6.10.3) and anonymous object initializers (§7.6.10.6), the definite assignment state is determined by the expansion that these constructs are defined in terms of.

#### Simple assignment expressions

For an expression *expr* of the form *w*`=`*expr-rhs*:

-  The definite assignment state of *v* before *expr-rhs* is the same as the definite assignment state of *v* before *expr*.
-  If *w* is the same variable as *v*, then the definite assignment state of *v* after *expr* is definitely assigned. Otherwise, the definite assignment state of *v* after *expr* is the same as the definite assignment state of *v* after *expr-rhs*.

#### &amp;&amp; expressions

For an expression *expr* of the form *expr-first*`&amp;&amp;`*expr-second*:

-  The definite assignment state of *v* before *expr-first* is the same as the definite assignment state of *v* before *expr*.
-  The definite assignment state of *v* before *expr-second* is definitely assigned if the state of *v* after *expr-first* is either definitely assigned or "definitely assigned after true expression". Otherwise, it is not definitely assigned.
-  The definite assignment state of *v* after *expr* is determined by:

If *expr-first* is a constant expression with the value `false`, then the definite assignment state of *v* after *expr* is the same as the definite assignment state of *v* after *expr-first*.

Otherwise, if the state of *v* after *expr-first* is definitely assigned, then the state of *v* after *expr* is definitely assigned.

Otherwise, if the state of *v* after *expr-second* is definitely assigned, and the state of *v* after *expr-first* is "definitely assigned after false expression", then the state of *v* after *expr* is definitely assigned.

Otherwise, if the state of *v* after *expr-second* is definitely assigned or "definitely assigned after true expression", then the state of *v* after *expr* is "definitely assigned after true expression".

Otherwise, if the state of *v* after *expr-first* is "definitely assigned after false expression", and the state of *v* after *expr-second* is "definitely assigned after false expression", then the state of *v* after *expr* is "definitely assigned after false expression".

Otherwise, the state of *v* after *expr* is not definitely assigned.

In the example

```csharp
class A
{
    static void F(int x, int y) {
        int i;
        if (x >= 0 &amp;&amp; (i = y) >= 0) {
            // i definitely assigned
        }
        else {
            // i not definitely assigned
        }
        // i not definitely assigned
    }
}
```

the variable `i` is considered definitely assigned in one of the embedded statements of an `if` statement but not in the other. In the `if` statement in method `F`, the variable `i` is definitely assigned in the first embedded statement because execution of the expression `(i``=``y)` always precedes execution of this embedded statement. In contrast, the variable `i` is not definitely assigned in the second embedded statement, since `x``>=``0` might have tested false, resulting in the variable `i` being unassigned.

#### || expressions

For an expression *expr* of the form *expr-first*`||`*expr-second*:

-  The definite assignment state of *v* before *expr-first* is the same as the definite assignment state of *v* before *expr*.
-  The definite assignment state of *v* before *expr-second* is definitely assigned if the state of *v* after *expr-first* is either definitely assigned or "definitely assigned after false expression". Otherwise, it is not definitely assigned.
-  The definite assignment statement of *v* after *expr* is determined by:

If *expr-first* is a constant expression with the value `true`, then the definite assignment state of *v* after *expr* is the same as the definite assignment state of *v* after *expr-first*.

Otherwise, if the state of *v* after *expr-first* is definitely assigned, then the state of *v* after *expr* is definitely assigned.

Otherwise, if the state of *v* after *expr-second* is definitely assigned, and the state of *v* after *expr-first* is "definitely assigned after true expression", then the state of *v* after *expr* is definitely assigned.

Otherwise, if the state of *v* after *expr-second* is definitely assigned or "definitely assigned after false expression", then the state of *v* after *expr* is "definitely assigned after false expression".

Otherwise, if the state of *v* after *expr-first* is "definitely assigned after true expression", and the state of *v* after *expr-second* is "definitely assigned after true expression", then the state of *v* after *expr* is "definitely assigned after true expression".

Otherwise, the state of *v* after *expr* is not definitely assigned.

In the example

```csharp
class A
{
    static void G(int x, int y) {
        int i;
        if (x >= 0 || (i = y) >= 0) {
            // i not definitely assigned
        }
        else {
            // i definitely assigned
        }
        // i not definitely assigned
    }
}
```

the variable `i` is considered definitely assigned in one of the embedded statements of an `if` statement but not in the other. In the `if` statement in method `G`, the variable `i` is definitely assigned in the second embedded statement because execution of the expression `(i``=``y)` always precedes execution of this embedded statement. In contrast, the variable `i` is not definitely assigned in the first embedded statement, since `x``>=``0` might have tested true, resulting in the variable `i` being unassigned.

#### ! expressions

For an expression *expr* of the form `!`*expr-operand*:

-  The definite assignment state of *v* before *expr-operand* is the same as the definite assignment state of *v* before *expr*.
-  The definite assignment state of *v* after *expr* is determined by:

If the state of *v* after *expr-operand *is definitely assigned, then the state of *v* after *expr* is definitely assigned.

If the state of *v* after *expr-operand *is not definitely assigned, then the state of *v* after *expr* is not definitely assigned.

If the state of *v* after *expr-operand *is "definitely assigned after false expression", then the state of *v* after *expr* is "definitely assigned after true expression".

If the state of *v* after *expr-operand *is "definitely assigned after true expression", then the state of *v* after *expr* is "definitely assigned after false expression".

#### ?? expressions

For an expression *expr* of the form *expr-first***`??`* expr-second*:

-  The definite assignment state of *v* before *expr-first* is the same as the definite assignment state of *v* before *expr*.
-  The definite assignment state of *v* before *expr-second* is the same as the definite assignment state of *v* after *expr-first*.
-  The definite assignment statement of *v* after *expr* is determined by:

If *expr-first* is a constant expression (§7.19) with value null, then the the state of *v* after *expr* is the same as the state of *v* after *expr-second*.

-  Otherwise, the state of *v* after *expr* is the same as the definite assignment state of *v* after *expr-first*.

#### ?: expressions

For an expression *expr* of the form *expr-cond*`?`*expr-true*`:`*expr-false*:

-  The definite assignment state of *v* before *expr-cond* is the same as the state of *v* before *expr*.
-  The definite assignment state of *v* before *expr-true* is definitely assigned if and only if one of the following holds:

*expr-cond* is a constant expression with the value `false`

the state of *v* after *expr-cond* is definitely assigned or "definitely assigned after true expression".

-  The definite assignment state of *v* before *expr-false* is definitely assigned if and only if one of the following holds:

*expr-cond* is a constant expression with the value `true`

-  the state of *v* after *expr-cond* is definitely assigned or "definitely assigned after false expression".
-  The definite assignment state of *v* after *expr* is determined by:

If *expr-cond* is a constant expression (§7.19) with value `true` then the state of *v* after *expr* is the same as the state of *v* after *expr-true*.

Otherwise, if *expr-cond* is a constant expression (§7.19) with value `false` then the state of *v* after *expr* is the same as the state of *v* after *expr-false*.

Otherwise, if the state of *v* after *expr-true* is definitely assigned and the state of *v* after *expr-false* is definitely assigned, then the state of *v* after *expr* is definitely assigned.

Otherwise, the state of *v* after *expr* is not definitely assigned.

#### Anonymous functions

For a *lambda-expression* or *anonymous-method-expression**expr* with a body (either *block* or *expression*) *body*:

-  The definite assignment state of an outer variable *v* before *body* is the same as the state of *v* before *expr*. That is, definite assignment state of outer variables is inherited from the context of the anonymous function.
-  The definite assignment state of an outer variable *v* after *expr* is the same as the state of *v* before *expr*.

The example

```csharp
delegate bool Filter(int i);

void F() {
    int max;

    // Error, max is not definitely assigned
    Filter f = (int n) => n < max;

    max = 5;
    DoWork(f);
}
```

generates a compile-time error since `max` is not definitely assigned where the anonymous function is declared. The example

```csharp
delegate void D();

void F() {
    int n;
    D d = () => { n = 1; };

    d();

    // Error, n is not definitely assigned
    Console.WriteLine(n);
}
```

also generates a compile-time error since the assignment to `n` in the anonymous function has no affect on the definite assignment state of `n` outside the anonymous function.

## Variable references

A *variable-reference* is an *expression* that is classified as a variable. A *variable-reference* denotes a storage location that can be accessed both to fetch the current value and to store a new value.

<pre>variable-reference:
expression</pre>

In C and C++, a *variable-reference* is known as an *lvalue*.

## Atomicity of variable references

Reads and writes of the following data types are atomic: `bool`, `char`, `byte`, `sbyte`, `short`, `ushort`, `uint`, `int`, `float`, and reference types. In addition, reads and writes of enum types with an underlying type in the previous list are also atomic. Reads and writes of other types, including `long`, `ulong`, `double`, and `decimal`, as well as user-defined types, are not guaranteed to be atomic. Aside from the library functions designed for that purpose, there is no guarantee of atomic read-modify-write, such as in the case of increment or decrement.
