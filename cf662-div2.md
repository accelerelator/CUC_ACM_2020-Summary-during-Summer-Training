# [Codeforces Round #662 (Div. 2)](http://codeforces.com/contest/1393)

- [Codeforces Round #662 (Div. 2)](#codeforces-round-662-div-2)
  - [A. Rainbow Dash, Fluttershy and Chess Coloring](#a-rainbow-dash-fluttershy-and-chess-coloring)
  - [B. Applejack and Storages](#b-applejack-and-storages)
  - [C. Pinkie Pie Eats Patty-cakes](#c-pinkie-pie-eats-patty-cakes)

## [A. Rainbow Dash, Fluttershy and Chess Coloring](http://codeforces.com/contest/1393/problem/A)
原文题目描述乱七八糟说不清楚，Announcement加了很多。

n×n 的棋盘，每回合可以选择一种颜色涂任意次，要求上色的块邻边是**边界**或是**上一回合上色的块**，求至少要多少回合才能把这个正方形区域涂满。

结果 n/2+1，如图。

![d9tqHg.png](https://s1.ax1x.com/2020/08/14/d9tqHg.png)

## [B. Applejack and Storages](http://codeforces.com/contest/1393/problem/B)

给定n个数，分别代表木板长度，给定q个操作，每个操作增加或减少一块木板，问是否能组成一个正方形和一个矩形（正方形也是矩形）。

卡了好长时间，开始像用优先队列或者set维护最大值和次大值，但是都存在着不好查找的问题。

用map记录出现次数，cnt2、cnt4分别记录出现2、4的倍数的次数，最后判断。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn = 1e5 + 5;

int t,x,n,q,cnt2,cnt4;
char c;
map<int,int> mp;

int main() {
	cnt2=cnt4=0;
	scanf("%d",&n);
	for(int i=0;i<n;i++) {
		scanf("%d",&x);
		mp[x]++;
		if(mp[x]%2==0) cnt2++;
		if(mp[x]%4==0) cnt4++;
	}
	scanf("%d",&q);
	getchar();
	while(q--) {
		scanf("%c", &c);
		scanf("%d",&x);
		getchar();
		if(c=='+') {
			mp[x]++;
			if(mp[x]%2==0) cnt2++;
			if(mp[x]%4==0) cnt4++;
		} else {
			if(mp[x]%2==0) cnt2--;
			if(mp[x]%4==0) cnt4--;
			mp[x]--;
		}
		if(cnt4>=1 && cnt2>=4) puts("YES");
		else puts("NO");
	}
	
}
```

## [C. Pinkie Pie Eats Patty-cakes](http://codeforces.com/contest/1393/problem/C)

将n个数更换顺序使相同的数字之间最小的距离最大。求距离。

二分WA了，不过感觉也会超时。

只有出现次数最多的数字会影响结果，同时可能出现多个数字同时出现次数最多，设有x个数字同时出现了mx次，则结果为$\lfloor \frac{n-x*mx}{mx-1}\rfloor +x-1$。

```cpp
#include <bits/stdc++.h>
using namespace std;
typedef long long ll;
const int maxn = 1e5 + 5;

int t,n,x,tcnt,mx;

int cnt[maxn];

int main() {
	scanf("%d",&t);
	while(t--) {
		memset(cnt,0,sizeof(cnt));
		scanf("%d",&n);
		for(int i=0;i<n;i++) {
			scanf("%d",&x); cnt[x]++;
		}
		sort(cnt+1,cnt+n+1,greater<int>());
		tcnt=1; mx=cnt[1];
		for(int i=2;i<=n+1;i++) {
			if(cnt[i]==mx) tcnt++;
		}
		printf("%d\n",(n-tcnt*mx)/(mx-1)+tcnt-1);
	}
}
```