# 前言

1. 树或二叉树常用底层解题思路：递归

# 简单

## [589. N 叉树的前序遍历](https://leetcode.cn/problems/n-ary-tree-preorder-traversal/)

1. 思路：本题使用递归的思路在于在进入每个节点的时候先打印输出节点的 value，然后再通过循环的方式遍历节点的子节点。其中前序遍历的顺序是根->左子树->右子树的顺序
2. 完整示例

```C++
/*
// Definition for a Node.
class Node {
public:
    int val;
    vector<Node*> children;

    Node() {}

    Node(int _val) {
        val = _val;
    }

    Node(int _val, vector<Node*> _children) {
        val = _val;
        children = _children;
    }
};
*/

class Solution {
public:
    void helper(Node* root, vector<int>& res){
        if(root == nullptr) return;
        res.emplace_back(root->val);
        for(auto& ch : root->children){
            helper(ch, res);
        }
        return ;
    }

    vector<int> preorder(Node* root) {
        vector<int> res; // output
        helper(root, res);
        return res;
    }
};
```

3. 相似题目：[144. 二叉树的前序遍历](https://leetcode.cn/problems/binary-tree-preorder-traversal/)，[590. N 叉树的后序遍历](https://leetcode.cn/problems/n-ary-tree-postorder-traversal/)
4. 时间复杂度：O(m) 其中 m 为节点的个数；空间复杂度：O(m) 

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

# 中等

## [96. 不同的二叉搜索树](https://leetcode.cn/problems/unique-binary-search-trees/)

1. 思路：直接利用卡特兰数的数学公式求解。但这里有个注意点，如果我们直接用下面的公式计算，会因为超出整型类型的取值范围而报错（大数溢出的问题）

$$
h(n)=\frac{C^n_{2n}}{n+1}
$$

因此，我们选择利用迭代的方式，计算出最终的结果，公式如下
$$
C_0 = 1,  C_{n+1} = \frac{2(2n+1)}{n+2}C_n
$$

2. 完整示例 ```力扣官方题解``` ```未手动实现```

```C++
class Solution {
public:
    int numTrees(int n) {
        long long C = 1;
        for (int i = 0; i < n; ++i) {
            C = C * 2 * (2 * i + 1) / (i + 2);
        }
        return (int)C;
    }
};
```

## [98. 验证二叉搜索树](https://leetcode.cn/problems/validate-binary-search-tree/)

1. 思路：本题采用递归的方式，每一次在递归中验证所在子树是否满足要求，即左子树小于根，根小于右子树的条件，同时，在每一次递归时，我们将缩小比较的范围。以上论述比较抽象，在具体的代码中，我通过注释的方式描述了
1. 完整示例 ```力扣官方题解```

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
    bool isValidBST(TreeNode* root) {
        return compare(root, LONG_MIN,  LONG_MAX);
    }

    bool compare(TreeNode* root, long long lower, long long upper){
        // 若节点为空，说明要么是最开始传入一个空树，要么是递归到了边界，即叶子结点
        // 此时则返回 true
        if(root == nullptr) return true;
        // 若当前节点的值，小于最小值或者大于最大值，则返回 false
        // 在递归左子树时，最大值是每次递归子树的树根
        // 在递归右子树时，最小值是每次递归子树的树根
        if(root->val <= lower || root->val >= upper) return false;
        return compare(root->left, lower, root->val) && compare(root->right, root->val, upper);
    }
};
```

## [102. 二叉树的层序遍历](https://leetcode.cn/problems/binary-tree-level-order-traversal/)

1. 思路：本题只要掌握了[二叉树的层序遍历](https://blog.csdn.net/qq_34902437/article/details/94012432?csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%2294012432%22%2C%22source%22%3A%22qq_34902437%22%7D)，那么核心思想就知道了，但是针对不同语言，需要利用到各种语言的数据结构（如，队列）的基本操作，所以需要你了解这些数据结构在语言层面上提供的各种 api 函数
2. 完整示例 ```代码随想录题解```，```未手动实现```

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
    vector<vector<int>> levelOrder(TreeNode* root) {
        vector <vector<int>> ret;
        if(root == nullptr) return ret;

        queue<TreeNode *> q;
        q.push(root);
        while(q.empty() == false){
            // 为适应题目需求，这里计算每一层节点的数量
            // 然后一次性处理完一层
            int currentLevelSize = q.size();
            vector<int> vec;
            for(int i= 0;i<currentLevelSize; i++){
                TreeNode* node = q.front();
                q.pop();
                vec.push_back(node->val);
                if(node->left) q.push(node->left);
                if(node->right) q.push(node->right);
            }
            ret.push_back(vec);
        }
    return ret;
    }
};
```

