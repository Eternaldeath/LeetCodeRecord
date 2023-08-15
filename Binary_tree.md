# 简单

## [617. 合并二叉树](https://leetcode.cn/problems/merge-two-binary-trees/)

本题求解方式放在了[这里](https://blog.csdn.net/qq_34902437/article/details/132282156?csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%22132282156%22%2C%22source%22%3A%22qq_34902437%22%7D)

## [114. 二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

本题求解方式放在了[这里](https://github.com/Eternaldeath/LeetCodeRecord/blob/master/List.md#114-%E4%BA%8C%E5%8F%89%E6%A0%91%E5%B1%95%E5%BC%80%E4%B8%BA%E9%93%BE%E8%A1%A8)

## [94. 二叉树的中序遍历](https://leetcode.cn/problems/binary-tree-inorder-traversal/)

1. 思路：本题非常简单，就是直接套用中序遍历的方法即可
2. 完整示例

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    // 定义一个全局变量 inorder 用来存放数据
    vector<int> inorder;
    
    vector<int> inorderTraversal(TreeNode* root) {
        if(root == nullptr) return inorder;

        inorderTraversal(root->left);
        inorder.push_back(root->val);
        inorderTraversal(root->right);

        return inorder;
    }
};
```

## [101. 对称二叉树](https://leetcode.cn/problems/symmetric-tree/)

1. 思路：本题采用递归的方式，用两个指针指向根节点，然后一个左移，一个右移进行判断
2. 完整示例 

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    bool isSymmetric(TreeNode* root) {
        return isMirror(root, root);
    }

    bool isMirror(TreeNode* t1, TreeNode* t2){
        // 递归结束条件，判断遍历已经到达了叶节点
        if(t1 == nullptr && t2 == nullptr) return true;
        // 递归结束条件，判断遍历的某镜像节点，一个为 null，一个不为 null
        if(t1 == nullptr && t2 != nullptr) return false;
        if(t1 != nullptr && t2 == nullptr) return false;

        return (t1->val == t2->val) 
            && isMirror(t1->left, t2->right)
            && isMirror(t1->right, t2->left);
    }
};
```

## [104. 二叉树的最大深度](https://leetcode.cn/problems/maximum-depth-of-binary-tree/)

1. 思路：通过递归的方式
   1. 递归的边界条件是访问到空节点
   2. 递归的递进式子是 ```max(maxDepth(t1), maxDepth(t2)) + 1```
2. 完整示例

```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int depth = 0;
    int maxDepth(TreeNode* root) {
        // 递归边界条件
        if(root == nullptr) return 0;
        depth = max(maxDepth(root->left), maxDepth(root->right)) + 1;
        return depth;
    }
};
```

## [226. 翻转二叉树](https://leetcode.cn/problems/invert-binary-tree/)

1. 思路：通过自底向上的方式递归翻转二叉树，举例如图所示

![](.\img\binary_tree\invert_01.png)

左图是一开始的图示，最终翻转成右侧的图示

算法的最开始，我们通过递归遍历（这里本质上是后序遍历）达到叶子节点，如下图所示

![](.\img\binary_tree\invert_02.png)

当到达叶子结点的下一层，即空节点的时候，就开始向上返回。这里演示的是递归左孩子，同理，参考代码可以递归右孩子。

在到达叶子节点后，并执行完对应的 invertTree(...) 后，就可以开始执行孩子的交换了，这里演示的是交换空节点，其他节点的交换相同。当叶子节点所在的层次处理完成，继续返回，**之后交换的时候，处理的本质上就是左右子树了**

![](.\img\binary_tree\invert_03.png)

2. 完整示例 ```力扣官方题解``` ```未手动实现```

```C++
class Solution {
public:
    TreeNode* invertTree(TreeNode* root) {
        if (root == nullptr) {
            return nullptr;
        }
        TreeNode* left = invertTree(root->left);
        TreeNode* right = invertTree(root->right);
        root->left = right;
        root->right = left;
        return root;
    }
};
```

