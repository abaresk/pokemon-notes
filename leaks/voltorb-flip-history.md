# History of Voltorb Flip

- 2009-04-08: First mention in codebase [r19185, tomoya]
  - Commits some graphic files
- 2009-07-04: Code development begins [r28001, hosaka]
- 2009-07-10: Initial version of the mini-game is complete [r28115, hosaka]
- 2009-07-13: Initial implementation of memo functionality [r28143, hosaka]
- 2009-07-14: Memo functionality completed [r28146, hosaka]
- 2009-07-14: Payout display implemented [r28151, hosaka]
- 2009-07-15: Initial logic for suspenseful card flip sound effect [r28165, hosaka]

    Probability of sound effect drawn from the following table. 1's, 2's, and 3's
    are summed to get the total points.

    | No SE | Minor SE | Suspense SE | Condition |
    |-------|----------|-------------|------------------------------|
    |   100 |        0 |           0 | No bombs in row or col       |
    |   100 |        0 |           0 | Collected <25% total points  |
    |    50 |       50 |           0 | Collected <50% total points  |
    |    30 |       50 |          20 | Collected <75% total points  |
    |    25 |       35 |          40 | Collected >=75% total points |

- 2009-07-17: Suspense music conditions changed [r28201, hosaka]
  - Same as above, but no longer includes 1 cards in point tally

- 2009-07-18: First board generation and level advancement algorithms written [r28206, hosaka]
  - Board configs only track how many bombs, 2's, and 3's should appear.
    - Nothing fancy, doesn't increase monotically with payout.
  - There aren't really levels at this point.
    - There are 5 _unique_ board types (21 total) of increasing difficulty.
    - The next-level algorithm does have 8 'levels' of conditional logic.
    - The 'level' you get depends on how many cards you flipped over and whether
      you won the previous round.
      - NOTE: In this version, flipping over more cards matters more than actually
        winning.
    - There's a hard-coded probability distribution over the 21 boards depending
      on your 'level'.
    - The 5th board type is much harder than the rest. To reach it, you need to
      win and flip over at least 12 cards for 5 consecutive rounds.
  - Board generation has some bugs:
    - Doesn't check if there's already a bomb before placing a new one.
    - 3 cards don't check if there's already a 2 card there.
  - Diff: https://pastebin.com/raw/3G95R9KY

- 2009-07-20: Refactor card placement logic [r28222, hosaka]
  - QUESTION: Do they fix the bugs above?
  - The logic for where to place bombs, 2's and 3's. At this point it's just random,
    but the code is cleaner.

- 2009-07-20: Game designer tweaks level progression constants [r28237, shimoyamada]

- 2009-07-21: Modifies sound effect conditions [r28247, hosaka]
  - Plays one SE when there's 1 multiplier card left AND either 1) there's a bomb
    in the row or column, or 2) the row or column has at least 1 multiplier card.
  - Otherwise, play a suspenseful SE if at least 3 cards have already been opened
    in the row or column.
     - There's a comment that says "play SE if chance of Voltorb is 60% or higher",
       but the author never confirmed whether there's any unflipped bombs in the
       row/column.

- 2009-07-21: Fix bug in the last change [r28248, hosaka]
  - The suspenseful SE now plays if 60% or more of the unopened cards are bombs.

- 2009-07-22: Adjust level advancement algorithm [r28257, hosaka]
  - Level advancement depends on which 'level' you are currently on. Now it's
    not as easy to jump to a high 'level' just by flipping over many cards.


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 
Developers take a break for about a week and revisit on July 30.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - 


- 2009-07-30: New SVN branch created called `gs_card090729` [r28295, hosaka]

- 2009-07-31: Game designer switches from 21 to 80 board tables [r28311, shimoyamada]
  - The game designer adds the 80 board configurations that appear in the final
    version. The assignment of 2's and 3's is such that the payout increases
    monotically.
  - Each level draws from a set of 5 _unique_ boards (10 total) with equal
    likelihood.
  - Changes some next-level progression requirements:
    - Lv. 7 requires beating at least Lv. 5
    - Lv. 5 & 6 require being at least Lv. 3
    - Lv. 3 & 4 require being at least Lv. 2

- 2009-07-31: Specify limit on 2's and 3's that can appear on a line without a bomb [r28312, hosaka]
  - Board configuration now contains constraints on how easy the board can be by
    forcing some 2's and 3's to be on lines with bombs.
    - Restricts:
       1) How many 2's and 3's can appear in a single line without a bomb
       2) How many 2's and 3's on the whole board can appear in a line without a bomb

