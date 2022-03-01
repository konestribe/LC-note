Bits can be used by couple scenarios. From it's own level, bit operations got below properties:
 1. light-weighted. Bit operation is fast
 1. Each digit position only can be one or zero. 
 1. For odd number, last digit is 1, for even it is 0
 1. when multiply 2, we move all bits left for 1 position (i << 1)
 1. when divide 2, we move all bits right for 1 position (i >> i)
 1. bit operations:
   - and: `1 & 1 = 1;   1 & 0 = 0;`
   - or:  `1 | 1 = 1;   1 | 0 = 1;`
   - xor: `1 ^ 1 = 0;   0 ^ 0 = 0;   1 ^ 0 = 1;`

# 1. Utilizing Bit Counts
We can utilize bit count with it's relationship with odd/even
## 1.1 Counting Bits
 - Giving n numbers (sequencial), returning a list containing number of bit for each number
 - [link](https://leetcode.com/submissions/detail/651417380/)
 - We should realize for an even number, the bit size is same as i/2 (check no.4). For odd number, we should be 1 + number of bit in (i-1)/2
 ```java
     public int[] countBits(int n) {
        int[] ret = new int[n+1];
        for(int i = 0; i < n+1; i++) {
            ret[i] = (i%2==0) ? (ret[i/2]) : (1 + ret[(i-1)/2]);
        }
        return ret;
    }
 ```

--------------------------------------------------------------------------------

### find missing number(one)
- practise LC268
- a^a = 0 is valid for all integers.
- ^ operation is chainable.
- based on these, we can simply find missing number without using any extra space
```
    int missingNumber(vector<int>& nums) {
        int mask = nums.size();
        for(int i = 0;i < nums.size();i++) {
            mask ^= i;
            mask ^= nums[i];
        }
        return mask;
    }
```
