---
title: 动态规划 dnf自动寻路过图
date: 2016-06-10 18:53:00
updated: 2016-06-10 18:55:38
tags: 
- javascript
- class
- typescript
categories: 
- js

---
```cpp
#include<iostream>
#include<string>
using namespace std;
 


<!--more-->


int map[100][100] = { 0 };
struct node {
	int x, y;
	int step = -1;
	string find;
};
node p;
node beginw;
node lastw;
void tomap(bool goo, char fx = 'rtn') {//goo = 1 前进  0 后退  fx为方向（后退可不写）
	if (goo == 1) {//前进
		if (fx == 'L') {
			p.y -= 1;
			CoordinateMov(0);
		}
		else if (fx == 'R') {
			p.y += 1;
			CoordinateMov(1);
		}
		else if (fx == 'U') {
			p.x -= 1;
			CoordinateMov(2);
		}
		else if (fx == 'D') {
			p.x += 1;
			CoordinateMov(3);
		}
		p.find += fx;
		Sleep(2000);
	}
	if (goo == 0) {//后退
		if (p.find[p.step] == 'R' && map[p.x][p.y - 1] != -1) {
			horn(55, L"等待左退测试");
			Sleep(2000);
			tomap(1, 'L');
		}
		else if (p.find[p.step] == 'L'  && map[p.x][p.y - 1] != -1) {
			horn(55, L"等待右退测试");
			Sleep(2000);
			tomap(1, 'R');
		}
		else if (p.find[p.step] == 'D'  && map[p.x][p.y - 1] != -1) {
			horn(55, L"等待上退测试");
			Sleep(2000);
			tomap(1, 'U');
		}
		else if (p.find[p.step] == 'U'  && map[p.x][p.y - 1] != -1) {
			horn(55, L"等待下退测试");
			Sleep(2000);
			tomap(1, 'D');
		}
	}
	p.step++;
	return;
}
int find() {
	if (p.x == lastw.x&&p.y == lastw.y) {
		fill(*map, *map + 100, 0);
		return p.step + 1;
	}
	while (!OpenDoor()) {
		Sleep(1500);
	}
	int n = 0;
	char A = 0;
	int l, r, u, d;
	l = RetnDirection().left;
	r = RetnDirection().right;
	u = RetnDirection().up;
	d = RetnDirection().down;
	horn(55, L"开启新一轮搜索");
	if (l == 1 && map[p.x][p.y - 1] == 0) {
		if (p.x == lastw.x&&p.y - 1 == lastw.y) A = 'L';
		map[p.x][p.y - 1] = 1;
		n++;
		horn(55, L"左侧标1");
	}
	if (r == 1 && map[p.x][p.y + 1] == 0) {
		if (p.x == lastw.x&&p.y + 1 == lastw.y) A = 'R';
		map[p.x][p.y + 1] = 1;
		n++;
		horn(55, L"右侧标1");
	}
	if (u == 1 && map[p.x - 1][p.y] == 0) {
		if (p.x - 1 == lastw.x&&p.y == lastw.y) A = 'U';
		map[p.x - 1][p.y] = 1;
		n++;
		horn(55, L"上侧标1");
	}
	if (d == 1 && map[p.x + 1][p.y] == 0) {
		if (p.x + 1 == lastw.x&&p.y == lastw.y) A = 'D';
		map[p.x + 1][p.y] = 1;
		n++;
		horn(55, L"下侧标1");
	}
	if (A != 0) {
		tomap(1, A);
		fill(*map, *map + 100, 0);
		return p.step + 1;
	}
	char cmp;
	if (p.find[p.step] == 'L') cmp = 'R';
	else if (p.find[p.step] == 'R') cmp = 'L';
	else if (p.find[p.step] == 'U') cmp = 'D';
	else if (p.find[p.step] == 'D') cmp = 'U';
	if (l == 1 && map[p.x][p.y - 1] == 1 && cmp != 'L') {
		horn(55, L"尝试左拐");
		if (n <= 1) map[p.x][p.y] = -1;
		tomap(1, 'L');
		return find();
	}
	else if (r == 1 && map[p.x][p.y + 1] == 1 && cmp != 'R') {
		horn(55, L"尝试右拐");
		if (n <= 1) map[p.x][p.y] = -1;
		tomap(1, 'R');
		return find();
	}
	else if (u == 1 && map[p.x - 1][p.y] == 1 && cmp != 'U') {
		horn(55, L"尝试上拐");
		if (n <= 1) map[p.x][p.y] = -1;
		tomap(1, 'U');
		return find();
	}
	else if (d == 1 && map[p.x + 1][p.y] == 1 && cmp != 'D') {
		horn(55, L"尝试下拐");
		if (n <= 1) map[p.x][p.y] = -1;
		tomap(1, 'D');
		return find();
	}
	else {
		map[p.x][p.y] = -1;
		horn(55, L"倒退ing继续搜索");
		tomap(0);
	}
}
int AutoMov() {//horn(55, L"temp");
			   /*cin >> beginw.x >> beginw.y
			   >> lastw.x >> lastw.y;*/
	beginw.x = CurrentRoom().x;
	beginw.y = CurrentRoom().y;
	lastw.x = BossRoom().x;
	lastw.y = BossRoom().y;
 
	p.x = beginw.x;
	p.y = beginw.y;
	map[beginw.x][beginw.y] = 1;
	find();//返回步数/结束寻路
	return 0;
}
```