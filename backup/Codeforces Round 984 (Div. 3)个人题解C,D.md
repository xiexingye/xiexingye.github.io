# C
本题思路：暴力枚举，但是要进行优化，不然会超时间复杂度
优化策略：首先记录符合1100模式的字符串的首字符的位置，然后在查询时候，我们只需要修改的前后位置并修改就行。
````
#include<bits/stdc++.h>
using namespace std;
void solve() {
    string s;
    cin >> s;
    int q;
    cin >> q;
    int n = s.length();
    set<int> positions;
    for (int i = 0; i <= n - 4; i++) {
        if (s[i] == '1' && s[i+1] == '1' && s[i+2] == '0' && s[i+3] == '0') {
            positions.insert(i);
        }
    }

    while (q--) {
        int i;
        char v;
        cin >> i >> v;
        i--;
        s[i] = v;
        for (int j = max(0, i - 3); j <= min(n - 4, i); j++) {
            if (s[j] == '1' && s[j+1] == '1' && s[j+2] == '0' && s[j+3] == '0') {
                positions.insert(j);
            } else {
                positions.erase(j);
            }
        }
        if (!positions.empty()) {
            cout << "YES\n";
        } else {
            cout << "NO\n";
        }
    }
}

int main() {
    int t;
    cin >> t;
    while (t--) {
        solve();
    }
    return 0;
}
````

# D
题目意思是每层都是顺时针变为一个字符串，然后找1543个数，措施我们可以通过将一层字符串前三个字符再拼到末尾，就能实现环形结构
````
#include <bits/stdc++.h>
#define int long long
using namespace std;
const int maxn = 2e6 + 7;
char aa[5000][5000];
void solve(){
    int n,m;
    cin >> n >> m;
    for (int i = 1;i <= n;i++){
        for (int j = 1;j <= m;j++){
            cin >> aa[i][j];
        }
    }
    int cnt = 0;
    for (int i = 1;i <= min(n,m) / 2;i++){
        string bb,cc = "1543";
        int s = i + 1,e = m - i + 1;
        while (s <= e) {
            bb += aa[i][s++];
        }
        s = i + 1,e = n - i + 1;
        while(s <= e) {
            bb += aa[s++][m - i + 1];
        }
        s = m - i + 1 - 1,e = i;
        while(s >= e) {
            bb += aa[n - i + 1][s--];
        }
        s = n - i + 1 - 1,e = i;
        while (s >= e) {
            bb += aa[s--][i];
        }
        bb += bb.substr(0,3);
        for (int j = 0;cc.size() >= 4 && j + 3 < bb.size();j++){ 
            if(bb.substr(j,4) == cc)cnt++;
        }
    }
    cout << cnt << endl;
}

signed main(){
    int t;
    cin >> t;
    while (t--){
        solve();
    }
}

````