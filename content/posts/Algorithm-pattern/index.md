---
title: "Algorithm Pattern"
date: 2022-05-23
draft: true
author: Youhee
images: []
tags: ['python']
categories: ["Machine Learning"]

lightgallery: true

toc:
  auto: false
math:
  enable: true
---
https://www.educative.io/blog/top-data-engineer-interview-questions-and-tips#programming-languages
https://www.codinginterview.com/interview-roadmap
## 0. Brushing up Python 
* How to read/write from/to files


* How to read input from the console


* How to split strings


* Is string length a function or a property (answer: it doesn’t matter, but still reflects poorly on you)

​
* How to declare and use 2D arrays

* Process command line arguments

* Remember to revise topics like:

Complexity Analysis (a.k.a BigO), Arrays, Stacks, Queues, Linked List, Trees, Tries, Graphs (BFS and DFS), Hash Tables, Heaps, Sorting, Searching
​

## 1. Introduction - Binary Search 

The function will look for item within the list of values in an array. You guess the middle number and eliminate half the remaining numbers every time

```python

def binary_search(list, item):
  low = 0 
  high = len(array) - 1
  while low <= high: 
    middle = (low + high) / 2
    guess = list[middle]
    if guess == item:
      return guess
    elif guess > item:
      high = middle - 1 
    else: 
      low = middle + 1 
  return None 
  



```