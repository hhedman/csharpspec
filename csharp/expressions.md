# Expressions

An expression is a sequence of operators and operands. This chapter defines the syntax, order of evaluation of operands and operators, and meaning of expressions.

## Expression classifications

An expression is classified as one of the following:

-  A value. Every value has an associated type.
-  A variable. Every variable has an associated type, namely the declared type of the variable.
-  A namespace. An expression with this classification can only appear as the left hand side of a *member-access* (§7.6.4). In any other context, an expression classified as a namespace causes a compile-time error.
-  A type. An expression with this classification can only appear as the left hand side of a *member-access* (§7.6.4), or as an operand for the `as` operator (§7.10.11), the `is` operator (§7.10.10), or the `typeof` operator (§7.6.11). In any other context, an expression classified as a type causes a compile-time error.
-  A method group, which is a set of overloaded methods resulting from a member lookup (§7.4). A method group may have an associated instance expression and an associated type argument list. When an instance method is invoked, the result of evaluating the instance expression becomes the instance represented by `this` (§7.6.7). A method group is permitted in an *invocation-expression* (§7.6.5) , a *delegate-creation-expression* (§7.6.10.5) and as the left hand side of an is operator, and can be implicitly converted to a compatible delegate type (§6.6). In any other context, an expression classified as a method group causes a compile-time error.
-  A null literal. An expression with this classification can be implicitly converted to a reference type or nullable type.
-  An anonymous function. An expression with this classification can be implicitly converted to a compatible delegate type or expression tree type.
-  A property access. Every property access has an associated type, namely the type of the property. Furthermore, a property access may have an associated instance expression. When an accessor (the `get` or `set` block) of an instance property access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` (§7.6.7).
-  An event access. Every event access has an associated type, namely the type of the event. Furthermore, an event access may have an associated instance expression. An event access may appear as the left hand operand of the `+=` and `-=` operators (§7.17.3). In any other context, an expression classified as an event access causes a compile-time error.
-  An indexer access. Every indexer access has an associated type, namely the element type of the indexer. Furthermore, an indexer access has an associated instance expression and an associated argument list. When an accessor (the `get` or `set` block) of an indexer access is invoked, the result of evaluating the instance expression becomes the instance represented by `this` (§7.6.7), and the result of evaluating the argument list becomes the parameter list of the invocation.
-  Nothing. This occurs when the expression is an invocation of a method with a return type of `void`. An expression classified as nothing is only valid in the context of a *statement-expression* (§8.6).

The final result of an expression is never a namespace, type, method group, or event access. Rather, as noted above, these categories of expressions are intermediate constructs that are only permitted in certain contexts.

A property access or indexer access is always reclassified as a value by performing an invocation of the *get-accessor* or the *set-accessor*. The particular accessor is determined by the context of the property or indexer access: If the access is the target of an assignment, the *set-accessor* is invoked to assign a new value (§7.17.1). Otherwise, the *get-accessor* is invoked to obtain the current value (§7.1.1).

### Values of expressions

Most of the constructs that involve an expression ultimately require the expression to denote a *__value__*. In such cases, if the actual expression denotes a namespace, a type, a method group, or nothing, a compile-time error occurs. However, if the expression denotes a property access, an indexer access, or a variable, the value of the property, indexer, or variable is implicitly substituted:

-  The value of a variable is simply the value currently stored in the storage location identified by the variable. A variable must be considered definitely assigned (§5.3) before its value can be obtained, or otherwise a compile-time error occurs.
-  The value of a property access expression is obtained by invoking the *get-accessor* of the property. If the property has no *get-accessor*, a compile-time error occurs. Otherwise, a function member invocation (§7.5.4) is performed, and the result of the invocation becomes the value of the property access expression.
-  The value of an indexer access expression is obtained by invoking the *get-accessor* of the indexer. If the indexer has no *get-accessor*, a compile-time error occurs. Otherwise, a function member invocation (§7.5.4) is performed with the argument list associated with the indexer access expression, and the result of the invocation becomes the value of the indexer access expression.

## Static and Dynamic Binding

The process of determining the meaning of an operation based on the type or value of constituent expressions (arguments, operands, receivers) is often referred to as *__binding__*. For instance the meaning of a method call is determined based on the type of the receiver and arguments. The meaning of an operator is determined based on the type of its operands.

In C# the meaning of an operation is usually determined at compile-time, based on the compile-time type of its constituent expressions. Likewise, if an expression contains an error, the error is detected and reported by the compiler. This approach is known as *__static binding__*.

However, if an expression is a dynamic expression (i.e. has the type `dynamic`) this indicates that any binding that it participates in should be based on its run-time type (i.e. the actual type of the object it denotes at run-time) rather than the type it has at compile-time. The binding of such an operation is therefore deferred until the time where the operation is to be executed during the running of the program. This is referred to as *__dynamic binding__*.

When an operation is dynamically bound, little or no checking is performed by the compiler. Instead if the run-time binding fails, errors are reported as exceptions at run-time.

The following operations in C# are subject to binding:

-  Member access: `e.M`
-  Method invocation: `e``.M``(``e`<sub>1</sub>`,…,e`<sub>n</sub>`)`
-  Delegate invocaton:```e(``e`<sub>1</sub>`,…,e`<sub>n</sub>`)`
-  Element access: `e[e`<sub>1</sub>`,…,e`<sub>n</sub>`]`
-  Object creation: `new C(e`<sub>1</sub>`,…,e`<sub>n</sub>`)`
-  Overloaded unary operators: `+`, `-`, `!`, `~`, `++`, `--`, `true`, `false`
-  Overloaded binary operators: `+`, `-`, `*`, `/`, `%`, `&amp;`, `&amp;``&amp;`, `|`, `||`, `??`, `^`, `<<`, `>>`, `==`,`!=`, `>`, `<`, `>=`, `<=`
-  Assignment operators: `=`, `+``=`, `-``=`, `*=`, `/=`, `%=`, `&amp;=`, `|=`, `^=`, `<<=`, `>>=`
-  Implicit and explicit conversions

When no dynamic expressions are involved, C# defaults to static binding, which means that the compile-time types of constituent expressions are used in the selection process. However, when one of the constituent expressions in the operations listed above is a dynamic expression, the operation is instead dynamically bound.

### Binding-time

Static binding takes place at compile-time, whereas dynamic binding takes place at run-time. In the following sections, the term *__binding-time__* refers to either compile-time or run-time, depending on when the binding takes place.

The following example illustrates the notions of static and dynamic binding and of binding-time:

```csharp
object o  = 5;
dynamic d = 5;

Console.WriteLine(5); //  static binding to Console.WriteLine(int)
Console.WriteLine(o); //  static binding to Console.WriteLine(object)
Console.WriteLine(d); // dynamic binding to Console.WriteLine(int)
```

The first two calls are statically bound: the overload of `Console.WriteLine` is picked based on the compile-time type of their argument. Thus, the binding-time is compile-time.

The third call is dynamically bound: the overload of `Console.WriteLine` is picked based on the run-time type of its argument. This happens because the argument is a dynamic expression – its compile-time type is `dynamic`. Thus, the binding-time for the third call is run-time.

### Dynamic binding

The purpose of dynamic binding is to allow C# programs to interact with *__dynamic objects__, i.e. objects that do not follow the normal rules of the C# type system. Dynamic objects may be objects from other programming languages with different types systems, or they may be objects that are programmatically setup to implement their own binding semantics for different operations.*

*The mechanism by which a dynamic object implements its own semantics is implementation defined. A given interface – again implementation defined – is implemented by dynamic objects to signal to the C# run-time that they have special semantics. Thus, whenever operations on a dynamic object are dynamically bound, their own binding semantics, rather than those of C# as specified in this document, take over.*

*While the purpose of dynamic binding is to allow interoperation with dynamic objects, C# allows dynamic binding on all objects, whether they are dynamic or not. This allows for a smoother integration of dynamic objects, as the results of operations on them may not themselves be dynamic objects, but are still of a type unknown to the programmer at compile-time. Also dynamic binding can help eliminate error-prone reflection-based code even when no objects involved are dynamic objects.*

*The following sections describe for each construct in the language exactly when dynamic binding is applied, what compile time checking – if any – is applied, and what the compile-time result and expression classification is.*

### *Types of constituent expressions*

When an operation is statically bound, the type of a constituent expression (e.g. a receiver, and argument, an index or an operand) is always considered to be the compile-time type of that expression.

When an operation is dynamically bound, the type of a constituent expression is determined in different ways depending on the compile-time type of the constituent expression:

-  A constituent expression of compile-time type `dynamic` is considered to have the type of the actual value that the expression evaluates to at runtime
-  A constituent expression whose compile-time type is a type parameter is considered to have the type which the type parameter is bound to at runtime
-  Otherwise the constituent expression is considered to have its compile-time type.

## Operators

Expressions are constructed from *__operands__* and *__operators__*. The operators of an expression indicate which operations to apply to the operands. Examples of operators include `+`, `-`, `*`, `/`, and `new`. Examples of operands include literals, fields, local variables, and expressions.

There are three kinds of operators:

-  Unary operators. The unary operators take one operand and use either prefix notation (such as `–x`) or postfix notation (such as `x++`).
-  Binary operators. The binary operators take two operands and all use infix notation (such as `x` `+` `y`).
-  Ternary operator. Only one ternary operator, `?:`, exists; it takes three operands and uses infix notation (`c?` `x:` `y`).

The order of evaluation of operators in an expression is determined by the *__precedence__* and *__associativity__* of the operators (§7.3.1).

Operands in an expression are evaluated from left to right. For example, in `F(i)``+``G(i++)``*``H(i)`, method `F` is called using the old value of `i`, then method `G` is called with the old value of `i`, and, finally, method `H` is called with the new value of `i`. This is separate from and unrelated to operator precedence.

Certain operators can be *__overloaded__*. Operator overloading permits user-defined operator implementations to be specified for operations where one or both of the operands are of a user-defined class or struct type (§7.3.2).

### Operator precedence and associativity

When an expression contains multiple operators, the *__precedence__* of the operators controls the order in which the individual operators are evaluated. For example, the expression `x` `+` `y` `*` `z` is evaluated as `x` `+` `(y` `*` `z)` because the `*` operator has higher precedence than the binary `+` operator. The precedence of an operator is established by the definition of its associated grammar production. For example, an *additive-expression* consists of a sequence of *multiplicative-expression*s separated by `+` or `-` operators, thus giving the `+` and `-` operators lower precedence than the `*`, `/`, and `%` operators.

The following table summarizes all operators in order of precedence from highest to lowest:


| __Section__ | __Category__ | __Operators__ | 
| 7.6 | Primary | `x.y  f(x)  a[x]  x++  x--  new``typeof  ``default  ``checked  unchecked``  delegate` | 
| 7.7 | Unary | `+  -  !  ~  ++x  --x  (T)x` | 
| 7.8 | Multiplicative | `*  /  %` | 
| 7.8 | Additive | `+  -` | 
| 7.9 | Shift | `<<  >>` | 
| 7.10 | Relational and type testing | `<  >  <=  >=  is  as` | 
| 7.10 | Equality | `==  !=` | 
| 7.11 | Logical AND | `&amp;` | 
| 7.11 | Logical XOR | `^` | 
| 7.11 | Logical OR | `|` | 
| 7.12 | Conditional AND | `&amp;&amp;` | 
| 7.12 | Conditional OR | `||` | 
| 7.13 | Null coalescing | `??` | 
| 7.14 | Conditional | `?:` | 
| 7.17, 7.15 | Assignment and lambda expression | `=  *=  /=  %=  +=  -=  <<=  >>=  &amp;=  ^=  |=``=>` | 

When an operand occurs between two operators with the same precedence, the associativity of the operators controls the order in which the operations are performed:

-  Except for the assignment operators and the null coalescing operator, all binary operators are *__left-associative__*, meaning that operations are performed from left to right. For example, `x``+``y``+``z` is evaluated as `(x``+``y)``+``z`.
-  The assignment operators, the null coalescing operator and the conditional operator (`?:`) are *__right-associative__*, meaning that operations are performed from right to left. For example, `x``=``y``=``z` is evaluated as `x``=``(y``=``z)`.

Precedence and associativity can be controlled using parentheses. For example, `x``+``y``*``z` first multiplies `y` by `z` and then adds the result to `x`, but `(x``+``y)``*``z` first adds `x` and `y` and then multiplies the result by `z`.

### Operator overloading

All unary and binary operators have predefined implementations that are automatically available in any expression. In addition to the predefined implementations, user-defined implementations can be introduced by including `operator` declarations in classes and structs (§10.10). User-defined operator implementations always take precedence over predefined operator implementations: Only when no applicable user-defined operator implementations exist will the predefined operator implementations be considered, as described in §7.3.3 and §7.3.4.

The *__overloadable unary operators__* are:

```csharp
+   -   !   ~   ++   --   true   false
```

Although `true` and `false` are not used explicitly in expressions (and therefore are not included in the precedence table in §7.3.1), they are considered operators because they are invoked in several expression contexts: boolean expressions (§7.20) and expressions involving the conditional (§7.14), and conditional logical operators (§7.12).

The *__overloadable binary operators__* are:

```csharp
+   -   *   /   %   &amp;   |   ^   <<   >>   ==   !=   >   <   >=   <=
```

Only the operators listed above can be overloaded. In particular, it is not possible to overload member access, method invocation, or the `=`, `&amp;&amp;`, `||`, `??`, `?:`, `=>`, `checked`, `unchecked`, `new`, `typeof`, `default`, `as`, and i`s` operators.

When a binary operator is overloaded, the corresponding assignment operator, if any, is also implicitly overloaded. For example, an overload of operator `*` is also an overload of operator `*=`. This is described further in §7.17.2. Note that the assignment operator itself (`=`) cannot be overloaded. An assignment always performs a simple bit-wise copy of a value into a variable.

Cast operations, such as `(T)x`, are overloaded by providing user-defined conversions (§6.4).

Element access, such as `a[x]`, is not considered an overloadable operator. Instead, user-defined indexing is supported through indexers (§10.9).

In expressions, operators are referenced using operator notation, and in declarations, operators are referenced using functional notation. The following table shows the relationship between operator and functional notations for unary and binary operators. In the first entry, *op* denotes any overloadable unary prefix operator. In the second entry, *op* denotes the unary postfix `++` and `--` operators. In the third entry, *op* denotes any overloadable binary operator.


| __Operator notation__ | __Functional notation__ | 
| *op*`x` | `operator`*op*`(x)` | 
| `x`*op* | `operator`*op*`(x)` | 
| `x`*op*`y` | `operator`*op*`(x,``y)` | 

User-defined operator declarations always require at least one of the parameters to be of the class or struct type that contains the operator declaration. Thus, it is not possible for a user-defined operator to have the same signature as a predefined operator.

User-defined operator declarations cannot modify the syntax, precedence, or associativity of an operator. For example, the `/` operator is always a binary operator, always has the precedence level specified in §7.3.1, and is always left-associative.

While it is possible for a user-defined operator to perform any computation it pleases, implementations that produce results other than those that are intuitively expected are strongly discouraged. For example, an implementation of `operator``==` should compare the two operands for equality and return an appropriate `bool` result.

The descriptions of individual operators in §7.6 through §7.12 specify the predefined implementations of the operators and any additional rules that apply to each operator. The descriptions make use of the terms *__unary operator overload resolution__*, *__binary operator overload resolution__*, and *__numeric promotion__*, definitions of which are found in the following sections.

### Unary operator overload resolution

An operation of the form *op*`x` or `x`*op*, where *op* is an overloadable unary operator, and `x` is an expression of type `X`, is processed as follows:

-  The set of candidate user-defined operators provided by `X` for the operation `operator`*op*`(x)` is determined using the rules of §7.3.5.
-  If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation. Otherwise, the predefined unary `operator`*op* implementations, including their lifted forms, become the set of candidate operators for the operation. The predefined implementations of a given operator are specified in the description of the operator (§7.6 and §7.7).
-  The overload resolution rules of §7.5.3 are applied to the set of candidate operators to select the best operator with respect to the argument list `(x)`, and this operator becomes the result of the overload resolution process. If overload resolution fails to select a single best operator, a binding-time error occurs.

### Binary operator overload resolution

An operation of the form `x`*op*`y`, where *op* is an overloadable binary operator, `x` is an expression of type `X`, and `y` is an expression of type `Y`, is processed as follows:

-  The set of candidate user-defined operators provided by `X` and `Y` for the operation `operator`*op*`(x,``y)` is determined. The set consists of the union of the candidate operators provided by `X` and the candidate operators provided by `Y`, each determined using the rules of §7.3.5. If `X` and `Y` are the same type, or if `X` and `Y` are derived from a common base type, then shared candidate operators only occur in the combined set once.
-  If the set of candidate user-defined operators is not empty, then this becomes the set of candidate operators for the operation. Otherwise, the predefined binary `operator`*op* implementations, including their lifted forms,  become the set of candidate operators for the operation. The predefined implementations of a given operator are specified in the description of the operator (§7.8 through §7.12). For predefined enum and delegate operators, the only operators considered are those defined by an enum or delegate type that is the binding-time type of one of the operands.
-  The overload resolution rules of §7.5.3 are applied to the set of candidate operators to select the best operator with respect to the argument list `(x,``y)`, and this operator becomes the result of the overload resolution process. If overload resolution fails to select a single best operator, a binding-time error occurs.

### Candidate user-defined operators

Given a type `T` and an operation `operator`*op*`(A)`, where *op* is an overloadable operator and `A` is an argument list, the set of candidate user-defined operators provided by `T` for `operator`*op*`(A)` is determined as follows:

-  Determine the type `T`<sub>0</sub>. If `T` is a nullable type, `T`<sub>0</sub> is its underlying type, otherwise `T`<sub>0</sub> is equal to `T`.
-  For all `operator`*op* declarations in `T`<sub>0</sub> and all lifted forms of such operators, if at least one operator is applicable (§7.5.3.1) with respect to the argument list `A`, then the set of candidate operators consists of all such applicable operators in `T`<sub>0</sub>.
-  Otherwise, if `T`<sub>0</sub> is `object`, the set of candidate operators is empty.
-  Otherwise, the set of candidate operators provided by `T`<sub>0</sub> is the set of candidate operators provided by the direct base class of `T`<sub>0</sub>, or the effective base class of```T`<sub>0</sub> if `T`<sub>0</sub> is a type parameter.

### Numeric promotions

Numeric promotion consists of automatically performing certain implicit conversions of the operands of the predefined unary and binary numeric operators. Numeric promotion is not a distinct mechanism, but rather an effect of applying overload resolution to the predefined operators. Numeric promotion specifically does not affect evaluation of user-defined operators, although user-defined operators can be implemented to exhibit similar effects.

As an example of numeric promotion, consider the predefined implementations of the binary `*` operator:

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
float operator *(float x, float y);
double operator *(double x, double y);
decimal operator *(decimal x, decimal y);
```

When overload resolution rules (§7.5.3) are applied to this set of operators, the effect is to select the first of the operators for which implicit conversions exist from the operand types. For example, for the operation `b``*``s`, where `b` is a `byte` and `s` is a `short`, overload resolution selects `operator``*(int,``int)` as the best operator. Thus, the effect is that `b` and `s` are converted to `int`, and the type of the result is `int`. Likewise, for the operation `i``*``d`, where `i` is an `int` and `d` is a `double`, overload resolution selects `operator``*(double,``double)` as the best operator.

#### Unary numeric promotions

Unary numeric promotion occurs for the operands of the predefined `+`, `–`, and `~` unary operators. Unary numeric promotion simply consists of converting operands of type `sbyte`, `byte`, `short`, `ushort`, or `char` to type `int`. Additionally, for the unary `–` operator, unary numeric promotion converts operands of type `uint` to type `long`.

#### Binary numeric promotions

Binary numeric promotion occurs for the operands of the predefined `+`, `–`, `*`, `/`, `%`, `&amp;`, `|`, `^`, `==`, `!=`, `>`, `<`, `>=`, and `<=` binary operators. Binary numeric promotion implicitly converts both operands to a common type which, in case of the non-relational operators, also becomes the result type of the operation. Binary numeric promotion consists of applying the following rules, in the order they appear here:

-  If either operand is of type `decimal`, the other operand is converted to type `decimal`, or a binding-time error occurs if the other operand is of type `float` or `double`.
-  Otherwise, if either operand is of type `double`, the other operand is converted to type `double`.
-  Otherwise, if either operand is of type `float`, the other operand is converted to type `float`.
-  Otherwise, if either operand is of type `ulong`, the other operand is converted to type `ulong`, or a binding-time error occurs if the other operand is of type `sbyte`, `short`, `int`, or `long`.
-  Otherwise, if either operand is of type `long`, the other operand is converted to type `long`.
-  Otherwise, if either operand is of type `uint` and the other operand is of type `sbyte`, `short`, or `int`, both operands are converted to type `long`.
-  Otherwise, if either operand is of type `uint`, the other operand is converted to type `uint`.
-  Otherwise, both operands are converted to type `int`.

Note that the first rule disallows any operations that mix the `decimal` type with the `double` and `float` types. The rule follows from the fact that there are no implicit conversions between the `decimal` type and the `double` and `float` types.

Also note that it is not possible for an operand to be of type `ulong` when the other operand is of a signed integral type. The reason is that no integral type exists that can represent the full range of `ulong` as well as the signed integral types.

In both of the above cases, a cast expression can be used to explicitly convert one operand to a type that is compatible with the other operand.

In the example

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (1.0 + percent / 100.0);
}
```

a binding-time error occurs because a `decimal` cannot be multiplied by a `double`. The error is resolved by explicitly converting the second operand to `decimal`, as follows:

```csharp
decimal AddPercent(decimal x, double percent) {
    return x * (decimal)(1.0 + percent / 100.0);
}
```

### Lifted operators

*__Lifted operators__* permit predefined and user-defined operators that operate on non-nullable value types to also be used with nullable forms of those types. Lifted operators are constructed from predefined and user-defined operators that meet certain requirements, as described in the following:

-  For the unary operators

```csharp
+  ++  -  --  !  ~
```

a lifted form of an operator exists if the operand and result types are both non-nullable value types. The lifted form is constructed by adding a single `?` modifier to the operand and result types. The lifted operator produces a null value if the operand is null. Otherwise, the lifted operator unwraps the operand, applies the underlying operator, and wraps the result.

-  For the binary operators

```csharp
+  -  *  /  %  &amp;  |  ^  <<  >>
```

a lifted form of an operator exists if the operand and result types are all non-nullable value types. The lifted form is constructed by adding a single `?` modifier to each operand and result type. The lifted operator produces a null value if one or both operands are null (an exception being the `&amp;` and `|` operators of the `bool?` type, as described in §7.11.3). Otherwise, the lifted operator unwraps the operands, applies the underlying operator, and wraps the result.

-  For the equality operators

```csharp
==  !=
```

a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`. The lifted form is constructed by adding a single `?` modifier to each operand type. The lifted operator considers two null values equal, and a null value unequal to any non-null value. If both operands are non-null, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.

-  For the relational operators

```csharp
`<``>``<=``>=`
```

a lifted form of an operator exists if the operand types are both non-nullable value types and if the result type is `bool`. The lifted form is constructed by adding a single `?` modifier to each operand type. The lifted operator produces the value `false` if one or both operands are null. Otherwise, the lifted operator unwraps the operands and applies the underlying operator to produce the `bool` result.

## Member lookup

A member lookup is the process whereby the meaning of a name in the context of a type is determined. A member lookup can occur as part of evaluating a *simple-name* (§7.6.2) or a *member-access* (§7.6.4) in an expression. If the *simple-name* or *member-access* occurs as the *primary**-expression* of an *invocation-expression* (§7.6.5.1), the member is said to be invoked.

If a member is a method or event, or if it is a constant, field or property of either a delegate type (§15) or the type `dynamic` (§4.7), then the member is said to be invocable*.*

Member lookup considers not only the name of a member but also the number of type parameters the member has and whether the member is accessible. For the purposes of member lookup, generic methods and nested generic types have the number of type parameters indicated in their respective declarations and all other members have zero type parameters.

A member lookup of a name `N` with `K` type parameters in a type `T` is processed as follows:

-  First, a set of accessible members named `N` is determined:

If `T` is a type parameter, then the set is the union of the sets of accessible members named `N` in each of the types specified as a primary constraint or secondary constraint (§10.1.5) for `T`, along with the set of accessible members named `N` in `object`.

Otherwise, the set consists of all accessible (§3.5) members named `N` in `T`, including inherited members and the accessible members named `N` in `object`. If `T` is a constructed type, the set of members is obtained by substituting type arguments as described in §10.3.2. Members that include an `override` modifier are excluded from the set.

-  Next, if `K` is zero, all nested types whose declarations include type parameters are removed. If `K` is not zero, all members with a different number of type parameters are removed. Note that when `K` is zero, methods having type parameters are not removed, since the type inference process (§7.5.2) might be able to infer the type arguments.
-  Next, if the member is invoked*, all non-*invocable *members are removed from the set.*
-  Next, members that are hidden by other members are removed from the set. For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied:

If `M` is a constant, field, property, event, or enumeration member, then all members declared in a base type of `S` are removed from the set.

If `M` is a type declaration, then all non-types declared in a base type of `S` are removed from the set, and all type declarations with the same number of type parameters as `M` declared in a base type of `S` are removed from the set.

If `M` is a method, then all non-method members declared in a base type of `S` are removed from the set.

-  Next, interface members that are hidden by class members are removed from the set. This step only has an effect if `T` is a type parameter and `T` has both an effective base class other than `object` and a non-empty effective interface set (§10.1.5). For every member `S.M` in the set, where `S` is the type in which the member `M` is declared, the following rules are applied if `S` is a class declaration other than `object`:

If `M` is a constant, field, property, event, enumeration member, or type declaration, then all members declared in an interface declaration are removed from the set.

If `M` is a method, then all non-method members declared in an interface declaration are removed from the set, and all methods with the same signature as `M` declared in an interface declaration are removed from the set.

-  Finally, having removed hidden members, the result of the lookup is determined:

If the set consists of a single member that is not a method, then this member is the result of the lookup.

Otherwise, if the set contains only methods, then this group of methods is the result of the lookup.

Otherwise, the lookup is ambiguous, and a binding-time error occurs.

For member lookups in types other than type parameters and interfaces, and member lookups in interfaces that are strictly single-inheritance (each interface in the inheritance chain has exactly zero or one direct base interface), the effect of the lookup rules is simply that derived members hide base members with the same name or signature. Such single-inheritance lookups are never ambiguous. The ambiguities that can possibly arise from member lookups in multiple-inheritance interfaces are described in §13.2.5.

### Base types

For purposes of member lookup, a type `T` is considered to have the following base types:

-  If `T` is `object`, then `T` has no base type.
-  If `T` is an *enum-type*, the base types of `T` are the class types `System.Enum`, `System.ValueType`, and `object`.
-  If `T` is a *struct-type*, the base types of `T` are the class types `System.ValueType` and `object`.
-  If `T` is a *class-type*, the base types of `T` are the base classes of `T`, including the class type `object`.
-  If `T` is an *interface-type*, the base types of `T` are the base interfaces of `T` and the class type `object`.
-  If `T` is an *array-type*, the base types of `T` are the class types `System.Array` and `object`.
-  If `T` is a *delegate-type*, the base types of `T` are the class types `System.Delegate` and `object`.

## Function members

Function members are members that contain executable statements. Function members are always members of types and cannot be members of namespaces. C# defines the following categories of function members:

-  Methods
-  Properties
-  Events
-  Indexers
-  User-defined operators
-  Instance constructors
-  Static constructors
-  Destructors

Except for destructors and static constructors (which cannot be invoked explicitly), the statements contained in function members are executed through function member invocations. The actual syntax for writing a function member invocation depends on the particular function member category.

The argument list (§7.5.1) of a function member invocation provides actual values or variable references for the parameters of the function member.

Invocations of generic methods may employ type inference to determine the set of type arguments to pass to the method. This process is described in §7.5.2.

Invocations of methods, indexers, operators and instance constructors employ overload resolution to determine which of a candidate set of function members to invoke. This process is described in §7.5.3.

Once a particular function member has been identified at binding-time, possibly through overload resolution, the actual run-time process of invoking the function member is described in §7.5.4.

The following table summarizes the processing that takes place in constructs involving the six categories of function members that can be explicitly invoked. In the table, `e`, `x`, `y`, and `value` indicate expressions classified as variables or values, `T` indicates an expression classified as a type, `F` is the simple name of a method, and `P` is the simple name of a property.


| __Construct__ | __Example__ | __Description__ | 
| Method invocation | `F(x,``y)` | Overload resolution is applied to select the best method `F` in the containing class or struct. The method is invoked with the argument list `(x,``y)`. If the method is not `static`, the instance expression is `this`. | 
|  | `T.F(x,``y)` | Overload resolution is applied to select the best method `F` in the class or struct `T`. A binding-time error occurs if the method is not `static`. The method is invoked with the argument list `(x,``y)`. | 
|  | `e.F(x,``y)` | Overload resolution is applied to select the best method F in the class, struct, or interface given by the type of `e`. A binding-time error occurs if the method is `static`. The method is invoked with the instance expression `e` and the argument list `(x,``y)`. | 
| Property access | `P` | The `get` accessor of the property `P` in the containing class or struct is invoked. A compile-time error occurs if `P` is write-only. If `P` is not `static`, the instance expression is `this`. | 
|  | `P``=``value` | The `set` accessor of the property `P` in the containing class or struct is invoked with the argument list `(value)`. A compile-time error occurs if `P` is read-only. If `P` is not `static`, the instance expression is `this`. | 
|  | `T.P` | The `get` accessor of the property `P` in the class or struct `T` is invoked. A compile-time error occurs if `P` is not `static` or if `P` is write-only. | 
|  | `T.P``=``value` | The `set` accessor of the property `P` in the class or struct `T` is invoked with the argument list `(value)`. A compile-time error occurs if `P` is not `static` or if `P` is read-only. | 
|  | `e.P` | The `get` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`. A binding-time error occurs if `P` is `static` or if `P` is write-only. | 
|  | `e.P``=``value` | The `set` accessor of the property `P` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e` and the argument list `(value)`. A binding-time error occurs if `P` is `static` or if `P` is read-only. | 
| Event access | `E += value` | The `add` accessor of the event `E` in the containing class or struct is invoked. If `E` is not static, the instance expression is `this`. | 
|  | `E -= value` | The `remove` accessor of the event `E` in the containing class or struct is invoked. If `E` is not static, the instance expression is `this`. | 
|  | `T.E += value` | The `add` accessor of the event `E` in the class or struct `T` is invoked. A binding-time error occurs if `E` is not static. | 
|  | `T.E -= value` | The `remove` accessor of the event `E` in the class or struct `T` is invoked. A binding-time error occurs if `E` is not static. | 
|  | `e.E += value` | The `add` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`. A binding-time error occurs if `E` is static. | 
|  | `e.E -= value` | The `remove` accessor of the event `E` in the class, struct, or interface given by the type of `e` is invoked with the instance expression `e`. A binding-time error occurs if `E` is static. | 
| Indexer access | `e[x,``y]` | Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of e. The `get` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,``y)`. A binding-time error occurs if the indexer is write-only. | 
|  | `e[x,``y]``=``value` | Overload resolution is applied to select the best indexer in the class, struct, or interface given by the type of `e`. The `set` accessor of the indexer is invoked with the instance expression `e` and the argument list `(x,``y,``value)`. A binding-time error occurs if the indexer is read-only. | 
| Operator invocation | `-x` | Overload resolution is applied to select the best unary operator in the class or struct given by the type of `x`. The selected operator is invoked with the argument list `(x)`. | 
|  | `x``+``y` | Overload resolution is applied to select the best binary operator in the classes or structs given by the types of x and y. The selected operator is invoked with the argument list `(x,``y)`. | 
| Instance constructor invocation | `new``T(x,``y)` | Overload resolution is applied to select the best instance constructor in the class or struct `T`. The instance constructor is invoked with the argument list `(x,``y)`. | 

