# 前言

1. 什么时候采用一维 dp table，什么时候采用二维 dp table：一般单个字符串或数组采用一维，两个字符串，两个数组和二维表格采用二维
2. 子序列问题
   1. 连续：该种情况下仅考虑状态转移为 dp\[i][j] = dp\[i-1][j-1] + 1（or dp[i] = dp[i] + 1)，因为考虑的是连续情况，而 dp\[i][j] 的含义是在数组 A 的 i 位置和数组 B 的 j 位置的时的最值问题。如 718 题目
   2. 不连续：该种情况除了考虑“连续”的问题，还要考虑不连续的问题，因此分裂为两个子问题，则需要按照如下代码进行分别考虑

```c++
if(text1[i-1] = text2[j-1]){
    dp[i][j] = dp[i-1][j-1] + 1;
}else{
    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
}
```



# 基础题目

[64. 最小路径和_中等](https://leetcode.cn/problems/minimum-path-sum/)

1. 思路：直接上动规五部曲
   1. dp 数组：因为是二维的表格，所以设置为 dp\[i][j]，表示在 [i, j] 这个位置时的最小路径和
   2. 递推公式：dp\[i][j] 的状态要么是从它上方的表格来，要么是从它左边的表格来，选择上方或左边的最小值，所以`dp[i][j] = min(dp[i][j - 1], dp[i - 1][j]) + grid[i][j];`
   3. 数组的初始化：对于 dp\[0][0] 来说，按照 dp 数组的定义，它应该取值二维表格的 [0, 0] 位置的值，题目中二维表格为 grid，所以有 `dp[0][0] = grid[0][0];`
   4. 遍历顺序：考虑边界情况
      1. 首先首行的 dp\[0][j] 一定是从左边的表格递推过来的。同理，首列的  dp\[i][0] 值一定是从上面的表格递推过来的，因此我们先填满它们
      2. 然后其余的表格按照从左至右，从上至下的顺序填满
2. 注意点：-
3. 完整示例 `hello 算法题解`

```c++
int minPathSumDP(vector<vector<int>> &grid) {
    int n = grid.size(), m = grid[0].size();
    // 初始化 dp 表
    vector<vector<int>> dp(n, vector<int>(m));
    dp[0][0] = grid[0][0];
    // 状态转移：首行
    for (int j = 1; j < m; j++) {
        dp[0][j] = dp[0][j - 1] + grid[0][j];
    }
    // 状态转移：首列
    for (int i = 1; i < n; i++) {
        dp[i][0] = dp[i - 1][0] + grid[i][0];
    }
    // 状态转移：其余行列
    for (int i = 1; i < n; i++) {
        for (int j = 1; j < m; j++) {
            dp[i][j] = min(dp[i][j - 1], dp[i - 1][j]) + grid[i][j];
        }
    }
    return dp[n - 1][m - 1];
}
```

4. 时间复杂度：O(n*m)；空间复杂度：O(n\*m)

## [70. 爬楼梯_简单](https://leetcode.cn/problems/climbing-stairs/)

1. 思路：从第 n 层楼梯来看，它要么是从第 n-1 层楼梯来的，要么是从第 n-2 层楼梯来的。接下来我们用动归五部曲来完成下面的问题
   1. dp 数组和下标的含义
      1. i 表示第 i 层
      2. dp[i] 表示第 i 层有 dp[i] 种方法
   2. 递推公式：`dp[i] = dp[i-1]+dp[i-2]`
   3. dp 数组的初始化：因为本题说明 n 为一个正整数，所以我们知道 dp[1] = 1, dp[2] = 2，由此开始循环
   4. 遍历数组：根据递推公式来即可
2. 注意点：初始化的情况
3. 完整示例 `代码随想录`

```c++
// 未优化版本
class Solution {
public:
    int climbStairs(int n) {
        if(n <= 1) return n;
        vector<int> dp(n + 1);
        dp[1] = 1;
        dp[2] = 2;

        for(int i = 3; i <= n; i++){
            dp[i] = dp[i-1] + dp[i-2];
        }

        return dp[n];
    }
};

// 优化版本，因为实际上只有前两个状态需要在每次循环中被记录
class Solution {
public:
    int climbStairs(int n) {
        if (n <= 1) return n;
        int dp[3];
        dp[1] = 1;
        dp[2] = 2;
        for (int i = 3; i <= n; i++) {
            int sum = dp[1] + dp[2];
            dp[1] = dp[2];
            dp[2] = sum;
        }
        return dp[2];
    }
};
```

4. 时空复杂度
   1. 未优化
      1. 时间复杂度：O(n)
      2. 空间复杂度：O(n)
   2. 优化
      1. 时间复杂度：O(n)
      2. 空间复杂度：O(1)

## [509. 斐波那契数_简单](https://leetcode.cn/problems/fibonacci-number/)

1. 其他思路：暴力递归
2. 思路
   1. 对暴力递归的分析：首先，我们对暴力递归进行分析，递归的耗时原因在于重复计算，所以我们可以用一个备忘录（dp table）来记录每个子问题的答案，之后遇到新的子问题，先去备忘录中查询，有就直接用，无需重复计算
   2. 1 中描述的带备忘录的递归解法的效率和迭代的动归已经非常类似了，而两者的区别在于，递归是自顶向下，而迭代的动归是自底向上
3. 注意点：示例代码中，我们对 dp table 进行了优化，因为发现每次迭代中，仅需要记录两个状态，而不需要整个 dp table 来记录
4. 完整示例 `算法小抄`，`未手动实现`

```c++
class Solution {
public:
    int fib(int n) {
        if(n == 0 || n == 1) return n;

        int dp_i_1 = 1, dp_i_2 = 0;
        for(int i = 2; i <= n; i++){
            // dp[i] = dp[i - 1] + dp[i - 2]
            int dp_i = dp_i_1 + dp_i_2;

            dp_i_2 = dp_i_1;
            dp_i_1 = dp_i;
        }

        return dp_i_1;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)

## [746. 使用最小花费爬楼梯_简单](https://leetcode.cn/problems/min-cost-climbing-stairs/)

1. 思路：本题和 70 题类似，依旧用动归五部曲解答

   1. dp 数组以及下标的定义
      1. i 表示第 i 层台阶
      2. dp[i] 表示花费的**最少**体力
   2. 递推公式
      1. dp[i - 1] 跳到 dp[i] 需要花费 dp[i - 1] + cost[i - 1]（表示 i - 1 层之前的花费 + i - 1 层起跳的花费）
      2. dp[i - 2] 跳到 dp[i] 需要花费 dp[i - 2] + cost[i - 2]
      3. 一定是选最小的，所以dp[i] = min(dp[i - 1] + cost[i - 1], dp[i - 2] + cost[i - 2]);

   ![](.\img\dp\746.png)

2. 注意点：跳到 dp[0] 和 dp[1] 是不花费体力的

3. 完整示例 `代码随想录`

```c++
class Solution {
public:
    int minCostClimbingStairs(vector<int>& cost) {
        vector<int> dp(cost.size() + 1);
        dp[0] = 0;
        dp[1] = 0;
        for(int i = 2; i <= cost.size(); i++){
            dp[i] = min(dp[i - 1] + cost[i - 1], dp[i - 2] + cost[i - 2]);
        }

        return dp[cost.size()];
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(n)

## [416. 分割等和子集](https://leetcode.cn/problems/partition-equal-subset-sum/)

1. 思路
   1. 将本题建模抽象成 01 背包
      1. 背包的重量：sum / 2
      2. 元素的重量 = 元素的值
      3. 若背包能够装满说明找到了 sum/2 的子集
      4. 背包中元素不可重复放入
   2. 动归五部曲
      1. dp 数组及其下标的含义
         1. j 表示背包容量，dp[j] 表示背包容量对应的价值
         2. 递推公式：`dp[j] = max(dp[j], dp[j - nums[i]] + nums[i]);`，关于此处递推公式的推导见[这里](https://www.yuque.com/fromdark/yx0hps/swqwhsrw7v29ar0g#fUf6k)
         3. dp 数组初始化：均初始化为 0
         4. 遍历顺序：内层背包循环需要从大到小进行，这样每个元素只放一次
2. 注意点：本题中价值和重量是一个意义，所以在推导的递推公式中，`weight[i] = value[i] = nums[i]`
3. 完整示例 `代码随想录题解`

```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;

        // dp[i] 中的 i 表示背包内总和
        // 题目中说：每个数组中的元素不会超过 100，数组的大小不会超过 200
        // 总和不会大于 20000，背包最大只需要其中一半，所以 10001 大小就可以了
        vector<int> dp(10001, 0);
        for (int i = 0; i < nums.size(); i++) {
            sum += nums[i];
        }
        // 也可以使用库函数一步求和
        // int sum = accumulate(nums.begin(), nums.end(), 0);
        if (sum % 2 == 1) return false;
        int target = sum / 2;

        // 开始 01背包
        for(int i = 0; i < nums.size(); i++) {
            for(int j = target; j >= nums[i]; j--) { // 每一个元素一定是不可重复放入，所以从大到小遍历
                dp[j] = max(dp[j], dp[j - nums[i]] + nums[i]);
            }
        }
        // 集合中的元素正好可以凑成总和target
        if (dp[target] == target) return true;
        return false;
    }
};
```

4. 时间复杂度：O(n^2^)；空间复杂度：O(n)

## [931. 下降路径最小和_中等](https://leetcode.cn/problems/minimum-falling-path-sum/)

1. 思路：本题采用带备忘录（memo）自顶向下的动态规划，其中备忘录用来消除重叠子问题

   1. dp\[i][j] 及下标含义：dp 数组记录 i， j 位置的最小路径和，i，j 则表示表格中的行列位置
   2. 递归公式

   ```c++
   memo[i][j] = matrix[i][j] + min(
                   dp(matrix, memo, i - 1, j), 
                   dp(matrix, memo, i - 1, j - 1),
                   dp(matrix, memo, i - 1, j + 1)
               );
   ```

   3. 数组初始化：此处初始化的是备忘录 `vector<vector<int>> memo(n, vector<int>(n, 66666));`，这里为什么是用 66666 来初始化呢？因为根据已知条件，数组最多 100 行，每个单元格的值最大 100，所以，最多路径和为 10000
   4. 遍历顺序：因为采用递归的方式，所以遍历顺序如递归公式所示

2. 注意点：-

3. 完整示例 `算法小抄题解`

```c++
```



# 01 背包

## [474. 一和零](https://leetcode.cn/problems/ones-and-zeroes/)

1. 思路：本题题解看[这里](https://programmercarl.com/0474.%E4%B8%80%E5%92%8C%E9%9B%B6.html#%E6%80%9D%E8%B7%AF)

## [416. 分割等和子集_中等](https://leetcode.cn/problems/partition-equal-subset-sum/)

1. 思路：参考[本篇文章](https://www.yuque.com/fromdark/yx0hps/swqwhsrw7v29ar0g#CWOTW)和[分割等和子集](https://programmercarl.com/0416.%E5%88%86%E5%89%B2%E7%AD%89%E5%92%8C%E5%AD%90%E9%9B%86.html#%E7%AE%97%E6%B3%95%E5%85%AC%E5%BC%80%E8%AF%BE)
2. 注意点：dp table 下标是从 0 开始的，而物品的下标是从 1 开始的，所以需要用 nums[i-1] 来进行对位匹配
3. 完整示例

```c++
class Solution {
public:
    bool canPartition(vector<int>& nums) {
        int sum = 0;
        for(int i = 0; i < nums.size(); i++){
            sum += nums[i];
        }

        if(sum % 2 ==1) return false;
        // 因为是分割等和子集，所以只要数组中的内容能满足和的一半，则另一半自动满足
        int target = sum / 2;

        // dp table 首行，首列都是从 0 开始
        vector<vector<int>> dp(nums.size() + 1, vector<int>(target + 1, 0));

        for(int i = 1; i <= nums.size(); i++){
            for(int j = 1; j <= target; j++){
                if(nums[i-1] > j){
                    dp[i][j] = dp[i-1][j];
                }
                else{
                    dp[i][j] = max(dp[i-1][j], dp[i-1][j-nums[i-1]] + nums[i-1]);
                }
                
            }
        }

        if(dp[nums.size()][target] == target) return true;
        return false;
    }
};
```

4. 时间复杂度：O(n\*m)；空间复杂度：O(n\*m)

# 完全背包系列

## [322. 零钱兑换_中等](https://leetcode.cn/problems/coin-change/)

1. 思路：本题我采用**自底向上**的动态规划方式来解决
   1. dp[i] 数组及下标的确定，其中 i 表示当前零钱的总数，由于采用自底向上，所以 i 从 0 开始，表示从当前总金额 0 元开始，dp[i] 表示当前零钱为 i 时，最少的零钱数量
   2. 递推公式：`dp[i] = min(dp[i], dp[i - coin] + 1)`，其中 dp[i] 最开始我们会进行初始化（见下文），`dp[i - coin] + 1` 表示前一轮最小钱币数量加上当前轮的钱币数量（即此处的 1，也就是常见的 value 值，因为每次都只增加一个钱币，所以固定为 1），最终我们取二者的最小值
   3. dp 数组初始化：`vector<int> dp(amount + 1, amount + 1);`，其中第一个参数表示 dp 初始化的大小，因为有 dp[0] 的存在，所以加一，第二个参数为每一个位置的初始值，因为总额最大不超过 `amount`，所以 `amount + 1` 实际上起到了无穷大的效果，而我们又是去最小值，所以适合用这个参数
   4. 数组遍历：此处存在两轮 for 循环，外层 for 循环为对 i 的循环，内层 for 循环为对每一轮的 coins 的循环，因为钱币数量无限，所以合理
2. 注意点：-
3. 完整示例 `算法小抄`

```c++
class Solution {
public:
    int coinChange(vector<int>& coins, int amount) {
        vector<int> dp(amount + 1, amount + 1);

        dp[0] = 0;

        for(int i = 0; i < dp.size(); i++){
            for(auto& coin : coins){
                // 若当前总额 - 提供的某个钱币币值小于 0
                // 说明这种组合不满足
                if(i - coin < 0) continue;
                dp[i] = min(dp[i], dp[i - coin] + 1); 
            }
        }

        // 若满足等式，说明所有组合都不满足题意，否则满足题意则返回最小钱币数
        return (dp[amount] == amount + 1) ? -1 : dp[amount];
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)

## [518. 零钱兑换 II](https://leetcode.cn/problems/coin-change-ii/)

> 本题的解题思路最好还是回看《[代码随想录](https://programmercarl.com/0518.%E9%9B%B6%E9%92%B1%E5%85%91%E6%8D%A2II.html#%E6%80%9D%E8%B7%AF)》里面的内容

1. 思路
   1. dp 数组及下标的含义：dp\[i][j] 表示当到物品 i 时，重量为 j 是，背包的**装满方法有多少种**。这里有物品和重量两个**状态**，所以用二维数组（关于动态规划中，到底使用几维数组的问题，看[这里](https://www.yuque.com/fromdark/yx0hps/swqwhsrw7v29ar0g#QaNlV)）。其中有两个**选择**，分别是放入 i 物品和不放入 i 物品
   2. 递推公式：`dp\[i][j] = max(dp[i-1][j], dp[i-1][j-weight[i]] + value[i])`
   3. dp 数组初始化：当 i 为 0 时，表示没有物品，则装满方法一定是 0，当 j 为 0 时，表示背包重量为 0，则装满方法只有一种，就是什么也不装
   4. 遍历顺序：和 01 背包不同，01 背包在内层循环时，为了方式物品被装入多次，所以内层循环是从大到小，而完全背包中，我们就是希望一个物品被放入多次，所以内层循环是从小到大
2. 注意点：-
3. 完整示例 `代码随想录题解`

```c++
class Solution {
public:
    int change(int amount, vector<int>& coins) {
        vector<int> dp(amount + 1, 0);
        dp[0] = 1;
        for (int i = 0; i < coins.size(); i++) { // 遍历物品
            for (int j = coins[i]; j <= amount; j++) { // 遍历背包
                dp[j] += dp[j - coins[i]];
            }
        }
        return dp[amount];
    }
};
```

4. 时间复杂度：O(^2^)；空间复杂度：O(1)

# 打家劫舍系列

## [198. 打家劫舍_中等](https://leetcode.cn/problems/house-robber/)

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

## [337. 打家劫舍 III_中等](https://leetcode.cn/problems/house-robber-iii/)

1. 思路
   1. dp 数组：确定一个 dp 数组，其状态为 dp[0]，dp[1] 分别表示偷和不偷，因为这个 dp 是会被递归到每一层的，即表示每一层的偷和不偷的最值
   2. 采用递归和后序遍历，后序遍历的作用是从最底层开始遍历，然后往上递归得到结果
2. 注意点：-
3. 完整示例 `代码随想录题解`，`未手动实现`

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
    int rob(TreeNode* root) {
        vector<int> result = robTree(root);
        return max(result[0], result[1]);
    }

    vector<int> robTree(TreeNode* cur){
        if(cur == nullptr) return vector<int>{0, 0};
        vector<int> left = robTree(cur->left);
        vector<int> right = robTree(cur->right);
                // 偷cur，那么就不能偷左右节点。
        int val1 = cur->val + left[0] + right[0];
                // 不偷cur，那么可以偷也可以不偷左右节点，则取较大的情况
        int val2 = max(left[0], left[1]) + max(right[0], right[1]);
        return {val2, val1};
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(logn)

# 股票买卖

1. 关于股票买卖的通用模板看[这里](https://www.yuque.com/fromdark/yx0hps/swqwhsrw7v29ar0g#ZZmi6)

## [121. 买卖股票的最佳时机_简单](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock/)

1. 思路：我们套用模板来解题。

   1. dp 数组，下标及递推公式

   ```c++
   dp[i][1][0] = max(dp[i-1][1][0], dp[i-1][1][1] + prices[i])
   dp[i][1][1] = max(dp[i-1][1][1], dp[i-1][0][0] - prices[i])
       
   // 由于 k 为 1，所以 k 对状态转移没有影响，所以 k 的部分省略
   // 1 表示持有，0 表示不持有
   dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
   dp[i][1] = max(dp[i-1][1], -prices[i])
   ```

   2. 数组初始化
   3. 遍历顺序：从 i 为 1 开始，因为 i 表示天数

2. 注意点：-

3. 完整示例 `算法小抄题解`，`未手动实现`

```c++
int maxProfit_k_1(vector<int>& prices) {
    int n = prices.size();
    vector<vector<int>> dp(n, vector<int>(2));
    for (int i = 0; i < n; i++) {
        if (i - 1 == -1) {
            // base case
            dp[i][0] = 0;
            dp[i][1] = -prices[i];
            continue;
        }
        dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i]);
        dp[i][1] = max(dp[i-1][1], -prices[i]);
    }
    return dp[n - 1][0];
}

// 空间复杂度优化版本
int maxProfit_k_1(vector<int>& prices) {
    int n = prices.size();
    // base case: dp[-1][0] = 0, dp[-1][1] = -infinity
    int dp_i_0 = 0, dp_i_1 = INT_MIN;
    for (int i = 0; i < n; i++) {
        // dp[i][0] = max(dp[i-1][0], dp[i-1][1] + prices[i])
        dp_i_0 = max(dp_i_0, dp_i_1 + prices[i]);
        // dp[i][1] = max(dp[i-1][1], -prices[i])
        dp_i_1 = max(dp_i_1, -prices[i]);
    }
    return dp_i_0;
}
```

4. 时间复杂度：O(n)；空间复杂度：O(1)

# 子序列问题

1. 注意：子序列和子串是有区别的，子序列若无特殊说明是指非连续的序列，举例如下，在 `nums[1,2,1,3,10,2,15]` 中，其中的最长递增子序列是 `[1,3,10,15]`，可以看到中间允许中断

## 子序列连续情况

### [53. 最大子数组和_中等](https://leetcode.cn/problems/maximum-subarray/)

1. 思路：直接套用动规五部曲
   1. dp 数组和下标的含义：dp 数组表示下标为 i 时的最大连续子数组的和
   2. 递推公式：dp[i] = max(dp[i-1], dp[i-1]+nums[i]);，表示 i 位置的最大值，如果 nums[i] 为负数，则 dp[i-1] 为最大值，否则 dp[i-1] 再加上当前位置上的正数（即，nums[i]）为最大值
   3. 初始化：dp[0] 取 nums[0]
   4. 遍历顺序：从前往后
2. 注意点：-
3. 完整示例 `代码随想录题解`

```c++
class Solution {
public:
    int maxSubArray(vector<int>& nums) {
        if (nums.size() == 0) return 0;
        vector<int> dp(nums.size());
        dp[0] = nums[0];
        int maxValue = dp[0];
        for (int i = 1; i < nums.size(); i++) {
            dp[i] = max(dp[i - 1] + nums[i], nums[i]); 
            if (dp[i] > maxValue) maxValue = dp[i]; 
        }
        return maxValue;
    }
};
```

4. 时间复杂度：O(n)；空间复杂度：O(n)；

### [674. 最长连续递增序列_简单](https://leetcode.cn/problems/longest-continuous-increasing-subsequence/)

1. 思路：我们只需要比较相邻两个数字的大小即可，具体思路见[这里](https://programmercarl.com/0674.%E6%9C%80%E9%95%BF%E8%BF%9E%E7%BB%AD%E9%80%92%E5%A2%9E%E5%BA%8F%E5%88%97.html#%E6%80%9D%E8%B7%AF)。同时自己画一幅图，跟着代码跑一遍
2. 注意点：-
3. 完整示例 `代码随想录题解`

```c++
class Solution {
public:
    int findLengthOfLCIS(vector<int>& nums) {
        vector<int> dp(nums.size(), 1);
        int res = 1;

        for(int i = 1; i < nums.size(); i++){
            if(nums[i - 1] < nums[i]) dp[i] = dp[i - 1] + 1;
        }

        for(int i = 1; i < nums.size(); i++){
            res = max(res, dp[i]);
        }

        return res;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)

### [718. 最长重复子数组_中等](https://leetcode.cn/problems/maximum-length-of-repeated-subarray/)

1. 思路：本题是子数组，且题目说明了连续，则我们考虑状态转移为“前言_子序列问题\_连续。同时，因为有两个数组进行比较，则 dp table 我们设置为二维的
2. 注意点：-
3. 完整示例 `代码随想录题解`

```C++
class Solution {
public:
    int findLength(vector<int>& nums1, vector<int>& nums2) {
        int result = 0;
        vector<vector<int>> dp(nums1.size()+1, vector<int>(nums2.size()+1, 0));
        for(int i = 1; i <= nums1.size(); i++){
            for(int j = 1; j <= nums2.size(); j++){
                if(nums1[i-1] == nums2[j-1]) dp[i][j] = dp[i-1][j-1] + 1;

                if (dp[i][j] > result) result = dp[i][j];
            }
        }

        return result;
    }
};
```

4. 时间复杂度：O(n\*m)，n 和 m 为数组的长度；空间复杂度:O(n\*m)

## [300. 最长递增子序列_中等](https://leetcode.cn/problems/longest-increasing-subsequence/)

1. 思路：利用动归五部曲来分析

   1. dp[i] 数组及下标的含义：有原数组 nums[]，其中 i 表示原数组的下标，即第 i 个元素，**dp[i] 表示以 nums[i] 为结尾的最大递增子序列**，它包含了 nums[i] 的位置

   1. 递推公式
      1. 两层 for 循环：其中 i 用于定位当前 dp 数组的遍历到的位置。而 j 用来比较 dp[i] 与 dp[j] 的大小，每一轮（即外层 for 循环 i++）后，j 都会从 0 开始到 i-1 比较已有 dp 数组上的值（记录的该位置的最大子序列）与本轮中 dp[i] 的大小
      2. `dp[i] = max(dp[i], dp[j] + 1)` 的含义是，在当前轮次中，比较 i 位置和 j 位置的 dp 记忆包的大小，并选择其中大的
         1. 若 dp[i] 大，表示以 nums[i] 结尾的递增子序列更长
         2. 若 dp[j]+1 大，表示以 nums[j] 结尾的递增子序列加上当前 nums[i] 之后更长
         3. 注意：j 是从 0 到 i-1，而不是 j = i-1

      3. 注意：整个过程手动模拟一下会更容易理解

2. 注意点：dp 数组的作用就是记忆包，相比于暴力的 for 循环，它记录了每一轮 i 轮次得到的最大子序列，并用于在后期的比较中，而不用每一轮都重新计算

```c++
for(int i = 0; i < nums.size(); i++){
    for(int j = 0; j < i; j++){
        if(nums[i] > nums[j]) dp[i] = max(dp[i], dp[j] + 1);
    }
}
```

3. dp 数组初始化：显然长度必然包含自己，于是初始化为 1
4. 遍历顺序：如递推公式所示

2. 注意点：-

3. 完整示例 `算法小抄题解`

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> dp(nums.size(), 1);

        for(int i = 0; i < nums.size(); i++){
            for(int j = 0; j < i; j++){
                if(nums[i] > nums[j]) dp[i] = max(dp[i], dp[j] + 1); // +1 是把自己加上
            }
        }

        int res = 0;
        for(int i = 0; i < dp.size(); i++){
            res = max(res, dp[i]);
        }

        return res;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n^2^)
   2. 空间复杂度：O(1)

## [300. 最长递增子序列_中等](https://leetcode.cn/problems/longest-increasing-subsequence/)

1. 思路：每一次 nums[i] 对应的 dp[i] 每次从头比较它前面的递增子序列的长度

   ![](.\img\dp\300.png)

   1. dp 数组及其下标含义：i 表示到第 i 个位置时，dp[i] 的存放的最长递增子序列的大小
   2. 递推公式：`dp[i] = max(dp[i], dp[j] + 1)`，它表示的含义是，dp[i] 的取值取决于当前 dp[i] 的初始值和前面的 dp[j] 的最长递增子序列的比较
   3. dp 数组初始化：初始化都为 1
   4. 遍历顺序：两层 for 循环，外层为对位置 i 的循环。内层为对 j 的循环

2. 注意点：-

3. 完整示例 `代码随想录题解`

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        vector<int> dp(nums.size(), 1);

        for(int i = 0; i < nums.size(); i++){
            for(int j = 0; j < i; j++){
                if(nums[i] > nums[j]) dp[i] = max(dp[i], dp[j] + 1); // +1 是把自己加上
            }
        }

        int res = 0;
        for(int i = 0; i < dp.size(); i++){
            res = max(res, dp[i]);
        }

        return res;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n^2^)
   2. 空间复杂度：O(1)

## [1035. 不相交的线_中等](https://leetcode.cn/problems/uncrossed-lines/)

1. 思路：本题和 1143 考察的内容完全一样，其实就是找最长公共子序列，你只需要把这个本题的逻辑转换成最长公共最序列就好。如下图所示，就是两个数组的最长子序列为 [1,4]，长度为 2，也就是最多不想交线为 2

![](.\img\dp\1135_01.png)

2. 注意点：本题的代码和 1143 几乎一致，就不再贴代码了

## [1143. 最长公共子序列_中等](https://leetcode.cn/problems/longest-common-subsequence/)

1. 思路
   1. dp 数组的定义
      1. dp 数组在本例中表示在当前位置存在的最长公共子序列，首先由于是两个子数组，所以我们一开始定义 dp 数组的时候是二维的，其中横方向为 text1，纵方向为 text2
      2. dp\[i][0] 的含义：表示 text1 在下标为 i 位置和 text2 在下标为 0 位置相比较时的最长公共子序列
   2. 递推公式
      1. 我们选取中间某个位置进行比较，若有 text[i-1] = text[j-1] 则说明此此处为新的相同元素，那么该位置更新最长子序列是 dp\[i-1][j-1] 位置处的值再加一（PS，在代码随想录中，它选择了用 [0, i-1] 来定义子序列的范围
      2. 我们选取中间某个位置进行比较，若有 text[i-1] ≠ text[j-1] 则我们将 dp\[i][j-1] 或 dp\[i-1][j] 中的最大值拷贝过来，它的含义可以从下面的图中了解到

```c++
for(int i = 1; i < text1.size(); i++){
    for(int j = 1; j < text2.size(); j++){
        if(text1[i-1] = text2[j-1]){
            dp[i][j] = dp[i-1][j-1] + 1;
        }else{
            dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
        }
    }
}
```
   3. dp 数组初始化，dp\[i][0] 表示 text1 在下标为 i 和 text2 在下标为 0 中最长公共子序列，所以 dp 记录的最大公共子序列为 0，同理 dp\[0][j] 也为 0
   4. 遍历顺序：**从上往下，从左往右**，每个 dp 单元可以从三个方向推出，具体见下方图片

![](.\img\dp\1143_01.jpg)

2.  注意点：-
3. 完整示例 `代码随想录题解`

```C++
class Solution {
public:
    int longestCommonSubsequence(string text1, string text2) {
        vector<vector<int>> dp(text1.size() + 1, vector<int>(text2.size() + 1, 0));
        // 注意，这里是 <=
        for(int i = 1; i <= text1.size(); i++){
            for(int j = 1; j <= text2.size(); j++){
                if(text1[i - 1] == text2[j - 1]) dp[i][j] = dp[i-1][j-1] + 1;
                else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
            }
        }

        return dp[text1.size()][text2.size()];
    }
};
```

4. 时间复杂度：O(n*m)，其中 n，m 为 text1,2 的长度；空间复杂度：O(n\*m)，表示 dp table 的大小

## 编辑距离

### [583. 两个字符串的删除操作_中等](https://leetcode.cn/problems/delete-operation-for-two-strings/)

1. 思路：本题的大体思路同 [Hello 算法中介绍的编辑距离的解题通用思路](https://www.hello-algo.com/chapter_dynamic_programming/edit_distance_problem/#146)类似，不过这里我们仅考虑编辑操作为删除的情况。同时本题的一个变体点在于，常规的编辑距离问题是从一个字符串不动，另一个字符串进行编辑操作，进而变成不动的那个，而本题两个字符串都可以进行操作，因此在递推公式的部分，我们需要对其含义进行修正

   1. dp\[i][j] 的定义：它表示在字符串（s）和字符串（t）分别位于 i 和 j 位置时，让两个字符串变得一样需要的最小操作数量。举例来说，假设有字符串 "a" 和 字符串 "b"（此时 i 为 1，j 为 1），让两字符串变得一样，则最小的操作数是 2，即分别删除 a 和 b

   2. 递推公式：按照编辑距离的常规思路（见 [Hello 算法](https://www.hello-algo.com/chapter_dynamic_programming/edit_distance_problem/#146)），我们需要对上述情形进行两层分类，第一层是若 s[i] 和 t[j] 对应的字符是否相同，若相同则跳过，若不同则处理。第二层是如何处理，传统的处理方式为替代，删除和增加，而本题仅能删除，具体操作见下方

      1. 若 s[i] 和 t[j] 相同：dp\[i][j] = dp\[i-1][j-1]

      2. 若 s[i] 和 t[j] 不相同，则考虑三种情况，分别如下。最后 +1 操作表示增加本次操作

         1. 删除 s 的当前字符使得整体操作步骤最小：dp\[i][j] = dp\[i-1][j] + 1
         2. 删除 t 的当前字符使得整体操作步骤最小：dp\[i][j] = dp\[i][j-1] + 1

      3. 综上递推公式为

         ```c++
                 for(int i = 1; i <= w1Size; ++i){
                     for(int j = 1; j <= w2Size; ++j){
                         // 注意此处的下标，由于我们的 dp table 在横向和竖向
                         // 增加了一行一列，但我们的 word 字符串还是要从下标
                         // 为 0 的位置开始
                         if(word1[i-1] == word2[j-1]){
                             dp[i][j] = dp[i-1][j-1];
                         }else{
                             dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + 1;
                         }
                     }
                 }
         ```

   3. 初始化 dp\[i][j]

      1. dp\[0][0]：表示 s 和 t 都为空的时候，二者变得一样的步骤次数，显然不需要进行任何步骤，所以为 0
      2. dp\[i][0]：表示 s 存在而 t 为空的时候， 二者变得一样的步骤次数，其实就是把 s 的字符全部删除干净，所以 dp\[i][0] = i
      3. dp\[0][j]：表示 t 存在而 s 为空的时候， 二者变得一样的步骤次数，其实就是把 t 的字符全部删除干净，所以 dp\[0][j] = j

   4. 遍历顺序：参考代码随想录的题解，我们知道遍历顺序从上至下，从左至右，因此使用两层正向 for 循环

2. 注意点：-

3. 完整示例

```c++
class Solution {
public:
    int minDistance(string word1, string word2) {
        int w1Size = word1.size();
        int w2Size = word2.size();

        // 注意为 dp table 增加一行和一列，具体见 Hello 算法里面的图片
        vector<vector<int>> dp(w1Size + 1, vector<int>(w2Size + 1));

        // 初始化 dp table
        dp[0][0] = 0;
        for(int i = 1; i <= w1Size; ++i){
            dp[i][0] = i;
        }
        for(int j = 1; j <= w2Size; ++j){
            dp[0][j] = j;
        }

        // 递推公式
        for(int i = 1; i <= w1Size; ++i){
            for(int j = 1; j <= w2Size; ++j){
                // 注意此处的下标，由于我们的 dp table 在横向和竖向
                // 增加了一行一列，但我们的 word 字符串还是要从下标
                // 为 0 的位置开始
                if(word1[i-1] == word2[j-1]){
                    dp[i][j] = dp[i-1][j-1];
                }else{
                    dp[i][j] = min(dp[i-1][j], dp[i][j-1]) + 1;
                }
            }
        }

        return dp[w1Size][w2Size];

    }
};
```

4. 时空复杂度：O(n\*m)



## 回文

### [647. 回文子串_中等](https://leetcode.cn/problems/palindromic-substrings/)

1. 思路：本题利用二维动态规划来解决
   1. 首先，我们确定有三种情况会出现回文。针对前两种回文好判断，但第三种我们很难通过一维 dp 数组来建立递推关系式，实际上，我们这么来看第三种情况，若有 'acbca' 是回文，则 'cbc' 是回文，则 'b' 是回文，当我们从 'b' 扩展出来时，是需要判断 'b' 两边的 'c' 是否相同，于是如下图所示，我们要判断 [i, j] 是否回文，则先判断 [i+1, j-1] 是否回文，然后判断 i+1 的值是否等于 j-1
      1. 第一种，单个字母算一个回文，如 'a'，'b'，就是两个回文
      2. 第二种，两个相同字母算一个回文，如 'aa'
      3. 第三种，类似 'aba'，'acbca'，也是回文

![](.\img\dp\647_01.png)

   2. 所以我们建立二维 dp\[i][j] 数组，它表示该区间的是否为回文数组（bool）
   2. 递推关系式：如第一点论述的三种情况那样

```c++
if(s[i] == s[j]){
    if(j - 1 <= i) {
        res++;
        dp[i][j] = true;
    } else {
        if(dp[i+1][j-1]){
            res++;
            dp[i][j] = true;
        }
    }
}
```

4. 数组初始化：dp 数组均先初始化为 false
5. 遍历顺序：如图 dp\[i][j] 是通过 dp\[i+1][j-1] 推导出来的，所以遍历顺序是从下往上，从左往右的顺序

![](.\img\dp\647_02.png)

2. 注意点：-
3. 完整示例 `代码随想录题解`

```c++
class Solution {
public:
    int countSubstrings(string s) {
        int res = 0;
        vector<vector<bool>> dp(s.size(), vector<bool>(s.size(), false));

        for(int i = s.size() - 1; i >= 0; i--){
            for(int j = i; j < s.size(); j++){
                if(s[i] == s[j]){
                    if(j - 1 <= i){
                        res++;
                        dp[i][j] = true;
                    } else {
                        if(dp[i+1][j-1]){
                            res++;
                            dp[i][j] = true;
                        }
                    }
                }
            }
        }
        return res;
    }
};
```

二维 dp 判断逻辑在原数组上的的图示过程

![](.\img\dp\647_03.png)

