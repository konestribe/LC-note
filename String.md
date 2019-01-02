
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
