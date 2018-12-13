


### 1. largest common factor
- if a or b is zero, we return non-zero elements, because everything divides zero
```
public int gcd(int a, int b) { 
    // Everything divides 0  
    if (a == 0) 
      return b; 
    if (b == 0) 
      return a; 

    // base case 
    if (a == b) 
        return a; 

    // a is greater 
    if (a > b) 
        return gcd(a-b, b); 
    return gcd(a, b-a); 
 } 
```


### 2. rectangle area sum
- give a set of points, check if we can form rectangle/find max area/min area
- key idea is to use hash map to store elements with same x/y, and then O(n^2) defining diagonal points of the rectangle, update return value
- practise: 939
```
public int minAreaRect(int[][] points) {
        Map<Integer, Set<Integer>> map = new HashMap(); //x -> y's
        for(int[] p: points) {
            map.putIfAbsent(p[0], new HashSet());
            map.get(p[0]).add(p[1]);
        }
        int ret = Integer.MAX_VALUE;
        for(int[] i: points) {
            for(int[] j: points) {
                if(i[0] == j[0] || i[1] == j[1]) continue;
                if(!map.get(i[0]).contains(j[1]) || !map.get(j[0]).contains(i[1])) continue;
                ret = Math.min(ret, Math.abs(i[0]-j[0])*Math.abs(i[1]-j[1]));
            }
        }
        return ret == Integer.MAX_VALUE?0:ret;
    }
```
