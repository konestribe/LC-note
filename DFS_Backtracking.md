# DFS

We will be focusing on different special usage of DFS.

---------------
## Backtracking recording all possible combinations
For questions asking for all combinations, DP will be less prior as dp will neglect intermediate state, which is required under this scenario.
We should purely using DFS to traverse the whole number set.

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
