# 最短路模版

## 摘要

本文给出适应不同问题条件的最短路算法模版(cpp语法)，包括[朴素Dijkstra算法](#1)，[堆优化dijkstra算法](#2)，[Bellman-Ford算法](#3)，[SPFA算法](#4)([求最短路](#4.1)和[判断负环](#4.2))和[Floyd算法](#5)。文中题目均来源于[acwing](https://www.acwing.com/)。
下图为选择最短路算法的基本思路：
![p1](p1.png)

## 朴素Dijkstra算法 {#1}

849 Dijkstra求最短路 I：
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环，所有边权均为正值。
请你求出 1 号点到 n 号点的最短距离，如果无法从 1 号点走到 n 号点，则输出 −1。

输入格式：
第一行包含整数 n 和 m。
接下来 m 行每行包含三个整数 x,y,z，表示存在一条从点 x 到点 y 的有向边，边长为 z。

输出格式：
输出一个整数，表示 1 号点到 n 号点的最短距离。
如果路径不存在，则输出 −1。

数据范围：
1≤n≤500,
1≤m≤10^5,
图中涉及边长均不超过10000。

```cpp
#include<iostream>
#include<cstring>
#include<algorithm>

using namespace std;
const int N=510;
//g为邻接矩阵，dist为到起始点的距离
int g[N][N],dist[N];
//st为当前状态，st[i]==1表示已确定dist[i]
bool st[N];
int n,m;

int dijkstra(int start,int end){
    memset(dist,0x3f,sizeof dist);
    //初始化起始点距离
    dist[start]=0;
    //遍历n次，每次找距离最短的点
    for(int i=0;i<n;i++){
        //t存储距离最短的点的下标，初始为-1表示未找到
        int t=-1;
        for(int j=1;j<=n;j++){
            //在未确定的点中找距离最短的点
            if(!st[j] && (t==-1 || dist[j]<dist[t])){
                t=j;
                if(t==end){
                    break;
                }
            }
        }
        st[t]=1;
        //用当前距离最短的点更新未确定的点的距离
        for(int j=1;j<=n;j++){
            if(!st[j]) dist[j]=min(g[t][j]+dist[t],dist[j]);
        }
    }
    if(dist[end]==0x3f3f3f3f) return -1;
    else return dist[end];
}

int main(){
    cin>>n>>m;
    memset(g,0x3f,sizeof g);
    for(int i=1;i<=m;i++){
        int a,b,c;
        cin>>a>>b>>c;
        //重边取权最小的边
        g[a][b]=min(g[a][b],c);
    }
    cout<<dijkstra(1,n);

    return 0;
}
```

## 堆优化dijkstra算法 {#2}

850 Dijkstra求最短路 II：
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环，所有边权均为非负值。
请你求出 1 号点到 n 号点的最短距离，如果无法从 1 号点走到 n 号点，则输出 −1。

输入格式：
第一行包含整数 n 和 m。
接下来 m 行每行包含三个整数 x,y,z，表示存在一条从点 x 到点 y 的有向边，边长为 z。

输出格式：
输出一个整数，表示 1 号点到 n 号点的最短距离。
如果路径不存在，则输出 −1。

数据范围：
1≤n,m≤1.5×10^5,
图中涉及边长均不小于 0，且不超过 10000。
数据保证：如果最短路存在，则最短路的长度不超过 10^9。

```cpp
#include<algorithm>
#include<iostream>
#include<cstring>
#include<queue>

using namespace std;
typedef pair<int,int> PII;
const int N=150010;

int h[N],e[N],ne[N],w[N],idx;
int dist[N];
bool st[N];

void add(int a,int b,int c){
    e[idx]=b;
    w[idx]=c;
    ne[idx]=h[a];
    h[a]=idx++;
}

int dijkstra(int start,int end){
    memset(dist,0x3f,sizeof dist);
    dist[start]=0;
    priority_queue<PII,vector<PII>,greater<PII>> heap;
    heap.push({0,start});
    while(!heap.empty()){
        auto t=heap.top();
        heap.pop();
        int ver=t.second,distance=t.first;
        if(ver==end) break;
        if(st[ver]) continue;
        st[ver]=1;
        for(int i=h[ver];i!=-1;i=ne[i]){
            int j=e[i];
            if(dist[j]>distance+w[i]){
                dist[j]=distance+w[i];
                heap.push({dist[j],j});
            }
        }
    }
    if(dist[end]==0x3f3f3f3f) return -1;
    else return dist[end];
}

int main()
{
    int n,m;cin>>n>>m;
    memset(h,-1,sizeof h);
    for(int i=1;i<=m;i++){
        int a,b,c;
        cin>>a>>b>>c;
        add(a,b,c);
    }
    cout<<dijkstra(1,n);

    return 0;
}
```

## Bellman-Ford算法 {#3}

853 有边数限制的最短路：
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环， 边权可能为负数。
请你求出从 1 号点到 n 号点的最多经过 k 条边的最短距离，如果无法从 1 号点走到 n 号点，输出 impossible。
注意：图中可能 存在负权回路 。

输入格式：
第一行包含三个整数 n,m,k。
接下来 m 行，每行包含三个整数 x,y,z，表示存在一条从点 x 到点 y 的有向边，边长为 z。
点的编号为 1∼n。

输出格式：
输出一个整数，表示从 1 号点到 n 号点的最多经过 k 条边的最短距离。
如果不存在满足条件的路径，则输出 impossible。

数据范围：
1≤n,k≤500,
1≤m≤10000,
1≤x,y≤n，
任意边长的绝对值不超过 10000。

```cpp
#include<cstring>
#include<iostream>
#include<algorithm>

using namespace std;
const int N=10010;

struct Edge{
    int a,b,w;
}edges[N];

int n,m,k;
int dist[N],last[N];

void bellman_ford(int start,int end){
    memset(dist,0x3f,sizeof dist);
    dist[start]=0;
    for(int i=1;i<=k;i++){
        memcpy(last,dist,sizeof dist);
        for(int j=1;j<=m;j++){
            auto e=edges[j];
            dist[e.b]=min(dist[e.b],last[e.a]+e.w);
        }
    }
}

int main(){
    cin>>n>>m>>k;
    for(int i=1;i<=m;i++){
        int a,b,c;
        cin>>edges[i].a>>edges[i].b>>edges[i].w;
    }
    bellman_ford(1,n);
    if(dist[n]>0x3f3f3f3f/2) cout<<"impossible";
    else cout<<dist[n];

    return 0;
}
```

## SPFA算法 {#4}

### 求最短路 {#4.1}

851 spfa求最短路：
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环， 边权可能为负数。
请你求出 1 号点到 n 号点的最短距离，如果无法从 1 号点走到 n 号点，则输出 impossible。
数据保证不存在负权回路。

输入格式：
第一行包含整数 n 和 m。
接下来 m 行每行包含三个整数 x,y,z，表示存在一条从点 x 到点 y 的有向边，边长为 z。

输出格式：
输出一个整数，表示 1 号点到 n 号点的最短距离。
如果路径不存在，则输出 impossible。

数据范围：
1≤n,m≤10^5,
中涉及边长绝对值均不超过 10000。

```cpp
#include<iostream>
#include<cstring>
#include<algorithm>
#include<queue>

using namespace std;
const int N=100010;
int h[N],e[N],ne[N],w[N],idx;
int dist[N];
bool st[N];

void add(int a,int b,int c){
    e[idx]=b;
    w[idx]=c;
    ne[idx]=h[a];
    h[a]=idx++;
}

int spfa(int start,int end){
    memset(dist,0x3f,sizeof dist);
    dist[start]=0;
    queue<int>q;
    q.push(start);
    st[start]=true;
    while(!q.empty()){
        auto t=q.front();
        q.pop();
        st[t]=false;
        for(int i=h[t];i!=-1;i=ne[i]){
            int j=e[i];
            if(dist[j]>dist[t]+w[i]){
                dist[j]=dist[t]+w[i];
                if(!st[j]){
                    q.push(j);
                    st[j]=true;
                }
            }
        }
    }
    return dist[end];
}

int main(){
    memset(h,-1,sizeof h);
    int n,m;cin>>n>>m;
    for(int i=1;i<=m;i++){
        int a,b,c;
        cin>>a>>b>>c;
        add(a,b,c);
    }
    int t=spfa(1,n);
    if(t==0x3f3f3f3f) cout<<"impossible";
    else cout<<t;

    return 0;
}
```

### 判断负环 {#4.2}

852 spfa判断负环：
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环， 边权可能为负数。
请你判断图中是否存在负权回路。

输入格式：
第一行包含整数 n 和 m。
接下来 m 行每行包含三个整数 x,y,z，表示存在一条从点 x 到点 y 的有向边，边长为 z。

输出格式：
如果图中存在负权回路，则输出 Yes，否则输出 No。

数据范围：
1≤n≤2000,
1≤m≤10000,
图中涉及边长绝对值均不超过 10000。

```cpp
#include<iostream>
#include<cstring>
#include<algorithm>
#include<queue>

using namespace std;
const int N=100010;
int h[N],e[N],ne[N],w[N],idx;
int dist[N],cnt[N];
bool st[N];
int n,m;

void add(int a,int b,int c){
    e[idx]=b;
    w[idx]=c;
    ne[idx]=h[a];
    h[a]=idx++;
}

bool spfa(){
    queue<int>q;
    for(int i=1;i<=n;i++){
        q.push(i);
        st[i]=true;
    }
    while(!q.empty()){
        auto t=q.front();
        q.pop();
        st[t]=false;
        for(int i=h[t];i!=-1;i=ne[i]){
            int j=e[i];
            if(dist[j]>dist[t]+w[i]){
                dist[j]=dist[t]+w[i];
                cnt[j]=cnt[t]+1;
                if(cnt[j]>n) return true;
                if(!st[j]){
                    q.push(j);
                    st[j]=true;
                }
            }
        }
    }
    return false;
}

int main(){
    memset(h,-1,sizeof h);
    cin>>n>>m;
    for(int i=1;i<=m;i++){
        int a,b,c;
        cin>>a>>b>>c;
        add(a,b,c);
    }
    bool t=spfa();
    if(t) cout<<"Yes";
    else cout<<"No";

    return 0;
}
```

## Floyd算法 {#5}

854 Floyd求最短路：
给定一个 n 个点 m 条边的有向图，图中可能存在重边和自环，边权可能为负数。
再给定 k 个询问，每个询问包含两个整数 x 和 y，表示查询从点 x 到点 y 的最短距离，如果路径不存在，则输出 impossible。
数据保证图中不存在负权回路。

输入格式：
第一行包含三个整数 n,m,k。
接下来 m 行，每行包含三个整数 x,y,z，表示存在一条从点 x 到点 y 的有向边，边长为 z。
接下来 k 行，每行包含两个整数 x,y，表示询问点 x 到点 y 的最短距离。

输出格式：
共 k 行，每行输出一个整数，表示询问的结果，若询问两点间不存在路径，则输出 impossible。

数据范围：
1≤n≤200,
1≤k≤n^2
1≤m≤20000,
图中涉及边长绝对值均不超过 10000。

```cpp
#include<iostream>
#include<algorithm>

using namespace std;
const int N=210,INF=1e9;
int d[N][N],n,m,k;

void floyd(){
    for(int k=1;k<=n;k++)
        for(int i=1;i<=n;i++)
            for(int j=1;j<=n;j++)
                d[i][j]=min(d[i][j],d[i][k]+d[k][j]);
}

int main()
{
    cin>>n>>m>>k;
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            if(i==j) d[i][j]=0;
            else d[i][j]=INF;
        }
    }
    for(int i=1;i<=m;i++){
        int a,b,c;
        cin>>a>>b>>c;
        d[a][b]=min(d[a][b],c);
    }
    floyd();

    for(int i=1;i<=k;i++){
        int x,y;
        cin>>x>>y;
        if(d[x][y]>INF/2) cout<<"impossible\n";
        else cout<<d[x][y]<<'\n';
    }
    
    return 0;
}
```

<script src="https://giscus.app/client.js"
    data-repo="chenjh726/chenjh726.github.io"
    data-repo-id="R_kgDOMeZntw"
    data-category="General"
    data-category-id="DIC_kwDOMeZnt84ChYwb"
    data-mapping="pathname"
    data-strict="0"
    data-reactions-enabled="1"
    data-emit-metadata="0"
    data-input-position="bottom"
    data-theme="light_high_contrast"
    data-lang="zh-CN"
    crossorigin="anonymous"
    async>
</script>