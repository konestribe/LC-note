# Greedy

We focus on sub optimization, which contribute to global optimization.
Unlike DP, which is to track possible intermediate transitions, greedy can only have 1 intermediate status, for which is optimized

---------------
## [Array slicing window]: Tracking the maximum limit
### LC 763. Partition Labels
 - practise: [link](https://leetcode.com/problems/partition-labels/)
 -  Solution 1:
  -  We know if we reach the last occured index of a char, we used up all of this char and we could potentially split the string. However, how can we make sure all the other chars visited are also used up?
  -  We guarantee this condition by tracking all of their max end point. This is not quite straightforward, see below example:
    - For string `abbabc`, when we first visit a, we know it's last index is 3, which means we at least need to move to 3 to split the array.
    - When we move to second char b, we know we need to move to it's last index 4 to split array, as Max(3, 4) = 4;
    - Until we reach to that max index, we can split the array and guarantee this max index cover all previous char's max index
  ```java
  public List<Integer> partitionLabels(String s) {
        int[] last = new int[26];
        for(int i = 0; i < s.length(); i++) {
            char cur = s.charAt(i);
            last[cur - 'a'] = i;
        }
        int st = 0;
        int ed = 0;
        List<Integer> list = new ArrayList<>();
        for(int i = 0; i < s.length(); i++) {
            ed = Math.max(ed, last[s.charAt(i) - 'a']);
            if(ed == i) {
                list.add(ed - st + 1);
                st = i+1;
            }
        }
        return list;
    }
  ```
### LC 55 jump game
 - practise: [link](https://leetcode.com/problems/jump-game/)
 - Solution:
   - If for `d[i] = j`, we know for index range [i, i+j], we can all visit. Thus, we just need to continue updating the max limit. Until we reach to an index which `d[i] = i`, we know this is the maximum range that we can reach.
   ```java
       public boolean canJump(int[] nums) {
        int max = 0;
        for(int i = 0; i < nums.length; i++) {
            max = Math.max(max, i + nums[i]);
            if(max == i) break;
        }
        return max >= nums.length-1;
    }
   ```
### LC 45 jump game 2
  - practise: [link](https://leetcode.com/problems/jump-game-ii/)
  - Solution:
    - This time instead for asking whether we can reach, we should compute minimal steps
    - Still, based on moving max, we know from a certain index, we can reach `[i. i+num[i]]` values. Those are the possible landing point for our next jump.
    - Meahwhile, for each point within above range, we can track a furthest index a next jump can reach. In that case we can guarantee/define the scope for next jump.
    - The greedy constrain is for `[i, i+num[i]]` and its `max(b+b[i])`, b within `[i, i+num[i]]`, no matter which landing point we choose (between i and i + num[i]), in next jump we can always reach to at most b+b[i] index.
   ```java
       public int jump(int[] nums) {
        int end = 0; 
        int curMax = 0;
        int jump = 0;
        for(int i = 0; i < nums.length-1; i++) {
            curMax = Math.max(curMax, i + nums[i]);
            if(end == i) { // indicating we evaluated this jump's all possible intermediate step
                jump++;
                end = curMax;
            }
        }
        return jump;
    }
   ```




-------------------------------------------------------

## Array slicing window
### LC 763. Partition Labels
 - practise: [link](https://leetcode.com/problems/partition-labels/)
 - Solution 1:
   - We know if we running out of one char, if all visited char has been used, then we can partition all previous visited elements
   - We track count of a char, and subtract it when slicing the window. We add visited char to a set
   - If one char has been used up, if set is empty, we know all previous existed char has been used up. This is the greedy condition for this question
   ```java
   public List<Integer> partitionLabels(String s) {
        int[] cnt = new int[26]; //count of letter
        for(int i = 0; i < s.length(); i++) {
            cnt[s.charAt(i) - 'a'] ++;
        }
        
        List<Integer> list = new ArrayList<>();
        int st = 0;
        Set<Character> visited = new HashSet<>();
        for(int i =0; i < s.length(); i++) {
            char cur = s.charAt(i);
            if(--cnt[cur - 'a'] == 0) {
                visited.remove(cur);
            } else{
                visited.add(cur);
            }
            if(visited.isEmpty()) {
                list.add(i - st + 1);
                st = i+1;
            }
        }
        return list;
    }
   ```
 -------------------------------------------------------
 
 ## Using stack to track previous optmized greedy condition
 Stack often being used together with greedy, as last-in-first-out property can be utilized to store intermediate optimized condition. By stacking intermediate optimized state, we can stack up to final optimized solution
 ### 402. Remove K Digits
  - Practise: [link](https://leetcode.com/problems/remove-k-digits/)
  - Solution:
    -  By removing k digits, making the remaining integer max. The greedy condition would be always removing large integers from beginning. For instance, by looking `1,2,3,4,2`, we know 4 should be removed to make integer optimized. Thus a stack storing increasing digits would be the greedy condition.
    -  Remember to handle corner cases such as string with all `0`.
    ```java
        public String removeKdigits(String num, int k) {
        // from left to right, if find peak, remove it
        Stack<Character> stack = new Stack<>();
        int pt = 0;
        while(pt < num.length()) {
            char cur = num.charAt(pt++);
            while(!stack.isEmpty() && cur < stack.peek() && k > 0) {
                stack.pop();
                k--;
            }
            stack.push(cur);
        }
        StringBuilder sb = new StringBuilder();
        while(!stack.isEmpty()) {
            char cur = stack.pop();
            if(k > 0) {                
                k--;
            } else{
                sb.append(cur);
            }
        }

        int j = sb.length()-1;
        while(j >= 0) {
            if(sb.charAt(j) != '0') {
                sb.setLength(j+1);
                break;
            }
            j--;
        }
        if(j < 0) return "0";
        return sb.reverse().toString();
    }
    ```
 

