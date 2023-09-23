# 前言

1. 排序的基本知识看[这里](https://www.yuque.com/fromdark/yx0hps/zncgtd)

# 中等

## [215. 数组中的第K个最大元素](https://leetcode.cn/problems/kth-largest-element-in-an-array/)

1. 思路：维护动态数据的最值的方法是使用堆排序。在本题中，建立一个大根堆，然后做 k-1 次删除操作后，得到的对顶元素就是答案
2. 注意点：-
3. 完整示例 ```力扣官方题解```

```c++
class Solution {
public:
    int findKthLargest(vector<int>& nums, int k) {
        int heapSize = nums.size();
        buildMaxHeap(nums, heapSize);
        for (int i = nums.size() - 1; i >= nums.size() - k + 1; --i) {
            swap(nums[0], nums[i]);
            --heapSize;
            maxHeapify(nums, 0, heapSize);
        }
        return nums[0];
    }

    void buildMaxHeap(vector<int>& a, int heapSize){
        for(int i = heapSize / 2; i >= 0; --i){
            maxHeapify(a, i, heapSize);
        }
    }

    void maxHeapify(vector<int>& a, int i, int heapSize){
        int l = i * 2 + 1;
        int r = i * 2 + 2;
        int largest = i;
        if (l < heapSize && a[l] > a[largest]) {
            largest = l;
        } 
        if (r < heapSize && a[r] > a[largest]) {
            largest = r;
        }
        if (largest != i) {
            swap(a[i], a[largest]);
            maxHeapify(a, largest, heapSize);
        }
    }

};
```

4. 时空复杂度
   1. 时间复杂度：O(nlogn)
   2. 空间复杂度：O(logn)