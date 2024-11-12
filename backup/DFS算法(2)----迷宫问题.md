# dfs算法解决迷宫问题
```
#include<iostream>
using namespace std;
int dx[4] = { 0,1,0,-1 };
int dy[4] = { 1,0,-1,0 };
int a[100][100];
int n, m;
int v[100][100];
int min = 1000000;
void dfs(int x, int y, int step) {
	if (x == n && y == m) {
		if (step < min) {
			min = step;
		}
		return;
	}
	for (int i = 0; i < 4; i++) {
		int tx = x + dx[i];
		int ty = y + dy[i];
		if (a[tx][ty] == 1 && v[tx][ty] == 0) {
			v[tx][ty] = 1;
			dfs(tx, ty, step + 1);
			v[tx][ty] = 0;
		}
	}
}
int main()
{
	cin >> n >> m;
	
	for (int i = 0; i < n; i++) {
		for (int j = 0; j < m; j++) {
			cin >> a[i][j];
		}
	}
	v[0][0] = 1;
	dfs(0, 0, 0);
	return 0;
}
```