# 前言

1. 数组常用的算法技巧：双指针，二分查找，前缀和，差分数组

# 简单

## [704. 二分查找](https://leetcode.cn/problems/binary-search/)

1. 思路：本题为一道非常典型的二分查找题，只要掌握基本的原理，并注意边界条件上的细节，配合 debug，就能轻松完成
2. 注意点：格外注意边界条件，如 while(left <= right) 还是 while(left < right) 等，这里附上一篇[文章](https://www.cnblogs.com/kyoner/p/11080078.html)，对此有非常详细的讨论
3. 完整示例

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        int left = 0;
        int right = nums.size() - 1;

        while(left <= right){
            int mid = (left + right) / 2;
            if(nums[mid] == target) return mid;
            else if(nums[mid] > target) right = mid - 1;
            else if(nums[mid] < target) left = mid + 1;
        }

        return -1;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)
5. 补充与扩展：-

## [27. 移除元素](https://leetcode.cn/problems/remove-element/)

1. 思路：如图所示，当检测到 nums[i] == val 时，我们就将数组中后续剩余的元素前移，同时 numsSize 的长度减少一个，当遇到 i 遍历到最后的时候退出，若 i 遍历到最后时，恰巧最后一个元素等于 val，则可以直接 return --numsSize

​		![](.\img\array\remove.png)

1. 注意点：-
2. 完整示例

```C
int removeElement(int* nums, int numsSize, int val){
    for(int i = 0; i < numsSize; i++)
    {
        if(nums[i] == val)
        {
            // 若 i 遍历到当前数组最后一个，且最后一个值等价于 val
            // 则直接返回当前数组长度减一
            if(i == numsSize - 1) return --numsSize;
            // 后续数组的值覆盖前面需要被替换的（即 nums[i] == val）
            for(int j = i; j < numsSize - 1; j++)
            {
                nums[j] = nums[j+1];
            }
            // 每次循环从 i = 0，即数组开头开始
            // 又因为 for 循环有 i++ 操作
            // 所以设置 i = -1，这样经过 i++ 后就成为了 i = 0
            i = -1;
            numsSize--;
        }
    }
    return numsSize;
}
```

## [977. 有序数组的平方](https://leetcode.cn/problems/squares-of-a-sorted-array/)

### 思路一：直接排序

1. 思路：这种方式就是先把各个位的数进行平方，然后利用某种基本排序算法解决，不过这种方式虽然能够 AC，但未达到题目的**进阶要求**

### 思路二：双指针

1. 思路：这种方式是定义两个指针（当然不一定真的是指针，就是说同时操作数组中的两个数值进行比较），其中一个指针指向开头，另一个指针指向数组末尾，利用题目给出的序列的性质进行求解

   ![](.\img\array\array_square.png)

2. 注意点：该方法，我们需要使用第二个 vector，用来存放每次比较的较大值

3. 完整示例

```c++
class Solution {
public:
    vector<int> sortedSquares(vector<int>& nums) {
        int n = nums.size();
        vector<int> ans(n);
        for (int i = 0, j = n - 1, pos = n - 1; i <= j;) {
            if (nums[i] * nums[i] > nums[j] * nums[j]) {
                ans[pos] = nums[i] * nums[i];
                ++i;
            }
            else {
                ans[pos] = nums[j] * nums[j];
                --j;
            }
            --pos;
        }
        return ans;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)，仅需要额外开辟一个常量的新的 vector 空间

# 中等

## [209. 长度最小的子数组](https://leetcode.cn/problems/minimum-size-subarray-sum/)

1. 思路：本题的思路有多种，最简单的是暴力破解，但是目前会出现超时的现象。另一种解法叫做滑动窗口，关于滑动窗口的基本原理看[这里](https://leetcode.cn/problems/minimum-size-subarray-sum/solutions/305704/chang-du-zui-xiao-de-zi-shu-zu-by-leetcode-solutio/)
2. 注意点：-
3. 完整示例

```C++
class Solution {
public:
    int minSubArrayLen(int target, vector<int>& nums) {
        int n = nums.size();
        if(n == 0){
            return 0;
        }

        int minLength = INT_MAX;
        int start = 0;
        int end = 0;
        int tempSum = 0;
        while(end < n){
            tempSum += nums[end];
            while(tempSum >= target){
                minLength = min(minLength, end - start + 1);
                tempSum -= nums[start];
                start++;
            }
            end++;
        }

        if(minLength == INT_MAX) return 0;
        else return minLength;

    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)，这里虽然是两个嵌套的循环，但是两个循环的总体走向仅遍历了一遍
   2. 空间复杂度：O(1)

## [59. 螺旋矩阵 II](https://leetcode.cn/problems/spiral-matrix-ii/)

1. 思路：本题为模拟题，不涉及算法，即只需要小心认真的处理边界条件，按照基本的思路书写就行
2. 注意点：边界条件的处理，注意循环条件固定，确定一种边界处理方案就不要动
3. 完整示例 ```代码随想录题解```，```未手动实现```

```C++
class Solution {
public:
    vector<vector<int>> generateMatrix(int n) {
        vector<vector<int>> res(n, vector<int>(n, 0)); // 使用vector定义一个二维数组
        int startx = 0, starty = 0; // 定义每循环一个圈的起始位置
        int loop = n / 2; // 每个圈循环几次，例如n为奇数3，那么loop = 1 只是循环一圈，矩阵中间的值需要单独处理
        int mid = n / 2; // 矩阵中间的位置，例如：n为3， 中间的位置就是(1，1)，n为5，中间位置为(2, 2)
        int count = 1; // 用来给矩阵中每一个空格赋值
        int offset = 1; // 需要控制每一条边遍历的长度，每次循环右边界收缩一位
        int i,j;
        while (loop --) {
            i = startx;
            j = starty;

            // 下面开始的四个for就是模拟转了一圈
            // 模拟填充上行从左到右(左闭右开)
            for (j = starty; j < n - offset; j++) {
                res[startx][j] = count++;
            }
            // 模拟填充右列从上到下(左闭右开)
            for (i = startx; i < n - offset; i++) {
                res[i][j] = count++;
            }
            // 模拟填充下行从右到左(左闭右开)
            for (; j > starty; j--) {
                res[i][j] = count++;
            }
            // 模拟填充左列从下到上(左闭右开)
            for (; i > startx; i--) {
                res[i][j] = count++;
            }

            // 第二圈开始的时候，起始位置要各自加1， 例如：第一圈起始位置是(0, 0)，第二圈起始位置是(1, 1)
            startx++;
            starty++;

            // offset 控制每一圈里每一条边遍历的长度
            offset += 1;
        }

        // 如果n为奇数的话，需要单独给矩阵最中间的位置赋值
        if (n % 2) {
            res[mid][mid] = count;
        }
        return res;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n^2^)
   2. 空间复杂度：O(1)
5. 补充与扩展：[代码随想录视频解答](https://www.bilibili.com/video/BV1SL4y1N7mV/?vd_source=fc31b4f33de669564b3e17f5585f1253)