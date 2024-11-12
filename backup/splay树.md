# splay树
平衡树的一种，自身可以通过旋转来降低树的深度


# 普通平衡树模板
````
#include<iostream>
#include<cstdio>
#include<queue>
#include<cstring>
#include<algorithm>
#include<climits>
typedef long long LL;
using namespace std;
int RD(){
    int out = 0,flag = 1;char c = getchar();
    while(c < '0' || c >'9'){if(c == '-')flag = -1;c = getchar();}
    while(c >= '0' && c <= '9'){out = out * 10 + c - '0';c = getchar();}
    return flag * out;
    }
//第一次打treap，不压行写注释XD
const int maxn = 1000019,INF = 1e9;
//平衡树，利用BST性质查询和修改，利用随机和堆优先级来保持平衡，把树的深度控制在log N，保证了操作效率
//基本平衡树有以下几个比较重要的函数：新建，插入，删除，旋转
//节点的基本属性有val(值)，dat(随机出来的优先级)
//通过增加属性，结合BST的性质可以达到一些效果，如size(子树大小，查询排名)，cnt(每个节点包含的副本数)等
int na;
int ch[maxn][2];//[i][0]代表i左儿子，[i][1]代表i右儿子
int val[maxn],dat[maxn];
int size[maxn],cnt[maxn];
int tot,root;
int New(int v){//新增节点，
	val[++tot] = v;//节点赋值
	dat[tot] = rand();//随机优先级
	size[tot] = 1;//目前是新建叶子节点，所以子树大小为1
	cnt[tot] = 1;//新建节点同理副本数为1
	return tot;
	}
void pushup(int id){//和线段树的pushup更新一样
	size[id] = size[ch[id][0]] + size[ch[id][1]] + cnt[id];//本节点子树大小 = 左儿子子树大小 + 右儿子子树大小 + 本节点副本数
	}
void build(){
	root = New(-INF),ch[root][1] = New(INF);//先加入正无穷和负无穷，便于之后操作(貌似不加也行)
	pushup(root);//因为INF > -INF,所以是右子树，
	}
void Rotate(int &id,int d){//id是引用传递，d(irection)为旋转方向，0为左旋，1为右旋
	int temp = ch[id][d ^ 1];//旋转理解：找个动图看一看就好(或参见其他OIer的blog)
	ch[id][d ^ 1] = ch[temp][d];//这里讲一个记忆技巧，这些数据都是被记录后马上修改
	ch[temp][d] = id;//所以像“Z”一样
	id = temp;//比如这个id，在上一行才被记录过，ch[temp][d]、ch[id][d ^ 1]也是一样的
	pushup(ch[id][d]),pushup(id);//旋转以后size会改变，看图就会发现只更新自己和转上来的点，pushup一下,注意先子节点再父节点
	}//旋转实质是({在满足BST的性质的基础上比较优先级}通过交换本节点和其某个叶子节点)把链叉开成二叉形状(从而控制深度)，可以看图理解一下
void insert(int &id,int v){//id依然是引用，在新建节点时可以体现
	if(!id){
		id = New(v);//若节点为空，则新建一个节点
		return ;
		}
	if(v == val[id])cnt[id]++;//若节点已存在，则副本数++;
	else{//要满足BST性质，小于插到左边，大于插到右边
		int d = v < val[id] ? 0 : 1;//这个d是方向的意思，按照BST的性质，小于本节点则向左，大于向右
		insert(ch[id][d],v);//递归实现
		if(dat[id] < dat[ch[id][d]])Rotate(id,d ^ 1);//(参考一下图)与左节点交换右旋，与右节点交换左旋
		}
	pushup(id);//现在更新一下本节点的信息
	}
