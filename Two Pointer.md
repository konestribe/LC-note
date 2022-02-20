# Two Pointer
Two pointer usually used for greedy questions. There are couple different scenarios, but all of them require some "stack-like" solutions, discarding previous completed state by manipulating pointers and moving forward.

## Moving pointers from both side
This kinds of question usually asks max/min area covered by index combination. We should be utilizing some conditions to discard some part of area scanned by 1 pointer until both of them reach.
### 11. Container With Most Water
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

## Moving pointers from one side
Normally we use both pointer to scan array twice, and utilize slow pointer to track intermediate state
### 2062. Count Vowel Substrings of a String
 - practise: [link](https://leetcode.com/problems/count-vowel-substrings-of-a-string/)
 - Solution
   - We need to count number of continuous substrings contain all vowels in a string. For example: "cuaieuouac", we will have `uaieuo`, `uaieuou`, `ieuoua`, etc
   - A straightforward solution would be using a map tracking existence of vowels, then for each starting index, we search a substring only containing vowels, and if it contains all vowels, we update return value
   - Above solution is O(n^2). However, this solution is not utilizing two pointers, and similar to brute force. 
   - One step further, we can know that if we find a substring containing all vowels, any prefix/suffix with any vowels will made up solution. That is being said, we can introduce another pointer k to track the last index, before which [k, fastPointer] will form up a valid candidate
   ```java
   public int countVowelSubstrings(String word) {
        Set<Character> set = new HashSet<>(){{add('a');add('e');add('i');add('o');add('u');}};
        int st = 0;
        int ed = 0;
        int k = 0;
        int ret = 0;
        Map<Character, Integer> map = new HashMap<>();
        while(ed < word.length()) {
            char cur = word.charAt(ed);
            if(set.contains(cur)) {
                map.put(cur, map.getOrDefault(cur, 0) + 1);
                while(map.size() == 5) {
                    char rmv = word.charAt(k++);
                    map.put(rmv, map.get(rmv) - 1);
                    if(map.get(rmv) <= 0) map.remove(rmv);
                }
                ret += k - st;
                ed++;
            } else{
                ed++;
                st = ed;
                k = ed;
                map.clear();
            }
        }
        return ret;
    }
   ```
