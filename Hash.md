# 前言

1. 关于哈希表在刷 leetcode 时的一些必备的知识，看[这里](https://programmercarl.com/%E5%93%88%E5%B8%8C%E8%A1%A8%E7%90%86%E8%AE%BA%E5%9F%BA%E7%A1%80.html#%E5%B8%B8%E8%A7%81%E7%9A%84%E4%B8%89%E7%A7%8D%E5%93%88%E5%B8%8C%E7%BB%93%E6%9E%84)
2. 这里是对上面链接的总结
   1. 使用哈希法解决问题的三种数据结构
      1. 数组：适合解决范围固定的情况
      2. set（集合）
      3. map（映射）
   2. C++ 对 set 和 map 又提供了三种更细分的数据结构
      1. 若数据大小比较小
      2. 优先使用 unordered_*，因为它最快
      3. 若要有序，就是用 set 或者 map
      4. 若要有序且重复数据，就要用 multi*
      5. set 和 map 的区别在于，map 是 kv 数据结构，其中的限制发生在 key 上，而没有对 value 有限制

| 集合               | 底层实现 | 是否有序 | 数值是否可以重复 | 能否更改数值 | 查询效率 | 增删效率 |
| ------------------ | -------- | -------- | ---------------- | ------------ | -------- | -------- |
| std::set           | 红黑树   | 有序     | 否               | 否           | O(log n) | O(log n) |
| std::multiset      | 红黑树   | 有序     | 是               | 否           | O(logn)  | O(logn)  |
| std::unordered_set | 哈希表   | 无序     | 否               | 否           | O(1)     | O(1)     |

| 映射               | 底层实现 | 是否有序 | 数值是否可以重复 | 能否更改数值 | 查询效率 | 增删效率 |
| ------------------ | -------- | -------- | ---------------- | ------------ | -------- | -------- |
| std::map           | 红黑树   | key有序  | key不可重复      | key不可修改  | O(logn)  | O(logn)  |
| std::multimap      | 红黑树   | key有序  | key可重复        | key不可修改  | O(log n) | O(log n) |
| std::unordered_map | 哈希表   | key无序  | key不可重复      | key不可修改  | O(1)     | O(1)     |

3. 数据结构应用的场景：快速在集合里判断某个元素是否出现
4. 数据结构的缺点：需要额外的数组

# 简单

## [1. 两数之和](https://leetcode.cn/problems/two-sum/)

1. 思路：本题思路见于[此处](https://programmercarl.com/0001.%E4%B8%A4%E6%95%B0%E4%B9%8B%E5%92%8C.html#%E6%80%9D%E8%B7%AF)
2. 注意点：-
3. 完整示例 ```代码随想录题解```

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        std::unordered_map <int,int> map;
        for(int i = 0; i < nums.size(); i++) {
            // 遍历当前元素，并在map中寻找是否有匹配的key
            auto iter = map.find(target - nums[i]); 
            if(iter != map.end()) {
                return {iter->second, i};
            }
            // 如果没找到匹配对，就把访问过的元素和下标加入到map中
            map.insert(pair<int, int>(nums[i], i)); 
        }
        return {};
    }
};
```

4. 时空复杂度：均为 O(n)

## [202. 快乐数](https://leetcode.cn/problems/happy-number/)

1. 思路
   1. 在每一次递归的开头，比较 n 值是否在哈希表中，若在则说明陷入循环，退出
   2. 若不再，则每一次将 n 存入哈希表中（本题选用 unordered_set 这种哈希表）
   3. 然后按照题目要求计算新的中间过渡值（例如，1^2^ + 9^2^ = 82，这里 82 就是 n），用的是自己写的 split 函数
   4. 若 n 为 1，说明找到了快乐数
2. 注意点：-
3. 完整示例

```c++
class Solution {
public:
    bool isHappy(int n) {
        if(un_set.find(n) != un_set.end()) return false;
        else un_set.insert(n);

        if(n == 1) return true;

        vector<int> splitNum;
        long long happTempNum = 0;

        split(splitNum, n);
        for(int v : splitNum){
            happTempNum += v * v;
        }

        return isHappy(happTempNum);
    }