### Argument lists

Every function member and delegate invocation includes an argument list which provides actual values or variable references for the parameters of the function member. The syntax for specifying the argument list of a function member invocation depends on the function member category:

-  For instance constructors, methods, indexers and delegates, the arguments are specified as an *argument-list*, as described below. For indexers, when invoking the `set` accessor, the argument list additionally includes the expression specified as the right operand of the assignment operator.
-  For properties, the argument list is empty when invoking the `get` accessor, and consists of the expression specified as the right operand of the assignment operator when invoking the `set` accessor.
-  For events, the argument list consists of the expression specified as the right operand of the `+=` or `-=` operator.
-  For user-defined operators, the argument list consists of the single operand of the unary operator or the two operands of the binary operator.

The arguments of properties (§10.7), events (§10.8), and user-defined operators (§10.10) are always passed as value parameters (§10.6.1.1). The arguments of indexers (§10.9) are always passed as value parameters (§10.6.1.1) or parameter arrays (§10.6.1.4). Reference and output parameters are not supported for these categories of function members.

The arguments of an instance constructor, method, indexer or delegate invocation are specified as an *argument-list*:

<pre>argument-list:
argument
argument-list   <b>,</b>   argument</pre>

<pre>argument:
argument-name<sub>opt</sub>   argument-value</pre>

<pre>argument-name:
identifier   <b>:</b></pre>

<pre>argument-value:
expression
<b>ref</b>   variable-reference
<b>out</b>   variable-reference</pre>

An *argument-list* consists of one or more *argument*s, separated by commas. Each argument consists of an optional  *argument-name* followed by an *argument-value*. An *argument* with an *argument-name* is referred to as a *__named argument__*, whereas an *argument* without an *argument-name* is a *__positional argument__*. It is an error for a positional argument to appear after a named argument in an *argument-list*.

The *argument-value* can take one of the following forms:

-  An *expression*, indicating that the argument is passed as a value parameter (§10.6.1.1).
-  The keyword `ref` followed by a *variable-reference* (§5.4), indicating that the argument is passed as a reference parameter (§10.6.1.2). A variable must be definitely assigned (§5.3) before it can be passed as a reference parameter. The keyword `out` followed by a *variable-reference* (§5.4), indicating that the argument is passed as an output parameter (§10.6.1.3). A variable is considered definitely assigned (§5.3) following a function member invocation in which the variable is passed as an output parameter.

#### Corresponding parameters

For each argument in an argument list there has to be a corresponding parameter in the function member or delegate being invoked.

The parameter list used in the following is determined as follows:

-  For virtual methods and indexers defined in classes, the parameter list is picked from the most specific declaration or override of the function member, starting with the static type of the receiver, and searching through its base classes.
-  For interface methods and indexers, the parameter list is picked form the most specific definition of the member, starting with the interface type and searching through the base interfaces. If no unique parameter list is found, a parameter list with inaccessible names and no optional parameters is constructed, so that invocations cannot use named parameters or omit optional arguments.
-  For partial methods, the parameter list of the defining partial method declaration is used.
-  For all other function members and delegates there is only a single parameter list, which is the one used.

The position of an argument or parameter is defined as the number of arguments or parameters preceding it in the argument list or parameter list.

The corresponding parameters for function member arguments are established as follows:

-  Arguments in the *argument-list* of instance constructors, methods, indexers and delegates:

A positional argument where a fixed parameter occurs at the same position in the parameter list corresponds to that parameter.

A positional argument of a function member with a parameter array invoked in its normal form corresponds to the parameter  array, which must occur at the same position in the parameter list.

A positional argument of a function member with a parameter array invoked in its expanded form, where no fixed parameter occurs at the same position in the parameter list, corresponds to an element in the parameter array.

A named argument corresponds to the parameter of the same name in the parameter list.

For indexers, when invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.

-  For properties, when invoking the `get` accessor there are no arguments. When invoking the `set` accessor, the expression specified as the right operand of the assignment operator corresponds to the implicit `value` parameter of the `set` accessor declaration.
-  For user-defined unary operators (including conversions), the single operand corresponds to the single parameter of the operator declaration.
-  For user-defined binary operators, the left operand corresponds to the first parameter, and the right operand corresponds to the second parameter of the operator declaration.

#### Run-time evaluation of argument lists

During the run-time processing of a function member invocation (§7.5.4), the expressions or variable references of an argument list are evaluated in order, from left to right, as follows:

-  For a value parameter, the argument expression is evaluated and an implicit conversion (§6.1) to the corresponding parameter type is performed. The resulting value becomes the initial value of the value parameter in the function member invocation.
-  For a reference or output parameter, the variable reference is evaluated and the resulting storage location becomes the storage location represented by the parameter in the function member invocation. If the variable reference given as a reference or output parameter is an array element of a *reference-type*, a run-time check is performed to ensure that the element type of the array is identical to the type of the parameter. If this check fails, a `System.ArrayTypeMismatchException` is thrown.

Methods, indexers, and instance constructors may declare their right-most parameter to be a parameter array (§10.6.1.4). Such function members are invoked either in their normal form or in their expanded form depending on which is applicable (§7.5.3.1):

-  When a function member with a parameter array is invoked in its normal form, the argument given for the parameter array must be a single expression that is implicitly convertible (§6.1) to the parameter array type. In this case, the parameter array acts precisely like a value parameter.
-  When a function member with a parameter array is invoked in its expanded form, the invocation must specify zero or more positional arguments for the parameter array, where each argument is an expression that is implicitly convertible (§6.1) to the element type of the parameter array. In this case, the invocation creates an instance of the parameter array type with a length corresponding to the number of arguments, initializes the elements of the array instance with the given argument values, and uses the newly created array instance as the actual argument.

The expressions of an argument list are always evaluated in the order they are written. Thus, the example

```csharp
class Test
{
    static void F(int x, int y = -1, int z = -2) {
        System.Console.WriteLine("x = {0}, y = {1}, z = {2}", x, y, z);
    }

    static void Main() {
        int i = 0;
        F(i++, i++, i++);
        F(z: i++, x: i++);
    }
}
```

produces the output

```csharp
x = 0, y = 1, z = 2
x = 4, y = -1, z = 3
```

The array co-variance rules (§12.5) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`. Because of these rules, when an array element of a *reference-type* is passed as a reference or output parameter, a run-time check is required to ensure that the actual element type of the array is identical to that of the parameter. In the example

```csharp
class Test
{
    static void F(ref object x) {...}

    static void Main() {
        object[] a = new object[10];
        object[] b = new string[10];
        F(ref a[0]);        // Ok
        F(ref b[1]);        // ArrayTypeMismatchException
    }
}
```

the second invocation of `F` causes a `System.ArrayTypeMismatchException` to be thrown because the actual element type of `b` is `string` and not `object`.

When a function member with a parameter array is invoked in its expanded form, the invocation is processed exactly as if an array creation expression with an array initializer (§7.6.10.4) was inserted around the expanded parameters. For example, given the declaration

```csharp
void F(int x, int y, params object[] args);
```

the following invocations of the expanded form of the method

```csharp
F(10, 20);
F(10, 20, 30, 40);
F(10, 20, 1, "hello", 3.0);
```

correspond exactly to

```csharp
F(10, 20, new object[] {});
F(10, 20, new object[] {30, 40});
F(10, 20, new object[] {1, "hello", 3.0});
```

In particular, note that an empty array is created when there are zero arguments given for the parameter array.

When arguments are omitted from a function member with corresponding optional parameters, the default arguments of the function member declaration are implicitly passed. Because these are always constant, their evaluation will not impact the evaluation order of the remaining arguments.

### Type inference

When a generic method is called without specifying type arguments, a *__type inference__* process attempts to infer type arguments for the call. The presence of type inference allows a more convenient syntax to be used for calling a generic method, and allows the programmer to avoid specifying redundant type information. For example, given the method declaration:

```csharp
class Chooser
{
    static Random rand = new Random();

    public static T Choose<T>(T first, T second) {
        return (rand.Next(2) == 0)? first: second;
    }
}
```

it is possible to invoke the `Choose` method without explicitly specifying a type argument:

```csharp
int i = Chooser.Choose(5, 213);                    // Calls Choose<int>

string s = Chooser.Choose("foo", "bar");        // Calls Choose<string>
```

Through type inference, the type arguments `int` and `string` are determined from the arguments to the method.

Type inference occurs as part of the binding-time processing of a method invocation (§7.6.5.1) and takes place before the overload resolution step of the invocation. When a particular method group is specified in a method invocation, and no type arguments are specified as part of the method invocation, type inference is applied to each generic method in the method group. If type inference succeeds, then the inferred type arguments are used to determine the types of arguments for subsequent overload resolution. If overload resolution chooses a generic method as the one to invoke, then the inferred type arguments are used as the actual type arguments for the invocation. If type inference for a particular method fails, that method does not participate in overload resolution. The failure of type inference, in and of itself, does not cause a binding-time error. However, it often leads to a binding-time error when overload resolution then fails to find any applicable methods.

If the supplied number of arguments is different than the number of parameters in the method, then inference immediately fails. Otherwise, assume that the generic method has the following signature:

```csharp
T<sub>r</sub> M<X<sub>1</sub>…X<sub>n</sub>>(T<sub>1</sub> x<sub>1</sub> … T<sub>m</sub> x<sub>m</sub>)
```

With a method call of the form `M(``E`<sub>1</sub>` …``E`<sub>m</sub>`)` the task of type inference is to find unique type arguments `S`<sub>1</sub>`…S`<sub>n</sub> for each of the type parameters `X`<sub>1</sub>`…X`<sub>n</sub> so that the call `M<S`<sub>1</sub>`…S`<sub>n</sub>`>(``E`<sub>1</sub>`…``E`<sub>m</sub>`)`becomes valid.

During the process of inference each type parameter `X`<sub>i</sub> is either *fixed* to a particular type `S`<sub>i</sub> or *unfixed* with an associated set of *bounds.* Each of the bounds is some type `T`. Initially each type variable `X`<sub>i</sub> is unfixed with an empty set of bounds.

Type inference takes place in phases. Each phase will try to infer type arguments for more type variables based on the findings of the previous phase. The first phase makes some initial inferences of bounds, whereas the second phase fixes type variables to specific types and infers further bounds. The second phase may have to be repeated a number of times.

*Note:* Type inference takes place not only when a generic method is called. Type inference for conversion of method groups is described in §7.5.2.13 and finding the best common type of a set of expressions is described in §7.5.2.14.

#### The first phase

For each of the method arguments `E`<sub>i</sub>:

<i:listitem level="0" type="9" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">If `E`<sub>i</sub> is an anonymous function, an explicit parameter type inference (§7.5.2.7) is made from `E`<sub>i</sub> to `T`<sub>i</sub></i:listitem><i:listitem level="0" type="9" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">Otherwise, if `E`<sub>i</sub> has a type `U` and `x`<sub>i</sub> is a value parameter then a *lower-bound inference* is made from `U` to `T`<sub>i</sub>.</i:listitem><i:listitem level="0" type="9" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">Otherwise, if `E`<sub>i</sub> has a type `U` and `x`<sub>i</sub> is a `ref` or `out` parameter then an exact inference is made from `U` to `T`<sub>i</sub>.</i:listitem><i:listitem level="0" type="9" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">Otherwise, no inference is made for this argument.</i:listitem>#### The second phase

The second phase proceeds as follows:

<i:listitem level="0" type="15" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">All unfixed type variables `X`<sub>i</sub> which do not depend on (§7.5.2.5) any `X`<sub>j</sub> are fixed (§7.5.2.10).</i:listitem><i:listitem level="0" type="15" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">If no such type variables exist, all unfixed type variables `X`<sub>i</sub> are fixed for which all of the following hold:</i:listitem><i:listitem level="1" type="15" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">There is at least one type variable `X`<sub>j</sub>``that depends on `X`<sub>i</sub></i:listitem><i:listitem level="1" type="15" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">`X`<sub>i</sub> has a non-empty set of bounds</i:listitem><i:listitem level="0" type="15" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">If no such type variables exist and there are still unfixed type variables, type inference fails.</i:listitem><i:listitem level="0" type="15" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">Otherwise, if no further unfixed type variables exist, type inference succeeds.</i:listitem><i:listitem level="0" type="15" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">Otherwise, for all arguments `E`<sub>i</sub> with corresponding parameter type `T`<sub>i</sub> where the output types (§7.5.2.4) contain unfixed type variables `X`<sub>j</sub> but the input types (§7.5.2.3) do not, an output type inference (§7.5.2.6) is made from `E`<sub>i</sub> to `T`<sub>i</sub>. Then the second phase is repeated.</i:listitem>#### Input types

If `E` is a method group or implicitly typed anonymous function and `T` is a delegate type or expression tree type then all the parameter types of `T` are *input types**of*`E`*with type*`T`.

####  Output types

If `E` is a method group or an anonymous function and `T` is a delegate type or expression tree type then the return type of `T` is an *output type**of*`E`*with type*`T`.

#### Dependence

An unfixed type variable `X`<sub>i</sub>*depends directly on* an unfixed type variable `X`<sub>j</sub> if for some argument `E`<sub>k</sub> with type `T`<sub>k</sub>`X`<sub>j</sub> occurs in an input type of `E`<sub>k</sub> with type `T`<sub>k</sub> and `X`<sub>i</sub> occurs in an output type of `E`<sub>k</sub> with type `T`<sub>k</sub>.

`X`<sub>j</sub>*depends on*`X`<sub>i</sub> if `X`<sub>j</sub> depends directly on `X`<sub>i</sub> or if `X`<sub>i</sub> depends directly on `X`<sub>k</sub> and `X`<sub>k</sub> depends on `X`<sub>j</sub>. Thus "depends on" is the transitive but not reflexive closure of "depends directly on".

#### Output type inferences

An output type inference is made from an expression `E` to a type `T` in the following way:

-  If `E` is an anonymous function with inferred return type  `U` (§7.5.2.12) and `T` is a delegate type or expression tree type with return type `T`<sub>b</sub>, then a lower-bound inference (§7.5.2.9) is made from `U` to `T`<sub>b</sub>.
-  Otherwise, if `E` is a method group and `T` is a delegate type or expression tree type with parameter types `T`<sub>1</sub>`…T`<sub>k</sub> and return type `T`<sub>b</sub>, and overload resolution of `E` with the types `T`<sub>1</sub>`…T`<sub>k</sub> yields a single method with return type `U`, then a lower-bound inference is made from `U` to `T`<sub>b</sub>.
-  Otherwise, if `E` is an expression with type `U`, then a lower-bound inference is made from `U` to `T`.
-  Otherwise, no inferences are made.

#### Explicit parameter type inferences

An explicit parameter type inference is made from an expression `E` to a type `T` in the following way:

-  If `E` is an explicitly typed anonymous function with parameter types `U`<sub>1</sub>`…U`<sub>k</sub> and `T` is a delegate type or expression tree type with parameter types `V`<sub>1</sub>`…V`<sub>k</sub> then for each `U`<sub>i</sub> an exact inference (§7.5.2.8) is made from `U`<sub>i</sub> to the corresponding `V`<sub>i</sub>.

#### Exact inferences

An exact inference from a type `U` to a type `V` is made as follows:

-  If `V` is one of the unfixed `X`<sub>i</sub> then `U` is added to the set of exact bounds for `X`<sub>i</sub>.
-  Otherwise, sets `V`<sub>1</sub>`…V`<sub>k</sub><sub />and `U`<sub>1</sub>`…U`<sub>k</sub>``are determined by checking if any of the following cases apply:
-  `V` is an array type `V`<sub>1</sub>`[…]```and```U` is an array type `U`<sub>1</sub>`[…]`  of the same rank
-  `V` is the type```V`<sub>1</sub>`?` and `U` is the type `U`<sub>1</sub>`?`
-  `V` is a constructed type `C<V`<sub>1</sub>`…V`<sub>k</sub>`>``and ``U` is a constructed type `C<U`<sub>1</sub>`…U`<sub>k</sub>`>```
-  If any of these cases apply then an exact inference is made *from* each `U`<sub>i</sub>*to* the corresponding `V`<sub>i</sub>.
-  Otherwise no inferences are made.

#### Lower-bound inferences

A lower-bound inference from a type `U` to a type `V` is made as follows:

-  If `V` is one of the unfixed `X`<sub>i</sub> then `U` is added to the set of lower bounds for `X`<sub>i</sub>.
-  Otherwise, if `V` is the type `V`<sub>1</sub>`?```and `U` is the type `U`<sub>1</sub>`?` then a lower bound inference is made from `U`<sub>1</sub> to `V`<sub>1</sub>.
-  Otherwise, sets `U`<sub>1</sub>`…U`<sub>k</sub>``and `V`<sub>1</sub>`…V`<sub>k</sub><sub />are determined by checking if any of the following cases apply:
-  `V` is an array type `V`<sub>1</sub>`[…]`and `U` is an array type `U`<sub>1</sub>`[…]` (or a type parameter whose effective base type is `U`<sub>1</sub>`[…]`) of the same rank
-  `V` is one of `IEnumerable<V`<sub>1</sub>`>`, `ICollection<V`<sub>1</sub>`>` or `IList<V`<sub>1</sub>`>` and `U` is a one-dimensional array type `U`<sub>1</sub>`[]`(or a type parameter whose effective base type is `U`<sub>1</sub>`[``]`)
-  `V` is a constructed class, struct, interface or delegate type `C<V`<sub>1</sub>`…V`<sub>k</sub>`>` and there is a unique type `C<``U`<sub>1</sub>`…``U`<sub>k</sub>`>```such that `U` (or, if `U` is a type parameter, its effective base class or any member of its effective interface set) is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) `C<``U`<sub>1</sub>`…``U`<sub>k</sub>`>``.`
-  (The "uniqueness" restriction means that in the case interface C<T>{} class U: C<X>, C<Y>{}, then no inference is made when inferring from `U` to C<T> because `U`<sub>1</sub> could be X or Y.)
-  If any of these cases apply then an inference is made *from* each `U`<sub>i</sub>*to* the corresponding `V`<sub>i</sub> as follows:
-          If  `U`<sub>i</sub><sub />is not known to be a reference type then an *exact inference* is made
-          Otherwise, if `U` is an array type then a *lower-bound inference* is made
-          Otherwise, if `V` is `C<V`<sub>1</sub>`…V`<sub>k</sub>`>` then inference depends on the i-th type parameter of `C`:
-          If it is covariant then a *lower-bound inference* is made.
-          If it is contravariant then an *upper-bound inference* is made.
-          If it is invariant then an *exact inference* is made.
-  Otherwise, no inferences are made.

#### Upper-bound inferences

An upper-bound inference from a type `U` to a type `V` is made as follows:

-  If `V` is one of the unfixed `X`<sub>i</sub> then `U` is added to the set of upper bounds for `X`<sub>i</sub>.
-  Otherwise, sets `V`<sub>1</sub>`…V`<sub>k</sub><sub />and```U`<sub>1</sub>`…U`<sub>k</sub> are determined by checking if any of the following cases apply:
-  `U` is an array type `U`<sub>1</sub>`[…]`and `V` is an array type `V`<sub>1</sub>`[…]`of the same rank
-  `U` is one of `IEnumerable<U`<sub>e</sub>`>`, `ICollection<U`<sub>e</sub>`>` or `IList<U`<sub>e</sub>`>` and `V` is a one-dimensional array type `V`<sub>e</sub>`[]`
-  `U` is the type `U`<sub>1</sub>`?` and `V` is the type```V`<sub>1</sub>`?`
-  `U` is constructed class, struct, interface or delegate type `C<U`<sub>1</sub>`…U`<sub>k</sub>`>` and `V` is a class, struct, interface or delegate type which is identical to, inherits from (directly or indirectly), or implements (directly or indirectly) a unique type `C<V`<sub>1</sub>`…V`<sub>k</sub>`>`
-  (The "uniqueness" restriction means that if we have interface C<T>{} class V<Z>: C<X<Z>>, C<Y<Z>>{}, then no inference is made when inferring from `C<U`<sub>1</sub>`>` to V<Q>. Inferences are not made from `U`<sub>1</sub> to either X<Q> or Y<Q>.)
-  If any of these cases apply then an inference is made *from* each `U`<sub>i</sub>*to* the corresponding `V`<sub>i</sub> as follows:
-          If  `U`<sub>i</sub><sub />is not known to be a reference type then an *exact inference* is made
-          Otherwise, if `V` is an array type then an *upper-bound inference* is made
-          Otherwise, if `U` is `C<``U`<sub>1</sub>`…``U`<sub>k</sub>`>` then inference depends on the i-th type parameter of `C`:
-          If it is covariant then an *upper-bound inference* is made.
-          If it is contravariant then a *lower-bound inference* is made.
-          If it is invariant then an *exact inference* is made.
-  Otherwise, no inferences are made.   

#### Fixing

An unfixed type variable `X`<sub>i</sub> with a set of bounds is fixed as follows:

-  The set of *candidate types*`U`<sub>j</sub> starts out as the set of all types in the set of bounds for `X`<sub>i</sub>.
-  We then examine each bound for `X`<sub>i</sub> in turn: For each exact bound `U` of `X`<sub>i</sub> all types `U`<sub>j</sub> which are not identical to `U` are removed from the candidate set. For each lower bound `U` of `X`<sub>i</sub> all types `U`<sub>j</sub> to which there is *not* an implicit conversion from `U` are removed from the candidate set. For each upper bound `U` of `X`<sub>i</sub> all types `U`<sub>j</sub> from which there is *not* an implicit conversion to `U` are removed from the candidate set.
-  If among the remaining candidate types `U`<sub>j</sub> there is a unique type `V` from which there is an implicit conversion to all the other candidate types, then `X`<sub>i</sub> is fixed to `V`.
-  Otherwise, type inference fails.

#### Inferred return type

The inferred return type of an anonymous function `F` is used during type inference and overload resolution. The inferred return type can only be determined for an anonymous function where all parameter types are known, either because they are explicitly given, provided through an anonymous function conversion or inferred during type inference on an enclosing generic method invocation.

The *__inferred result type__* is determined as follows:

-  If the body of `F` is an *expression* that has a type, then the inferred result type of `F` is the type of that expression.
-  If the body of `F` is a *block* and the set of expressions in the block's `return` statements has a best common type `T` (§7.5.2.14), then the inferred result type of `F` is `T`.
-  Otherwise, a result type cannot be inferred for `F`.

The *__inferred return type__* is determined as follows:

-  If `F` is async and the body of `F` is either an expression classified as nothing (§7.1), or a statement block where no return statements have expressions, the inferred return type is `System.Threading.Tasks.Task`
-  If `F` is async and has an inferred result type `T`, the inferred return type is `System.Threading.Tasks.Task<T>`.
-  If `F` is non-async and has an inferred result type `T`, the inferred return type is `T`.
-  Otherwise a return type cannot be inferred for `F`.

As an example of type inference involving anonymous functions, consider the `Select` extension method declared in the `System.``Linq.Enumerable` class:

```csharp
namespace System.Linq
{
    public static class Enumerable
    {
        public static IEnumerable<TResult> Select<TSource,TResult>(
            this IEnumerable<TSource> source,
            Func<TSource,TResult> selector)
        {
            foreach (TSource element in source) yield return selector(element);
        }
    }
}
```

Assuming the `System.``Linq` namespace was imported with a `using` clause, and given a class `Customer` with a `Name` property of type `string`, the `Select` method can be used to select the names of a list of customers:

```csharp
List<Customer> customers = GetCustomerList();
IEnumerable<string> names = customers.Select(c => c.Name);
```

The extension method invocation (§7.6.5.2) of `Select` is processed by rewriting the invocation to a static method invocation:

```csharp
IEnumerable<string> names = Enumerable.Select(customers, c => c.Name);
```

Since type arguments were not explicitly specified, type inference is used to infer the type arguments. First, the `customers` argument is related to the `source` parameter, inferring `T` to be `Customer`. Then, using the anonymous function type inference process described above, `c` is given type `Customer`, and the expression `c.Name` is related to the return type of the `selector` parameter, inferring `S` to be `string`. Thus, the invocation is equivalent to

```csharp
Sequence.Select<Customer,string>(customers, (Customer c) => c.Name)
```

and the result is of type `IEnumerable<string>`.

The following example demonstrates how anonymous function type inference allows type information to "flow" between arguments in a generic method invocation. Given the method:

```csharp
static Z F<X,Y,Z>(X value, Func<X,Y> f1, Func<Y,Z> f2) {
    return f2(f1(value));
}
```

Type inference for the invocation:

```csharp
double seconds = F("1:15:30", s => TimeSpan.Parse(s), t => t.TotalSeconds);
```

proceeds as follows: First, the argument `"1:15:30"` is related to the `value` parameter, inferring `X` to be `string`. Then, the parameter of the first anonymous function, `s`, is given the inferred type `string`, and the expression `TimeSpan.Parse(s)` is related to the return type of `f1`, inferring `Y` to be `System.TimeSpan`. Finally, the parameter of the second anonymous function, `t`, is given the inferred type `System.TimeSpan`, and the expression `t.TotalSeconds` is related to the return type of `f2`, inferring `Z` to be `double`. Thus, the result of the invocation is of type `double`.

#### Type inference for conversion of method groups

Similar to calls of generic methods, type inference must also be applied when a method group `M` containing a generic method is converted to a given delegate type `D`` (``§````6.6``)`. Given a method

```csharp
T<sub>r</sub> M<X<sub>1</sub>…X<sub>n</sub>>(T<sub>1</sub> x<sub>1</sub> … T<sub>m</sub> x<sub>m</sub>)
```

and the method group `M` being assigned to the delegate type `D` the task of type inference is to find type arguments `S`<sub>1</sub>`…S`<sub>n</sub> so that the expression:

```csharp
M<S<sub>1</sub>…S<sub>n</sub>>
```

becomes compatible (§15.1) with `D`.

Unlike the type inference algorithm for generic method calls, in this case there are only argument types, no argument expressions. In particular, there are no anonymous functions and hence no need for multiple phases of inference.

Instead, all `X`<sub>i</sub> are considered unfixed, and a lower-bound inference is made from each argument type `U`<sub>j</sub> of `D` to the corresponding parameter type `T`<sub>j</sub> of `M`. If for any of the `X`<sub>i</sub> no bounds were found, type inference fails. Otherwise, all `X`<sub>i</sub> are fixed to corresponding `S`<sub>i</sub>, which are the result of type inference.

#### Finding the best common type of a set of expressions

In some cases, a common type needs to be inferred for a set of expressions. In particular, the element types of implicitly typed arrays and the return types of anonymous functions with *block* bodies are found in this way.

Intuitively, given a set of expressions `E`<sub>1</sub>`…``E`<sub>m</sub> this inference should be equivalent to calling a method

```csharp
T<sub>r</sub> M<X>(X x<sub>1</sub> … X x<sub>m</sub>)
```

with the `E`<sub>i</sub> as arguments.

More precisely, the inference starts out with an unfixed type variable `X`. Output type inferences are then made from each `E`<sub>i</sub> to `X`. Finally, `X` is fixed and, if successful, the resulting type `S` is the resulting best common type for the expressions. If no such `S` exists, the expressions have no best common type.

### Overload resolution

Overload resolution is a binding-time mechanism for selecting the best function member to invoke given an argument list and a set of candidate function members. Overload resolution selects the function member to invoke in the following distinct contexts within C#:

-  Invocation of a method named in an *invocation-expression* (§7.6.5.1).
-  Invocation of an instance constructor named in an *object-creation-expression* (§7.6.10.1).
-  Invocation of an indexer accessor through an *element-access* (§7.6.6).
-  Invocation of a predefined or user-defined operator referenced in an expression (§7.3.3 and §7.3.4).

Each of these contexts defines the set of candidate function members and the list of arguments in its own unique way, as described in detail in the sections listed above. For example, the set of candidates for a method invocation does not include methods marked `override` (§7.4), and methods in a base class are not candidates if any method in a derived class is applicable (§7.6.5.1).

Once the candidate function members and the argument list have been identified, the selection of the best function member is the same in all cases:

-  Given the set of applicable candidate function members, the best function member in that set is located. If the set contains only one function member, then that function member is the best function member. Otherwise, the best function member is the one function member that is better than all other function members with respect to the given argument list, provided that each function member is compared to all other function members using the rules in §7.5.3.2. If there is not exactly one function member that is better than all other function members, then the function member invocation is ambiguous and a binding-time error occurs.

The following sections define the exact meanings of the terms *__applicable function member__* and *__better function member__*.

#### Applicable function member

A function member is said to be an *__applicable function member__* with respect to an argument list `A` when all of the following are true:

-  Each argument in `A` corresponds to a parameter in the function member declaration as described in §7.5.1.1, and any parameter to which no argument corresponds is an optional parameter.
-  For each argument in `A`, the parameter passing mode of the argument (i.e., value, `ref`, or `out`) is identical to the parameter passing mode of the corresponding parameter, and

for a value parameter or a parameter array, an implicit conversion (§6.1) exists from the argument to the type of the corresponding parameter, or

for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter. After all, a `ref` or `out` parameter is an alias for the argument passed.

For a function member that includes a parameter array, if the function member is applicable by the above rules, it is said to be applicable in its *__normal form__*. If a function member that includes a parameter array is not applicable in its normal form, the function member may instead be applicable in its *__expanded form__*:

-  The expanded form is constructed by replacing the parameter array in the function member declaration with zero or more value parameters of the element type of the parameter array such that the number of arguments in the argument list `A` matches the total number of parameters. If `A` has fewer arguments than the number of fixed parameters in the function member declaration, the expanded form of the function member cannot be constructed and is thus not applicable.
-  Otherwise, the expanded form is applicable if for each argument in `A` the parameter passing mode of the argument is identical to the parameter passing mode of the corresponding parameter, and

for a fixed value parameter or a value parameter created by the expansion, an implicit conversion (§6.1) exists from the type of the argument to the type of the corresponding parameter, or

for a `ref` or `out` parameter, the type of the argument is identical to the type of the corresponding parameter.

#### Better function member

For the purposes of determining the better function member, a stripped-down argument list A is constructed containing just the argument expressions themselves in the order they appear in the original argument list.

Parameter lists for each of the candidate function members are constructed in the following way:

-  The expanded form is used if the function member was applicable only in the expanded form.
-  Optional parameters with no corresponding arguments are removed from the parameter list
-  The parameters are reordered so that they occur at the same position as the corresponding argument in the argument list.

Given an argument list `A` with a set of argument expressions { `E`<sub>1</sub>, `E`<sub>2</sub>, ..., `E`<sub>N</sub> } and two applicable function members `M`<sub>P</sub> and `M`<sub>Q</sub> with parameter types { `P`<sub>1</sub>, `P`<sub>2</sub>, ..., `P`<sub>N</sub> } and { `Q`<sub>1</sub>, `Q`<sub>2</sub>, ..., `Q`<sub>N</sub> }, `M`<sub>P</sub> is defined to be a *__better function member__* than `M`<sub>Q</sub> if

-  for each argument, the implicit conversion from `E`<sub>X</sub> to `Q`<sub>X</sub> is not better than the implicit conversion from `E`<sub>X</sub> to `P`<sub>X</sub>, and
-  for at least one argument, the conversion from `E`<sub>X</sub> to `P`<sub>X</sub> is better than the conversion from `E`<sub>X</sub> to `Q`<sub>X</sub>.

When performing this evaluation, if `M`<sub>P</sub> or `M`<sub>Q</sub> is applicable in its expanded form, then `P`<sub>X</sub> or `Q`<sub>X</sub> refers to a parameter in the expanded form of the parameter list.

In case the parameter type sequences `{``P`<sub>1</sub>, `P`<sub>2</sub>, …, `P`<sub>N</sub>`}` and `{``Q`<sub>1</sub>, `Q`<sub>2</sub>, …, `Q`<sub>N</sub>`}` are equivalent (i.e. each `P`<sub>i</sub> has an identity conversion to the corresponding `Q`<sub>i</sub>), the following tie-breaking rules are applied, in order, to determine the better function member.

-  If `M`<sub>P</sub> is a non-generic method and `M`<sub>Q</sub> is a generic method, then `M`<sub>P</sub> is better than `M`<sub>Q</sub>.
-  Otherwise, if `M`<sub>P</sub> is applicable in its normal form and `M`<sub>Q</sub> has a `params` array and is applicable only in its expanded form, then `M`<sub>P</sub> is better than `M`<sub>Q</sub>.
-  Otherwise, if `M`<sub>P</sub> has more declared parameters than `M`<sub>Q</sub>, then `M`<sub>P</sub> is better than `M`<sub>Q</sub>. This can occur if both methods have `params` arrays and are applicable only in their expanded forms.
-  Otherwise if all parameters of `M`<sub>P</sub> have a corresponding argument whereas default arguments need to be substituted for at least one optional parameter in `M`<sub>Q</sub> then `M`<sub>P</sub> is better than `M`<sub>Q</sub>.
-  Otherwise, if `M`<sub>P</sub> has more specific parameter types than `M`<sub>Q</sub>, then `M`<sub>P</sub> is better than `M`<sub>Q</sub>. Let `{R`<sub>1</sub>, `R`<sub>2</sub>, …, `R`<sub>N</sub>`}` and `{S`<sub>1</sub>, `S`<sub>2</sub>, …, `S`<sub>N</sub>`}` represent the uninstantiated and unexpanded parameter types of `M`<sub>P</sub> and `M`<sub>Q</sub>. `M`<sub>P</sub>'s parameter types are more specific than `M`<sub>Q</sub>'s if, for each parameter, `R`<sub>X</sub> is not less specific than `S`<sub>X</sub>, and, for at least one parameter, `R`<sub>X</sub> is more specific than `S`<sub>X</sub>:

A type parameter is less specific than a non-type parameter.

Recursively, a constructed type is more specific than another constructed type (with the same number of type arguments) if at least one type argument is more specific and no type argument is less specific than the corresponding type argument in the other.

An array type is more specific than another array type (with the same number of dimensions) if the element type of the first is more specific than the element type of the second.

-  Otherwise if one member is a non-lifted operator and  the other is a lifted operator, the non-lifted one is better.
-  Otherwise, neither function member is better.

#### Better conversion from expression

Given an implicit conversion `C`<sub>1</sub> that converts from an expression `E` to a type `T`<sub>1</sub>, and an implicit conversion `C`<sub>2</sub> that converts from an expression `E` to a type `T`<sub>2</sub>, C<sub>1</sub> is a *__better conversion__* than C<sub>2</sub> if at least one of the following holds:

<i:listitem level="0" type="8" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">E has a type S and an identity conversion exists from `S` to `T`<sub>1</sub> but not from `S` to `T`<sub>2</sub></i:listitem>- E is not an anonymous function and T<sub>1</sub> is a better conversion target than T<sub>2</sub> (§7.5.3.5)
<i:listitem level="0" type="8" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">E is an anonymous function, T<sub>1 </sub>is either a delegate type D<sub>1 </sub>or an expression tree type `Expression<`D<sub>1</sub>`>`, T<sub>2 </sub>is either a delegate type D<sub>2 </sub>or an expression tree type `Expression<`D<sub>2</sub>`>` and one of the following holds:</i:listitem>D<sub>1</sub> is a better conversion target than D<sub>2</sub>

D<sub>1 </sub>and D<sub>2</sub> have identical parameter lists, and one of the following holds:

D<sub>1 </sub>has a return type Y<sub>1</sub>, and D<sub>2</sub> has a return type Y<sub>2</sub>, an inferred return type X exists for E in the context of that parameter list (§7.5.2.12), and the conversion from X to Y<sub>1 </sub>is better than the conversion from X to Y<sub>2</sub>

`E` is async, `D`<sub>1</sub> has a return type `Task<Y`<sub>1</sub>`>`, and `D`<sub>2</sub> has a return type `Task<Y`<sub>2</sub>`>`, an inferred return type `Task<X>` exists for `E` in the context of that parameter list (§7.5.2.12), and the conversion from `X` to `Y`<sub>1</sub> is better than the conversion from `X` to `Y`<sub>2</sub>

D<sub>1 </sub>has a return type Y, and D<sub>2</sub> is void returning

#### Better conversion from type

Given a conversion `C`<sub>1</sub> that converts from a type `S` to a type `T`<sub>1</sub>, and a conversion `C`<sub>2</sub> that converts from a type `S` to a type `T`<sub>2</sub>, C<sub>1</sub> is a *__better conversion__* than C<sub>2</sub> if at least one of the following holds:

<i:listitem level="0" type="8" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">An identity conversion exists from `S` to `T`<sub>1</sub> but not from `S` to `T`<sub>2</sub></i:listitem><i:listitem level="0" type="8" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">T<sub>1</sub> is a better conversion target than T<sub>2</sub> (§7.5.3.5)</i:listitem>#### Better conversion target

Given two different types T<sub>1</sub> and T<sub>2</sub>, T<sub>1</sub> is a better conversion target than T<sub>2</sub> if at least one of the following holds:

-  An implicit conversion from `T`<sub>1</sub> to `T`<sub>2</sub> exists, and no implicit conversion from `T`<sub>2</sub> to `T`<sub>1</sub> exists
-  `T`<sub>1</sub> is a signed integral type and `T`<sub>2</sub> is an unsigned integral type. Specifically:

`T`<sub>1</sub> is `sbyte` and `T`<sub>2</sub> is `byte`, `ushort`, `uint`, or `ulong`

`T`<sub>1</sub> is `short` and `T`<sub>2</sub> is `ushort`, `uint`, or `ulong`

`T`<sub>1</sub> is `int` and `T`<sub>2</sub> is `uint`, or `ulong`

`T`<sub>1</sub> is `long` and `T`<sub>2</sub> is `ulong`

#### Overloading in generic classes

While signatures as declared must be unique, it is possible that substitution of type arguments results in identical signatures. In such cases, the tie-breaking rules of overload resolution above will pick the most specific member.

The following examples show overloads that are valid and invalid according to this rule:

```csharp
interface I1<T> {...}

