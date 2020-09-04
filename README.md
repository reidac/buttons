## Some Arithmetic for the Buttons Computer

In the fall of 2020, I did some assembly-style hacking
for a web toy hacked up by [Zed Shaw](https://twitter.com/lzsthw),
which I ran across on Twitter.

It's a model of a simple stack computer, with a few
registers and a minimal feature set.  It's called
"[buttons.computer](https://buttons.computer)", and it's about
boundary-crossing and the difference between "button-pushing"
and "real programming".

I wrote some programs for the Buttons computer, and
tweeted them, but they were hard-to-read screenshots.

This repo may eventually contain shareable code, but for now,
it's got annotated "assembly" listings of the programs
I hacked up.

### Fibonacci Squence

The sequence is given by a simple recurrence relation, the nth
term in the series, s_n, is given by s_n=s_{n-1}+s_{n-2}.

The first two nonzero entries in the series are 1 and 1, and the 
series continues, 2, 3, 5, 8, 13, etc.

The annotated buttons program to do this, and write the
sequence out to the memory array, is here:

    00: PUSH 1    ; Seed the stack with the first two entries.
    01: PUSH 1    ; They're s[n-1] (top of stack) and s[n-2]
    02: POKE 1
    03: POKE 1    ; Also seed the memory array.
    04: STOR AX   ; Cache s[n-1], we'll need it later.
    05: ADD       ; Generate s[n] by adding the top two stack entries.
    06: POKE 1    ; Write it out.
    09: RSTOR AX  ; Push s[n] on top of s[n-1] on the stack.
    10; SWAP      ; Flip the top two stack entries.
    11: JUMP 4    ; Loop, with s[n] and s[n-1] now on the stack.

Note that the behavior of the memory indexing is such
that it wraps around, so if this code did not run into
the click limit at 256 steps, it would cyclically
overwrite memory as it went.

### Factorial 

This is a simpler algorithm, and probably should have been
done first.  

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

The annotated code is below.  Note that, as with the Fibonacci
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
    09: RSTOR AX        ; 2x -- first for remainer.
    11: PUSH 65536      ; Output is state/2^16.
    12: MOD             ; Consumes 1 of the v's. 
    13: SUB             ; Stack now has v divisible by 65536.
    14: PUSH 65536      ; Push it back on
    15: DIV             ; Complete the integer division.
    16: POKE 1          ; Write output and increment IX.
    17: POP             ; Clean up the stack.
    18: JUMP 1          ; Go again.

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
 

### Square Roots

So we "cheated" in the foregoing example, but we don't have to cheat,
we have a computer, that can compute square roots.

There's a [Babylonian](https://en.wikipedia.org/wiki/Methods_of_computing_square_roots#Babylonian_method)
method, another infinite-series algorithm, that only uses simple
arithmetic, and so is well-suited to our machine's capabilities.

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
    15: JUMP 6    ; Iterate.

I get accuracy of one part in 10^11 when this algo stops after 256 clicks.
