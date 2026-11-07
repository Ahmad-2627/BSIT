# CC-313 Analysis of Algorithms: Phase 1, Session 1 (Foundations)

BSIT 5th Semester | Prerequisite: CC-213 Data Structures | Covers CLO1 and CLO2 (introduction)

## 1. Objectives and Prerequisites

**Objectives of this session**

- Understand what an algorithm is and why it matters in computing.
- Understand input size (n) and input nature.
- Learn the 6-step algorithm design process.

**Prerequisites:** arrays, loops, basic pseudocode (Phase 0 check completed: 8/9).

## 2. Topic Index

1. What is an algorithm?
2. Role of algorithms in computing
3. Size of input and nature of input
4. Designing algorithms
5. Common mistakes
6. Practice questions (all compulsory)
7. Exam-oriented answers
8. Summary and revision checklist

## 3. Concepts

### 3.1 What is an algorithm?

**Simple idea (Unglish):** Algorithm bas ek step-by-step tareeqa hai kisi problem ko solve karne ka, jaise chai ki recipe: input (pani, patti, doodh), steps, aur output (chai).

**Formal definition (English):** An algorithm is a finite sequence of well-defined steps that takes some input and produces the required output.

**The 5 properties of an algorithm**

| Property | Simple meaning |
| --- | --- |
| Input | Zero or more values are given. |
| Output | At least one result is produced. |
| Definiteness | Every step is clear, with no confusion. |
| Finiteness | It stops after a limited number of steps. |
| Effectiveness | Every step is simple enough to be done as a basic operation. |

### 3.2 Role of algorithms in computing

**Simple idea (Unglish):** Computer tez hai, lekin data bohat bara ho sakta hai. Tab behtar algorithm hardware se zyada farq daalta hai.

**Formal statement (English):** Computing resources (time and memory) are limited, so efficient algorithms are needed to solve large problems in reasonable time.

**Example: searching 1 billion (10^9) log lines**

| Algorithm | Approximate steps |
| --- | --- |
| Linear search | 1,000,000,000 |
| Binary search (on sorted data) | about 30 |

Same computer, same data, but the difference is about 30 million times. This gain comes from a better algorithm, not from faster hardware.

**Additional / Industry Context (not official syllabus):** SOC teams work with very large logs and blocklists, so fast searching and sorting matter there. This is a useful way of thinking, not a direct SOC skill.

### 3.3 Size of input and nature of input

**Simple idea (Unglish):** Algorithm ka time input ki size par depend karta hai. Size ko hum **n** kehtay hain. Lekin input ki **nature** (kaisa hai) bhi matter karti hai.

**Formal definition (English):** The input size is the quantity that measures how large the input is, usually written as n. The nature of the input means its characteristics, such as being sorted, reverse sorted, random, or containing duplicates.

**What is n for different problems?**

| Problem | Input size |
| --- | --- |
| Searching or sorting an array | Number of elements, n |
| String matching | Length of the string |
| Graph algorithms (BFS, DFS) | Two quantities: V (vertices) and E (edges) |
| Primality test of a number | Number of bits (digits) of the number |

**Example: linear search**

```
LINEAR-SEARCH(A, n, key)
1  for i = 1 to n
2      if A[i] == key
3          return i
4  return NOT-FOUND
```

| Case | Condition | Comparisons |
| --- | --- | --- |
| Best case | key is at the first position | 1 |
| Worst case | key is at the last position or not present | n |

The code is the same, but the input nature changes the running time. This is the idea behind best, expected, and worst case (formal treatment in Phase 2).

### 3.4 Designing algorithms

**Simple idea (Unglish):** Naya problem aaye to har baar yeh 6 steps chalain.

| Step | What to do |
| --- | --- |
| 1. Understand the problem | What is the input? What output is needed? |
| 2. Try examples | Solve small inputs by hand. |
| 3. Choose a strategy | Brute force, divide and conquer, greedy, or dynamic programming (covered in later phases). |
| 4. Write pseudocode | Express the steps clearly. |
| 5. Check correctness | Does it always give the right answer? |
| 6. Analyze complexity | How much time and memory does it need? |

**Brute force (first look):** Try all possible options. It is usually correct and simple, but often slow.

## 4. Common Mistakes

- Thinking that a faster computer removes the need for good algorithms.
- Taking n as the value of the input, not its size (for example, in a primality test).
- Forgetting that graphs have two size parameters, V and E.
- Judging an algorithm from only one input instead of considering best and worst cases.

## 5. Practice Questions (all compulsory)

1. Define "algorithm" and list any 3 of its 5 properties.
2. Linear search runs on an array of 500 elements. What is n here? Which input condition makes it fastest and which makes it slowest?
3. Binary search runs on a sorted array of 1,000,000 elements. Roughly how many steps does it take? (Hint: each step halves the array.)
4. Which two quantities describe the input size when running BFS on a graph?
5. Algorithm A takes n^2 steps and algorithm B takes n log2(n) steps. For n = 1,000,000, roughly how big is the difference?
6. Write pseudocode to find the smallest element of an array. Are the best and worst cases different or the same, and why?

## 6. Exam-Oriented Answers

**Q: Define algorithm.**

An algorithm is a finite sequence of well-defined steps that takes input and produces the required output. It must have input, output, definiteness, finiteness, and effectiveness.

**Q: Why do we need to analyze algorithms?**

Resources like time and memory are limited. Analysis helps us compare algorithms and choose the one that is efficient for large inputs.

## 7. Summary and Revision Checklist

- An algorithm is a finite sequence of well-defined steps from input to output.
- Efficient algorithms matter because data is large and resources are limited.
- Input size is n (or V and E for graphs); input nature changes the running time.
- Design process: understand, examples, strategy, pseudocode, correctness, complexity.

**Revision checklist**

- [ ] I can define an algorithm and name its 5 properties.
- [ ] I can explain why an efficient algorithm beats faster hardware.
- [ ] I can say what n is for a given problem.
- [ ] I can explain why the same code can have different best and worst cases.
- [ ] I can list the 6 design steps.