void Remove(int &id,int v){//最难de部分了
	if(!id)return ;//到这了发现查不到这个节点，该点不存在，直接返回
	if(v == val[id]){//检索到了这个值
		if(cnt[id] > 1){cnt[id]--,pushup(id);return ;}//若副本不止一个，减去一个就好
		if(ch[id][0] || ch[id][1]){//发现只有一个值，且有儿子节点,我们只能把值旋转到底部删除
			if(!ch[id][1] || dat[ch[id][0]] > dat[ch[id][1]]){//当前点被移走之后，会有一个新的点补上来(左儿子或右儿子)，按照优先级，优先级大的补上来
				Rotate(id,1),Remove(ch[id][1],v);//我们会发现，右旋是与左儿子交换，当前点变成右节点；左旋则是与右儿子交换，当前点变为左节点
				}
			else Rotate(id,0),Remove(ch[id][0],v);
			pushup(id);
			}
		else id = 0;//发现本节点是叶子节点，直接删除
		return ;//这个return对应的是检索到值de所有情况
		}
	v < val[id] ? Remove(ch[id][0],v) : Remove(ch[id][1],v);//继续BST性质
	pushup(id);
	}
int get_rank(int id,int v){
	if(!id)return 1;//若查询值不存在，返回；因为最后要减一排除哨兵节点，想要结果为-1这里就返回0
	if(v == val[id])return size[ch[id][0]] + 1;//查询到该值，由BST性质可知：该点左边值都比该点的值(查询值)小，故rank为左儿子大小 + 1
	else if(v < val[id])return get_rank(ch[id][0],v);//发现需查询的点在该点左边，往左边递归查询
	else return size[ch[id][0]] + cnt[id] + get_rank(ch[id][1],v);//若查询值大于该点值。说明询问点在当前点的右侧，且此点的值都小于查询值，所以要加上cnt[id]
	}
int get_val(int id,int rank){
	if(!id)return INF;//一直向右找找不到，说明是正无穷
	if(rank <= size[ch[id][0]])return get_val(ch[id][0],rank);//左边排名已经大于rank了，说明rank对应的值在左儿子那里
		else if(rank <= size[ch[id][0]] + cnt[id])return val[id];//上一步排除了在左区间的情况，若是rank在左与中(目前节点)中，则直接返回目前节点(中区间)的值
	else return get_val(ch[id][1],rank - size[ch[id][0]] - cnt[id]);//剩下只能在右区间找了，rank减去左区间大小和中区间，继续递归
	}
int get_pre(int v){
	int id = root,pre;//递归不好返回，以循环求解
	while(id){//查到节点不存在为止
		if(val[id] < v)pre = val[id],id = ch[id][1];//满足当前节点比目标小，往当前节点的右侧寻找最优值
		else id = ch[id][0];//无论是比目标节点大还是等于目标节点，都不满足前驱条件，应往更小处靠近
		}
	return pre;
	}
int get_next(int v){
	int id = root,next;
	while(id){
		if(val[id] > v)next = val[id],id = ch[id][0];//同理，满足条件向左寻找更小解(也就是最优解)
		else id = ch[id][1];//与上方同理
		}
	return next;
	}
int main(){
	build();//不要忘记初始化[运行build()会连同root一并初始化，所以很重要]
	na = RD();
	for(int i = 1;i <= na;i++){
		int cmd = RD(),x = RD();
		if(cmd == 1)insert(root,x);//函数都写好了，注意：需要递归的函数都从根开始，不需要递归的函数直接查询
		else if(cmd == 2)Remove(root,x);
		else if(cmd == 3)printf("%d\n",get_rank(root,x) - 1);//注意：因为初始化时插入了INF和-INF,所以查询排名时要减1(-INF不是第一小，是“第零小”)
		else if(cmd == 4)printf("%d\n",get_val(root,x + 1));//同理，用排名查询值得时候要查x + 1名，因为第一名(其实不是)是-INF
		else if(cmd == 5)printf("%d\n",get_pre(x));
		else if(cmd == 6)printf("%d\n",get_next(x));
		}
	return 0;
	}
````

