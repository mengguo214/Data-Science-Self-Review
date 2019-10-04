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
