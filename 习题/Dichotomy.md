# 前言

二分法

# 简单

## [69. x 的平方根 ](https://leetcode.cn/problems/sqrtx/)

1. 思路：在不利用已有函数的情况下，我们可以通过判断一个数的平方来判断它是否是另一个数的平方根整数部分。例如，2^2^ = 4，3^2^ = 9，那么 2.xxx 一定是 8 的平方根。在本例中无法使用单纯的 for 循环从前向后遍历，因为会超时，所以采用二分法来加快遍历的速度
2. 完整示例

```c++
class Solution {
public:
    int mySqrt(int x) {
        int l = 0, r = x, ans = -1;
        while (l <= r) {
            int mid = l + (r - l) / 2;
            if ((long long)mid * mid <= x) {
                ans = mid;
                l = mid + 1;
            } else {
                r = mid - 1;
            }
        }
        return ans;
    }
};
```

3. 时空复杂度：时间复杂度 O(logx)；空间复杂度 O(1)