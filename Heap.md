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
 
# 2. 1057. Campus Bikes
 - [link](https://leetcode.com/problems/campus-bikes/)
 - We are given w number of worker, n (n >= w) number of bikes. We need to assign bikes to workers, each worker should get bike with minimum distance. If there are conflict, smaller index of worker will have priviledge.
 - Straightforwardly, we should be using `PriorityQueue` to store all possible distances (with smaller in the top, min-heap). When reading from pq, we check if w/b assigned and assign return list accordingly. The TC will ve O(mnlog(mn)), 
 - From another thoughts, instead of using a priorityQueue and sort / retrieve it, we can utilize `Bucket Sort`, with bucket to be the distance itself. After computing all values, we read from small distance to large distance, and assign worker / bikes accordingly
 ```java
 public int[] assignBikes(int[][] w, int[][] b) {
        int m = w.length;
        int n = b.length;
        List<int[]>[] dis = new List[2000]; // widx, bidx
        for(int i = 0; i < m; i++) {
            for(int j = 0; j < n; j++) {
                int dist = dist(w, b, i, j);
                if(dis[dist] == null) dis[dist] = new ArrayList<>();
                dis[dist].add(new int[]{i, j});
            }
        }
        int[] ret = new int[m];
        boolean[] bVisited = new boolean[b.length];
        Arrays.fill(ret, -1);
        int pt = 0;
        for(int i = 0; i < 2000; i++) {
            if(dis[i] == null) continue;
            for(int[] itv: dis[i]) {
                if(ret[itv[0]] != -1 || bVisited[itv[1]]) continue;
                ret[itv[0]] = itv[1];
                bVisited[itv[1]] = true;
                if(++pt == ret.length) return ret;
            }
        }
        return ret;
    }
    
    private int dist(int[][] w, int[][] b, int i, int j) {
        return Math.abs(w[i][0] - b[j][0]) + Math.abs(w[i][1] - b[j][1]);
    }
 ```