3. 相似题目：[429. N 叉树的层序遍历](https://leetcode.cn/problems/n-ary-tree-level-order-traversal/)

## [236. 二叉树的最近公共祖先](https://leetcode.cn/problems/lowest-common-ancestor-of-a-binary-tree/)

### 思路一：递归

1. 思路：本题将 p，q 的情况分成几种类型，在递归的时候判断这几种类型，若符合即可

   1. 类型一：若 root 为空，则返回空

   1. 类型二：若 root 为 p 或者 q，则返回 p 或者 q，然后递归左右子树
      1. 若右子树返回空，说明 p，q 均在左子树上，则对应的 p 或者 q 就是公共祖先，**反之亦然**![image-20230820142629237](C:\Users\23380\AppData\Roaming\Typora\typora-user-images\image-20230820142629237.png)
      2. 若左右子树均不为空，说明 p，q 在两边的子树上，则二者的 root 为公共祖先

​					![image-20230820142550106](C:\Users\23380\AppData\Roaming\Typora\typora-user-images\image-20230820142550106.png)

2. 完整示例 ```网友题解```

```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    TreeNode* lowestCommonAncestor(TreeNode* root, TreeNode* p, TreeNode* q) {
        // 1. 若 root 为空，则返回空
        if(root == nullptr) return nullptr;
        // 2. 若 root 为 p 或者 q，则返回 root
        if(root == p || root == q) return root;

        TreeNode* treeLeft = lowestCommonAncestor(root->left, p, q);
        TreeNode* treeRight = lowestCommonAncestor(root->right, p, q);

        // 2.1 若左右子树某一棵为空，则返回另一棵，且说明 p，q 均在同一边子树上
        if(treeLeft == nullptr) return treeRight;
        if(treeRight == nullptr) return treeLeft;
        // 若左右子树均不为空，说明 p，q 在两边的子树上，则二者的 root 为公共祖先
        if(treeLeft && treeRight) return root;

        return nullptr;
    }
};
```

3. 时空复杂度
   1. 时间复杂度：可能访问每一个节点，O(n)
   2. 空间复杂度：递归利用栈空间，空间复杂度与树的高度有关，O(n)

## [105. 从前序与中序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-preorder-and-inorder-traversal/)

1. 思路
   1. 先决条件：了解[前序和中序遍历的基本原理](https://blog.csdn.net/qq_34902437/article/details/94012432?csdn_share_tail=%7B%22type%22%3A%22blog%22%2C%22rType%22%3A%22article%22%2C%22rId%22%3A%2294012432%22%2C%22source%22%3A%22qq_34902437%22%7D)，以及[已知前序和中序（或后序和中序）如何求得二叉树的原理](https://whybee.blog.csdn.net/article/details/81291936)
   2. 
2. 注意点：-
3. 完整示例 ```力扣官方题解```，```未手动实现```

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
    TreeNode* buildTree(vector<int>& preorder, vector<int>& inorder) {
        int n = inorder.size();
        // inorder[i] 取得中序遍历中，第 i 个元素对应的值
        // index[inorder[i]] = i，按照键值对的方式组织映射关系
        // 这里的键（key）是实际的数值，而值是数值在数组中的下标
        // 举例：如题目给出的示例 1 中，有中序遍历 [9,3,15,20,7]，
        // 则可以将数值 3 作为键，而它在数组中的下标 1 作为值进行
        // 映射
        for(int i= 0;i<n;i++){
            index[inorder[i]] = i;
        }

        return myBuildTree(preorder, inorder, 0, n-1, 0, n-1);
    }

    TreeNode* myBuildTree(const vector<int>& preorder, const vector<int>& inorder, int preorder_left, int preorder_right, int inorder_left, int inorder_right) {
        if (preorder_left > preorder_right) {
            return nullptr;
        }
        
        // 前序遍历中的第一个节点就是根节点
        int preorder_root = preorder_left;
        // 在中序遍历中定位根节点
        int inorder_root = index[preorder[preorder_root]];
        
        // 先把根节点建立出来
        TreeNode* root = new TreeNode(preorder[preorder_root]);
        // 得到左子树中的节点数目
        int size_left_subtree = inorder_root - inorder_left;
        // 递归地构造左子树，并连接到根节点
        // 先序遍历中「从 左边界+1 开始的 size_left_subtree」个元素就对应了中序遍历中「从 左边界 开始到 根节点定位-1」的元素
        root->left = myBuildTree(preorder, inorder, preorder_left + 1, preorder_left + size_left_subtree, inorder_left, inorder_root - 1);
        // 递归地构造右子树，并连接到根节点
        // 先序遍历中「从 左边界+1+左子树节点数目 开始到 右边界」的元素就对应了中序遍历中「从 根节点定位+1 到 右边界」的元素
        root->right = myBuildTree(preorder, inorder, preorder_left + size_left_subtree + 1, preorder_right, inorder_root + 1, inorder_right);
        return root;
    }

private:
    unordered_map<int, int> index;
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：这里需要两个空间，分别是递归的函数栈的空间 O(h)，h 表示树的高度，以及哈希表的空间 O(n)，h 一定是小于 n 的，所以实际值为 O(h+n)，这里省略小的部分，所以最终为 O(n)
5. 补充与扩展：代码随想录的[视频讲解](https://www.bilibili.com/video/BV1vW4y1i7dn/?vd_source=fc31b4f33de669564b3e17f5585f1253)，视频以 [106. 从中序与后序遍历序列构造二叉树](https://leetcode.cn/problems/construct-binary-tree-from-inorder-and-postorder-traversal/) 为蓝本来讲解的，但是其基本思想适用本题

## [437. 路径总和 III](https://leetcode.cn/problems/path-sum-iii/)

### 思路一：递归

1. 思路：通过穷举的方式，递归遍历每个节点的可能路径
2. 注意点：-
3. 完整示例 ```力扣官方题解```，```未手动实现```

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
    int rootSum(TreeNode* root, long targetSum) {
        if (!root) {
            return 0;
        }

        int ret = 0;
        if (root->val == targetSum) {
            ret++;
        } 

        ret += rootSum(root->left, targetSum - root->val);
        ret += rootSum(root->right, targetSum - root->val);
        return ret;
    }

    int pathSum(TreeNode* root, int targetSum) {
        if (!root) {
            return 0;
        }
        
        int ret = rootSum(root, targetSum);
        // 递归遍历每个节点
        ret += pathSum(root->left, targetSum);
        ret += pathSum(root->right, targetSum);
        return ret;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：因为要对每个节点都求一次它的路径和，所以为 O(n^2^)
   2. 空间复杂度：递归需要开辟栈空间，所以为 O(n)

### 思路二：前缀和

## [337. 打家劫舍 III](https://leetcode.cn/problems/house-robber-iii/)

