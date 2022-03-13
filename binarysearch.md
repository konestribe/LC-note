
# 1. search range is value instead of index
 - Usually, binary search will help us identify the index. However, Sometimes we can use it to search the value.
 - By identifying max and min value, we embeded the real code logic into binarySearch conditions.
## 1.1 1891. Cutting Ribbons
 - [link](https://leetcode.com/problems/cutting-ribbons/)
 - Similar: [link](https://leetcode.com/problems/capacity-to-ship-packages-within-d-days/)
 - We are given a list of ribbons, we can cut any ribbons into any length. Return the max integer length that you can keep k ribbons. In other words, try to make a list of size k, within each group the size is the same and should be maximum
 - We can try to binarySearch the group size and check if we can form up the answer by scanning list once each time. The total TC would be O(nlogn)
 ```java
 public int maxLength(int[] ribbons, int k) {
        long sum = 0;
        for(int v: ribbons) sum += v;
        if(sum < (long)k) return 0;
        
        int st = 1; 
        int ed = (int)(sum / (long)k) + 1;
        
        while(st < ed) {
            int mid = st + (ed - st)/2;
            if(canSplit(ribbons, mid, k)) {
                st = mid + 1;
            } else{
                ed = mid;
            }
        }
        return st - 1;
    }
    
    private boolean canSplit(int[] a, int n, int k) {
        int ret = 0;
        for(int v: a) {
            ret += v/n;
            if(ret >= k) return true;
        }
        return false;
    }
 ```
