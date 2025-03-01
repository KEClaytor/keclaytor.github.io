---
layout: post
title: Sudoku Solver
author: K. E. Claytor
tags: programming, optics
---

A GUI that allows one to enter in a Sudoku and then solves it.
- At this point it only uses a "forced method" and cannot solve all sudoku puzzles.
- Stay tuned for Version 2 which should be able to solve even harder ones.

To use simply install the two matlab ".m" files to your matlab work path and then type in `SudokuSolver`.

[SudokuSolver.m](/assets/mfiles/SudokuSolver.m) - The GUI that allows easy interface to building a Sudoku matrix (not required).

[SudokuCore.m](/assets/mfiles/SudokuCore.m) - The program that actually solves the 9x9 matrix (required).

![initial puzzle through the ui](/assets/images/sudoku/sudokua.jpg)

A screenshot of the Sudoku GUI before solving

![solved sudoku puzzle](/assets/images/sudoku/sudokub.jpg)

A screenshot of the Sudoku GUI after solving - This one was rated a 5 of 5 in the Houston Chronicle.

Revision history;
- Version 1.0 - Functional forced method
