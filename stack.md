
### 1. balanced parenthesis
  - LC 678
  - scenario: we have ( and ) and * to represent any character(l,r,'')
  - we use two counters to finish this problem in one pass, high means we regards all * to be ), while low to be (
  ```
  public boolean checkValidString(String s) {
        int low = 0, high = 0;
        for(char c: s.toCharArray()) {
            if(c == '(') {
                low++;
                high++;
            }else if(c == ')') {
                low--;
                high--;
            }else{
                low--;
                high++;
            }
            
            if(high < 0) return false;
            low = Math.max(0, low);
        }
        return low == 0;
    }
  ```


### 2. remove invalid parenthesis
- LC 301
- scenario: we will be given an invalid parenthesis. We need to remove least parenthesis to make it valid
- we first find out the number of lft and rgt parenthesis need to be removed and then run a dfs to create valid one
```
class Solution {
    public List<String> removeInvalidParentheses(String s) {
        //right parenthesis cannot appear before left
        int left = 0, right = 0;
        for(int i = 0;i< s.length();i++) {
            char cur = s.charAt(i);
            if(cur == '(') left++;
            else if(cur == ')') {
                if(left > 0) left--;
                else right++;
            }
        }
        Set<String> ret = new HashSet<>();
        buildAns(ret, s, new StringBuilder(), 0, left, right, 0);
        return new ArrayList<>(ret);
    }
    public void buildAns(Set<String> ret, String s, StringBuilder sb, int i, int left, int right, int open) {
        if(left < 0 || right < 0 || open < 0) return;
        
        if(i == s.length()) {
            if(left == 0 && right == 0 && open == 0) {
                ret.add(sb.toString());
            }
            return;
        }

        char cur = s.charAt(i);
        int len = sb.length();
        if(cur == '(') {
            buildAns(ret, s, sb, i+1, left-1, right,open);
            buildAns(ret, s, sb.append(cur), i+1, left, right, open+1);
        }else if(cur == ')') {
            buildAns(ret, s, sb, i+1, left, right-1,open);
            buildAns(ret, s, sb.append(cur),i+1, left, right, open-1);            
        }else{
            buildAns(ret, s, sb.append(cur), i+1, left, right,open);
        }
        sb.setLength(len);
        return;
    }
}
```
