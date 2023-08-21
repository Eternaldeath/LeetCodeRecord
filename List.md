# 前言

1. 链表常用的算法技巧：双指针

# 简单

## [160 相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/)

思路：循环遍历法。假设有 A，B 链，以及 PA 和 PB 指向 A，B 链头结点，PB 先固定不动，PA 循环，比较 PA 与 PB，循环完一轮（即无匹配），则 PB 移动到下一个位置，PA 重新从头开始匹配。后面的重复这样的操作，直到找到匹配项，或者 A，B 链不想交。

```C++
class Solution {
public:
    ListNode *getIntersectionNode(ListNode *headA, ListNode *headB) {
        if(headA == NULL || headB == NULL) return NULL;

        ListNode *pA = headA;
        ListNode *pB = headB;

        while(pA != pB && pA != NULL && pB != NULL)
        {
            pA = pA->next;
            if(pA==NULL)
            {
                pA = headA;
                pB = pB->next;
                if(pB == NULL) return NULL;
            }
        }

        return pA;
    }
};
```

## [21. 合并有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/)

1. 思路：本题我采用迭代的方式：
   1. 设置 Prev 为迭代变量
   2. list1 或者 list2 为迭代终止条件
   3. 迭代关系式

```C++
while(list1 != nullptr || list2 != nullptr)
        {
            if(list1->val < list2->val)
            {
                Prev->next = list1;
                list1 = list1->next;
            }
            else
            {
                Prev->next = list2;
                list2 = list2->next;
            }
            Prev = Prev->next;
        }
```
完整示例
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* mergeTwoLists(ListNode* list1, ListNode* list2) {
        // 特殊情况处理
        // 均为空链表
        if(list1 == nullptr && list2 == nullptr) return nullptr;
        // 其中一个链表为空
        if(list1 == nullptr) return list2;
        if(list1 != nullptr) return list1;

        // 始终指向头节点
        ListNode *Head = new ListNode(-1);
        ListNode *Prev = Head;

        while(list1 != nullptr && list2 != nullptr)
        {
            if(list1->val < list2->val)
            {
                Prev->next = list1;
                list1 = list1->next;
            }
            else
            {
                Prev->next = list2;
                list2 = list2->next;
            }
            Prev = Prev->next;
        }

        Prev->next = (list1 == nullptr) ? list2 : list1;
        
        return Head->next;
    }
};
```

## [206. 反转链表](https://leetcode.cn/problems/reverse-linked-list/)

1. 思路：本题我采用迭代的方式：
   1. 设置 Cur 为迭代变量
   2. Cur ！= nullptr 为迭代终止条件
   3. 以及如下的迭代关系式

```c++
Cur->next = Prev;
Prev = Cur;
Cur = Next;
if(Cur != nullptr) Next = Cur->next;
```
2. 第一步图解

   ![](.\img\list\reversal_list.png)

   3. 完整代码

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* reverseList(ListNode* head) {
        // 特殊情况判断
        // 空链或链表仅有一个元素
        if(head == nullptr || head->next == nullptr) 
        {
            return head;
        }

        ListNode *Prev = nullptr;
        ListNode *Cur = head;
        ListNode *Next  = Cur->next;

        while(Cur != nullptr)
        { 
            Cur->next = Prev;
            Prev = Cur;
            Cur = Next;
            if(Cur != nullptr) Next = Cur->next;
        }

        head = Prev;
        return head;
    }
};
```

上面的代码中，我们使用了专门的 Next，但实际上也可以使用临时的 Next 指针来指示 Cur 的下一个位置，好处是让代码更简洁，如下所示

```C++
ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr != nullptr) {
            ListNode* nextTemp = curr->next; // 临时的 Next 指针，这里命名为 nextTemp
            curr->next = prev;
            prev = curr;
            curr = nextTemp;
        }
        return prev;
    }
```

## [141. 环形链表](https://leetcode.cn/problems/linked-list-cycle/)

1. 思路：本题采用迭代法作为基本思想，每访问一个节点时，将 val 修改为一个特殊值，比如本题中系统给出的 val 取值范围在 [-10^5^, 10^5^]，那么我们选择 100001 作为范围外的特殊值。

   当通过 next 指针访问到特殊值时，说明存在环形，否则当遍历到 NULL 的时候，说明链表遍历完成，没有环形

