# Algorithm Pattern

https://www.educative.io/blog/top-data-engineer-interview-questions-and-tips#programming-languages
https://www.codinginterview.com/interview-roadmap
## 0. Brushing up Python 
* How to read/write from/to files

​

* How to read input from the console

​

* How to split strings

​

* Is string length a function or a property (answer: it doesn’t matter, but still reflects poorly on you)

​

* How to declare and use 2D arrays

* Process command line arguments

* Remember to revise topics like:

Complexity Analysis (a.k.a BigO), Arrays, Stacks, Queues, Linked List, Trees, Tries, Graphs (BFS and DFS), Hash Tables, Heaps, Sorting, Searching
​



Parse CSV or text files

​## 1. Pattern Sliding
> Given an `array`, find the average of all `subarrays` of '`K`' contiguous elments in it 

### Basic answer
```python 
def find_averages_of_subarrays(K, arr):
    result = [] 
    for i in range(len(arr)-k+1):
        # find sum of next K elements
        _sum = 0.0
        for j in range(i, k):
            _sum += arr[i]
        result.append(_sum/K) # calculate the averages
    
    return result 
```
* Time Complexity : O(N*K)
Every element of the input array, we are calculating the sum of its next K element. 

### Slide Window approach 
we are reusing the `sum` from previous subarray, we will subtract the element going out of the window and add the element now being included in the sliding window. 

```python 
def find_averages_of_subarrays(K, arr):
    results = []
    windomSum, windowEnd = 0.0, 0
    for windowEnd in range(len(arr)):
        windowSum += arr[windowEnd] # add the next element
        # slide the window, we don't need to slide the indow if weve not hit the requeired window
        if windowEnd >= k - 1:
            result.append(windowSum / K)
            windowSum -= arr[windowStart] # subtrack the element going out 
            windowStart += 1
    return result 

def main():
  result = find_averages_of_subarrays(5, [1, 3, 2, 6, -1, 4, 1, 8, 2])
  print("Averages of subarrays of size K: " + str(result))


main()
```
#### Exercise 1. 
Given an array of positive numbers and a positive number ‘k,’ find the maximum sum of any contiguous subarray of size ‘k’.

```python 
Input: [2, 1, 5, 1, 3, 2], k=3 
Output: 9
Explanation: Subarray with maximum sum is [5, 1, 3].
```
```python 
def max_sub_array_of_size_k(k, arr):
  max_sum , window_sum = 0, 0
  window_start = 0

  for window_end in range(len(arr)):
    window_sum += arr[window_end]  # add the next element
    # slide the window, we don't need to slide if we've not hit the required window size of 'k'
    if window_end >= k-1:
      max_sum = max(max_sum, window_sum)
      window_sum -= arr[window_start]  # subtract the element going out
      window_start += 1  # slide the window ahead
  return max_sum


def main():
  print("Maximum sum of a subarray of size K: " + str(max_sub_array_of_size_k(3, [2, 1, 5, 1, 3, 2])))
  print("Maximum sum of a subarray of size K: " + str(max_sub_array_of_size_k(2, [2, 3, 4, 1, 5])))

main()
```
* Time Complexity : O(N) 
* Space Complexity: O(1) - algorithm runs in constant space

#### Exercise 2. Smallest Subarray With a Greater Sum (easy)

Given an array of positive numbers and a positive number ‘S,’ find the length of the smallest contiguous subarray whose sum is greater than or equal to ‘S’. Return 0 if no such subarray exists.

```python
Input: [2, 1, 5, 2, 3, 2], S=7
Output: 2
Explanation: The smallest subarray with a sum greater than or equal to ‘7’ is [5, 2].

Input: [2, 1, 5, 2, 8], S=7
Output: 1
Explanation: The smallest subarray with a sum greater than or equal to ‘7’ is [8].

Input: [3, 4, 1, 1, 6], S=8
Output: 3
Explanation: Smallest subarrays with a sum greater than or equal to ‘8’ are [3, 4, 1] or [1, 1, 6].
```



```python 

```
#### Exercise 3. Longest Substring with maximum K Distinct Characters (medium)

Given a string, find the length of the longest substring in it with no more than K distinct characters.

```python
## Example 1:

Input: String="araaci", K=2
Output: 4
Explanation: The longest substring with no more than '2' distinct characters is "araa".

## Example 2:

Input: String="araaci", K=1
Output: 2
Explanation: The longest substring with no more than '1' distinct characters is "aa".

## Example 3:

Input: String="cbbebi", K=3
Output: 5
Explanation: The longest substrings with no more than '3' distinct characters are "cbbeb" & "bbebi".

## Example 4:

Input: String="cbbebi", K=10
Output: 6
Explanation: The longest substring with no more than '10' distinct characters is "cbbebi".

* Steps
1. 
```
* Solution 
```python 


```



## 2. Two pointers
## 3. Fast & Slow pointers
## 4. Merge Intervals
## 5. Cyclic Sort
## 6. In Place Reversal of a LinkedList
## 7. Tree Breadth First Search 
## 8. Tree Depth First Search 
## 9. Two Heaps
## 10. Subsets
## 11. Modified Binary Search 
## 12. Bitwise XOR 
## 13. Top 'K' Elements
## 14. K-way merge
## 15. 0/1 Knapsack (Dynamic Programming)
## 16. Topological Sort (Graph)

