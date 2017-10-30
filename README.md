# Stargazer: A &pi;-calculus simulator

This is a JavaScript, [D3.js][d3] powered simulator for the [&pi;-calculus][wikipedia], a process algebra modelling concurrency and mobility.

I am releasing it for didactic purposes.

## License [![License](https://i.creativecommons.org/l/by-sa/4.0/80x15.png "License")][license]

Stargazer by [Emanuele D'Osualdo][home] is licensed under a
[Creative Commons Attribution-ShareAlike 4.0 International License](http://creativecommons.org/licenses/by-sa/4.0/).

I would appreciate if you contacted me before using the simulator in presentations/other material.
Note that the current version is experimental and distributed as is.
A new, open-sourced version will be released at some point.

## Usage
You can use the syntax of π-calculus with the ascii notation:

*   Actions `α.P`
    *   Output: `x<y1,...,yn>.P`
    *   Input: `x(y1,...,yn).P`
    *   Internal: `tau.P`
*   The inactive process: `0` or `zero` (it can be omitted after an action: `α.zero` can be abbreviated `α`)
*   Parallel: `P | Q`
*   Guarded Sum (`M`): `α1.P1 + … + αn.Pn`
*   Process call: `P[y1,...,yn]`
*   Restriction: `new y1,...,yn.P`
*   Process definition: `P[y1,...,yn] := M`

Parentheses can be used to group actions, for example: `x(y).x(z) + x<a>` is different from `x(y).(x(z) + x<a>)`.

Your program should have the form

    INIT
    DEFS

Where `INIT` is a process and `DEFS` is a sequence of definitions.

You should write _normalised_ programs: the initial term should not contain sums and only the top-level of each definition should be a sum. For example:

    new x,y.(A[x] | B[x,y])
    A[x] := x(u).new z.(A[u] | B[u,x] | C[z,u])
    B[x,y] := x<y>

is normalised, but

    new x,y.(A[x] | x<y>)
    A[x] := new z.x(u).(A[u] | u<x>| C[z,u])

is not since the initial term contains a sum `x<y>`, and so does the continuation in the definition of `A[x]`. Moreover the definition of `A[x]` contains a restriction at top level which is not allowed: if you need it there, move it outside in the call of `A[x]`, otherwise consider if you meant to use it under a prefix.

Process calls `A[x]` where `A` is not defined, will be treated as if the definition was `A[x] := 0`.

Another important restriction: the free names of the body of a definition **have to be bound by the definition's head!** This means that the definition `A[x] := x(y).(z<y> | A[y])` is not valid because `z` occurs free in the body but is not in the argument list. To fix the definition you have to include it as in `A[x,z] := x(y).(z<y> | A[y,z])`.


[wikipedia]: http://en.wikipedia.org/wiki/Pi-calculus
[license]: http://creativecommons.org/licenses/by-sa/4.0/
[home]: http://emanueledosualdo.com
[d3]: https://d3js.org/