    void split(vector<int> &v, int n){
        while(n >= 1){
            v.push_back(n % 10);
            n /= 10;
        }
    }
private:
    unordered_set<int> un_set;
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(n)

## [242. 有效的字母异位词](https://leetcode.cn/problems/valid-anagram/)

1. 思路：关于什么是字母异位词在题目中有所讲解，这里不再重复。本题使用的是数组来模拟哈希表从而完成题解。我们知道 a ~ z 有 26 个字母，它们的 ASCII 码是连续的，根据这个特点，我们可以利用数组来模拟哈希表的效果，具体如下。

​		假设我们有给定字符串 ```s = “abbc”``` 和 ```hash[26]``` 这个数组，通过 ```s[i]``` 的方式访问每个字符，我们用 ```s[i]-'a'``` 来计算每个字符距离 		```a``` 的偏移量，得到的这		个值可以作为数组的下标，举个例子，比如 ```s[0] - 'a' = 0```，那么说明 hash[0] 这个位置有一个 a 字符		存		储，于是执行 ```hash[0]++```，同理，```s[1] - 'a' = 1```，```s[2] - 'a' = 1```，说明 ```hash[1]``` 这个位置有两个 ```b``` 字符存储，于是执行 		```hash[1]++``` 两次的操作。

​	同理，我们在验证给定字符串 ```t``` 时，只需要执行对应的 ```--``` 操作，最后用 for 循环来判断 ```hash[i]``` 是否有不等于 0 的位置即可，若有说明存在无效的异	位词。

2. 注意点：-
3. 完整示例 ```代码随想录题解```

```C++
class Solution {
public:
    bool isAnagram(string s, string t) {
        int hash[26] = {0};

        for(int i = 0; i < s.size(); i++){
            hash[s[i] - 'a']++; 
        }

        for(int i = 0; i < t.size(); i++){
            hash[t[i] - 'a']--;
        }

        for(int i = 0; i < 26; i++){
            if(hash[i] != 0) return false;
        }

        return true;
    }
};
```

## [349. 两个数组的交集](https://leetcode.cn/problems/intersection-of-two-arrays/)

1. 思路：用一个 set（这里选用 unordered_set）集合现将 num1 中的数据进行哈希存储，然后用 num2 中的数据进行比较，若存在则放入结果集中，这样做的速度比直接进行暴力循环（O(n^2^)）快得多

   ![](.\img\hash\349.png)

2. 注意点：-

3. 完整示例 ```代码随想录题解```

```c++
class Solution {
public:
    vector<int> intersection(vector<int>& nums1, vector<int>& nums2) {
        unordered_set<int> res_hashset;
        // 将 nums1 的数据映射到 set 哈希表中
        unordered_set<int> nums_hashset(nums1.begin(), nums1.end());

        for(int num : nums2){
            // 首选在 hash 里找得到 num，且 num 不是 hash 末尾
            if(nums_hashset.find(num) != nums_hashset.end()){
                res_hashset.insert(num);
            }
        }

        return vector<int>(res_hashset.begin(), res_hashset.end());

    }
};
```

## [383. 赎金信](https://leetcode.cn/problems/ransom-note/)

1. 思路：
   1. 将 magazine 里面的字符都存入哈希表中
   2. 然后用 ransomNote 里的值去执行 find 操作，匹配哈希表里面的值
   3. 若找到，则将 magazine 里面的值去除
   4. 若找不到则返回 false
   5. 最终遍历完成返回 true
2. 注意点：-
3. 完整示例

```c++
class Solution {
public:
    bool canConstruct(string ransomNote, string magazine) {
        unordered_map<char, int> un_map;

        for(char m : magazine){
            if(un_map.find(m) != un_map.end()) un_map[m]++;
            else un_map.insert(pair<char, int>{m, 1});
        }

        for(char r : ransomNote){
            if(un_map.find(r) != un_map.end() && un_map[r] != 0) un_map[r]--;
            else return false;
        }

        return true;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(n)

# 中等

## [454. 四数相加 II](https://leetcode.cn/problems/4sum-ii/)

1. 思路：见[本文](https://programmercarl.com/0454.%E5%9B%9B%E6%95%B0%E7%9B%B8%E5%8A%A0II.html#%E6%80%9D%E8%B7%AF)的[视频](https://www.bilibili.com/video/BV1Md4y1Q7Yh/?vd_source=fc31b4f33de669564b3e17f5585f1253)部分
2. 注意点：-
3. 完整示例 ```代码随想录题解```

```c++
class Solution {
public:
    int fourSumCount(vector<int>& nums1, vector<int>& nums2, vector<int>& nums3, vector<int>& nums4) {
        unordered_map<int, int> un_map;
        for(int n1 : nums1){
            for(int n2 : nums2){
                if( un_map.find(n1 + n2) != un_map.end()) un_map[n1 + n2]++;
                else un_map.insert(pair<int, int>(n1 + n2, 1));
            }
        }

        int count = 0;
        for(int n3 : nums3){
            for(int n4 : nums4){
                if( un_map.find(0 - (n3 + n4)) != un_map.end()) count += un_map[0 - (n3 + n4)];
            }
        }

        return count;
    }
};
```

4. 时空复杂度
   1. 时间复杂度：O(n^2^)
   2. 空间复杂度：O(n^2^)
