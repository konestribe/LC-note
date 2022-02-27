## Handling interval insertion / deletion with Heap
Combining intervals is simple. However if we need to insert / remove interval, we usually need help from special data structure. By using heap, we can auto get best option from heap with O(logn). However, the heap will not achieve O(1) removal, which require other thoughts on data structure
### Exam room
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
