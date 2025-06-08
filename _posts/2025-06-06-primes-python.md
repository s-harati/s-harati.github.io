---
title: A Prime Number Finder Function with Python
date: 2025-06-06
layout: post
image: "/_posts/primes.jpg"
tags: [Python, Primes]
---
# A Prime Number Finder Function with Python
In this post we are going to walk through a small but beatifully challenging Python programming project: writing a function that finds all prime numbers up to a given number.
## Prime Numbers and the Sieve of Erathostenes
Prime numbers are positive integers (whole numbers) greater than 1 that are *only* divisible by themselves and 1. For example, 2 and 3 and 5 are primes, but 4 and 6 are not. An interesting question then is how to find prime numbers.
Greek mathematician Erathostenes developed an algorithm for successively finding prime numbers. In this algorithm, which is now know as the Sieve of Erathostenes, we first write all numbers in order, then we cross out all multiples of 2, which is the first prime number. The smallest number that is not crossed is 3, a prime number. We repeat the same operation and cross out all of multiples of 3, and so on. As the name implies, the algorithm works like a sieve, where non-prime multiples are crossed out and only prime numbers remain.
## A Python Implementation
### Objective
To write a Python function that takes an integer *n* as input, and produces a list of all prime numbers up to *n*.
### Basic Idea: Lists
Of the many possible ways to implement this algorithm, I suggest working with lists, and particularly with a list of booleans. That is, a list in which every element is either `True` or `False`. This will match with the algorithm's logic of crossing out numbers. Here, numbers are indices that point to elements of the list. We set the value of an element of the list to `True` when the algorithm has identified the state of its index. This will be a sign not to spend any more time for analyzing that number again. Below, we will see the implementation of these ideas in more details, followed by a simple example.
***Initiation***
Suppose the upper limit of our search for prime numbers is `n`.
We define an empty list to store primes as we find them:
```python
primes_list = []
```
We also define a boolean list for all numbers less than or equal to `n`. We initially set all values to `False` except for indices 0 and 1. That is because the smallest number that requires our attention is 2:
```python
crossed_list = [False] * (n+1)
crossed_list[0] = True
crossed_list[1] = True
```
And we define an index variable to read through the boolean list, starting from 2:
```python
reader = 2
```
***Iteration***
At the beginning of each iteration, the `reader` index is a prime number, so we add it to `primes_list`:
```python
primes_list.append(reader)
```
Then, we identify all multiples of this `reader` and mark them in `crossed_list` so that we don't consider them again. Since we have already identified this `reader` as prime and we don't want to analyze it again, we mark this `reader` to. This is easily done by a `for` loop:
```python
for i in range(reader , n+1 , reader):
    crossed_list[i] = True
```
Then, we update the `reader` index by moving it forward along the `crossed_list`. We stop when we arrive at an element that is not marked, hence its value is `False`. This index will be the `reader` at the beginning of the next iteration. 
At the heart of this operation we have the following `while` loop:
```python
while crossed_list[reader]:
    reader += 1
```
We should note that in the above loop, the `reader` index will eventually reach the end of the `crossed_list`. That should raise a flag to break out of the loop and terminate the procedure:
```python
while crossed_list[reader]:
    if reader == n:
        break
    reader += 1
```
***Example***
Let us try a very, very simple example to see how the algorithm flows. We will try more serious tests later on. For now, let us consider `n` is 4, that is, we want to find the list of prime numbers less than or equal to 4. We know of course that the answer is `[2,3]`. In the following, we are going to `print` the `crossed_list` at beginning, middle, and end of the implementation.
After initiation, `primes_list` is empty, `reader` is 2, and here is our 1st `print` statement. Note that the smallest index with a value of `False` is 2:
```
print(crossed_list)
[True, True, False, False, False]
```
The iterations begin. Current `reader`, 2, is added to `primes_list`. Then multiples of 2 are marked in `crossed_list`. Let us see another snapshot of the list:
```
print(crossed_list)
[True, True, True, False, True]
```
Now the `reader` index is updated by increments. The incrementation stops at index 3, because `crossed_list[3]` is `False`. Therefore, the value of `reader` for the next iteration is 3.
In the next iteration, current `reader`, now 3, is added to `primes_list`. Then multiples of 3 are marked in `crossed_list`. Here is our third snapshot of the list:
```
print(crossed_list)
[True, True, True, True, True]
```
Now the `while` loop for updating `reader` becomes active. But `crossed_list[4]` is already marked `True`, and 4 is the upper limit of calculations of this example. There is nothing left to check. Therefore, we raise a flag to show the end the procedure.
### Code
The code below automates the iterations described above in a `while` loop, and uses a boolean variable named `termination_flag` to note when to end the procedure. Also, the entire implementation is defined as a function of `n`, which returns `primes_list` and prints a summary of results.
```python
def primes_finder(n):
    # list to be filled with discovered primes
    primes_list = []

    # boolean list to identify crossed out numbers
    crossed_list = [False] * (n+1)
    crossed_list[0] = True
    crossed_list[1] = True

    # index to read through the crossed list, also to add to primes list
    reader = 2

    # boolean variable for ending the program
    termination_flag = False

    # main loop
    while not termination_flag:
        # put reader in primes list
        primes_list.append(reader)
        
        # cross out multiples of reader (set their value True in crossed list)
        for i in range(reader , n+1 , reader):
            crossed_list[i] = True
        
        # update reader and check for termination flag
        while crossed_list[reader]:
            if reader == n:
                termination_flag = True
                break
            reader += 1

        
    # summarize and report primes list
    prime_count = len(primes_list)
    largest_prime = max(primes_list)

    print(f"There are {prime_count} prime numbers between 2 and {n}, the largest of which is {largest_prime}.")
    
    return primes_list
```
### Results
To run, first select the entire code of the function and run it. Then, call the function as shown below.
Let us run this function with upper limit 20:
```
test_primes = primes_finder(20)
There are 8 prime numbers between 2 and 20, the largest of which is 19.
```
And we can see the primes:
```
print(test_primes)
[2, 3, 5, 7, 11, 13, 17, 19]
```
Let us now try upto 100:
```
test_primes = primes_finder(100)
There are 25 prime numbers between 2 and 100, the largest of which is 97.
```
And the longer list of primes:
```
print(test_primes)
[2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71, 73, 79, 83, 89, 97]
```
We can still push further. I will not print the list of primes for larger limits, but it is interesting to see the successful runs of the function with large numbers.
1,000:
```
test_primes = primes_finder(1000)
There are 168 prime numbers between 2 and 1000, the largest of which is 997.
```
1,000,000:
```
test_primes = primes_finder(1000000)
There are 78498 prime numbers between 2 and 1000000, the largest of which is 999983.
```
10,000,000:
```
test_primes = primes_finder(10000000)
There are 664579 prime numbers between 2 and 10000000, the largest of which is 9999991.
```
The last operation took only about 3 seconds on my machine!
I hope you enjoyed this exercise, and I hope that you go ahead and try it your own way!