interface I2<T> {...}

class G1<U>
{
    int F1(U u);                    // Overload resulotion for G<int>.F1
    int F1(int i);                    // will pick non-generic

    void F2(I1<U> a);                // Valid overload
    void F2(I2<U> a);
}

class G2<U,V>
{
    void F3(U u, V v);            // Valid, but overload resolution for
    void F3(V v, U u);            // G2<int,int>.F3 will fail

    void F4(U u, I1<V> v);        // Valid, but overload resolution for    
   void F4(I1<V> v, U u);        // G2<I1<int>,int>.F4 will fail

    void F5(U u1, I1<V> v2);    // Valid overload
    void F5(V v1, U u2);

    void F6(ref U u);                // valid overload
    void F6(out V v);
}
```

### Compile-time checking of dynamic overload resolution

For most dynamically bound operations the set of possible candidates for resolution is unknown at compile-time. In certain cases, however the candidate set is known at compile-time:

-  Static method calls with dynamic arguments
-  Instance method calls where the receiver is not a dynamic expression
-  Indexer calls where the receiver is not a dynamic expression
-  Constructor calls with dynamic arguments

In these cases a limited compile-time check is performed for each candidate to see if any of them could possibly apply at run-time.This check consists of the following steps:

-  Partial type inference: Any type argument that does not depend directly or indirectly on an argument of type `dynamic` is inferred using the rules of §7.5.2. The remaining type arguments are unknown.
-  Partial applicability check: Applicability is checked according to §7.5.3.1, but ignoring parameters whose types are unknown.
-  If no candidate passes this test, a compile-time error occurs.

### Function member invocation

This section describes the process that takes place at run-time to invoke a particular function member. It is assumed that a binding-time process has already determined the particular member to invoke, possibly by applying overload resolution to a set of candidate function members.

For purposes of describing the invocation process, function members are divided into two categories:

-  Static function members. These are instance constructors, static methods, static property accessors, and user-defined operators. Static function members are always non-virtual.
-  Instance function members. These are instance methods, instance property accessors, and indexer accessors. Instance function members are either non-virtual or virtual, and are always invoked on a particular instance. The instance is computed by an instance expression, and it becomes accessible within the function member as `this` (§7.6.7).

The run-time processing of a function member invocation consists of the following steps, where `M` is the function member and, if `M` is an instance member, `E` is the instance expression:

-  If `M` is a static function member:

The argument list is evaluated as described in §7.5.1.

`M` is invoked.

-  If `M` is an instance function member declared in a *value-type*:

`E` is evaluated. If this evaluation causes an exception, then no further steps are executed.

If `E` is not classified as a variable, then a temporary local variable of `E`'s type is created and the value of `E` is assigned to that variable. `E` is then reclassified as a reference to that temporary local variable. The temporary variable is accessible as `this` within `M`, but not in any other way. Thus, only when `E` is a true variable is it possible for the caller to observe the changes that `M` makes to `this`.

The argument list is evaluated as described in §7.5.1.

`M` is invoked. The variable referenced by `E` becomes the variable referenced by `this`.

-  If `M` is an instance function member declared in a *reference-type*:

`E` is evaluated. If this evaluation causes an exception, then no further steps are executed.

The argument list is evaluated as described in §7.5.1.

If the type of `E` is a *value-type*, a boxing conversion (§4.3.1) is performed to convert `E` to type `object`, and `E` is considered to be of type `object` in the following steps. In this case, `M` could only be a member of `System.Object`.

The value of `E` is checked to be valid. If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.

The function member implementation to invoke is determined:

If the binding-time type of `E` is an interface, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`. This function member is determined by applying the interface mapping rules (§13.4.4) to determine the implementation of `M` provided by the run-time type of the instance referenced by `E`.

Otherwise, if `M` is a virtual function member, the function member to invoke is the implementation of `M` provided by the run-time type of the instance referenced by `E`. This function member is determined by applying the rules for determining the most derived implementation (§10.6.3) of `M` with respect to the run-time type of the instance referenced by `E`.

Otherwise, `M` is a non-virtual function member, and the function member to invoke is `M` itself.

The function member implementation determined in the step above is invoked. The object referenced by `E` becomes the object referenced by `this`.

#### Invocations on boxed instances

A function member implemented in a *value-type* can be invoked through a boxed instance of that *value-type* in the following situations:

-  When the function member is an `override` of a method inherited from type `object` and is invoked through an instance expression of type `object`.
-  When the function member is an implementation of an interface function member and is invoked through an instance expression of an *interface-type*.
-  When the function member is invoked through a delegate.

In these situations, the boxed instance is considered to contain a variable of the *value-type*, and this variable becomes the variable referenced by `this` within the function member invocation. In particular, this means that when a function member is invoked on a boxed instance, it is possible for the function member to modify the value contained in the boxed instance.

## Primary expressions

Primary expressions include the simplest forms of expressions.

<pre>primary-expression: 
primary-no-array-creation-expression
array-creation-expression</pre>

<pre>primary-no-array-creation-expression:
literal
simple-name
parenthesized-expression
member-access
invocation-expression
element-access
this-access
base-access
post-increment-expression
post-decrement-expression
object-creation-expression
delegate-creation-expression
anonymous-object-creation-expression
typeof-expression
 checked-expression
unchecked-expression 
default-value-expression
anonymous-method-expression</pre>

Primary expressions are divided between *array-creation-expressions* and *primary-no-array-creation-expression*s. Treating array-creation-expression in this way, rather than listing it along with the other simple expression forms, enables the grammar to disallow potentially confusing code such as

```csharp
object o = new int[3][1];
```

which would otherwise be interpreted as

```csharp
object o = (new int[3])[1];
```

### Literals

A *primary-expression* that consists of a *literal* (§2.4.4) is classified as a value.

### Simple names

A *simple-name* consists of an identifier, optionally followed by a type argument list:

<pre>simple-name:
identifier   type-argument-list<sub>opt</sub></pre>

A *simple-name* is either of the form `I` or of the form `I<A`<sub>1</sub>`,` ...`,``A`<sub>K</sub>`>`, where `I` is a single identifier and `<A`<sub>1</sub>`,` ...`,``A`<sub>K</sub>`>` is an optional *type-argument-list*. When no *type-argument-list* is specified, consider `K` to be zero. The *simple-name* is evaluated and classified as follows:

-  If `K` is zero and the *simple-name* appears within a *block* and if the *block*'s (or an enclosing *block*'s) local variable declaration space (§3.3) contains a local variable, parameter or constant with name `I`, then the *simple-name* refers to that local variable, parameter or constant and is classified as a variable or value.
-  If `K` is zero and the *simple-name* appears within the body of a generic method declaration and if that declaration includes a type parameter with name `I`, then the *simple-name* refers to that type parameter.
-  Otherwise, for each instance type `T` (§10.3.1), starting with the instance type of the immediately enclosing type declaration and continuing with the instance type of each enclosing class or struct declaration (if any):

If `K` is zero and the declaration of `T` includes a type parameter with name `I`, then the *simple-name* refers to that type parameter.

Otherwise, if a member lookup (§7.4) of `I` in `T` with `K` type arguments produces a match:

If `T` is the instance type of the immediately enclosing class or struct type and the lookup identifies one or more methods, the result is a method group with an associated instance expression of `this`. If a type argument list was specified, it is used in calling a generic method (§7.6.5.1).

Otherwise, if `T` is the instance type of the immediately enclosing class or struct type, if the lookup identifies an instance member, and if the reference occurs within the *block* of an instance constructor, an instance method, or an instance accessor, the result is the same as a member access (§7.6.4) of the form `this.I`. This can only happen when `K` is zero.

Otherwise, the result is the same as a member access (§7.6.4) of the form `T.I` or `T.I<A`<sub>1</sub>`,` ...`,``A`<sub>K</sub>`>`. In this case, it is a binding-time error for the *simple-name* to refer to an instance member.

-  Otherwise, for each namespace `N`, starting with the namespace in which the *simple-name* occurs, continuing with each enclosing namespace (if any), and ending with the global namespace, the following steps are evaluated until an entity is located:

If `K` is zero and `I` is the name of a namespace in `N`, then:

If the location where the *simple-name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern-alias-directive* or *using-alias-directive* that associates the name `I` with a namespace or type, then the *simple**-name* is ambiguous and a compile-time error occurs.

Otherwise, the *simple**-name* refers to the namespace named `I` in `N`.

Otherwise, if `N` contains an accessible type having name `I` and `K` type parameters, then:

If `K` is zero and the location where the *simple-name* occurs is enclosed by a namespace declaration for `N` and the namespace declaration contains an *extern-alias-directive* or *using-alias-directive* that associates the name `I` with a namespace or type, then the *simple**-name* is ambiguous and a compile-time error occurs.

Otherwise, the *namespace-or-type-name* refers to the type constructed with the given type arguments.

Otherwise, if the location where the *simple-name* occurs is enclosed by a namespace declaration for `N`:

If `K` is zero and the namespace declaration contains an *extern-alias-directive* or *using-alias-directive* that associates the name `I` with an imported namespace or type, then the *simple-name* refers to that namespace or type.

Otherwise, if the namespaces imported by the *using-namespace-directive*s of the namespace declaration contain exactly one type having name `I` and `K` type parameters, then the *simple-name* refers to that type constructed with the given type arguments.

Otherwise, if the namespaces imported by the *using-namespace-directive*s of the namespace declaration contain more than one type having name `I` and `K` type parameters, then the *simple-name* is ambiguous and an error occurs.

Note that this entire step is exactly parallel to the corresponding step in the processing of a *namespace-or-type-name* (§3.8).

-  Otherwise, the *simple-name* is undefined and a compile-time error occurs.

#### Invariant meaning in blocks

For each occurrence of a given identifier as a full *simple-name* (without a type argument list) in an expression or declarator, within the local variable declaration space (§3.3) immediately enclosing that occurrence, every other occurrence of the same identifier as a full *simple-name* in an expression or declarator must refer to the same entity. This rule ensures that the meaning of a name is always the same within a given block, switch block, for-, foreach- or using-statement, or anonymous function.

The example

```csharp
class Test
{
    double x;

    void F(bool b) {
        x = 1.0;
        if (b) {
            int x;
            x = 1;
        }
    }
}
```

results in a compile-time error because `x` refers to different entities within the outer block (the extent of which includes the nested block in the `if` statement). In contrast, the example

```csharp
class Test
{
    double x;

    void F(bool b) {
        if (b) {
            x = 1.0;
        }
        else {
            int x;
            x = 1;
        }
    }
}
```

is permitted because the name `x` is never used in the outer block.

Note that the rule of invariant meaning applies only to simple names. It is perfectly valid for the same identifier to have one meaning as a simple name and another meaning as right operand of a member access (§7.6.4). For example:

```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }
}
```

The example above illustrates a common pattern of using the names of fields as parameter names in an instance constructor. In the example, the simple names `x` and `y` refer to the parameters, but that does not prevent the member access expressions `this.x` and `this.y` from accessing the fields.

### Parenthesized expressions

A *parenthesized-expression* consists of an *expression* enclosed in parentheses.

<pre>parenthesized-expression:
<b>(</b>   expression   <b>)</b></pre>

A *parenthesized-expression* is evaluated by evaluating the *expression* within the parentheses. If the *expression* within the parentheses denotes a namespace or type, a compile-time error occurs. Otherwise, the result of the *parenthesized-expression* is the result of the evaluation of the contained *expression*.

### Member access

A *member-access* consists of a *primary-expression*, a *predefined-type*, or a *qualified-alias-member*, followed by a "`.`" token, followed by an *identifier*, optionally followed by a *type-argument-list*.

<pre>member-access:
primary-expression   <b>.</b>   identifier   type-argument-list<sub>opt</sub>
predefined-type   <b>.</b>   identifier   type-argument-list<sub>opt</sub>
qualified-alias-member   <b>.</b>   identifier   type-argument-list<sub>opt</sub></pre>

<pre>predefined-type:  one of
<b>bool</b><b>byte</b><b>char</b><b>decimal</b><b>double</b><b>float</b><b>int</b><b>long</b>
<b>object</b><b>sbyte</b><b>short</b><b>string</b><b>uint</b><b>ulong</b><b>ushort</b></pre>

The *qualified-alias-member* production is defined in §9.7.

A *member-access* is either of the form `E.I` or of the form `E.I<A`<sub>1</sub>`,` ...`,``A`<sub>K</sub>`>`, where `E` is a primary-expression, `I` is a single identifier and `<A`<sub>1</sub>`,` ...`,``A`<sub>K</sub>`>` is an optional *type-argument-list*. When no *type-argument-list* is specified, consider `K` to be zero.

A *member-access* with a *primary-expression* of type `dynamic` is dynamically bound (§7.2.2). In this case the compiler classifies the member access as a property access of type `dynamic`. The rules below to determine the meaning of the *member-access* are then applied at run-time, using the run-time type instead of the compile-time type of the *primary-expression*. If this run-time classification leads to a method group, then the member access must be the *primary-expression* of an *invocation-expression*.

The *member-access* is evaluated and classified as follows:

-  If `K` is zero and `E` is a namespace and `E` contains a nested namespace with name `I`, then the result is that namespace.
-  Otherwise, if `E` is a namespace and `E` contains an accessible type having name `I` and `K` type parameters, then the result is that type constructed with the given type arguments.
-  If `E` is a *predefined-type* or a *primary-expression* classified as a type, if `E` is not a type parameter, and if a member lookup (§7.4) of `I` in `E` with `K` type parameters produces a match, then `E.I` is evaluated and classified as follows:

If `I` identifies a type, then the result is that type constructed with the given type arguments.

If `I` identifies one or more methods, then the result is a method group with no associated instance expression. If a type argument list was specified, it is used in calling a generic method (§7.6.5.1).

If `I` identifies a `static` property, then the result is a property access with no associated instance expression.

If `I` identifies a `static` field:

If the field is `readonly` and the reference occurs outside the static constructor of the class or struct in which the field is declared, then the result is a value, namely the value of the static field `I` in `E`.

Otherwise, the result is a variable, namely the static field `I` in `E`.

If `I` identifies a `static` event:

If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event-accessor-declarations* (§10.8), then `E.I` is processed exactly as if `I` were a static field.

Otherwise, the result is an event access with no associated instance expression.

If `I` identifies a constant, then the result is a value, namely the value of that constant.

If `I` identifies an enumeration member, then the result is a value, namely the value of that enumeration member.

Otherwise, `E.I` is an invalid member reference, and a compile-time error occurs.

-  If `E` is a property access, indexer access, variable, or value, the type of which is `T`, and a member lookup (§7.4) of `I` in `T` with `K` type arguments produces a match, then `E.I` is evaluated and classified as follows:

First, if `E` is a property or indexer access, then the value of the property or indexer access is obtained (§7.1.1) and `E` is reclassified as a value.

If `I` identifies one or more methods, then the result is a method group with an associated instance expression of `E`. If a type argument list was specified, it is used in calling a generic method (§7.6.5.1).

If `I` identifies an instance property, then the result is a property access with an associated instance expression of `E`.

If `T` is a *class-type* and `I` identifies an instance field of that *class-type*:

If the value of `E` is `null`, then a `System.NullReferenceException` is thrown.

Otherwise, if the field is `readonly` and the reference occurs outside an instance constructor of the class in which the field is declared, then the result is a value, namely the value of the field `I` in the object referenced by `E`.

Otherwise, the result is a variable, namely the field `I` in the object referenced by `E`.

If `T` is a *struct-type* and `I` identifies an instance field of that *struct-type*:

If `E` is a value, or if the field is `readonly` and the reference occurs outside an instance constructor of the struct in which the field is declared, then the result is a value, namely the value of the field `I` in the struct instance given by `E`.

Otherwise, the result is a variable, namely the field `I` in the struct instance given by `E`.

If `I` identifies an instance event:

If the reference occurs within the class or struct in which the event is declared, and the event was declared without *event-accessor-declarations* (§10.8), and the reference does not occur as the left-hand side of a `+=` or `-=` operator, then `E.I` is processed exactly as if `I` was an instance field.

Otherwise, the result is an event access with an associated instance expression of `E`.

-  Otherwise, an attempt is made to process```E.I` as an extension method invocation (§7.6.5.2). If this fails, `E.I` is an invalid member reference, and a binding-time error occurs.

#### Identical simple names and type names

In a member access of the form `E.I`, if `E` is a single identifier, and if the meaning of `E` as a *simple-name* (§7.6.2) is a constant, field, property, local variable, or parameter with the same type as the meaning of `E` as a *type-name* (§3.8), then both possible meanings of `E` are permitted. The two possible meanings of `E.I` are never ambiguous, since `I` must necessarily be a member of the type `E` in both cases. In other words, the rule simply permits access to the static members and nested types of `E` where a compile-time error would otherwise have occurred. For example:

```csharp
struct Color
{
    public static readonly Color White = new Color(...);
    public static readonly Color Black = new Color(...);

    public Color Complement() {...}
}

class A
{
    public Color Color;                    // Field Color of type Color

    void F() {
        Color = Color.Black;             // References Color.Black static member
        Color = Color.Complement();    // Invokes Complement() on Color field
    }

    static void G() {
        Color c = Color.White;            // References Color.White static member
    }
}
```

Within the `A` class, those occurrences of the `Color` identifier that reference the `Color` type are underlined, and those that reference the `Color` field are not underlined.

#### Grammar ambiguities

The productions for *simple-name* (§7.6.2) and *member-access* (§7.6.4) can give rise to ambiguities in the grammar for expressions. For example, the statement:

```csharp
F(G<A,B>(7));
```

could be interpreted as a call to `F` with two arguments, `G``<``A` and `B``>``(7)`. Alternatively, it could be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument.

If a sequence of tokens can be parsed (in context) as a *simple-name* (§7.6.2), *member-access* (§7.6.4), or *pointer-member-access* (§18.5.2) ending with a *type-argument-list* (§4.4.1), the token immediately following the closing `>` token is examined. If it is one of

```csharp
`(  )  ]````}``  :  ;  ,  .  ?  ==  !=``  |  ^`
```

then the *type-argument-list* is retained as part of the *simple-name*, *member-access* or *pointer-member-access* and any other possible parse of the sequence of tokens is discarded. Otherwise, the *type-argument-list* is not considered to be part of the *simple-name*, *member-access* or *pointer-member-access*, even if there is no other possible parse of the sequence of tokens. Note that these rules are not applied when parsing a *type-argument-list* in a *namespace-or-type-name* (§3.8). The statement

```csharp
F(G<A,B>(7));
```

will, according to this rule, be interpreted as a call to `F` with one argument, which is a call to a generic method `G` with two type arguments and one regular argument. The statements

```csharp
F(G < A, B > 7);
F(G < A, B >> 7);
```

will each be interpreted as a call to `F` with two arguments. The statement

```csharp
x = F < A > +y;
```

will be interpreted as a less than operator, greater than operator, and unary plus operator, as if the statement had been written `x``=``(F``<``A)``>``(+y)`, instead of as a *simple-name* with a *type-argument-list* followed by a binary plus operator. In the statement

```csharp
x = y is C<T> + z;
```

the tokens `C<T>` are interpreted as a *namespace-or-type-name* with a *type-argument-list*.

### Invocation expressions

An *invocation-expression* is used to invoke a method.

<pre>invocation-expression:
primary-expression   <b>(</b>   argument-list<sub>opt</sub><b>)</b></pre>

An *invocation-expression* is dynamically bound (§7.2.2) if at least one of the following holds:

<i:listitem level="0" type="25" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">The *primary-expression* has compile-time type `dynamic`.</i:listitem><i:listitem level="0" type="25" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">At least one argument of the optional *argument-list* has compile-time type `dynamic` and the *primary-expression* does not have a delegate type.</i:listitem>In this case the compiler classifies the *invocation**-expression* as a value of type `dynamic`. The rules below to determine the meaning of the *invocation**-expression* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary-expression* and arguments which have the compile-time type `dynamic`. If the *primary-expression* does not have compile-time type `dynamic`, then the method invocation undergoes a limited compile time check as described in §7.5.4.

The *primary-expression* of an *invocation-expression* must be a method group or a value of a *delegate-type*. If the *primary-expression* is a method group, the *invocation-expression* is a method invocation (§7.6.5.1). If the *primary-expression* is a value of a *delegate-type*, the *invocation-expression* is a delegate invocation (§7.6.5.3). If the *primary-expression* is neither a method group nor a value of a *delegate-type*, a binding-time error occurs.

The optional *argument-list* (§7.5.1) provides values or variable references for the parameters of the method.

The result of evaluating an *invocation-expression* is classified as follows:

-  If the *invocation-expression* invokes a method or delegate that returns `void`, the result is nothing. An expression that is classified as nothing is permitted only in the context of a *statement-expression* (§8.6) or as the body of a *lambda-expression* (§7.15). Otherwise a binding-time error occurs.
-  Otherwise, the result is a value of the type returned by the method or delegate.

#### Method invocations

For a method invocation, the *primary-expression* of the *invocation-expression* must be a method group. The method group identifies the one method to invoke or the set of overloaded methods from which to choose a specific method to invoke. In the latter case, determination of the specific method to invoke is based on the context provided by the types of the arguments in the *argument-list*.

The binding-time processing of a method invocation of the form `M(A)`, where `M` is a method group (possibly including a *type-argument-list*), and `A` is an optional *argument-list*, consists of the following steps:

-  The set of candidate methods for the method invocation is constructed. For each method `F` associated with the method group `M`:

If `F` is non-generic, `F` is a candidate when:

`M` has no type argument list, and

`F` is applicable with respect to `A` (§7.5.3.1).

If `F` is generic and `M` has no type argument list, `F` is a candidate when:

