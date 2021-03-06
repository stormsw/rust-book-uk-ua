## Визначення і інстанціювання структур Struct

Структури подібні до кортежів, про які ми говорили в Розділі 3. Як і кортежі,
частини структур можуть бути різних типів. На відміну від кортежів, ми даємо
ім'я кожному елементу даних, щоб було зрозуміло, що ці значення означають. 
Завдяки цим іменам структури гнучкіші за кортежі: ми не мусимо покладатися на 
порядок даних, щоб визначати чи отримувати доступ до значень екземляра.

Для визначення структури, ми вводимо ключове слово `struct` і називаємо всю 
структуру. Ім'я структури має описувати сенс групування цих елементів даних. 
Потім, у фігурних дужках, ми визначаємо імена і типи елементів даних, які 
звуться *полями*. Наприклад, Роздрук 5-1 показує структуру, що зберігає 
інформацію про обліковий запис користувача:

```rust
struct User {
    username: String,
    email: String,
    sign_in_count: u64,
    active: bool,
}
```

<span class="caption">Роздрук 5-1: Визначення структури `User`</span>

Щоб скористатися структурою по визначенню, ми створюємо *екземляр* цієї 
структури, визначаючи конкретні значення для кожного поля. Ми створюємо 
екземляр, вказуючи ім'я структури, а потім в фігурних дужках додаємо пари `ключ:
значення`, де ключі - це імена полів, а значення - дані, які ми хочемо зберігати
в цих полях. Поля не обов'язково вказувати у тому ж порядку, в якому вони були 
проголошені в структурі. Іншими словами, визначення структури - це загальний 
шаблон типу, а екземпляри заповнюють цей шаблон конкретними даними, щоб створити
значення цього типу. Наприклад, ми можемо проголосити конкретного користувача,
як показано в Роздруку 5-2:

```rust
# struct User {
#     username: String,
#     email: String,
#     sign_in_count: u64,
#     active: bool,
# }
#
let user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};
```

<span class="caption">Роздру 5-2: Створення екземпляру структури `User`</span>

To get a specific value from a struct, we can use dot notation. If we wanted
just this user’s email address, we can use `user1.email` wherever we want to
use this value. If the instance is mutable, we can change a value by using the
dot notation and assigning into a particular field. Listing 5-3 shows how to
change the value in the `email` field of a mutable `User` instance:

```rust
# struct User {
#     username: String,
#     email: String,
#     sign_in_count: u64,
#     active: bool,
# }
#
let mut user1 = User {
    email: String::from("someone@example.com"),
    username: String::from("someusername123"),
    active: true,
    sign_in_count: 1,
};

