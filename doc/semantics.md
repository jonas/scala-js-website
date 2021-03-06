---
layout: page
title: Semantics of Scala.js
tagline: and how they differ from Scala
---
{% include JB/setup %}

Because the target platform of Scala.js is quite different from that of Scala,
a few language semantics differences exist.

## Numbers and characters

Except `Long`, all primitive number types of Scala.js, as well as the `Char`
type, are mapped to JavaScript `Number`'s, i.e., `Double`'s, and no overflow
detection is performed.

This means that operations on numbers that would overflow their range do not
wrap, but instead take on bigger values.

Integer division does follow the expected semantics, though, i.e., it always
return an integer.

Binary operations (`&`, `|`, etc.) are always performed on signed 32-bit
integers, just as in JavaScript.

`Long`s are 64-bits and follow the same semantics as on the JVM.

Note that float literals are still truncated to their (binary)
precision. However, output does not truncate to that precision. This
can lead to the following behavior (this works as expected when using
doubles):

    println(13.345f)
    // Scala:    13.345
    // Scala.js: 13.345000267028809

## JavaScript interoperability

The JavaScript interoperability feature is, in itself, a big semantic
difference. However, its details are discussed in a
[dedicated page](./js-interoperability.html).

## Reflection

Java reflection and, a fortiori, Scala reflection, are not supported. There is
limited support for `java.lang.Class`, e.g., `obj.getClass.getName` will work
for any Scala.js object (not for objects that come from JavaScript interop).

## Exceptions

In general, Scala.js supports exceptions, including catching them based on their
type. However, exceptions that are typically triggered by the JVM have flaky
semantics. E.g., `ArrayIndexOutOfBoundsException` are not checked; and
`NullPointerException` are reported as JavaScript `TypeError` instead.

## Regular expressions

[JavaScript regular expressions](http://developer.mozilla.org/en/docs/Core_JavaScript_1.5_Guide:Regular_Expressions)
are slightly different from
[Java regular expressions](http://docs.oracle.com/javase/6/docs/api/java/util/regex/Pattern.html).
The support for regular expressions in Scala.js is implemented on top of
JavaScript regexes.

This sometimes has an impact on functions in the Scala library that
use regular expressions themselves. A list of known functions that are
affected is given here:

- `StringLike.split(x: Array[Char])` (see issue #105)

## Symbols

`scala.Symbol` is unsupported since its implementation requires weak references
in order to not leak memory.
