# Sorting Algorithms — DSA Study Guide

A practical reference for **16 sorting algorithms**, including time and auxiliary-space complexity, stability, key ideas, and interview-style selection scenarios.

> **Notation:** `n` = number of elements; `k` = value-range size, bucket count, or digit-base size (as specified); `d` = number of digit passes; `I` = number of inversions; `R` = integer value-range size. Complexity assumes standard implementations unless otherwise noted.

## Contents

1. [Comparison-based sorting](#1-comparison-based-sorting)
2. [Non-comparison-based sorting](#2-non-comparison-based-sorting)
3. [Hybrid sorting](#3-hybrid-sorting)
4. [Choosing an algorithm](#4-choosing-an-algorithm)
5. [Nearly sorted arrays: interview example](#5-nearly-sorted-arrays-interview-example)
6. [Bubble Sort vs. Insertion Sort](#6-bubble-sort-vs-insertion-sort)
7. [Key interview takeaways](#7-key-interview-takeaways)

## 1. Comparison-based sorting

These algorithms primarily determine order by comparing elements.

| Algorithm | Best time | Average time | Worst time | Auxiliary space | Stable? | Key idea |
|---|---|---|---|---|---|---|
| Selection Sort | O(n²) | O(n²) | O(n²) | O(1) | No | Repeatedly select the minimum |
| Bubble Sort (optimized) | O(n) | O(n²) | O(n²) | O(1) | Yes | Swap adjacent inversions; stop if no swaps |
| Insertion Sort | O(n) | O(n²) | O(n²) | O(1) | Yes | Insert each item into the sorted prefix |
| Merge Sort (array) | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes | Divide, sort, merge |
| Quick Sort | O(n log n) | O(n log n) | O(n²) | O(log n) average; O(n) worst for typical recursion | No | Partition around a pivot |
| Heap Sort | O(n log n) | O(n log n) | O(n log n) | O(1) | No | Build a heap and extract repeatedly |
| Cycle Sort | O(n²) | O(n²) | O(n²) | O(1) | No | Rotate items to final positions, minimizing writes |
| 3-way Merge Sort | O(n log n) | O(n log n) | O(n log n) | O(n) | Yes, if implemented with stable merges | Split into three parts, then merge |

**Implementation notes:** Bubble Sort achieves O(n) on already-sorted input only with an early-exit swap flag. Quick Sort's recursion space can be limited to O(log n) worst-case by always recursing into the smaller partition and iterating on the larger one. The 3-way Merge Sort row refers to **three-way splitting**, not three-way Quick Sort partitioning.

### How each one works

- **Selection Sort:** Find the smallest remaining item and swap it into position. Few swaps, but O(n²) comparisons even when already sorted.
- **Bubble Sort:** Compare neighbors and swap when out of order. An optimized version stops after a pass with no swaps.
- **Insertion Sort:** Shift larger items right until the current item fits. Especially useful for small or nearly sorted inputs.
- **Merge Sort:** Split recursively and merge sorted halves. Predictable O(n log n) time and stable ordering, at the cost of extra array storage.
- **Quick Sort:** Partition around a pivot and recursively sort partitions. Usually fast in practice, but poor pivot behavior can lead to O(n²).
- **Heap Sort:** Build a max heap, move the maximum to the end, and restore the heap. Guarantees O(n log n) time with constant auxiliary space.
- **Cycle Sort:** Place items by calculating their final positions. Useful when writes are expensive; comparisons remain quadratic.
- **3-way Merge Sort:** Recursively split into three subarrays and merge them. Its asymptotic complexity is the same as standard Merge Sort; it is not automatically faster.

## 2. Non-comparison-based sorting

These exploit properties of the keys (ranges, digits, or distributions), rather than relying exclusively on pairwise comparisons.

| Algorithm | Best time | Average time | Worst time | Auxiliary space | Stable? | Key idea |
|---|---|---|---|---|---|---|
| Counting Sort | O(n + R) | O(n + R) | O(n + R) | O(n + R) for stable output version | Yes, with stable output placement | Count occurrences in a bounded integer range |
| Radix Sort (LSD with Counting Sort) | O(d(n + k)) | O(d(n + k)) | O(d(n + k)) | O(n + k) | Yes, with stable digit passes | Sort by digits from least to most significant |
| Bucket Sort (insertion-sorted buckets) | O(n + k) | O(n + k), under roughly uniform distribution | O(n² + k) | O(n + k) | Depends on bucket implementation | Distribute into buckets, sort each bucket |
| Pigeonhole Sort | O(n + R) | O(n + R) | O(n + R) | O(R) for frequency-based integer output | Depends on implementation | Use a hole for each key value |

**Definitions:** `R = max − min + 1`; for Radix Sort, `d` is the number of digit positions and `k` is the radix/base; for Bucket Sort, `k` is the number of buckets. The Bucket Sort average bound assumes well-distributed values and a suitable bucket count (often `k = Θ(n)`). Counting Sort may use O(R) space if only frequencies are needed and stability is not required.

### How each one works

- **Counting Sort:** Count how many times each integer key occurs, then reconstruct the sorted result. Excellent when the key range is reasonably small.
- **Radix Sort:** Process one digit at a time using a stable digit sorter. Useful for fixed-width integers or strings with suitable key encoding.
- **Bucket Sort:** Distribute values into intervals, sort each bucket, then concatenate. Performs well when values are spread evenly.
- **Pigeonhole Sort:** Allocate slots for the integer range and record each key's frequency. Efficient only when the range is manageable.

> **Classification correction:** TimSort and Comb Sort were in our original non-comparison list, but **both use comparisons**. TimSort belongs under hybrid comparison sorts; Comb Sort is a comparison-based sort. Their complexities appear below.

### Comb Sort (comparison-based)

| Algorithm | Best time | Average time | Worst time | Auxiliary space | Stable? |
|---|---|---|---|---|---|
| Comb Sort | Implementation-dependent; typically not linear | Commonly quadratic in basic implementations | O(n²) | O(1) | No |

Comb Sort is a Bubble Sort variant that compares items separated by a shrinking gap (often using a shrink factor near 1.3). Its precise best and average bounds depend on the gap sequence and implementation; avoid memorizing an unsupported universal O(n log n) best-case guarantee.

## 3. Hybrid sorting

Hybrid algorithms combine multiple strategies to improve real-world performance.

| Algorithm | Best time | Average time | Worst time | Auxiliary space | Stable? | Combines |
|---|---|---|---|---|---|---|
| IntroSort | O(n log n) in standard implementations | O(n log n) | O(n log n) | O(log n) | No | Quick Sort + Heap Sort + often Insertion Sort |
| TimSort | O(n) | O(n log n) | O(n log n) | Up to O(n) | Yes | Merge Sort + Insertion Sort + run detection |

### IntroSort

1. Start with Quick Sort for efficient partitioning.
2. If recursion becomes too deep, switch to Heap Sort to avoid quadratic worst-case time.
3. Often use Insertion Sort for small partitions.

**Where used:** Common implementations of C++ `std::sort` use an introspective sorting strategy (the C++ standard does not mandate a particular algorithm).

### TimSort

1. Detect already ordered **runs** in the data.
2. Extend short runs using an insertion-sort technique.
3. Merge runs while maintaining invariants and stable ordering.

**Where used:** Python `list.sort()` and `sorted()`; Java uses TimSort-derived implementations for certain object-array sorting paths. It is adaptive and especially effective on partially sorted data.

## 4. Choosing an algorithm

| Scenario | Good choice | Why |
|---|---|---|
| Already sorted or nearly sorted | Insertion Sort; TimSort | Exploit existing order |
| Small array | Insertion Sort | Low overhead, simple implementation |
| Large, randomly ordered array | Quick Sort or IntroSort | Good practical performance; IntroSort guards worst case |
| Need stable O(n log n) sorting | Merge Sort or TimSort | Preserve the order of equal-key items |
| Need worst-case O(n log n) and O(1) auxiliary space | Heap Sort | Strong bounds without an auxiliary array |
| Integers in a small range | Counting Sort / Pigeonhole Sort | Can approach linear time in `n + R` |
| Fixed-length integer/string keys | Radix Sort | Digit-wise sorting can be efficient |
| Uniformly distributed numeric values | Bucket Sort | Buckets stay small on average |
| Writes are expensive | Cycle Sort | Minimizes writes, though comparisons are quadratic |

**Reminder:** The fastest option depends on input size, value distribution, memory constraints, stability requirements, and implementation details.

## 5. Nearly sorted arrays: interview example

**Question:** An array has **100,000 elements** and only **10 adjacent pairs** have been swapped. Which sort should you choose from Heap Sort, Merge Sort, Quick Sort, and Insertion Sort?

**Answer: Insertion Sort.**

Insertion Sort runs in **O(n + I)** time, where `I` is the number of inversions (pairs in the wrong relative order). If the array starts sorted and only 10 adjacent swaps are applied, then `I ≤ 10` (some swaps could undo earlier ones). So the runtime is **O(100,000 + 10) = O(n)** for this input.

Heap Sort and Merge Sort still take O(n log n). Standard Quick Sort does not reliably exploit near-sortedness and can even perform badly with poor pivot selection.

**What if most elements are unsorted?** Insertion Sort may require O(n²) time. For large randomly ordered arrays, Quick Sort/IntroSort are usually strong practical choices; Merge Sort is preferable when stability is required, and Heap Sort when worst-case O(n log n) time and constant auxiliary space matter.

## 6. Bubble Sort vs. Insertion Sort

If an array is nearly sorted and the only choices are Bubble Sort and Insertion Sort, **usually choose Insertion Sort**.

| Property | Optimized Bubble Sort | Insertion Sort |
|---|---|---|
| Already sorted input | O(n) | O(n) |
| Nearly sorted input | Can be fast, but may need many passes | O(n + I), where `I` is inversions |
| Random input | O(n²) | O(n²) |
| Worst case | O(n²) | O(n²) |
| Extra space | O(1) | O(1) |
| Stable | Yes | Yes |

**Important nuance:** A small inversion count gives Insertion Sort a direct O(n + I) bound. Bubble Sort may still need many passes if a small element starts far from its destination, even when the inversion count is relatively small. For example, `[2, 3, 4, 5, 1]` has only four inversions, but the `1` can move left by at most one position per left-to-right Bubble Sort pass.

## 7. Key interview takeaways

- **Nearly sorted?** Insertion Sort or TimSort.
- **Random large data?** Quick Sort or IntroSort are often strong choices.
- **Guaranteed O(n log n) with constant auxiliary space?** Heap Sort.
- **Need stability?** Merge Sort, TimSort, or stable Counting/Radix Sort implementations.
- **Small bounded integer range?** Counting Sort or Pigeonhole Sort.
- **Digits/characters suitable for positional sorting?** Radix Sort.
- **Uniform numeric distribution?** Bucket Sort.
- **Minimize writes?** Cycle Sort.
- **Worst-case protection for Quick Sort?** IntroSort.

### Quick definitions

- **Stable:** Equal-key items retain their original relative order.
- **In-place:** Uses constant or very small auxiliary storage; recursion stacks may still count toward space usage.
- **Adaptive:** Takes advantage of existing order in the input.
- **Inversion:** A pair of indices `i < j` for which `a[i] > a[j]`.
- **Best/average/worst case:** Describe input-dependent work for a specified implementation, not universal speed rankings.

---

**Purpose:** A personal Data Structures & Algorithms revision resource for implementation practice, interview preparation, and algorithm selection.
