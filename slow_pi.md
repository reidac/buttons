### Leibniz's Series for Pi

This algorithm feels like it makes better use of the floating-point
capabilities of the button computer.

There is an infinite sum that (slowly) approaches the value of pi,
given by (pi/4) = 1 - (1/3) + (1/5) - (1/7) ... + (-1)^n/(2n+1)

Multiplying by 4 on both sides gives us pi directly.

The buttons program I came up with provides an example of 
"loop unrolling" -- it's convenient in the buttons code to do 
the sum two terms at a time, explicitly coding the alternating 
sign.

Here is the result:

    00: PUSH 1   ; Starting index
    01: STOR AX  ; We will manipulate the index from AX.
    02: POP 
    03: PUSH 0   ; Partial sum after zero terms.
    04: PUSH 4   ; Start of loop. Construct 4/AX
    05: RSTOR AX
    06: DIV      
    07: ADD      ; Add 4/AX to the parital sum.
    08: RSTOR AX ; Pull the index back to increment it.
    09: PUSH 2 
    10: ADD
    11: STOR AX  ; Complete AX=AX+2
    12: POP      ; Clean up the stack from index operations.
    13: PUSH -4  ; Second part of the loop body, analog of step 4, but negative.
    14: RSTOR AX ;
    15: DIV      ;
    16: ADD      ; Second term added to the partial sum.
    17: RSTOR AX ; Pull the index back to increment again.
    18: PUSH 2
    19: ADD
    20: STOR AX  ; AX=AX+2 completed for the second time.
    21: POP      ; Stack clean-up.
    22: JUMP 4

Running the code, you see that it's not an especially good 
or fast-converging approximation -- after 256 ticks, the partial 
sum is 3.1786, and AX is 53, indicating 26 terms have been 
summed.
