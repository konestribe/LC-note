

### 1. possibility
- practise: LC837 21 GAME
  - maintaining a k size window and dp array store the probability up to current state.
  - if window size larger than k, substract the size previous
  - if size larger than limit, substract the window larger than limit



### 2. dependent + print path
- practise: LC368(Largest Divisible Subset)
  - we need to print path(print possible subset combinations)
  - observation: actually we can form a sequence that larger elements can always fully divide smaller ones
    we can continuing enlarging subsets and maintain the records of the largest one
  - when doing the path printing, we have to trace back the elements(remember smaller divisible dp value will always be 1 smaller than the larger one, that's how we update the dp array)
  ```
  vector<int> largestDivisibleSubset(vector<int>& nums) {
        if(nums.size() == 0) return vector<int>();
        sort(nums.begin(), nums.end());
        vector<int> dp(nums.size(), 1);
        int maxidx = 0;
        for(int i = 0;i < nums.size();i++) {
            for(int j = 0;j < i;j++) {
                if(nums[i]%nums[j] == 0) {
                    dp[i] = max(dp[i], dp[j]+1);
                }
            }
            if(dp[i] > dp[maxidx]) maxidx = i;
        }
        vector<int> res;
        res.push_back(nums[maxidx]);
        for(int i = maxidx-1;i >= 0;i--) {
            if(dp[i]+1 == dp[maxidx] && nums[maxidx] % nums[i] == 0){
                res.push_back(nums[i]);
                maxidx = i;
            }
        }
        return res;
    }
  ```
  
  
  
  
### 3. trick of using bitset
- practise: LC 691(STICKERS TO SPELL WORD)
  - Sometimes we can use a bit set to represent all possible intermediate states(int this problem, we can form a bitset of size - 1<<len(target)), this can help us to define the search range
  - a forward dp is performed (similar to frog jump). we record minimal number of stickers we used to form current string base.
  ```
   int minStickers(vector<string>& stickers, string target) {
        unordered_map<char, vector<int>> mp;
        for(int i = 0;i < target.length();i++) {
            char c = target[i];
            if(mp.find(c) == mp.end()) mp[c] = vector<int>();
            mp[c].push_back(i);
        }
        int limit = 1<<(target.length());
        vector<int> dp(limit, -1);
        dp[0] = 0;
        for(int i = 0;i < limit;i++) {
            if(dp[i] < 0) continue;
            for(auto stk: stickers) {
                int base = i;
                for(char c: stk) {
                    if(mp.find(c) == mp.end()) continue;
                    for(int idx: mp[c]) {
                        if((base>>idx) & 1 == 1) continue;
                        base |= (1<<idx);
                        break;
                    }
                }
                dp[base] = dp[base] == -1?dp[i]+1:min(dp[base], 1+dp[i]);
            }
        }
        return dp[dp.size()-1];
    }
  ```
    
    
    
    