2. 时空复杂度：满足题目进阶要求
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)
3. 完整示例

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    bool hasCycle(ListNode *head) {
        ListNode *cur = head;

        while(cur != nullptr)
        {
            if(cur->val == 100001) return true;

            cur->val = 100001;
            cur      = cur->next;
        }

        return false;
    }
};
```

3. 其他思路：哈希表，快慢指针

## [234. 回文链表](https://leetcode.cn/problems/palindrome-linked-list/)

1. 思路
   1. 找到前半部分链表的尾节点：这一步利用快慢指针，当快指针到达链表尾时，慢指针到达链表中间部分
   2. 反转后半部分链表：利用**反转链表**章节的方法即可
   3. 判断是否回文
   4. 恢复链表
   5. 返回结果
2. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)
3. 完整示例：```力扣官方题解```，```未手动实现```

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    bool isPalindrome(ListNode* head) {
        // 特殊情况判断
        if(head == nullptr) return true;

        // 1. 找到前半部分表尾
        ListNode *firstHalfEnd = endOfFirstHalf(head);

        // 2. 反转后半部分链表
        ListNode *secondHalfStart = reverseList(firstHalfEnd->next);

        // 3. 判断是否回文
        ListNode *p1 = head;
        ListNode *p2 = secondHalfStart;
        bool result = true;
        while(result && p2 != nullptr)
        {
                        if (p1->val != p2->val) {
                result = false;
            }
            p1 = p1->next;
            p2 = p2->next;
        }

        return result;
    }

    ListNode* reverseList(ListNode* head) {
        ListNode* prev = nullptr;
        ListNode* curr = head;
        while (curr != nullptr) {
            ListNode* nextTemp = curr->next;
            curr->next = prev;
            prev = curr;
            curr = nextTemp;
        }
        return prev;
    }

    ListNode* endOfFirstHalf(ListNode* head) {
        ListNode* fast = head;
        ListNode* slow = head;
        while (fast->next != nullptr && fast->next->next != nullptr) {
            fast = fast->next->next;
            slow = slow->next;
        }
        return slow;
    }
};
```

# 中等

## [2. 两数相加](https://leetcode.cn/problems/add-two-numbers/)

1. 思路：A，B 链（即需要相加的链）可以直接对位让每一个节点相加，相加的值若有进位，通过 ```carry``` 标志位标识进位的值，所以每一次求和链上的节点值的计算为 = ai + bi + carry，其中 ai 和 bi 为 A，B 链上的节点
2. 注意点
   1. 最后一次若依然有 carry 进位，则需要额外填充该值
   2. 若 A，B 链长度不一样，用 0 来填充
   3. 此题不适合将 A，B 链转换成具体的**整型数值**，因为在测试用例中有**大数**出现（即，用 long long 长度也远远无法表示的大数）
3. 时空复杂度
   1. 时间复杂度：O(max(A, B))，即 A，B 链的长度
   2. 空间复杂度：O(1)
4. 完整示例```力扣官方题解```，```未手动实现```

```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        // head 指向和链的头
        // sumLink 指向和链的每一次步进
        ListNode *head = nullptr, *sumLinkCur = nullptr;
        int carry = 0; // 进位

        while (l1 || l2) {
            // 若相加链的长度不一样，则用 0 填补短链
            int n1 = l1 ? l1->val: 0;
            int n2 = l2 ? l2->val: 0;
            int sum = n1 + n2 + carry;
            if (!head) {
                // 创建和链，让 head 和 sumLinkCur 指向首元节点
                head = sumLinkCur = new ListNode(sum % 10);
            } else {
                // 以后每次用 sumLinkCur 步进，增加新节点
                sumLinkCur->next = new ListNode(sum % 10);
                sumLinkCur = sumLinkCur->next;
            }
            carry = sum / 10; // 求进位
            if (l1) {
                l1 = l1->next;
            }
            if (l2) {
                l2 = l2->next;
            }
        }
        // 若最后一次进位存在，则补充最后一次进位的值到和链中
        if (carry > 0) {
            sumLinkCur->next = new ListNode(carry);
        }
        return head;
    }
};
```

## [19. 删除链表的倒数第 N 个结点](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/)

1. 思路：两次循环，第一次获取链表的长度，然后根据要被删除的节点的倒序位置得到正序位置，然后第二次遍历，找到要被删除的节点

2. 注意点：自己添加头结点，否则若被删除的节点是第一个，则无法操作

3. 时空复杂度

   1. 时间复杂度：O(n + m)，n 为链表长度，m 为被删除节点的正序位置
   2. 空间复杂度：O(1)

