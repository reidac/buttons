### Linear Congruential Pseudo-Random Number Generator

This code, taken from [here](https://rosettacode.org/wiki/Linear_congruential_generator)
and using the "Microsoft" parameters, implements a pseudo-random
number generator using the simple 
[Linear Congruential Method.](https://mathworld.wolfram.com/LinearCongruenceMethod.html)

This code was written subsequent to the addition of the `POKE`
instruction in the buttons computer, and so it uses that to
put subsequent random numbers into the memory array.

Here we run up against a feature of the buttons computer, which
is that it does floating-point operations on its values. We actually
want integer division, so the commands from 08 through 15 do this
for us -- first find the remainder of the division, and subtract
it from the original value, so that what's left is evenly divisible
by the divisor, then do the regular division to get an integer 
result.

The annotated code is below.  Note that, as with the 
[Fibonacci](fibonacci.md)
example, this code has no halting condition, but will keep
going until the host computer runs out of clicks.

    00: PUSH 17         ; Put the first "random" state on the stack. 
    01: PUSH 214013     ; Start of the loop. v*=214013
    02: MUL
    03: PUSH 253101     ; v+= 253101
    04: ADD
    05: PUSH 2147483648 ; v = v mod 2^31
    06: MOD
    07: STOR AX         ; Preserve the state value, but duplicate it
    08: RSTOR AX        ; ...on the stack to generate output.
    09: RSTOR AX        ; 2x -- first for remainder.
    11: PUSH 65536      ; Output is state/2^16.
    12: MOD             ; Consumes 1 of the v's. 
    13: SUB             ; Stack now has v - (v%65536), divisible by 65536.
    14: PUSH 65536      ; Push it back on
    15: DIV             ; Complete the integer division.
    16: POKE 1          ; Write output and increment IX.
    17: POP             ; Clean up the stack.
    18: JUMP 1          ; Go again.
