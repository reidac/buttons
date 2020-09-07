
### Rule 30 1-D Cellular Automoton

[Rule 30](https://en.wikipedia.org/wiki/Rule_30)
is a 1D cellular automoton, which has some interesting
mathematical features, particularly that, starting from one "black"
dot, the temporal evolution of the square corresponding to the
initial dot is non-periodic, and evolution under the rule is 
[chaotic](https://en.wikipedia.org/wiki/Rule_30#Chaos)
in a formal sense.

For the buttons computer, we can use 1s and 0s in the 
8x8 RAM array as a sort of display.  The top row will contain the
look-up table for the evolution rule, the second row the 
initial condition, and we'll evolve the rule down the rows.

    00: PUSH 1
    01: STOR IX   ; Good luck -- the look-up table has 4 contiguous
    02: POKE 1    ; 1s starting from cell #1. 
    03: POKE 1
    04: POKE 1  
    05: POKE 1 
    06: PUSH 11   ; Initial condition in cell #11.
    07: STOR IX   ;
    08: POP    
    09: POKE 0    ; Initialization complete.
    10: POP       ; Clean up the stack.
    11: PUSH 16   ; Start evolution from cell 16, start of 2nd row.
    12: STOR AX   ; Track current row in AX.
    13: PUSH 8    ; Cell counter, will count down as we progress.
    14: STOR BX   ; Track the cell-counter in BX.
    15: SUB       ; 1st cell is ROW-8.
    16: STOR CX   ; Track the actual cell in CX.
    17: POP       ; Clean up for loop.
    18: RSTOR CX  ; Start of loop over cells.
    19: PUSH 1    ; 
    20: SUB       ; Back up one to start assessing.
    21: STOR IX   ; Read three cells, accumulating powers of 2.
    22: POP       ; 
    23: PEEK 1    ;
    24: PUSH 2    ;
    25: MUL       ;
    26: PEEK 1    ;
    27: ADD       ; 
    28: PUSH 2    ; 
    29: MUL       ; 
    30: PEEK 1    ; 
    31: ADD       ; Integer value of three cells "above" us.
    32: STOR IX   ; Use as the index into the look-up table.
    33: POP       ;
    34: PEEK 0    ; Do the look-up.
    35: RSTOR CX  ; Retrieve the current cell.
    36: PUSH 8    ; Add the offset to where to write.
    37: ADD       ; 
    38: STOR IX   ;
    39: POP       ; Remove index, look-up result now on top of stack.
    40: POKE 0    ; Write the look-up table value to memory.
    41: POP       ; Clear the look-up table value.
    42: RSTOR CX  ; Retrieve the cell 
    43: PUSH 1    ;
    44: ADD       ;
    45: STOR CX   ;
    46: POP       ; Clean up stack from CX-increment.
    47: RSTOR BX  ; 
    49: PUSH 1    ;
    49: SUB       ;
    50: STOR BX   ; 
    51: JNZ 17    ; Do the next cell.
    52: POP       ; Clean up for next-row mechanics.
    53: RSTOR AX  ; Pull the row index.
    54: PUSH 8    ;
    55: ADD 
    56: JUMP 12   ; Loop. 

Note that we are cheating on the boundaries of the rows, assuming
we can index outside the nominal bounds -- as it happens, the
structure of the problem is such that there are zeros everywhere
we incorrectly read, so it's harmless in this case, but bad
practice in general.

This feels like the practical limit of the complexity one can
manage with the buttons computer.  The 256-click limit means
we only do part of the first nontrivial row, and entering
a 55-line program via the button interface is taxing.
