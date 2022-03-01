## 1. Check interval overlap, merge intervals
This kind of question will be asking for interval overlap / non overlapped interval. We might be using heap solving those kinds of interval merging questions, but the solution will not be staightforward. 

Alternatively, we should realize that if each interval has no priority, we can simply track the merge by tracking number of open intervals. A general option would be:
 - We use **TreeMap** to store index and corresponding action (ex, interval start with value 1, interval end with value -1). As treeMap will by design sorting elements, when traversing, we will just tracking the max overlapping counts
 - **!!*Scenario that we need special handling (when index is the same for start / end)***
   - When we treat single index legit for start,end. ex: `[3, 3]` we should check current open interval. If we have an open interval, we should neglect it. If no open interval, we should treat this [3,3] as standalone interval

### 1.1 Meeting Rooms II
 - [link](https://leetcode.com/problems/meeting-rooms-ii/)
 - We are given a list of meetings with start/end. return number of meeting room needed
 ```java
 public int minMeetingRooms(int[][] intervals) {
        TreeMap<Integer, Integer> tmap = new TreeMap<>();
        for(int[] itv: intervals) {
            tmap.put(itv[0], tmap.getOrDefault(itv[0], 0) + 1);
            tmap.put(itv[1], tmap.getOrDefault(itv[1], 0) - 1);
        }
        int ret = 0;
        int curCount = 0;
        // System.out.println(tmap);
        for(int key: tmap.keySet()) {
            curCount += tmap.get(key);
            ret = Math.max(ret, curCount);
        }
        return ret;
    }
 ```



## 2. Handling interval insertion / deletion with Heap
Combining intervals is simple. However if we need to insert / remove interval, we usually need help from special data structure. By using heap, we can auto get best option from heap with O(logn). However, the heap will not achieve O(1) removal, which require other thoughts on data structure
### 2.1 Exam room
 - [link](https://leetcode.com/problems/exam-room/)
 - The exam room require us to insert seat at maximum distance with existing seat. Meanwhile, we need to remove the seat which was already taken, and possibly combine intervals
 - Trivially, as for seat api we neede to take the best seat, a heap kindof data structure would be helpful. However, when it comes to remove and merge, the heap won't work (Or performance is not ideal) as heap cannot support O(1)/O(logn) removal. 
 - Also, for "seat" api, we need a heap-like structure storing intervals, treating distance as priority. We also should be able to remove objects directly from it, and combine interval and possibly insert back. By considering that, we should consider treeMap/treeSet which enable sorting with O(logn) insertion/removal.
 - For seat, we need to know if there exists intervals on left/right side of this index, and possibly merge them. We also need a data structure storing information such as available seat. In that way, when we want to remove, we check it's left and right seat/interval and decide the merge. 
 - With all the above consideration, we will use treeSet store interval objects, and use a treeMap store index(available seat) with it's corresponding intervals.
 ```java
 class Itv{
        int seat;
        int st;
        int ed;
        int dist;
        public Itv(int st, int ed, int n) {
            this.st = st;
            this.ed = ed;
            if(st == 0) {
                seat = 0;
                dist = ed;
            } else if(ed == n-1) {
                seat = n-1;
                dist = n-1-st;
            } else{
                seat = (st+ed)/2;
                dist = (ed - st)/2;
            }
        }
    }
    
    TreeSet<Itv> tset;
    TreeMap<Integer, Itv> tmap;
    int n;
    public ExamRoom(int n) {
        this.n = n;
        this.tset = new TreeSet<>((a, b) -> {
           return a.dist == b.dist?a.seat - b.seat: b.dist - a.dist;
        });
        this.tmap = new TreeMap<>();
        Itv fst = new Itv(0, n-1, n); 
        tmap.put(0, fst);
        tset.add(fst);
    }
    
    public int seat() {
        Itv cur = tset.first();
        int ret = cur.seat;
        // update with new entries for tmap
        tset.remove(cur);
        tmap.remove(ret);
        int newSt = cur.st;
        int newEd = cur.ed;
        if(cur.st == 0) {
            newSt = 1;
            updateMaps(newSt, newEd);
        } else if(cur.ed == n-1) {
            newEd = n-2;
            updateMaps(newSt, newEd);
        } else{
            updateMaps(newSt, ret-1);
            updateMaps(ret+1, newEd);
        }
        return ret;
    }
    
    public void leave(int p) {
        Integer prev = tmap.floorKey(p);
        Integer nxt = tmap.ceilingKey(p);
        int st = p;
        int ed = p;
        if(prev != null) {
            if(tmap.get(prev).ed + 1 == p) {
                st = removeKey(prev).st;
            }
        }
        if(nxt != null) {
            if(tmap.get(nxt).st - 1 == p) {
                ed = removeKey(nxt).ed;
            }
        }
        updateMaps(st, ed);
    }
    private Itv removeKey(int k) {
        Itv cur = tmap.get(k);
        tmap.remove(k);
        tset.remove(cur);
        return cur;
    }
    
    private void updateMaps(int st, int ed) { 
        //return next available seat for input interval
        if(st > ed) return;
        Itv cur = new Itv(st, ed, n); 
        tset.add(cur);
        tmap.put(cur.seat, cur);
    }
 ```
### 2.2 CPU scheduler
We are given list of [start_time, duration] for CPU tasks, need to return order for specific execution order. More specific requirements are (be aware of those before starting to coding):
 - If cpu is idle, it can pick up any stacking jobs **including the job with current start time**
 - If cpu is running when a job is scheduling, it will be but into stacking queue
 - For jobs in the queue, we will execute the job with shortest duration. For same duration we choose smaller index.
Trivially, we should be utilizing priorityQueue to store stacking jobs. Meanwhile looping through each tasks based on their start_time (which is sorted beforehand)
```java
public int[] getOrder(int[][] tasks) {
        int[][] tsks = new int[tasks.length][3];
        for(int i = 0; i < tasks.length; i++) {
            tsks[i] = new int[]{tasks[i][0], tasks[i][1], i};
        }
        Arrays.sort(tsks, (a, b) -> { // start time, duration, array Idx
           return a[0] == b[0] ? a[1] - b[1]: a[0] - b[0]; 
        });
        int[] ret = new int[tasks.length];
        int pt = 0;
        int curTime = 0;
        PriorityQueue<int[]> pq = new PriorityQueue<>((a, b) -> { //index, processing time, 
           return a[1] == b[1] ?  a[0] - b[0]: a[1] - b[1];
        });
        for(int[] t: tsks){
            while(!pq.isEmpty() && curTime < t[0]) {
                int[] tmp = pq.poll();
                ret[pt++] = tmp[0];
                curTime += tmp[1];
            }
            if(t[0] > curTime) {
                curTime = t[0] + t[1];
                ret[pt++] = t[2];
            } else{
                // cannot be processed, stack
                pq.offer(new int[]{t[2], t[1]});
            }
        }
        while(!pq.isEmpty()) ret[pt++] = pq.poll()[0];
        return ret;
    }
```



------------------------------------------------------------------------------------------------------------------------
<past Note>
 
### 1. practise: LC218 skyline
  - scenario: we have a list of skyline outline with[xin, xout, height]
  - all we need to do is to check the outline after combination
  - we need to output list of turple like [x, y]
  ```java
  public List<int[]> getSkyline(int[][] buildings) {
        int[][] list = new int[buildings.length*2][3]; // x, y, in or out
        List<int[]> ret = new ArrayList<>();
        int pt = 0, pb = 0;
        while(pt < list.length) {
            list[pt++] = new int[]{buildings[pb][0], -buildings[pb][2], pb};
            list[pt++] = new int[]{buildings[pb][1], buildings[pb][2], pb};
            pb++;
        }
        Arrays.sort(list, (a, b)->{
            if(a[0] == b[0]) return a[1] - b[1];
           return a[0] - b[0]; 
        });
        TreeMap<Integer, Set<Integer>> tmap = new TreeMap<>(); //height and the index
        tmap.put(0, new HashSet());
        int height = 0;
        for(int[] b: list) {
            if(b[1] > 0) { //indicating a finish
                tmap.get(b[1]).remove(b[2]);
                if(tmap.get(b[1]).size() == 0) tmap.remove(b[1]);
            }else{
                tmap.putIfAbsent(-b[1], new HashSet<>());
                tmap.get(-b[1]).add(b[2]);
            }
            Integer cur = tmap.lastKey();
            if(cur != height) {
                ret.add(new int[]{b[0], cur});
            }
            height = cur;
        }
        return ret;
    }
  ```
