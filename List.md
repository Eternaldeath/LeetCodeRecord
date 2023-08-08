# 相交链表

| [160. 相交链表](https://leetcode.cn/problems/intersection-of-two-linked-lists/) | 简单 |
| ------------------------------------------------------------ | ---- |

## 我的解法

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

## 标准解法

1. [代码随想录](https://programmercarl.com/%E9%9D%A2%E8%AF%95%E9%A2%9802.07.%E9%93%BE%E8%A1%A8%E7%9B%B8%E4%BA%A4.html#%E6%80%9D%E8%B7%AF)

# 合并有序链表

| [21. 合并两个有序链表](https://leetcode.cn/problems/merge-two-sorted-lists/) | 简单 |
| ------------------------------------------------------------ | ---- |