# splay树模板
````
#include<bits/stdc++.h>
#include<unordered_map>
//#pragma GCC optimize(3)
#define endl "\n"
using namespace std;
#define debug(x) cout<<"DEBUG "<<#x<<" : "<<x<<endl
#define NO cout<<"NO"<<endl
#define YES cout<<"YES"<<endl
#define IOS std::ios::sync_with_stdio(false);std::cin.tie(0);
#define INF 0x3f3f3f3f
#define mst(x) memset(x,0,sizeof(x))
typedef long long ll;
#define mod 998244353
//#define int long long


const int N = 100005;
int rt, tot, fa[N], ch[N][2], val[N], cnt[N], siz[N];

struct Splay {

    void push_up(int x) { siz[x] = siz[ch[x][0]] + siz[ch[x][1]] + cnt[x]; }

    bool get(int x) { return x == ch[fa[x]][1]; }

    void clear(int x) {
        ch[x][0] = ch[x][1] = fa[x] = val[x] = siz[x] = cnt[x] = 0;
    }

    void rotate(int x) {
        int y = fa[x], z = fa[y], chk = get(x);
        ch[y][chk] = ch[x][chk ^ 1];
        if (ch[x][chk ^ 1]) fa[ch[x][chk ^ 1]] = y;
        ch[x][chk ^ 1] = y;
        fa[y] = x;
        fa[x] = z;
        if (z) ch[z][y == ch[z][1]] = x;
        push_up(y);
    }

    void splay(int x,int goal) {
        for (int f = fa[x]; (f = fa[x]) != goal; rotate(x))
            if (fa[f] != goal) rotate(get(x) == get(f) ? f : x);
        if(goal == 0)rt = x;
    }

    void splay(int x) {
        splay(x, 0);
    }

    void ins(int k) {
        if (!rt) {
            val[++tot] = k;
            cnt[tot]++;
            rt = tot;
            push_up(rt);
            return;
        }
        int cur = rt, f = 0;
        while (1) {
            if (val[cur] == k) {
                cnt[cur]++;
                push_up(cur);
                push_up(f);
                splay(cur);
                break;
            }
            f = cur;
            cur = ch[cur][val[cur] < k];
            if (!cur) {
                val[++tot] = k;
                cnt[tot]++;
                fa[tot] = f;
                ch[f][val[f] < k] = tot;
                push_up(tot);
                push_up(f);
                splay(tot);
                break;
            }
        }
    }

    int rk(int k) {
        int res = 0, cur = rt;
        while (1) {
            if (k < val[cur]) {
                cur = ch[cur][0];
            }
            else {
                res += siz[ch[cur][0]];
                if (k == val[cur]) {
                    splay(cur);
                    return res + 1;
                }
                res += cnt[cur];
                cur = ch[cur][1];
            }
        }
    }

    int kth(int k) {
        int cur = rt;
        while (1) {
            if (ch[cur][0] && k <= siz[ch[cur][0]]) {
                cur = ch[cur][0];
            }
            else {
                k -= cnt[cur] + siz[ch[cur][0]];
                if (k <= 0) {
                    splay(cur);
                    return val[cur];
                }
                cur = ch[cur][1];
            }
        }
    }

    int pre() {
        int cur = ch[rt][0];
        if (!cur) return cur;
        while (ch[cur][1]) cur = ch[cur][1];
        splay(cur);
        return cur;
    }

    int nxt() {
        int cur = ch[rt][1];
        if (!cur) return cur;
        while (ch[cur][0]) cur = ch[cur][0];
        splay(cur);
        return cur;
    }

