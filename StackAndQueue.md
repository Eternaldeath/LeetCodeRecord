# 前言

1. 栈这种数据结构应用的场景：元素匹配与消除

# 简单

## [20. 有效的括号](https://leetcode.cn/problems/valid-parentheses/)

1. 思路：在代码随想录中，关于本题的思路已经讲的非常完整了，可以阅读这个[链接](https://programmercarl.com/0020.%E6%9C%89%E6%95%88%E7%9A%84%E6%8B%AC%E5%8F%B7.html#%E6%80%9D%E8%B7%AF)
2. 注意点：厘清楚哪些情况不合法
3. 完整示例 ```代码随想录题解```，```未手动实现```

```C++
class Solution {
public:
    bool isValid(string s) {
        if (s.size() % 2 != 0) return false; 
        for(int i = 0; i < s.size(); i++){
            if(s[i] == '{') bracket.push('}');
            else if(s[i] == '[') bracket.push(']');
            else if(s[i] == '(') bracket.push(')');
            else if(bracket.empty() || bracket.top() != s[i]) return false;
            else bracket.pop();
        }

        return bracket.empty();
    }
private:
    stack<char> bracket;
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(n)

## [225. 用队列实现栈](https://leetcode.cn/problems/implement-stack-using-queues/)

1. 思路：本题和 232 题不同，在本题中，我们使用两个队列 a，b，b 队列用作备份。举例来说，假设 a 队列入队 1,2,3，然后此时要按照栈的方式出栈，即弹出 3，我们先将 a 队列中元素出队 1,2 到 b 队列中，这样 a 队列仅剩下 3，于是弹出 3，然后将 b 队列的值复制给 a，这样就实现了队列模拟栈的功能

![](.\img\stack_queue\queue_stack.png)

2. 注意点：-
3. 完整代码 ```代码随想录题解```

```C++
class MyStack {
public:
    MyStack() {

    }
    
    // 入栈
    void push(int x) {
        que1.push(x);
    }
    
    // 出栈
    int pop() {
        int que1Size = que1.size();
        que1Size--; // 保留最后一个在 que1 的中元素作为出栈的元素

        while(que1Size--){
            que2.push(que1.front()); // 为 que2 队列填入 que1 出队的元素
            que1.pop();
        }

        int res = que1.front();
        que1.pop();
        que1 = que2; // 将备份队列 que2 中的元素全部复制给 que1

        while(!que2.empty()){
            que2.pop();
        }

        return res;
    }
    
    int top() {
        return que1.back();
    }
    
    bool empty() {
        return que1.empty();
    }
private:
    queue<int> que1; 
    queue<int> que2; // 备份
};
```

4. 时空复杂度
   1. 时间复杂度：-
   2. 空间复杂度：O(n)

## [232. 用栈实现队列](https://leetcode.cn/problems/implement-queue-using-stacks/)

1. 思路：队列的性质是先进先出，栈的性质是先进后出，为了用栈模拟队列的行为，题目给出两个栈来操作，我们用一个栈作为入栈，模拟入队，一个栈作为出栈，模拟出队，而数据需要在入栈和出栈之间做一次移动，即当有元素弹出的时候，将入栈中所有数据放入到出栈中

![](.\img\stack_queue\stack_queue.png)

2. 注意点：每次有元素弹出时，必须将所有入栈中的元素放入出栈中
3. 完整示例 ```代码随想录题解```

```C++
class MyQueue {
public:
    MyQueue() {

    }
    
    void push(int x) {
        stIn.push(x);
    }
    
    int pop() {
        if(stOut.empty()){
            while(!stIn.empty()){
                stOut.push(stIn.top());
                stIn.pop();
            }
        }

        int res = stOut.top();
        stOut.pop();

        return res;
    }
    
    int peek() {
        int res = this->pop();
        stOut.push(res);
        return res;
    }
    
    bool empty() {
        return stIn.empty() && stOut.empty();
    }
private:
    stack<int> stIn;
    stack<int> stOut;
};
```

4. 时空复杂度
   1. 时间复杂度：-
   2. 空间复杂度：O(n)

## [1047. 删除字符串中的所有相邻重复项](https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string/)

1. 思路：本题的思路和 **20.有效括号** 一样，都是元素匹配与消除的问题，代码的大体结构和逻辑都类似
2. 注意点：-
3. 完整示例 ```代码随想录题解```

```C++
class Solution {
public:
    string removeDuplicates(string s) {
        for(char se : s){
            if(st.empty() || st.top() != se) st.push(se);
            else {
                st.pop();
            }
        } 

        while(!st.empty()){
            res += st.top();
            st.pop();
        }

        reverse (res.begin(), res.end()); // 此时字符串需要反转一下
        return res;
    }
private:
    stack<char> st;
    string res;
};
```

# 中等

## [150. 逆波兰表达式求值](https://leetcode.cn/problems/evaluate-reverse-polish-notation/)

1. 思路：本题是一道经典的栈应用的案例，我们的思路如下，将字符为数字的情况入栈，当遇到字符为计算符号（+，-，*，/）时，则将栈中的两个数字弹出计算（**因为题目给出的计算符号都是二元操作符**），所以正常逻辑，栈中一定有两个数字，将计算的结果再入栈留待后续使用，最后栈中一定只有一个计算结果，将其出栈即可
2. 注意点：-
3. 完整示例

```C++
class Solution {
public:
    int evalRPN(vector<string>& tokens) {
        for(int i = 0; i < tokens.size(); i++){
            if(tokens[i] == "+" || tokens[i] == "-" || tokens[i] == "*" || tokens[i] == "/") {
                if(tokens[i] == "*"){
                    int tempRight = st.top();st.pop();
                    int tempLeft = st.top();st.pop();
                    int res = tempLeft * tempRight;
                    st.push(res);
                } else if (tokens[i] == "/"){
                    int tempRight = st.top();st.pop();
                    int tempLeft = st.top();st.pop();
                    int res = tempLeft / tempRight;
                    st.push(res);
                } else if (tokens[i] == "+"){
                    int tempRight = st.top();st.pop();
                    int tempLeft = st.top();st.pop();
                    int res = tempLeft + tempRight;
                    st.push(res);
                } else if (tokens[i] == "-"){
                    int tempRight = st.top();st.pop();
                    int tempLeft = st.top();st.pop();
                    int res = tempLeft - tempRight;
                    st.push(res);
                }
            } else {
                st.push(atoi(tokens[i].c_str()));
            }
        }

        return st.top();
    }
private:
    stack<int> st;
};
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(n)

## [347. 前 K 个高频元素](https://leetcode.cn/problems/top-k-frequent-elements/)

1. 看[这里](https://programmercarl.com/0347.%E5%89%8DK%E4%B8%AA%E9%AB%98%E9%A2%91%E5%85%83%E7%B4%A0.html#%E6%80%9D%E8%B7%AF)