user1.email = String::from("anotheremail@example.com");
```

<span class="caption">Listing 5-3: Changing the value in the `email` field of a
`User` instance</span>

Note that the entire instance must be mutable; Rust doesn’t allow us to mark
only certain fields as mutable. Also note that as with any expression, we can
construct a new instance of the struct as the last expression in the function
body to implicitly return that new instance.

Listing 5-4 shows a `build_user` function that returns a `User` instance with
the given email and username. The `active` field gets the value of `true`, and
the `sign_in_count` gets a value of `1`.

```rust
# struct User {
#     username: String,
#     email: String,
#     sign_in_count: u64,
#     active: bool,
# }
#
fn build_user(email: String, username: String) -> User {
    User {
        email: email,
        username: username,
        active: true,
        sign_in_count: 1,
    }
}
```

<span class="caption">Listing 5-4: A `build_user` function that takes an email
and username and returns a `User` instance</span>

It makes sense to name the function arguments with the same name as the struct
fields, but having to repeat the `email` and `username` field names and
variables is a bit tedious. If the struct had more fields, repeating each name
would get even more annoying. Luckily, there's a convenient shorthand!

### Using the Field Init Shorthand when Variables and Fields Have the Same Name

Because the parameter names and the struct field names are exactly the same in
Listing 5-4, we can use the *field init shorthand* syntax to rewrite
`build_user` so that it behaves exactly the same but doesn’t have the
repetition of `email` and `username` in the way shown in Listing 5-5.

```rust
# struct User {
#     username: String,
#     email: String,
#     sign_in_count: u64,
#     active: bool,
# }
#
fn build_user(email: String, username: String) -> User {
    User {
        email,
        username,
        active: true,
        sign_in_count: 1,
    }
}
```

<span class="caption">Listing 5-5: A `build_user` function that uses field init
shorthand since the `email` and `username` parameters have the same name as
struct fields</span>

Here, we’re creating a new instance of the `User` struct, which has a field
named `email`. We want to set the `email` field’s value to the value in the
`email` parameter of the `build_user` function. Because the `email` field and
the `email` parameter have the same name, we only need to write `email` rather
than `email: email`.

### Creating Instances From Other Instances With Struct Update Syntax

It’s often useful to create a new instance of a struct that uses most of an old
instance’s values, but changes some. We do this using *struct update syntax*.

First, Listing 5-6 shows how we create a new `User` instance in `user2` without
the update syntax. We set new values for `email` and `username`, but otherwise
use the same values from `user1` that we created in Listing 5-2:

```rust
# struct User {
#     username: String,
#     email: String,
#     sign_in_count: u64,
#     active: bool,
# }
#
# let user1 = User {
#     email: String::from("someone@example.com"),
#     username: String::from("someusername123"),
#     active: true,
#     sign_in_count: 1,
# };
#
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    active: user1.active,
    sign_in_count: user1.sign_in_count,
};
```

<span class="caption">Listing 5-6: Creating a new `User` instance using some of
the values from `user1`</span>

Using struct update syntax, we can achieve the same effect with less code,
shown in Listing 5-7. The syntax `..` specifies that the remaining fields not
explicitly set should have the same value as the fields in the given instance.

```rust
# struct User {
#     username: String,
#     email: String,
#     sign_in_count: u64,
#     active: bool,
# }
#
# let user1 = User {
#     email: String::from("someone@example.com"),
#     username: String::from("someusername123"),
#     active: true,
#     sign_in_count: 1,
# };
#
let user2 = User {
    email: String::from("another@example.com"),
    username: String::from("anotherusername567"),
    ..user1
};
```

<span class="caption">Listing 5-7: Using struct update syntax to set a new
`email` and `username` values for a `User` instance but use the rest of the
values from the fields of the instance in the `user1` variable</span>

The code in Listing 5-7 also creates an instance in `user2` that has a
different value for `email` and `username` but has the same values for the
`active` and `sign_in_count` fields from `user1`.

### Tuple Structs without Named Fields to Create Different Types

We can also define structs that look similar to tuples, called *tuple structs*,
that have the added meaning the struct name provides, but don’t have names
associated with their fields, just the types of the fields. Tuple structs are
useful when you want to give the whole tuple a name and make the tuple be a
different type than other tuples, but naming each field as in a regular struct
would be verbose or redundant.

To define a tuple struct you start with the `struct` keyword and the struct
name followed by the types in the tuple. For example, here are definitions and
usages of two tuple structs named `Color` and `Point`:

```rust
struct Color(i32, i32, i32);
struct Point(i32, i32, i32);

let black = Color(0, 0, 0);
let origin = Point(0, 0, 0);
```

Note that the `black` and `origin` values are different types, since they’re
instances of different tuple structs. Each struct we define is its own type,
even though the fields within the struct have the same types. For example, a
function that takes a parameter of type `Color` cannot take a `Point` as an
argument, even though both types are made up of three `i32` values. Otherwise,
tuple struct instances behave like tuples, which we covered in Chapter 3: you
can destructure them into their individual pieces, you can use a `.` followed
by the index to access an individual value, and so on.

### Unit-Like Structs without Any Fields

We can also define structs that don’t have any fields! These are called
*unit-like structs* since they behave similarly to `()`, the unit type.
Unit-like structs can be useful in situations such as when you need to
implement a trait on some type, but you don’t have any data that you want to
store in the type itself. We’ll be discussing traits in Chapter 10.

> ### Ownership of Struct Data
>
> In the `User` struct definition in Listing 5-1, we used the owned `String`
> type rather than the `&str` string slice type. This is a deliberate choice
> because we want instances of this struct to own all of its data and for that
> data to be valid for as long as the entire struct is valid.
>
> It’s possible for structs to store references to data owned by something else,
> but to do so requires the use of *lifetimes*, a Rust feature that is discussed
> in Chapter 10. Lifetimes ensure that the data referenced by a struct is valid
> for as long as the struct is. Let’s say you try to store a reference in a
> struct without specifying lifetimes, like this:
>
> <span class="filename">Filename: src/main.rs</span>
>
> ```rust,ignore
> struct User {
>     username: &str,
>     email: &str,
>     sign_in_count: u64,
>     active: bool,
> }
>
> fn main() {
>     let user1 = User {
>         email: "someone@example.com",
>         username: "someusername123",
>         active: true,
>         sign_in_count: 1,
>     };
> }
> ```
>
> The compiler will complain that it needs lifetime specifiers:
>
> ```text
> error[E0106]: missing lifetime specifier
>  -->
>   |
> 2 |     username: &str,
>   |               ^ expected lifetime parameter
>
> error[E0106]: missing lifetime specifier
>  -->
>   |
> 3 |     email: &str,
>   |            ^ expected lifetime parameter
> ```
>
> We’ll discuss how to fix these errors so you can store references in structs
> in Chapter 10, but for now, we’ll fix errors like these using owned types like
> `String` instead of references like `&str`.
