
### 1. randomization with index picking
- LC 398
- we will be given a long array and we need to randomly return the index of query value
- scenario:
  - we cannot require extra space.
  - one way to call with randomization is that when scan through the array, we will randomize based on total counts.
  - if the randomized number is 0, we know we have to update it tobe a new value
  ```
  class Solution {
    int[] nums;
    Random rand;
    public Solution(int[] nums) {
        this.nums = nums;
        this.rand = new Random();
    }
    
    public int pick(int target) {
        int ret = 0;
        int cnt = 0;
        for(int i = 0;i < nums.length;i++) {
            if(nums[i] != target) continue;
            cnt++;
            if(rand.nextInt(cnt) == 0) ret = i;
        }
        return ret;
    }
  }
  ```

### 2. concurrent topk query
- we will be given a system with incoming query(text documents)
- we need to return top k frequent query at any given time
- scenario:
  - heap is very useful in this scenario. More specifically, we will need a minHeap to record top k.
  - problem is: how can we update it if we directly storing text documents inside the heap?
  - we need another Hashmap to store text->count of the documents
  - we can just compare the peek value of this minHeap to decide whether we should update the heap based on current text
