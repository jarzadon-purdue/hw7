# Homework 7: Card Shuffling using Riffle Shuffling (Part 1)

	  _ _ _ ___ ___ _ _ ___ _ _ _________
	 |A|Q|5|4  |7  |J|9|7  |A|K|2        |
	 |@|@|@|@  |## |O|O|OO |+|+|+        |
	 | | | | @ |   | | |   | | |    +    |
	 | | | |   | # | | | O | | |         |
	 | | | | @ |   | | |   | | |    +    |
	 | | | |   | # | | | O | | |        +|
	 | | | |   |   | | |   | | |        Z|
	  ~ ~ ~ ~~~ ~~~ ~ ~ ~~~ ~ ~ ~~~~~~~~~

## Goals

This is the first part of two assignments. You need to implement the
riffle shuffling algorithm of cards. This part (HW7) asks you to shuffle a
deck of cards **only once**. For the next part (HW8) you will shuffle 
a deck of cards multiple times.

This assignment considers only cards' numbers (between A,1,2,..,T,J,Q,K) 
[where 'T' represents 10]. In other words, this assignment does not consider jokers and
does not distingish Hearts, Diamonds, Clubs, or Spades.

You will learn:

* Use recursion to solve a problem.
* Make copies of structures using `memcpy`.
* Understand the mathematics calculating the number of outputs.


## Background

### 1. Connection between Part 1 and Part 2

Part 1 (HW7) asks you to shuffle cards only once. The next
part (HW8) asks you to shuffle cards multiple times.
As a consequence, you can use a lot of code from HW7 for your HW8.
**Please read both parts so that you can reuse part 1 (HW7) as much as possible 
when you implement part 2 (HW8).**

Your part 1's score will be at least 50% of the part 2's score. Thus,
if you do well in part 2, you may get some points back for part 1, even
though your original score for part 1 may be low.


### 2. Shuffling method

**For this assignment, we always assume that the original deck of cards is ordered, 
that is, {A, 2, 3, 4, 5, 6, ...}**. The purpose of shuffling is to make the result difficult to predict.
Riffle shuffling is one of the most widely used shuffling methods.

Riffle shuffling has two steps: division and interleave.
In the following we discuss these steps in details:

* **Step 1: Division.** Divide a deck of cards into two groups, 
called the **leftDeck** and **rightDeck** 
(because they are held by the left hand and the right hand). 
Each group has at least one card. The two groups may have
different numbers of cards. This is one reason why the shuffling
results is hard to predict.

The following explains the division step with four cards,
that is, the original deck is **{A, 2, 3, 4}**.

* To divide the deck of four cards into two groups (leftDeck and rightDeck), there are three different possibilities.

  * Division 1: leftDeck is **{A}**; rightDeck is **{2, 3, 4}**
  * Division 2: leftDeck is **{A, 2}**; rightDeck is **{3, 4}**
  * Division 3: leftDeck is **{A, 2, 3}**; rightDeck is **{4}**

To generalize, if the original deck has k cards, there are k - 1
different ways to divide the deck because the leftDeck can have 1, 2, ...,
k - 1 cards. Correspondingly, the rightDeck has k - 1, k - 2, ..., 1
cards.

* **Step 2: Interleave.** Typically, the cards are released by the thumbs of the two hands. It is possible (in fact, likely) that the two thumbs are not "perfect" and more than one cards are released at once.  This is the reason why the suffling result is hard to predict. 

The interleaving step is illustrated with the following examples.

#### Example 1

Let us consider the simplest example: the original deck has only two cards: **{A, 2}**

There is one way to divide the original deck into two groups: leftDeck is **{A}**; rightDeck is **{2}**

Interleaving them can produce two possible results: 

* If the right thumb releases the card first, then the interleave result is **{2, A}**
* If the left thumb releases the card first, then the interleave result is **{A, 2}**

#### Example 2

The next example has three cards: the original deck is **{A, 2, 3}**

The cards can be divided in two ways. 

* Division 1: leftDeck is **{A}**; rightDeck is **{2, 3}**. With Division 1, there are three possible interleave results:

  * 1.a: Number A is put before 2, thus the resulting sequence is **{A, 2, 3}**
  * 1.b: Number A is put between 2 and 3, thus the resulting sequence is **{2, A, 3}**
  * 1.c: Number A is put after 3, thus the resulting sequence is **{2, 3, A}**

* Division 2: leftDeck is **{A, 2}**; rightDeck is **{3}**. With Division 2, there are three possible interleave results:

  * 2.a: Number 3 is put before A, thus the resulting sequence is **{3, A, 2}**
  * 2.b: Number 3 is put between A and 2, thus the resulting sequence is **{A, 3, 2}**
  * 2.c: Number 3 is put after 2, thus the resulting sequence is **{A, 2, 3}**

Totally, there are six possible results when shuffling 3 cards.

Please notice that the sequence **{A, 2, 3}** appears twice (in 1.a and 2.c) and the sequence **{3, 2, A}** does not appear.

> **Why can not {3, 2, A} appear?** This is because the order within the leftDeck and within the rightDeck **cannot change**.  
If the leftDeck has 2 cards, then A is always before 2, regardless where 3 is inserted.
If the rightDeck has 2 card, then 2 is always before 3, regardless where A is inserted.
Thus it is not possible to get the sequence **{3, 2, A}** with shuffling once. 
In other words, to get the sequence **{3, 2, A}**, it requires that the order of A-2 and the order of 2-3 are changed, which is not possible when shuffling only once.
Instead, if we can shuffle multiple times (in HW8), then it is possible to get the sequence **{3, 2, A}**. You can think about why this is true. :)

