# Language Introduction

This document is a brief introduction to the Pachuco language, aimed
at those already familiar with Scheme or Common Lisp or Scheme.

In summary:

- The syntax of Pachuco is based on S-expressions, like Scheme and
  Common Lisp.

- Pachuco is a Lisp-1 (i.e. there is a single namespace for function
  and data variables), like Scheme.

- Pachuco employs lexical scoping, like Scheme.

- Pachuco is dynamically typed, like Scheme and Common Lisp.

- Pachuco allows definitions to be interleaved with expression forms,
  both at the top-level and in bodies, with no requirement that
  definitions precede expressions.  Thus it doesn't need the "let"
  forms typical of the dominant Lisp dialects.

- Pachuco has a Common-Lisp-like "unhygienic" macro system.


# Data Types

The set of data types in Pachuco is similar to the set of core data
types present in other Lisps:

- Symbols: atomic values with associated names.

- Pairs a.k.a. conses: used together with the empty list to construct
  lists.

- Functions: produced by the `lambda` special form.

- Numbers: currently only integer fixnums are supported.

- Vectors: one-dimensional arrays.

- Strings: one-dimensional arrays of 8-bit bytes.

- Special values: A small set of distinct atomic values of no
  particular type.

  All of these are distinct from each other, and from any values of
  other data types:

  - `()`: the empty list

  - `#t`, `#f`: Boolean values.

  - '#u`: nasal demons ahoy!

Pairs, vectors, and strings are all mutable.


# Syntax

The syntax is similar to a subset of that of Common Lisp:

## Number literal syntax

    123   ; a decimal number literal
    -123  ; a negative decimal number literal
    #x1f  ; hexadecimal number literal
    #x-1f ; a negative hexadecimal number literal
    #b101 ; a binary number literal

Currently, characters are also represented as numbers:

    #\x       ; the character code for the character 'x'
    #\"       ; the character code for the character '"'
    #\Space   ; a readable way to write the character code for the space
              ; character
    #\Newline ; a readable way to write the character code for the newline
             ; character

## Strings

    "Hello"   ; A string containing five characters

## Lists

    ()        ; The empty list
    (1)       ; A one element list, corresponding to (cons 1 ())
    (1 2)     ; A two element list, corresponding to (cons 1 (cons 2 ()))
    (1 . 2)   ; A "dotted list", corresponding to (cons 1 2)
    (1 . ())  ; Another way to write a one element list

## Symbols

Any token (a run of constituent characters: alphanumerics and most
punctuation characters with the exception of `();"'#`) that does not
match a special value and cannot be parsed as a number.

## Quote

Syntactic sugar for the quote special form:

    'x        ; equivalent to (quote x)

## Vectors

There is currently no literal syntax for vectors.  They are
constructed with the `vector` and `make-vector` functions.


## Special forms

### begin

    (begin <body>)

An expression that establishes a new lexical scope, and
evaluates the forms in the body in sequence.

### lambda

    (lambda (<parameters>) <body>)
    (lambda (<parameters> . <rest parameter>) <body>)
    (lambda <rest parameter> <body>)

An expression that yields a function.  The first case accepts a fixed
number of arguments; the second and third accept a variable number of
arguments.

### define

    (define <symbol>)
    (define <symbol> <expression>)
    (define (<symbol> <parameters>) <body forms>)
    (define (<symbol> <parameters> . <rest parameter>) <body forms>)

A body form that binds a variable to the given symbol in the
current scope.  Define forms may only occur in the bodies of
`begin`, `definitions` or `lambda` forms, or at the top-level.

The variable binding is visible throughout its scope, before and
after the define form, so allowing recursive and mutually
recursive functions to be defined.  The result of accessing the
value of a variable prior to the execution of the corresponding
`define` form is unspecified.

The third and forth forms are syntactic sugar which bind a
function to a variable, according to the following transformation:

    (define (<symbol> <parameters>) <body forms>)
    =>
    (define <symbol> (lambda (<parameters>) <body forms>))

    (define (<symbol> <parameters> . <rest parameter>) <body forms>)
    =>
    (define <symbol>
            (lambda (<parameters> . <rest parameter>) <body forms>))

    (define (<symbol> . <rest parameter>) <body forms>)
    =>
    (define <symbol> (lambda <rest parameters> <body forms>))

The value of a `define` form is the initial value of the binding
established (though this is rarely of interest).

### if

    (if <expression> <expression> <expression>)

An expression that evaluates the first sub-expression, and if the
result is not the value `false` evaluates the second sub-expression,
otherwise evaluates the third sub-expression.  In other words, any
value other than `false` is considered to be true in Boolean context.

### quote

    (quote <form>)
    '<form>

An expression that evaluates to the quoted value.

### set!

    (set! <symbol> <expression>)

An expression that evaluates the sub-expression and assigns the
resulting value to the variable bound to the given symbol, also
yielding the same value.


# Standard Library

The Pachuco runtime includes a miscellaneous collection of generic
facilities and functions.  These often correspond to their equivalents
in Common Lisp.  See `runtime/runtime.pco` and
`runtime/runtime-common.pco`.