Type inference (§7.5.2) succeeds, inferring a list of type arguments for the call, and

Once the inferred type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints (§4.4.4), and the parameter list of `F` is applicable with respect to `A` (§7.5.3.1).

If `F` is generic and `M` includes a type argument list, `F` is a candidate when:

`F` has the same number of method type parameters as were supplied in the type argument list, and

Once the type arguments are substituted for the corresponding method type parameters, all constructed types in the parameter list of F satisfy their constraints (§4.4.4), and the parameter list of `F` is applicable with respect to `A` (§7.5.3.1).

-  The set of candidate methods is reduced to contain only methods from the most derived types: For each method `C.F` in the set, where `C` is the type in which the method `F` is declared, all methods declared in a base type of `C` are removed from the set. Furthermore, if `C` is a class type other than `object`, all methods declared in an interface type are removed from the set. (This latter rule only has affect when the method group was the result of a member lookup on a type parameter having an effective base class other than object and a non-empty effective interface set.)
-  If the resulting set of candidate methods is empty, then further processing along the following steps are abandoned, and instead an attempt is made to process the invocation as an extension method invocation (§7.6.5.2). If this fails, then no applicable methods exist, and a binding-time error occurs.
-  The best method of the set of candidate methods is identified using the overload resolution rules of §7.5.3. If a single best method cannot be identified, the method invocation is ambiguous, and a binding-time error occurs. When performing overload resolution, the parameters of a generic method are considered after substituting the type arguments (supplied or inferred) for the corresponding method type parameters.
-  Final validation of the chosen best method is performed:

The method is validated in the context of the method group: If the best method is a static method, the method group must have resulted from a *simple-name* or a *member-access* through a type. If the best method is an instance method, the method group must have resulted from a *simple-name*, a *member-access* through a variable or value, or a *base-access*. If neither of these requirements is true, a binding-time error occurs.

If the best method is a generic method, the type arguments (supplied or inferred) are checked against the constraints (§4.4.4) declared on the generic method. If any type argument does not satisfy the corresponding constraint(s) on the type parameter, a binding-time error occurs.

Once a method has been selected and validated at binding-time by the above steps, the actual run-time invocation is processed according to the rules of function member invocation described in §7.5.4.

The intuitive effect of the resolution rules described above is as follows: To locate the particular method invoked by a method invocation, start with the type indicated by the method invocation and proceed up the inheritance chain until at least one applicable, accessible, non-override method declaration is found. Then perform type inference and overload resolution on the set of applicable, accessible, non-override methods declared in that type and invoke the method thus selected. If no method was found, try instead to process the invocation as an extension method invocation.

#### Extension method invocations

In a method invocation (§7.5.5.1) of one of the forms

```csharp
*expr* . *identifier* ( )

*expr* . *identifier* ( *args* )

*expr* . *identifier* < *typeargs* > ( )

*expr* . *identifier* < *typeargs* > ( *args* )
```

if the normal processing of the invocation finds no applicable methods, an attempt is made to process the construct as an extension method invocation. If *expr* or any of the *args* has compile-time type `dynamic`, extension methods will not apply.

The objective is to find the best *type-name*`C`, so that the corresponding static method invocation can take place:

```csharp
C . *identif**i**er* ( *expr* )

C . *identifier* ( *expr* , *args* )

C . *identif**i**er* < *typeargs* > ( *expr* )

C . *identifier* < *typeargs* > ( *expr* , *args* )
```

An extension method `C`<sub>i</sub>`.``M`<sub>j</sub> is *__eligible__* if:

-  `C`<sub>i</sub> is a non-generic, non-nested class
-  The name of `M`<sub>j</sub> is *identifier*
-  `M`<sub>j</sub> is accessible and applicable when applied to the arguments as a static method as shown above
-  An implicit identity, reference or boxing conversion exists from *expr* to the type of the first parameter of `M`<sub>j</sub>`.`

The search for `C` proceeds as follows:

-  Starting with the closest enclosing namespace declaration, continuing with each enclosing namespace declaration, and ending with the containing compilation unit, successive attempts are made to find a candidate set of extension methods:

If the given namespace or compilation unit directly contains non-generic type declarations `C`<sub>i</sub> with eligible extension methods `M`<sub>j</sub>, then the set of those extension methods is the candidate set.

If namespaces imported by using namespace directives in the given namespace or compilation unit directly contain non-generic type declarations `C`<sub>i</sub> with eligible extension methods `M`<sub>j</sub>, then the set of those extension methods is the candidate set.

-  If no candidate set is found in any enclosing namespace declaration or compilation unit, a compile-time error occurs.
-  Otherwise, overload resolution is applied to the candidate set as described in (§7.5.3). If no single best method is found, a compile-time error occurs.
-  `C` is the type within which the best method is declared as an extension method.
-  Using `C` as a target, the method call is then processed as a static method invocation (§7.5.4).

The preceding rules mean that instance methods take precedence over extension methods, that extension methods available in inner namespace declarations take precedence over extension methods available in outer namespace declarations, and that extension methods declared directly in a namespace take precedence over extension methods imported into that same namespace with a using namespace directive. For example:

```csharp
public static class E
{
    public static void F(this object obj, int i) { }

    public static void F(this object obj, string s) { }
}

class A { }

class B
{
    public void F(int i) { }
}

class C
{
    public void F(object obj) { }
}

class X
{
    static void Test(A a, B b, C c) {
        a.F(1);                // E.F(object, int)
        a.F("hello");        // E.F(object, string)

        b.F(1);                // B.F(int)
        b.F("hello");        // E.F(object, string)

        c.F(1);                // C.F(object)
        c.F("hello");        // C.F(object)
    }
}
```

In the example, `B`'s method takes precedence over the first extension method, and `C`'s method takes precedence over both extension methods.

```csharp
public static class C
{
    public static void F(this int i) { Console.WriteLine("C.F({0})", i); }
    public static void G(this int i) { Console.WriteLine("C.G({0})", i); }
    public static void H(this int i) { Console.WriteLine("C.H({0})", i); }
}

namespace N1
{
    public static class D
    {
        public static void F(this int i) { Console.WriteLine("D.F({0})", i); }
        public static void G(this int i) { Console.WriteLine("D.G({0})", i); }
    }
}

namespace N2
{
    using N1;

    public static class E
    {
        public static void F(this int i) { Console.WriteLine("E.F({0})", i); }
    }

    class Test
    {
        static void Main(string[] args)
        {
            1.F();
            2.G();
            3.H();
        }
    }
}
```

The output of this example is:

```csharp
E.F(1)
D.G(2)
C.H(3)
```

`D.G` takes precendece over `C.G`, and `E.F` takes precedence over both `D.F` and `C.F`.

#### Delegate invocations

For a delegate invocation, the *primary-expression* of the *invocation-expression* must be a value of a *delegate-type*. Furthermore, considering the *delegate-type* to be a function member with the same parameter list as the *delegate-type*, the *delegate-type* must be applicable (§7.5.3.1) with respect to the *argument-list* of the *invocation-expression*.

The run-time processing of a delegate invocation of the form `D(A)`, where `D` is a *primary-expression* of a *delegate-type* and `A` is an optional *argument-list*, consists of the following steps:

-  `D` is evaluated. If this evaluation causes an exception, no further steps are executed.
-  The value of `D` is checked to be valid. If the value of `D` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.
-  Otherwise, `D` is a reference to a delegate instance. Function member invocations (§7.5.4) are performed on each of the callable entities in the invocation list of the delegate. For callable entities consisting of an instance and instance method, the instance for the invocation is the instance contained in the callable entity.

### Element access

An *element-access* consists of a *primary-no-array-creation-expression*, followed by a "`[`" token, followed by an *argument**-list*, followed by a "`]`" token. The *argument**-list* consists of one or more *argument*s, separated by commas.

<pre>element-access:
primary-no-array-creation-expression   <b>[</b>   argument-list   <b>]</b></pre>

The *argument-list* of an *element-access* is not allowed to contain `ref` or `out` arguments.

An *element-access* is dynamically bound (§7.2.2) if at least one of the following holds:

<i:listitem level="0" type="25" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">The *primary-no-array-creation-expression* has compile-time type `dynamic`.</i:listitem><i:listitem level="0" type="25" xmlns:i="urn:docx2md:intermediary" xmlns:w="http://schemas.openxmlformats.org/wordprocessingml/2006/main" xmlns:r="http://schemas.openxmlformats.org/officeDocument/2006/relationships" xmlns:rels="http://schemas.openxmlformats.org/package/2006/relationships" xmlns:a="http://schemas.openxmlformats.org/drawingml/2006/main" xmlns:wp="http://schemas.openxmlformats.org/drawingml/2006/wordprocessingDrawing" xmlns:cp="http://schemas.openxmlformats.org/package/2006/metadata/core-properties">At least one expression of the *argument**-list* has compile-time type `dynamic` and the *primary-no-array-creation-expression* does not have an array type.</i:listitem>In this case the compiler classifies the *element-access* as a value of type `dynamic`. The rules below to determine the meaning of the *element-access* are then applied at run-time, using the run-time type instead of the compile-time type of those of the *primary-no-array-creation-expression* and *argument**-list* expressions which have the compile-time type `dynamic`. If the *primary-no-array-creation-expression* does not have compile-time type `dynamic`, then the element access undergoes a limited compile time check as described in §7.5.4.

If the *primary-no-array-creation-expression* of an *element-access* is a value of an *array-type*, the *element-access* is an array access (§7.6.6.1). Otherwise, the *primary-no-array-creation-expression* must be a variable or value of a class, struct, or interface type that has one or more indexer members, in which case the *element-access* is an indexer access (§7.6.6.2).

#### Array access

For an array access, the *primary-no-array-creation-expression* of the *element-access* must be a value of an *array-type*. Furthermore, the *argument-list* of an array access is not allowed to contain named arguments.The number of expressions in the *argument**-list* must be the same as the rank of the *array-type*, and each expression must be of type `int`, `uint`, `long`, `ulong`, or must be implicitly convertible to one or more of these types.

The result of evaluating an array access is a variable of the element type of the array, namely the array element selected by the value(s) of the expression(s) in the *argument**-list*.

The run-time processing of an array access of the form `P[A]`, where `P` is a *primary-no-array-creation-expression* of an *array-type* and `A` is an *argument**-list*, consists of the following steps:

-  `P` is evaluated. If this evaluation causes an exception, no further steps are executed.
-  The index expressions of the *argument**-list* are evaluated in order, from left to right. Following evaluation of each index expression, an implicit conversion (§6.1) to one of the following types is performed: `int`, `uint`, `long`, `ulong`. The first type in this list for which an implicit conversion exists is chosen. For instance, if the index expression is of type `short` then an implicit conversion to `int` is performed, since implicit conversions from `short` to `int` and from `short` to `long` are possible. If evaluation of an index expression or the subsequent implicit conversion causes an exception, then no further index expressions are evaluated and no further steps are executed.
-  The value of `P` is checked to be valid. If the value of `P` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.
-  The value of each expression in the *argument**-list* is checked against the actual bounds of each dimension of the array instance referenced by `P`. If one or more values are out of range, a `System.IndexOutOfRangeException` is thrown and no further steps are executed.
-  The location of the array element given by the index expression(s) is computed, and this location becomes the result of the array access.

#### Indexer access

For an indexer access, the *primary-no-array-creation-expression* of the *element-access* must be a variable or value of a class, struct, or interface type, and this type must implement one or more indexers that are applicable with respect to the *argument**-list* of the *element-access*.

The binding-time processing of an indexer access of the form `P[A]`, where `P` is a *primary-no-array-creation-expression* of a class, struct, or interface type `T`, and `A` is an *argument**-list*, consists of the following steps:

-  The set of indexers provided by `T` is constructed. The set consists of all indexers declared in `T` or a base type of `T` that are not `override` declarations and are accessible in the current context (§3.5).
-  The set is reduced to those indexers that are applicable and not hidden by other indexers. The following rules are applied to each indexer `S.I` in the set, where `S` is the type in which the indexer `I` is declared:

If `I` is not applicable with respect to `A` (§7.5.3.1), then `I` is removed from the set.

If `I` is applicable with respect to `A` (§7.5.3.1), then all indexers declared in a base type of `S` are removed from the set.

If `I` is applicable with respect to `A` (§7.5.3.1) and `S` is a class type other than `object`, all indexers declared in an interface are removed from the set.

-  If the resulting set of candidate indexers is empty, then no applicable indexers exist, and a binding-time error occurs.
-  The best indexer of the set of candidate indexers is identified using the overload resolution rules of §7.5.3. If a single best indexer cannot be identified, the indexer access is ambiguous, and a binding-time error occurs.
-  The index expressions of the *argument**-list* are evaluated in order, from left to right. The result of processing the indexer access is an expression classified as an indexer access. The indexer access expression references the indexer determined in the step above, and has an associated instance expression of `P` and an associated argument list of `A`.

Depending on the context in which it is used, an indexer access causes invocation of either the *get-accessor* or the *set-accessor* of the indexer. If the indexer access is the target of an assignment, the *set-accessor* is invoked to assign a new value (§7.17.1). In all other cases, the *get-accessor* is invoked to obtain the current value (§7.1.1).

### This access

A *this-access* consists of the reserved word `this`.

<pre>this-access:
<b>this</b></pre>

A *this-access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor. It has one of the following meanings:

-  When `this` is used in a *primary-expression* within an instance constructor of a class, it is classified as a value. The type of the value is the instance type (§10.3.1) of the class within which the usage occurs, and the value is a reference to the object being constructed.
-  When `this` is used in a *primary-expression* within an instance method or instance accessor of a class, it is classified as a value. The type of the value is the instance type (§10.3.1) of the class within which the usage occurs, and the value is a reference to the object for which the method or accessor was invoked.
-  When `this` is used in a *primary-expression* within an instance constructor of a struct, it is classified as a variable. The type of the variable is the instance type (§10.3.1) of the struct within which the usage occurs, and the variable represents the struct being constructed. The `this` variable of an instance constructor of a struct behaves exactly the same as an `out` parameter of the struct type—in particular, this means that the variable must be definitely assigned in every execution path of the instance constructor.
-  When `this` is used in a *primary-expression* within an instance method or instance accessor of a struct, it is classified as a variable. The type of the variable is the instance type (§10.3.1) of the struct within which the usage occurs.

If the method or accessor is not an iterator (§10.14), the `this` variable represents the struct for which the method or accessor was invoked, and behaves exactly the same as a `ref` parameter of the struct type.

If the method or accessor is an iterator, the `this` variable represents a copy of the struct for which the method or accessor was invoked, and behaves exactly the same as a value parameter of the struct type.

Use of `this` in a *primary-expression* in a context other than the ones listed above is a compile-time error. In particular, it is not possible to refer to `this` in a static method, a static property accessor, or in a *variable-initializer* of a field declaration.

### Base access

A *base-access* consists of the reserved word `base` followed by either a "`.`" token and an identifier or an *argument**-list* enclosed in square brackets:

<pre>base-access:
<b>base</b><b>.</b>   identifier
<b>base</b><b>[</b>   argument-list   <b>]</b></pre>

A *base-access* is used to access base class members that are hidden by similarly named members in the current class or struct. A *base-access* is permitted only in the *block* of an instance constructor, an instance method, or an instance accessor. When `base.I` occurs in a class or struct, `I` must denote a member of the base class of that class or struct. Likewise, when `base[E]` occurs in a class, an applicable indexer must exist in the base class.

At binding-time, *base-access* expressions of the form `base.I` and `base[E]` are evaluated exactly as if they were written `((B)this).I` and `((B)this)[E]`, where `B` is the base class of the class or struct in which the construct occurs. Thus, `base.I` and `base[E]` correspond to `this.I` and `this[E]`, except `this` is viewed as an instance of the base class.

When a *base-access* references a virtual function member (a method, property, or indexer), the determination of which function member to invoke at run-time (§7.5.4) is changed. The function member that is invoked is determined by finding the most derived implementation (§10.6.3) of the function member with respect to `B` (instead of with respect to the run-time type of `this`, as would be usual in a non-base access). Thus, within an `override` of a `virtual` function member, a *base-access* can be used to invoke the inherited implementation of the function member. If the function member referenced by a *base-access* is abstract, a binding-time error occurs.

### Postfix increment and decrement operators

<pre>post-increment-expression:
primary-expression   <b>++</b></pre>

<pre>post-decrement-expression:
primary-expression   <b>--</b></pre>

The operand of a postfix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access. The result of the operation is a value of the same type as the operand.

If the *primary-expression***has the compile-time type `dynamic` then the operator is dynamically bound (§7.2.2), the *post-increment-expression* or *post-de**crement-expression* has the compile-time type `dynamic` and the following rules are applied at run-time using the run-time type of the *primary-expression*.

If the operand of a postfix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor. If this is not the case, a binding-time error occurs.

Unary operator overload resolution (§7.3.3) is applied to select a specific operator implementation. Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type. The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand. In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.

The run-time processing of a postfix increment or decrement operation of the form `x++` or `x--` consists of the following steps:

-  If `x` is classified as a variable:

`x` is evaluated to produce the variable.

The value of `x` is saved.

The selected operator is invoked with the saved value of `x` as its argument.

The value returned by the operator is stored in the location given by the evaluation of `x`.

The saved value of `x` becomes the result of the operation.

-  If `x` is classified as a property or indexer access:

The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.

The `get` accessor of `x` is invoked and the returned value is saved.

The selected operator is invoked with the saved value of `x` as its argument.

The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.

The saved value of `x` becomes the result of the operation.

The `++` and `--` operators also support prefix notation (§7.7.5). Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation. In either case, `x` itself has the same value after the operation.

An `operator``++` or `operator``--` implementation can be invoked using either postfix or prefix notation. It is not possible to have separate operator implementations for the two notations.

### The new operator

The `new` operator is used to create new instances of types.

There are three forms of `new` expressions:

-  Object creation expressions are used to create new instances of class types and value types.
-  Array creation expressions are used to create new instances of array types.
-  Delegate creation expressions are used to create new instances of delegate types.

The `new` operator implies creation of an instance of a type, but does not necessarily imply dynamic allocation of memory. In particular, instances of value types require no additional memory beyond the variables in which they reside, and no dynamic allocations occur when `new` is used to create instances of value types.

#### Object creation expressions

An *object-creation-expression* is used to create a new instance of a *class-type* or a *value-type*.

<pre>object-creation-expression:
<b>new</b>   type   <b>(</b>   argument-list<sub>opt</sub><b>)</b>   object-or-collection-initializer<sub>opt</sub>
<b>new</b>   type   object-or-collection-initializer</pre>

<pre>object-or-collection-initializer:
object-initializer
collection-initializer</pre>

The *type* of an *object-creation-expression* must be a *class-type*, a *value-type* or a***type-parameter*. The *type* cannot be an `abstract`*class-type*.

The optional *argument-list* (§7.5.1) is permitted only if the *type* is a *class-type* or a *struct-type*.

An object creation expression can omit the constructor argument list and enclosing parentheses provided it includes an object initializer or collection initializer. Omitting the constructor argument list and enclosing parentheses is equivalent to specifying an empty argument list.

Processing of an object creation expression that includes an object initializer or collection initializer consists of first processing the instance constructor and then processing the member or element initializations specified by the object initializer (§7.6.10.2) or collection initializer (§7.6.10.3).

If any of the arguments in the optional *argument-list* has the compile-time type `dynamic` then the *object-creation-expression* is dynamically bound (§7.2.2) and the following rules are applied at run-time using the run-time type of those arguments of the *argument-list* that have the compile time type `dynamic`. However, the object creation undergoes a limited compile time check as described in §7.5.4.

The binding-time processing of an *object-creation-expression* of the form `new``T(A)`, where `T` is a *class-type* or a *value-type* and `A` is an optional *argument-list*, consists of the following steps:

-  If `T` is a *value-type* and `A` is not present:

The *object-creation-expression* is a default constructor invocation. The result of the *object-creation-expression* is a value of type `T`, namely the default value for `T` as defined in §4.1.1.

-  Otherwise, if `T` is a *type-parameter* and `A` is not present:

If no value type constraint or constructor constraint (§10.1.5) has been specified for `T`, a binding-time error occurs.

The result of the *object-creation-expression* is a value of the run-time type that the type parameter has been bound to, namely the result of invoking the default constructor of that type. The run-time type may be a reference type or a value type.

-  Otherwise, if `T` is a *class-type* or a *struct-type*:

If `T` is an `abstract`*class-type*, a compile-time error occurs.

The instance constructor to invoke is determined using the overload resolution rules of §7.5.3. The set of candidate instance constructors consists of all accessible instance constructors declared in `T` which are applicable with respect to `A` (§7.5.3.1). If the set of candidate instance constructors is empty, or if a single best instance constructor cannot be identified, a binding-time error occurs.

The result of the *object-creation-expression* is a value of type `T`, namely the value produced by invoking the instance constructor determined in the step above.

-  Otherwise, the *object-creation-expression* is invalid, and a binding-time error occurs.

Even if the *object-creation-expression* is dynamically bound, the compile-time type is still `T`.

The run-time processing of an *object-creation-expression* of the form `new``T(A)`, where `T` is *class-type* or a *struct-type* and `A` is an optional *argument-list*, consists of the following steps:

-  If `T` is a *class-type*:

A new instance of class `T` is allocated. If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.

All fields of the new instance are initialized to their default values (§5.2).

The instance constructor is invoked according to the rules of function member invocation (§7.5.4). A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.

-  If `T` is a *struct-type*:

An instance of type `T` is created by allocating a temporary local variable. Since an instance constructor of a *struct-type* is required to definitely assign a value to each field of the instance being created, no initialization of the temporary variable is necessary.

The instance constructor is invoked according to the rules of function member invocation (§7.5.4). A reference to the newly allocated instance is automatically passed to the instance constructor and the instance can be accessed from within that constructor as `this`.

#### Object initializers

An *__object initializer__* specifies values for zero or more fields or properties of an object.

<pre>object-initializer:
<b>{</b>   member-initializer-list<sub>opt</sub><b>}</b>
<b>{</b>   member-initializer-list   <b>,</b><b>}</b></pre>

<pre>member-initializer-list:
member-initializer
member-initializer-list   <b>,</b>   member-initializer</pre>

<pre>member-initializer:
identifier   =   initializer-value</pre>

<pre>initializer-value:
expression
object-or-collection-initializer</pre>

An object initializer consists of a sequence of member initializers, enclosed by `{` and `}` tokens and separated by commas. Each member initializer must name an accessible field or property of the object being initialized, followed by an equals sign and an expression or an object initializer or collection initializer. It is an error for an object initializer to include more than one member initializer for the same field or property. It is not possible for the object initializer to refer to the newly created object it is initializing.

A member initializer that specifies an expression after the equals sign is processed in the same way as an assignment (§7.17.1) to the field or property.

A member initializer that specifies an object initializer after the equals sign is a *__nested object initializer__*, i.e. an initialization of an embedded object. Instead of assigning a new value to the field or property, the assignments in the nested object initializer are treated as assignments to members of the field or property. Nested object initializers cannot be applied to properties with a value type, or to read-only fields with a value type.

A member initializer that specifies a collection initializer after the equals sign is an initialization of an embedded collection. Instead of assigning a new collection to the field or property, the elements given in the initializer are added to the collection referenced by the field or property. The field or property must be of a collection type that satisfies the requirements specified in §7.6.10.3.

The following class represents a point with two coordinates:

```csharp
public class Point
{
    int x, y;

    public int X { get { return x; } set { x = value; } }
    public int Y { get { return y; } set { y = value; } }
}
```

An instance of `Point` can be created and initialized as follows:

```csharp
Point a = new Point { X = 0, Y = 1 };
```

which has the same effect as

```csharp
Point __a = new Point();
__a.X = 0;
__a.Y = 1; 
Point a = __a;
```

where `__a` is an otherwise invisible and inaccessible temporary variable. The following class represents a rectangle created from two points:

```csharp
public class Rectangle
{
    Point p1, p2;

    public Point P1 { get { return p1; } set { p1 = value; } }
    public Point P2 { get { return p2; } set { p2 = value; } }
}
```

An instance of `Rectangle` can be created and initialized as follows:

```csharp
Rectangle r = new Rectangle {
    P1 = new Point { X = 0, Y = 1 },
    P2 = new Point { X = 2, Y = 3 }
};
```

which has the same effect as

```csharp
Rectangle __r = new Rectangle();
Point __p1 = new Point();
__p1.X = 0;
__p1.Y = 1;
__r.P1 = __p1;
Point __p2 = new Point();
__p2.X = 2;
__p2.Y = 3;
__r.P2 = __p2; 
Rectangle r = __r;

```

where `__``r`, `__``p1` and `__p2` are temporary variables that are otherwise invisible and inaccessible.

If `Rectangle's` constructor allocates the two embedded `Point` instances

```csharp
public class Rectangle
{
    Point p1 = new Point();
    Point p2 = new Point();

    public Point P1 { get { return p1; } }
    public Point P2 { get { return p2; } }
}
```

the following construct can be used to initialize the embedded `Point` instances instead of assigning new instances:

```csharp
Rectangle r = new Rectangle {
    P1 = { X = 0, Y = 1 },
    P2 = { X = 2, Y = 3 }
};
```

which has the same effect as

```csharp
Rectangle __r = new Rectangle();
__r.P1.X = 0;
__r.P1.Y = 1;
__r.P2.X = 2;
__r.P2.Y = 3;
Rectangle r = __r;
```

#### Collection initializers

A collection initializer specifies the elements of a collection.

<pre>collection-initializer:
<b>{</b>   element-initializer-list   <b>}</b>
<b>{</b>   element-initializer-list   <b>,</b><b>}</b></pre>

<pre>element-initializer-list:
element-initializer
element-initializer-list   <b>,</b>   element-initializer</pre>

<pre>element-initializer:
non-assignment-expression
<b>{</b>   expression-list   <b>}</b></pre>

<pre>expression-list:
expression
expression-list   <b>,</b>   expression</pre>

A collection initializer consists of a sequence of element initializers, enclosed by `{` and `}` tokens and separated by commas. Each element initializer specifies an element to be added to the collection object being initialized, and consists of a list of expressions enclosed by `{` and `}` tokens and separated by commas.  A single-expression element initializer can be written without braces, but cannot then be an assignment expression, to avoid ambiguity with member initializers. The *non-assignment-expression* production is defined in §7.18.

The following is an example of an object creation expression that includes a collection initializer:

```csharp
List<int> digits = new List<int> { 0, 1, 2, 3, 4, 5, 6, 7, 8, 9 };
```

The collection object to which a collection initializer is applied must be of a type that implements `System.Collections.``IEnumerable` or a compile-time error occurs. For each specified element in order, the collection initializer invokes an `Add` method on the target object with the expression list of the element initializer as argument list, applying normal overload resolution for each invocation. Thus, the collection object must contain an applicable `Add` method for each element initializer.

The following class represents a contact with a name and a list of phone numbers:

```csharp
public class Contact
{
    string name;
    List<string> phoneNumbers = new List<string>();

    public string Name { get { return name; } set { name = value; } }

    public List<string> PhoneNumbers { get { return phoneNumbers; } }
}
```

A `List<Contact>` can be created and initialized as follows:

```csharp
var contacts = new List<Contact> {
    new Contact {
        Name = "Chris Smith",
        PhoneNumbers = { "206-555-0101", "425-882-8080" }
    },
    new Contact {
        Name = "Bob Harris",
        PhoneNumbers = { "650-555-0199" }
    }
};
```

which has the same effect as

```csharp
var __clist = new List<Contact>();
Contact __c1 = new Contact();
__c1.Name = "Chris Smith";
__c1.PhoneNumbers.Add("206-555-0101");
__c1.PhoneNumbers.Add("425-882-8080");
__clist.Add(__c1);
Contact __c2 = new Contact();
__c2.Name = "Bob Harris";
__c2.PhoneNumbers.Add("650-555-0199");
__clist.Add(__c2);
var contacts = __clist;
```

where `__clist`, `__c``1` and `__c``2` are temporary variables that are otherwise invisible and inaccessible.

#### Array creation expressions

An *array-creation-expression* is used to create a new instance of an *array-type*.

<pre>array-creation-expression:
<b>new</b>   non-array-type   <b>[</b>   expression-list   <b>]</b>   rank-specifiers<sub>opt</sub>   array-initializer<sub>opt</sub>
<b>new</b>   array-type   array-initializer 
<b>new</b>   rank-specifier   array-initializer</pre>

An array creation expression of the first form allocates an array instance of the type that results from deleting each of the individual expressions from the expression list. For example, the array creation expression `new``int[10,``20]` produces an array instance of type `int[,]`, and the array creation expression `new``int[10][,]` produces an array of type `int[][,]`. Each expression in the expression list must be of type `int`, `uint`, `long`, or `ulong`, or implicitly convertible to one or more of these types. The value of each expression determines the length of the corresponding dimension in the newly allocated array instance. Since the length of an array dimension must be nonnegative, it is a compile-time error to have a *constant-expression* with a negative value in the expression list.

Except in an unsafe context (§18.1), the layout of arrays is unspecified.

If an array creation expression of the first form includes an array initializer, each expression in the expression list must be a constant and the rank and dimension lengths specified by the expression list must match those of the array initializer.

In an array creation expression of the second or third form, the rank of the specified array type or rank specifier must match that of the array initializer. The individual dimension lengths are inferred from the number of elements in each of the corresponding nesting levels of the array initializer. Thus, the expression

```csharp
new int[,] {{0, 1}, {2, 3}, {4, 5}}
```

exactly corresponds to

```csharp
new int[3, 2] {{0, 1}, {2, 3}, {4, 5}}
```

An array creation expression of the third form is referred to as an *__implicitly typed array creation expression__*. It is similar to the second form, except that the element type of the array is not explicitly given, but determined as the best common type (§7.5.2.14) of the set of expressions in the array initializer. For a multidimensional array, i.e., one where the *rank-specifier* contains at least one comma, this set comprises all *expression*s found in nested *array-initializer*s.

Array initializers are described further in §12.6.

The result of evaluating an array creation expression is classified as a value, namely a reference to the newly allocated array instance. The run-time processing of an array creation expression consists of the following steps:

