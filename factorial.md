### Factorial 

This is a [simple idea](https://en.wikipedia.org/wiki/Factorial)
and algorithm, and is a good place to start.

The factorial of a number n, denoted n!, is the product of
that number and all the numbers less than it, down to 1, and
not including zero or negative numbers.

The annotated buttons program, with 9 as input, is:
 
    00: PUSH 9   ; Push the input on to the stack.
    01: STOR AX  ; Make a copy of it, for counting down.
    02: RSTOR AX ; Start of loop -- push the previous factor on to the stack.
    03: PUSH 1   ; 
    04: SUB      ; On the k-th iteration, the stack has N-k on top now.
    05: JZ 9     ; If N-k is zero, we're done, jump to post-processing.
    06: STOR AX  ; This is the next factor, store it.
    07: MUL      ; Cumulative product times next factor.
    08: JUMP 2   ; Loop -- the new cumulative product is on the stack.
    09: POP      ; The zero from testing is still on the stack, remove it.
    10: HALT     ; Full product is on the stack now.

