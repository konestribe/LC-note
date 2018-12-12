

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
