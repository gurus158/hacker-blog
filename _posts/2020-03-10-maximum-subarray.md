---
published: true
---
## Hi Coders,

Today I will talk about a very simple but good programming problem which is also very famous among coding interviews.

**Description**
Given an integer array nums, find the contiguous subarray (containing at least one number) which has the largest sum and return its sum.

**Example:**

Input: -2,1,-3,4,-1,2,1,-5,4

Output: 6

Explanation: 4,-1,2,1 has the largest sum = 6.

We have an Integer array(-ve included) and our task is to find a subarray which has maximum sum.

Before reading more, you might want to solve this by yourself first.

There can be many solution to this problem. Below are two of them.

### NAIVE

Simplest Approach will be finding all subarray and calculate their sum and find maximum among those. But only problem is this approach is slow.

Say we have array of 10 elements. (1,2,3,4,5,6,7,8,9,10) . How many subarrays are there??

Well the number will be n*(n+1)/2 , where n is number of element in arrays. So finding all these and calculate their sum will require atleast N*(N+1)/2 operations and hence time complexity of this will be O(n^2) 

So naive approach is not the one we should go with.

### O(n) approach

Fortunately, We can solve this problem in linear time i.e O(n).

The idea is to iterate through the array and track two values current_max and max_sofar 

initaily current_max and max_sofar both are equal to first element of array.

**in each iteration,** 

* update **current_max** equal to maximum of **current_max + current_element** and **current_element**. The idea is if we found a element which is greater than current sum (including it) we should discard the previous values and start from this elemnent.

* update max_so_far

That's it return max_so_far at the end.

### C++ code will be :

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums){
    int c_max=nums[0];
    int max_so_far = nums[0];
    for(int i = 1;i<nums.size();i++){
      c_max = max(c_max+nums[i],nums[i]);
      max_so_far = max(max_so_far,c_max);
    }

    return max_so_far;
  }
};
```
I like this problem... 

hope you find this helpful.


