---
title: Toml
date: 2026-04-07
  - 编程语言
  - TOML
type: note
status: complete
---

## Toml

[https://github.com/toml-lang/toml](https://github.com/toml-lang/toml)

- [ ]  2020年4月15日 @thooh Li

```c
TOML is case sensitive.
A TOML file must be a valid UTF-8 encoded Unicode document.
Whitespace means tab (0x09) or space (0x20).
Newline means LF (0x0A) or CRLF (0x0D 0x0A).

The primary building block of a TOML document is the key/value pair.

Keys are on the left of the equals sign and values are on the right. Whitespace is ignored around key names and values. The key, equals sign, and value must be on the same line (though some values can be broken over multiple lines).

There must be a newline after a key/value pair.

A key may be either bare, quoted or dotted.

`.`
Dotted keys are a sequence of bare or quoted keys joined with a dot. This allows for grouping similar properties together:

Whitespace around dot-separated parts is ignored, however, best practice is to not use any extraneous whitespace.

Defining a key multiple times is invalid.

For writing long strings without introducing extraneous whitespace, use a "line ending backslash". When the last non-whitespace character on a line is a \, it will be trimmed along with all whitespace (including newlines) up to the next non-whitespace character or closing delimiter. All of the escape sequences that are valid for basic strings are also valid for multi-line basic strings.

Array
Arrays are square brackets with values inside. Whitespace is ignored. Elements are separated by commas. Arrays can contain values of the same data types as allowed in key/value pairs. Values of different types may be mixed.

Table
Tables (also known as hash tables or dictionaries) are collections of key/value pairs. They appear in square brackets on a line by themselves. You can tell them apart from arrays because arrays are only ever values.

Inline Table
Inline tables provide a more compact syntax for expressing tables. They are especially useful for grouped data that can otherwise quickly become verbose. Inline tables are enclosed in curly braces { and }. Within the braces, zero or more comma separated key/value pairs may appear. Key/value pairs take the same form as key/value pairs in standard tables. All value types are allowed, including inline tables.

Array of Tables
The last type that has not yet been expressed is an array of tables. These can be expressed by using a table name in double brackets. Under that, and until the next table or EOF are the key/values of that table. Each table with the same double bracketed name will be an element in the array of tables. The tables are inserted in the order encountered. A double bracketed table without any key/value pairs will be considered an empty table.
```