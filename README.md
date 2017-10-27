
# Rust Supplementary FAQ

* [Strings](#strings)
* [Patterns](#patterns)
* [Iterators](#iterators)
* [Lifetimes](#lifetimes)
* [Type System Theory](#type-system-theory)

## Introduction
The supplementary Rust FAQ is intended to be extensive, answer questions that are often encountered, and being simple to update.

[**See also the official Rust FAQ**](https://www.rust-lang.org/en-US/faq.html) which answers many basic questions about Rust.

## Strings

### How are string slices sliced?

Using the index syntax with a range: `&foo[i..j]` if foo is a string or string slice

### What indices are used for slicing strings?

Byte offsets from the start of the string.

The same offsets that are returned by string methods `find`, `char_indices`, `match_indicies` and many others.

Always base string slicing indices on the results one of these searching methods!

### Can I slice a string at 0 or at s.len()?

Yes, these indices are always valid for slicing.

## Patterns

### The `_` pattern, no-op pattern

Note that for the _ pattern means no-op as in *“don't hold this”*. It doesn't mean *”drop this*”. But the former sometimes leads to the latter.

```rust
let s = String::new();
let _ = s; // “don't hold this”
let t = s; // “t, please hold this” => the t variable now owns the string s.
```

```rust
let opt = Some(String::new());
if Some(_) = opt {
    // Yes it's a Some(String), but we didn't consume the string -- we said "don't hold it".
    println!("The string: {:?}", opt.as_ref().unwrap());
}
```

In this example, not holding the value results in it dropping:

```rust
let _ = f(); // Don't hold the return value of f.
             // Nobody's holding it -- so it drops.
```

`let _ = ` is a common way to disable the “value must be used” warning.

## Iterators

### How does a DoubleEndedIterator work?

A double ended iterator has two ends, and you can alternate taking elements from either side. Taking from one side does not alter the other side, unless you reach the point where the ends meet.

**Note:** Double ended does not mean “bi-directional”! We can only “take” from the front and from the back.

Sketch about the concept of a DEI:

```rust
[a b c d ] start
[. b c d ] .next() -> a
[. b c . ] .next_back() -> d
[. b . . ] .next_back() -> c
[. . . . ] .next() -> b
[. . . . ] .next() or .next_back() -> None
```


## Lifetimes

### Lifetime bound on a type? What is this? `T: 'a`

If we have a type parameter `T` and a lifetime bound like this: `where T: 'a`, what does it mean?

The intuitive and high level explanation is: “T maybe contains references, and if it does, all of them are valid for **at least** the scope `'a`”.

(If T doesn't contain references, then we are fine anyway. Having no references, we satisfy `T: 'a` for all `'a`.)

Also note that if we have the type `&'a T`, then we always require `T: 'a`. How can we read this intuitively?  If we have a reference valid for the scope `'a`, then the type `T` and anything it contains, must be valid to use for at least the scope `'a` too.

### Lifetime bound on a type? What's `U: 'static`?

This is just a special case of the previous question, but it often shows up.

The intuitive explanation is that `U: 'static` means that “U does not contain references”.

*Almost.* It’s “U does not contain references, except `'static` ones.” More formally, we can say that we have an indefinite lease on values of type `U` and there is no point in the program where the value goes invalid.

**Note** it does not mean that values of `U` are in a static variable or must live forever! It means that we **are allowed to** keep them around forever. But we don't have to. There are no strings attached and no best-used-before-date, because `'static` is the longest lifetime there is in a Rust program.


## Type System Theory

### What is Subtyping?

The definition of [subtyping](https://en.wikipedia.org/wiki/Subtyping), from Wikipedia, is that if S is a subtype of T, *any term of type S can be safely used in a context where a term of type T is expected.*

In Rust, subtyping only ever applies to the lifetime part of types. For example, we can use `&String` in many places where `&str` is expected, but that does **not** mean that `&String` is a subtype of `&str`.

The subtyping relation we do have is that `&'a X` is a subtype of `&'b X` if `'a` is a longer or equal lifetime than `'b`, which is usally written `'a: 'b` (“'a outlives 'b”).

We can see that clearly, where a `&'b X` is expected, we can safely use any `&X` with a lifetime that *outlives* `'b`.

### Variance

Please see [Subtyping](#what-is-subtyping) before learning about variance.
