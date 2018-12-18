### 1. practise: LC218 skyline
  - scenario: we have a list of skyline outline with[xin, xout, height]
  - all we need to do is to check the outline after combination
  - we need to output list of turple like [x, y]
  ```
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