- 2009-07-31: Game designer tweaks the constraints above [r28313, shimoyamada]
  - The general rule is:
    - No more than half the total number of multipliers can appear on a single
      row without a bomb.
    - For half of a level's 10 boards, this value applies to the constraint for
      the whole board. In the other half, this value is 1 greater.
  - There appear to be some data entry bugs.
  - Now, each of the 80 board configurations is unique.

- 2009-07-31: Adjustment to next-level algorithm [r28314, hosaka]
  - The adjustment doesn't quite make sense. Lv 6 and Lv 4 will always be chosen
    instead of Lv 5 and Lv 3, respectively.

- 2009-07-31: Game designer tries to fix data entry bugs  [r28315, shimoyamada]
  - They try to fix 2 errors in Lv 4 configs; one fix is correct, but the other is
    erroneous.


- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Development on the entire game halts for 2 weeks, presumably as
the Japenese ROM "goes gold". They pick up again on August 20.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -


- 2009-08-20: Enforce the constraints when generating a board [r28328, hosaka]
  - Checks that the constraints defined in r28312 actually hold. If they
    don't, regenerate the board up to 100 times until they do hold.

- 2009-08-25: Display level to the user [r28357, hosaka]
  - The concept of Levels is solidified here. The current level is displayed to
    the user. A message appears after finishing a board if the level changed.
  - Now as long as you beat the previous board, you'll at least get Lv 2.

- 2009-08-25: Game designer tweaks Lv 8 condition [r28359, shimoyamada]
  - You need to have flipped 10 cards instead of 12 for the last 5 games.

- 2009-08-26: Game designer tweaks next-level requirements and board configs [r28364, shimoyamada]
  - These changes don't make the most sense...
    - Lv 3 requires flipping 12 cards
    - Lv 4 requires flipping 8 cards
    - Lv 5 requires flipping 12 cards
    - Lv 6 requires flipping 8 cards
  - Adds tougher constraints around 2's and 3's without bombs for Lv 1 only

- 2009-08-26: Switch to linear level progression [r28365, hosaka]
  - Switches to a linear level progression:
     - Except for Lv 8, you must complete the previous level to advance to the
       next one.
     - If you lose or quit, you can stay on the current level if you flipped the
       required number of cards.
       - Lv 2: flipped 12 cards
       - Lv 3: flipped 10 cards
       - Lv 4: flipped 8 cards
       - Lv 5: flipped 6 cards
       - Lv 6: flipped 4 cards
       - Lv 7: flipped 4 cards
       - Lv 8: flipped 10 cards (each time)
  - Adds requirement that you must be at least at Lv 5 to reach Lv 8.
  - Regenerates the board up to 1000 times to satisfy the constraints.

- 2009-08-26: Fixes Lv 8 progression bug [r28367, hosaka]
  - Fixes bug: Now you can stay on Lv 8 as long as you beat it.
  - Adds a debugging mechanism to jump to any level.

- 2009-08-27: Game designer adjusts card flip requirements [r28369, shimoyamada]
  - Now if you lose or quit, you need to have flipped 5 cards to stay on the current
    level. If you failed to flip 5 cards, you drop all the way to Lv 1.

- 2009-08-27: Game designer adjusts card flip requirements again [r28370, shimoyamada]
  - Realizing the harshness of the previous change, we return to a custom number of
    card flips to stay on the same level:
       - Lv 2: flipped 4 cards
       - Lv 3: flipped 5 cards
       - Lv 4: flipped 6 cards
       - Lv 5: flipped 7 cards
       - Lv 6: flipped 8 cards
       - Lv 7: flipped 9 cards
       - Lv 8: flipped 10 cards (each time)

- 2009-08-27: Fixes Lv 8 progression bug [r28376, hosaka]
  - Fixes bug that inadvertently made it impossible to reach Lv 8, even if you
    met all the conditions.

- 2009-08-27: Game designer adjusts card flip requirements again [r28378, shimoyamada]
  - If you lose or quit, you need to have flipped X cards to stay on Lv X.
  - Reaching Lv 8 requires flipping 8 cards (each time).
  - This matches the behavior in the final game.

- 2009-08-27: Modify condition for suspenseful music [r28379, hosaka]
  - Percentage of unopened cards in the row or column that must be bombs changes
    from 60% -> 75%.


- - - - - - - - - - - - - - - - - - - - - - - - -
Finally, the mini-game is done!.. Or not quite...
They fix one last bug about a month later.
- - - - - - - - - - - - - - - - - - - - - - - - -


- 2009-10-05: Fix bug in coin display after tutorial [r28459, hosaka]


- - - -
THE END
- - - -
