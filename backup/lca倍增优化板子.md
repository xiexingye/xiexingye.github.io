```
#include<bits/stdc++.h>
using namespace std;
#define N 500005
#define endl "\n"
int head[N], to[N<<1], nex[N<<1], cnt=1;
int fa[N][21], dep[N];
void add(int x, int y){
	to[cnt] = y;
	nex[cnt] = head[x];
	head[x] = cnt++;
}
void dfs(int rt, int f){
	dep[rt] = dep[f] + 1;
	fa[rt][0] = f;
	for(int i = 1; (1 << i) <= dep[rt]; i++){ 
		fa[rt][i] = fa[fa[rt][i - 1]][i - 1];
	}
	for(int i = head[rt]; i; i = nex[i]){
		if(to[i] == f){
			continue;
		}
		dfs(to[i], rt);
	}
}
int LCA(int x, int y){
	if(dep[x] < dep[y]){
		swap(x, y);
	}
	for(int i = 20; i >= 0; i--){  
		if(dep[fa[x][i]] >= dep[y]){
			x = fa[x][i];
		}
	} 
	if(x == y){  
		return x;
	}
	for(int i = 20; i >= 0; i--){   
		if(fa[x][i] != fa[y][i]){
			x = fa[x][i];
			y = fa[y][i];
		}
	}
	return fa[x][0];  
}
int main(){
	ios::sync_with_stdio(false);
	int n, m, s;
	while(cin >> n >> m >> s){
		memset(head, 0, sizeof(head));
		cnt = 1;
		int x, y;
		for(int i = 0; i < n - 1; i++){
			cin >> x >> y;
			add(x, y);
			add(y, x);
		}
		dfs(s, 0);
		for(int i = 0; i < m; i++){
			cin >> x >> y;
			cout << LCA(x, y) << endl;
		}
	}
	return 0;
}
```