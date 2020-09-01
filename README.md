## Some Arithmetic for the Buttons Computer

In the fall of 2020, I did some assembly-style hacking
for a web toy hacked up by [Zed Shaw](https://twitter.com/lzsthw)
on Twitter.

It's a model of a simple stack computer, with a few
registers and a minimal feature set.  It's called
"[buttons.computer](buttons.computer)", and it's about
boundary-crossing and the difference between "button-pushing"
and "real programming".

I wrote some programs for the Buttons computer, and
tweeted them, but they were hard-to-read screenshots.

This repo may eventually contain shareable code, but for now,
it's got annotated "assembly" listings of the programs
I hacked up.

### Fibonacci Squence

The sequence is given by a simple recurrence relation, the $n$th
term in the series, $s_n$, is given by $s_n=s_{n-1}+s_{n-2}$.

The first two nonzero entries in the series are 1 and 1, and the 
series continues, 2, 3, 5, 8, 13, etc.

The annotated buttons program to do this is:

    0: PUSH 1    ; Seed the stack with the first two entries.
    1: PUSH 1    ; They're s[n-1] (top of stack) and s[n-2]
    2: STOR AX   ; Cache s[n-1], we'll need it later.
    3: ADD       ; Generate s[n] by adding the top two stack entries.
    4: STOR BX   ; Store s[n] 
    5: POP       ; Clear the sum from the stack.
    6: RSTOR AX  ; Push s[n-1] and s[n] on to the stack.
    7: RSTOR BX  ; ... in the right order.
    8: JUMP 2    ; Loop, with s[n] and s[n-1] now on the stack.

The program never stops, unless there's a step limit or
the infrastructure responds badly to an overflow -- the 
sequence grows exponentially, so it will get to large
numbers quickly.

### Factorial 

This is a simpler algorithm, and probably should have been
done first.  

The factorial of a number $n$, denoted $n!$, is the product of
that number and all the numbers less than it, down to 1, and
not including zero or negative numbers.

The annotated buttons program, with $9$ as input, is:
 
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


### Collatz Conjecture

The [Collatz conjecture](https://en.wikipedia.org/wiki/Collatz_conjecture)
involves the behavior of integer sequences according to a simple rule. 

Start with some positive integer $N$.  If $N$ is even, divide it by 
two, and if it's odd, multiply it by $3$ and add $1$. Then repeat, 
using the result as the new input.  If at any point you get 
$1$ as a result, then stop.

The conjecture is that all positive integers eventually reach $1$
by this rule.  It's not known if this is true, and some 
sequences can get very long.

A buttons program to implement this rule is:

    00: PUSH 3   ; Push the input on to the stack.
    01: STOR AX  ; Start of the loop -- cache the current value.
    02: RSTOR AX ; Push back for even-ness testing.
    03: PUSH 2   ; 
    04: MOD      ; Take the mod-2 of the value.
    05: JZ 12    ; If mod-2 is zero, input was even, go to 12.
    06: POP      ; We didn't branch, so input was odd.  Clean up the stack.
    07: PUSH 3   ; 
    08: MUL      ; Multiply by 3.
    09: PUSH 1   ; 
    10: ADD      ; Add 1, stack now as 3N+1 on it.
    11: JUMP 15  ; Jump over the even-case code.
    12: POP      ; We branched, so input was even.  Clean up the stack.
    13: PUSH 2   ; 
    14: DIV      ; Divide by two.  Stack now has N/2 on it.
    15: STOR AX  ; Code paths re-join here. Cache the current value.
    16: PUSH 1   ; 
    17: SUB      ; If N-1=0, then N was 1, stop.
    18: JNZ 20   ; If it's not zero, jump over the halt instruction.
    19: HALT     ; We didn't branch, so N was 1, halt.
    20: POP      ; Clean up the stack from the N=1 test.
    21: RSTOR AX ; Put the current N on to the stack.
    22: JUMP 1   ; Re-run everything with the new value.

