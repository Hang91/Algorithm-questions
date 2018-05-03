# Algorithm-questions
Solution and discussion of algorithm questions
# Category Table
[Array](#chapter-1)

## Array<a id="chapter-1"></a>
[Container With Most Water](#chapter-1-question-1)
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
                    right ++;
                }
            }
        }
        return result;
    }
```
