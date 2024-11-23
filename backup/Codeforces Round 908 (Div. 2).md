# A
题意：A和B玩游戏，当回合结束或者某位玩家赢得足够次数时，游戏就结束。
思路：最后一把谁赢就是谁获得游戏胜利。
````
#include<bits/stdc++.h>
using namespace std;
#define int long long
void solve()
{
    int n;
    cin>>n;
    string s;
    cin>>s;
    int cnt1=0;
    int cnt2=0;
    cout<<s[n-1]<<"\n";
}
signed main()
{
    int t;
    cin>>t;
    while(t--){
        solve();
    }
    return 0;
}
````
# B
题目大意：给定一个数组a，并且告诉数组b只有1，2，3组成，要满足三个条件中的两个，构造一个数组b
思路：若有三个数相等，他们下标在b数组上分别为1、2、3，那么将同时满足3个条件，不满足题意。因此对于相等的数而言，只能选其中一个条件进行满足。要求刚好满足两个条件，则至少需要两个不同的数在数组中存在相等的数。让他们分别满足两个条件，其余的数全部置1即可。

````
#include <bits/stdc++.h>
using namespace std;
#define LL long long
#define pb push_back
#define x first
#define y second
#define endl '\n'
const LL maxn = 4e05+7;
const LL N=1e05+10;
const LL mod=1e09+7;
typedef pair<int,int>pl;
priority_queue<LL , vector<LL>, greater<LL> >t;
priority_queue<LL> q;
LL gcd(LL a, LL b){
    return b > 0 ? gcd(b , a % b) : a;
}

LL lcm(LL a , LL b){
    return a / gcd(a , b) * b;
}
void solve()
{
    int n;
    cin>>n;
    int a[n];
    for(int i = 0 ; i < n ; i ++)
        cin>>a[i];
    map<int,int>mp;
    int ans[n];
    int flag = 0;
    for(int i = 0 ; i < n ; i ++){
        mp[a[i]]++;
        if(mp[a[i]] == 1){
            ans[i] = 1;
        }
        else if(mp[a[i]] == 2){
            if(flag == 0){
                ans[i] = 2;
                flag ++;
            }
            else if(flag == 1){
                ans[i] = 3;
                flag ++;
            }
            else{
                ans[i] = 1;
            }
        }
        else{
            ans[i] = 1;
        }
    }
    if(flag == 2){
        for(int i = 0 ; i < n ; i ++){
            cout<<ans[i]<<" ";
        }
        cout<<endl;
    }
    else{
        cout<<-1<<endl;
    }
}
int main()
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cout.precision(10);
    int t=1;
    cin>>t;
    while(t--)
    {
        solve();
    }
    return 0;
}
````

# C
题目大意：给定一个长度为n的数列b，要求构造出数列a，问是否能够用k次操作将a数组转化成b数组。
思路：考虑逆推，由于每次操作选定的都会在操作之后变为数组的最后一位，那么b数组的最后一位就是最后一次操作选择的。如此不断逆推k次，若期间出现了循环，则一定能够构造出a，使得k次操作后变为b。若期间出现了逆推得到的的情况，则代表无法选择该数进行操作，则代表操作无法进行，则代表无法构造出a。
````
 #include <bits/stdc++.h>
using namespace std;
#define LL long long
#define pb push_back
#define x first
#define y second 
#define endl '\n'
const LL maxn = 4e05+7;
const LL N=1e05+10;
const LL mod=1e09+7;
typedef pair<int,int>pl;
priority_queue<LL , vector<LL>, greater<LL> >t;
priority_queue<LL> q;
LL gcd(LL a, LL b){
	return b > 0 ? gcd(b , a % b) : a;
}
 
LL lcm(LL a , LL b){
	return a / gcd(a , b) * b;
}
void solve() 
{
	int n , k;
	cin >> n >> k;
	int a[n];
	for(int i = 0 ; i < n ; i ++){
		cin>>a[i];
	}
	int left = 0;
	int st = n - 1;
	map<int,int>mp;
	mp[0] = 1;
	for(int i = 0 ; i < min(n , k) ; i ++){
		if(a[st - left] > n){
			cout<<"No\n";
			return;
		}
		left += a[st - left];
		left %= n;
		if(mp[left] == 0){
			mp[left]++;
		}
		else{
			cout<<"Yes\n";
			return;
		}
	}
	cout<<"Yes\n";
}            
int main() 
{
    ios::sync_with_stdio(false);
    cin.tie(0);
    cout.tie(0);
    cout.precision(10);
    int t=1;
	cin>>t;
    while(t--)
    {
    	solve();
    }
    return 0;
}
````