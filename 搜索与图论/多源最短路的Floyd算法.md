# 多源最短路的Floyd算法
> Tags:  最短路  多源最短路  
> [AcWing 854. Floyd求最短路](https://www.acwing.com/activity/content/problem/content/923/)

## 题目要求
给定一个 n个点 m条边的有向图，图中可能存在重边和自环，边权可能为负数。  
再给定 k个询问，每个询问包含两个整数 x和 y，表示查询从点 x到点 y的最短距离，如果路径不存在，则输出 impossible。  
数据保证图中不存在负权回路。  

**输入格式**  
第一行包含三个整数 n,m,k  
接下来 m行，每行包含三个整数 x,y,z，表示存在一条从点 x到点 y的有向边，边长为 z。  
接下来 k行，每行包含两个整数 x,y，表示询问点 x到点 y的最短距离。  

**输出格式**  
共 k行，每行输出一个整数，表示询问的结果，若询问两点间不存在路径，则输出 impossible。  

**数据范围**  
$1 \leq n \leq 200$  
$1 \leq k \leq n^2$  
$1 \leq m \leq 20000$  

**输入样例**  
```
3 3 2
1 2 1
2 3 2
1 3 1
2 1
1 3
```

**输出样例**
```
impossible
1
```

## 整体思路
* n 比较小，题目查询了**多个起点、终点**的最短路，属于**多源最短路问题**，应使用 ***Floyd* 算法**  
* 时间复杂度为 $O(n^3)$  

## 整体代码
```
# include<cstring>
# include<algorithm>
# include<iostream>

using namespace std;

const int N = 210, INF = 1e9;
int d[N][N];
int n, m, q;

void floyd(){
    for(int k=1;k<=n;k++){
        for(int i=0;i<=n;i++){
            for(int j=0;j<=n;j++){
                d[i][j] = min(d[i][j],d[i][k]+d[k][j]);
            }
        }
    }
}

int main(){
    cin>>n>>m>>q;
    
    for(int i=1;i<=n;i++){
        for(int j=1;j<=n;j++){
            if(i == j) d[i][j] = 0;
            else d[i][j] = INF;
        }
    }
    
    while(m--){
        int a, b, c;
        scanf("%d%d%d",&a,&b,&c);
        d[a][b] = min(d[a][b],c);
    }
    
    floyd();
    
    while(q--){
        int a, b;
        scanf("%d%d",&a,&b);
        if(d[a][b]>1e9/2) printf("impossible\n");
        else printf("%d\n", d[a][b]);
    }
}
```
## 代码细节
1. 题目中有“重边和自环”，所以在输入时要记得**取最小值**
2. 因为是动态规划类问题，所以无限大会被减
