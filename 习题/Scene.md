# 简单的画图程序

## 功能

1. 画布大小 100 * 100，由方格组成，以 [0,0] 为起点
2. 绘图函数：输入起始行列和宽高，绘制矩形，要求矩形不能重叠或超出画布范围。不满足要求输出 false，否则输出 true
3. 删除矩形函数：输入起始行列和宽高，在这个范围内的矩形被删除。输出被删除矩形的个数
4. 查询函数：输入无，输出当前画布中所有矩形组成的组成的面积

## 关键点

1. 矩形定位：通过两个二维 vector 来定义矩形，第一个 vector 记录矩形的起始行列，第二个 vector 记录矩形的结束行列。第二维的 vector 记录行和列

```c++
vector<vector<int>> beginRowAndColGather;
vector<vector<int>> endRowAndColGather;
```

​	由于只提供了起始行列，这里要计算结束行列，需要通过宽高来进行计算：

```c++
结束行：row + height - 1
结束列：col + width - 1
```

2. 判断是否重叠：这里采用[分离轴定理在矩形重叠检测上的应用来判断](https://whybee.blog.csdn.net/article/details/148530410?spm=1001.2014.3001.5502)
3. 在【删除矩形函数】中，我们需要在遍历迭代器时，删除顺序容器的元素，为了保证删除元素后，迭代器不失效，我们采用[这些](https://whybee.blog.csdn.net/article/details/148531796?spm=1001.2014.3001.5502)方式避免这个问题

## 代码示例

```c++
#pragma once
#include <vector>

using namespace std;

class PbrushSystem {
public:
	vector<vector<int>> beginRowAndColGather;
	vector<vector<int>> endRowAndColGather;

	PbrushSystem()
	{

	}

	bool IsDrawOut(vector<int>& rowAndCol) {
		if (rowAndCol[0] < 0 || rowAndCol[0] > 99 || rowAndCol[1] < 0 || rowAndCol[1] > 99) return true;
		return false;
	}

	bool IsOverLap(vector<int>& beginRowAndCol, vector<int>& endRowAndCol) {
		for (int i = 0; i < beginRowAndColGather.size(); ++i) {
			if (beginRowAndCol[0] <= endRowAndColGather[i][0] &&
				beginRowAndCol[1] <= endRowAndColGather[i][1] &&
				endRowAndCol[0] >= beginRowAndColGather[i][0] &&
				endRowAndCol[1] >= beginRowAndColGather[i][1]) {
				return true;
			}
		}
		return false;
	}

	bool DrawRectangle(int row, int col, int width, int height)
	{
		vector<int> beginRowAndCol = { row, col };
		vector<int> endRowAndCol = { row + height - 1 , col + width - 1};

		// 判断是否超出边界
		if (IsDrawOut(beginRowAndCol) || IsDrawOut(endRowAndCol)) return false;

		// 判断是否重叠
		if (IsOverLap(beginRowAndCol, endRowAndCol)) return false;

		// 绘制成功
		beginRowAndColGather.push_back(beginRowAndCol);
		endRowAndColGather.push_back(endRowAndCol);
		return true;
	}

	int EraseArea(int row, int col, int width, int height)
	{
		vector<int> beginRowAndCol = { row, col };
		vector<int> endRowAndCol = { row + height - 1 , col + width - 1 };
		int eraseNum{ 0 };

		for (int i = 0; i < beginRowAndColGather.size();) {
			if (beginRowAndCol[0] <= endRowAndColGather[i][0] &&
				beginRowAndCol[1] <= endRowAndColGather[i][1] &&
				endRowAndCol[0] >= beginRowAndColGather[i][0] &&
				endRowAndCol[1] >= beginRowAndColGather[i][1]) {
				beginRowAndColGather.erase(beginRowAndColGather.begin() + i);
				endRowAndColGather.erase(endRowAndColGather.begin() + i);
				eraseNum++;
			}
			else {
				++i;
			}
		}
		return eraseNum;
	}

	int QueryArea()
	{
		if (beginRowAndColGather.size() == 0) return 0;
		int minBeginRow = beginRowAndColGather[0][0];
		int minBeginCol = beginRowAndColGather[0][1];
		int maxEndRow = endRowAndColGather[0][0];
		int maxEndCol = endRowAndColGather[0][1];

		for (int i = 1; i < beginRowAndColGather.size(); ++i) {
			if (minBeginRow > beginRowAndColGather[i][0]) minBeginRow = beginRowAndColGather[i][0];
			if (minBeginCol > beginRowAndColGather[i][1]) minBeginCol = beginRowAndColGather[i][1];
			if (maxEndRow < endRowAndColGather[i][0])   maxEndRow = endRowAndColGather[i][0];
			if (maxEndCol < endRowAndColGather[i][1])   maxEndCol = endRowAndColGather[i][1];
		}

		return (maxEndRow - minBeginRow + 1) * (maxEndCol - minBeginCol + 1);
	}
};
```

测试

```bash
PbrushSystem pb; // null
cout << pb.QueryArea() << endl;  // 0
cout << pb.DrawRectangle(0, 1, 5, 4) << endl; // true
cout << pb.DrawRectangle(0, 7, 1, 1) << endl; // true
cout << pb.DrawRectangle(2, 7, 1, 3) << endl; // true
cout << pb.DrawRectangle(5, 3, 2, 2) << endl; // true
cout << pb.DrawRectangle(4, 1, 3, 2) << endl; // false
cout << pb.QueryArea() << endl; // 49
cout << pb.EraseArea(1, 3, 4, 6) << endl; // 2
cout << pb.EraseArea(1, 3, 4, 6) << endl; // 0
cout << pb.QueryArea() << endl; // 5
cout << pb.DrawRectangle(2, 8, 93, 1) << endl; // false
cout << pb.DrawRectangle(2, 8, 92, 1) << endl; // true
```

# 线路配置

## 功能

1. 有多条线路，线路由端口组成，表示为 [1,2], [5,6]，前述表示端口 1,2 之间为线路，端口 5,6 之间为线路，端口不重复使用，也就是说，这时如果有 [2,3] 由于 2 已经和端口 1 配对，所以不可以有 2,3 端口的线路
2. 初始情况下有现有线路用 configs 表示，现在有配置端口的命令由 batchReqs 组成，batchReqs 描述如下
   1. ['a', 7, 8] 表示增加端口 7,8 之间的线路
   2. ['d', 1, 2] 表示删除端口 1,2 之间的线路
3. 现在给出批处理的 batchReqs 命令，由于 batchReqs 中存在增加和删除重复的指令，存在新增或删除不合法的指令，现在需要用程序去精简指令，得到最终操作的指令
4. 最终的指令要求，删除操作的放在一起，新增操作的放在一起，且删除操作都位于新增操作之前。此外删除和新增中命令的顺序，按照第一个端口大小进行排序

## 关键点

1. tuple 的使用，可以用 get<n>(var) 来获取 tuple 类型数据变量 var 的第 n 个值
2. pair 的使用，可以用 .first 和 .second 来获取变量的第一个和第二个值
3. sort 可以用来进行排序

## 代码示例

```c++
#pragma once
#include <vector>
#include <utility>
#include <tuple>
#include <algorithm>

using namespace std;

class LineConfig {
public:
	vector<tuple<char, int, int>> GetBatchCmds(const vector<pair<int, int>>& configs, const vector<tuple<char, int, int>>& batchReqs) {
		// 把 configs 的内容复制到成员变量 m_configs 中
		for (auto& config : configs) {
			m_configs.push_back(config);
		}
		for (auto& batchReq : batchReqs) {
			Operation(m_configs, batchReq);
		}
		// 对 addVec 排序
		sort(addVec.begin(), addVec.end(), [](const tuple<char, int, int>& lhs, const tuple<char, int, int>& rhs) {
			return get<1>(lhs) < get<1>(rhs);
			});
		// 对 delVec 排序
		sort(delVec.begin(), delVec.end(), [](const tuple<char, int, int>& lhs, const tuple<char, int, int>& rhs) {
			return get<1>(lhs) < get<1>(rhs);
			});
		// 拼接为最简命令
		delVec.insert(delVec.end(), addVec.begin(), addVec.end());
		return delVec;
	}
	void Operation(vector<pair<int, int>>& configs, const tuple<char, int, int>& batchReq) {
		if (get<0>(batchReq) == 'a') {
			for (auto& config : configs) {
				if (get<1>(batchReq) == config.first || // 存在以下任何一种情况则冲突
					get<2>(batchReq) == config.first ||
					get<1>(batchReq) == config.second ||
					get<2>(batchReq) == config.second) {
					return;
				}
			}
			addVec.push_back(batchReq);
		}
		else if (get<0>(batchReq) == 'd') {
			for (auto it = configs.begin(); it != configs.end();) {
				if (get<1>(batchReq) == (*it).first && // 匹配到要删除的专线
					get<2>(batchReq) == (*it).second) {
					configs.erase(it);
					delVec.push_back(batchReq);
					return;
				}
				else {
					++it;
				}
			}
			for (auto it = addVec.begin(); it != addVec.end();) { // 若 addVec 和 delVec 均存在
				if (get<1>(batchReq) == get<1>(*it) &&
					get<2>(batchReq) == get<2>(*it)) {
					it = addVec.erase(it);
					return;
				}
				else {
					++it;
				}
			}
		}
	}

private:
	vector<tuple<char, int, int>> addVec;
	vector<tuple<char, int, int>> delVec;
	vector<pair<int, int>> m_configs;
};
```

