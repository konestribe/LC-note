# Two Pointer
Two pointer usually used for greedy questions. There are couple different scenarios, but all of them require some "stack-like" solutions, discarding previous completed state by manipulating pointers and moving forward.

## Moving pointers from both side
This kinds of question usually asks max/min area covered by index combination. We should be utilizing some conditions to discard some part of area scanned by 1 pointer until both of them reach.
###
11. Container With Most Water
 - practise: [link](https://leetcode.com/problems/container-with-most-water/)
 - Solution
   - Given a list of height, we should return largest area covered by lines. The idea would be starting from both side, and calculating area. Meanwhile, we need to keep large height and moving the other one to be inner height. This is due to the greedy condition: If two points with smaller distance, they must need to have larger height to generate a large area. As a result, we only moving smaller height hoping to find a larger inner height
   ```java
       public int maxArea(int[] height) {
        int l = 0, r = height.length-1;
        int ret = 0;
        while(l < r) {
            ret = Math.max(ret, (r-l)*Math.min(height[l], height[r]));
            if(height[l] > height[r]) {
                r--;
            }else{
                l++;
            }
        }
        return ret;
    }
   ```
