+++
title = "Advent of Code 2025 - Day 01 ILE RPG solution"
date = "2025-12-02T15:28:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
authorTwitter = "" #do not include @
cover = ""
tags = ["ibmi","aoc"]
keywords = ["ibmi","aoc"]
description = "Advent of Code 2025 — Day 01 solved in ILE RPG (dial wrap logic, %rem). Code and notes on GitHub."
showFullContent = false
readingTime = true
hideComments = false
+++

## Introduction

Advent of Code is an annual event in December where programming puzzles are released daily. This post presents my solution for Day 01 of Advent of Code 2025 using ILE RPG on IBM i.

The puzzle can be found [here](https://adventofcode.com/2025/day/1).

My complete solution code is available on [GitHub](https://github.com/dferrand/aoc2025).

## Problem Description

The day 01 puzzle involves a dial with numbers from 0 to 99. The dial starts at 50, the input consists of a series of instructions to turn the dial left or right by a certain number of steps.

## First Part

In the first part, we need to determine how many times the dial ended on position 0 after an instruction from the input.

We use the `QSYS2.IFS_READ_UTF8` table function to read the input file line by line.

We store the current position of the dial in a `position` variable, initialized to 50. For each instruction, we parse the direction (L or R) and the number of steps, then update the position accordingly.

We use the `%rem` built-in function to handle the wrap-around of the dial. If the position goes below 0, `%rem` will return a negative value, so we add 100 to it to get the correct position.

At each line, we check if th e position is 0, and if so, we increment the `zeroCount` variable.

## Second Part

In the second part, we need to find how many times the dial ended on position 0 after each click.

We modify the logic to add the absolute value (using the `%abs` built-in function) of the integer division of the position by 100 (using the `%div` built-in function) to the `zeroCount` variable.

If the position is negative or 0, we add 1 more to the `zeroCount` variable if we didn't stop on 0 on th previous step.)