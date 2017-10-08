
# Rust Supplementary FAQ

* [Strings](#strings)

## Introduction
The supplementary Rust FAQ is intended to be extensive, answer questions that are often encountered, and being simple to update.

[**See also the official Rust FAQ**](https://www.rust-lang.org/en-US/faq.html) which answers many basic questions about Rust.

## Strings

### How are string slices sliced?

Using the index syntax with a range: `&foo[i..j]` if foo is a string or string slice

### What indices are used for slicing strings

Byte offsets from the start of the string.

The same offsets that are returned by string methods `find`, `char_indices`, `match_indicies` and many others.

Always base string slicing indices on the results one of these searching methods!

### Can I slice a string at 0 or at s.len()

Yes, these indices are always valid for slicing.
