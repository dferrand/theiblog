+++
title = "ILE RPG binary search"
date = "2025-10-30T06:30:00-04:00"
#dateFormat = "2006-01-02" # This value can be configured for per-post date formatting
author = "Damien Ferrand"
cover = ""
tags = ["ibmi", "rpg", "ile rpg"]
keywords = ["ibmi", "rpg", "ile rpg"]
description = "How and why to use binary search in ILE RPG"
showFullContent = false
readingTime = true
hideComments = false
+++

## Array searching algorithms

When searching for a value in an array, there are two main algorithms that can be used: linear search and binary search.

### Linear search

A linear search goes through each element of the array one by one until it finds the target value or reaches the end of the array. This algorithm is simple to implement but can be inefficient for large arrays, as it has a time complexity of O(n), where n is the number of elements in the array (it means that in the worst case, it will take n comparisons to find the target value).

### Binary search

A binary search, on the other hand, is a more efficient algorithm that works on sorted arrays. It repeatedly divides the search interval in half, eliminating half of the remaining elements from consideration at each step. This algorithm has a time complexity of O(log n), making it much faster than linear search for large arrays.

### Comparison of linear and binary search

| Number of elements | Linear search (max comparisons) | Binary search (max comparisons) |
|--------------------|---------------------------------|----------------------------------|
| 10                 | 10                              | 4                                |
| 100                | 100                             | 7                                | 
| 1,000              | 1,000                           | 10                               |
| 10,000             | 10,000                          | 14                               |

As you can see from the table above, binary search is significantly more efficient than linear search for large arrays.

## ILE RPG 

### LOOKUP operation code

The fixed format LOOKUP operation code always performs a linear search. It can be used with both sorted and unsorted arrays, but it is not as efficient as binary search for large sorted arrays.

### %LOOKUP built-in function

The %LOOKUP built-in function performs a linear search when used with unsorted arrays.

When used with a sorted array (the array is defined with the ASCEND or DESCEND keyword), %LOOKUP performs a binary search, making it much more efficient for large sorted arrays.

### Binary search caveats

While binary search is more efficient than linear search for large sorted arrays, it does have some caveats.

#### Sorting cost

The array must be sorted before performing a binary search. While I couldn't find an official definition of the algorithm used by the SORTA operation code, it is, at best, an O(n log n) algorithm. This means that sorting the array to perform a single binary search is likely more expensive than just performing a linear search.

If the array is loaded in sorted order, or if multiple searches are performed on the same sorted array, then the cost of sorting can be amortized over multiple searches, making binary search more efficient overall.

#### Sorting is not enforced

Neither the ILE RPG compiler nor the %LOOKUP built-in function enforce that the array is sorted when performing a binary search. If the array is not sorted, the result of the binary search might be incorrect.
It is the programmer's responsibility to ensure that the array is sorted before performing a binary search.

The following example demonstrates this:

```rpg
**free

dcl-s tbl int(10) dim(5) ascend;

tbl = %list(9:8:7:6:5);

snd-msg 'Index of 9 for unsorted ' + %char(%lookup(9:tbl));

sorta tbl;

snd-msg 'Index of 9 for sorted ' + %char(%lookup(9:tbl));

*inlr = *on;
return;
```

The array is first purposefully populated in descending order while being defined as sorted in ascending order, then a binary search is performed using %LOOKUP. Since the array is not properly sorted, the result is incorrect (0, not found). After sorting the array in ascending order, the binary search returns the correct index (5).