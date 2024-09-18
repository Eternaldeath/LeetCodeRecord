# 前言

1. 为总结题目
   1. [344. 反转字符串](https://leetcode.cn/problems/reverse-string/)

# 简单

## [541. 反转字符串 II](https://leetcode.cn/problems/reverse-string-ii/)

1. 思路：本题的思路很简单，只需要认真处理每一个小步骤的逻辑块即可。在遍历字符串时，只需要每次移动 2k 的长度，让这段跨越中前 k 的部分进行翻转，当进行到最后的时候，判断是小于 k 还是 小于 2k

   ![](.\img\string\514_01.png)

2. 注意点：题目说的是每计数 2k，注意理解这里的意思

3. 完整示例 `代码随想录题解`，`未手动实现`

```c++
class Solution {
public:
    string reverseStr(string s, int k) {
        for (int i = 0; i < s.size(); i += (2 * k)) {
            // 1. 每隔 2k 个字符的前 k 个字符进行反转
            // 2. 剩余字符小于 2k 但大于或等于 k 个，则反转前 k 个字符
            if (i + k <= s.size()) {
                reverse(s.begin() + i, s.begin() + i + k );
            } else {
                // 3. 剩余字符少于 k 个，则将剩余字符全部反转。
                reverse(s.begin() + i, s.end());
            }
        }
        return s;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)

## [剑指 Offer 05. 替换空格](https://leetcode.cn/problems/ti-huan-kong-ge-lcof/)

看这里

## [剑指 Offer 58 - II. 左旋转字符串](https://leetcode.cn/problems/zuo-xuan-zhuan-zi-fu-chuan-lcof/)

1. 思路

   1. 翻转前 n 个字符

   2. 翻转剩余的字符

   3. 翻转两块的字符

      ![](.\img\string\offer_58.png)

2. 注意点：-

3. 完整示例 `代码随想录`

```c++
class Solution {
public:
    string reverseLeftWords(string s, int n) {
        // reverse 两个参数的范围 [first,last)
        reverse(s.begin(), s.begin() + n);
        reverse(s.begin() + n, s.end());
        reverse(s.begin(), s.end());

        return s;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)

# 中等

## [5. 最长回文子串](https://leetcode.cn/problems/longest-palindromic-substring/)

1. 思路：本题采用动态规划的方式
   1. 首先从分情况来看，对于 1 个字符长度的字符串，必然是回文；对于 2 个字符长度的字符串，若两个字符相同则回文；对于 3 个字符长度的字符串，若第一个和最后一个字符相同则回文；若满足前述任意一种情况，则再往后，前后新增字符，若两个字符相同则回文
2. 完整实例

```C++
#include <iostream>
#include <string>
#include <vector>

using namespace std;

class Solution {
public:
    string longestPalindrome(string s) {
        int n = s.size(); // 字符串长度
        // 单个字符必然回文
        if (n < 2) {
            return s;
        }

        int maxLen = 1; // 最长回文子串长度
        int begin = 0;  // 最长回文子串长度起始点
        // dp[i][j] 表示 s[i..j] 是否是回文串
        vector<vector<int>> dp(n, vector<int>(n));
        // 初始化：所有长度为 1 的子串都是回文串
        for (int i = 0; i < n; i++) {
            dp[i][i] = true;
        }
        // 递推开始
        // 先枚举子串长度，L 表示本次判断的子串长度
        for (int L = 2; L <= n; L++) {
            // 枚举左边界，左边界的上限设置可以宽松一些
            for (int i = 0; i < n; i++) {
                // 由 L 和 i 可以确定右边界，即 j - i + 1 = L 得
                int j = L + i - 1;
                // 如果右边界越界，就可以退出当前循环
                if (j >= n) {
                    break;
                }

                if (s[i] != s[j]) {
                    dp[i][j] = false;
                } else {
                    if (j - i < 3) {
                        dp[i][j] = true;
                    } else {
                        dp[i][j] = dp[i + 1][j - 1]; // 该位置的 true 从上一个较短的回文子串出获取
                    }
                }

                // 只要 dp[i][L] == true 成立，就表示子串 s[i..L] 是回文，此时记录回文长度和起始位置
                if (dp[i][j] && j - i + 1 > maxLen) {
                    maxLen = j - i + 1;
                    begin = i;
                }
            }
        }
        return s.substr(begin, maxLen);
    }
};
```

![](.\img\string\5_01.png)

3. 时间复杂度：O(n^2)；空间复杂度：O(n^2)；n 是字符串的长度

## [151. 反转字符串中的单词](https://leetcode.cn/problems/reverse-words-in-a-string/)

1. 思路：本题我们将思路分为三步走
   1. 首先，我们去除字符串中不符合题目要求的空格，采用的方法类似 27 题
   2. 然后，我们用类似 344 题的方式翻转整个字符串
   3. 最后，我们对每个单词进行翻转
2. 注意点：本题的难点在于去除字符串中不符合要求的空格
3. 完整示例 ```代码随想录题解```，```未手动实现```

```c++
class Solution {
public:
    void reverse(string& s, int start, int end){ //翻转，区间写法：左闭右闭 []
        for (int i = start, j = end; i < j; i++, j--) {
            swap(s[i], s[j]);
        }
    }

    void removeExtraSpaces(string& s) {//去除所有空格并在相邻单词之间添加空格, 快慢指针。
        int slow = 0;   //整体思想参考https://programmercarl.com/0027.移除元素.html
        for (int i = 0; i < s.size(); ++i) { //
            if (s[i] != ' ') { //遇到非空格就处理，即删除所有空格。
                if (slow != 0) s[slow++] = ' '; //手动控制空格，给单词之间添加空格。slow != 0说明不是第一个单词，需要在单词前添加空格。
                while (i < s.size() && s[i] != ' ') { //补上该单词，遇到空格说明单词结束。
                    s[slow++] = s[i++];
                }
            }
        }
        s.resize(slow); //slow的大小即为去除多余空格后的大小。
    }

    string reverseWords(string s) {
        removeExtraSpaces(s); //去除多余空格，保证单词之间之只有一个空格，且字符串首尾没空格。
        reverse(s, 0, s.size() - 1);
        int start = 0; //removeExtraSpaces后保证第一个单词的开始下标一定是0。
        for (int i = 0; i <= s.size(); ++i) {
            if (i == s.size() || s[i] == ' ') { //到达空格或者串尾，说明一个单词结束。进行翻转。
                reverse(s, start, i - 1); //翻转，注意是左闭右闭 []的翻转。
                start = i + 1; //更新下一个单词的开始下标start
            }
        }
        return s;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(1)