Heap is used for sorting elements with pre-assigned order. We can only access elements from head, no O(1) access to elements. When using heap, it's important to figure out how to push elements into the heap, as well as how we are sorting elements inside the queue

# 1. Minimum Cost to Hire K Workers
 - [link](https://leetcode.com/problems/minimum-cost-to-hire-k-workers/)
 - We are given two list with worker quality and their minimum wage. 
   - Every worker should be paied with the ratio for their quality in the paid group. 
   - Every worker should get minimum wage
 - We are required to return the sum of wage which is mimimum for k workers
 - By some pre investigation, we will notice that for a list of given workers, their wage are defined by maximum ratio (quality / wage) from a worker. Moreover, when a ratio is fixed, we need to choose k-1 worker with lowest quality. 
 - With above consideration, we should pre sort the array with the ratio. We should evaluate small ratio first, as later when moving to larger ratio, their mimimum wage will be auto-fulfilled. For the priorityQueue, we should be storing their quality, with max to low. We will be discarding maximum quality once queue size is exceeding k-1
 - By using above technique, we will be able to evaluate each possible "maximum ratio" worker, with their corresponding lowest quality co-workers
 ```java
 public double mincostToHireWorkers(int[] quality, int[] wage, int k) {
        int[][] ratio = new int[quality.length][2];
        for(int i = 0; i < quality.length; i++) {
            ratio[i] = new int[]{quality[i], wage[i]};
        }
        Arrays.sort(ratio, (a, b) -> {
            double r1 = a[1] * 1.0 / a[0];
            double r2 = b[1] * 1.0 / b[0];
            return r1 == r2 ? a[0] - b[0] : Double.compare(r1, r2);
        });
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> { // quality, wage,
            return b[0] - a[0];
        });
        
        double ret = Double.MAX_VALUE;
        int curSum = 0;
        for(int[] r: ratio) {
            if(pq.size() < k-1) {
                pq.offer(r);
                curSum += r[0];
                continue;
            } 
            
            while(pq.size() > k-1) {
                curSum -= pq.poll()[0];
            }
            ret = Math.min(ret, r[1]*1.0 + curSum * 1.0 * r[1]/r[0]);
            pq.offer(r);
            curSum += r[0];
        }
        return ret;
    }
 ```