-  The dimension length expressions of the *expression-list* are evaluated in order, from left to right. Following evaluation of each expression, an implicit conversion (§6.1) to one of the following types is performed: `int`, `uint`, `long`, `ulong`. The first type in this list for which an implicit conversion exists is chosen. If evaluation of an expression or the subsequent implicit conversion causes an exception, then no further expressions are evaluated and no further steps are executed.
-  The computed values for the dimension lengths are validated as follows. If one or more of the values are less than zero, a `System.OverflowException` is thrown and no further steps are executed.
-  An array instance with the given dimension lengths is allocated. If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.
-  All elements of the new array instance are initialized to their default values (§5.2).
-  If the array creation expression contains an array initializer, then each expression in the array initializer is evaluated and assigned to its corresponding array element. The evaluations and assignments are performed in the order the expressions are written in the array initializer—in other words, elements are initialized in increasing index order, with the rightmost dimension increasing first. If evaluation of a given expression or the subsequent assignment to the corresponding array element causes an exception, then no further elements are initialized (and the remaining elements will thus have their default values).

An array creation expression permits instantiation of an array with elements of an array type, but the elements of such an array must be manually initialized. For example, the statement

```csharp
int[][] a = new int[100][];
```

creates a single-dimensional array with 100 elements of type `int[]`. The initial value of each element is `null`. It is not possible for the same array creation expression to also instantiate the sub-arrays, and the statement

```csharp
int[][] a = new int[100][5];        // Error
```

results in a compile-time error. Instantiation of the sub-arrays must instead be performed manually, as in

```csharp
int[][] a = new int[100][];
for (int i = 0; i < 100; i++) a[i] = new int[5];
```

When an array of arrays has a "rectangular" shape, that is when the sub-arrays are all of the same length, it is more efficient to use a multi-dimensional array. In the example above, instantiation of the array of arrays creates 101 objects—one outer array and 100 sub-arrays. In contrast,

```csharp
int[,] = new int[100, 5];
```

creates only a single object, a two-dimensional array, and accomplishes the allocation in a single statement.

The following are examples of implicitly typed array creation expressions:

```csharp
var a = new[] { 1, 10, 100, 1000 };                                // int[]

var b = new[] { 1, 1.5, 2, 2.5 };                                // double[]

var c = new[,] { { "hello", null }, { "world", "!" } };    // string[,]

var d = new[] { 1, "one", 2, "two" };                            // Error
```

The last expression causes a compile-time error because neither `int` nor `string` is implicitly convertible to the other, and so there is no best common type. An explicitly typed array creation expression must be used in this case, for example specifying the type to be `object[]`. Alternatively, one of the elements can be cast to a common base type, which would then become the inferred element type.

Implicitly typed array creation expressions can be combined with anonymous object initializers (§7.6.10.6) to create anonymously typed data structures. For example:

```csharp
var contacts = new[] {
    new {
        Name = "Chris Smith",
        PhoneNumbers = new[] { "206-555-0101", "425-882-8080" }
    },
    new {
        Name = "Bob Harris",
        PhoneNumbers = new[] { "650-555-0199" }
    }
};
```

#### Delegate creation expressions

A *delegate-creation-expression* is used to create a new instance of a *delegate-type*.

<pre>delegate-creation-expression:
<b>new</b>   delegate-type   <b>(</b>   expression   <b>)</b></pre>

The argument of a delegate creation expression must be a method group, an anonymous function or a value of either the compile time type `dynamic` or a *delegate-type*. If the argument is a method group, it identifies the method and, for an instance method, the object for which to create a delegate. If the argument is an anonymous function it directly defines the parameters and method body of the delegate target. If the argument is a value it identifies a delegate instance of which to create a copy.

If the *expression* has the compile-time type `dynamic`, the *delegate-creation-expression* is dynamically bound (§7.2.2), and the rules below are applied at run-time using the run-time type of the *expression*. Otherwise the rules are applied at compile-time.

The binding-time processing of a *delegate-creation-expression* of the form `new``D(E)`, where `D` is a *delegate-type* and `E` is an *expression*, consists of the following steps:

-  If `E` is a method group, the delegate creation expression is processed in the same way as a method group conversion (§6.6) from `E` to `D`.
-  If `E` is an anonymous function, the delegate creation expression is processed in the same way as an anonymous function conversion (§6.5) from `E` to `D`.
-  If `E` is a value, `E` must be compatible (§15.1) with `D`, and the result is a reference to a newly created delegate of type `D` that refers to the same invocation list as `E`. If `E` is not compatible with `D`, a compile-time error occurs.

The run-time processing of a *delegate-creation-expression* of the form `new``D(E)`, where `D` is a *delegate-type* and `E` is an *expression*, consists of the following steps:

-  If `E` is a method group, the delegate creation expression is evaluated as a method group conversion (§6.6) from `E` to `D`.
-  If `E` is an anonymous function, the delegate creation is evaluated as an anonymous function conversion from `E` to `D` (§6.5).
-  If `E` is a value of a *delegate-type*:

`E` is evaluated. If this evaluation causes an exception, no further steps are executed.

If the value of `E` is `null`, a `System.NullReferenceException` is thrown and no further steps are executed.

A new instance of the delegate type `D` is allocated. If there is not enough memory available to allocate the new instance, a `System.OutOfMemoryException` is thrown and no further steps are executed.

The new delegate instance is initialized with the same invocation list as the delegate instance given by `E`.

The invocation list of a delegate is determined when the delegate is instantiated and then remains constant for the entire lifetime of the delegate. In other words, it is not possible to change the target callable entities of a delegate once it has been created. When two delegates are combined or one is removed from another (§15.1), a new delegate results; no existing delegate has its contents changed.

It is not possible to create a delegate that refers to a property, indexer, user-defined operator, instance constructor, destructor, or static constructor.

As described above, when a delegate is created from a method group, the formal parameter list and return type of the delegate determine which of the overloaded methods to select. In the example

```csharp
delegate double DoubleFunc(double x);

class A
{
    DoubleFunc f = new DoubleFunc(Square);

    static float Square(float x) {
        return x * x;
    }

    static double Square(double x) {
        return x * x;
    }
}
```

the `A.f` field is initialized with a delegate that refers to the second `Square` method because that method exactly matches the formal parameter list and return type of `DoubleFunc`. Had the second `Square` method not been present, a compile-time error would have occurred.

#### Anonymous object creation expressions

An *anonymous-object-creation-expression* is used to create an object of an anonymous type.

<pre>anonymous-object-creation-expression:
<b>new</b>   anonymous-object-initializer</pre>

<pre>anonymous-object-initializer:
<b>{</b>   member-declarator-list<sub>opt</sub><b>}</b>
<b>{</b>   member-declarator-list   <b>,</b><b>}</b></pre>

<pre>member-declarator-list:
member-declarator
member-declarator-list   <b>,</b>   member-declarator</pre>

<pre>member-declarator:
simple-name
member-access
base-access
identifier   =   expression</pre>

An anonymous object initializer declares an anonymous type and returns an instance of that type. An anonymous type is a nameless class type that inherits directly from `object`. The members of an anonymous type are a sequence of read-only properties inferred from the anonymous object initializer used to create an instance of the type. Specifically, an anonymous object initializer of the form

```csharp
new { *p*<sub>1</sub> = *e*<sub>1</sub> , *p*<sub>2</sub> = *e*<sub>2</sub> , *…**p*<sub>n</sub> = *e*<sub>n</sub> }
```

declares an anonymous type of the form

```csharp
class __Anonymous1
{
    private readonly *T*<sub>1</sub>*f*<sub>1</sub> ;
    private readonly *T*<sub>2</sub>*f*<sub>2</sub> ;
*…*
    private readonly *T*<sub>n</sub>*f*<sub>n</sub> ;

    public __Anonymous1(*T*<sub>1</sub>*a*<sub>1</sub>,***T*<sub>2</sub>*a*<sub>2</sub>,*…*,***T*<sub>n</sub>*a*<sub>n</sub>) {
*f*<sub>1</sub> = *a*<sub>1</sub> ;
*f*<sub>2</sub> = *a*<sub>2</sub> ;
*…*
*f*<sub>n</sub> = *a*<sub>n</sub> ;
    }

    public *T*<sub>1</sub>*p*<sub>1</sub> { get { return *f*<sub>1</sub> ; } }
    public *T*<sub>2</sub>*p*<sub>2</sub> { get { return *f*<sub>2</sub> ; } }
*…*
    public *T*<sub>n</sub>*p*<sub>n</sub> { get { return *f*<sub>n</sub> ; } }

    public override bool Equals(object __o) { … }
    public override int GetHashCode() { … }
}
```

where each *T*<sub>x</sub> is the type of the corresponding expression *e*<sub>x</sub>. The expression used in a *member-declarator* must have a type. Thus, it is a compile-time error for an expression in a *member-declarator* to be null or an anonymous function. It is also a compile-time error for the expression to have an unsafe type.

The names of an anonymous type and of the parameter to its `Equals` method are automatically generated by the compiler and cannot be referenced in program text.

Within the same program, two anonymous object initializers that specify a sequence of properties of the same names and compile-time types in the same order will produce instances of the same anonymous type.

In the example

```csharp
var p1 = new { Name = "Lawnmower", Price = 495.00 };
var p2 = new { Name = "Shovel", Price = 26.95 };
p1 = p2;
```

the assignment on the last line is permitted because `p1` and `p2` are of the same anonymous type.

The `Equals` and `GetHashcode` methods on anonymous types override the methods inherited from `object`, and are defined in terms of the `Equals` and `GetHashcode` of the properties, so that two instances of the same anonymous type are equal if and only if all their properties are equal.

A member declarator can be abbreviated to a simple name (§7.5.2), a member access (§7.5.4) or a base access (§7.6.8). This is called a *__projection initializer__* and is shorthand for a declaration of and assignment to a property with the same name. Specifically, member declarators of the forms

```csharp
*identifier******************expr* . *identifier*
```

are precisely equivalent to the following, respectively:

```csharp
*identifer* = *identifier**identifier* = *expr* . *identifier*
```

Thus, in a projection initializer the *identifier* selects both the value and the field or property to which the value is assigned. Intuitively, a projection initializer projects not just a value, but also the name of the value.

### The typeof operator

The `typeof` operator is used to obtain the `System.Type` object for a type.

<pre>typeof-expression:
<b>typeof</b><b>(</b>   type   <b>)</b>
<b>typeof</b><b>(</b>   unbound-type-name   <b>)</b>
<b>typeof ( void )</b></pre>

<pre>unbound-type-name:
identifier   generic-dimension-specifier<sub>opt</sub>
identifier   <b>::</b>   identifier   generic-dimension-specifier<sub>opt</sub>
unbound-type-name   *__.__*   identifier   generic-dimension-specifier<sub>opt</sub></pre>

<pre>generic-dimension-specifier:
<b><</b>   commas<sub>opt</sub><b>></b></pre>

<pre>commas:
<b>,</b>
commas   <b>,</b></pre>

The first form of *typeof-expression* consists of a `typeof` keyword followed by a parenthesized *type*. The result of an expression of this form is the `System.Type` object for the indicated type. There is only one `System.Type` object for any given type. This means that for a type `T`, `typeof(T)``==``typeof(T)` is always true. The *type* cannot be `dynamic`.

The second form of *typeof-expression* consists of a `typeof` keyword followed by a parenthesized *unbound-type-name*. An *unbound-type-name* is very similar to a *type-name* (§3.8) except that an *unbound-type-name* contains *generic-dimension-specifier*s where a *type-name* contains *type-argument-list*s. When the operand of a *typeof-expression* is a sequence of tokens that satisfies the grammars of both *unbound-type-name* and *type-name*, namely when it contains neither a *generic-dimension-specifier* nor a *type-argument-list*, the sequence of tokens is considered to be a *type-name*. The meaning of an *unbound-type-name* is determined as follows:

-  Convert the sequence of tokens to a *type-name* by replacing each *generic-dimension-specifier* with a *type-argument-list* having the same number of commas and the keyword `object` as each *type-argument*.
-  Evaluate the resulting *type-name*, while ignoring all type parameter constraints.
-  The *unbound-type-name* resolves to the unbound generic type associated with the resulting constructed type (§4.4.3).

The result of the *typeof-expression* is the `System.Type` object for the resulting unbound generic type.

The third form of *typeof-expression* consists of a `typeof` keyword followed by a parenthesized `void` keyword. The result of an expression of this form is the `System.Type` object that represents the absence of a type. The type object returned by `typeof(void)` is distinct from the type object returned for any type. This special type object is useful in class libraries that allow reflection onto methods in the language, where those methods wish to have a way to represent the return type of any method, including void methods, with an instance of `System.Type`.

The `typeof` operator can be used on a type parameter. The result is the `System.Type` object for the run-time type that was bound to the type parameter. The `typeof` operator can also be used on a constructed type or an unbound generic type (§4.4.3). The `System.Type` object for an unbound generic type is not the same as the `System.Type` object of the instance type. The instance type is always a closed constructed type at run-time so its `System.Type` object depends on the run-time type arguments in use, while the unbound generic type has no type arguments.

The example

```csharp
using System;

class X<T>
{
    public static void PrintTypes() {
        Type[] t = {
            typeof(int),
            typeof(System.Int32),
            typeof(string),
            typeof(double[]),
            typeof(void),
            typeof(T),
            typeof(X<T>),
            typeof(X<X<T>>),
            typeof(X<>)
        };
        for (int i = 0; i < t.Length; i++) {
            Console.WriteLine(t[i]);
        }
    }
}

class Test
{
    static void Main() {
        X<int>.PrintTypes();
    }
}
```

produces the following output:

```csharp
System.Int32
System.Int32
System.String
System.Double[]
System.Void
System.Int32
X`1[System.Int32]
X`1[X`1[System.Int32]]
X`1[T]
```

Note that `int` and `System.Int32` are the same type.

Also note that the result of `typeof(X<>)` does not depend on the type argument but the result of `typeof(X<T>)` does.

### The checked and unchecked operators

The `checked` and `unchecked` operators are used to control the *__overflow checking context__* for integral-type arithmetic operations and conversions.

<pre>checked-expression:
<b>checked</b><b>(</b>   expression   <b>)</b></pre>

<pre>unchecked-expression:
<b>unchecked</b><b>(</b>   expression   <b>)</b></pre>

The `checked` operator evaluates the contained expression in a checked context, and the `unchecked` operator evaluates the contained expression in an unchecked context. A *checked-expression* or *unchecked-expression* corresponds exactly to a *parenthesized-expression* (§7.6.3), except that the contained expression is evaluated in the given overflow checking context.

The overflow checking context can also be controlled through the `checked` and `unchecked` statements (§8.11).

The following operations are affected by the overflow checking context established by the `checked` and `unchecked` operators and statements:

-  The predefined `++` and `--` unary operators (§7.6.9 and §7.7.5), when the operand is of an integral type.
-  The predefined `-` unary operator (§7.7.2), when the operand is of an integral type.
-  The predefined `+`, `-`, `*`, and `/` binary operators (§7.8), when both operands are of integral types.
-  Explicit numeric conversions (§6.2.1) from one integral type to another integral type, or from `float` or `double` to an integral type.

When one of the above operations produce a result that is too large to represent in the destination type, the context in which the operation is performed controls the resulting behavior:

-  In a `checked` context, if the operation is a constant expression (§7.19), a compile-time error occurs. Otherwise, when the operation is performed at run-time, a `System.OverflowException` is thrown.
-  In an `unchecked` context, the result is truncated by discarding any high-order bits that do not fit in the destination type.

For non-constant expressions (expressions that are evaluated at run-time) that are not enclosed by any `checked` or `unchecked` operators or statements, the default overflow checking context is `unchecked` unless external factors (such as compiler switches and execution environment configuration) call for `checked` evaluation.

For constant expressions (expressions that can be fully evaluated at compile-time), the default overflow checking context is always `checked`. Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur during the compile-time evaluation of the expression always cause compile-time errors.

The body of an anonymous function is not affected by `checked` or `unchecked` contexts in which the anonymous function occurs.

In the example

```csharp
class Test
{
    static readonly int x = 1000000;
    static readonly int y = 1000000;

    static int F() {
        return checked(x * y);        // Throws OverflowException
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;                    // Depends on default
    }
}
```

no compile-time errors are reported since neither of the expressions can be evaluated at compile-time. At run-time, the `F` method throws a `System.OverflowException`, and the `G` method returns –727379968 (the lower 32 bits of the out-of-range result). The behavior of the `H` method depends on the default overflow checking context for the compilation, but it is either the same as `F` or the same as `G`.

In the example

```csharp
class Test
{
    const int x = 1000000;
    const int y = 1000000;

    static int F() {
        return checked(x * y);        // Compile error, overflow
    }

    static int G() {
        return unchecked(x * y);    // Returns -727379968
    }

    static int H() {
        return x * y;                    // Compile error, overflow
    }
}
```

the overflows that occur when evaluating the constant expressions in `F` and `H` cause compile-time errors to be reported because the expressions are evaluated in a `checked` context. An overflow also occurs when evaluating the constant expression in `G`, but since the evaluation takes place in an `unchecked` context, the overflow is not reported.

The `checked` and `unchecked` operators only affect the overflow checking context for those operations that are textually contained within the "`(`" and "`)`" tokens. The operators have no effect on function members that are invoked as a result of evaluating the contained expression. In the example

```csharp
class Test
{
    static int Multiply(int x, int y) {
        return x * y;
    }

    static int F() {
        return checked(Multiply(1000000, 1000000));
    }
}
```

the use of `checked` in `F` does not affect the evaluation of `x` `*` `y` in `Multiply`, so `x` `*` `y` is evaluated in the default overflow checking context.

The `unchecked` operator is convenient when writing constants of the signed integral types in hexadecimal notation. For example:

```csharp
class Test
{
    public const int AllBits = unchecked((int)0xFFFFFFFF);

    public const int HighBit = unchecked((int)0x80000000);
}
```

Both of the hexadecimal constants above are of type `uint`. Because the constants are outside the `int` range, without the `unchecked` operator, the casts to `int` would produce compile-time errors.

The `checked` and `unchecked` operators and statements allow programmers to control certain aspects of some numeric calculations. However, the behavior of some numeric operators depends on their operands' data types. For example, multiplying two decimals always results in an exception on overflow even within an explicitly `unchecked` construct. Similarly, multiplying two floats never results in an exception on overflow even within an explicitly `checked` construct. In addition, other operators are never affected by the mode of checking, whether default or explicit.

### Default value expressions

A default value expression is used to obtain the default value (§5.2) of a type. Typically a default value expression is used for type parameters, since it may not be known if the type parameter is a value type or a reference type. (No conversion exists from the `null` literal to a type parameter unless the type parameter is known to be a reference type.)

<pre>default-value-expression:
<b>default</b><b>(</b>   type   <b>)</b></pre>

If the *type* in a *default-value-expression* evaluates at run-time to a reference type, the result is `null` converted to that type. If the *type* in a *default-value-expression* evaluates at run-time to a value type, the result is the *value-type*'s default value (§4.1.2).

A *default-value-expression* is a constant expression (§7.19) if the type is a reference type or a type parameter that is known to be a reference type (§10.1.5). In addition, a *default-value-expression* is a constant expression if the type is one of the following value types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, or any enumeration type.

### Anonymous method expressions

An *anonymous-method-expression* is one of two ways of defining an anonymous function. These are further described in §7.15.

## Unary operators

The `+`, `-`, `!`, `~`, `++`, `--`, cast, and `await` operators are called the unary operators.

<pre>unary-expression:
primary-expression
<b>+</b>   unary-expression
<b>-</b>   unary-expression
<b>!</b>   unary-expression
<b>~</b>   unary-expression
pre-increment-expression
pre-decrement-expression
cast-expression
await-expression</pre>

If the operand of a *unary-expression* has the compile-time type `dynamic`, it is dynamically bound (§7.2.2). In this case the compile-time type of the *unary-expression* is `dynamic`, and the resolution described below will take place at run-time using the run-time type of the operand.

### Unary plus operator

For an operation of the form `+x`, unary operator overload resolution (§7.3.3) is applied to select a specific operator implementation. The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator. The predefined unary plus operators are:

```csharp
int operator +(int x);
uint operator +(uint x);
long operator +(long x);
ulong operator +(ulong x);
float operator +(float x);
double operator +(double x);
decimal operator +(decimal x);
```

For each of these operators, the result is simply the value of the operand.

### Unary minus operator

For an operation of the form `–x`, unary operator overload resolution (§7.3.3) is applied to select a specific operator implementation. The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator. The predefined negation operators are:

-  Integer negation:

```csharp
int operator –(int x);
long operator –(long x);
```

