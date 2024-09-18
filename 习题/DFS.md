# 前言

1. 关于岛屿问题看[这里](https://www.yuque.com/fromdark/yx0hps/nx0bxivgao3lqyki)

# 中等

## [200. 岛屿数量](https://leetcode.cn/problems/number-of-islands/)

1. 思路：找到第一个岛屿，即第一个 “1” 就对岛屿数量加一，然后将其置位 “0”（**这样做的好处是，不用维护 visited 数组**），然后把与该“1”岛屿连接的其他“1”岛屿全部置位“0”
   1. 定义变量 iNum 用来记录岛屿数量
   2. 定义 m 和 n 用来记录二维数组的边界大小
   3. 通过两层 for 循环遍历数组，若遇到第一个 "1" 就 iNum++，并对 i 位置进行深度遍历，对与之相连的其他“1”岛屿置位“0”
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

## [1254. 统计封闭岛屿的数目](https://leetcode.cn/problems/number-of-closed-islands/)

1. 思路：本题表达的含义就是靠边的那些陆地不能算作陆地，这里的靠边不仅仅是最上下左右的那一行一列，以及它们所连接的“陆地”（当然本题不能算作陆地），于是我们首先把这些陆地“淹没”掉，即修改成海水，这样在调用之前的方法来计算岛屿数量就可以了
2. 注意点

```c++
// 错误的做法：这样只是最上下左右的那一行一列被修改为海水，而与之相连的没有。你可以在力扣的该题的 case 中跑出错误的示例来验证
for (int j = 0; j < n; j++) {
    grid[0][j] = 1;
    grid[m-1][j] = 1;
}
for (int i = 0; i < m; i++) {
    grid[i][0] = 1;
    grid[i][n-1] = 1;
}

// 正确的做法
for (int j = 0; j < n; j++) {
    // 把靠上边的岛屿淹掉
    dfs(grid, 0, j);
    // 把靠下边的岛屿淹掉
    dfs(grid, m - 1, j);
}
for (int i = 0; i < m; i++) {
    // 把靠左边的岛屿淹掉
    dfs(grid, i, 0);
    // 把靠右边的岛屿淹掉
    dfs(grid, i, n - 1);
}
```

3. 完整示例 `算法小抄`

```c++
class Solution {
public:
    // 主函数：计算封闭岛屿的数量
    int closedIsland(vector<vector<int>>& grid) {
        int m = grid.size(), n = grid[0].size();
        for (int j = 0; j < n; j++) {
            // 把靠上边的岛屿淹掉
            dfs(grid, 0, j);
            // 把靠下边的岛屿淹掉
            dfs(grid, m - 1, j);
        }
        for (int i = 0; i < m; i++) {
            // 把靠左边的岛屿淹掉
            dfs(grid, i, 0);
            // 把靠右边的岛屿淹掉
            dfs(grid, i, n - 1);
        }
        // 遍历 grid，剩下的岛屿都是封闭岛屿
        int res = 0;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (grid[i][j] == 0) {
                    res++;
                    dfs(grid, i, j);
                }
            }
        }
        return res;
    }

    // 从 (i, j) 开始，将与之相邻的陆地都变成海水
    void dfs(vector<vector<int>>& grid, int i, int j) {
        int m = grid.size(), n = grid[0].size();
        if (i < 0 || j < 0 || i >= m || j >= n) {
            return;
        }
        if (grid[i][j] == 1) {
            // 已经是海水了
            return;
        }
        // 将 (i, j) 变成海水
        grid[i][j] = 1;
        // 淹没上下左右的陆地
        dfs(grid, i + 1, j);
        dfs(grid, i, j + 1);
        dfs(grid, i - 1, j);
        dfs(grid, i, j - 1);
    }
};
```

4. 时间复杂度：O(n\*m)；空间复杂度：O(1)