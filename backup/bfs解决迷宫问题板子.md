```
#include<bits/stdc++.h>
using namespace std;
int n,m;
int startx,starty;
int dx[4]={0,1,0,-1};
int dy[4]={1,0,-1,0};
int a[100][100];
int v[100][100];
int flag=0;
struct point
{
	int x;
	int y;
	int step;
};
queue<point> r;
int main()
{
	cin>>n>>m;
	cin>>startx>>starty;
	for(int i=1;i<=n;i++){
		for(int j=1;j<=m;j++){
			cin>>a[i][j];
		} 
	}
	point start;
	start.x=startx;
	start.y=starty;
	start.step=0;
	r.push(start);
	v[startx][starty]=1;
	while(!r.empty()){
		int x=r.front().x;
		int y=r.front().y;
		int step=r.front().step;
		if(x==n&&y==m){
			flag=1;
			cout<<r.front().step;
			break;
		}
		for(int i=0;i<4;i++){
			int tx=x+dx[i];
			int ty=y+dy[i];
			if(v[tx][ty]==0&&a[tx][ty]==1){
				point temp;
				temp.x=tx;
				temp.y=ty;
				temp.step=r.front().step+1;
				r.push(temp);
				v[tx][ty]=1;
			}
		}
		r.pop();
	}
	if(flag==0){
		cout<<"-1";
	}
	return 0;
}
```