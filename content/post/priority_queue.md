---
title: "Priority queue"
date: 2018-04-23T16:31:02+02:00
draft: true
tags: [
    "go",
    "golang",
    "development",
    "data structures",
    "algorithms",
]

categories: [
    "data structures",
    "algorithms",
]
---

In this post series I am going to discuss and show implementations of common data structures, let's consider a simple problem, how would an operating system kernel select which process has to run amongst all the ones that are being launched? We could solve this problem using a priority queue give each process a priority ( think about Linux and the niceness of a process, a priority queue can be used as a building block for a process scheduler ). 
A priority queue is an abstract data type meaning it defines a series of methods that the actual implementation has to have in order to be considered a priority queue.
This type of queues are characterized by two operations that can be done on them:

* Insert
* Max or Min
* Pop Max or Pop Min

Priority queues can either remove the smallest or the largest item in the queue depending on the function the compares the values.
To implement a queue efficiently we can use a "binary heap", in such a concrete data structure the keys are stored in an array such that each key in each node is larger or smaller ( depending on the type of queue max or min ) than or equal to the keys in that node's two children if any, this is also called "heap ordered binary tree".
It is particularly convenient to use a complete binary tree because it offers the opportunity to use a compact array implementation, specifically we can represent it
sequentially within an array by putting nodes in "level order" with root a position 1 its children at position 2 and 3 and their children in position 4,5,6,7 and so on. 
Instead of using links like in normal trees using an array representation allows us
to move to the children of K by doing simple arithmetic in fact it two children are in position 2K and 2K+1 and the parent is in position K/2.
Complete binary trees represented as arrays ( heaps ) are rigid structures but they
have enough flexibility to allow us to implement efficient priority queue operations,
in particular they are ideal to implement insert and remove min or max in logarithmic time.
The implementation works by keeping the 0 element of the array empty, and start inserting the nodes at index 1, the heap operations works by first invalidating
the the heap condition and then traveling trough the heap as required to ensure
the heap condition is satisfied everywhere, we refer to it as restoring heap order.
The heap order is violated if a node key becomes larger or smaller ( depending on
the type of heap ) than both of it's children, we can make progress toward restoring the heap order by exchanging the node with its parent, we keep doing so until moving up the heap until we reach a node with larger or smaller key or the root.
The implementation is straight forward when we keep in mind that the parent of node at position K in a heap is at position K/2.

```go
func(q *Pqueue) swim(k int){
    for ;k > 1 && compare(k/2, k, q.array); k = k / 2{
        q.array[k], q.array[k/2] = q.array[k/2], q.array[k] // a swap in go
    }
}
```
So just like I explained earlier we do like so:

```go
func(q *Pqueue ) Insert(v int){
    q.array = append(q.array, v)
    q.swim(len(q.array)-1)
}
```
We first possibly violate the heap order by adding an element at the end of the array
and then restore heap order with the swim routine. Let's look at the following example of inserting a small series of integers like the following 1,2,3,4,5:

---------

* Insert(1)

- nil, 1

Loop condition is not satisfied by because len-1 of the array is equal to 1, nothing to do.

--------


* Insert(2)
- nil, 1, 2

Loop condition is satisfied, the heap order is violated because the children is bigger than the parent therefore we swim and restore the order by exchanging parent ahd children.

- nil, 2, 1

------

* Insert(3)
- nil, 2, 1, 3

Loop condition is satisfied, the heap order is violated because the children is bigger than the parent therefore we swim and restore the order by exchanging parent ahd children.

- nil, 3, 1, 2  

-------

* Insert(4)
- nil, 3, 1, 2, 4

Loop condition is satisfied, the heap order is violated because the children is bigger than the parent therefore we swim and restore the order by exchanging parent ahd children.

- nil, 3, 4, 2, 1
- nil, 4, 3, 2, 1

------

* Insert(5)

- nil, 4, 3, 2, 1, 5

Loop condition is satisfied, the heap order is violated because the children is bigger than the parent therefore we swim and restore the order by exchanging parent ahd children.

- nil, 4, 5, 2, 1, 3
- nil, 5, 4, 2 ,1 ,3