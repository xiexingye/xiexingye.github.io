# 深度优先算法的优点与思想
深度优先算法，是利用递推和递归实现的暴力搜索算法。因为递归使用的是堆空间，所以导致时间复杂度与一般的暴力枚举会更加快。

深度优先算法，基本思想就是对于一种情况搜索到底，如果不对，就回溯到上一个节点，对另一个节点进行搜索，运用的是一个栈的思想，即先进后出，后进先出。基于这个，我们可以使用数组和递归来模拟栈。

# 深度优先算法的实现
深度优先算法一般有一个模板，即    判断是否满足条件搜完=>若搜完就输出，若未搜完就继续进行递归搜索。
实现代码如下：
C语言实现

```
int a[510];   //存储每次选出来的数据
int book[510];   //标记是否被访问
int ans = 0; //记录符合条件的次数

void DFS(int cur){
	if(cur == k){ //k个数已经选完，可以进行输出等相关操作 
		for(int i = 0; i < cur; i++){
			printf("%d ", a[i]);
		} 
		ans++;
		return ;
	}
	
	for(int i = 0; i < n; i++){ //遍历 n个数，并从中选择k个数 
		if(!book[i]){ //若没有被访问 
			book[i] = 1; //标记已被访问 
			a[cur] = i;  //选定本数，并加入数组 
			DFS(cur + 1);  //递归，cur+1 
			book[i] = 0;  //释放，标记为没被访问，方便下次引用 
		}
	}
}
```

C++实现

```
vector<int> a; // 记录每次排列 
vector<int> book; //标记是否被访问 

void DFS(int cur, int k, vector<int>& nums){
    if(cur == k){ //k个数已经选完，可以进行输出等相关操作 
        for(int i = 0; i < cur; i++){
			printf("%d ", a[i]);
		} 
        return ;
    }
    for(int i = 0; i < k; i++){ //遍历 n个数，并从中选择k个数 
        if(book[nums[i]] == 0){ //若没有被访问
            a.push_back(nums[i]); //选定本输，并加入数组 
            book[nums[i]] = 1; //标记已被访问 
            DFS(cur + 1, n, nums); //递归，cur+1 
            book[nums[i]] = 0; //释放，标记为没被访问，方便下次引用 
            a.pop_back(); //弹出刚刚标记为未访问的数
        }
    }
}
```