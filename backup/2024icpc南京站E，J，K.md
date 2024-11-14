# E Left Shifting 3
题目大意：给一个字符串，可以左移至多k次，最大化左移之后 nanjing 子串的数量。
题目思路：两个nanjing 子串不可能相交，因此只要左移之后，字符串的开头不会“截断”一个nanjing子串，就能得到最大的答案nanjing 子串的长度是 7，所以左移 7 次之内肯定有个位置不会截断nanjing 子串。因此模拟min(k,7) 次左移并计算答案即可。
个人思路：讲自己拼接一个一模一样的串，然后对每个下标对n取模，进行遍历。
````
#include <bits/stdc++.h>
using namespace std;
int t,n,k;
string S,T="nanjing";
int op[400011],s[400011];
int main()
{
    cin>>t;
    while(t--){
        cin>>n>>k;
        k=min(k,n-1);
        cin>>S;
        for(int i=0;i<k;++i)S+=S[i];
        for(int i=0;i<n+k;++i){
            op[i]=0;
        }
        for(int i=0;i+T.size()-1<S.size();++i)
        {
            bool flag=1;
            for(int _=0;_<7;++_)if(S[i+_]!=T[_])
                {
                    flag=0;
                    break;
                }
            op[i]=flag;
        }
        s[0]=0;
        for(int i=0;i<n+k;++i){
            s[i+1]=s[i]+op[i];
        }
        int ans=0;
        for(int i=n;i<=n+k;++i)
        {
            ans=max(ans,s[i]-s[i-n]);
        }
        cout<<ans<<"\n";
    }
}
````
# J Social Media
题目大意：一个社交平台有k个用户，其中有n个是你的好友。平台上还有m条评论。每个评论与两个用户ai,bi 相关，可以看到该评论当且仅当你同时和ai 与bi 两位用户是好友。现在可以添加至多两个用户为好友，求出能看到的评论数量的最大值。
题目思路：将评论分为三类：
1.相关的两个用户都是初始存在的好友；
2.相关的用户中恰有一个是初始存在的好友；
3.相关的两个用户都是新添加的好友。
对于第一类评论，在任何情况下都可见，直接统计出这样的评论个数即可。
对于第二类评论，我们可以对于每个非初始好友u统计cu，表示与u和初始好友相关的评论数量。则可见的第二类评论的数量是新添加用户的c值之和。
若不存在可见的第三类评论，则应当按照c值从大到小的顺序贪心选择新增的好友。若存在可见的第三类评论，枚举该评论后，与其相关的两个用户必须都是新增的好友，也即可以完全确定新增的好友是哪两位，于是将其c值相加后更新答案即可。

````
#include<bits/stdc++.h>
using namespace std;
int T,n,m,k,x[200010],y[200010],F[200010];
bool mark[200010];
map<int,int> f[200010];
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cin>>T;
    while(T--)
    {
        cin>>n>>m>>k;
        for(int i=1;i<=k;i++)
        {
            f[i].clear();
            mark[i]=F[i]=0;
        }
        for(int i=1,x;i<=n;i++)
        {
            cin>>x;
            mark[x]=1;
        }
        int ans=0;
        for(int i=1;i<=m;i++)
        {
            cin>>x[i]>>y[i];
            f[x[i]][y[i]]++;
            f[y[i]][x[i]]++;
            if(mark[x[i]]&&mark[y[i]])
            {
                ans++;
            }
            else if(x[i]==y[i])
            {
                F[x[i]]++;
            }
            else if(mark[x[i]]||mark[y[i]])
            {
                F[x[i]]++,F[y[i]]++;
            }
        }
        int Ans=0;
        for(int i=1,maxx=0;i<=k;i++)
        {
            if(mark[i])
            {
                continue;
            }
            Ans=max(Ans,maxx+F[i]);
            maxx=max(maxx,F[i]);
        }
        for(int i=1;i<=m;i++)
        {
            if(x[i]!=y[i]&&!mark[x[i]]&&!mark[y[i]])
            {
                Ans=max(Ans,F[x[i]]+F[y[i]]+f[x[i]][y[i]]);
            }
        }
        cout<<ans+Ans<<'\n';
    }
    return 0;
}
````

# K Strips
题目大意：有w个格子排成一行，从左到右编号从1到w。这些格子中，有n个是红色的，m个是黑色的，剩下的格子是白色的。接下来需要用一些不相交的纸条覆盖所有红色格子。每张纸条必须覆盖k个连续的格子。每个红色格子都要被覆盖，每个黑色格子都不能被覆盖，且纸条数要尽量小。构造一个方案。
题目思路：黑色格子把所有格子分成了几段，我们可以对每段分别求解。分段后，问题变为：一些格子排成一行，其中一些格子是红色的。用最少纸条覆盖所有红色格子，且纸条不超过边界。先假设没有边界的限制，只最小化纸条数量。这是一个非常经典的贪心问题：从左到右枚举红色格子，若当前格子未被覆盖，则添加一张以该格子为左端点的纸条。加入边界后可能出现的问题只有：最后一张纸条的右端点超过了右边界。那我们尝试调整一下，把最后一张纸条的右端点和当前段的右端点对其。调整之后，该纸条可能会和上一张纸条发生重叠，那么把上一张纸条也往左移，依次调整。如果调整完之后，第一张纸条超出了左端点则无解。因为这说明最少纸条数乘以纸条长度已经大于本段长度。否则我们就得到了一个合法解。
````
#include <bits/stdc++.h>
using namespace std;

using ll = long long;
using ld = long double;

#define all(a) begin(a), end(a)
#define len(a) int((a).size())

void solve(int /* test_num */) {
    int n, m, k, w;
    cin >> n >> m >> k >> w;
    vector<pair<int, int>> events;
    events.emplace_back(0, 0);
    events.emplace_back(w + 1, 0);

    for (int i = 0, x; i < n; i++) {
        cin >> x;
        events.emplace_back(x, 1);
    }

    for (int i = 0, x; i < m; i++) {
        cin >> x;
        events.emplace_back(x, 0);
    }

    sort(all(events));
    assert(events.back().second == 0);

    vector<int> ans;
    for (int l = 0; l + 1 < len(events); l++) {
        if (events[l].second == 1) {
            continue;
        }

        vector<int> cur;

        int r = l + 1;
        while (events[r].second == 1) {
            cur.push_back(events[r].first);
            r++;
        }

        if (cur.empty()) {
            continue;
        }

        vector<int> cur_ans;
        int last = -1e9 - 228;
        for (auto x : cur) {
            if (last + k > x) {
                continue;
            }
            cur_ans.push_back(x);
            last = x;
        }

        if (last + k > events[r].first) {
            int shift = last + k - events[r].first;
            for (int i = len(cur_ans) - 1; i >= 0; i--) {
                cur_ans[i] -= shift;
                if (i == 0) {
                    break;
                }

                if (cur_ans[i - 1] + k <= cur_ans[i]) {
                    break;
                }
                shift = cur_ans[i - 1] + k - cur_ans[i];
            }

            if (cur_ans[0] <= events[l].first) {
                cout << "-1\n";
                return;
            }
            assert(cur_ans.back() + k <= events[r].first);
        }

        for (auto x : cur_ans) {
            ans.push_back(x);
        }
    }

    cout << len(ans) << '\n';
    sort(all(ans));
    for (auto x : ans) {
        cout << x << ' ';
    }
    cout << '\n';
}

int main() {
    cin.tie(nullptr)->sync_with_stdio(false);

    int tests;
    cin >> tests;
    for (int test_num = 1; test_num <= tests; test_num++) {
        solve(test_num);
    }
}
````