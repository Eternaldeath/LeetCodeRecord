# 前言

1. 描述：回溯问题本质就是在一棵决策树上，在叶子节点“采摘”合法的结果。关于回溯问题的基本原理，看[这里](https://www.yuque.com/fromdark/yx0hps/oyke34248lhubkyy)
2. 回溯问题模板

```python
result = []
def backtrack(路径, 选择列表):
    if 满足结束条件:
        result.add(路径)
        return
    
    for 选择 in 选择列表:
        做选择
        backtrack(路径, 选择列表)
        撤销选择
```

# 组合系列问题

## [40. 组合总和 II_中等](https://leetcode.cn/problems/combination-sum-ii/)

1. 思路：本题为了对答案进行去重，所以我们采用这样的思路，先对原数组进行排序，这样相同元素就相邻了，我们在相邻元素使用时，仅使用一次即可
2. 注意点：-
3. 完整示例 `代码随想录题解`

```c++
class Solution {
private:
    vector<vector<int>> result;
    vector<int> path;
    void backtracking(vector<int>& candidates, int target, int sum, int startIndex) {
        if (sum == target) {
            result.push_back(path);
            return;
        }
        for (int i = startIndex; i < candidates.size() && sum + candidates[i] <= target; i++) {
            // 要对同一树层使用过的元素进行跳过
            if (i > startIndex && candidates[i] == candidates[i - 1]) {
                continue;
            }
            sum += candidates[i];
            path.push_back(candidates[i]);
            backtracking(candidates, target, sum, i + 1); // 和39.组合总和的区别1，这里是i+1，每个数字在每个组合中只能使用一次
            sum -= candidates[i];
            path.pop_back();
        }
    }

public:
    vector<vector<int>> combinationSum2(vector<int>& candidates, int target) {
        path.clear();
        result.clear();
        // 首先把给candidates排序，让其相同的元素都挨在一起。
        sort(candidates.begin(), candidates.end());
        backtracking(candidates, target, 0, 0);
        return result;
    }
};
```

4. 时间复杂度：O(n*2^n^)；空间复杂度：O(n)

## [77. 组合_中等](https://leetcode.cn/problems/combinations/)

1. 思路：基本就是套回溯的模板，然后大体代码类似《全排列_中等》这道题，不过做一些小的修改
2. 注意点：因为它不能要求重复数据的出现，即 [1,2] 和 [2,1] 是一样的数据，只能出现一次，因此需要对这个做一下判断
3. 完整示例 

```c++
class Solution {
public:
    vector<vector<int>> combine(int n, int k) {
        vector<int> track;
        vector<bool> uesd(n, false);

        backtrack(track, k, n, 1);

        return res;
    }

    void backtrack(vector<int>& track, int k, int n, int cur){
        if(track.size() == k){
            res.push_back(track);
            return ;
        }

        for(int i = cur; i <= n; i++){
            track.push_back(i);

            backtrack(track, k, n, i + 1);

            track.pop_back();
        }
    }
private:
    vector<vector<int>> res;
};
```

4. 时间复杂度：O(n!)；空间复杂度：O(n)

## [216. 组合总和 III](https://leetcode.cn/problems/combination-sum-iii/)

1. 思路：-
2. 注意点：-
3. 完整示例

```c++
class Solution {
public:
    vector<vector<int>> combinationSum3(int k, int n) {
        int tempSum = 0;
        backtrack(track, 1, k, n, tempSum);

        return res;
    }

    void backtrack(vector<int>& track, 
                   int startIndex,
                   int k,
                   int n,
                   int& tempSum){
        if(track.size() == k){
            if(tempSum == n) res.push_back(track);
            return ;
        }

        for(int i = startIndex; i <= 9; i++){
            // if(tempSum > n) break;

            track.push_back(i);
            tempSum += i;
            backtrack(track, i + 1, k, n, tempSum);
            track.pop_back();
            tempSum -= i;
        }
    }
private:
    vector<vector<int>> res;
    vector<int> track;
};
```

4. 时间复杂度：O(n * 2^n)；空间复杂度：O(n)

# 分割系列问题

## [93. 复原 IP 地址](https://leetcode.cn/problems/restore-ip-addresses/)

1. 思路：要分析出这是和 131 类似的题目
2. 注意点：本题的比较复杂，需要把大问题分解成几个小问题来解决（比如判断是否合法的模块），而不是写在一堆
3. 完整示例 `代码随想录题解`，`未手动实现`

```c++
class Solution {
private:
    vector<string> result;// 记录结果
    // startIndex: 搜索的起始位置，pointNum:添加逗点的数量
    void backtracking(string& s, int startIndex, int pointNum) {
        if (pointNum == 3) { // 逗点数量为3时，分隔结束
            // 判断第四段子字符串是否合法，如果合法就放进result中
            if (isValid(s, startIndex, s.size() - 1)) {
                result.push_back(s);
            }
            return;
        }
        for (int i = startIndex; i < s.size(); i++) {
            if (isValid(s, startIndex, i)) { // 判断 [startIndex,i] 这个区间的子串是否合法
                s.insert(s.begin() + i + 1 , '.');  // 在i的后面插入一个逗点
                pointNum++;
                backtracking(s, i + 2, pointNum);   // 插入逗点之后下一个子串的起始位置为i+2
                pointNum--;                         // 回溯
                s.erase(s.begin() + i + 1);         // 回溯删掉逗点
            } else break; // 不合法，直接结束本层循环
        }
    }
    // 判断字符串s在左闭又闭区间[start, end]所组成的数字是否合法
    bool isValid(const string& s, int start, int end) {
        if (start > end) {
            return false;
        }
        if (s[start] == '0' && start != end) { // 0开头的数字不合法
                return false;
        }
        int num = 0;
        for (int i = start; i <= end; i++) {
            if (s[i] > '9' || s[i] < '0') { // 遇到非数字字符不合法
                return false;
            }
            num = num * 10 + (s[i] - '0');
            if (num > 255) { // 如果大于255了不合法
                return false;
            }
        }
        return true;
    }
public:
    vector<string> restoreIpAddresses(string s) {
        result.clear();
        if (s.size() < 4 || s.size() > 12) return result; // 算是剪枝了
        backtracking(s, 0, 0);
        return result;
    }
};

```

4. 时间复杂度：O(3^4^)；空间复杂度：O(n);

## [131. 分割回文串](https://leetcode.cn/problems/palindrome-partitioning/)

1. 思路：本题较为复杂，我们把问题拆分成如下的小问题来逐个击破

   1. 分割字符串：首先我们要理解题目要求分割的题意，也就是如何分割。大体的思路是，我设置两个指针（数组下标），然后判断两个指针之间的子串是否满足回文即可

      ![](.\img\backtrack\131_01.png)

   2. 判断回文：本题采用双指针的方式，一个指针 start 指向子串起始，一个指针 end 指向子串末尾，向中间靠拢，若某一处 start 指针和 end 指针的值不相同，则不是回文

2. 注意点：-

3. 完整示例 `代码随想录题解`，`未手动实现`

```c++
class Solution {
private:
    vector<vector<string>> result;
    vector<string> path; // 放已经回文的子串
    void backtracking (const string& s, int startIndex) {
        // 如果起始位置已经大于s的大小，说明已经找到了一组分割方案了
        if (startIndex >= s.size()) {
            result.push_back(path);
            return;
        }
        for (int i = startIndex; i < s.size(); i++) {
            if (isPalindrome(s, startIndex, i)) {   // 是回文子串
                // 获取[startIndex,i]在s中的子串
                string str = s.substr(startIndex, i - startIndex + 1);
                path.push_back(str);
            } else {                                // 不是回文，跳过
                continue;
            }
            backtracking(s, i + 1); // 寻找i+1为起始位置的子串
            path.pop_back(); // 回溯过程，弹出本次已经添加的子串
        }
    }
    // 通过双指针判断回文字符串
    bool isPalindrome(const string& s, int start, int end) {
        for (int i = start, j = end; i < j; i++, j--) {
            if (s[i] != s[j]) {
                return false;
            }
        }
        return true;
    }
public:
    // 原题目初始函数
    vector<vector<string>> partition(string s) {
        result.clear();
        path.clear();
        backtracking(s, 0);
        return result;
    }
};
```

本题的代码逻辑图如下图所示：

![](.\img\backtrack\131_02.png)

4. 时间复杂度：O(n * 2^n^)；空间复杂度：O(n)

# 子集系列问题

## [78. 子集](https://leetcode.cn/problems/subsets/)

1. 思路：本题依旧是套用回溯的模板，不过和模板相比，回溯的基本思路是在叶子结点收割答案，但本题是在每一个节点处收割答案，如下图

   ![](.\img\backtrack\78_01.png)

2. 注意点：本题是在每个节点都要收割答案

3. 完整示例

```c++
class Solution {
public:
    vector<vector<int>> subsets(vector<int>& nums) {
        vector<int> track;
        int cur = 0;
        res.push_back(vector<int>());

        backtrack(track, cur, nums);

        return res;
    }

    void backtrack(vector<int>& track, int cur, vector<int>& nums){
        if(cur == nums.size())  return ;

        for(int i = cur; i < nums.size(); i++){
            track.push_back(nums[i]);
            res.push_back(track);

            backtrack(track, i + 1, nums);

            track.pop_back();
        }
    }
private:
    vector<vector<int>> res;
};
```

4. 时间复杂度：O(n!)；空间复杂度：O(n)

## [90. 子集 II](https://leetcode.cn/problems/subsets-ii/)

1. 思路：
2. 注意点：-
3. 完整示例

```c++
class Solution {
public:
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<int> used(nums.size(), 0);
        sort(nums.begin(), nums.end());
        res.push_back(vector<int>());
        backtrack(nums, used, 0);
        return res;
    }

    void backtrack(vector<int>& nums, vector<int>& used, int cur){
        if(cur == nums.size()) return ;

        for(int i = cur; i < nums.size(); i++){
            if(i >0 && nums[i] == nums[i-1] && used[i-1] == 0) continue;

            track.push_back(nums[i]);
            used[i] = 1;
            res.push_back(track);
            backtrack(nums, used, i + 1);
            track.pop_back();
            used[i] = 0;
        }
    }
private:
    vector<vector<int>> res;
    vector<int> track;
    int cur;
};
```

4. 时间复杂度：

# 排列系列问题

## [46. 全排列_中等](https://leetcode.cn/problems/permutations/)

1. 思路
   1. 准备三个数组
      1. res 数组用来记录收集全部的答案
      2. track 数组用来记录当前路径下收集的结果
      3. used 数组用来标记当前节点是否被选择过，这是一个布尔值
   2. 根据回溯算法模板
      1. 结束条件：track 数组长度 == 原数组长度
      2. 做选择的具体操作：将当前节点加入 track 数组，并且标记 uesd[当前节点] 为 true，表示访问过
      3. 撤销选择的具体操作：将当前节点剔除 track 数组，并且标记 uesd[当前节点] 为 false，表示未访问过（即访问完毕）
2. 注意点：-
3. 完整示例 `算法小抄题解`

```C++
class Solution {
public:
    vector<vector<int>> permute(vector<int>& nums) {
        vector<bool> usedFlag(nums.size(), false);
        backtrack(nums, usedFlag);

        return res;
    }

    void backtrack(vector<int>& nums, vector<bool>& usedFlag){
        if(track.size() == nums.size()){
            res.push_back(track);
            return ;
        }

        for(int i = 0; i < nums.size(); i++){
            // 若被标记过，则不再计入 track
            if(usedFlag[i]) continue;

            // 做选择
            track.push_back(nums[i]);
            usedFlag[i] = true;

            backtrack(nums, usedFlag);

            // 撤销选择
            track.pop_back();
            usedFlag[i] = false;
        }
    }
private:
    vector<vector<int>> res;
    vector<int> track;
    
};
```

4. 时间复杂度：O(n!)；空间复杂度：O(n)

## [47. 全排列 II_中等](https://leetcode.cn/problems/permutations-ii/)

1. 思路：本题和 46 题的区别在于，它给出的数组中存在重复的数字，比如 [1,1,2]，这里数组的前两项就重复了，因此我们需要对其进行去重操作。其去重的核心代码如下，意思就是，如果满足当前操作的数字和前一个数字相同且前一个数字已经没有被使用，那么判定为重复

   ![](.\img\backtrack\47_01.png)

```C++
if(i > 0 && nums[i-1] == nums[i] &&  used[i-1] == 0) continue;
```

2. 注意点：-
3. 完整示例 `代码随想录题解`

```c++
class Solution {
public:
    vector<vector<int>> permuteUnique(vector<int>& nums) {
        vector<int> uesd(nums.size(), 0);
        sort(nums.begin(), nums.end()); // 排序
        backtrack(nums, track, uesd);
        return res;
    }

    void backtrack(vector<int>& nums, 
                   vector<int>& track,
                   vector<int>& used){
        if(track.size() == nums.size()){
            res.push_back(track);
            return ;
        }

        for(int i = 0; i < nums.size(); i++){
            if(i > 0 && nums[i-1] == nums[i] &&  used[i-1] == 0) continue;
            if(used[i] == 1) continue;

            track.push_back(nums[i]);
            used[i] = 1;
            backtrack(nums, track, used);
            track.pop_back();
            used[i] = 0;
        }
    }
private:
    vector<vector<int>> res;
    vector<int> track;
};
```



# 棋盘问题

## [51. N 皇后_困难](https://leetcode.cn/problems/n-queens/)

1. 思路：本质上还是套用回溯算法的框架，有一些细节的处理在下面阐述

   1. 根据题意我们需要判断皇后所在的行列和斜线没有其他皇后，所以我们定义一个 isValid 函数来检查 \[i][j] 这个位置皇后的安全性

      实际上只需要检查左上角，正上方，右上角即可，因为下方的位置还没有遍历到，所以无需检查

2. 注意点：-

3. 完整示例 `算法小抄题解`

```c++
class Solution {
public:
    vector<vector<string>> res;
    vector<vector<string>> solveNQueens(int n) {
        // 构造棋盘
        vector<string> board(n, string(n, '.'));
        backtrack(board, 0);

        return res;
    }

    void backtrack(vector<string>& board, int row){
        if(row == board.size()){
            res.push_back(board);
            return ;
        }

        int n = board[row].size();
        for(int col = 0; col < n; col++){
            // 检查皇后位置是否合法
            if(!isValid(board, row, col)) continue;

            // 放置皇后
            board[row][col] = 'Q';
            backtrack(board, row + 1);
            // 撤销放置
            board[row][col] = '.';
        }
    }

    bool isValid(vector<string>& board, int row, int col) {
    int n = board.size();
    // 检查列是否有皇后互相冲突
    for (int i = 0; i <= row; i++) {
        if (board[i][col] == 'Q')
            return false;
    }
    // 检查右上方是否有皇后互相冲突
    for (int i = row - 1, j = col + 1; 
            i >= 0 && j < n; i--, j++) {
        if (board[i][j] == 'Q')
            return false;
    }
    // 检查左上方是否有皇后互相冲突
    for (int i = row - 1, j = col - 1;
            i >= 0 && j >= 0; i--, j--) {
        if (board[i][j] == 'Q')
            return false;
    }
    return true;
}
};
```

4. 时间复杂度：O(n!)；空间复杂度：O(n)

# 其他

## [491. 递增子序列_中等](https://leetcode.cn/problems/non-decreasing-subsequences/)

1. 思路
   1. 终止条件：本题中其实无需设置终止条件，因为递归中 startIndex 会有 +1 操作，不会无限递归。但是在终止条件的地方，可以添加 push_back 操作，即当子序列的长度 >= 2 的时候，将其放入结果 vector 中
   2. 去重的逻辑：本题中去重出现在同一层次，若出现了相同的子序列组合应该去重，但不同层次不存在去重，如下图。针对这种本层需要去重，但不同层不需要去重的情况，可以使用在 for 循环中添加 unordered_set 的方式，并且不对其进行任何 pop 操作，因为其出现在每轮的 for 循环中，而进入递归的下一层 for 循环不会受到影响，如下面的代码

![](.\img\array\419_01.png)

```c++
// 单层搜索
unordered_set<int> uset; // 在每一层设置新的 set，当递归进入下一层时，程序定义的是新的 set
for (int i = startIndex; i < nums.size(); i++) {
    if ((!path.empty() && nums[i] < path.back())
            || uset.find(nums[i]) != uset.end()) {
            continue;
    }
    uset.insert(nums[i]); // 记录这个元素在本层用过了，本层后面不能再用了
    path.push_back(nums[i]);
    backtracking(nums, i + 1);
    path.pop_back();
}
```

2. 注意点：-
3. 完整示例

```c++
class Solution {
private:
    vector<vector<int>> res;
    vector<int> track;
public:
    void backtracking(vector<int>& nums, int startIndex){
        if(track.size() >= 2){
            res.push_back(track);
        }

        unordered_set<int> uset;
        for(int i = startIndex; i < nums.size(); ++i){
            // 哪些情况不向 track 中添加元素呢？
            // 1. 下一个元素比上一个元素小，当 i 为 0 是不进行比较，所以用 !track.empty() 来避开
            // 2. uset 中重复的元素
            if((!track.empty() && nums[i] < track.back()) || uset.find(nums[i]) != uset.end()) continue;
            uset.insert(nums[i]);
            track.push_back(nums[i]);
            backtracking(nums, i + 1);
            track.pop_back();
        }
    }
    vector<vector<int>> findSubsequences(vector<int>& nums) {
        res.clear();
        track.clear();
        backtracking(nums, 0);

        return res;
    }
};
```

4. 时间复杂度：O(n * 2^n)；空间复杂度：O(n)