# 单源最短路的朴素 *Dijkstra* 算法
> 题源：[AcWing 849](https://www.acwing.com/problem/content/description/851/)  
> tags：Dijkstra算法  最短路问题  模板题  

## 题目要求
给定一个 n个点 m条边的有向图，图中可能存在重边和自环，所有边权均为正值。  
请你求出 1号点到 n号点的最短距离，如果无法从 1号点走到 n号点，则输出 −1。  

**输入格式**  
第一行包含整数n和m。  
接下来 m行每行包含三个整数 x,y,z，表示存在一条从点 x到点 y的有向边，边长为 z。  

**输出格式**  
输出一个整数，表示 1号点到 n号点的最短距离。  
如果路径不存在，则输出 −1

**数据范围**  
$1 \leq n \leq 500$  
$1 \leq m \leq 10^5$  

**输入样例**
```
3 3
1 2 2
2 3 1
1 3 4
```

**输出样例**
```
3
```

## 整体思路
1. 看题：可得是**单源最短路问题**，存在带权边，重边，自环，可以用 **朴素*Dijkstra*算法** 解决
2. 看数据范围：有 $n^2 \approx m$ ，应用邻接矩阵存储图

## 整体代码
```
# include<iostream>
# include<cstring>
# include<algorithm>

using namespace std;

const int N = 510;
int n, m, x, y, z;
int dist[N];
bool st[N];
int g[N][N];

int dijkstra(){
    memset(dist,0x3f,sizeof(dist)); //距离数组的初始化
    dist[1] = 0;
    for(int i=0;i<n;i++){
        int t=-1;
        
        for(int j=1;j<=n;j++){ //找到距离最小的点
            if(!st[j] && (t==-1 || dist[t]>dist[j]))
                t = j; 
        }
        
        st[t] = true; //确定t已经取到最小距离
        
        for(int j=1;j<=n;j++){
            dist[j] = min(dist[j],dist[t]+g[t][j]);
        }
    }
    if(dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}

int main(void){
    scanf("%d%d",&n ,&m);
    memset(g,0x3f,sizeof(g)); //图的初始化
    
    while(m--){
        scanf("%d%d%d",&x,&y,&z);
        g[x][y] = min(g[x][y],z); //防重边
    }
    
    int ans = dijkstra();
    cout<<ans;
    
    return 0;
}
```

## 代码分析
### 1. 变量定义
```
const int N = 510;
int n, m, x, y, z;
int dist[N];
bool st[N];
int g[N][N];
```
* dist 数组表示点n到1号点的距离
* st 数组表示判断n点是否**已经确定是最短路径**
* g 矩阵存储图和权重

### 2. 朴素 *Dijkstra* 算法
```
int dijkstra(){
    memset(dist,0x3f,sizeof(dist)); //距离数组的初始化
    dist[1] = 0;
    for(int i=0;i<n;i++){
        int t=-1;
        
        for(int j=1;j<=n;j++){ //找到距离最小的点
            if(!st[j] && (t==-1 || dist[t]>dist[j]))
                t = j; 
        }
        
        st[t] = true; //确定t已经取到最小距离
        
        for(int j=1;j<=n;j++){
            dist[j] = min(dist[j],dist[t]+g[t][j]);
        }
    }
    if(dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}
 ```
1. 初始化
  ```
    memset(dist,0x3f,sizeof(dist)); //距离数组的初始化
    dist[1] = 0;
  ```
    * 将距离数组初始化为最大
    * 将第一个点（源点距离他的距离设置为0

2. 主循环
   ```
   for(int i=0;i<n;i++){
        int t=-1;
   ...
   }
   ```
   * 单纯的循环n次
   * 每次循环的作用：找到离源点距离最小的点、标记、更新其它点的距离
   * 变量t的作用是**存储距离最小的点**

3. 找到距离最小的点并将其锁定
   ```
   for(int j=1;j<=n;j++){ //找到距离最小的点
       if(!st[j] && (t==-1 || dist[t]>dist[j]))
           t = j; 
        }
   st[t] = true; //确定t已经取到最小距离
   ```

 4. 更新其他点的距离
    ``` 
    for(int j=1;j<=n;j++){
        dist[j] = min(dist[j],dist[t]+g[t][j]);
    }
    ```

5. 返回
   ```
   if(dist[n] == 0x3f3f3f3f) return -1;
   return dist[n];
   ```

### main 函数
```
int main(void){
    scanf("%d%d",&n ,&m);
    memset(g,0x3f,sizeof(g)); //图的初始化
    
    while(m--){
        scanf("%d%d%d",&x,&y,&z);
        g[x][y] = min(g[x][y],z); //防重边
    }
    
    int ans = dijkstra();
    cout<<ans;
    
    return 0;
}
```
* 注意在输入边时要与自身比较以**防重边**

## 扩展：堆优化的 *Dijkstra* 算法
相比于朴素的 *Dijkstra* 算法，堆优化算法使用了优先队列，不再用**第一个循环**寻找当前最接近的点了  
* 朴素算法： $O(n^2)$  
* 堆优化方法： $O(mlog(n))$   

```
# include <iostream>
# include <algorithm>
# include <cstring>
# include <queue>

using namespace std;
typedef pair<int, int> PII;

const int N = 2e5 + 10;

int n, m;
int dist[N];
int h[N], e[N], w[N], ne[N], idx;
bool st[N];

void add(int x,int y,int z){
    e[idx] = y, w[idx] = z, ne[idx] = h[x], h[x] = idx++;
    return;
}

int dijkstra(void){
    // 初始化
    priority_queue<PII,vector<PII>,greater<PII>> heap;
    memset(dist,0x3f,sizeof(dist));
    dist[1] = 0;
    heap.push({0,1});
    
    
    while(heap.size()){
        // 出堆
        auto t = heap.top();
        heap.pop();
        int ver = t.second, distance = t.first;
        
        // 标记
        if(st[ver]) continue;
        st[ver] = true;
        
        for(int i=h[ver];i!=-1;i=ne[i]){
            int j = e[i]; // j是点
            if(dist[j]>dist[ver]+w[i]){ // 需要更新
                dist[j] = dist[ver]+w[i];
                heap.push({dist[j], j});
            }
        }
    }
    if(dist[n] == 0x3f3f3f3f) return -1;
    return dist[n];
}


int main(void){
    cin>>n>>m;
    
    memset(h, -1, sizeof h);
    
    while(m--){
        int x, y, z;
        scanf("%d%d%d",&x,&y,&z);
        add(x,y,z);
    }
    
    int ans = dijkstra();
    printf("%d", ans);
    
    return 0;
}
```
