4. 完整示例
```C++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode() : val(0), next(nullptr) {}
 *     ListNode(int x) : val(x), next(nullptr) {}
 *     ListNode(int x, ListNode *next) : val(x), next(next) {}
 * };
 */
class Solution {
public:
    ListNode* removeNthFromEnd(ListNode* head, int n) {
        ListNode *headNode = new ListNode(-1);
        headNode->next = head;

        ListNode *del = headNode;
        ListNode *pLength = headNode;
        int linkLength = 0;
        int delOrder = 0;

        while(pLength != nullptr)
        {
            linkLength++;
            pLength = pLength->next;
        }

        // 特殊情况处理
        if((linkLength-1) == 1) return nullptr;

        delOrder = linkLength - n + 1;

        delOrder--;
        while(delOrder!=1) 
        {
            delOrder--;
            del = del->next;
        }

        del->next = del->next->next;

        return headNode->next;
    }
};   
```

 ## [114. 二叉树展开为链表](https://leetcode.cn/problems/flatten-binary-tree-to-linked-list/)

1. 思路：新建一个 TreeNode 指针类型 vector，用来按顺序存储含有值的节点。通过前序遍历的方式把二叉树的节点都放到 vector 中。题目要求形成单链表，但又要是 TreeNode 类型的，我觉得这里题目表述的有问题，它实际上的含义是把所有节点都依序放在二叉树每个节点的右子树上，如下图

![](.\img\list\flatten-binary-tree-to-linked-list.png)

2. 完整示例```力扣官方题解```，```未手动实现```

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
    void flatten(TreeNode* root) {
       vector<TreeNode*> l; // TreeNode 类型的 vector
        preorderTraversal(root, l);
        int n = l.size();
        // 为 vector 补全二叉树结构
        // 因为要求的还是以 TreeNode 的数据类型来构成链表
        // 所以实际上就是把所有节点依次排在右子树上
        for (int i = 1; i < n; i++) {
            TreeNode *prev = l.at(i - 1), *curr = l.at(i);
            prev->left = nullptr;
            prev->right = curr;
        }
    }

        void preorderTraversal(TreeNode* root, vector<TreeNode*> &l) {
        if (root != NULL) {
            l.push_back(root);
            preorderTraversal(root->left, l);
            preorderTraversal(root->right, l);
        }
    }
};
```

## [142. 环形链表 II](https://leetcode.cn/problems/linked-list-cycle-ii/)

1. 思路

   1. 判断相遇：采用快慢指针的方式，快指针每次移动 2 个节点，慢指针每次移动一个节点，二者走过的距离存在如下关系式 ```fastLength = slowLength```，且当快慢指针相遇时，说明存在环路
   2. 判断入环节点的位置：如下图所示
      1. 快指针走过的距离是 a + n * ( b + c ) +b
      2. 慢指针走过的距离是 a + b
      3. 由 1 可知，二者的关系是 a + n * ( b + c ) +b = 2 * ( a + b)，化简该式子可得，a = c + (n-1) * (b + c)，该式子表明，从链头到入环点的距离等价于从相遇点到入环点再加上 n-1 圈的环

   ![fig1](https://assets.leetcode-cn.com/solution-static/142/142_fig1.png)

2. 进阶要求：使用 `O(1)` 空间解决此题
3. 完整示例```力扣官方题解```，```符合进阶要求```

```c++
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode *detectCycle(ListNode *head) {
        ListNode *fast = head, *slow = head;

        while(fast != nullptr)
        {
            slow = slow->next;
            if(fast->next == nullptr) return nullptr;
            fast = fast->next->next;
            if(fast == slow)
            {
                ListNode *ptr = head;
                while(ptr != slow)
                {
                    ptr = ptr->next;
                    slow = slow->next;
                }

                return ptr;
            }
        }
        return nullptr;
    }
};
```

## [148. 排序链表](https://leetcode.cn/problems/sort-list/)

1. 思路
   1. 本题要求的时间复杂度是 O(nlogn)，如果用类似冒泡的方式（时间复杂度为O(n^2^)）,则超出了时间限制，无法 AC 成功
   2. 那么既然是 O(nlogn) 的复杂度，则根据排序算法的时间复杂度规律，有归并，堆排和快排可以实现要求，这里采用归并是最合适的方式
   3. 归并的思路在这里不讲述，归并中，我们采用一个额外的数组来完成此操作，而这里是链表，其实大致的方式是相同的

【注】[二路归并排序](https://zhuanlan.zhihu.com/p/62076299)
