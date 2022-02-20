# String

## non-continous substring with optimized condition
If question is not asking for contiguous substring, when it comes to optimized conditions, we can think about below data structures
 - Stack: stack can hold intermediate results **in sequence** based on certain criteria. 
### LC Smallest Subsequence of Distinct Characters
 - Practise: [link](https://leetcode.com/problems/smallest-subsequence-of-distinct-characters/)
 - Solutions:
   - Given a string s, return the lexicographically smallest subsequence of s that contains all the distinct characters of s exactly once. For example, "cbacdcbc" -> "acdb"
   - We know there are couple hard criteria we need to apply for characters
     - If we figure out we are inserting a char after a larger char, and if larger char have further locations in the string, we can discard previous one, as according to greedy condition, this will always be true
     - If this is the last occurrence for this char, we should anyway keep it in the string
   - Based on above two facts, we can use stack to hold intermediate results
   ```java
   public String smallestSubsequence(String text) {
        Stack<Character> stack = new Stack<>();
        int[] carray = new int[26];
        boolean[] visited = new boolean[26];
        for(char c: text.toCharArray()) {
            carray[c-'a']++;
        }
        for(char c: text.toCharArray()) {
            carray[c-'a']--;
            if(visited[c-'a']) continue;
            while(!stack.isEmpty() && stack.peek() > c && carray[stack.peek()-'a'] > 0) {
                visited[stack.pop()-'a'] = false;
            }
            stack.push(c);
            visited[c-'a'] = true;
        }
        StringBuilder sb = new StringBuilder();
        while(!stack.isEmpty()) sb.append(stack.pop());
        return sb.reverse().toString();
    }
   ```




<Below are notes before 2022>
---------------------------------------------------------------------------------------------------------------------
### 1. palidrome
- practise: LC5(lps)
- scenario:
  - check if palidrome substring:
    - methods 1: 
      - fix center, spread to both side. remember to check for both even and odd
      - O(n)
    - methods 2: 
      - get substring, check if reverse is the same.
      - O(n) if we already get substring (reverse O(n), check equal O(n))
      
      
      
 ### 2. substring match
 
 - scenario 1: substring concatenated with all words in a dictionary
  - practise: LC30, LC97
    - dfs with memo
 - scenario 2: recursive match, can use cache to speed up
  - practise: LC87
  - 
 - scenario 3: substring match within a parent string:
  - kmp algorithms
    - create index array for mapping
    - go through origin string to execute pattern match. Time complexity O(m+n) instead of O(m*n)
    ```
    int ps = 0, pp = 0;
    while(ps < s.length()) {
        if(s.charAt(ps) == pattern.charAt(pp)) {
            ps++;
            pp++;
            if(pp == pattern.length()) return ps-pattern.length();
        }else if(pp == 0) {
            ps++;
        }else{
            pp = kmp[pp-1];
        }
    }
    ```
    ```
    int[] getKMP(String s) {
        int[] ret = new int[s.length()];
        int i = 0, j = 1;
        while(j < s.length()) {
            if(s.charAt(i) == s.charAt(j)) {
                ret[j++] = ++i;
            }else{
                if(i == 0) j++;
                else{
                    i = ret[i-1];
                }
            }
        }
        return ret;
    }
    ```
 
 
 ### 3. offset match
 
 - scenario 1: we can shift string to make them alike, keep in mind that 'ba' and 'az' are actually the same.
 
 
 ### 4. alphabetic order
 
 - scenario 1: delete string columns to make them sorted
 - practise: 944, 955
  - greedy search, we only need to check for strings that are not sorted (for new columns) 




#### optimization
  - using trie to substitute set
  - usually combined with dfs, provide early termination properties.
