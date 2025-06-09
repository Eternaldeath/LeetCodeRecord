# 简单的画图程序

# 功能

1. 画布大小 100 * 100，由方格组成，以 [0,0] 为起点
2. 绘图函数：输入起始行列和宽高，绘制矩形，要求矩形不能重叠或超出画布范围。不满足要求输出 false，否则输出 true
3. 删除矩形函数：输入起始行列和宽高，在这个范围内的矩形被删除。输出被删除矩形的个数
4. 查询函数：输入无，输出当前画布中所有矩形组成的组成的面积

# 关键点

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

# 代码示例

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

