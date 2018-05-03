# Algorithm-questions
Solution and discussion of algorithm questions
# Category Table
[Array](#chapter-1)

## Array<a id="chapter-1"></a>
[Container With Most Water](#chapter-1-question-1)

[3Sum Closest](#chapter-1-question-2)

[4Sum](#chapter-1-question-3)
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
### 4Sum<a id="chapter-1-question3"></a>
#### Problem Description:
Given an array nums of n integers and an integer target, are there elements a, b, c, and d in nums such that a + b + c + d = target? Find all unique quadruplets in the array which gives the sum of target.

Note:

The solution set must not contain duplicate quadruplets.

#### Discussion:
This question is similar to [3Sum Closest](#chapter-1-question-3)
Sort the array. And use two pointers, pointer1, pointer2, to iterate the array, and use two pointers, left, right, that start from pointer2 and a.length - 1 to make the sum move around target.

Time complexity is O(n^3). Brute force solution's time complexity is O(n^4);

There is a requirement should be highly noticed. "The solution set must not contain duplicate quadruplets." Therefore, in iterations of pointer1, pointer2 and left, the number of pointer must not equal to the number of pointer - 1, except the first one. 
#### Solution:
```
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
```
