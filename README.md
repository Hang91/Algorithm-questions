# Algorithm-questions
Solution and discussion of algorithm questions
# Category Table
[Array](#chapter-1)

## Array<a id="chapter-1"></a>
[Container With Most Water](#chapter-1-question-1)

[3Sum Closest](#chapter-1-question-2)

[4Sum](#chapter-1-question-3)

[Search Insert Position](#chapter-1-question-4)

[Find Missing Positive](#chapter-1-question-5)
### Container With Most Water<a id="chapter-1-question-1"></a>
#### Problme Description:
Given n non-negative integers a1, a2, ..., an, where each represents a point at coordinate (i, ai). n vertical lines are drawn such that the two endpoints of line i is at (i, ai) and (i, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

Note: You may not slant the container and n is at least 2.
#### Discussion:
At first glance, we can get a brute force solution in which we will iterate all pairs of lines and keep updating the the maximum area. Apparently, this solution costs O(n^2) time.

Can we do better?

Think about this. Suppose we select the most left line and the most right line at the beginning, the area is Min(height_left, height_right) * (right - left), which depeneds on the length between two lines and the minimum height of two lines. Next, we need to figure out how to select next pair of lines to make sure they can contain more water. Because we have selected the most left and most right line, (right - left) is maximum. We need to move the line with lower height to the other line to get a bigger volume. 

+ Step1, select 0 as left and a.length - 1 as right 
+ Step2, calcute area
+ Step3, update maximum area if the result of Step2 is greater
+ Step4, move the line with lower height to the other line until we find a line with height higher than the lower line.
+ Step5, repeat Step2 to Step4 until left = right

This solution only costs O(n) time, since left move from 0 to right and right move from a.length - 1 to left; 

Provement:
In the solution, we tried every possible pair of lines whose area might be greater than last pair.
I don't have a mathmatical provement. If anybody get one, please tell me.

#### Solution:
```
    public int maxArea(int[] height) {
        int left = 0, right = height.length - 1;
        int result = 0;
        while (left < right) {
            int current_area = Math.min(height[left], height[right]) * (right - left);
            result = Math.max(result, current_area);
            if (height[left] <= height[right]) {
                int current_height = height[left];
                while (left < right && current_height >= height[left]) {
                    left ++;
                }
            } else {
                int current_height = height[right];
                while (left < right && current_height >= height[right]) {
                    right --;
                }
            }
        }
        return result;
    }
```
### 3Sum Closest<a id="chapter-1-question-2"></a>
#### Problme Description:
Given an array nums of n integers and an integer target, find three integers in nums such that the sum is closest to target. Return the sum of the three integers. You may assume that each input would have exactly one solution.

#### Discussion:
Brute force solution is traverse all the groups of 3 numbers and find the closest one. The time complexity is O(n^3);

Better solution?

Sometimes, we can find more solution in a sorted array. So, we shuold try to sort the array first. 

After soring, we will use three pointers. The pointer1 will traverse the array. The pointer2 start at pointer1 + 1 to avoid duplicated computation, the pointer3 start at index a.length - 1. Then we compare the sum of three pointed numbers with target. What should we do next if the sum is greater of less than the target?

Since the array is sorted, if the sum is greater than target, we cannot move pointer2 to right to get a closer sum. Vice versa. Therefore, we should move pointer3 to left if sum is greater than target, and we shuold move pointer2 to right if sum is less than target. If sum is equal to target? Return the target!

#### Solution:
```
    public int threeSumClosest(int[] nums, int target) {
        if (nums == null || nums.length < 3) {
            throw new NoSuchElementException();
        }
        Arrays.sort(nums);
        int result = nums[0] + nums[1] + nums[2];
        for (int i = 0; i < nums.length; i++) {
            int left = i + 1;
            int right = nums.length - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                int diff = Math.abs(sum - target);
                if (diff < Math.abs(result - target)) {
                    result = sum;
                }
                if (sum > target) {
                    right --;
                } else if (sum < target) {
                    left ++;
                } else {
                    return target;
                }
            }
        }
        return result;
    }
```
### 4Sum<a id="chapter-1-question-3"></a>
#### Problem Description:
Given an array nums of n integers and an integer target, are there elements a, b, c, and d in nums such that a + b + c + d = target? Find all unique quadruplets in the array which gives the sum of target.

Note:

The solution set must not contain duplicate quadruplets.

#### Discussion:
This question is similar to [3Sum Closest](#chapter-1-question-2). 
Sort the array. And use two pointers, pointer1, pointer2, to iterate the array, and use two pointers, left, right, which start from pointer2 and a.length - 1 to make the sum move around target.

Time complexity is O(n^3). Brute force solution's time complexity is O(n^4);

There is a requirement should be highly noticed. "The solution set must not contain duplicate quadruplets." Therefore, in iterations of pointer1, pointer2 and left, the number pointed by pointer must not equal to the number pointed by pointer - 1, except the first one. 
#### Solution:
```
    public List<List<Integer>> fourSum(int[] nums, int target) {
        Arrays.sort(nums);
        List<List<Integer>> result = new ArrayList<>();
        for (int i = 0; i < nums.length; i++) {
            if (i != 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            for (int j = i + 1; j < nums.length; j++) {
                if (j != i + 1 && nums[j] == nums[j - 1]) {
                    continue;
                }
                int left = j + 1, right = nums.length - 1;
                while (left < right) {
                    if (left != j + 1 && nums[left] == nums[left - 1]) {
                        left ++;
                        continue;
                    } 
                    int sum = nums[i] + nums[j] + nums[left] + nums[right];
                    if (sum > target) {
                        right --;
                    } else if (sum < target) {
                        left ++;
                    } else {
                        List<Integer> subResult = new ArrayList<>();
                        subResult.add(nums[i]);
                        subResult.add(nums[j]);
                        subResult.add(nums[left]);
                        subResult.add(nums[right]);
                        result.add(subResult);
                        left ++;
                        right --;
                    }
                }
            }
        }
        return result;
    }
```
### Search Insert Position<a id="chapter-1-question-4"></a>
#### Problem Description:
Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.
#### Discussion
Brute force solution is continuously traverse the array, until we find the target or the first number that is greater than target. Then return the position.

The time complexity is O(n).

Since the input array is sorted. So we can try to use binary search to get a better solution with time complexity O(logn). Now, I want to discuss the stop condition of binary search. 

If the target is not in the array, we need to find a index, where the num[index] is less than target and num[index + 1] is greater than target. So, I want to use two pointers left and right. left start from 0, right start from a.length - 1. The stop condition of binary search is left < right - 1. If we can find the target before the stop condition, we will return the index of target in the while loop. If we cannot find the target before stop condition, we should check if a[left] and a[right] is equal to target. If they are not target, we will check which one is the first num that greater than the target and return its index.

#### Solution
```
    public int searchInsert(int[] nums, int target) {
        int left = 0, right = nums.length - 1;
        while (left < right - 1) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == target) {
                return mid;
            } else if (nums[mid] > target) {
                right = mid - 1;
            } else {
                left = mid + 1;
            }
        }
        if (nums[left] == target) {
            return left;
        }
        if (nums[right] == target) {
            return right;
        }
        if (nums[left] > target) {
            return left;
        }
        if (nums[right] > target) {
            return right;
        }
        return right + 1;
    }
```
### Find Missing Positve<a id="chapter-1-question-5"></a>
#### Problem Description:
Given an unsorted integer array, find the smallest missing positive integer.
Input: [1,2,0]
Output: 3
Input: [7,8,9,11,12]
Output: 1
#### Discussion:
Brute force solution will cost O(n^2) time. 

Sort the array and check from the start of the array will cost O(nlogn) time. 

Put all the numbers that are greater than 0 into a HaseSet and keep count how many numbers that are greater than 0. Then check if numbers from 1 to the count result are in the HashSet. Return the first number that is not in the HashSet. If all the numbers are in the hashSet, return the count result + 1. This solution cost O(n) time and O(n) space.

Can we solve it in O(n) time and O(1) space?

Bing!

We can continuelly swap nums[i] with nums[nums[i] - 1] until nums[i] = i + 1 and check if nums[i] = i + 1 from the begining of the array. Return the first one that is not equal.

Consider about what will happen if the input includes duplicate number?

For example, if the input is [1, 1]. When we check number at index 1, it is not equal to 2. Then we swap it with number at index 0. It is still 1. Then we come into a endless loop. So, we should add an additional stop condition to avoid this bug. Check if nums[nums[i] - 1] is equal to nums[i]. 

In addition, if nums[i] < 0 or nums[i] > nums.length, then it can not be swap with other numbers.

#### Solution:
```
    public int firstMissingPositive(int[] nums) {
        for (int i = 0; i < nums.length; i++) {
            while (nums[i] != i + 1 && nums[i] > 0 && nums[i] <= nums.length) {
                if (nums[nums[i] - 1] == nums[i]) {
                    break;
                }
                int temp = nums[nums[i] - 1];
                nums[nums[i] - 1] = nums[i];
                nums[i] = temp;
            }
        }
        
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] != i + 1) {
                return i + 1;
            }
        }
        return nums.length + 1;
    }
```
