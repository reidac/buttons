### Square Roots

The [fast pi](fast_pi.md) example has an explicit square root of
12 in it, which is aesthetically distasteful, as it deviates from the
minimalist principle of being able to input the full algorithm
with mouse-clicks on the [buttons.computer](https://buttons.comptuer)
page.

We can redeem this partially by showing a square-root
algorithm for this system.

There's a [Babylonian](https://en.wikipedia.org/wiki/Methods_of_computing_square_roots#Babylonian_method)
method, another infinite-series algorithm, that only uses simple
arithmetic, and so is well-suited to our machine's capabilities.

The algorithm is, given an estimate x for the square root of some
quantity S, compute an improved estiamte by x=((S/x)+x)/2.


    00: PUSH 12   ; The thing we want the square root of.
    01: STOR AX   ; 
    02: PUSH 3    ;
    03: DIV       ; Divide by three for the (bad?) initial guess.
    04: STOR BX   ; Keep track of our iterates in BX.
    05: POP       ; Clean up the stack.
    06: RSTOR BX  ; Start of main loop. It's ((AX/BX)+BX)/2
    07: RSTOR AX  ; 
    08: RSTOR BX  ;
    09: DIV       ; AX/BX
    10: ADD       ; (AX/BX)+BX
    11: PUSH 2    ;
    12: DIV       ; Done!
    13: STOR BX   ; Save it.
    14: POP       ; Clean up the stack.
    15: POKE 1    ; Record the approximations, why not.
    16: JUMP 6    ; Iterate.

I get accuracy of one part in 10^11 when this algo stops after 256 clicks,
and the recorded sequence of values seems to show that it actually
converged after five passes through the loop, and we did 22, 
so we did about four times too much work!