    void del(int k) {
        rk(k);
        if (cnt[rt] > 1) {
            cnt[rt]--;
            push_up(rt);
            return;
        }
        if (!ch[rt][0] && !ch[rt][1]) {
            clear(rt);
            rt = 0;
            return;
        }
        if (!ch[rt][0]) {
            int cur = rt;
            rt = ch[rt][1];
            fa[rt] = 0;
            clear(cur);
            return;
        }
        if (!ch[rt][1]) {
            int cur = rt;
            rt = ch[rt][0];
            fa[rt] = 0;
            clear(cur);
            return;
        }
        int cur = rt;
        int x = pre();
        fa[ch[cur][1]] = x;
        ch[x][1] = ch[cur][1];
        clear(cur);
        push_up(rt);
    }
} tree;

int n;
void slove() {
    cin >> n;
    
    for (int i = 1; i <= n; i++) {
        int opt, x; cin >> opt >> x;
        if (opt == 1)
            tree.ins(x);
        else if (opt == 2)
            tree.del(x);
        else if (opt == 3)
            printf("%d\n", tree.rk(x));
        else if (opt == 4)
            printf("%d\n", tree.kth(x));
        else if (opt == 5)
            tree.ins(x), printf("%d\n", val[tree.pre()]), tree.del(x);
        else
            tree.ins(x), printf("%d\n", val[tree.nxt()]), tree.del(x);
    }
}
signed main() {
    IOS;
    slove();
}
````


# 区间翻转模板
````
struct Splay {
    int rt, tot, fa[N], ch[N][2], val[N], cnt[N], siz[N];
    int tag[N];
    void push_up(int x) {
        siz[x] = siz[ch[x][0]] + siz[ch[x][1]] + cnt[x];
    }
    void push_down(int x) {
        if (x && tag[x]) {
            if (ch[x][0])tag[ch[x][0]] ^= 1;
            if (ch[x][1])tag[ch[x][1]] ^= 1;
            swap(ch[x][0], ch[x][1]);
            tag[x] = 0;
        }
    }
    bool get(int x) { return x == ch[fa[x]][1]; }
    void clear(int x) {
        ch[x][0] = ch[x][1] = fa[x] = val[x] = siz[x] = cnt[x] = 0;
    }

    void rotate(int x) {
        int y = fa[x], z = fa[y], chk = get(x);
        push_down(x);
        push_down(y);
        ch[y][chk] = ch[x][chk ^ 1];
        if (ch[x][chk ^ 1]) fa[ch[x][chk ^ 1]] = y;
        ch[x][chk ^ 1] = y;
        fa[y] = x;
        fa[x] = z;
        if (z) ch[z][y == ch[z][1]] = x;
        push_up(y);
    }
    void splay(int x, int goal) {
        for (int f = fa[x]; (f = fa[x]) != goal; rotate(x))
            if (fa[f] != goal) rotate(get(x) == get(f) ? f : x);
        if (goal == 0)rt = x;
    }
    void splay(int x) {
        splay(x, 0);
    }
    int kth(int k) {

        int cur = rt;
        while (1) {
            push_down(cur);
            if (ch[cur][0] && k <= siz[ch[cur][0]]) {
                cur = ch[cur][0];
            }
            else {
                k -= cnt[cur] + siz[ch[cur][0]];
                if (k <= 0) {
                    splay(cur);
                    return cur;
                }
                cur = ch[cur][1];
            }
        }
    }
    int find(int x) {
        return kth(x + 1);
    }
    int build(int L, int R, int father) {
        if (L > R) { return 0; }
        int x = ++tot;
        int mid = (L + R) / 2;
        fa[x] = father;
        cnt[x] = 1;
        val[x] = mid;
        ch[x][0] = build(L, mid - 1, x);
        ch[x][1] = build(mid + 1, R, x);
        push_up(x);
        return x;
    }
    void rev(int L, int R) {
        int fl = find(L - 1);
        int fr = find(R + 1);
        splay(fl, 0);
        splay(fr, fl);
        int pos = ch[rt][1]; pos = ch[pos][0];
        tag[pos] ^= 1;
    }
    void dfs(int x) {
        push_down(x);
        if (ch[x][0])dfs(ch[x][0]);
        if (val[x] != 0 && val[x] != (n + 1))cout << val[x] << " ";
        if (ch[x][1])dfs(ch[x][1]);
    }
} tree;

