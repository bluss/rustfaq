
# Rust Supplementary FAQ

* [Strings](#strings)

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
