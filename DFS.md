# 前言

1. 关于岛屿问题看[这里](https://www.yuque.com/fromdark/yx0hps/nx0bxivgao3lqyki)

# 中等

## [200. 岛屿数量](https://leetcode.cn/problems/number-of-islands/)

1. 思路：找到第一个岛屿，即第一个 “1” 就对岛屿数量加一，然后将其置位 “0”（这样做的好处是，不用维护 visited 数组）
   1. 定义变量 iNum 用来记录岛屿数量
   2. 定义 m 和 n 用来记录二维数组的边界大小
   3. 通过两层 for 循环遍历数组，若遇到第一个 "1" 就 iNum++，并对 i 位置进行深度遍历
   4. 深度遍历的逻辑如下
      1. 定义 m 和 n 用来记录二维数组的边界大小
      2. 判定非法条件
      3. 标记 i 位置为 “0”
      4. 对 i 位置的上下左右进行 dfs 搜索
2. 注意点：-
3. 完整示例 ```算法小抄题解```

```c++
class Solution {
public:
    int numIslands(vector<vector<char>>& grid) {
        int iNum = 0;
        int m = grid.size();
        int n = grid[0].size();

        // 遍历每个位置
        for(int i = 0; i < m; i++){
            for(int j = 0 ; j < n; j++){
                if(grid[i][j] == '1'){
                    iNum ++;
                    dfs(grid, i, j); // 对 i 位置进行深度搜索
                }
            }
        }

        return iNum;
    }

    void dfs(vector<vector<char>>& grid, int i, int j){
        int m = grid.size();
        int n = grid[0].size();

        // 边界判断
        if( i < 0 || j < 0 || i >= m || j >= n) return ;
        if(grid[i][j] == '0') return;

        // 另岛屿被淹没（置位 0），这样就不用维护 visited 数组
        grid[i][j] = '0';

        dfs(grid, i + 1, j);
        dfs(grid, i - 1, j);
        dfs(grid, i, j + 1);
        dfs(grid, i, j - 1);

        return;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n^2^)
   2. 空间复杂度：O(n)