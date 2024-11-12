```
#include<bits/stdc++.h>
using namespace std;
const int maxn=1000010;
const int INF=2e9;
int head[maxn],nxt[maxn],to[maxn],val[maxn],tt;
int n,m,s;
int dis[maxn];
bool vis[maxn];
priority_queue<pair<int,int> >q;//前面存距离，后面存编号
//优先队列维护的规则是以前前面的数字为关键字，然后再按照后面的数字为第二关键字 
void add(int u,int v,int w)
{
	to[++tt]=v;
	nxt[tt]=head[u];
	val[tt]=w;
	head[u]=tt;
}
void dijkstra()
{
	for(int i=0;i<=maxn;i++) dis[i]=INF;
	dis[s]=0;
	q.push(make_pair(0,s));
	while(!q.empty())
	{
		int u=q.top().second;
		q.pop();
		if(vis[u]) continue;
		vis[u]=1;
		for(int i=head[u];i;i=nxt[i])
		{
			int v=to[i];
			if(dis[v]>dis[u]+val[i])
			{
				dis[v]=dis[u]+val[i];
				q.push(make_pair(-dis[v],v));
			}
		}
	}
}
int main()
{
	cin>>n>>m>>s;
	for(int i=1;i<=m;i++)
	{
		int x,y,w;
		cin>>x>>y>>w;
		add(x,y,w);
	}
	dijkstra();
	for(int i=1;i<=n;i++) cout<<dis[i];
	return 0;
}
```