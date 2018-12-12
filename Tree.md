

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
