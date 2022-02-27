# 1. Level traveresing a tree
Sometimes, the question set up require us to traverse the tree using level traversing. Usually we just use a queue to first-in-first-out the nodes. However, sometimes we neeed to use Deque to access elements from both side.
# 1.1 Maximum width of binary tree
 - [link](https://leetcode.com/problems/maximum-width-of-binary-tree/submissions/)
 - As we need to know the width of a level, which also might contains bunch of null nodes, DFS is not my top option now (it might work though). Using BFS should be ideal as we have control and visibility for each level by natural.
 - We use a deque as we neeed to trim leading and trailing empty nodes. Those should be discarded. 
 - However, for one optimization idea we can have is to instead of listing out all empty nodees, we combine them with value indicating counts. 
 ```java
 public int widthOfBinaryTree(TreeNode root) {
        Deque<TreeNode> dq = new ArrayDeque<>();
        dq.offer(root);
        int ret = 0;
        while(!dq.isEmpty()) {
            int curSize = 0;
            boolean firstOccurNxt = false;
            for(int z = dq.size(); z > 0; z--) {
                TreeNode cur = dq.pollFirst();
                if(cur.val > 100) {
                    curSize += cur.val - 100;
                    if(firstOccurNxt) {
                        cur.val = 100 + (cur.val - 100) * 2;
                        dq.offer(cur);
                    }
                } else{
                    curSize++;
                    if(cur.left == null) {
                        if(firstOccurNxt) {
                            dq.offer(new TreeNode(101));
                        }
                    } else{
                        firstOccurNxt = true;
                        dq.offer(cur.left);
                    }
                    if(cur.right == null) {
                        if(firstOccurNxt) {
                            dq.offer(new TreeNode(101));
                        }
                    } else{
                        firstOccurNxt = true;
                        dq.offer(cur.right);
                    }
                }
            }
            ret = Math.max(ret, curSize);
            while(!dq.isEmpty() && dq.peekLast().val > 100) {
                dq.pollLast();
            }
        }
        return ret;
    }
 ```

----------------------------------------------------------------------------------------------------

### 1. complete binary tree
- practise: LC 222
- if we know the left and right height is the same, we can safely compute the total number of nodes. 
Else we need to recursively check left and right height
```
int getheight(TreeNode* rt, int direct) {
        if(rt == nullptr) return 0;
        return 1+(direct == 0?getheight(rt->left, direct):getheight(rt->right, direct));
    }
public:
    int countNodes(TreeNode* root) {
        int lheight = getheight(root, 0), rheight = getheight(root, 1);
        if(lheight == rheight) {
            return (1<<lheight)-1;
        }else{
            return 1+countNodes(root->left)+countNodes(root->right);
        }
    }
```


### 2. layer traversal
- practise: LC 117
- we need to establish next pointer for each layer
- we can use BFS naively, but we can also finish it in O(1) space complexity
- we set pointers for current parent nodes, set head and prev pointers to construct next layer.
```
public class Solution {
    public void connect(TreeLinkNode root) {
        //iterative O(1)
        TreeLinkNode cur = root; //node for current level
        TreeLinkNode head = null;//head for next level
        TreeLinkNode prev = null; //node for the next available one
        while(cur != null) {
            //connect child for current layer
            while(cur != null) {
                if(cur.left != null) {
                    if(head == null) {
                        //not assign head, assign it
                        head = cur.left;
                    }
                    if(prev != null) {
                        prev.next = cur.left;
                    }
                    prev = cur.left;
                }
                if(cur.right != null) {
                    if(head == null) {
                        //not assign head, assign it
                        head = cur.right;
                    }
                    if(prev != null) {
                        prev.next = cur.right;
                    }
                    prev = cur.right;
                }
                cur = cur.next;
            }
            //move pointers to next layer
            cur = head;
            head = null;
            prev = null;
        }
    }
```
### 3. serialize / deserialize BST
- LC 449
- unlike s/ds bt, we need to make this structure as compact as possible. An observation is that BST is self-order contained, which means we don't need to use NULL value to indicate endleaves. Instead, we can use lower/upper bound to constrain the rebuild process.
```
public class Codec {

    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        helperBuild(root, sb);
        return sb.toString();
    }
    public void helperBuild(TreeNode node, StringBuilder sb) {
        if(node == null) return;
        sb.append(" ").append(node.val);
        helperBuild(node.left, sb);
        helperBuild(node.right, sb);
    }
    int pt = 1;
    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        String[] nd = data.split(" ");
        return buildTree(nd, Integer.MIN_VALUE, Integer.MAX_VALUE);
    }
    public TreeNode buildTree(String[] nd, int min, int max) {
        if(pt == nd.length) return null;
        int val = Integer.parseInt(nd[pt]);
        if(val <= min || val >= max) return null;
        TreeNode cur = new TreeNode(val);
        pt++;
        cur.left = buildTree(nd, min, val);
        cur.right = buildTree(nd, val, max);
        return cur;
    }
}
```

### 4. morris tranversal
- inorder morris:
  - we use two pointers to finish O(1) time coplexity traversal
```
public List<Integer> inorderTraversal(TreeNode root) {
        List<Integer> list = new ArrayList<>();
        TreeNode cur = root, prev = null;
        while(cur != null) {
            if(cur.left != null) {
                prev = cur.left;
                while(prev.right != null && prev.right != cur) {
                    prev = prev.right;
                }
                if(prev.right == null) {
                    prev.right = cur;
                    cur = cur.left;
                }else {
                    list.add(cur.val);
                    prev.right = null;
                    cur = cur.right;
                }
            }else{
                list.add(cur.val);
                cur = cur.right;
            }
        }
        return list;
    }
```     
