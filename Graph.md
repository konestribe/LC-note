

### 1. BFS

## 1. calculate minimum step
- practise
- scenarios: we will be required to find the minimum step start from position st to ed
- main steps:
  - create queue, whenever we add new states onto the queue, we mark the states with visited 
  - each time we poll from queue, we check the spreading choice of current position. 
  - if the state is not visited and also valid, we add it to queue.
  - if we find one which is valid, we simply return
  
  
  
  
### 2. DFS
  - practise: LC 99 recover bst
  - scenario: two nodes are swapped by mistake. We need to adjust the seq of two nodes to make it valid
  - main steps:
    - if we use extra space, we can actually run an inorder tranverse, we will just know the wrong nodes position and adjust their values
    - if we want to finish it in constant space, we need to know prev and cur nodes to make judgement:
  ```
  class Solution {
    TreeNode fst = null, scd = null, prev = new TreeNode(Integer.MIN_VALUE);
    public void recoverTree(TreeNode root) {
        tranverse(root);
        int tmp = fst.val;
        fst.val = scd.val;
        scd.val = tmp;
        return;
    }
    public void tranverse(TreeNode node) {
        if(node == null) return;
        tranverse(node.left);
        if(node.val < prev.val) {
            if(fst == null) {
                fst = prev;
                scd = node;
            }else{
                scd = node;
            }
        }
        prev = node;
        tranverse(node.right);
    }
}
  ```
## 2. Bipartite Graph
- practise: LC 886, LC 785
- scenarios: we are forced to divide a group into two, each sub group of the elements are contradicted with another one.
  we will be given a set of relations representing the contradictory
- main steps: 
  - create graph, representing relations i -> [j]
  - dfs marking colors for elements
  - if there exists contradictory, means impossible partition.

- practise: LC 968
- SCENARIO: we need to place cameras to cover itself and nearby nodes. A greedy approach is that for leaf nodes, we can always make sure that it's better to place camera on the parents of it.
```
class Solution {
    int ret = 0;
    public int minCameraCover(TreeNode root) {
        return (dfs(root)<1?1:0)+ret;
    }
    public int dfs(TreeNode node) {//0 means leaf, 1 means parent of leaf, 2 means covered
        int left = node.left==null?2:dfs(node.left);
        int right = node.right == null?2:dfs(node.right);
        if(left==0||right==0) {
            ret++;
            return 1;
        }
        return (left==1||right==1)?2:0;
    }
}
```


### 3. Union Find
- practise 685: redundant connection 2
- scenario
  - we are given a directed graph, we need to find last invalid edge to delete to make graph a valid tree.
  - remember there will only be one possible ways
- idea:
  - we can find out there are two kinds of invalid edge, one is loop(which is the same with RC1), another is dup parent issue.
  - we use three pointers to track the possible invalid status. An important point: if we find dop parent issue, we will assume first ndoe is valid and going along so if we find issues later it means our assumption is false;
  ```
  public int[] findRedundantDirectedConnection(int[][] edges) {
        int[] fst = null, scd = null, loop = null;
        int[] root = new int[edges.length+1];
        for(int i = 1;i < root.length;i++) root[i] = i;
        for(int[] edge: edges) {
            int rt1 = find(edge[0], root), rt2 = find(edge[1], root);
            if(rt1 == rt2) {
                loop = edge;
            }else{
                if(rt2 == edge[1]) {//means not two parent issue, valid ongoing
                    root[edge[1]] = edge[0];
                }else{ //we did not change existing relationship, means we assume we need to delete scd
                    fst = new int[]{root[edge[1]], edge[1]};
                    scd = edge;
                }
            }
        }
        if(scd == null) return loop;
        if(loop == null) return scd;
        return fst;
    }
    public int find(int edge, int[] root) {
        while(root[edge] != edge) edge = root[edge];
        return edge;
    }
  ```
  
  ## Topological Sort
  Need prerequisite that graph is with order (DAG). The idea is used for solving below questions
  - Detecting loop. Circular dependency will be kept in Queue
  - Identify order for items from queue

We use below data structure and process to solve questions
  - Graph. A graph storing information a -> b, which means a need to be fulfilled before executing b
  - Indegree array. We use it to track dependencies. If an element has no dependency, we will put it into the queue, which means it can be analyzed as all it's prerequisites have been fulfilled
  - Queue. We use it to loop through elements that could be analyzed. If the queue contains elements when we exit, those will be circular dependent elements

Time Complexity:
  - Same as DFS / BFS, ideally it should be `O(v+e)`, v is the vertex (node) and e is edge(lines). We visit each node and edge exactly once.

Practise:
  - Google tag question: [2115. Find All Possible Recipes from Given Supplies](https://leetcode.com/problems/find-all-possible-recipes-from-given-supplies/)
