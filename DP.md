

### 1. probabilities
- practise: LC837 21 GAME
  - maintaining a k size window and dp array store the probability **up to current state**.
  - if window size larger than k, substract the previous total probability
  - if size is larger than limit, substract the window larger than limit
  ```
      double new21Game(int N, int K, int W) {
        if(K == 0) return 1;
        int maxval = K+W+1;
        vector<double> dp(maxval-1, 0);
        dp[0] = 1;
        for(int i = 1;i <= N;i++) {
            dp[i] = dp[i-1];
            if(i <= W) {
                dp[i] += dp[i-1]/W;
            }else if(i > W) {
                dp[i] += (dp[i-1] - dp[i-W-1])/W;
            }
            if(i >= K) {
                dp[i] -= (dp[i-1]-dp[K-1])/W;
            }
        }
        return dp[N] - dp[K-1];
    }
  ```

 - practise: LC688 chess
  - we need to give the probability of the chess still on the board after k moves.
  - remember, for the knight, each move is 1/8, we can do a forward dp and check the possibility of arrival at each point on the chessboard after k moves;



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
    
    
 ### 4. knapsack problems
 1. 0 - 1 knapsack
 - In this kinds of problems, we can only use an item 0 or 1 time, while under the constrains of the total capacity.
 - In 0-1 knapsack problems, we prefer fill our array from very back. This can save extra space when running the loop.
  - practise: 416. Partition Equal Subset Sum
    - we need to check if the array can be partitioned into two identical sum
    ```
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for(int a: nums) sum += a;
        if(sum%2!=0) return false;
        vector<bool> dp(sum/2+1, false);
        dp[0] = true;
        for(int i: nums) {
            for(int j = dp.size()-1;j >=i;j--) {
                if(dp[j]) continue;
                dp[j] = dp[j-i];
            }
        }
        return dp[dp.size()-1];
    }
    ```
    
 2. 0-inf knapsack
 - In this kinds of problems, we usually loop from start. Because we can use up an item up to inf times, it's safe to add on prev values
 ```
 int change(int amount, vector<int>& coins) {
        vector<int> dp(amount+1, 0);
        dp[0] = 1;
        for(int c: coins) {
            for(int i = c;i < amount+1;i++) {
                dp[i]+= dp[i-c];
            }
        }
        return dp[amount];
    }
 ```
    
 3. multi-dimensional knapsack  
  - practise: 879. Profitable Schemes
    - we have two dimension constraints(with diff direction constrains). we are required to count the number of combinations that have gang member numbers smaller than G and total profit at least P
    - We don't really care about the profit in this case - we only need to care about the group constrains. 
    - An optimization is that we are filling the dp array from bottom right to top left. This can reduce the space from N3 to N2. if we doing dp forward, we need to use another tmp array to store new values and replace the original one.
    ```
    public int profitableSchemes(int G, int P, int[] group, int[] profit) {
        int[][] dp = new int[P+1][G+1];
        dp[0][0] = 1;
        int mod = 1000000007;
        for(int k = 0;k < group.length;k++) {
            int g = group[k], p = profit[k];
            for(int i = P;i >= 0;i--) {
                for(int j = G-g;j >= 0;j--) {
                    dp[Math.min(P, i+p)][j+g] = (dp[Math.min(P, i+p)][j+g]+dp[i][j])%mod;
                }
            }
        }
        int total = 0;
        
        for(int i: dp[P]) {
            total = (total+i)%mod;
        } 
        return total;
    }
    ```
    
