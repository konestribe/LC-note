
# 1 String transformation with unique char
If we have constrains that each char only occur once, we can use bit set to hash string and record their status. For string transformations, we should consider using set and by manipulating original string, we check if transformation target exists
## 1.1 Count Words Obtained After Adding a Letter
 - Practise: [link](https://leetcode.com/problems/count-words-obtained-after-adding-a-letter/)
 - Similar question: [link](https://leetcode.com/problems/maximum-length-of-a-concatenated-string-with-unique-characters/)
 ```java
 public int wordCount(String[] startWords, String[] targetWords) {
        Map<Integer, Set<Integer>> map = new HashMap<>();
        int ret = 0;
        //construct startWords
        for(String s: startWords) {
            map.computeIfAbsent(s.length(), x -> new HashSet<>());
            map.get(s.length()).add(getBinary(s));
        }
        
        for(String s: targetWords) {
            if(!map.containsKey(s.length()-1)) continue;
            Set<Integer> targetSet = map.get(s.length()-1);
            int val = getBinary(s);
            for(int i = 0; i < 26; i++) {
                if( ((val >> i) & 1) == 0) continue;
                // int updatedVal = val
                if(targetSet.contains(val ^ (1 << i))) {
                    ret++;
                    break;
                }
            }
        }
        return ret;
    }
    
    private int getBinary(String s) {
        int ret = 0;
        for(char c: s.toCharArray()) {
            ret |= 1 << (c-'a');
        }
        return ret;
    }
 ```
-----------------------------------------------------------------------------

# 2. Longest Increasing substring
Finding longest increasing substring. Intuitively, we can O(n^2) with dp to search longest substring length for each index. However, there is another O(nlogn) solution by memorizing the substring length with tail. The tail string is proved to be increasing
 - Practise" [link](https://leetcode.com/problems/longest-increasing-subsequence/)
 - Solution:
 ```java
     public int lengthOfLIS(int[] nums) {
        // An array called tail, index represents sub seq length, value indicates the smallest tail for subarray with this length
        int size = 0;
        int[] tail = new int[nums.length];
        for(int num: nums) {
            int pt = Arrays.binarySearch(tail, 0, size, num);
            if (pt < 0) {
                int insertion = -1 * pt - 1;
                if(insertion >= size) {
                    tail[insertion] = num;
                    size ++;
                } else{
                    tail[insertion] = num;
                }
            }
            // print(tail);
        }
        return size;
    }
 ```

-----------------------------------------------------------------------------
# 3. non-continous substring with optimized condition
If question is not asking for contiguous substring, when it comes to optimized conditions, we can think about below data structures
 - Stack: stack can hold intermediate results **in sequence** based on certain criteria. 
## 3.1 LC Smallest Subsequence of Distinct Characters
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
-----------------------------------------------------------------------------
# 4. Palidrome
Strings with below properties:
 1. Strings are symmetrical from both side
 2. Strings that can be split into char pairs with mostly even count, with at most 1 pair to be odd
 3. Strings with middle element leftalone if odd, or no middle element if even
## 4.1 Constructing palidrome without array order
If we are asked to construct palidrome without string order, we should consider tracking their pairs(**Based on criteria 2**). Left-alone char should be classified to a single group, while pair of char could be 1 or 2 group. As no order is required, we can utilize `int[26]` to count occurence
### 4.1.1 Construct K Palindrome Strings
 - [link](https://leetcode.com/problems/construct-k-palindrome-strings/)
 ```java
     public boolean canConstruct(String s, int k) {
        int[] arr = new int[26];
        for(char c: s.toCharArray()) {
            arr[c - 'a'] ++;
        }
        int odd = 0;
        int pair = 0;
        for(int i = 0; i < 26; i++) {
            if(arr[i] % 2 == 1) {
                odd++;
                pair += arr[i]/2;
            }
        }
        return k >= odd && k <= s.length();
    }
 ```
 ## 4.2 Constructing palidrome with array order
 If we need to consider ordered substring, only counting occurrence will not be sufficient. Instead, we should consider **property 1**, also consider checking its first and last index, to narrow the search range. Meanwhile, as we has fixed number of alpha numbers, the time complexity multiplier would be O(26) == O(1), which will contribute to a O(n) solution
### 4.2.1 Unique Length-3 Palindromic Subsequences
 - [link](https://leetcode.com/problems/unique-length-3-palindromic-subsequences/)
 - We are asked to construct palidrome with length of 3 from a string. We cannot modify char sequence but can choose non-continuous char: `For example, "ace" is a subsequence of "abcde".`
 - Solution
   - We track first and last index for each char.
   - Then for each char, we potentially loop the string, check uniq char in between, and contributing to final answer.
### 4.2.2 Split Two Strings to Make Palindrome
 - [link](https://leetcode.com/problems/split-two-strings-to-make-palindrome/)
 - Solution
   - For two strings with same length, we need to take an index and construct `a(prefix)+b(suffix)` or `b(prefix)+a(suffix)`. return true if we can construct a palidrome
   - Trivial solution would be taking each index, scan both string and check if it's a palidrome. This will make an O(n^2) solution
   - One level deeper, by considering **Property 1**, if string can be palidrome, then there should be certain length from a's prefix and from b's suffix to be the same. 
   - Based on this criteria, we can traverse the rest of the part if necessary to decide if this is a palidrome.
   ```java
    public boolean checkPalindromeFormation(String a, String b) {
        return checkMatch(a, b) || checkMatch(b, a);
    }
    
    private boolean checkMatch(String a, String b) {
        int n = a.length();
        int pt = 0;
        while(pt < n) {
            if(pt > n/2 - 1) return true;
            if(a.charAt(pt) == b.charAt(n-1-pt)) {
                pt++;
                continue;
            }
            break;
        }
        int ed = n - 1 - pt;
        return isPalidrome(a, pt, ed) || isPalidrome(b, pt, ed);
    }
    
    private boolean isPalidrome(String s, int st, int ed) {
        while(st < ed) {
            if(s.charAt(st++) == s.charAt(ed--)) continue;
            return false;
        }
        return true;
    }
   ```
---------------------------------------------------------------------------------------------------------------------
## 5. Substring match
We will be asking if there are any string match can be made from a candidate list. There will be couple different thoughts
 1. Replacing each bit with 26 options (if all lower case)
   - This is considered O(26) constant, as we check possibility of each bit at most 26 times
 2. We evaluate bit by bit, check if pattern string exists in set after removing the bit
   - We don't really care what is the diff between each string. By neglecting the bit and evaluating the all string, we will find potential match from set.
### 5.1 1554. Strings Differ by One Character
 - [link](https://leetcode.com/problems/strings-differ-by-one-character/)
 - Solution
   - Using method 2, which is neglecting bit by bit and check string from set
   ```java
   public boolean differByOne(String[] dict) {
        Set<String> set = new HashSet<>();
        int len = dict[0].length();

        for(int i = 0; i < len; i++) {
            set.clear();
            for(String s: dict) {
                char[] arr = s.toCharArray();
                arr[i] = '.';
                if(!set.add(String.valueOf(arr))) return true;             
            }
        }
        return false;
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