void slove() {
    cin >> n >> m;
    tree.build(0, n + 1, 0);
    tree.rt = 1;//注意不要忘了这个地方
    while (m--) {
        int L, R; cin >> L >> R;
        tree.rev(L, R);
    }
    tree.dfs(tree.rt);
}
````

# splay树实现线段树
````
struct Splay {

    int rt, tot, fa[N], ch[N][2], id[N], cnt[N], siz[N];
    ll sum[N], val[N], tag[N];

    void push_up(int x) {
        siz[x] = siz[ch[x][0]] + siz[ch[x][1]] + cnt[x];
        sum[x] = sum[ch[x][0]] + sum[ch[x][1]] + val[x];
    }
    void push(int x, ll k) {
        if (x) {
            tag[x] += k;
            sum[x] += (ll)siz[x] * k;
            val[x] += k;
        }
    }
    void push_down(int x) {
        if (x && tag[x]) {
            push(ch[x][0], tag[x]);
            push(ch[x][1], tag[x]);
            tag[x] = 0;
        }
    }

    bool get(int x) { return x == ch[fa[x]][1]; }

    void clear(int x) {
        ch[x][0] = ch[x][1] = fa[x] = id[x] = siz[x] = cnt[x] = 0;
    }

    void rotate(int x) {
        int y = fa[x], z = fa[y], chk = get(x);
        push_down(x);
        push_down(y);
        ch[y][chk] = ch[x][chk ^ 1];
        if (ch[x][chk ^ 1]) fa[ch[x][chk ^ 1]] = y;
        ch[x][chk ^ 1] = y;
        fa[y] = x;
        fa[x] = z;
        if (z) ch[z][y == ch[z][1]] = x;
        push_up(y);
    }
    void splay(int x, int goal) {
        for (int f = fa[x]; (f = fa[x]) != goal; rotate(x))
            if (fa[f] != goal) rotate(get(x) == get(f) ? f : x);
        if (goal == 0)rt = x;
    }
    void splay(int x) {
        splay(x, 0);
    }
    int kth(int k) {
        int cur = rt;
        while (1) {
            push_down(cur);
            if (ch[cur][0] && k <= siz[ch[cur][0]]) {
                cur = ch[cur][0];
            }
            else {
                k -= cnt[cur] + siz[ch[cur][0]];
                if (k <= 0) {
                    splay(cur);
                    return cur;
                }
                cur = ch[cur][1];
            }
        }
    }
    int find(int x) {
        return kth(x + 1);
    }
    int build(int L, int R, int father) {
        if (L > R) { return 0; }
        int x = ++tot;
        int mid = (L + R) / 2;
        fa[x] = father;
        cnt[x] = 1;
        id[x] = mid;
        val[x] = a[mid];
        ch[x][0] = build(L, mid - 1, x);
        ch[x][1] = build(mid + 1, R, x);
        push_up(x);
        return x;
    }

    void add(int L, int R, ll k) {
        int fl = find(L - 1), fr = find(R + 1);
        splay(fl, 0);
        splay(fr, fl);
        int x = ch[rt][1]; x = ch[x][0];
        push(x, k);
    }

    ll que(int L, int R) {
        int fl = find(L - 1), fr = find(R + 1);
        splay(fl, 0);
        splay(fr, fl);
        int x = ch[rt][1]; x = ch[x][0];
        return sum[x];
    }
} tree;

void slove() {
    cin >> n >> m;
    for (int i = 1; i <= n; i++)cin >> a[i];
    tree.build(0, n + 1, 0); tree.rt = 1;
    while (m--) {
        int op, x, y, k;
        cin >> op;
        if (op == 1) {
            cin >> x >> y >> k;
            tree.add(x, y, k);
        }
        else {
            cin >> x >> y;
            cout << tree.que(x, y) << endl;
        }
    }
}
````