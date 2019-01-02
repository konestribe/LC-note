

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
