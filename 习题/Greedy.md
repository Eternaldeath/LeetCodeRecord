 # 简单

## [455. 分发饼干](https://leetcode.cn/problems/assign-cookies/)

1. 思路：本题直觉上可以使用贪心策略，可以从局部最优推出全局最优
   1. 排序：首先我们给小孩和饼干进行从小到大的顺序排序
   2. 比较：然后按照将大饼干交给大胃口的孩子的思路，从数组后端开始往前匹配
2. 注意点：-
3. 完整示例

```c++
class Solution {
public:
    int findContentChildren(vector<int>& g, vector<int>& s) {
        sort(g.begin(), g.end());
        sort(s.begin(), s.end());

        int res = 0;
        int indexCookie = s.size() - 1;

        for(int i = g.size() - 1; i >= 0; i--){
            if(indexCookie >= 0 && s[indexCookie] >= g[i]){
                res ++;
                indexCookie --;
            }
        }

        return res;
    }
};
```

4. 时间复杂度：O(n)；空间复杂度：O(1)

## [860. 柠檬水找零](https://leetcode.cn/problems/lemonade-change/)

1. 思路：只需要列出对应的找零方案即可
   1. 顾客支付 5 元，无需找零
   2. 顾客支付 10 元，找零方案一种：退款 5 元
   3. 顾客支付 20 元，找零方案两种：退款 10 + 5 元或者 5 + 5 + 5 元
2. 注意点：题目表示，账单已经是有序的，所以我们不能自己排序
3. 完整示例

```c++
class Solution {
public:
    bool lemonadeChange(vector<int>& bills) {
        vector<int> change(3, 0); // 当前零钱数组

        for(int i = 0; i < bills.size(); i++){
            // 顾客支付 5 元
            if(bills[i] == 5) change[0]++;
            // 顾客支付 10 元
            // 找零方案一种：退款 5 元
            else if(bills[i] == 10){
                if(change[0] > 0){
                    change[0]--;
                    change[1]++;
                } else return false;
            }
            // 顾客支付 20 元
            // 找零方案两种：退款 10 + 5 元或者 5 + 5 + 5 元
            else if(bills[i] == 20){
                if(change[1] > 0 && change[0] > 0){
                    change[1]--; change[0]--; change[2]++;
                } else if(change[0] >= 3){
                    change[0] -= 3; change[2]++;
                } else return false;
            }
        }

        return true;
    }
};
```

4. 时间复杂度：O(n)；空间复杂度：O(1)

# 中等

## [55. 跳跃游戏](https://leetcode.cn/problems/jump-game/)

1. 思路：本题我采用代码随想录的思路，它是这样描述的：每一个数组元素的长度表示从该数组位置最长（最远）能够**覆盖**到的范围，于是建立一个 for 循环，遍历每一个数组元素，将覆盖值相加，如果覆盖范围能够 >= 数组长度则说明最终一定可以到达
2. 注意点：-
3. 完整示例

```c++
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int cover = 0;
        for(int i = 0; i <= cover; i++){
            cover = max(cover, i + nums[i]);
            if(cover >= nums.size() - 1) return true;
        }

        return false;
    }
};
```

4. 时间复杂度：O(n)；空间复杂度：O(1)

## [122. 买卖股票的最佳时机 II](https://leetcode.cn/problems/best-time-to-buy-and-sell-stock-ii/)

1. 思路：本题如何寻找贪心的思路呢？记住贪心都是一维的，邻近就要判定结果的逻辑，所以我们考虑计算出每天的利润差值（即后项 - 前项），然后只选择正利润的天数收集
2. 注意点：本题的背景是一只股票，可以在同一天买卖
3. 完整示例

```c++
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        vector<int> profits;
        int j = 0;

        // 收集每天正利润
        for(int i = 1; i < prices.size(); i++){
            if(prices[i] - prices[i-1] > 0) {
                profits.push_back(prices[i] - prices[i-1]) ;
                j++;
            }
        }

        // 统计正利润之和
        int amount = 0;
        for(int i = 0; i < profits.size(); i++){
            amount += profits[i];
        }

        return amount;
    }
};
```

4. 时间复杂度：O(n)；空间复杂度：O(1)

## [376. 摆动序列](https://leetcode.cn/problems/wiggle-subsequence/)

1. 思路：本题可以用贪心算法也可以用动态规划来求解，这里我选择动态规划的方式处理。按照代码随想录的 dp 问题五步法，分析过程如下

   1. dp 数组的含义
      1. 这里 dp 记录摆动序列的最长长度，而且分为两种情况，第一种是第 i 个数作为山峰时的最长子序列，第二种是第 i 个数作为山谷时的最长子序列，所以我们将 dp 数组设置成二维的，且列的维度仅有两列，分别表示山峰和山谷作为 i 位置摆动最长子序列
      2. **其中我们将 dp\[i][0] 视做 i 位置为山峰的情况，将 dp\[i][1] 视做 i 位置为山谷的情况**
   2. 递推公式：递推公式非常简单，它的逻辑如下

   ```c++
   for(int i = 0; i < nums.size(); i++){
       for(int j = 0; j < i; j ++){
           if(nums[j] > nums[i]) dp[i][1] = max(dp[i][1], dp[j][0] + 1);
       }
       for(int j = 0; j < i; j ++){
           if(nums[j] < nums[i]) dp[i][0] = max(dp[i][0], dp[j][1] + 1);
       }
   }
   ```

   3. 初始化：注意，从任何某个位置开始，都可以将该位置视为一个起始点，即 `dp\[i][0] = dp\[i][1] = 1`
   4. 遍历顺序：从左往右遍历
   5. 示意图

2. 注意点：这里的 dp 数组虽然是二维的，但和我们在动规小节前言中说的选择一维还是二维 dp 数组并不是一个东西，本题是根据具体题意来构建的

# 困难

## [135. 分发糖果](https://leetcode.cn/problems/candy/)

1. [思路](https://programmercarl.com/0135.%E5%88%86%E5%8F%91%E7%B3%96%E6%9E%9C.html#%E6%80%9D%E8%B7%AF)
2. 注意点：本题的逻辑不同常理
3. 完整示例

```c++
class Solution {
public:
    int candy(vector<int>& ratings) {
        vector<int> candyVec(ratings.size(), 1);
        // 从前向后
        for (int i = 1; i < ratings.size(); i++) {
            if (ratings[i] > ratings[i - 1]) candyVec[i] = candyVec[i - 1] + 1;
        }
        // 从后向前
        for (int i = ratings.size() - 2; i >= 0; i--) {
            if (ratings[i] > ratings[i + 1] ) {
                candyVec[i] = max(candyVec[i], candyVec[i + 1] + 1);
            }
        }
        // 统计结果
        int result = 0;
        for (int i = 0; i < candyVec.size(); i++) result += candyVec[i];
        return result;
    }
};
```

4. 时间复杂度：O(n)；空间复杂度：O(n)