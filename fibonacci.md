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
