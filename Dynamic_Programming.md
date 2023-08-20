> 动态规划问题的整体解决方案，我参考的是《代码随想录》的解题思路

# 打家劫舍系列

## [198. 打家劫舍](https://leetcode.cn/problems/house-robber/)

> 注意：下文中，totalMoney[i] 表示第 i 次打劫后（或者说打劫了第 i 个房间后）小偷总共的收入，curRoomMoney[i] 表示第 i 个房间的价值（收入）

1. 思路

   1. 确定 dp 数组的意义，这里我把 dp 数组命名为 totalMoney[]，以便符合语义化：其中下标 i 表示第 i 次打劫房间，**totalMoney[i] 表示第 i 次打劫房间后，总共的价值**

   2. 确定递推公式：第 i 次（或者也是第 i 个房间）是否打劫，取决于 i-1 和 i-2 两个房间的状况，若 i-2 次打劫了，那么 i 次可能进行打劫，若 i-1 次打劫了，那么 i 次就不能打劫。

      我们拿题目中的示例 1 来举例子，有数组 curRoomMoney = [1,2,3,1]，那么最后一个房间 curRoomMoney[3] = 1 是否打劫，实际上取决于前两个房间是否打劫（即 nums[2] = 3，nums[1] = 2），这里的取决的意思是，如何让 totalMoney 最大化。

      也就是说，```totalMoney[3] = max(totalMoney[3-2] + curRoomMoney[3], totalMoney[3-1])```，意思就是最后一次（第四次打劫）总收入最大化取决于，第 2 次打劫的最大化收入 + 当前房间的价值，与第 3 次打劫的最大化收入相比，哪个价值更大（**注意，数组下标从 0 开始，而描述第 i 次打劫是从 1 开始**）。

      因此不难得出递推公式：```totalMoney[i] = max(totalMoney[i-2] + curRoomMoney[i], totalMoney[i-1])```。	

   3. dp 数组的初始化：由递推公式可知，totalMoney 要求 i = 0 和 i = 1 的值准备好，其中 ```totalMoney [1] = max(curRoomMoney[0], curRoomMoney[1]);```

   4. 遍历顺序：显然 for 循环应该从 i = 2 开始，直至结束

2. 注意点：-

3. 完整示例 ```代码随想录题解```
```C++
class Solution {
public:
    int rob(vector<int>& nums) {
        if (nums.size() == 0) return 0;
        if (nums.size() == 1) return nums[0];
        vector<int> dp(nums.size());
        dp[0] = nums[0];
        dp[1] = max(nums[0], nums[1]);
        for (int i = 2; i < nums.size(); i++) {
            dp[i] = max(dp[i - 2] + nums[i], dp[i - 1]);
        }
        return dp[nums.size() - 1];
    }
};
```

   

   

   

   

   


​					

