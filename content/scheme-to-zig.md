+++
title = "Scheme to Zig"
date = 2022-11-01

[taxonomies]
tags = ["zig", "scheme", "pl", "compiler", "lemon"]
+++

Some Scheme implementations use C as their compile target, yielding portability and performance.
Re-reading the [Cheney on the MTA](https://dl.acm.org/doi/10.1145/214448.214454) paper I thought,
"why not Zig?"

<!-- more -->

**Note:** There isn't anything beyond this post and
[this tweet](https://twitter.com/Borlaag/status/1583555782976876545)... yet :-) This is
a big undertaking for me, and will likely remain a background project for me for a while.
I'll try to post regular status updates as I work on this. Documenting implementation is
a useful endeavor I think.

## Why

There are lots of Scheme implementations and several mature implementations that target C.
Implementing a Scheme seems fun (why else are there so many?), and given the ratified standards
an implementor does not need to worry about language design and can for the most part just implement
things from the spec.

Beyond that, the goals of this project are to create a simple reference implementation, conform to the
specification, and to experiment with a newer compile target. Although not a primary goal, it might also
be interesting to have an extension language for Zig, similar to
[Guile](https://www.gnu.org/software/guile/) being an extension language for C and C++. I'm not sure
how this would work (or if it could work at all) given that my implementation idea is not an
interpreter/VM but a source-to-source compiler.

 I also want to learn Zig by taking on a substantial project. This is a substantial project.
 You can learn more about the merits of Zig in the [in-depth overview](https://ziglang.org/learn/overview/)
 and the [Why Zig?](https://ziglang.org/learn/why_zig_rust_d_cpp/) page.

## Design

The idea is to implement [R7RS-small](https://small.r7rs.org/) in the style described in the paper,
and to target [Zig](https://ziglang.org) instead of C. The
[overview](https://small.r7rs.org/attachment/overview.pdf) of R7RS is fairly easy to digest but the
actual [specification](https://small.r7rs.org/attachment/r7rs.pdf) is a solid 88 pages.

Below is a higher-level visual overview of the compiler.

![](/images/compiler.svg)

The basic idea is we parse the Scheme source file(s) into Abstract Syntax Trees (AST) which is
something we can now transform. The transformations can be both additive and subtractive.
One such transformation necessary is known as
[Continuation Passing Style](https://en.wikipedia.org/wiki/Continuation-passing_style) (CPS for short).
We take the resulting [lambda expressions](https://en.wikipedia.org/wiki/Lambda_calculus) and compile
them into individual Zig functions.

The next step is to combine this into a Zig file that makes sense as a full program. We'd still be
missing some things. For example, we need a garbage collector. We also want some boilerplate structure
(or a template) for us to write these Zig functions into. This is the runtime and raw template,
something like a `main.zig` with gaps in it that we fill with the Zig functions we compiled in the
previous step. Putting this together, we can now call the Zig compiler and get an executable.

As a first pass I wouldn't add any performance optimizations but there are many that can be applied
directly to the AST so this design does make the project future-proof, if someone were to decide they
wanted to improve the quality of the Zig code that is produced. As an aside, there's a framework called
[nanopass](http://andykeep.com/pubs/dissertation.pdf) where the idea is to make it very easy to make tiny
modifications to the AST, and to make lots of them through the 'middle-end' compilation process. This is
how the [chez scheme](https://github.com/cisco/ChezScheme) compiler is implemented.
