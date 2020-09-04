
### Signum

One of the operations missing from the 
[buttons.computer](https://buttons.computer) is greater-than or 
less-than comparisons.

They might get added, it's a work in progress, but in the meantime,
here's a code snippet which figures out whether the thing on 
the stack is postive or negative, and replaces it with 
+1 or -1, respectively.

It's a bit involved in part because there's also no 
absolute-value operator, but it can be done.

The trick is, if you add one to a positive number and then
take the modulus of the result with the original number
(that is, compute (x+1) mod x), you get 1 if and only if
the original number is positive.

    00: PUSH -42  ; The number we're testing, call it x.
    01: STOR AX   ; We'll need it again.
    02: PUSH 1    ;
    03: ADD       ; Comptue x+1..
    04: RSTOR AX  ; 
    05: MOD       ; (x+1) mod x
    06: PUSH 1    ;
    07: SUB       ; Subtract one.  If this is zero, then x was positive.
    08: JNZ 12    ;
    09: POP       ; Clean up the stack from the test
    10: PUSH 1    ; Signum(x)=1 for positive x.
    11: HALT      ; 
    12: POP       ; Negative branch stack clean-up
    13: PUSH -1   ; Negative branch result.
    14: HALT

