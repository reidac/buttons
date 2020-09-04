### Faster series for Pi

A faster approximation for pi is given by the summation of 
terms of the form (-1)^n/((2n+1)(3^n)), summed from n to 
as high as you want, with the overall sum then multiplied
by the square root of 12.  This was apparently derived
from Leibniz series above in the [middle ages](https://en.wikipedia.org/wiki/Approximations_of_%CF%80#Middle_Ages)

This code is sufficiently complicated that we don't do the loop
unrolling, we test for even-ness of N instead.

Note the presence of the magic 3.4641016514 in the code -- this
is the overall factor of the square root of 12 that this sum
requires.  It's legitimate to wonder, if we can have magic numbers
in our code, why not just hard-code pi? 

The answer is that this is a coding exercise, but that's
a good question to be asking in general.

Also, being non-integer, this is arguably not in the spirit
of push-button computing that is the actual point of the
buttons computer.

    00: PUSH 1             ; Initial value for 3^N.
    01: STOR BX            ;
    02: POP                ; Clean up the stack.
    03: PUSH 0             ; Partial sum starts from zero.
    04: STOR AX            ; ...which is also the initial N. 
    05: RSTOR AX           ; Even-ness test starts.
    06: PUSH 2             ;
    07: MOD                ; N mod 2 on the stack now.
    08: JZ 12              ; 
    09: POP                ; Clean up the stack from the even-ness test
    10: PUSH -3.4641016514 ; N is odd, this term is negative.
    11: JUMP 14            ; Jump over the even case.
    12: POP                ; Even-case stack clean-up.
    13: PUSH 3.4641016514  ; N is even, this term is positive.
    14: PUSH 1             ; Code paths rejoin here. Do the denominator.
    15: PUSH 2 
    16: RSTOR AX           ; Arguments for 2*AX+1 on the stack.
    17: MUL
    18: ADD                ; 2*AX+1 computed.
    19: RSTOR BX           ; 3^N on the stack.
    20: MUL                ; Denominator complete.
    21: DIV                ; First sub-term complete.
    22: ADD                ; Contribute to partial sum.
    23: RSTOR AX           ; Retrieve N to increment.
    24: PUSH 1           
    25: ADD 
    26: STOR AX            ; Increment N.
    27: POP                ; Clean up the stack.
    28: RSTOR BX           ; Next power of 3.
    29: PUSH 3
    30: MUL
    31: STOR BX            ; 3^N now in BX.
    32: POP                ; Clean up increment junk.
    33: JUMP 5             ; Restart the main loop.
