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

