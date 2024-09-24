# 前言

1. 数组常用的算法技巧：[双指针](https://www.yuque.com/fromdark/yx0hps/ymei734s1es050v6)，[二分查找](https://www.yuque.com/fromdark/yx0hps/kvi75l#IxtCw)，前缀和，差分数组

# 简单

## [283. 移动零](https://leetcode.cn/problems/move-zeroes/)

1. 思路
   1. 使用两个指针，这里用 i，j 表示，其中 i 用来移动非零数组元素，j 每次移动一格，用来表示当前被覆盖的数组位置
   2. 第一遍，我们从前往后遍历数组，将所有非零的元素按序移动到数组左侧，最终 j 所在的位置就是非零元素和零元素的分界点
   3. 第二遍，我们从 j 元素开始，将后续的元素赋值为 0，表示将零元素移动到后面
2. 注意点：最开始我设想按照上述逻辑，可能有同学误以为一遍也可以完成，但是实际上不行，大家可以动手模拟一下 [1,0] 的情况
3. 完整示例

```c++
class Solution {
public:
    void moveZeroes(vector<int>& nums) {
        if(nums.size() == 0) return ;

        int j = 0;
        for(int i = 0; i < nums.size(); i++){
            if(nums[i] != 0){
                nums[j] = nums[i];
                j++;
            }
        }

        for(int i = j; i < nums.size(); i++){
            nums[i] = 0;
        }
    }
};
```

4. 时间复杂度：O(n)；空间复杂度：O(1)

## [303. 区域和检索 - 数组不可变](https://leetcode.cn/problems/range-sum-query-immutable/)

1. 思路：采用前缀和算法，将前缀和存入一个数组中，当需要求某个区间的和时，利用 ```preSum[right + 1] - preSum[left]``` 数学逻辑来求得结果
2. 注意点：preSum 即前缀和数组的大小为输入数组大小 + 1
3. 完整示例 ```算法小抄题解```

```c++
class NumArray {
public:
    NumArray(vector<int>& nums) {
        preSum.resize(nums.size() + 1);
        preSum[0] = 0;

        for(int i = 1; i < preSum.size(); i++){
            preSum[i] = preSum[i - 1] + nums[i - 1];
        }
    }
    
    int sumRange(int left, int right) {
        return preSum[right + 1] - preSum[left];
    }
private:
    vector<int> preSum;
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)

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

## [15. 三数之和](https://leetcode.cn/problems/3sum/)

1. 思路一：本题可以采用哈希法，但是哈希法在解决去重的问题上时有很多小细节，且整体效率不高，你可以在[这里](https://programmercarl.com/0015.%E4%B8%89%E6%95%B0%E4%B9%8B%E5%92%8C.html#%E6%80%9D%E8%B7%AF)阅读哈希法的解法

2. 思路二：采用双指针

   1. 对整个数组进行排序

   2. 我们根据逻辑 a + b + c = 0，推导出 0 - (b + c) = a

   3. 分别用 i 指向当前执行的元素（其中对 i 有一个 for 循环），我们用 a 表示当前执行的元素，left 指向 b，right 执行 c

   4. 然后每一轮对 i 的 for 循环中，按照如下原则进行小循环（内循环），特别注意，这是已经按照从小打到排序完成的数组

      1. i + left + right > 0，说明值大了，于是 right--
      2. i + left + right < 0，说明值小了，于是 left++
      3. i + left + right = 0，则存入返回结果中。然而这里又有一个难点，就是题目要求进行去重操作，看下面

   5. 去重是本题的难点，假设有 nums = [-4, -1, -1, 0, 1, 2] 已经排序完成的数组，当 i = 1 轮循环时，可以得到 [-1, 1, 2] 满足题目条件，当 i = 2 轮循环时，发现又有 [-1, 1, 2] 满足条件，此时需要去重。去重的逻辑是这样的 nums[i] == nums[i-1] 则跳过本次循环，因为我们是排序好的数组，所以相同元素一定相邻，而相同元素一定会重复，所以排除。

      那么另一个问题是，为什么不能是 nums[i] == nums[i+1] 时跳过呢，这种判断条件是决定的内部元素去重，而不是元组去重，大家可以手动计算一边就知道了。

      而且是对 a，b，c 三个元素都按照这个逻辑去进行去重处理。

3. 完整示例 ```代码随想录题解```，```未手动实现```

```c++
class Solution {
public:
    vector<vector<int>> threeSum(vector<int>& nums) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        // 找出a + b + c = 0
        // a = nums[i], b = nums[left], c = nums[right]
        for (int i = 0; i < nums.size(); i++) {
            // 排序之后如果第一个元素已经大于零，那么无论如何组合都不可能凑成三元组，直接返回结果就可以了
            if (nums[i] > 0) {
                return result;
            }
            // 错误去重a方法，将会漏掉-1,-1,2 这种情况
            /*
            if (nums[i] == nums[i + 1]) {
                continue;
            }
            */
            // 正确去重a方法
            if (i > 0 && nums[i] == nums[i - 1]) {
                continue;
            }
            int left = i + 1;
            int right = nums.size() - 1;
            while (right > left) {
                // 去重复逻辑如果放在这里，0，0，0 的情况，可能直接导致 right<=left 了，从而漏掉了 0,0,0 这种三元组
                /*
                while (right > left && nums[right] == nums[right - 1]) right--;
                while (right > left && nums[left] == nums[left + 1]) left++;
                */
                if (nums[i] + nums[left] + nums[right] > 0) right--;
                else if (nums[i] + nums[left] + nums[right] < 0) left++;
                else {
                    result.push_back(vector<int>{nums[i], nums[left], nums[right]});
                    // 去重逻辑应该放在找到一个三元组之后，对b 和 c去重
                    while (right > left && nums[right] == nums[right - 1]) right--;
                    while (right > left && nums[left] == nums[left + 1]) left++;

                    // 找到答案时，双指针同时收缩
                    right--;
                    left++;
                }
            }

        }
        return result;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n^2^)
   2. 空间复杂度：O(n)

## [18. 四数之和](https://leetcode.cn/problems/4sum/)

1. 思路：本题同样采用和 15 题一样的思路，区别在于三数之和是一层 for 循环 + left 和 right 指针。而本题采用两层 for 循环 + left 和 right 指针
2. 注意点：可以看到，类似这种 n 数之和的解法，就是把暴力破解的 O(n^n^) 时间复杂度，降为 O(n^n-1^) 时间复杂度
3. 完整示例```代码随想录题解```，```未手动实现```

```c++
class Solution {
public:
    vector<vector<int>> fourSum(vector<int>& nums, int target) {
        vector<vector<int>> result;
        sort(nums.begin(), nums.end());
        for (int k = 0; k < nums.size(); k++) {
            // 剪枝处理
            if (nums[k] > target && nums[k] >= 0) {
            	break; // 这里使用break，统一通过最后的return返回
            }
            // 对nums[k]去重
            if (k > 0 && nums[k] == nums[k - 1]) {
                continue;
            }
            for (int i = k + 1; i < nums.size(); i++) {
                // 2级剪枝处理
                if (nums[k] + nums[i] > target && nums[k] + nums[i] >= 0) {
                    break;
                }

                // 对nums[i]去重
                if (i > k + 1 && nums[i] == nums[i - 1]) {
                    continue;
                }
                int left = i + 1;
                int right = nums.size() - 1;
                while (right > left) {
                    // nums[k] + nums[i] + nums[left] + nums[right] > target 会溢出
                    if ((long) nums[k] + nums[i] + nums[left] + nums[right] > target) {
                        right--;
                    // nums[k] + nums[i] + nums[left] + nums[right] < target 会溢出
                    } else if ((long) nums[k] + nums[i] + nums[left] + nums[right]  < target) {
                        left++;
                    } else {
                        result.push_back(vector<int>{nums[k], nums[i], nums[left], nums[right]});
                        // 对nums[left]和nums[right]去重
                        while (right > left && nums[right] == nums[right - 1]) right--;
                        while (right > left && nums[left] == nums[left + 1]) left++;

                        // 找到答案时，双指针同时收缩
                        right--;
                        left++;
                    }
                }

            }
        }
        return result;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n^3^)
   2. 空间复杂度：O(1)

## [48. 旋转图像](https://leetcode.cn/problems/rotate-image/)

1. 思路：本题是一道模拟类型的题目，也就是不涉及算法，但是需要用代码模拟图像旋转的过程
2. 注意点
   1. 本题的难点在于题目要求原地操作，在手动模拟的过程中我们知道，直接旋转会导致覆盖问题，使得还没有被使用的元素被覆盖掉，而后续就无法使用了，其实也很好解决，就是利用一个临时变量来存储，并且我们一次 for 循环中模拟的旋转，一次就把四个（假设 size 为 4）旋转过程都模拟完

![](.\img\array\48_01.png)

   2. 第二个注意点是每轮循环的终止条件。我们循环的思路是行循环为外层大循环，列循环为内层循环，实际上可以看到，在一次内循环的时候，多行多列都被处理到了，那么我们的内外循环应该处理到哪一步呢？实际上它们存在如下的逻辑关系，并且这个循环判定考虑到了 `size` 大小为偶数和奇数两种情况

      ![](.\img\array\48_02.png)

      ```c++
      for(int i = 0; i < n / 2; ++i){
          for(int j = 0; j < (n+1) / 2; ++j){
              // ... 
          }
      }
      ```

   3. 完整示例

```c++
class Solution {
public:
    void rotate(vector<vector<int>>& matrix) {
        int n = matrix.size();
        for(int i = 0; i < n / 2; ++i){
            for(int j = 0; j < (n+1) / 2; ++j){
                int temp = matrix[i][j];
                matrix[i][j] = matrix[n - j - 1][i];
                matrix[n - j - 1][i] = matrix[n - i - 1][n - j - 1];
                matrix[n - i - 1][n - j - 1] = matrix[j][n - i - 1];
                matrix[j][n - i - 1] = temp;
            }
        }
    }
};
```

4. 时间复杂度：O(n^2^)；空间复杂度：O(1)

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

## [240. 搜索二维矩阵 II](https://leetcode.cn/problems/search-a-2d-matrix-ii/)

1. 思路：对矩阵的每一行执行二分法操作
2. 注意点：-
3. 完整示例

```c++
class Solution {
public:
    bool searchMatrix(vector<vector<int>>& matrix, int target) {

        for(int i = 0; i < matrix.size(); i++){
            int left = 0;
            int right = matrix[i].size() - 1;


            while(left <= right){
                int mid = (left + right)/2;

                if(matrix[i][mid] == target) return true;
                else if(matrix[i][mid] > target){
                    right = mid - 1;
                }else if(matrix[i][mid] < target){
                    left = mid + 1;
                }
            }
        }

        return false;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(mlogn)，其中 m 表示 m 行，logn 表示每行执行二分查找的时间为 logn
   2. 空间复杂度：O(1)

## [304. 二维区域和检索 - 矩阵不可变](https://leetcode.cn/problems/range-sum-query-2d-immutable/)

1. 思路：看[这里](https://www.yuque.com/fromdark/yx0hps/dom2630p08gzk65g/edit?toc_node_uuid=340816%3An2IYP8vgmBc0m5MV)

## [33. 搜索旋转排序数组](https://leetcode.cn/problems/search-in-rotated-sorted-array/)

1. 思路：我的思路是，先顺序遍历，找到旋转点，同时顺便搜索旋转的前半部分（一定是有序的）。若 target 不在前半部分，则利用二分法搜索后半部分
2. 注意点：-
3. 完整示例

```c++
class Solution {
public:
    int search(vector<int>& nums, int target) {
        // 特殊情况处理
        if(nums.size() == 1) return nums[0] == target ? 0 : -1;

        int mid = 0, left = 0, right = 0;
        int arrSize = nums.size() - 1;

        // 找到旋转点
        // 顺便搜索旋转后的前半部分
        for(int i = 1; i < nums.size(); i++){
            if(nums[i-1] == target) return i-1;
            if(nums[i-1] > nums[i]){
                mid = i;
                break;
            }
        }

        // 再用二分法搜索旋转的后半部分
        if(nums[mid] == target) return mid;
        else if(nums[mid] < target && nums[arrSize]){
            left = mid; right = arrSize;
        }else return -1;

        while(left <= right){
            mid = (left + right) / 2;
            if(nums[mid] == target) return mid;
            else if(nums[mid] < target) left = mid + 1;
            else if(nums[mid] > target) right = mid -1;
        }

        return -1;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(logn)，但最坏情况为 O(n)
   2. 空间复杂度：O(1)

## [34. 在排序数组中查找元素的第一个和最后一个位置](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/)

1. 思路：本题是二分查找的变体，即所谓的左右边界的二分查找，它不是去查找一个值，而是数组中重复值的左右边界，我会在拓展与补充部分添加一个写得比较好的文章的链接
2. 注意点：-
3. 完整示例

```c++
class Solution {
public:
    vector<int> searchRange(vector<int>& nums, int target) {
        vector<int> res(2, -1);
        // 特殊情况处理
        if(nums.size() == 0) return res;
        if(nums.size() == 1){
            if(nums[0] == target){
                res[0] = 0;
                res[1] = 0;
            }

            return res;
        }

        res[0] = left_bound(nums, target);
        res[1] = right_bound(nums, target);

        return res;
    }

    int left_bound(vector<int>& nums, int target){
        int left = 0;
        int right = nums.size() - 1;

        while(left <= right){
            int mid = (left + right) / 2;

            if(nums[mid] == target) right = mid - 1;
            else if(nums[mid] > target) right = mid - 1;
            else if(nums[mid] < target) left = mid + 1;
        }

        if (left < 0 || left >= nums.size()) return -1;
        return nums[left] == target ? left : -1;
    }

    int right_bound(vector<int>& nums, int target){
        int left = 0;
        int right = nums.size() - 1;

        while(left <= right){
            int mid = (left + right) / 2;

            if(nums[mid] == target) left = mid + 1;
            else if(nums[mid] > target) right = mid - 1;
            else if(nums[mid] < target) left = mid + 1;
        }

        if (right < 0 || right >= nums.size()) return -1;
        return nums[right] == target ? right : -1;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(logn)
   2. 空间复杂度：O(1)
5. 拓展与补充：[我写了首诗，把二分搜索算法变成了默写题](https://labuladong.github.io/algo/di-ling-zh-bfe1b/wo-xie-le--3c789/)

## [560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

1. 思路：本题主要思路的前缀和 + 哈希表。具体逻辑是这样的，我们知道前缀和若要求 [a, b] 区间的和 k，则存在关系式 `k = preSum[b] - preSum[a-1]`。

   在本题中，我们已知和为 k，且将 preSum[b] 视为固定累加的值，其实要求的是 preSum[b-1] 出现的次数，即这样的关系式 `preSum[a-1] = preSum[b] - k`。

   同时，将 preSum[b] 放入哈希表中，因为 preSum[b]（b 从第一个下标开始向右运动）一定计算了所有前缀和，在每一次求 preSum[a-1] 时，直接从哈希表中寻找（时间复杂度为 O(1)），而且整个过程只有一次从左向右的移动，所以整体时间复杂度为 O(n)

2. 注意点：`mp[0] = 1` 是为了统计刚好有 `preSum[i] == k` 的情况，具体见本题官方题解下面的回答中

3. 完整示例 `力扣官方题解`

```c++
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        unordered_map<int, int> mp;
        mp[0] = 1;
        int count = 0;
        int pre = 0;

        for(auto& x:nums){
            pre += x;
            if(mp.find(pre - k) != mp.end()) count += mp[pre - k];

            mp[pre]++;
        }
        return count;
    } 
};
```

4. 时空复杂度
   1. 时间复杂度：O(1)
   2. 空间复杂度：O(n)

# 困难

[76. 最小覆盖子串](https://leetcode.cn/problems/minimum-window-substring/)

1. 标签：滑动窗口，双指针
2. 思路
   1. 设置两个 map，need 用来记录 t，也就是子串的字符，window 用来记录当前窗口中满足 t 的每个字符的个数（记住它是一个 map）。valid 用来记录 window 中符合要求的子串字符的个数
   2. 当 right < s.size() 也就是右指针没有到原串 s 的尾巴时，right 窗口递增
   3. 当每次移入的字符满足要求时，window 和 valid 自增
   4. 当 valid 的大小等于 need.size() 时，说明当前窗口已经包含了子串的所有字符。接着收缩 left，用来找到最优的情况
3. 完整示例 `参考算法小抄`

```c++
class Solution {
public:
    string minWindow(string s, string t) {
        // need 存放 t 的映射
        // window 存放当前窗口中，含有 t 子串字符的个数，可能有重复的，如 ABB 中，BB 就在 window 中重复了
        unordered_map<char, int> need, window;
        // 将 t 中的字符映射到 need 中，每个字符（键）对应的数量（值）初始化为 1
        for(char c : t) need[c]++;

        int start = 0;     // 子串起始位置
        int len = INT_MAX; // 子串长度
        int valid = 0;     // window 中符合 need 字符的个数

        int right = 0;
        int left  = 0;

        while(right < s.size()) {
            char c = s[right]; // 移入窗口的字符
            right++;

            if(need.count(c)) {
                window[c]++;
                if(window[c] == need[c]) valid++; // 当 window 中字符匹配 need 中字符，则说明需要的字符数量增加
            }

            while(valid == need.size()){ // 当合法字符数量和 need 字符数量相同，则说明需要收缩寻找最优解
                // 寻找最小子串
                if(right - left < len) {
                    start = left;
                    len = right - left;
                }

                char d = s[left]; // 移除窗口的字符
                left++;                // 缩小窗口

                if(need.count(d)) {
                    if(window[d] == need[d]) valid--;
                    window[d]--;
                }

            }
        }

        return len == INT_MAX ? "" : s.substr(start, len);

    }
};
```

4. 时间复杂度：O(N) 因为每个元素都只会被遍历一遍