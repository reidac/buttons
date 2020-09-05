### Logarithms

There is a [fast series](https://en.wikipedia.org/wiki/Logarithm#Calculation)
for computing the natural logarithm of a positive number x.

This series uses only integer powers and basic arithmetic, so
it's well-suited to the buttons computer.

The series is given by summing terms of the form 
(1/(2n+1))*(((x-1)/(x+1))^(2n+1)).  

We'll refer to (x-1)/(x+1) as "a". 

As code, it looks like this:

    00: PUSH 5    ; Our input.
    01: STOR AX   ; First construct (x-1)/(x+1).
    02: PUSH 1    ;
    03: STOR BX   ; Handy to initialize BX here -- it will track 2n+1.
    04: SUB       ; x-1 is on the stack now.
    05: RSTOR AX  ;
    06: PUSH 1    ;
    07: ADD       ; Stack has x+1 on top, x-1 next.
    08: DIV       ;
    09: STOR AX   ; AX now has (x-1)/(x+1).  
    10: STOR CX   ; Powers in AX, 'a' iteself in CX.
    11: PUSH 2    ;
    12: MUL       ; First partial sum is 2x AX.
    13: POKE 1    ; Start of the main loop. Previous "a" power in AX,
                  ; previous (2n+1) is in BX, current sum is on stack.
    14: RSTOR AX  ;
    15: RSTOR CX  ; 
    16: RSTOR CX  ; Need to increase power of a by 2.
    17: MUL
    18: MUL
    19: STOR AX   ; Cache it for next iteration.
    20: RSTOR BX  ; 
    21: PUSH 2    ;
    22: ADD       ;
    23: STOR BX   ; Update BX for this iteration, and store it too.
    24: DIV       ; Sum term is complete.
    25: PUSH 2    ;
    26: MUL       ; Apply overall factor of two.
    27: ADD       ; Updated sum is now on the stack.
    28: JUMP 13   ; Go again.

For the example input, it seems to converge quickly, in maybe 10
iterations or so.
