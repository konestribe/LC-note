
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
