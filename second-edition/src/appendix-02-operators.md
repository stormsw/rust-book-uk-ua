## Оператори

### Унарні оператори

Rust визначає наступні унарні оператори. Вони всі записані як префіксні оператори
перед виразом до якого вони застосовуються.

* `-`
  : Знакова інверсія. Знакові цілі типи та типи з плаваючою комою підтримують опертор '-'.
    Використання інверсії знака для беззнакових типів є помилкою: наприклад компілятор відхилить `-1u32`.
* `*`
  : Розіменування. Коли застосовано до вказівника, він позначає данні за посиланням.
    Для вказівника на несталі локації, результуюче значення можна змінювати також.
    For pointers to mutable locations, the resulting value can be assigned to.
    On non-pointer types, it calls the `deref` method of the `std::ops::Deref`
    trait, or the `deref_mut` method of the `std::ops::DerefMut` trait (if
    implemented by the type and required for an outer expression that will or
    could mutate the dereference), and produces the result of dereferencing the
    `&` or `&mut` borrowed pointer returned from the overload method.
* `!`
  : Логічне заперечення. Для булевого типу він переключає `true` та `false`.
    Для цілочисельних типів він інвертує побітово у форму другого комплементу значення. 
* `&` та `&mut`
  : Позичання. Коли застосовано до значення то ці оператори дають посилання на це значення.
    Значення також встановлюється в позичений стан на період посилання. 
    Для спільних позичань (`&`), це призводить що значення не можна змінити, 
    але його можна читати та позичати. Для несталих позичань (`&mut`), до значення не можливо 
    звернутись жодним способом аж доки термін позичання не сплине.

### Binary operator expressions

Binary operators expressions are given in order of operator precedence.

#### Arithmetic operators

Binary arithmetic expressions are syntactic sugar for calls to built-in traits,
defined in the `std::ops` module of the `std` library. This means arithmetic
operators can be overridden for user-defined types. The default meaning of the
operators on standard types is given here.

* `+`
  : Addition and array/string concatenation.
    Calls the `add` method on the `std::ops::Add` trait.
* `-`
  : Subtraction.
    Calls the `sub` method on the `std::ops::Sub` trait.
* `*`
  : Multiplication.
    Calls the `mul` method on the `std::ops::Mul` trait.
* `/`
  : Quotient.
    Calls the `div` method on the `std::ops::Div` trait.
* `%`
  : Remainder.
    Calls the `rem` method on the `std::ops::Rem` trait.

Note that Rust does not have a built-in operator for exponential (power)
calculation; see the `pow` method on the numeric types.

#### Bitwise operators

Like the arithmetic operators, bitwise operators are syntactic sugar for calls
to methods of built-in traits. This means bitwise operators can be overridden
for user-defined types. The default meaning of the operators on standard types
is given here. Bitwise `&`, `|` and `^` applied to boolean arguments are
equivalent to logical `&&`, `||` and `!=` evaluated in non-lazy fashion.

* `&`
  : Bitwise AND.
    Calls the `bitand` method of the `std::ops::BitAnd` trait.
* `|`
  : Bitwise inclusive OR.
    Calls the `bitor` method of the `std::ops::BitOr` trait.
* `^`
  : Bitwise exclusive OR.
    Calls the `bitxor` method of the `std::ops::BitXor` trait.
* `<<`
  : Left shift.
    Calls the `shl` method of the `std::ops::Shl` trait.
* `>>`
  : Right shift (arithmetic).
    Calls the `shr` method of the `std::ops::Shr` trait.

#### Lazy boolean operators

The operators `||` and `&&` may be applied to operands of boolean type. The
`||` operator denotes logical 'or', and the `&&` operator denotes logical
'and'. They differ from `|` and `&` in that the right-hand operand is only
evaluated when the left-hand operand does not already determine the result of
the expression. That is, `||` only evaluates its right-hand operand when the
left-hand operand evaluates to `false`, and `&&` only when it evaluates to
`true`.

#### Comparison operators

Comparison operators are, like the arithmetic operators and bitwise operators,
syntactic sugar for calls to built-in traits. This means that comparison
operators can be overridden for user-defined types. The default meaning of the
operators on standard types is given here.

* `==`
  : Equal to.
    Calls the `eq` method on the `std::cmp::PartialEq` trait.
* `!=`
  : Unequal to.
    Calls the `ne` method on the `std::cmp::PartialEq` trait.
* `<`
  : Less than.
    Calls the `lt` method on the `std::cmp::PartialOrd` trait.
* `>`
  : Greater than.
    Calls the `gt` method on the `std::cmp::PartialOrd` trait.
* `<=`
  : Less than or equal.
    Calls the `le` method on the `std::cmp::PartialOrd` trait.
* `>=`
  : Greater than or equal.
    Calls the `ge` method on the `std::cmp::PartialOrd` trait.

#### Type cast expressions

A type cast expression is denoted with the binary operator `as`.

Executing an `as` expression casts the value on the left-hand side to the type
on the right-hand side.

An example of an `as` expression:

```rust
# fn sum(values: &[f64]) -> f64 { 0.0 }
# fn len(values: &[f64]) -> i32 { 0 }

fn average(values: &[f64]) -> f64 {
    let sum: f64 = sum(values);
    let size: f64 = len(values) as f64;
    sum / size
}
```

Some of the conversions which can be done through the `as` operator
can also be done implicitly at various points in the program, such as
argument passing and assignment to a `let` binding with an explicit
type. Implicit conversions are limited to "harmless" conversions that
do not lose information and which have minimal or no risk of
surprising side-effects on the dynamic execution semantics.

#### Assignment expressions

An *assignment expression* consists of a pattern followed by an equals
sign (`=`) and an expression.

Evaluating an assignment expression either copies or
moves its right-hand operand to its left-hand
operand.

```
# let mut x = 0;
# let y = 0;
x = y;
```

#### Compound assignment expressions

The `+`, `-`, `*`, `/`, `%`, `&`, `|`, `^`, `<<`, and `>>` operators may be
composed with the `=` operator. The expression `lval OP= val` is equivalent to
`lval = lval OP val`. For example, `x = x + 1` may be written as `x += 1`.

Any such expression always has the `unit` type.

#### Operator precedence

The precedence of Rust binary operators is ordered as follows, going from
strong to weak:

```text
as :
* / %
+ -
<< >>
&
^
|
== != < > <= >=
&&
||
.. ...
<-
=
```

Operators at the same precedence level are evaluated left-to-right. Unary
operators have the same precedence level and are stronger than any of the
binary operators.