The result is computed by subtracting `x` from zero. If the value of of `x` is the smallest representable value of the operand type (?2<sup>31</sup> for `int` or ?2<sup>63</sup> for `long)`, then the mathematical negation of `x` is not representable within the operand type. If this occurs within a `checked` context, a `System.OverflowException` is thrown; if it occurs within an `unchecked` context, the result is the value of the operand and the overflow is not reported.

If the operand of the negation operator is of type `uint`, it is converted to type `long`, and the type of the result is `long`. An exception is the rule that permits the `int` value ?2147483648 (?2<sup>31</sup>) to be written as a decimal integer literal (§2.4.4.2).

If the operand of the negation operator is of type `ulong`, a compile-time error occurs. An exception is the rule that permits the `long` value ?9223372036854775808 (?2<sup>63</sup>) to be written as a decimal integer literal (§2.4.4.2).

-  Floating-point negation:

```csharp
float operator –(float x);
double operator –(double x);
```

The result is the value of `x` with its sign inverted. If `x` is NaN, the result is also NaN.

-  Decimal negation:

```csharp
decimal operator –(decimal x);
```

The result is computed by subtracting `x` from zero. Decimal negation is equivalent to using the unary minus operator of type `System.Decimal`.

### Logical negation operator

For an operation of the form `!x`, unary operator overload resolution (§7.3.3) is applied to select a specific operator implementation. The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator. Only one predefined logical negation operator exists:

```csharp
bool operator !(bool x);
```

This operator computes the logical negation of the operand: If the operand is `true`, the result is `false`. If the operand is `false`, the result is `true`.

### Bitwise complement operator

For an operation of the form `~x`, unary operator overload resolution (§7.3.3) is applied to select a specific operator implementation. The operand is converted to the parameter type of the selected operator, and the type of the result is the return type of the operator. The predefined bitwise complement operators are:

```csharp
int operator ~(int x);
uint operator ~(uint x);
long operator ~(long x);
ulong operator ~(ulong x);
```

For each of these operators, the result of the operation is the bitwise complement of `x`.

Every enumeration type `E` implicitly provides the following bitwise complement operator:

```csharp
E operator `~`(E x);
```

The result of evaluating ~`x`, where `x` is an expression of an enumeration type `E` with an underlying type `U`, is exactly the same as evaluating `(E)``(~(U)x``)`, except that the conversion to `E` is always performed as if in an `unchecked` context (§7.6.12).

### Prefix increment and decrement operators

<pre>pre-increment-expression:
<b>++</b>   unary-expression</pre>

<pre>pre-decrement-expression:
<b>--</b>   unary-expression</pre>

The operand of a prefix increment or decrement operation must be an expression classified as a variable, a property access, or an indexer access. The result of the operation is a value of the same type as the operand.

If the operand of a prefix increment or decrement operation is a property or indexer access, the property or indexer must have both a `get` and a `set` accessor. If this is not the case, a binding-time error occurs.

Unary operator overload resolution (§7.3.3) is applied to select a specific operator implementation. Predefined `++` and `--` operators exist for the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, and any enum type. The predefined `++` operators return the value produced by adding 1 to the operand, and the predefined `--` operators return the value produced by subtracting 1 from the operand. In a `checked` context, if the result of this addition or subtraction is outside the range of the result type and the result type is an integral type or enum type, a `System.OverflowException` is thrown.

The run-time processing of a prefix increment or decrement operation of the form `++x` or `--x` consists of the following steps:

-  If `x` is classified as a variable:

`x` is evaluated to produce the variable.

The selected operator is invoked with the value of `x` as its argument.

The value returned by the operator is stored in the location given by the evaluation of `x`.

The value returned by the operator becomes the result of the operation.

-  If `x` is classified as a property or indexer access:

The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `get` and `set` accessor invocations.

The `get` accessor of `x` is invoked.

The selected operator is invoked with the value returned by the `get` accessor as its argument.

The `set` accessor of `x` is invoked with the value returned by the operator as its `value` argument.

The value returned by the operator becomes the result of the operation.

The `++` and `--` operators also support postfix notation (§7.6.9). Typically, the result of `x++` or `x--` is the value of `x` before the operation, whereas the result of `++x` or `--x` is the value of `x` after the operation. In either case, `x` itself has the same value after the operation.

An `operator``++` or `operator``--` implementation can be invoked using either postfix or prefix notation. It is not possible to have separate operator implementations for the two notations.

### Cast expressions

A *cast-expression* is used to explicitly convert an expression to a given type.

<pre>cast-expression:
<b>(</b>   type   <b>)</b>   unary-expression</pre>

A *cast-expression* of the form `(T)E`, where `T` is a *type* and `E` is a *unary-expression*, performs an explicit conversion (§6.2) of the value of `E` to type `T`. If no explicit conversion exists from `E` to `T`, a binding-time error occurs. Otherwise, the result is the value produced by the explicit conversion. The result is always classified as a value, even if `E` denotes a variable.

The grammar for a *cast-expression* leads to certain syntactic ambiguities. For example, the expression `(x)–y` could either be interpreted as a *cast-expression* (a cast of `–y` to type `x`) or as an *additive-expression* combined with a *parenthesized-expression* (which computes the value `x``–``y)`.

To resolve *cast-expression* ambiguities, the following rule exists: A sequence of one or more *token*s (§2.3.3) enclosed in parentheses is considered the start of a *cast-expression* only if at least one of the following are true:

-  The sequence of tokens is correct grammar for a *type*, but not for an *expression*.
-  The sequence of tokens is correct grammar for a *type*, and the token immediately following the closing parentheses is the token "`~`", the token "`!`", the token "`(`", an *identifier* (§2.4.1), a *literal* (§2.4.4), or any *keyword* (§2.4.3) except `as` and `is`.

The term "correct grammar" above means only that the sequence of tokens must conform to the particular grammatical production. It specifically does not consider the actual meaning of any constituent identifiers. For example, if `x` and `y` are identifiers, then `x.y` is correct grammar for a type, even if `x.y` doesn't actually denote a type.

From the disambiguation rule it follows that, if `x` and `y` are identifiers, `(x)y`, `(x)(y)`, and `(x)(-y)` are *cast-expression*s, but `(x)-y` is not, even if `x` identifies a type. However, if `x` is a keyword that identifies a predefined type (such as `int`), then all four forms are *cast-expression*s (because such a keyword could not possibly be an expression by itself).

### Await expressions

The await operator is used to suspend evaluation of the enclosing async function until the asynchronous operation represented by the operand has completed.

<pre>await-expression:
<b>await</b>   unary-expression</pre>

An *await-expression* is only allowed in the body of an async function (§10.14). Within the nearest enclosing async function, an *await-expression* may not occur in these places:

-  Inside a nested (non-async) anonymous function
-  In a catch or finally block of a *try-statement*
-  Inside the block of a *lock-statement*
-  In an unsafe context

Note that an *await**-**expression* cannot occur in most places within a *query-expression*, because those are syntactically transformed to use non-async lambda expressions.

Inside of an async function, `await` cannot be used as an identifier. There is therefore no syntactic ambiguity between await-expressions and various expressions involving identifiers. Outside of async functions, `await` acts as a normal identifier.

The operand of an *await-expression* is called the *__task__*. It represents an asynchronous operation that may or may not be complete at the time the *await-expression* is evaluated. The purpose of the await operator is to suspend execution of the enclosing async function until the awaited task is complete, and then obtain its outcome.

#### Awaitable expressions

The task of an await expression is required to be *__awaitable__*. An expression `*t*` is awaitable if one of the following holds:

-  `*t*`` is of compile time type ``dynamic`
-  `*t*`` has an accessible instance or extension method called ``GetAwaiter`` with no parameters and no type parameters, and a return ``type ``*A*````for which all of the following hold:`

`*A*` implements the interface `System.Runtime.CompilerServices.INotifyCompletion` (hereafter known as `INotifyCompletion` for brevity)

`*A*` has an accessible, readable instance property `IsCompleted` of type `bool`

`*A*` has an accessible instance method `GetResult` with no parameters and no type parameters

The purpose of the `GetAwaiter` method is to obtain an *__awaiter__* for the task. The type `*A*` is called the *__awaiter type__* for the await expression.

The purpose of the `IsCompleted` property is to determine if the task is already complete. If so, there is no need to suspend evaluation.

The purpose of the `INotifyCompletion.``OnCompleted` method is to sign up a "continuation" to the task; i.e. a delegate (of type `System.Action`) that will be invoked once the task is complete.

The purpose of the `GetResult` method is to obtain the outcome of the task once it is complete. This outcome may be successful completion, possibly with a result value, or it may be an exception which is thrown by the `GetResult` method.

#### Classification of await expressions

The expression `await``*t*` is classified the same way as the expression `(``*t*``).GetAwaiter().GetResult()`. Thus, if the return type of `GetResult` is `void`, the *await-expression* is classified as nothing. If it has a non-void return type `*T*`, the *await-expression* is classified as a value of type `*T*`.

#### Runtime evaluation of await expressions

At runtime, the expression `await``*t*` is evaluated as follows:

-  An awaiter `*a*` is obtained by evaluating the expression `(``*t*``).GetAwaiter()`.
-  A `bool``*b*` is obtained by evaluating the expression `(``*a*``).IsCompleted`.
-  If `*b*` is `false` then evaluation depends on whether `*a*` implements the interface `System.Runtime.CompilerServices.I``Critical``NotifyCompletion` (hereafter known as `I``Critical``NotifyCompletion` for brevity). This check is done at binding time; i.e. at runtime if `*a*` has the compile time type `dynamic`, and at compile time otherwise. Let `*r*` denote the resumption delegate (§10.14):

If `*a*` does not implement `I``Critical``NotifyCompletion`, then the expression 
`(``*a*``as``(``I``NotifyCompletion)``).``OnCompleted(``*r*``)` is evaluated.

If `*a*` does implement `ICriticalNotifyCompletion`, then the expression 
`(``*a*``as``(ICriticalNotifyCompletion)).UnsafeOnCompleted(``*r*``)` is evaluated.

Evaluation is then suspended, and control is returned to the current caller of the async function.

-  Either immediately after (if `*b*` was `true`), or upon later invocation of the resumption delegate (if `*b*` was `false`), the expression `(``*a*``).GetResult()` is evaluated. If it returns a value, that value is the result of the *await-expression*. Otherwise the result is nothing.

An awaiter's implementation of the interface methods `INotifyCompletion.O``nCompleted` and `ICriticalNotifyCompletion.UnsafeOnCompleted` should cause the delegate `*r*` to be invoked at most once. Otherwise, the behavior of the enclosing async function is undefined.

## Arithmetic operators

The `*`, `/`, `%`, `+`, and `–` operators are called the arithmetic operators.

<pre>multiplicative-expression:
unary-expression
multiplicative-expression   <b>*</b>   unary-expression
multiplicative-expression   <b>/</b>   unary-expression
multiplicative-expression   <b>%</b>   unary-expression</pre>

<pre>additive-expression:
multiplicative-expression
additive-expression   <b>+</b>   multiplicative-expression
additive-expression   <b>–</b>   multiplicative-expression</pre>

If an operand of an arithmetic operator has the compile-time type `dynamic`, then the expression is dynamically bound (§7.2.2). In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.

### Multiplication operator

For an operation of the form `x``*``y`, binary operator overload resolution (§7.3.4) is applied to select a specific operator implementation. The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.

The predefined multiplication operators are listed below. The operators all compute the product of `x` and `y`.

-  Integer multiplication:

```csharp
int operator *(int x, int y);
uint operator *(uint x, uint y);
long operator *(long x, long y);
ulong operator *(ulong x, ulong y);
```

In a `checked` context, if the product is outside the range of the result type, a `System.OverflowException` is thrown. In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.

-  Floating-point multiplication:

```csharp
float operator *(float x, float y);
double operator *(double x, double y);
```

The product is computed according to the rules of IEEE 754 arithmetic. The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's. In the table, `x` and `y` are positive finite values. `z` is the result of `x``*``y`. If the result is too large for the destination type, `z` is infinity. If the result is too small for the destination type, `z` is zero.


|  | `+y` | `–y` | `+0` | `–0` | `+?` | `–?` |  | 
| `+x` | `+z` | `–z` | `+0` | `–0` | `+?` | `–?` |  | 
| `–x` | `–z` | `+z` | `–0` | `+0` | `–?` | `+?` |  | 
| `+0` | `+0` | `–0` | `+0` | `–0` |  |  |  | 
| `–0` | `–0` | `+0` | `–0` | `+0` |  |  |  | 
| `+?` | `+?` | `–?` |  |  | `+?` | `–?` |  | 
| `–?` | `–?` | `+?` |  |  | `–?` | `+?` |  | 
|  |  |  |  |  |  |  |  | 

-  Decimal multiplication:

```csharp
decimal operator *(decimal x, decimal y);
```

If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown. If the result value is too small to represent in the `decimal` format, the result is zero. The scale of the result, before any rounding, is the sum of the scales of the two operands.

Decimal multiplication is equivalent to using the multiplication operator of type `System.Decimal`.

### Division operator

For an operation of the form `x``/``y`, binary operator overload resolution (§7.3.4) is applied to select a specific operator implementation. The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.

The predefined division operators are listed below. The operators all compute the quotient of `x` and `y`.

-  Integer division:

```csharp
int operator /(int x, int y);
uint operator /(uint x, uint y);
long operator /(long x, long y);
ulong operator /(ulong x, ulong y);
```

If the value of the right operand is zero, a `System.DivideByZeroException` is thrown.

The division rounds the result towards zero. Thus the absolute value of the result is the largest possible integer that is less than or equal to the absolute value of the quotient of the two operands. The result is zero or positive when the two operands have the same sign and zero or negative when the two operands have opposite signs.

If the left operand is the smallest representable `int` or `long` value and the right operand is `–1`, an overflow occurs. In a `checked` context, this causes a `System.ArithmeticException` (or a subclass thereof) to be thrown. In an `unchecked` context, it is implementation-defined as to whether a `System.ArithmeticException` (or a subclass thereof) is thrown or the overflow goes unreported with the resulting value being that of the left operand.

-  Floating-point division:

```csharp
float operator /(float x, float y);
double operator /(double x, double y);
```

The quotient is computed according to the rules of IEEE 754 arithmetic. The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's. In the table, `x` and `y` are positive finite values. `z` is the result of `x``/``y`. If the result is too large for the destination type, `z` is infinity. If the result is too small for the destination type, `z` is zero.


|  | `+y` | `–y` | `+0` | `–0` | `+?` | `–?` |  | 
| `+x` | `+z` | `–z` | `+?` | `–?` | `+0` | `–0` |  | 
| `–x` | `–z` | `+z` | `–?` | `+?` | `–0` | `+0` |  | 
| `+0` | `+0` | `–0` |  |  | `+0` | `–0` |  | 
| `–0` | `–0` | `+0` |  |  | `–0` | `+0` |  | 
| `+?` | `+?` | `–?` | `+?` | `–?` |  |  |  | 
| `–?` | `–?` | `+?` | `–?` | `+?` |  |  |  | 
|  |  |  |  |  |  |  |  | 

-  Decimal division:

```csharp
decimal operator /(decimal x, decimal y);
```

If the value of the right operand is zero, a `System.DivideByZeroException` is thrown. If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown. If the result value is too small to represent in the `decimal` format, the result is zero. The scale of the result is the smallest scale that will preserve a result equal to the nearest representantable decimal value to the true mathematical result.

Decimal division is equivalent to using the division operator of type `System.Decimal`.

### Remainder operator

For an operation of the form `x``%``y`, binary operator overload resolution (§7.3.4) is applied to select a specific operator implementation. The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.

The predefined remainder operators are listed below. The operators all compute the remainder of the division between `x` and `y`.

-  Integer remainder:

```csharp
int operator %(int x, int y);
uint operator %(uint x, uint y);
long operator %(long x, long y);
ulong operator %(ulong x, ulong y);
```

The result of `x``%``y` is the value produced by `x``–``(x``/``y)``*``y`. If `y` is zero, a `System.DivideByZeroException` is thrown.

If the left operand is the smallest `int` or `long` value and the right operand is `-1`, a `System.``Overflow``Exception` is thrown. In no case does `x``%``y` throw an exception where `x``/``y` would not throw an exception.

-  Floating-point remainder:

```csharp
float operator %(float x, float y);
double operator %(double x, double y);
```

The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's. In the table, `x` and `y` are positive finite values. `z` is the result of `x``%``y` and is computed as `x``–``n``*``y`, where `n` is the largest possible integer that is less than or equal to `x``/``y`. This method of computing the remainder is analogous to that used for integer operands, but differs from the IEEE 754 definition (in which `n` is the integer closest to `x``/``y`).


|  | `+y` | `–y` | `+0` | `–0` | `+?` | `–?` |  | 
| `+x` | `+z` | `+z` |  |  | `x` | `x` |  | 
| `–x` | `–z` | `–z` |  |  | `–x` | `–x` |  | 
| `+0` | `+0` | `+0` |  |  | `+0` | `+0` |  | 
| `–0` | `–0` | `–0` |  |  | `–0` | `–0` |  | 
| `+?` |  |  |  |  |  |  |  | 
| `–?` |  |  |  |  |  |  |  | 
|  |  |  |  |  |  |  |  | 

-  Decimal remainder:

```csharp
decimal operator %(decimal x, decimal y);
```

If the value of the right operand is zero, a `System.DivideByZeroException` is thrown. The scale of the result, before any rounding, is the larger of the scales of the two operands, and the sign of the result, if non-zero, is the same as that of `x`.

Decimal remainder is equivalent to using the remainder operator of type `System.Decimal`.

### Addition operator

For an operation of the form `x``+``y`, binary operator overload resolution (§7.3.4) is applied to select a specific operator implementation. The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.

The predefined addition operators are listed below. For numeric and enumeration types, the predefined addition operators compute the sum of the two operands. When one or both operands are of type string, the predefined addition operators concatenate the string representation of the operands.

-  Integer addition:

```csharp
int operator +(int x, int y);
uint operator +(uint x, uint y);
long operator +(long x, long y);
ulong operator +(ulong x, ulong y);
```

In a `checked` context, if the sum is outside the range of the result type, a `System.OverflowException` is thrown. In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.

-  Floating-point addition:

```csharp
float operator +(float x, float y);
double operator +(double x, double y);
```

The sum is computed according to the rules of IEEE 754 arithmetic. The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaN's. In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x``+``y`. If `x` and `y` have the same magnitude but opposite signs, `z` is positive zero. If `x``+``y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x``+``y`.


|  | `y` | `+0` | `–0` | `+?` | `–?` |  | 
| `x` | `z` | `x` | `x` | `+?` | `–?` |  | 
| `+0` | `y` | `+0` | `+0` | `+?` | `–?` |  | 
| `–0` | `y` | `+0` | `–0` | `+?` | `–?` |  | 
| `+?` | `+?` | `+?` | `+?` | `+?` |  |  | 
| `–?` | `–?` | `–?` | `–?` |  | `–?` |  | 
|  |  |  |  |  |  |  | 

-  Decimal addition:

```csharp
decimal operator +(decimal x, decimal y);
```

If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown. The scale of the result, before any rounding, is the larger of the scales of the two operands.

Decimal addition is equivalent to using the addition operator of type `System.Decimal`.

-  Enumeration addition. Every enumeration type implicitly provides the following predefined operators, where `E` is the enum type, and `U` is the underlying type of `E`:

```csharp
E operator +(E x, U y);
E operator +(U x, E y);
```

At run-time these operators are evaluated exactly as `(E)((U)x``+``(U)y)`.

-  String concatenation:

```csharp
string operator +(string x, string y);
string operator +(string x, object y);
string operator +(object x, string y);
```

These overloads of the binary `+` operator perform string concatenation. If an operand of string concatenation is `null`, an empty string is substituted. Otherwise, any non-string argument is converted to its string representation by invoking the virtual `ToString` method inherited from type `object`. If `ToString` returns `null`, an empty string is substituted.

```csharp
using System;

class Test
{
    static void Main() {
        string s = null;
        Console.WriteLine("s = >" + s + "<");        // displays s = ><
        int i = 1;
        Console.WriteLine("i = " + i);                // displays i = 1
        float f = 1.2300E+15F;
        Console.WriteLine("f = " + f);                // displays f = 1.23E+15
        decimal d = 2.900m;
        Console.WriteLine("d = " + d);                // displays d = 2.900
    }
}
```

The result of the string concatenation operator is a string that consists of the characters of the left operand followed by the characters of the right operand. The string concatenation operator never returns a `null` value. A `System.OutOfMemoryException` may be thrown if there is not enough memory available to allocate the resulting string.

-  Delegate combination. Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:

```csharp
D operator `+`(D x, D y);
```

The binary `+` operator performs delegate combination when both operands are of some delegate type `D`. (If the operands have different delegate types, a binding-time error occurs.) If the first operand is `null`, the result of the operation is the value of the second operand (even if that is also `null`). Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand. Otherwise, the result of the operation is a new delegate instance that, when invoked, invokes the first operand and then invokes the second operand. For examples of delegate combination, see §7.8.5 and §15.4. Since `System.Delegate` is not a delegate type, `operator` `+` is not defined for it.

### Subtraction operator

For an operation of the form `x``–``y`, binary operator overload resolution (§7.3.4) is applied to select a specific operator implementation. The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.

The predefined subtraction operators are listed below. The operators all subtract `y` from `x`.

-  Integer subtraction:

```csharp
int operator `–`(int x, int y);
uint operator `–`(uint x, uint y);
long operator `–`(long x, long y);
ulong operator `–`(ulong x, ulong y);
```

In a `checked` context, if the difference is outside the range of the result type, a `System.OverflowException` is thrown. In an `unchecked` context, overflows are not reported and any significant high-order bits outside the range of the result type are discarded.

-  Floating-point subtraction:

```csharp
float operator `–`(float x, float y);
double operator `–`(double x, double y);
```

The difference is computed according to the rules of IEEE 754 arithmetic. The following table lists the results of all possible combinations of nonzero finite values, zeros, infinities, and NaNs. In the table, `x` and `y` are nonzero finite values, and `z` is the result of `x``–``y`. If `x` and `y` are equal, `z` is positive zero. If `x``–``y` is too large to represent in the destination type, `z` is an infinity with the same sign as `x``–``y`.


|  | `y` | `+0` | `–0` | `+?` | `–?` |  | 
| `x` | `z` | `x` | `x` | `–?` | `+?` |  | 
| `+0` | `–y` | `+0` | `+0` | `–?` | `+?` |  | 
| `–0` | `–y` | `–0` | `+0` | `–?` | `+?` |  | 
| `+?` | `+?` | `+?` | `+?` |  | `+?` |  | 
| `–?` | `–?` | `–?` | `–?` | `–?` |  |  | 
|  |  |  |  |  |  |  | 

-  Decimal subtraction:

```csharp
decimal operator `–`(decimal x, decimal y);
```

If the resulting value is too large to represent in the `decimal` format, a `System.OverflowException` is thrown. The scale of the result, before any rounding, is the larger of the scales of the two operands.

Decimal subtraction is equivalent to using the subtraction operator of type `System.Decimal`.

-  Enumeration subtraction. Every enumeration type implicitly provides the following predefined operator, where `E` is the enum type, and `U` is the underlying type of `E`:

```csharp
U operator `–`(E x, E y);
```

This operator is evaluated exactly as `(U)((U)x``–``(U)y)`. In other words, the operator computes the difference between the ordinal values of `x` and `y`, and the type of the result is the underlying type of the enumeration.

```csharp
E operator `–`(E x, U y);
```

This operator is evaluated exactly as `(E)((U)x``–``y)`. In other words, the operator subtracts a value from the underlying type of the enumeration, yielding a value of the enumeration.

-  Delegate removal. Every delegate type implicitly provides the following predefined operator, where `D` is the delegate type:

```csharp
D operator `–`(D x, D y);
```

The binary `–` operator performs delegate removal when both operands are of some delegate type `D`. If the operands have different delegate types, a binding-time error occurs. If the first operand is `null`, the result of the operation is `null`. Otherwise, if the second operand is `null`, then the result of the operation is the value of the first operand. Otherwise, both operands represent invocation lists (§15.1) having one or more entries, and the result is a new invocation list consisting of the first operand's list with the second operand's entries removed from it, provided the second operand's list is a proper contiguous sublist of the first's.     (To determine sublist equality, corresponding entries are compared as for the delegate equality operator (§7.10.8).) Otherwise, the result is the value of the left operand. Neither of the operands' lists is changed in the process. If the second operand's list matches multiple sublists of contiguous entries in the first operand's list, the right-most matching sublist of contiguous entries is removed. If removal results in an empty list, the result is `null`. For example:

```csharp
delegate void D(int x);

class C
{
    public static void M1(int i) { /* … */ }
    public static void M2(int i) { /* … */ }
}

class Test
{
    static void Main() { 
        D cd1 = new D(C.M1);
        D cd2 = new D(C.M2);
        D cd3 = cd1 + cd2 + cd2 + cd1;    // M1 + M2 + M2 + M1
        cd3 -= cd1;                                // => M1 + M2 + M2

        cd3 = cd1 + cd2 + cd2 + cd1;        // M1 + M2 + M2 + M1
        cd3 -= cd1 + cd2;                        // => M2 + M1

        cd3 = cd1 + cd2 + cd2 + cd1;        // M1 + M2 + M2 + M1
        cd3 -= cd2 + cd2;                        // => M1 + M1

        cd3 = cd1 + cd2 + cd2 + cd1;        // M1 + M2 + M2 + M1
        cd3 -= cd2 + cd1;                        // => M1 + M2

        cd3 = cd1 + cd2 + cd2 + cd1;        // M1 + M2 + M2 + M1
        cd3 -= cd1 + cd1;                        // => M1 + M2 + M2 + M1
    }
}
```

## Shift operators

The `<<` and `>>` operators are used to perform bit shifting operations.

<pre>shift-expression:
additive-expression 
shift-expression   <b><<</b>   additive-expression
shift-expression   right-shift   additive-expression</pre>

If an operand of a *shift-expression* has the compile-time type `dynamic`, then the expression is dynamically bound (§7.2.2). In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.

For an operation of the form `x``<<``count` or `x``>>``count`, binary operator overload resolution (§7.3.4) is applied to select a specific operator implementation. The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.

When declaring an overloaded shift operator, the type of the first operand must always be the class or struct containing the operator declaration, and the type of the second operand must always be `int`.

The predefined shift operators are listed below.

-  Shift left:

```csharp
int operator <<(int x, int count);
uint operator <<(uint x, int count);
long operator <<(long x, int count);
ulong operator <<(ulong x, int count);
```

The `<<` operator shifts `x` left by a number of bits computed as described below.

The high-order bits outside the range of the result type of `x` are discarded, the remaining bits are shifted left, and the low-order empty bit positions are set to zero.

-  Shift right:

```csharp
int operator >>(int x, int count);
uint operator >>(uint x, int count);
long operator >>(long x, int count);
ulong operator >>(ulong x, int count);
```

The `>>` operator shifts `x` right by a number of bits computed as described below.

When `x` is of type `int` or `long`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero if `x` is non-negative and set to one if `x` is negative.

When `x` is of type `uint` or `ulong`, the low-order bits of `x` are discarded, the remaining bits are shifted right, and the high-order empty bit positions are set to zero.

For the predefined operators, the number of bits to shift is computed as follows:

-  When the type of `x` is `int` or `uint`, the shift count is given by the low-order five bits of `count`. In other words, the shift count is computed from `count``&amp;``0x1F`.
-  When the type of `x` is `long` or `ulong`, the shift count is given by the low-order six bits of `count`. In other words, the shift count is computed from `count``&amp;``0x3F`.

If the resulting shift count is zero, the shift operators simply return the value of `x`.

Shift operations never cause overflows and produce the same results in `checked` and `unchecked` contexts.

When the left operand of the `>>` operator is of a signed integral type, the operator performs an arithmetic shift right wherein the value of the most significant bit (the sign bit) of the operand is propagated to the high-order empty bit positions. When the left operand of the `>>` operator is of an unsigned integral type, the operator performs a logical shift right wherein high-order empty bit positions are always set to zero. To perform the opposite operation of that inferred from the operand type, explicit casts can be used. For example, if `x` is a variable of type `int`, the operation `unchecked((int)((uint)x``>>``y))` performs a logical shift right of `x`.

## Relational and type-testing operators

The `==`, `!=`, `<`, `>`, `<=`, `>=`, `is` and `as` operators are called the relational and type-testing operators.

<pre>relational-expression:
shift-expression
relational-expression   <b><</b>   shift-expression
relational-expression   <b>></b>   shift-expression
relational-expression   <b><=</b>   shift-expression
relational-expression   <b>>=</b>   shift-expression
relational-expression   <b>is</b>   type
relational-expression   <b>as</b>   type</pre>

<pre>equality-expression:
relational-expression
equality-expression   <b>==</b>   relational-expression
equality-expression   <b>!=</b>   relational-expression</pre>

The `is` operator is described in §7.10.10 and the `as` operator is described in §7.10.11.

The `==`, `!=`, `<`, `>`, `<=` and `>=` operators are *__comparison operators__*.

If an operand of a comparison operator has the compile-time type `dynamic`, then the expression is dynamically bound (§7.2.2). In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.

For an operation of the form `x`*op*`y`, where *op* is a comparison operator, overload resolution (§7.3.4) is applied to select a specific operator implementation. The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.

The predefined comparison operators are described in the following sections. All predefined comparison operators return a result of type `bool`, as described in the following table.


| __Operation__ | __Result__ | 
| `x``==``y` | `true` if `x` is equal to `y`, `false` otherwise | 
| `x``!=``y` | `true` if `x` is not equal to `y`, `false` otherwise | 
| `x``<``y` | `true` if `x` is less than `y`, `false` otherwise | 
| `x``>``y` | `true` if `x` is greater than `y`, `false` otherwise | 
| `x``<=``y` | `true` if `x` is less than or equal to `y`, `false` otherwise | 
| `x``>=``y` | `true` if `x` is greater than or equal to `y`, `false` otherwise | 

### Integer comparison operators

The predefined integer comparison operators are:

```csharp
bool operator `==`(int x, int y);
bool operator `==`(uint x, uint y);
bool operator `==`(long x, long y);
bool operator `==`(ulong x, ulong y);

bool operator `!=`(int x, int y);
bool operator `!=`(uint x, uint y);
bool operator `!=`(long x, long y);
bool operator `!=`(ulong x, ulong y);

bool operator `<`(int x, int y);
bool operator `<`(uint x, uint y);
bool operator `<`(long x, long y);
bool operator `<`(ulong x, ulong y);

bool operator `>`(int x, int y);
bool operator `>`(uint x, uint y);
bool operator `>`(long x, long y);
bool operator `>`(ulong x, ulong y);

bool operator `<=`(int x, int y);
bool operator `<=`(uint x, uint y);
bool operator `<=`(long x, long y);
bool operator `<=`(ulong x, ulong y);

bool operator `>=`(int x, int y);
bool operator `>=`(uint x, uint y);
bool operator `>=`(long x, long y);
bool operator `>=`(ulong x, ulong y);
```

Each of these operators compares the numeric values of the two integer operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`.

### Floating-point comparison operators

The predefined floating-point comparison operators are:

```csharp
bool operator `==`(float x, float y);
bool operator `==`(double x, double y);

bool operator `!=`(float x, float y);
bool operator `!=`(double x, double y);

bool operator `<`(float x, float y);
bool operator `<`(double x, double y);

bool operator `>`(float x, float y);
bool operator `>`(double x, double y);

bool operator `<=`(float x, float y);
bool operator `<=`(double x, double y);

bool operator `>=`(float x, float y);
bool operator `>=`(double x, double y);
```

The operators compare the operands according to the rules of the IEEE 754 standard:

-  If either operand is NaN, the result is `false` for all operators except `!=`, for which the result is `true`. For any two operands, `x``!=``y` always produces the same result as `!(x``==``y)`. However, when one or both operands are NaN, the `<`, `>`, `<=`, and `>=` operators do not produce the same results as the logical negation of the opposite operator. For example, if either of `x` and `y` is NaN, then `x``<``y` is `false`, but `!(x``>=``y)` is `true`.
-  When neither operand is NaN, the operators compare the values of the two floating-point operands with respect to the ordering

```csharp
`–? < –max < `...` < –min < –0.0 == +0.0 < +min < ... < +max < +?`
```

where `min` and `max` are the smallest and largest positive finite values that can be represented in the given floating-point format. Notable effects of this ordering are:

Negative and positive zeros are considered equal.

A negative infinity is considered less than all other values, but equal to another negative infinity.

A positive infinity is considered greater than all other values, but equal to another positive infinity.

### Decimal comparison operators

The predefined decimal comparison operators are:

```csharp
bool operator ==(decimal x, decimal y);

bool operator !=(decimal x, decimal y);

bool operator <(decimal x, decimal y);

bool operator >(decimal x, decimal y);

bool operator <=(decimal x, decimal y);

bool operator >=(decimal x, decimal y);
```

Each of these operators compares the numeric values of the two decimal operands and returns a `bool` value that indicates whether the particular relation is `true` or `false`. Each decimal comparison is equivalent to using the corresponding relational or equality operator of type `System.Decimal`.

### Boolean equality operators

The predefined boolean equality operators are:

```csharp
bool operator `==`(bool x, bool y);

bool operator `!=`(bool x, bool y);
```

The result of `==` is `true` if both `x` and `y` are `true` or if both `x` and `y` are `false`. Otherwise, the result is `false`.

The result of `!=` is `false` if both `x` and `y` are `true` or if both `x` and `y` are `false`. Otherwise, the result is `true`. When the operands are of type `bool`, the `!=` operator produces the same result as the `^` operator.

### Enumeration comparison operators

Every enumeration type implicitly provides the following predefined comparison operators:

```csharp
bool operator `==`(E x, E y);

bool operator `!=`(E x, E y);

bool operator `<`(E x, E y);

bool operator `>`(E x, E y);

bool operator `<=`(E x, E y);

bool operator `>=`(E x, E y);
```

The result of evaluating `x`*op*`y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and *op* is one of the comparison operators, is exactly the same as evaluating `((U)x)`*op*`((U)y)`. In other words, the enumeration type comparison operators simply compare the underlying integral values of the two operands.

### Reference type equality operators

The predefined reference type equality operators are:

```csharp
bool operator `==`(object x, object y);

bool operator `!=`(object x, object y);
```

The operators return the result of comparing the two references for equality or non-equality.

Since the predefined reference type equality operators accept operands of type `object`, they apply to all types that do not declare applicable `operator``==` and `operator``!=` members. Conversely, any applicable user-defined equality operators effectively hide the predefined reference type equality operators.

The predefined reference type equality operators require one of the following:

-  Both operands are a value of a type known to be a *reference-type* or the literal `null`. Furthermore, an explicit reference conversion (§6.2.4) exists from the type of either operand to the type of the other operand.
-  One operand is a value of type `T` where `T` is a *type-parameter* and the other operand is the literal `null`. Furthermore `T` does not have the value type constraint.

Unless one of these conditions are true, a binding-time error occurs. Notable implications of these rules are:

-  It is a binding-time error to use the predefined reference type equality operators to compare two references that are known to be different at binding-time. For example, if the binding-time types of the operands are two class types `A` and `B`, and if neither `A` nor `B` derives from the other, then it would be impossible for the two operands to reference the same object. Thus, the operation is considered a binding-time error.
-  The predefined reference type equality operators do not permit value type operands to be compared. Therefore, unless a struct type declares its own equality operators, it is not possible to compare values of that struct type.
-  The predefined reference type equality operators never cause boxing operations to occur for their operands. It would be meaningless to perform such boxing operations, since references to the newly allocated boxed instances would necessarily differ from all other references.
-  If an operand of a type parameter type `T` is compared to `null`, and the run-time type of `T` is a value type, the result of the comparison is `false`.

The following example checks whether an argument of an unconstrained type parameter type is `null`.

```csharp
class C<T>
{
    void F(T x) {
        if (x == null) throw new ArgumentNullException();
        ...
    }
}
```

The `x``==``null` construct is permitted even though `T` could represent a value type, and the result is simply defined to be `false` when `T` is a value type.

For an operation of the form `x``==``y` or `x``!=``y`, if any applicable `operator``==` or `operator``!=` exists, the operator overload resolution (§7.3.4) rules will select that operator instead of the predefined reference type equality operator. However, it is always possible to select the predefined reference type equality operator by explicitly casting one or both of the operands to type `object`. The example

```csharp
using System;

class Test
{
    static void Main() {
        string s = "Test";
        string t = string.Copy(s);
        Console.WriteLine(s == t);
        Console.WriteLine((object)s == t);
        Console.WriteLine(s == (object)t);
        Console.WriteLine((object)s == (object)t);
    }
}
```

produces the output

```csharp
True
False
False
False
```

The `s` and `t` variables refer to two distinct `string` instances containing the same characters. The first comparison outputs `True` because the predefined string equality operator (§7.10.7) is selected when both operands are of type `string`. The remaining comparisons all output `False` because the predefined reference type equality operator is selected when one or both of the operands are of type `object`.

Note that the above technique is not meaningful for value types. The example

```csharp
class Test
{
    static void Main() {
        int i = 123;
        int j = 123;
        System.Console.WriteLine((object)i == (object)j);
    }
}
```

outputs `False` because the casts create references to two separate instances of boxed `int` values.

### String equality operators

The predefined string equality operators are:

```csharp
bool operator `==`(string x, string y);

bool operator `!=`(string x, string y);
```

Two `string` values are considered equal when one of the following is true:

-  Both values are `null`.
-  Both values are non-null references to string instances that have identical lengths and identical characters in each character position.

The string equality operators compare string values rather than string references. When two separate string instances contain the exact same sequence of characters, the values of the strings are equal, but the references are different. As described in §7.10.6, the reference type equality operators can be used to compare string references instead of string values.

### Delegate equality operators

Every delegate type implicitly provides the following predefined comparison operators:

```csharp
bool operator `==`(System.Delegate x, System.Delegate y);

bool operator `!=`(System.Delegate x, System.Delegate y);
```

Two delegate instances are considered equal as follows:

-  If either of the delegate instances is `null`, they are equal if and only if both are `null`.
-  If the delegates have different run-time type they are never equal.
-  If both of the delegate instances have an invocation list (§15.1), those instances are equal if and only if their invocation lists are the same length, and each entry in one's invocation list is equal (as defined below) to the corresponding entry, in order, in the other's invocation list.

The following rules govern the equality of invocation list entries:

-  If two invocation list entries both refer to the same static method then the entries are equal.
-  If two invocation list entries both refer to the same non-static method on the same target object (as defined by the reference equality operators) then the entries are equal.
-  Invocation list entries produced from evaluation of semantically identical***anonymous-function-expression*s with the same (possibly empty) set of captured outer variable instances are permitted (but not required) to be equal.

### Equality operators and null

The `==` and `!=` operators permit one operand to be a value of a nullable type and the other to be the `null` literal, even if no predefined or user-defined operator (in unlifted or lifted form) exists for the operation.

For an operation of one of the forms

```csharp
x == null    null == x    x != null    null != x
```

where `x` is an expression of a nullable type, if operator overload resolution (§7.2.4) fails to find an applicable operator, the result is instead computed from the `HasValue` property of `x`. Specifically, the first two forms are translated into `!x.HasValue`, and last two forms are translated into `x.HasValue`.

### The is operator

The `is` operator is used to dynamically check if the run-time type of an object is compatible with a given type. The result of the operation `E``is``T`, where `E` is an expression and `T` is a type, is a boolean value indicating whether `E` can successfully be converted to type `T` by a reference conversion, a boxing conversion, or an unboxing conversion. The operation is evaluated as follows, after type arguments have been substituted for all type parameters:

-  If `E` is an anonymous function, a compile-time error occurs
-  If `E` is a method group or the `null` literal, of if the type of `E` is a reference type or a nullable type and the value of `E` is null, the result is false.
-  Otherwise, let `D` represent the dynamic type of `E` as follows:

If the type of `E` is a reference type, `D` is the run-time type of the instance reference by `E`.

If the type of `E` is a nullable type, `D` is the underlying type of that nullable type.

If the type of `E` is a non-nullable value type, `D` is the type of `E`.

-  The result of the operation depends on `D` and `T` as follows:

If `T` is a reference type, the result is true if `D` and `T` are the same type, if `D` is a reference type and an implicit reference conversion from `D` to `T` exists, or if `D` is a value type and a boxing conversion from `D` to `T` exists.

If `T` is a nullable type, the result is true if `D` is the underlying type of `T`.

If `T` is a non-nullable value type, the result is true if `D` and `T` are the same type.

Otherwise, the result is false.

Note that user defined conversions, are not considered by the `is` operator.

### The as operator

The `as` operator is used to explicitly convert a value to a given reference type or nullable type. Unlike a cast expression (§7.7.6), the `as` operator never throws an exception. Instead, if the indicated conversion is not possible, the resulting value is `null`.

In an operation of the form `E``as``T`, `E` must be an expression and `T` must be a reference type, a type parameter known to be a reference type, or a nullable type. Furthermore, at least one of the following must be true, or otherwise a compile-time error occurs:

-  An identity (§6.1.1), implicit nullable (§6.1.4), implicit reference (§6.1.6), boxing (§6.1.7), explicit nullable (§6.2.3), explicit reference (§6.2.4), or unboxing (§6.2.5) conversion exists from `E` to `T`.
-  The type of `E` or `T` is an open type.
-  `E` is the `null` literal.

If the compile-time type of `E` is not `dynamic`, the operation `E``as``T` produces the same result as

```csharp
E is T ? (T)(E) : (T)null
```

-  except that `E` is only evaluated once. The compiler can be expected to optimize `E``as``T` to perform at most one dynamic type check as opposed to the two dynamic type checks implied by the expansion above.
-  If the compile-time type of `E` is `dynamic`, unlike the cast operator the `as` operator is not dynamically bound (§7.2.2). Therefore the expansion in this case is:

```csharp
E is T ? (T)(object)(E) : (T)null
```

Note that some conversions, such as user defined conversions, are not possible with the `as` operator and should instead be performed using cast expressions.

In the example

```csharp
class X
{

    public string F(object o) {
        return o as string;        // OK, string is a reference type
    }

    public T G<T>(object o) where T: Attribute {
        return o as T;                // Ok, T has a class constraint
    }

    public U H<U>(object o) {
        return o as U;                // Error, U is unconstrained 
    }
}
```

the type parameter `T` of `G` is known to be a reference type, because it has the class constraint. The type parameter `U` of `H` is not however; hence the use of the `as` operator in `H` is disallowed.

## Logical operators

The `&amp;`, `^`, and `|` operators are called the logical operators.

<pre>and-expression:
equality-expression
and-expression   <b>&amp;</b>   equality-expression</pre>

<pre>exclusive-or-expression:
and-expression
exclusive-or-expression   <b>^</b>   and-expression</pre>

<pre>inclusive-or-expression:
exclusive-or-expression
inclusive-or-expression   <b>|</b>   exclusive-or-expression</pre>

If an operand of a logical operator has the compile-time type `dynamic`, then the expression is dynamically bound (§7.2.2). In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.

For an operation of the form `x`*op*`y`, where *op* is one of the logical operators, overload resolution (§7.3.4) is applied to select a specific operator implementation. The operands are converted to the parameter types of the selected operator, and the type of the result is the return type of the operator.

The predefined logical operators are described in the following sections.

### Integer logical operators

The predefined integer logical operators are:

```csharp
int operator `&amp;`(int x, int y);
uint operator `&amp;`(uint x, uint y);
long operator `&amp;`(long x, long y);
ulong operator `&amp;`(ulong x, ulong y);

int operator `|`(int x, int y);
uint operator `|`(uint x, uint y);
long operator `|`(long x, long y);
ulong operator `|`(ulong x, ulong y);

int operator `^`(int x, int y);
uint operator `^`(uint x, uint y);
long operator `^`(long x, long y);
ulong operator `^`(ulong x, ulong y);
```

The `&amp;` operator computes the bitwise logical `AND` of the two operands, the `|` operator computes the bitwise logical `OR` of the two operands, and the `^` operator computes the bitwise logical exclusive `OR` of the two operands. No overflows are possible from these operations.

### Enumeration logical operators

Every enumeration type `E` implicitly provides the following predefined logical operators:

```csharp
E operator `&amp;`(E x, E y);
E operator `|`(E x, E y);
E operator `^`(E x, E y);
```

The result of evaluating `x`*op*`y`, where `x` and `y` are expressions of an enumeration type `E` with an underlying type `U`, and *op* is one of the logical operators, is exactly the same as evaluating (E)`((U)x`*op*`(U)y)`. In other words, the enumeration type logical operators simply perform the logical operation on the underlying type of the two operands.

### Boolean logical operators

The predefined boolean logical operators are:

```csharp
bool operator `&amp;`(bool x, bool y);

bool operator `|`(bool x, bool y);

bool operator `^`(bool x, bool y);
```

The result of `x``&amp;``y` is `true` if both `x` and `y` are `true`. Otherwise, the result is `false`.

The result of `x``|``y` is `true` if either `x` or `y` is `true`. Otherwise, the result is `false`.

The result of `x``^``y` is `true` if `x` is `true` and `y` is `false`, or `x` is `false` and `y` is `true`. Otherwise, the result is `false`. When the operands are of type `bool`, the `^` operator computes the same result as the `!=` operator.

### Nullable boolean logical operators

The nullable boolean type `bool?` can represent three values, `true`, `false`, and `null`, and is conceptually similar to the three-valued type used for boolean expressions in SQL. To ensure that the results produced by the `&amp;` and `|` operators for `bool?` operands are consistent with SQL's three-valued logic, the following predefined operators are provided:

```csharp
bool? operator &amp;(bool? x, bool? y);

bool? operator |(bool? x, bool? y);
```

The following table lists the results produced by these operators for all combinations of the values `true`, `false`, and `null`.


| `x` | `y` | `x &amp; y` | `x | y` | 
| `true` | `true` | `true` | `true` | 
| `true` | `false` | `false` | `true` | 
| `true` | `null` | `null` | `true` | 
| `false` | `true` | `false` | `true` | 
| `false` | `false` | `false` | `false` | 
| `false` | `null` | `false` | `null` | 
| `null` | `true` | `null` | `true` | 
| `null` | `false` | `false` | `null` | 
| `null` | `null` | `null` | `null` | 

## Conditional logical operators

The `&amp;&amp;` and `||` operators are called the conditional logical operators. They are also called the "short-circuiting" logical operators.

<pre>conditional-and-expression:
inclusive-or-expression
conditional-and-expression   <b>&amp;&amp;</b>   inclusive-or-expression</pre>

<pre>conditional-or-expression:
conditional-and-expression
conditional-or-expression   <b>||</b>   conditional-and-expression</pre>

The `&amp;&amp;` and `||` operators are conditional versions of the `&amp;` and `|` operators:

-  The operation `x``&amp;&amp;``y` corresponds to the operation `x``&amp;``y`, except that `y` is evaluated only if `x` is not `false`.
-  The operation `x``||``y` corresponds to the operation `x``|``y`, except that `y` is evaluated only if `x` is not `true`.

If an operand of a conditional logical operator has the compile-time type `dynamic`, then the expression is dynamically bound (§7.2.2). In this case the compile-time type of the expression is `dynamic`, and the resolution described below will take place at run-time using the run-time type of those operands that have the compile-time type `dynamic`.

An operation of the form `x``&amp;&amp;``y` or `x``||``y` is processed by applying overload resolution (§7.3.4) as if the operation was written `x``&amp;``y` or `x``|``y`. Then,

-  If overload resolution fails to find a single best operator, or if overload resolution selects one of the predefined integer logical operators, a binding-time error occurs.
-  Otherwise, if the selected operator is one of the predefined boolean logical operators (§7.11.3) or nullable boolean logical operators (§7.11.4), the operation is processed as described in §7.12.1.
-  Otherwise, the selected operator is a user-defined operator, and the operation is processed as described in §7.12.2.

It is not possible to directly overload the conditional logical operators. However, because the conditional logical operators are evaluated in terms of the regular logical operators, overloads of the regular logical operators are, with certain restrictions, also considered overloads of the conditional logical operators. This is described further in §7.12.2.

### Boolean conditional logical operators

When the operands of `&amp;&amp;` or `||` are of type `bool`, or when the operands are of types that do not define an applicable `operator``&amp;` or `operator``|`, but do define implicit conversions to `bool`, the operation is processed as follows:

-  The operation `x``&amp;&amp;``y` is evaluated as `x``?``y``:``false`. In other words, `x` is first evaluated and converted to type `bool`. Then, if `x` is `true`, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation. Otherwise, the result of the operation is `false`.
-  The operation `x``||``y` is evaluated as `x``?``true``:``y`. In other words, `x` is first evaluated and converted to type `bool`. Then, if `x` is `true`, the result of the operation is `true`. Otherwise, `y` is evaluated and converted to type `bool`, and this becomes the result of the operation.

### User-defined conditional logical operators

When the operands of `&amp;&amp;` or `||` are of types that declare an applicable user-defined `operator` `&amp;` or `operator` `|`, both of the following must be true, where `T` is the type in which the selected operator is declared:

-  The return type and the type of each parameter of the selected operator must be `T`. In other words, the operator must compute the logical `AND` or the logical `OR` of two operands of type `T`, and must return a result of type `T`.
-  `T` must contain declarations of `operator``true` and `operator``false`.

A binding-time error occurs if either of these requirements is not satisfied. Otherwise, the `&amp;&amp;` or `||` operation is evaluated by combining the user-defined `operator``true` or `operator``false` with the selected user-defined operator:

-  The operation `x``&amp;&amp;``y` is evaluated as `T.false(x)``?``x``:``T.&amp;(x,``y)`, where `T.false(x)` is an invocation of the `operator``false` declared in `T`, and `T.&amp;(x,``y)` is an invocation of the selected `operator``&amp;`. In other words, `x` is first evaluated and `operator``false` is invoked on the result to determine if `x` is definitely false. Then, if `x` is definitely false, the result of the operation is the value previously computed for `x`. Otherwise, `y` is evaluated, and the selected `operator``&amp;` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.
-  The operation `x``||``y` is evaluated as `T.true(x)``?``x``:``T.|(x,``y)`, where `T.true(x)` is an invocation of the `operator``true` declared in `T`, and `T.|(x,``y)` is an invocation of the selected `operator``|`. In other words, `x` is first evaluated and `operator``true` is invoked on the result to determine if `x` is definitely true. Then, if `x` is definitely true, the result of the operation is the value previously computed for `x`. Otherwise, `y` is evaluated, and the selected `operator``|` is invoked on the value previously computed for `x` and the value computed for `y` to produce the result of the operation.

In either of these operations, the expression given by `x` is only evaluated once, and the expression given by `y` is either not evaluated or evaluated exactly once.

For an example of a type that implements `operator``true` and `operator``false`, see §11.4.2.

## The null coalescing operator

The `??` operator is called the null coalescing operator.

<pre>null-coalescing-expression:
conditional-or-expression
conditional-or-expression   <b>??</b>   null-coalescing-expression</pre>

A null coalescing expression of the form `a``??``b` requires `a` to be of a nullable type or reference type. If `a` is non-null, the result of `a``??``b` is `a`; otherwise, the result is `b`. The operation evaluates `b` only if `a` is null.

The null coalescing operator is right-associative, meaning that operations are grouped from right to left. For example, an expression of the form `a``??``b``??``c` is evaluated as `a``??``(b``??``c)`. In general terms, an expression of the form `E`<sub>1</sub>`??``E`<sub>2</sub>`??` ... `??``E`<sub>N</sub> returns the first of the operands that is non-null, or null if all operands are null.

The type of the expression `a``??``b` depends on which implicit conversions are available on the operands. In order of preference, the type of `a``??``b` is `A`<sub>0</sub>, `A`, or `B`, where `A` is the type of `a` (provided that `a` has a type), `B` is the type of `b` (provided that `b` has a type), and `A`<sub>0</sub> is the underlying type of `A` if `A` is a nullable type, or `A` otherwise. Specifically, `a``??``b` is processed as follows:

-  If `A` exists and is not a nullable type or a reference type, a compile-time error occurs.
-  If `b` is a dynamic expression, the result type is `dynamic`. At run-time, `a` is first evaluated. If `a` is not null, `a` is converted to dynamic, and this becomes the result. Otherwise, `b` is evaluated, and this becomes the result.
-  Otherwise, if `A` exists and is a nullable type and an implicit conversion exists from `b` to `A`<sub>0</sub>, the result type is `A`<sub>0</sub>. At run-time, `a` is first evaluated. If `a` is not null, `a` is unwrapped to type `A`<sub>0</sub>, and this becomes the result. Otherwise, `b` is evaluated and converted to type `A`<sub>0</sub>, and this becomes the result.
-  Otherwise, if `A` exists and an implicit conversion exists from `b` to `A`, the result type is `A`. At run-time, `a` is first evaluated. If `a` is not null, `a` becomes the result. Otherwise, `b` is evaluated and converted to type `A`, and this becomes the result.
-  Otherwise, if `b` has a type `B` and an implicit conversion exists from `a` to `B`, the result type is `B`. At run-time, `a` is first evaluated. If `a` is not null, `a` is unwrapped to type `A`<sub>0</sub> (if `A` exists and is nullable) and converted to type `B`, and this becomes the result. Otherwise, `b` is evaluated and becomes the result.
-  Otherwise, `a` and `b` are incompatible, and a compile-time error occurs.

## Conditional operator

The `?:` operator is called the conditional operator. It is at times also called the ternary operator.

<pre>conditional-expression:
null-coalescing-expression
null-coalescing-expression   <b>?</b>   expression   <b>:</b>   expression</pre>

A conditional expression of the form `b``?``x``:``y` first evaluates the condition `b`. Then, if `b` is `true`, `x` is evaluated and becomes the result of the operation. Otherwise, `y` is evaluated and becomes the result of the operation. A conditional expression never evaluates both `x` and `y`.

The conditional operator is right-associative, meaning that operations are grouped from right to left. For example, an expression of the form `a``?``b``:``c``?``d``:``e` is evaluated as `a``?``b``:``(c``?``d``:``e)`.

The first operand of the `?:` operator must be an expression that can be implicitly converted to `bool`, or an expression of a type that implements `operator``true`. If neither of these requirements is satisfied, a compile-time error occurs.

The second and third operands, `x` and `y`, of the `?:` operator control the type of the conditional expression.

-  If `x` has type `X` and `y` has type `Y` then

If an implicit conversion (§6.1) exists from `X` to `Y`, but not from `Y` to `X`, then `Y` is the type of the conditional expression.

If an implicit conversion (§6.1) exists from `Y` to `X`, but not from `X` to `Y`, then `X` is the type of the conditional expression.

Otherwise, no expression type can be determined, and a compile-time error occurs.

-  If only one of `x` and `y` has a type, and both `x` and `y`, of areimplicitly convertible to that type, then that is the type of the conditional expression.
-  Otherwise, no expression type can be determined, and a compile-time error occurs.

The run-time processing of a conditional expression of the form `b``?``x``:``y` consists of the following steps:

-  First, `b` is evaluated, and the `bool` value of `b` is determined:

If an implicit conversion from the type of `b` to `bool` exists, then this implicit conversion is performed to produce a `bool` value.

Otherwise, the `operator``true` defined by the type of `b` is invoked to produce a `bool` value.

-  If the `bool` value produced by the step above is `true`, then `x` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.
-  Otherwise, `y` is evaluated and converted to the type of the conditional expression, and this becomes the result of the conditional expression.

## Anonymous function expressions

An *__anonymous function__* is an expression that represents an "in-line" method definition. An anonymous function does not have a value or type in and of itself, but is convertible to a compatible delegate or expression tree type. The evaluation of an anonymous function conversion depends on the target type of the conversion: If it is a delegate type, the conversion evaluates to a delegate value referencing the method which the anonymous function defines. If it is an expression tree type, the conversion evaluates to an expression tree which represents the structure of the method as an object structure.

For historical reasons there are two syntactic flavors of anonymous functions, namely *lambda-expression*s and *anonymous-method-expression*s. For almost all purposes, *lambda-expression*s are more concise and expressive than *anonymous-method-expression*s, which remain in the language for backwards compatibility.

<pre>lambda-expression:
<b>async</b><sub>*opt*</sub>   anonymous-function-signature   <b>=></b>   anonymous-function-body</pre>

<pre>anonymous-method-expression:
<b>async</b><sub>*opt*</sub><b>delegate</b>   explicit-anonymous-function-signature<sub>opt</sub>   block</pre>

<pre>anonymous-function-signature:
explicit-anonymous-function-signature 
implicit-anonymous-function-signature</pre>

<pre>explicit-anonymous-function-signature:
<b>(</b>   explicit-anonymous-function-parameter-list<sub>*opt*</sub>**`*)*`</pre>

<pre>explicit-anonymous-function-parameter-list:
explicit-anonymous-function-parameter
explicit-anonymous-function-parameter-list   <b>,</b>   explicit-anonymous-function-parameter</pre>

<pre>explicit-anonymous-function-parameter:
anonymous-function-parameter-modifier<sub>opt</sub>   type   identifier</pre>

<pre>anonymous-function-parameter-modifier: 
<b>ref</b>
<b>out</b></pre>

<pre>implicit-anonymous-function-signature:
`*(*`** implicit-anonymous-function-parameter-list<sub>*opt*</sub>**`*)*``
`implicit-anonymous-function-parameter</pre>

<pre>implicit-anonymous-function-parameter-list:
implicit-anonymous-function-parameter
implicit-anonymous-function-parameter-list   <b>,</b>   implicit-anonymous-function-parameter</pre>

<pre>implicit-anonymous-function-parameter:
identifier</pre>

<pre>anonymous-function-body:
expression
block</pre>

The `=>` operator has the same precedence as assignment (`=`) and is right-associative.

An anonymous function with the `async` modifier is an async function and follows the rules described in §10.14.

The parameters of an anonymous function in the form of a *lambda**-**expression* can be explicitly or implicitly typed. In an explicitly typed parameter list, the type of each parameter is explicitly stated. In an implicitly typed parameter list, the types of the parameters are inferred from the context in which the anonymous function occurs—specifically, when the anonymous function is converted to a compatible delegate type or expression tree type, that type provides the parameter types (§6.5).

In an anonymous function with a single, implicitly typed parameter, the parentheses may be omitted from the parameter list. In other words, an anonymous function of the form

```csharp
( *param* ) => *expr*
```

can be abbreviated to

```csharp
*param* => *expr*
```

The parameter list of an anonymous function in the form of an *anonymous-method-expression* is optional. If given, the parameters must be explicitly typed. If not, the anonymous function is convertible to a delegate with any parameter list not containing `out` parameters.

A *block* body of an anonymous function is reachable (§8.1) unless the anonymous function occurs inside an unreachable statement.

Some examples of anonymous functions follow below:

```csharp
x => x + 1                                        // Implicitly typed, expression body

x => { return x + 1; }                        // Implicitly typed, statement body

(int x) => x + 1                                // Explicitly typed, expression body

(int x) => { return x + 1; }                // Explicitly typed, statement body

(x, y) => x * y                                // Multiple parameters

() => Console.WriteLine()                    // No parameters

async (t1,t2) => await t1 + await t2    // Async

delegate (int x) { return x + 1; }        // Anonymous method expression

delegate { return 1 + 1; }                    // Parameter list omitted
```

The behavior of *l**ambda**-**expression*s and *anonymous**-method-expression*s is the same except for the following points:

-  *anonymous-method-expression*s permit the parameter list to be omitted entirely, yielding convertibility to delegate types of any list of value parameters.
-  *lambda-expression*s permit parameter types to be omitted and inferred whereas *anonymous-method-expression*s require parameter types to be explicitly stated.
-  The body of a *lambda-expression* can be an expression or a statement block whereas the body of an *anonymous-method-expression* must be a statement block.
-  Only *lambda-expression*s have conversions to compatible expression tree types (§4.6).

### Anonymous function signatures

The optional *anonymous-function-signature* of an anonymous function defines the names and optionally the types of the formal parameters for the anonymous function. The scope of the parameters of the anonymous function is the *anonymous-function-body*. (§3.7) Together with the parameter list (if given) the anonymous-method-body constitutes a declaration space (§3.3). It is thus a compile-time error for the name of a parameter of the anonymous function to match the name of a local variable, local constant or parameter whose scope includes the *anonymous-method-expression* or *lambda-expression*.

If an anonymous function has an *explicit-a**nonymous-**function**-signature*, then the set of compatible delegate types and expression tree types is restricted to those that have the same parameter types and modifiers in the same order. In contrast to method group conversions (§6.6), contra-variance of anonymous function parameter types is not supported. If an anonymous function does not have an *anonymous-**function**-signature*, then the set of compatible delegate types and expression tree types is restricted to those that have no `out` parameters.

Note that an *anonymous-**function**-signature* cannot include attributes or a parameter array. Nevertheless, an *anonymous-**function**-signature* may be compatible with a delegate type whose parameter list contains a parameter array.

Note also that conversion to an expression tree type, even if compatible, may still fail at compile-time (§4.6).

### Anonymous function bodies

The body (*expression* or *block*) of an anonymous function is subject to the following rules:

-  If the anonymous function includes a signature, the parameters specified in the signature are available in the body. If the anonymous function has no signature it can be converted to a delegate type or expression type having parameters (§6.5), but the parameters cannot be accessed in the body.
-  Except for `ref` or `out` parameters specified in the signature (if any) of the nearest enclosing anonymous function, it is a compile-time error for the body to access a `ref` or `out` parameter.
-  When the type of `this` is a struct type, it is a compile-time error for the body to access `this`. This is true whether the access is explicit (as in `this.x`) or implicit (as in `x` where `x` is an instance member of the struct). This rule simply prohibits such access and does not affect whether member lookup results in a member of the struct.
-  The body has access to the outer variables (§7.15.5) of the anonymous function. Access of an outer variable will reference the instance of the variable that is active at the time the *lambda-expression* or *anonymous-method-expression* is evaluated (§7.15.6).
-  It is a compile-time error for the body to contain a `goto` statement, `break` statement, or `continue` statement whose target is outside the body or within the body of a contained anonymous function.
-  A `return` statement in the body returns control from an invocation of the nearest enclosing anonymous function, not from the enclosing function member. An expression specified in a `return` statement must be implicitly convertible to the return type of the delegate type or expression tree type to which the nearest enclosing *lambda-expression* or *anonymous-method-expression* is converted (§6.5).

It is explicitly unspecified whether there is any way to execute the block of an anonymous function other than through evaluation and invocation of the *lambda-expression* or *anonymous-method-expression*. In particular, the compiler may choose to implement an anonymous function by synthesizing one or more named methods or types. The names of any such synthesized elements must be of a form reserved for compiler use.

### Overload resolution

Anonymous functions in an argument list participate in type inference and overload resolution. Please refer to §7.5.2 and §7.5.3 for the exact rules.

The following example illustrates the effect of anonymous functions on overload resolution.

```csharp
class ItemList<T>: List<T>
{
    public int Sum(Func<T,int> selector) {
        int sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }

    public double Sum(Func<T,double> selector) {
        double sum = 0;
        foreach (T item in this) sum += selector(item);
        return sum;
    }
}
```

The `ItemList<T>` class has two `Sum` methods. Each takes a `selector` argument, which extracts the value to sum over from a list item. The extracted value can be either an `int` or a `double` and the resulting sum is likewise either an `int` or a `double`.

The `Sum` methods could for example be used to compute sums from a list of detail lines in an order.

```csharp
class Detail
{
    public int UnitCount;
    public double UnitPrice;
    ...
}

void ComputeSums() {
    ItemList<Detail> orderDetails = GetOrderDetails(...);
    int totalUnits = orderDetails.Sum(d => d.UnitCount);
    double orderTotal = orderDetails.Sum(d => d.UnitPrice * d.UnitCount);
    ...
}
```

In the first invocation of `orderDetails.Sum`, both `Sum` methods are applicable because the anonymous function `d``=>``d.``UnitCount` is compatible with both `Func``<Detail,int>` and `Func``<Detail,double>`. However, overload resolution picks the first `Sum` method because the conversion to `Func``<Detail,int>` is better than the conversion to `Func``<Detail,double>`.

In the second invocation of `orderDetails.Sum`, only the second `Sum` method is applicable because the anonymous function `d``=>``d.UnitPrice``*``d.UnitCount` produces a value of type `double`. Thus, overload resolution picks the second `Sum` method for that invocation.

### Anonymous functions and dynamic binding

An anonymous function cannot be a receiver, argument or operand of a dynamically bound operation.

### Outer variables

Any local variable, value parameter, or parameter array whose scope includes the *lambda-expression* or *anonymous-method-expression* is called an *__outer variable__* of the anonymous function. In an instance function member of a class, the `this` value is considered a value parameter and is an outer variable of any anonymous function contained within the function member.

#### Captured outer variables

When an outer variable is referenced by an anonymous function, the outer variable is said to have been *__captured__* by the anonymous function. Ordinarily, the lifetime of a local variable is limited to execution of the block or statement with which it is associated (§5.1.7). However, the lifetime of a captured outer variable is extended at least until the delegate or expression tree created from the anonymous function becomes eligible for garbage collection.

In the example

```csharp
using System;

delegate int D();

class Test
{
    static D F() {
        int x = 0;
        D result = () => ++x;
        return result;
    }

    static void Main() {
        D d = F();
        Console.WriteLine(d());
        Console.WriteLine(d());
        Console.WriteLine(d());
    }
}
```

the local variable `x` is captured by the anonymous function, and the lifetime of `x` is extended at least until the delegate returned from `F` becomes eligible for garbage collection (which doesn't happen until the very end of the program). Since each invocation of the anonymous function operates on the same instance of `x`, the output of the example is:

```csharp
1
2
3
```

When a local variable or a value parameter is captured by an anonymous function, the local variable or parameter is no longer considered to be a fixed variable (§18.3), but is instead considered to be a moveable variable. Thus any `unsafe` code that takes the address of a captured outer variable must first use the `fixed` statement to fix the variable.

Note that unlike an uncaptured variable, a captured local variable can be simultaneously exposed to multiple threads of execution.

#### Instantiation of local variables

A local variable is considered to be *__instantiated__* when execution enters the scope of the variable. For example, when the following method is invoked, the local variable `x` is instantiated and initialized three times—once for each iteration of the loop.

```csharp
static void F() {
    for (int i = 0; i < 3; i++) {
        int x = i * 2 + 1;
        ...
    }
}
```

However, moving the declaration of `x` outside the loop results in a single instantiation of `x`:

```csharp
static void F() {
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        ...
    }
}
```

When not captured, there is no way to observe exactly how often a local variable is instantiated—because the lifetimes of the instantiations are disjoint, it is possible for each instantation to simply use the same storage location. However, when an anonymous function captures a local variable, the effects of instantiation become apparent.

The example

```csharp
using System;

delegate void D();

class Test
{
    static D[] F() {
        D[] result = new D[3];
        for (int i = 0; i < 3; i++) {
            int x = i * 2 + 1;
            result[i] = () => { Console.WriteLine(x); };
        }
        return result;
    }

    static void Main() {
        foreach (D d in F()) d();
    }
}
```

produces the output:

```csharp
1
3
5
```

However, when the declaration of `x` is moved outside the loop:

```csharp
static D[] F() {
    D[] result = new D[3];
    int x;
    for (int i = 0; i < 3; i++) {
        x = i * 2 + 1;
        result[i] = () => { Console.WriteLine(x); };
    }
    return result;
}
```

the output is:

```csharp
5
5
5
```

If a for-loop declares an iteration variable, that variable itself is considered to be declared outside of the loop. Thus, if the example is changed to capture the iteration variable itself:

```csharp
static D[] F() {
    D[] result = new D[3];
    for (int i = 0; i < 3; i++) {
        result[i] = () => { Console.WriteLine(i); };
    }
    return result;
}
```

only one instance of the iteration variable is captured, which produces the output:

```csharp
3
3
3
```

It is possible for anonymous function delegates to share some captured variables yet have separate instances of others. For example, if `F` is changed to

```csharp
static D[] F() {
    D[] result = new D[3];
    int x = 0;
    for (int i = 0; i < 3; i++) {
        int y = 0;
        result[i] = () => { Console.WriteLine("{0} {1}", ++x, ++y); };
    }
    return result;
}
```

the three delegates capture the same instance of `x` but separate instances of `y`, and the output is:

```csharp
1 1
2 1
3 1
```

Separate anonymous functions can capture the same instance of an outer variable. In the example:

```csharp
using System;

delegate void Setter(int value);

delegate int Getter();

class Test
{
    static void Main() {
        int x = 0;
        Setter s = (int value) => { x = value; };
        Getter g = () => { return x; };
        s(5);
        Console.WriteLine(g());
        s(10);
        Console.WriteLine(g());
    }
}
```

the two anonymous functions capture the same instance of the local variable `x`, and they can thus "communicate" through that variable. The output of the example is:

```csharp
5
10
```

### Evaluation of anonymous function expressions

An anonymous function `F` must always be converted to a delegate type `D` or an expression tree type `E`, either directly or through the execution of a delegate creation expression `new D(F)`. This conversion determines the result of the anonymous function, as described in §6.5.

## Query expressions

*__Query expressions__* provide a language integrated syntax for queries that is similar to relational and hierarchical query languages such as SQL and XQuery.

<pre>query-expression:
from-clause   query-body</pre>

<pre>from-clause:
<b>from</b>   type<sub>opt</sub>   identifier   <b>in</b>   expression</pre>

<pre>query-body:
query-body-clauses<sub>opt</sub>   select-or-group-clause   query-continuation<sub>opt</sub></pre>

<pre>query-body-clauses:
query-body-clause
query-body-clauses   query-body-clause</pre>

<pre>query-body-clause:
from-clause
let-clause
where-clause
join-clause
join-into-clause
orderby-clause</pre>

<pre>let-clause:
<b>let</b>   identifier   <b>=</b>   expression</pre>

<pre>where-clause:
<b>where</b>   boolean-expression</pre>

<pre>join-clause:
<b>join</b>   type<sub>opt</sub>   identifier   <b>in</b>   expression   <b>on</b>   expression   <b>equals</b>   expression </pre>

<pre>join-into-clause:
<b>j</b><b>oin</b>   type<sub>opt</sub>   identifier   <b>in</b>   expression   <b>on</b>   expression   <b>equals</b>   expression   <b>into</b>   identifier</pre>

<pre>orderby-clause:
<b>orderby</b>   orderings</pre>

<pre>orderings:
ordering
orderings   <b>,</b>   ordering</pre>

<pre>ordering:
expression    ordering-direction<sub>opt</sub></pre>

<pre>ordering-direction:
<b>ascending</b>
<b>descending</b></pre>

<pre>select-or-group-clause:
select-clause
group-clause</pre>

<pre>select-clause:
<b>select</b>   expression</pre>

<pre>group-clause:
<b>group</b>   expression   <b>by</b>   expression</pre>

<pre>query-continuation:
<b>into</b>   identifier   query-body</pre>

A query expression begins with a `from` clause and ends with either a `select` or `group` clause. The initial `from` clause can be followed by zero or more `from`, `let`, `where`, `join` or `orderby` clauses. Each `from` clause is a generator introducing a *__range variable__* which ranges over the elements of a *__sequence__*. Each `let` clause introduces a range variable representing a value computed by means of previous range variables. Each `where` clause is a filter that excludes items from the result. Each `join` clause compares specified keys of the source sequence with keys of another sequence, yielding matching pairs. Each `orderby` clause reorders items according to specified criteria.The final `select` or `group` clause specifies the shape of the result in terms of the range variables. Finally, an `into` clause can be used to "splice" queries by treating the results of one query as a generator in a subsequent query.

### Ambiguities in query expressions

Query expressions contain a number of "contextual keywords", i.e., identifiers that have special meaning in a given context. Specifically these are `from`, `where`, `join`, `on`, `equals`, `into`, `let`, `orderby`, `ascending`, `descending`, `select`, `group` and `by`. In order to avoid ambiguities in query expressions caused by mixed use of these identifiers as keywords or simple names, these identifiers are considered keywords when occurring anywhere within a query expression.

For this purpose, a query expression is any expression that starts with "`from`*identifier*" *followed by any token except "*`;`*", "*`=`*" or "*`,`*".*

*In order to use these words as identifiers within a query expression, they can be prefixed with "*`@`*" (§2.4.2).*

### Query expression translation

The C# language does not specify the execution semantics of query expressions. Rather, query expressions are translated into invocations of methods that adhere to the *query expression pattern* (§7.16.3). Specifically, query expressions are translated into invocations of methods named `Where`, `Select`, `SelectMany`, `Join`, `Group``Join`, `OrderBy`, `OrderByDescending`, `ThenBy`, `ThenByDescending`, `GroupBy`, and `Cast`.These methods are expected to have particular signatures and result types, as described in §7.16.3. These methods can be instance methods of the object being queried or extension methods that are external to the object, and they implement the actual execution of the query.

The translation from query expressions to method invocations is a syntactic mapping that occurs before any type binding or overload resolution has been performed. The translation is guaranteed to be syntactically correct, but it is not guaranteed to produce semantically correct C# code. Following translation of query expressions, the resulting method invocations are processed as regular method invocations, and this may in turn uncover errors, for example if the methods do not exist, if arguments have wrong types, or if the methods are generic and type inference fails.

A query expression is processed by repeatedly applying the following translations until no further reductions are possible. The translations are listed in order of application: each section assumes that the translations in the preceding sections have been performed exhaustively, and once exhausted, a section will not later be revisited in the processing of the same query expression.

Assignment to range variables is not allowed in query expressions. However a C# implementation is permitted to not always enforce this restriction, since this may sometimes not be possible with the syntactic translation scheme presented here.

Certain translations inject range variables with transparent identifiers denoted by `*`. The special properties of transparent identifiers are discussed further in §7.16.2.7.

#### Select and groupby clauses with continuations

A query expression with a continuation

```csharp
from *…* into *x**…*
```

is translated into

```csharp
from *x* in ( from *…* ) *…*
```

The translations in the following sections assume that queries have no `into` continuations.

The example

```csharp
from c in customers
group c by c.Country into g
select new { Country = g.Key, CustCount = g.Count() }
```

is translated into

```csharp
from g in
    from c in customers
    group c by c.Country
select new { Country = g.Key, CustCount = g.Count() }
```

the final translation of which is

```csharp
customers.
GroupBy(c => c.Country).
Select(g => new { Country = g.Key, CustCount = g.Count() })
```

#### Explicit range variable types

A `from` clause that explicitly specifies a range variable type

```csharp
from *T**x* in *e*
```

is translated into

```csharp
from *x* in ( *e* ) . Cast < *T* > ( )
```

A `join` clause that explicitly specifies a range variable type

```csharp
join *T**x* in *e* on *k*<sub>1</sub> equals *k*<sub>2</sub>
```

is translated into

```csharp
join *x* in ( *e* ) . Cast < *T* > ( ) on *k*<sub>1</sub> equals *k*<sub>2</sub>
```

The translations in the following sections assume that queries have no explicit range variable types.

The example

```csharp
from Customer c in customers
where c.City == "London"
select c
```

is translated into

```csharp
from c in customers.Cast<Customer>()
where c.City == "London"
select c
```

the final translation of which is

```csharp
customers.
Cast<Customer>().
Where(c => c.City == "London")
```

Explicit range variable types are useful for querying collections that implement the non-generic `IEnumerable` interface, but not the generic `IEnumerable<T>` interface. In the example above, this would be the case if `customers` were of type `ArrayList`.

#### Degenerate query expressions

A query expression of the form

```csharp
from *x* in *e* select *x*
```

is translated into

```csharp
( *e* ) . Select ( *x* => *x* )
```

The example

```csharp
from c in customers
select c
```

Is translated into

```csharp
customers.Select(c => c)
```

A degenerate query expression is one that trivially selects the elements of the source. A later phase of the translation removes degenerate queries introduced by other translation steps by replacing them with their source. It is important however to ensure that the result of a query expression is never the source object itself, as that would reveal the type and identity of the source to the client of the query. Therefore this step protects degenerate queries written directly in source code by explicitly calling `Select` on the source. It is then up to the implementers of `Select` and other query operators to ensure that these methods never return the source object itself.

#### From, let, where, join and orderby clauses

A query expression with a second `from` clause followed by a `select` clause

```csharp
from *x*<sub>1</sub> in *e*<sub>1</sub>
from *x*<sub>2</sub> in *e*<sub>2</sub>
select *v*
```

is translated into

```csharp
( *e*<sub>1</sub> ) . SelectMany( *x*<sub>1</sub> => *e*<sub>2</sub> , ( *x*<sub>1</sub> , *x*<sub>2</sub> ) => *v* )
```

A query expression with a second `from` clause followed by something other than a `select` clause:

```csharp
from *x*<sub>1</sub> in *e*<sub>1</sub>
from *x*<sub>2</sub> in *e*<sub>2</sub>
…
```

is translated into

```csharp
from * in ( *e*<sub>1</sub> ) . SelectMany( *x*<sub>1</sub> => *e*<sub>2</sub> , ( *x*<sub>1</sub> , *x*<sub>2</sub> ) => new { *x*<sub>1</sub> , *x*<sub>2</sub> } )
…
```

A query expression with a `let` clause

```csharp
from *x* in *e*
let *y* = *f**
…*
```

is translated into

```csharp
from * in ( *e* ) . Select ( *x* => new { *x* , *y* = *f* } )
…
```

A query expression with a `where` clause

```csharp
from *x* in *e*
where *f**
…*
```

is translated into

```csharp
from *x* in ( *e* ) . Where ( *x* => *f* )
…
```

A query expression with a `join` clause without an `into` followed by a `select` clause

```csharp
from *x*<sub>1</sub> in *e*<sub>1</sub>
join *x*<sub>2</sub> in *e*<sub>2</sub> on *k*<sub>1</sub> equals *k*<sub>2</sub>
select *v*
```

is translated into

```csharp
( *e*<sub>1</sub> ) . Join( *e*<sub>2</sub> , *x*<sub>1</sub> => *k*<sub>1</sub> , *x*<sub>2</sub> => *k*<sub>2</sub> , ( *x*<sub>1</sub> , *x*<sub>2</sub> ) => *v* )
```

A query expression with a `join` clause without an `into` followed by something other than a `select` clause

```csharp
from *x*<sub>1</sub> in *e*<sub>1</sub>
join *x*<sub>2</sub> in *e*<sub>2</sub> on *k*<sub>1</sub> equals *k*<sub>2</sub>
…
```

is translated into

```csharp
from * in ( *e*<sub>1</sub> ) . Join(
*e*<sub>2</sub> , *x*<sub>1</sub> => *k*<sub>1</sub> , *x*<sub>2</sub> => *k*<sub>2</sub> , ( *x*<sub>1</sub> , *x*<sub>2</sub> ) => new { *x*<sub>1</sub> , *x*<sub>2</sub> })
…
```

A query expression with a `join` clause with an `into` followed by a `select` clause

```csharp
from *x*<sub>1</sub> in *e*<sub>1</sub>
join *x*<sub>2</sub> in *e*<sub>2</sub> on *k*<sub>1</sub> equals *k*<sub>2</sub> into *g*
select *v*
```

is translated into

```csharp
( *e*<sub>1</sub> ) . GroupJoin( *e*<sub>2</sub> , *x*<sub>1</sub> => *k*<sub>1</sub> , *x*<sub>2</sub> => *k*<sub>2</sub> , ( *x*<sub>1</sub> , *g* ) => *v* )
```

A query expression with a `join` clause with an `into` followed by something other than a `select` clause

```csharp
from *x*<sub>1</sub> in *e*<sub>1</sub>
join *x*<sub>2</sub> in *e*<sub>2</sub> on *k*<sub>1</sub> equals *k*<sub>2</sub> into *g*
…
```

is translated into

```csharp
from * in ( *e*<sub>1</sub> ) . GroupJoin(
*e*<sub>2</sub> , *x*<sub>1</sub> => *k*<sub>1</sub> , *x*<sub>2</sub> => *k*<sub>2</sub> , ( *x*<sub>1</sub> , *g* ) => new { *x*<sub>1</sub> , *g* })
…
```

A query expression with an `orderby` clause

```csharp
from *x* in *e*
orderby *k*<sub>1</sub> , *k*<sub>2</sub> , … ,``*k*<sub>n</sub>
*…*
```

is translated into

```csharp
from *x* in ( *e* ) . 
OrderBy ( *x* => *k*<sub>1</sub> ) . 
ThenBy ( *x* => *k*<sub>2</sub> ) .
*…* . 
ThenBy ( *x* => *k*<sub>n</sub> )
…
```

If an ordering clause specifies a `descending` direction indicator, an invocation of `OrderByDescending` or `ThenByDescending` is produced instead.

The following translations assume that there are no `let`, `where`, `join` or `orderby` clauses, and no more than the one initial `from` clause in each query expression.

The example

```csharp
from c in customers
from o in c.Orders
select new { c.Name, o.OrderID, o.Total }
```

is translated into

```csharp
customers.
SelectMany(c => c.Orders,
     (c,o) => new { c.Name, o.OrderID, o.Total }
)
```

The example

```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```

is translated into

```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.OrderID, o.Total }
```

the final translation of which is

```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.OrderID, x.o.Total })
```

where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.

The example

```csharp
from o in orders
let t = o.Details.Sum(d => d.UnitPrice * d.Quantity)
where t >= 1000
select new { o.OrderID, Total = t }
```

is translated into

```csharp
from * in orders.
    Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) })
where t >= 1000 
select new { o.OrderID, Total = t }
```

the final translation of which is

```csharp
orders.
Select(o => new { o, t = o.Details.Sum(d => d.UnitPrice * d.Quantity) }).
Where(x => x.t >= 1000).
Select(x => new { x.o.OrderID, Total = x.t })
```

where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.

The example

```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
select new { c.Name, o.OrderDate, o.Total }
```

is translated into

```csharp
customers.Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c.Name, o.OrderDate, o.Total })
```

The example

```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID into co
let n = co.Count()
where n >= 10
select new { c.Name, OrderCount = n }
```

is translated into

```csharp
from * in customers.
    GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
        (c, co) => new { c, co })
let n = co.Count()
where n >= 10 
select new { c.Name, OrderCount = n }
```

the final translation of which is

```csharp
customers.
GroupJoin(orders, c => c.CustomerID, o => o.CustomerID,
    (c, co) => new { c, co }).
Select(x => new { x, n = x.co.Count() }).
Where(y => y.n >= 10).
Select(y => new { y.x.c.Name, OrderCount = y.n)
```

where `x` and `y` are compiler generated identifiers that are otherwise invisible and inaccessible.

The example

```csharp
from o in orders
orderby o.Customer.Name, o.Total descending
select o
```

has the final translation

```csharp
orders.
OrderBy(o => o.Customer.Name).
ThenByDescending(o => o.Total)
```

#### Select clauses

A query expression of the form

```csharp
from *x* in *e* select *v*
```

is translated into

```csharp
( *e* ) . Select ( *x* => *v* )
```

except when *v* is the identifier *x*, the translation is simply

```csharp
( *e* )
```

For example

```csharp
from c in customers.Where(c => c.City == "London")
select c
```

is simply translated into

```csharp
customers.Where(c => c.City == "London")
```

#### Groupby clauses

A query expression of the form

```csharp
from *x* in *e* group *v* by *k*
```

is translated into

```csharp
( *e* ) . GroupBy ( *x* => *k* , *x* => *v* )
```

except when *v* is the identifier *x*, the translation is

```csharp
( *e* ) . GroupBy ( *x* => *k* )
```

The example

```csharp
from c in customers
group c.Name by c.Country
```

is translated into

```csharp
customers.
GroupBy(c => c.Country, c => c.Name)
```

#### Transparent identifiers

Certain translations inject range variables with *__transparent identifiers__* denoted by `*`. Transparent identifiers are not a proper language feature; they exist only as an intermediate step in the query expression translation process.

When a query translation injects a transparent identifier, further translation steps propagate the transparent identifier into anonymous functions and anonymous object initializers. In those contexts, transparent identifiers have the following behavior:

-  When a transparent identifier occurs as a parameter in an anonymous function, the members of the associated anonymous type are automatically in scope in the body of the anonymous function.
-  When a member with a transparent identifier is in scope, the members of that member are in scope as well.
-  When a transparent identifier occurs as a member declarator in an anonymous object initializer, it introduces a member with a transparent identifier.
-  In the translation steps described above, transparent identifiers are always introduced together with anonymous types, with the intent of capturing multiple range variables as members of a single object. An implementation of C# is permitted to use a different mechanism than anonymous types to group together multiple range variables. The following translation examples assume that anonymous types are used, and show how transparent identifiers can be translated away.

The example

```csharp
from c in customers
from o in c.Orders
orderby o.Total descending
select new { c.Name, o.Total }
```

is translated into

```csharp
from * in customers.
    SelectMany(c => c.Orders, (c,o) => new { c, o })
orderby o.Total descending
select new { c.Name, o.Total }
```

which is further translated into

```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(* => o.Total).
Select(* => new { c.Name, o.Total })
```

which, when transparent identifiers are erased, is equivalent to

```csharp
customers.
SelectMany(c => c.Orders, (c,o) => new { c, o }).
OrderByDescending(x => x.o.Total).
Select(x => new { x.c.Name, x.o.Total })
```

where `x` is a compiler generated identifier that is otherwise invisible and inaccessible.

The example

```csharp
from c in customers
join o in orders on c.CustomerID equals o.CustomerID
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```

is translated into

```csharp
from * in customers.
    Join(orders, c => c.CustomerID, o => o.CustomerID, 
        (c, o) => new { c, o })
join d in details on o.OrderID equals d.OrderID
join p in products on d.ProductID equals p.ProductID
select new { c.Name, o.OrderDate, p.ProductName }
```

which is further reduced to

```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID, (c, o) => new { c, o }).
Join(details, * => o.OrderID, d => d.OrderID, (*, d) => new { *, d }).
Join(products, * => d.ProductID, p => p.ProductID, (*, p) => new { *, p }).
Select(* => new { c.Name, o.OrderDate, p.ProductName })
```

the final translation of which is

```csharp
customers.
Join(orders, c => c.CustomerID, o => o.CustomerID,
    (c, o) => new { c, o }).
Join(details, x => x.o.OrderID, d => d.OrderID,
    (x, d) => new { x, d }).
Join(products, y => y.d.ProductID, p => p.ProductID,
    (y, p) => new { y, p }).
Select(z => new { z.y.x.c.Name, z.y.x.o.OrderDate, z.p.ProductName })
```

where `x`, `y`, and `z` are compiler generated identifiers that are otherwise invisible and inaccessible.

### The query expression pattern

The *__Query expression pattern__* establishes a pattern of methods that types can implement to support query expressions. Because query expressions are translated to method invocations by means of a syntactic mapping, types have considerable flexibility in how they implement the query expression pattern. For example, the methods of the pattern can be implemented as instance methods or as extension methods because the two have the same invocation syntax, and the methods can request delegates or expression trees because anonymous functions are convertible to both.

The recommended shape of a generic type `C<T>` that supports the query expression pattern is shown below. A generic type is used in order to illustrate the proper relationships between parameter and result types, but it is possible to implement the pattern for non-generic types as well.

```csharp
delegate R Func<T1,R>(T1 arg1);

delegate R Func<T1,T2,R>(T1 arg1, T2 arg2);

class C
{
    public C<T> Cast<T>();
}

class C<T> : C
{
    public C<T> Where(Func<T,bool> predicate);

    public C<U> Select<U>(Func<T,U> selector);

    public C<V> SelectMany<U,V>(Func<T,C<U>> selector,
        Func<T,U,V> resultSelector);

    public C<V> Join<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,U,V> resultSelector);

    public C<V> GroupJoin<U,K,V>(C<U> inner, Func<T,K> outerKeySelector,
        Func<U,K> innerKeySelector, Func<T,C<U>,V> resultSelector);

    public O<T> OrderBy<K>(Func<T,K> keySelector);

    public O<T> OrderByDescending<K>(Func<T,K> keySelector);

    public C<G<K,T>> GroupBy<K>(Func<T,K> keySelector);

    public C<G<K,E>> GroupBy<K,E>(Func<T,K> keySelector,
        Func<T,E> elementSelector);
}

class O<T> : C<T>
{
    public O<T> ThenBy<K>(Func<T,K> keySelector);

    public O<T> ThenByDescending<K>(Func<T,K> keySelector);
}

class G<K,T> : C<T>
{
    public K Key { get; }
}
```

The methods above use the generic delegate types `Func<T1``,``R>` and `Func<T1``,``T2,``R>`, but they could equally well have used other delegate or expression tree types with the same relationships in parameter and result types.

Notice the recommended relationship between `C<T>` and `O<T>` which ensures that the `ThenBy` and `ThenByDescending` methods are available only on the result of an `OrderBy` or `OrderByDescending`. Also notice the recommended shape of the result of `GroupBy`—a sequence of sequences, where each inner sequence has an additional `Key` property.

The `System.Linq` namespace provides an implementation of the query operator pattern for any type that implements the `System.Collections.Generic.I``Enumerable<T>` interface.

## Assignment operators

The assignment operators assign a new value to a variable, a property, an event, or an indexer element.

<pre>assignment:
unary-expression   assignment-operator   expression</pre>

<pre>assignment-operator:
<b>=</b>
<b>+=</b>
<b>-=</b>
<b>*=</b>
<b>/=</b>
<b>%=</b>
<b>&amp;=</b>
<b>|=</b>
<b>^=</b>
<b><<=</b>
right-shift-assignment</pre>

The left operand of an assignment must be an expression classified as a variable, a property access, an indexer access, or an event access.

The `=` operator is called the *__simple assignment operator__*. It assigns the value of the right operand to the variable, property, or indexer element given by the left operand. The left operand of the simple assignment operator may not be an event access (except as described in §10.8.1). The simple assignment operator is described in §7.17.1.

The assignment operators other than the `=` operator are called the *__compound assignment operators__*. These operators perform the indicated operation on the two operands, and then assign the resulting value to the variable, property, or indexer element given by the left operand. The compound assignment operators are described in §7.17.2.

The `+=` and `-=` operators with an event access expression as the left operand are called the *event assignment operators*. No other assignment operator is valid with an event access as the left operand. The event assignment operators are described in §7.17.3.

The assignment operators are right-associative, meaning that operations are grouped from right to left. For example, an expression of the form `a``=``b``=``c` is evaluated as `a``=` (`b``=``c)`.

### Simple assignment

The `=` operator is called the simple assignment operator.

If the left operand of a simple assignment is of the form `E.P` or `E[E`<sub>i</sub>`]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound (§7.2.2). In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.

In a simple assignment, the right operand must be an expression that is implicitly convertible to the type of the left operand. The operation assigns the value of the right operand to the variable, property, or indexer element given by the left operand.

The result of a simple assignment expression is the value assigned to the left operand. The result has the same type as the left operand and is always classified as a value.

If the left operand is a property or indexer access, the property or indexer must have a `set` accessor. If this is not the case, a binding-time error occurs.

The run-time processing of a simple assignment of the form `x``=``y` consists of the following steps:

-  If `x` is classified as a variable:

`x` is evaluated to produce the variable.

`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion (§6.1).

If the variable given by `x` is an array element of a *reference-type*, a run-time check is performed to ensure that the value computed for `y` is compatible with the array instance of which `x` is an element. The check succeeds if `y` is `null`, or if an implicit reference conversion (§6.1.6) exists from the actual type of the instance referenced by `y` to the actual element type of the array instance containing `x`. Otherwise, a `System.ArrayTypeMismatchException` is thrown.

The value resulting from the evaluation and conversion of `y` is stored into the location given by the evaluation of `x`.

-  If `x` is classified as a property or indexer access:

The instance expression (if `x` is not `static`) and the argument list (if `x` is an indexer access) associated with `x` are evaluated, and the results are used in the subsequent `set` accessor invocation.

`y` is evaluated and, if required, converted to the type of `x` through an implicit conversion (§6.1).

The `set` accessor of `x` is invoked with the value computed for `y` as its `value` argument.

The array co-variance rules (§12.5) permit a value of an array type `A[]` to be a reference to an instance of an array type `B[]`, provided an implicit reference conversion exists from `B` to `A`. Because of these rules, assignment to an array element of a *reference-type* requires a run-time check to ensure that the value being assigned is compatible with the array instance. In the example

```csharp
string[] sa = new string[10];
object[] oa = sa;

oa[0] = null;                    // Ok
oa[1] = "Hello";                // Ok
oa[2] = new ArrayList();    // ArrayTypeMismatchException
```

the last assignment causes a `System.ArrayTypeMismatchException` to be thrown because an instance of `ArrayList` cannot be stored in an element of a `string[]`.

When a property or indexer declared in a *struct-type* is the target of an assignment, the instance expression associated with the property or indexer access must be classified as a variable. If the instance expression is classified as a value, a binding-time error occurs. Because of §7.6.4, the same rule also applies to fields.

Given the declarations:

```csharp
struct Point
{
    int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int X {
        get { return x; }
        set { x = value; }
    }

    public int Y {
        get { return y; }
        set { y = value; }
    }
}

struct Rectangle
{
    Point a, b;

    public Rectangle(Point a, Point b) {
        this.a = a;
        this.b = b;
    }

    public Point A {
        get { return a; }
        set { a = value; }
    }

    public Point B {
        get { return b; }
        set { b = value; }
    }
}
```

in the example

```csharp
Point p = new Point();
p.X = 100;
p.Y = 100;
Rectangle r = new Rectangle();
r.A = new Point(10, 10);
r.B = p;
```

the assignments to `p.X`, `p.Y`, `r.A`, and `r.B` are permitted because `p` and `r` are variables. However, in the example

```csharp
Rectangle r = new Rectangle();
r.A.X = 10;
r.A.Y = 10;
r.B.X = 100;
r.B.Y = 100;
```

the assignments are all invalid, since `r.A` and `r.B` are not variables.

### Compound assignment

If the left operand of a compound assignment is of the form `E.P` or `E[E`<sub>i</sub>`]` where `E` has the compile-time type `dynamic`, then the assignment is dynamically bound (§7.2.2). In this case the compile-time type of the assignment expression is `dynamic`, and the resolution described below will take place at run-time based on the run-time type of `E`.

An operation of the form `x`*op*`=``y` is processed by applying binary operator overload resolution (§7.3.4) as if the operation was written `x`*op*`y`. Then,

-  If the return type of the selected operator is implicitly convertible to the type of `x`, the operation is evaluated as `x``=``x`*op*`y`, except that `x` is evaluated only once.
-  Otherwise, if the selected operator is a predefined operator, if the return type of the selected operator is explicitly convertible to the type of `x`, and if `y` is implicitly convertible to the type of `x` or the operator is a shift operator, then the operation is evaluated as `x``=``(T)(x`*op*`y)`, where `T` is the type of `x`, except that `x` is evaluated only once.
-  Otherwise, the compound assignment is invalid, and a binding-time error occurs.

The term "evaluated only once" means that in the evaluation of `x`*op*`y`, the results of any constituent expressions of `x` are temporarily saved and then reused when performing the assignment to `x`. For example, in the assignment `A()[B()]` +`=``C()`, where `A` is a method returning `int[]`, and `B` and `C` are methods returning `int`, the methods are invoked only once, in the order `A`, `B`, `C`.

When the left operand of a compound assignment is a property access or indexer access, the property or indexer must have both a `get` accessor and a `set` accessor. If this is not the case, a binding-time error occurs.

The second rule above permits `x`*op*`=``y` to be evaluated as `x``=``(T)(x`*op*`y)` in certain contexts. The rule exists such that the predefined operators can be used as compound operators when the left operand is of type `sbyte`, `byte`, `short`, `ushort`, or `char`. Even when both arguments are of one of those types, the predefined operators produce a result of type `int`, as described in §7.3.6.2. Thus, without a cast it would not be possible to assign the result to the left operand.

The intuitive effect of the rule for predefined operators is simply that `x`*op*`=``y` is permitted if both of `x`*op*`y` and `x``=``y` are permitted. In the example

```csharp
byte b = 0;
char ch = '\0';
int i = 0;

b += 1;                // Ok
b += 1000;            // Error, b = 1000 not permitted
b += i;                // Error, b = i not permitted
b += (byte)i;        // Ok

ch += 1;                // Error, ch = 1 not permitted
ch += (char)1;        // Ok
```

the intuitive reason for each error is that a corresponding simple assignment would also have been an error.

This also means that compound assignment operations support lifted operations. In the example

```csharp
int? i = 0;
i += 1;           // Ok
```

the lifted operator `+(int?,int?)` is used.

### Event assignment

If the left operand of a `+=` or `-=` operator is classified as an event access, then the expression is evaluated as follows:

-  The instance expression, if any, of the event access is evaluated.
-  The right operand of the `+=` or `-=` operator is evaluated, and, if required, converted to the type of the left operand through an implicit conversion (§6.1).
-  An event accessor of the event is invoked, with argument list consisting of the right operand, after evaluation and, if necessary, conversion. If the operator was `+=`, the `add` accessor is invoked; if the operator was `-=`, the `remove` accessor is invoked.

An event assignment expression does not yield a value. Thus, an event assignment expression is valid only in the context of a *statement-expression* (§8.6).

## Expression

An *expression* is either a *non-assignment**-expression* or an *assignment*.

<pre>expression: 
non-assignment-expression
assignment</pre>

<pre>non-assignment-expression:
conditional-expression
lambda-expression
query-expression</pre>

## Constant expressions

A *constant-expression* is an expression that can be fully evaluated at compile-time.

<pre>constant-expression:
expression</pre>

A constant expression must be the `null` literal or a value with one of  the following types: `sbyte`, `byte`, `short`, `ushort`, `int`, `uint`, `long`, `ulong`, `char`, `float`, `double`, `decimal`, `bool`, `object`, `string`, or any enumeration type. Only the following constructs are permitted in constant expressions:

-  Literals (including the `null` literal).
-  References to `const` members of class and struct types.
-  References to members of enumeration types.
-  References to `const` parameters or local variables
-  Parenthesized sub-expressions, which are themselves constant expressions.
-  Cast expressions, provided the target type is one of the types listed above.
-  `checked` and `unchecked`**expressions
-  Default value expressions
-  The predefined `+`, `–`, `!`, and `~` unary operators.
-  The predefined `+`, `–`, `*`, `/`, `%`, `<<`, `>>`, `&amp;`, `|`, `^`, `&amp;&amp;`, `||`, `==`, `!=`, `<`, `>`, `<=`, and `>=` binary operators, provided each operand is of a type listed above.
-  The `?:` conditional operator.

The following conversions are permitted in constant expressions:

-  Identity conversions
-  Numeric conversions
-  Enumeration conversions
-  Constant expression conversions
-  Implicit and explicit reference conversions, provided that the source of the conversions is a constant expression that evaluates to the null value.

Other conversions including boxing, unboxing and implicit reference conversions of non-null values are not permitted in constant expressions. For example:

```csharp
class C {
    const object i = 5;  // error: boxing conversion not permitted
    const object str = "hello"; // error: implicit reference conversion
}
```

the initialization of iis an error because a boxing conversion is required. The initialization of str is an error because an implicit reference conversion from a non-null value is required.

Whenever an expression fulfills the requirements listed above, the expression is evaluated at compile-time. This is true even if the expression is a sub-expression of a larger expression that contains non-constant constructs.

The compile-time evaluation of constant expressions uses the same rules as run-time evaluation of non-constant expressions, except that where run-time evaluation would have thrown an exception, compile-time evaluation causes a compile-time error to occur.

Unless a constant expression is explicitly placed in an `unchecked` context, overflows that occur in integral-type arithmetic operations and conversions during the compile-time evaluation of the expression always cause compile-time errors (§7.19).

Constant expressions occur in the contexts listed below. In these contexts, a compile-time error occurs if an expression cannot be fully evaluated at compile-time.

-  Constant declarations (§10.4).
-  Enumeration member declarations (§14.3).
-  Default arguments of formal parameter lists (§10.6.1)
-  `case` labels of a `switch` statement (§8.7.2).
-  `goto``case` statements (§8.9.3).
-  Dimension lengths in an array creation expression (§7.6.10.4) that includes an initializer.
-  Attributes (§17).

An implicit constant expression conversion (§6.1.9) permits a constant expression of type `int` to be converted to `sbyte`, `byte`, `short`, `ushort`, `uint`, or `ulong`, provided the value of the constant expression is within the range of the destination type.

## Boolean expressions

A *boolean-expression* is an expression that yields a result of type `bool`; either directly or through application of `operator true` in certain contexts as specified in the following.

<pre>boolean-expression:
expression</pre>

The controlling conditional expression of an *if-statement* (§8.7.1), *while-statement* (§8.8.1), *do-statement* (§8.8.2), or *for-statement* (§8.8.3) is a *boolean-expression*. The controlling conditional expression of the `?:` operator (§7.14) follows the same rules as a *boolean-expression*, but for reasons of operator precedence is classified as a *conditional-or-expression*.

A *boolean-expression* `E` is required to be able to produce a value of type `bool`, as follows:

-  If `E` is implicitly convertible to `bool` then at runtime that implicit conversion is applied.
-  Otherwise, unary operator overload resolution (§7.3.3) is used to find a unique best implementation of operator `true` on `E`, and that implementation is applied at runtime.
-  If no such operator is found, a binding-time error occurs.

The `DBBool` struct type in §11.4.2 provides an example of a type that implements `operator true` and `operator false`.