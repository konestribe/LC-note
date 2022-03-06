
# 1. Two dimentional finding optimal value / condition
In this kinds of question, we will be given an input with 2 dimention, we are being asked for optimal value / condition all along the path
## 1.1 1937. Maximum Number of Points with Cost
 - [link](https://leetcode.com/problems/maximum-number-of-points-with-cost/)
 - We are given a 2D array, and moving from top to bottom. When moving down, we will be adding up sum with penalty, which is the col diff between prev val and cur val.
 - Trivially, we can dp it, and for each row, we do n^2 scan checking max value for a single cell.
 - However, we can notice either from left side or right side (accordingly we move right or left), if a value is optimal, no matter how far we move in the direction, it will always be the optimal choice comparing to other cells exists for comparison, except for newly added node when we are moving the evaluation cell.
 - With above fact, we can create leftMax / rightMax array storing this information, and compute maximum by O(n)
 ```java
     public long maxPoints(int[][] points) {
        int m = points.length;
        int n = points[0].length;
        long[][] dp = new long[m][n];
        long ret = 0;
        for(int i = 0; i < n; i++) {
            dp[0][i] = (long)points[0][i];
            ret = Math.max(ret, dp[0][i]);
        }
        
        for(int i = 1; i < m; i++) {
            int[] lftMax = new int[n];
            int[] rgtMax = new int[n];
            lftMax[0] = 0;
            rgtMax[n-1] = n-1;
            for(int k = 1; k < n; k++) {
                long curMax = dp[i-1][lftMax[k-1]];
                lftMax[k] = curMax-(k - lftMax[k-1]) >= dp[i-1][k] ? lftMax[k-1] : k;
            }
            for(int k = n-2; k >= 0; k--) {
                long curMax = dp[i-1][rgtMax[k+1]];
                rgtMax[k] = curMax-(rgtMax[k+1] - k) >= dp[i-1][k] ? rgtMax[k+1] : k;
            }
            for(int j = 0; j < n; j++) {
                dp[i][j] = Math.max(dp[i-1][lftMax[j]] - (j - lftMax[j]), 
                                   dp[i-1][rgtMax[j]] - (rgtMax[j] - j)) + points[i][j];
                ret = Math.max(ret, dp[i][j]);
            }
        }
        return ret;
    }
 ```
## 1.2 Maximum square
 - [link](https://leetcode.com/submissions/detail/157012344/)
 - We are given a 2D array containing 1 or 0. We need to return max area containing 1. 
 - Trivially, we can keep a 1D dp array counting 1's for prev area. Then we scan through this 1D array for each round, do a left/right expand to identifying max length for the possible square.
 - However, above will have O(m*n^2) time complexity. Instead we can do a direct DP
 - The idea would be we check above, left, and top-left cell what is the min value, and add one if current is a 1 cell.
 ```java
     public int maximalSquare(char[][] matrix) {
        if(matrix.length == 0 || matrix[0].length == 0) return 0;
        int rows = matrix.length;
        int cols = matrix[0].length;
        int[][] dp = new int[rows][cols];
        int max = 0;
        for(int i = 0;i < rows;i++) {
            dp[i][0] = matrix[i][0]=='0'?0:1;
            max = Math.max(max, dp[i][0]);
        }
        for(int i = 0;i < cols;i++) {
            dp[0][i] = matrix[0][i]=='0'?0:1;
            max = Math.max(max, dp[0][i]);
        }
        
        for(int i = 1;i < rows;i++) {
            for(int j = 1;j < cols;j++) {
                if(matrix[i][j] == '0') {
                    dp[i][j] = 0;
                    continue;
                }
                int min = Math.min(Math.min(dp[i][j-1],dp[i-1][j]),dp[i-1][j-1]);
                if(min == 0) {
                    dp[i][j] = 1;
                    max = Math.max(max, dp[i][j]);
                    continue;
                }else {
                    dp[i][j] = min+1;
                    max = Math.max(max, dp[i][j]*dp[i][j]);
                }
            }
        }
        return max;
    }
 ```

# 2. Permutation related DP
This kinds of question usually given a simple input with number of object, with certain condition constraining the permutation. We are asked to return all possible combinations (count). Ususally the answer will be large, and we are asked to mod the result by 10e9+7.
As from classic DP strategy, we are increasing counters 1 by 1. By solving sub problems and using sub conditions to contribute to final goals, we can reach out to n
## 2.1 Count All Valid Pickup and Delivery Options
This question given n order, each order we have to first pick up the deliver. We should bottom it up by adding order one by one
 - [link](https://leetcode.com/problems/count-all-valid-pickup-and-delivery-options/)
 - each time when we are inserting pair, we have i+1 / i+2 more ways based on previous conditions.
 ```java
     public int countOrders(int n) {
        int m = 1000000007;
        long ret = 1;
        long curSum = 1;
        int limit = 1;
        for(int i = 2; i <= n; i++) {
        // curSum is the way we insert pair based on previous condition, if prev is 2, then "1111", we have 5+4+3+2+1 ways to insert
            curSum += (++limit) + (++limit); 
            ret = (ret% m) * (curSum%m) ;
        }
        return (int) (ret % 1000000007);
    }
 ```


--------------------------------------------------------------------------------------------------------------------
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
    
### 5. dp with multiple choice for a state
1. 2 stage stock price
  - we can finish at most two total transactions. 
  - we can only hold one transaction at a time.
  - remember there are dependencies between two stages(ex. you can only buy second after you sell first)
  - we can use this relations to construct dp. the status transfer is listed below. We are tracking the day end with buy1/2 or sell1/2
  ```
  public int maxProfit(int[] prices) {
        //trace status with buy 1 and buy 2, sell 1 and sell 2
        int buy1 = Integer.MIN_VALUE, buy2 = buy1;
        int sell1 = 0, sell2 = sell1;
        for(int price: prices) {
            int newBuy1 = Math.max(buy1, 0 - price);
            int newSell1 = Math.max(sell1, buy1 + price);
            int newBuy2 = Math.max(buy2, sell1 - price);
            int newSell2 = Math.max(sell2, buy2 + price);
            buy1 = newBuy1;buy2 = newBuy2;sell1 = newSell1;sell2 = newSell2;
        }
        return Math.max(Math.max(buy1, buy2),Math.max(sell1, sell2));
    }
  ```
 2. followup: at most k transactions
 - diff with prev case, we need to use tmp dp array to store elements, and compute it along with stock price array.
 - An interesting optimization is that we add a quick solver. The solver is compressing the dp calculation. if we find we can have transaction numbers larger than half length of the input stock price, we can simply sum up all increasing trend inside our stock price array.
 ```
 public int maxProfit(int k, int[] prices) {
        if(k > prices.length/2) return quickSolve(prices, k);
        int[][] dp = new int[k+1][2];// dp0 is buy, dp1 is sell
        for(int i = 0;i < dp.length;i++) dp[i][0] = Integer.MIN_VALUE;
        for(int i = 0;i < prices.length;i++) {
            int[][] tmp = new int[k+1][2];
            for(int j = 1;j < tmp.length;j++) {
                tmp[j][0] = Math.max(dp[j][0], dp[j-1][1]-prices[i]);
                tmp[j][1] = Math.max(dp[j][1], dp[j][0] + prices[i]);
            }
            dp = tmp;
        }
        return Math.max(dp[k][0], dp[k][1]);
    }
    public int quickSolve(int[] prices, int k) {
        int sum = 0;
        for(int i = 1;i < prices.length;i++) {
            if(prices[i] > prices[i-1]) sum +=prices[i] - prices[i-1];
        }
        return sum;
    }
 ```
 
### 6. Longest Increasing Subsequence
- we will be given an array of elements. we need to return the largest length of possible subsequence that is increasing
- solution1: O(n2) trivial
  - by tracking the longest length of subseq that can be formed by index i, we can get the final answer by two loop
  ```
  public int lengthOfLIS(int[] nums) {
        int[] dp = new int[nums.length];
        Arrays.fill(dp, 1);
        int ret = 0;
        for(int i = 0;i < nums.length;i++) {
            for(int j = 0;j < i;j++) {
                if(nums[j] < nums[i]) {
                    dp[i] = Math.max(dp[i], dp[j]+1);
                }
            }
            ret = Math.max(ret, dp[i]);
        }
        return ret;
    }
  ```
- solution2: O(nlogn) 
  - We use an array to store the last value of this length of subseq (dp[2] stores the value of last element in subseq with length of 2)
    - think about seq: 1 2 9 4, when we are adding 4, 9 will be overwriten because it is already invalid(seq end with 9 is always valid if end with 4)
  - when updating the array, we can either update it to be a new length(larger than all length of elements) or update an existing index
  ```
  public int lengthOfLIS(int[] nums) {
        int[] tail = new int[nums.length];
        int size = 0;
        for(int i = 0;i < nums.length;i++) {
            int idx = Arrays.binarySearch(tail, 0, size, nums[i]);
            if(idx < 0) {
                if(idx*-1-1 == size) { //we are appending a new length
                    tail[size++] = nums[i];
                } else { // we are updating an existing length
                    tail[idx*-1-1] = nums[i];
                }
            }else{
                tail[idx] = nums[i];
            }
        }
        return size;
    }
  ```

### ** status transferring practise
- key point of dp problems is to find the status transferring functions. Below are several good examples to for you to get the idea.
- practise 1: LC920 number of music playlist
  - In this question we have three parameters: 
    - L(the total number of songs need to be played)
    - N(unique songs can be chosen by users)
    - K(minimum interval for the same song to be played)
  - we need to give out total number of scheme that can be used to construct the playlist
  - status transferring function:
    - form dp[L][N], where L is the number of songs, N is the unique number of songs
    - there are two different cases when there is a new song being played: the song is an old song or it is a brand new song
    - for the later one we have: dp[i][j] += dp[i-1][j-1]*(N-j+1); //for the new song we have (n-j+1) num of choice
    - for the former one we have: dp[i][j] += dp[i-1][j]*j; //for the old song we have j num of choice
    - if considering interval k, we should adjust our last status transferring function: dp[i][j] += dp[i-1][j]*(j-k);
    ```
    public int numMusicPlaylists(int N, int L, int K) {
        long[][] dp = new long[L+1][N+1]; //store num of songs for totally L songs and N choice
        long mod = (long)1e9+7;
        dp[0][0] = 1;
        for(int i = 1;i < L+1;i++) {
            for(int j = 1;j < N+1;j++) {
                dp[i][j] = (dp[i-1][j-1]*(N - j + 1)+dp[i][j])%mod; // song is a new song
                if(j >= K) dp[i][j] = (dp[i-1][j]*(j-K) + dp[i][j])%mod;
                dp[i][j] %= mod;
            }
        }
        return (int)dp[L][N];
    }
    ```
