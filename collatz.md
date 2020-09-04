### Collatz Conjecture

The [Collatz conjecture](https://en.wikipedia.org/wiki/Collatz_conjecture)
involves the behavior of integer sequences according to a simple rule. 

Start with some positive integer N.  If N is even, divide it by 
two, and if it's odd, multiply it by 3 and add 1. Then repeat, 
using the result as the new input.  If at any point you get 
1 as a result, then stop.

The conjecture is that all positive integers eventually reach 1
by this rule.  It's not known if this is true, and some 
sequences can get very long.

A buttons program to implement this rule and write the 
sequence out to memory is:

    00: PUSH 3   ; Push the input on to the stack.
    01: POKE 1   ; Start of the loop -- write the sequence to memory.
    02: STOR AX  ; Cache the current value.
    03: RSTOR AX ; Push back for even-ness testing.
    04: PUSH 2   ; 
    05: MOD      ; Take the mod-2 of the value.
    06: JZ 13    ; If mod-2 is zero, input was even, go to 12.
    07: POP      ; We didn't branch, so input was odd.  Clean up the stack.
    08: PUSH 3   ; 
    09: MUL      ; Multiply by 3.
    10: PUSH 1   ; 
    11: ADD      ; Add 1, stack now as 3N+1 on it.
    12: JUMP 16  ; Jump over the even-case code.
    13: POP      ; We branched, so input was even.  Clean up the stack.
    14: PUSH 2   ; 
    15: DIV      ; Divide by two.  Stack now has N/2 on it.
    16: STOR AX  ; Code paths re-join here. Cache the current value.
    17: PUSH 1   ; 
    18: SUB      ; If N-1=0, then N was 1, stop.
    19: JNZ 21   ; If it's not zero, jump over the halt instruction.
    20: HALT     ; We didn't branch, so N was 1, halt.
    21: POP      ; Clean up the stack from the N=1 test.
    22: RSTOR AX ; Put the current N on to the stack.
    23: JUMP 1   ; Re-run everything with the new value.
