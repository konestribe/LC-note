
## 1. search range is value instead of index
- sometimes we can use value range instead of index
- the mechanism is: if the number of value smaller than our middle value is larger than target, it means our target is smaller than middle value, we can in this case reduce search range, vice versal
```
public int kthSmallest(int[][] matrix, int k) {
        int n = matrix.length;
        int lo = matrix[0][0],hi = matrix[n-1][n-1];
        while(lo <= hi) {
            int mid = lo + (hi - lo)/2;
            int count = getSmaller(matrix, mid); //get elements smaller/equal to value
            if(count < k) {
                lo = mid+1;
            }else{
                hi = mid-1;
            }
        }
        return lo;
    }
    public int getSmaller(int[][] matrix, int mid) {
        int n = matrix.length;
        int i = 0, j = n-1, ret = 0;
        while(j >= 0 && i < n) {
            if(matrix[i][j] <= mid) {
                ret += j+1;
                i++; 
            }else{
                j--;
            }
        }
        return ret;
    }
```
