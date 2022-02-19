# DFS

We will be focusing on different special usage of DFS.

---------------
## Backtracking recording all possible combinations
For questions asking for all combinations, DP will be less prior as dp will neglect intermediate state, which is required under this scenario.
We should purely using DFS to traverse the whole number set.
The high level workflow for this kind of question would be:
 - Adjusting input
   - In some cases we need to preprocessing the candidate list, such as sort it. For example, if you need to dedup and candidate contains same elements, we should sort it beforehand.
 - Creating tracker for visited elements
   - Sometimes if we are required all permutations, we can start the tracking from anywhere. In that case we should be using a visit set
 - DFS
   - Exit criteria would be running out of the whole list. Remember to add intermediate results to the final set.
   - **Deduplication**
     - We usually dedup by sorting elements beforehands, and prevent visiting same value in the same layer
     - ex: `int prev = -1;`  and then inside for loop: `if(prev == c[i]) continue;`

### 78. Subsets
  - Practise: [link](https://leetcode.com/problems/subsets/)
  - Solution
    - We need to generate subsets for given numbers, see below example:
    - `[1,2,3]` -> Output: `[[],[1],[2],[1,2],[3],[1,3],[2,3],[1,2,3]]`
    - For DFS, we start from each index, append new number based on previous list. Remember to fall back the list to previous state after returned from next level dfs.
    ```java
    public List<List<Integer>> subsets(int[] nums) {
        Arrays.sort(nums);
        List<List<Integer>> ret = new ArrayList<>();
        dfs(nums, ret, 0, new ArrayList<>());   
        return ret;
    }
    
    private void dfs(int[] nums, List<List<Integer>> ret, int idx, List<Integer> list) {
        if(idx >= nums.length) {
            ret.add(new ArrayList<>(list));
            return;
        }
        ret.add(new ArrayList<>(list));
        for(int i = idx; i < nums.length; i++) {
            list.add(nums[i]);
            dfs(nums, ret, i+1, list);
            list.remove(list.size() - 1);
        }
    }
    ```
    
 ### 47. Permutations II
  - Practise: [link](https://leetcode.com/problems/permutations-ii/)
  - Solution
    - **Deduplication**
     - We usually dedup by sorting elements beforehands, and prevent visiting same value in the same layer
    ```java
        public List<List<Integer>> permuteUnique(int[] nums) {
        Arrays.sort(nums);
        boolean[] visited = new boolean[nums.length];
        List<List<Integer>> ret = new ArrayList<>();
        dfs(nums, ret, visited, new ArrayList<>());
        return ret;
    }
    
    private void dfs(int[] nums, List<List<Integer>> ret, boolean[] visited, List<Integer> list) {
        if(list.size() == nums.length) {
            ret.add(new ArrayList<>(list));
            return;
        }
        int prev = -20;
        for(int i = 0; i < nums.length; i++) {
            if(visited[i] || prev == nums[i]) continue;
            prev = nums[i];
            visited[i] = true;
            list.add(nums[i]);
            dfs(nums, ret, visited, list);
            list.remove(list.size()-1);
            visited[i] = false;
        }
    }
    ```
    
----------------------------------------------------------------------
## DFS early termination criteria

Under the impression, DFS by default will take up O(2^n) time complexity, as we go over each possible solutions. As a result, DFS usualy combined with early termination criteria, most often would be preventing visiting same scenario more than once. There are certain scenarios we need to think about 

### LC 909. Snakes and Ladders
 - Practise: [link](https://leetcode.com/problems/snakes-and-ladders/)
 - Solution
   - Even this is BFS, we also need early termination methods. In this example, we should allow revisit the location that directly visited by rolling dices instead of using ladder or snake. This is based on requirement: we only take ladder/snake once in one move. 

