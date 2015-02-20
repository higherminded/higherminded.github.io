---
layout: post
title: "A Kinder Rust"
date: 2015-02-18T19:03:05-08:00
---

This post is an attempt to layout some thoughts I have been sitting on
for a while around adding a kind system to Rust. Most specifically the
conversation around arithematic in the [type system](http://internals.rust-lang.org/t/pre-rfc-genericity-over-static-values/1538/26)
and the related [RFC](https://github.com/rust-lang/rfcs/pull/884).

One of the key features of Rust is the ability of the type system to make
it safe to write low level, high performance code. Type systems are
about being able to specify invariants and have them checked for you
by a machine.

Rust has a powerful and unique type system, and is most importantly the
first production ready language with types that express information
about the ownership and lifetime of memory. Additions like where
clauses, and associated types have made the typing language even more
expressive.

I believe few key extensions to the language that will make the typing
language even more powerful, becoming a tool
to help both applications and systems programs specify more invariants
statically.

My main intention is to revive and expand upon a proposal that I had
for higher kinded types last year. I lost some time and concentration due to
health issues, but it seems that on the eve of the 1.0 release it is
important to write down my thoughts and make it clear there are some
powerful language extensions that we should consider.

It seems clear that kinds are just the beginning and

I hope I can motivate why *you* should want Rust's type system
to be more expressive (even if you don't know think so yet).

Let's start from the basics.

Just as types act as a means of classifying values, kinds give us a
means to classify types.

Wait?! you may be asking what does that mean.

Let's stop for a moment and go back to basics. If we begin with a
completely untyped language we can write any syntactically (i.e
parse-able) program. This of course causes many problems because I can
do nonsensical things like invoke an integer `let f = 1; f(1)`.

By definition type systems restrict the space of possible programs, for
good reason. So if we move to untyped to simply typed we gain the
ability to give each expression a precise type.

Imagine a variant of Rust with just simple types (i.e no generics). This
is very limiting because I can no longer write any generic code. For
example if I wanted to define a List in a language without generics I
would need to rewrite it for every item type.

If you pay attention you notice at each level we recover expressiveness,
without sacrificing check-ability.

Adding generics allows us to add polymorphism to the language but by
exposing kinds we enable what is know as "type operators" or
"type constructors" in the theory.

This now allows us to write code that was previously impossible for
example a type parameterized by smart pointer.

Although very useful kinds have mostly remained an esoteric concept.
There are only a few languages that support them the most popular of
which are Scala, and Haskell.

As I previously noted we have seen that each extension increases our
For example we can now parametrize data types by their allocation
strategy:

TODO: this syntax probably needs to change the in the face of defaults

``` rust
enum List<A, Ptr<_>> {
  Cons(A, Ptr<List<A>>),
  Nil
}

impl<Ptr<_>> List<A, Ptr> {
   // show how sharing changes based on the type Ptr is instantied to
}
```

Now we can write code *once* that is generic over the internal pointer
and instead of needing to rewrite it for each strategy.

So far we have only added the ability to add declare kinded type
variables, and instantiate them to a properly kinded type.

This is pretty much where my initial proposal ends.

We can go much further then this by exposing a more powerful kind
system.

We can give the current typing language a grammar and our grammar admits
the declaration of new types via `struct`, `enum` and `type`.

In the kind system we just described we assumed a fixed world where the
kind grammar:

```
k ::= Type
  | Lifetime
  | Fn(klist) -> k

klist ::= e
       | k "," klist
```

A technique known as *phantom types* is already used by Rust programmers
as a way to enforce [invariants](http://rustbyexample.com/generics/phantom.html).

I hope this has motivated why features such as:
  - kinds
  - data kinds (or type promotion)
  - generalized algebraic data types
  - sealed traits

may be interesting general extensions to Rust that subsume the need to implement
one off solutions in the compiler.

Darin Morrison and I are working on a couple of RFCs specifying these
features that we hope to propose soon, so stay tuned.

If you are interested in collaborating feel free to get in touch with
us.
