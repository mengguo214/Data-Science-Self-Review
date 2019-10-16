
## Binary Search
### 167.TWO-SUM SORTED

普通方法是binary search，也可以用hash，重点是把位置当成dict的values，边找边存
```
class Solution:
    def twoSum(self, numbers: List[int], target: int) -> List[int]:
        dic={}
        for i in range(0,len(numbers)):
            if target-numbers[i] in dic:
                return [dic[target-numbers[i]]+1,i+1]
            dic[numbers[i]]=i
```


## Math
### 69. sqrt(x)

Newton's Method

```
class Solution(object):
    def mySqrt(self, x):
        n = x
        while n * n > x:
            n = (n + x / n) / 2
        return int(n)
```

Gradient Descent
```
class Solution:
    def mySqrt(self, x):
        n=2
        while True:
            if n*n<=x and (n+1)*(n+1)>x:
                return n
            z=(x-n*n)/(2*n)+n
            n=int(z)
```

### 441. Arranging Coins

We can solve this problem with pure math

The Coins are arranged in the following way:
[1,2,3,4,5....]
This is an arithmetic sequence. To find the sum S of an arithmetic series , the formula is :
S = ((a + b)* h) /2
Where:
a is the number of coins on the first line
b is the number of coins of the last line
h is the number of lines we have (we want to find h)
We note that a is always 1 because we start with one coin
Therefore a = 1
Note that the number of coins on the last line is always the same as the the number of total lines.
Therefore: b = h
The equation can then be simplified into:
S = (1 + h)* h / 2
Since we want only full lines, we want the sum S to be smaller than n, the total amount of coins in the question.
Therefore:
(1 + h)* h / 2 <= n
Which simplifies into:
h^2 + h <= 2n
Or:
h^2 + h - 2n <= 0
This can be solved using the quadratic formula: (note that a and b here are different from a and b before)
ax^2 + bx + c = 0
x = -b - sqrt(b^2 - 4ac) / 2a
x = -b + sqrt(b^2 - 4ac) / 2a
In our case:
a = 1, b = 1, c = -2n
which yields:
h ≤ (sqrt(8n + 1) -1)/2
h ≥ (-sqrt(8n + 1) -1)/2
Since n >= 1, and negative values of h are irrelevent, we can ignore the second equation. (It simply requires h > 0)
Finally, the code is simply:

```
return int((math.sqrt(8 * n + 1)-1)/2)
```

## Stack
###20. Valid Parentheses
用stack解决matching的问题
```
class Solution:
    def isValid(self, s: str) -> bool:
        left_bracket = []
        matching_bracket = {'(':')', '{':'}', '[':']'}
        for i in s:
            if i in matching_bracket:
                left_bracket.append(i)
            elif not left_bracket or matching_bracket[left_bracket[-1]] != i:
                return False
            else:
                left_bracket.pop()
        return not left_bracket
```

## Sort
### 977. Squares of a Sorted Array

因为是sorted list，有好性质，可以从左右开始向中间推进，O(n)的做法是

```
def sortedSquares(A):
    l,r = 0, len(A)-1
    res = []
    result =[]
    while l <= r:
        left,right = abs(A[l]),abs(A[r])
        if left > right:
            res.append(left**2)
            l += 1
        else:
            res.append(right**2)
            r -= 1

    return res[::-1]

```

也可以用heap（之后回来看），或者一个巧妙的方法：

先建好长度固定的list，通过研究左右指针和放置元素的关系：

```
def sortedSquares(self, A):
    answer = [0] * len(A)
    l, r = 0, len(A) - 1
    while l <= r:
        left, right = abs(A[l]), abs(A[r])
        if left > right:
            answer[r - l] = left * left
            l += 1
        else:
            answer[r - l] = right * right
            r -= 1
    return answer

```

### 88. Merge Sorted Array
和上一题相似的一道题，也是通过研究两个指针和放置元素的关系：
```
def merge(nums1,m,n,nums2):
    if m == 0 and n!= 0:
        nums1 = nums2
        return nums1
    if n == 0:
        return nums1
    while m>=1 and n >= 1:
        if nums2[n-1] > nums1[m-1]:
            nums1[n+m-1] = nums2[n-1]
            n -= 1
        else:
            nums1[n+m-1] = nums1[m-1]
            m -= 1
    if n > 0:
        nums1[:n] = nums2[:n]
    return nums1



```
