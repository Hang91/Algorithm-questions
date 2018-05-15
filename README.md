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

[Jump Game](#chapter-1-question-6)

[Median of Two Sorted Array](#chapter-1-question-7)

[Remove Duplicates from Sorted Array](#chapter-1-question-8)

[Remove Element](#chapter-1-question-9)

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

### Jump Game<a id="chapter-1-question-6"></a>
#### Problem Description
Given an array of non-negative integers, you are initially positioned at the first index of the array.
Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.
#### Discussion
An intuitive solution is that build a boolean array, and set the position to true accroding to the number stored in the input array. If the value in the boolean array is false, then stop setting, since this cell in the array cannot be reached.

Time cost is O(n), space cost is O(n).

#### Solution
```
    public boolean canJump(int[] nums) {
        if (nums.length == 0) {
            return true;
        }
        int len = nums.length;
        boolean[] arr = new boolean[len];
        int farthest = 0;
        arr[0] = true;
        for (int i = 0; i < len; i++) {
            if (!arr[i]) {
                break;
            }
            for (int j = farthest + 1; j < arr.length && j <= i + nums[i]; j++) {
                arr[j] = true;
            }
            farthest = Math.max(farthest, i + nums[i]);
        }
        return arr[len - 1];
    }
```
### Jump Game II
#### Problem Description
Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Your goal is to reach the last index in the minimum number of jumps.

You can assume that you can always reach the last index.
#### Discussion
I want to create a new array with same length of the input array and fill with the value of Integer.MAX_VALUE. Then, traverse the input array from 0. When I reach to an index, I can update cells from the index to index + number at the index. If the number in the new array of a updating cell is greater than number in the new array of the index + 1, it will be changed to the number + 1.

The worst case time complexity is O(n^2).

We can optimze the solution by keeping a farthest value that is the greatest index has been updated. If a left cell update a cell, and a right cell want to update the same cell, the left cell's value must be greater than the right cell, which is impossible. Because the left cell can be updated by the same cell which will update right cell and make them have the same value. The value on the left must be less than or equal to the value on the right.

Therefore, we can check the index from the farthest to the index + number, then the time complexity will be O(n).

#### Solution
```
    public int jump(int[] nums) {
        int len = nums.length;
        int[] table = new int[len];
        Arrays.fill(table, Integer.MAX_VALUE);
        table[0] = 0;
        int farthest = 1;
        for (int i = 0; i < len; i++) {
            for (int j = farthest; j <= nums[i] + i && j < len; j++) {
                if (table[j] > table[i] + 1) {
                    table[j] = table[i] + 1;
                } 
            }
            farthest = Math.max(farthest, i + nums[i]);
        }
        return table[len - 1];
    }
```
### Median of Two Sorted Array<a id="chapter-1-question-7"></a>
#### Problem Description
There are two sorted arrays nums1 and nums2 of size m and n respectively.

Find the median of the two sorted arrays. The overall run time complexity should be O(log (m+n)).

#### Discussion
A brute force idea is that merge two arrays into an array and get the median. The time complexity is O(n) and space complexity is O(n + m).

The second solution is using two pointers. Move two pointers from the start of two array, move the pointer which is pointing the little number. Move pointers (m + n) / 2 - 1 times, then if the total length is even, the median is (nums[pointer1] + nums[pointer2]) / 2, if the total length is odd, the median is the greater number pointed by pointers. The time complexity is O(n).

However, the requirement for time complexity is O(log (m + n)). Because two arraies are sorted, I can try to use binary search to solve the problem. 

Since if the total length is even, I need to find the (m + n) / 2 th number and (m + n) / 2 + 1 th number. And if the total number is odd, I need to find the (m + n) / 2 + 1 th number. For exampe, if the total length is 5, I need to find 3rd number, if the total length is 6, I need to find the 3rd and 4th number.

Then the question is change to find the Kth number in two sorted array. 

Use binary search in finding the Kth number in two sorted array. 

Recusively check the number at start1 + k / 2 and start2 + k / 2 in two arrays. If one number is less than or equal to the other, the kth number cannot be in the part of the array whose start + k / 2 number is less  and index is less than or equal to k / 2. Then try to find the k - k / 2 th number from start + k - k / 2 to end of one array whose k / 2 th number is less and start to end of the other array. Until k is 1, return the less number of array1[start1] and array2[start2].

+ intialize: start1 = 0, start2 = 0
+ step1: if (num1[start1 + k / 2] <= num2[start2 + k / 2]), start1 += k / 2, else start2 += k / 2.
+ step2: repeat step1 until k = 1.
+ step3: return the Min(num1[start1], num2[start2])

Please notice that the greatest number of one array can be less than the median. Thus, it's necessary to check if the start index is less the length of the array. Besides, if k / 2 is greater than the length of one array, the k th number can not be in the start to start + k / 2 of the other array. Since even if all the numbers in the shorter array is less than the first number in the other array, the number at start + k / 2 of the longer array cannot be the k th number because there is not enough numbers before it. 

#### Solution
```
    public double findMedianSortedArrays(int[] nums1, int[] nums2) {
        int m = nums1.length, n = nums2.length;
        int k = (m + n) / 2 + 1;
        if ((m + n) % 2 == 0) {
            return (getKth(nums1, nums2, 0, 0, k - 1) + getKth(nums1, nums2, 0, 0, k)) / 2.0;
        }
        return getKth(nums1, nums2, 0, 0, k);
    }
    
    public int getKth(int[] nums1, int[] nums2, int start1, int start2, int k) {
        if (k == 1) {
            if (start1 >= nums1.length) {
                return nums2[start2];
            } else if (start2 >= nums2.length) {
                return nums1[start1];
            }
            return Math.min(nums1[start1], nums2[start2]);
        }
        if (start1 + k / 2 > nums1.length) {
            return getKth(nums1, nums2, start1, start2 + k / 2, k - k / 2);
        }
        if (start2 + k / 2 > nums2.length) {
            return getKth(nums1, nums2, start1 + k / 2, start2, k - k / 2);
        }
        if (nums1[start1 + k / 2 - 1] <= nums2[start2 + k / 2 - 1]) {
            return getKth(nums1, nums2, start1 + k / 2, start2, k - k / 2);
        } else {
            return getKth(nums1, nums2, start1, start2 + k / 2, k - k / 2);
        }
    }
```
 
### Remove Duplicates from Sorted Array<a id="chapter-1-question-8"></a>
#### Problem Description
 Given a sorted array nums, remove the duplicates in-place such that each element appear only once and return the new length.

Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.

#### Discussion
Traverse the array, and keep a variable to store the last different value and a variable to store the numbers of differnet value. If current number is equal to the last different value, continue traversingr. If current number is not equal to the last different vale, update the last different vale to current number and the number of different value plus 1. 

#### Solution
```
    public int removeDuplicates(int[] nums) {
        if (nums.length == 0) {
            return 0;
        }
        int lastDiff = nums[0];
        int diffNums = 1;
        for (int i = 1; i < nums.length; i++) {
            if (lastDiff != nums[i]) {
                lastDiff = nums[i];
                nums[diffNums] = nums[i];
                diffNums ++;
            }
        }
        return diffNums;
    }
```
### Remove Element<a id="chapter-1-question-9"></a>
#### Problem Description

Given an array nums and a value val, remove all instances of that value in-place and return the new length.

Do not allocate extra space for another array, you must do this by modifying the input array in-place with O(1) extra memory.

The order of elements can be changed. It doesn't matter what you leave beyond the new length.
#### Discussion
I want to use two pointers. Left pointer start from 0, and stop at the first number that equals to val. Right pointer start from nums.length - 1, and stop at the first number that does not equal to val. Then swap these two numbers. Keep moving left pointer and right pointer until left equals to right. Then the most left number in the array that equals to val is at the left pointer. Then return left, which is the numbers of number that does not equal to 0. 

There is a corner case. If there is no number equal to val. Then left pointer is equal to nums.length - 1, which is not the result. To deel with the corner case, I should judge if nums[left] == val. If not, return left - 1, if true, return left.

#### Solution
```
    public int removeElement(int[] nums, int val) {
        if (nums.length == 0) {
            return 0;
        }
        int left = 0, right = nums.length - 1;
        while (left < right) {
            while (left < right && nums[left] != val) {
                left ++;
            }
            while (left < right && nums[right] == val) {
                right --;
            }
            if (left < right) {
                int temp = nums[left];
                nums[left] = nums[right];
                nums[right] = temp;
                left ++;
            }
        }
        if (nums[left] == val) {
            return left;
        }
        return left + 1;
    }
```

