# 前言

1. BFS 的基础：看[这里](https://www.yuque.com/fromdark/yx0hps/uamm05#4b03c)

# 中等

## [207. 课程表](https://leetcode.cn/problems/course-schedule/)

1. 其他思路：有向无环图的环检测，深度优先遍历

2. 思路

   1. 有向图描述依赖关系，假设有课程先决条件表：先决条件表：`[[3, 0], [3, 1], [4, 1], [4, 2], [5, 3], [5, 4]]` 其对应的有向图是这样的

      ![](.\img\bfs\207_01.png)

   2. 前期准备

      1. 一个入度表，记录各个节点的入度情况
      2. 一个 `unordered_map<int, vector<int>> map` 一个无序 map，其中的键值对为 int 和 vector<int>，分别表示**课程号**和**后续课程号**
      3. 一个队列，实时存放入度为 0 的节点
      4. count，记录进入队列中的课程数

   3. 整体思路

      1. 遍历先决表，记录每个课程号的入度情况，并将有后续课程的课程号存入前导课程的 map 中的 vector<int> 中。用这个来模拟拉链法的哈希表
      2. 然后遍历入度表，将入度为 0 的值存入队列中
      3. 以队列长度为判断条件，进行 while 循环
         1. 每一轮选择当前队列的第一个元素，记录元素值，并将其出队
         2. count ++
         3. 然后在 map 中找到该元素值的后续课程，对其入度进行 -- 操作，若入度为 0，将其加入队列中
      4. 比较 count 和原始课程数的大小，相等则表示可以完成课程

3. 完整示例 `网友题解`，`未手动实现`

```c++
vector<int> inDegree(numCourses);
unordered_map<int, vector<int>> map;
for (int i = 0; i < prerequisites.size(); i++) {
	inDegree[prerequisites[i][0]]++;//当前课程入度值+1
	map[prerequisites[i][1]].push_back(prerequisites[i][0]);//添加依赖他的后续课
}
queue<int> Qu;
for (int i = 0; i < numCourses; i++) {
	if (inDegree[i] == 0) Qu.push(i);//所有入度为0的课入列
}
int count = 0;
while (Qu.size()) {
	int selected = Qu.front();  //当前选的课
	Qu.pop();//出列
	count++;//选课数+1
	vector<int> toEnQueue = map[selected];//获取这门课对应的后续课
	if (toEnQueue.size()) { //确实有后续课
		for (int i = 0; i < toEnQueue.size(); i++) {
			inDegree[toEnQueue[i]]--; //依赖它的后续课的入度-1
			if (inDegree[toEnQueue[i]] == 0) Qu.push(toEnQueue[i]); //如果因此减为0，入列
		}
	}
}
if (count == numCourses) return true;
return false;
```

4. 时空复杂度
   1. 时间复杂度：O(n)
   2. 空间复杂度：O(n)

