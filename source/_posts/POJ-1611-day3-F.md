---
uuid: b0c791d5-c242-f811-22c1-e09d74981bc8
title: POJ - 1611-day3-F
date: 2019-09-01 22:06:06
categories:
- 算法题
tags:
- 并查集
---
<meta http-equiv="Content-Security-Policy" content="upgrade-insecure-requests">

# 问题来源

[POJ - 1611](http://poj.org/problem?id=1611)
# 问题描述
Severe acute respiratory syndrome (SARS), an atypical pneumonia of unknown aetiology, was recognized as a global threat in mid-March 2003. To minimize transmission to others, the best strategy is to separate the suspects from others.
In the Not-Spreading-Your-Sickness University (NSYSU), there are many student groups. Students in the same group intercommunicate with each other frequently, and a student may join several groups. To prevent the possible transmissions of SARS, the NSYSU collects the member lists of all student groups, and makes the following rule in their standard operation procedure (SOP). 
Once a member in a group is a suspect, all members in the group are suspects. 
However, they find that it is not easy to identify all the suspects when a student is recognized as a suspect. Your job is to write a program which finds all the suspects.
# 输入
The input file contains several cases. Each test case begins with two integers n and m in a line, where n is the number of students, and m is the number of groups. You may assume that 0 < n <= 30000 and 0 <= m <= 500. Every student is numbered by a unique integer between 0 and n−1, and initially student 0 is recognized as a suspect in all the cases. This line is followed by m member lists of the groups, one line per group. Each line begins with an integer k by itself representing the number of members in the group. Following the number of members, there are k integers representing the students in this group. All the integers in a line are separated by at least one space. 
A case with n = 0 and m = 0 indicates the end of the input, and need not be processed.
# 输出
For each case, output the number of suspects in one line.
# 例子
input

```
100 4
2 1 2
5 10 13 11 12 14
2 0 1
2 99 2
200 2
1 5
5 1 2 3 4 5
1 0
0 0
```

output

```
4
1
1
```

# 解题思路
并查集，找与0有关的所有人，用bb数组来存放头目有关的人数，最后找0的头目，就能在数组里找到所有与他有关的人。
# AC的代码

```c
#include<string>
#include<stdio.h>
#include <iostream>
using namespace std;
int gro,s,aa[30005],bb[30005],cc[30005];    //
int find(int x)                 //寻找根节点的函数
{
	if (aa[x] == x)
		return x;
	else
	{
		aa[x] = find(aa[x]);
	}
}
void join(int x, int y)
{
	int x1 = find(x);
	int y1 = find(y);
	if (x1 != y1)            //根节点不同连接
	{
		aa[x1] = y1;
		bb[y1] = bb[x1] + bb[y1];
	}
}
int main()
{
	
	while (cin >> s >> gro)
	{
		if (s == 0)
			break;
		for (int r = 0; r < 30005; r++)
		{
			aa[r] = r;
			bb[r] = 1;
		}
		for (int f = 0; f < gro; f++)
		{
			int p;
			scanf("%d",&p);
			for (int q = 0; q < p; q++)
			{
				scanf("%d",&cc[q]);
			}
			for (int x = 0; x < p - 1; x++)
			{
				join(cc[x], cc[x + 1]);
			}

		}


		int k = find(0);

		cout << bb[k] << endl;
	}
}

```

