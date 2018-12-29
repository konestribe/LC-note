

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