#### Example 3

The next example has three cards: the original deck is **{A, 2, 3, 4}**

There are 14 possible results by shuffling these cards once:

* With Division 1: leftDeck is **{A}**, rightDeck is **{2, 3, 4}**, there are four possible results:

  * **{A, 2, 3, 4}**

  * **{2, A, 3, 4}**

  * **{2, 3, A, 4}**

  * **{2, 3, 4, A}**

* With Division 2: leftDeck is **{A, 2}**, rightDeck is **{3, 4}**, there are six possible results:

  * **{A, 2, 3, 4}**

  * **{A, 3, 2, 4}**

  * **{A, 3, 4, 2}**

  * **{3, A, 2, 4}**

  * **{3, A, 4, 2}**

  * **{3, 4, A, 2}**

* With Division 3: leftDeck is **{A, 2, 3}**, rightDeck is **{4}**, there are four possible results:

  * **{A, 2, 3, 4}**

  * **{A, 2, 4, 3}**

  * **{A, 4, 2, 3}**

  * **{4, A, 2, 3}**


### 3. How many possible sequence can the new deck have?

* If there are *k* cards in the original deck, there are *k - 1* different ways to divide the original deck to two decks:
	* the leftDeck has 1 card, the rightDeck has *k - 1* cards
	* the leftDeck has 2 cards, the rightDeck has *k - 2* cards
	* the leftDeck has 3 cards, the rightDeck has *k - 3* cards
	* the leftDeck has 4 cards, the rightDeck has *k - 4* cards 
	* till, the leftDeck has *k - 1* cards, the rightDeck has 1 card
  
* If the leftDeck has *n* cards and the rightDeck has *m* cards, there are *((n+m)!)/(n! m!)* ways to interleave the two decks, while keeping the orders in the original leftDeck and the original rightDeck. (Remember that both *n* and *m* must be one or larger.)
* If the original deck has a total of k cards, then n + m = k, and n can be 1, 2, ..., k - 1. Adding them together 

  ```k!/(1! (k-1)!) + k!/(2! (k-2)!) + k!/(3! (k-3)!) + ... + k!/((k-1)! 1!) ```=  2<sup>k</sup> - 2

* Please be aware that if a sequence appears multiple times, it is **counted multiple time**. For example, in the previous example with three cards, **{A, 2, 3}** is counted twice.

* For a deck of *k* distinct cards, there are *k!* possible sequences (i.e., permutations).

|k|2|3|4|5|6|
|-|-|-|-|-|-|
|2<sup>k</sup> - 2|2|6|14|30|62|
|k!|2|6|24|120|720|

* As you can see, 
  * *2<sup>k</sup>- 2* is equal to *k!*, when *k* is 2 or 3. 
  * *2<sup>k</sup>- 2* is smaller than *k!*, when *k* is 4 or larger. 
 
Thus, shuffling once cannot generate all possible sequences, except for only when k is 2. Actually, *2<sup>k</sup>- 2* includes duplicate sequence in the count. For example, in the previous example with three cards, **{A, 2, 3}** is counted twice, and **{3, 2, A}** is not generated. 

For simplicity, this assignment does not ask you to determine which sequences are missing and does not ask you to detect duplicate sequences. 
If a sequence may be generated multiple times, your program should print it **multiple times**.


## What do you need to do?

You will need to implement riffle shuffling of k cards in `shuffle.c`.
Your program should take one integer (total number of cards *k*) as input, 
and output all the possible sequences by shuffling these k cards once.
Read the comments in `shuffle.c` carefully to understand the different functions.

### 1. Testing your code

We provide the expected output for three testcases in the `expected` directory: 
when the total number of cards k in the original deck is 4, 8, 11, repectively.

You'll need to write your own Makefile to test your program.
**Remember** to use `valgrind` to check memory leak (you can refer to the Makefile in HW5 and Hw6 for the use of `valgrind`).

It is possible that your program's output and the expected output are
different in some negligible ways. First, your program's output
may have different orders from the expected output. This can be easily
handled if your program's output is sorted. 
Second, you may have additional space in your program output. This can be easily handled by
adding `-w` (ignore white space, including space and
tab) to the `diff` command. The `-w` option tells `diff` to ignore the differences caused by space only.

To sum up, for testing, you can include the following commands in your Makefile:

```
./hw7 4 | sort > output4  # This runs your program with k=4, sorts the program's output, and saves it to a file named output4
diff -w output4 expected/expected4  # This compares your program's output with the expected and ignore spaces in comparison
```

For the functionality of `sort`, you can write a text file with multiple lines to see the specifc results. e.g.
```
echo "A 2 3
2 3 A
3 A 2" > eg.txt  # This command represents writing three lines into eg.txt (The three lines form the command)

sort eg.txt    # Sort lines in the eg.txt and output in lexicographical order. (This would not modify eg.txt)
```

## Grading

Autograder on Gradescope would run your program with arguments from 2 to 13. Full credits will be given if your outputs match the expected files and no memory leakage occurs.

You will lose 1 point for every 1 byte of memory leak. In other words, if your program leaks 100 or more bytes, you will **receive zero**. 

**After submitting your `shuffle.c`, remember to check your total points since your points may be adjusted by the memory leakage checker while your outputs are correct.**

## Submission
Submit the only the following file(s).
1. `shuffle.c`
