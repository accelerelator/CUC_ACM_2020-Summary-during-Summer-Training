# [Codeforces Round #661 (Div. 3)](http://codeforces.com/contest/1399)

- [Codeforces Round #661 (Div. 3)](#codeforces-round-661-div-3)
  - [A. Remove Smallest](#a-remove-smallest)
  - [B. Gifts Fixing](#b-gifts-fixing)
  - [C. Boats Competition](#c-boats-competition)
  - [D. Binary String To Subsequences](#d-binary-string-to-subsequences)
  - [E1. Weights Division (easy version)](#e1-weights-division-easy-version)


## [A. Remove Smallest](http://codeforces.com/contest/1399/problem/A)

签到，排序之后判断差值是否大于一。
```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn = 1e5 + 5;

int main() {
	int t,n;
	scanf("%d",&t);
	while(t--) {
		int a[55];
		int flag=0;
		scanf("%d",&n);
		for(int i=0;i<n;i++) scanf("%d",&a[i]);
		sort(a,a+n);
		for(int i=1;i<n;i++) {
			if(a[i]-a[i-1]>1) {flag=1; break;}
		}
		if(flag) puts("NO");
		else puts("YES");
	}
}
```

## [B. Gifts Fixing](http://codeforces.com/contest/1399/problem/B)
签到。模拟。
```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn = 1e5 + 5;

int main() {
	int t,n;
	scanf("%d",&t);
	while(t--) {
		int a[55],b[55];
		int mia,mib;
		mia=mib=0x3f3f3f3f;
		scanf("%d",&n);
		for(int i=0;i<n;i++) scanf("%d",&a[i]);
		for(int i=0;i<n;i++) scanf("%d",&b[i]);
		for(int i=0;i<n;i++) {
			mia=min(mia,a[i]);
			mib=min(mib,b[i]);
		}
		ll ans = 0;
		for(int i=0;i<n;i++) {
			int d=min(a[i]-mia,b[i]-mib);
			a[i]-=d; b[i]-=d; ans+=d;
			ans += a[i]-mia+b[i]-mib;
		}
		printf("%lld\n",ans);
	}
}
```
## [C. Boats Competition](http://codeforces.com/contest/1399/problem/C)
题意是给定n个数（n<=50），求两两组合能得到的最多的数的个数。

暴力遍历把每两个数相加的结果都存在ans[i]种。
```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;

int a[55],cnt[55],ans[105];
vector<int> v;
int t,n;

int main() {
	scanf("%d",&t);
	while(t--) {
		scanf("%d",&n);
		memset(ans,0,sizeof(ans));
		memset(cnt,0,sizeof(cnt));
		v.clear();
		for(int i=0;i<n;i++) {
			scanf("%d",&a[i]);
			if(!cnt[a[i]]) v.push_back(a[i]);
			cnt[a[i]]++;
		}
		int len = v.size();
		for(int i=0;i<len;i++) {
			for(int j=i;j<len;j++) {
				if(i==j) ans[v[i]+v[j]] += cnt[v[i]]/2;
				else ans[v[i]+v[j]] += min(cnt[v[i]],cnt[v[j]]);
			}
		}
		int mx=-1;
//		for(int i=0;i<=100;i++) if(ans[i]) printf("(%d:%d)\n",i,ans[i]);
		for(int i=0;i<=100;i++) mx=max(mx,ans[i]);
		printf("%d\n",mx);
	}
}
```

## [D. Binary String To Subsequences](http://codeforces.com/contest/1399/problem/D)
将一个01字符串分组，分成不同的子序列（不连续），标定每个数位的组号，如`0011`则结果为`1 2 1 2`。

比赛期间没想太多，用了两个set维护01出现的位置（set查找O(logn)删除O(1)），之后根据需要照0或1去二分搜索。总复杂度O(nlogn)。

标程好像是用队列？没仔细看。
```cpp
#include <bits/stdc++.h>
#define show(x) std::cerr << #x << "=" << x << std::endl
using namespace std;
typedef long long ll;
const int maxn = 2e5 + 5;

set <int> pos0, pos1;
int ans[maxn], t, n;
string s;

int main() {
	scanf("%d",&t);
	while(t--) {
		memset(ans,0,sizeof(ans));
		pos0.clear(); pos1.clear();
		scanf("%d",&n);
		cin>>s;
		for(int i=0;i<n;i++) {
			if(s[i]=='1') pos1.insert(i);
			else pos0.insert(i);
		}
		int cnt=0, cur=0, flag, k=1;
		set<int>::iterator it;
		while(cnt<n) {
			if(pos0.empty()) flag=0;
			else if(pos1.empty()) flag=1; 
			else flag = *(pos0.begin()) < *(pos1.begin());
			cur=0;
			while(1) {
				if(flag) {
					it=pos0.lower_bound(cur);
					if(it==pos0.end()) break;
					int temp = *it;
//					show(temp);
					pos0.erase(it);
					ans[temp]=k; flag=0; cur=temp+1;
				} else {
					it=pos1.lower_bound(cur);
					if(it==pos1.end()) break;
					int temp = *it;
//					show(temp);
					pos1.erase(it);
					ans[temp]=k; flag=1; cur=temp+1;
				}
				cnt++;
			}
			k++;
		}
		printf("%d\n",k-1);
		for(int i=0;i<n;i++) printf("%d ",ans[i]); printf("\n");
	}
}
```

## [E1. Weights Division (easy version)](http://codeforces.com/contest/1399/problem/E1)
一遇到数据结构/DP就没法做系列。

题目给定一棵树，1是根节点，给定每条边权值，根节点到所有叶结点权值之和即为树的总权值，现在每次操作可以将一条边的权值除以二（向下取整），问最少多少次可以使总权值小于S。

优先队列维护收益，每次贪心底删除收益最大的边。

优先队列元素定义顺序还跟结果有关系？没太搞懂。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn = 1e5 + 5;

int t, n, u, v, w;
ll S;

struct node {
	ll cnt,val; //写成val,cnt就不对了
	bool operator<(const node t)const{
		return val*cnt-val/2*cnt < t.val*t.cnt-t.val/2*t.cnt;
	}
};

struct edge {
	ll v, w;
};

vector<vector<edge> > G;
vector<ll> cnt, val;

void dfs(int u, int f) {
	if(f!=-1 && G[u].size()==1) { // 叶结点 
		cnt[u]=1;
	}
	for(int i=0;i<G[u].size();i++) {
		edge temp = G[u][i];
		if(temp.v == f) continue;
		val[temp.v] = temp.w;
		dfs(temp.v, u);
		cnt[u] += cnt[temp.v]; // 从根节点到叶结点经过了多少次 
	}
}

int main() {
	scanf("%d",&t);
	while(t--) {
		scanf("%d %lld",&n,&S);
		G = vector<vector<edge> >(n+1);
		cnt = val = vector<ll>(n+1); //init
		for(int i=0;i<n-1;i++) {
			scanf("%d%d%d",&u,&v,&w);
			G[u].push_back({v,w});
			G[v].push_back({u,w});
		}
		dfs(1, -1);
		priority_queue<node> q;
		ll sum = 0;
		for(int i=2;i<=n;i++) {
			sum += cnt[i]*val[i];
			q.push({cnt[i], val[i]});
		}
		int ans = 0;
		while(sum > S) {
			node temp = q.top();
			q.pop();
			sum -= temp.cnt*temp.val-temp.val/2*temp.cnt;
			q.push({temp.cnt, temp.val/2});
			ans++;
		}
		printf("%d\n",ans);
		
	}
}
```