<center><b><font size=6>Basic Graph Theory</font></b></center>



### 1. Graph Storage Structures

___

**Adjacency Matrix**: A two-dimensional array `graph[a][b]` stores the edge weight from node $a$ to node $b$. If no edge exists, the value is set to $+\infty$. This structure is suitable only when there are no parallel edges, or when parallel edges can be ignored.

+ Space complexity: $O(n^2)$  
+ Query existence of edge $(x,y)$: $O(1)$  
+ Enumerate all outgoing edges of a node: $O(n)$  
+ Traverse the entire graph: $O(n^2)$

```cpp
memset(graph,INF,sizeof(graph));
for(int i=1;i<=n;i++)
{
    graph[i][i]=0;
}
for(int i=0;i<m;i++)
{
    int x=read(),y=read(),z=read();
    graph[x][y]=graph[y][x]=min(graph[x][y],z);
}
````

**Adjacency List**: A dynamic array `graph[maxn]` that stores a list of outgoing edges for each node. The edges of a node can optionally be sorted.

* Space complexity: $O(m)$
* Query existence of edge $(x,y)$: $O[d^+(x)]$ (or $O[\log d^+(x)]$ if sorted and binary search is used)
* Enumerate outgoing edges: $O(d^+(x))$
* Traverse the entire graph: $O(n+m)$

```cpp
vector<pair<int,int>> graph[maxn];

// initialization
for(int i=1;i<=n;i++)
{
    graph[i].clear();
}

// build graph
for(int i=0;i<m;i++)
{
    int x=read(),y=read(),z=read();
    graph[x].emplace_back(y,z);
}

// iterate over all outgoing edges of x
for(auto i:graph[x])
{
    int y=i.first,z=i.second;
    //access (x,y,z)
}
```

**Linked Forward Star**: A linked-list style implementation of adjacency lists.

* Space complexity: $O(m)$
* Query existence of edge $(x,y)$: $O(d^+(x))$
* Enumerate outgoing edges: $O(d^+(x))$
* Traverse the entire graph: $O(n+m)$

```cpp
struct node
{
    int to;
    int val;
    int next;   // index of next edge
} edge[maxm];   // for undirected graphs, allocate twice the space
// head[i] = index of the first edge starting from i
int head[maxn],edge_num;

void add_edge(int from,int to,int val)
{
    edge[edge_num]=(node){to,val,head[from]};
    head[from]=edge_num++;
}
```
```cpp
// initialization
edge_num=0;
memset(head,-1,sizeof(head));

// build graph
for(int i=0;i<m;i++)
{
    int x=read(),y=read(),z=read();
    add_edge(x,y,z);
    // for undirected graphs, also add_edge(y,x,z)
    // edge[i] and edge[i^1] form a pair of reverse edges
}

// traverse all outgoing edges of x
for(int i=head[x];i>=0;i=edge[i].next)
{
    // access edge[i]
}
```



### 2. Depth-First Search

---

​	At each node $x$, DFS recursively follows any outgoing edge until backtracking is required. Each edge is visited exactly once (twice for undirected graphs). Time complexity $O(n+m)$.

```cpp
void dfs(int x)
{
    vis[x]=true;
    dfn[x]=++cnt;   // timestamp
    for(int i=head[x];i>=0;i=edge[i].next)
    {
        int y=edge[i].to;
        if(vis[y]) continue;
        else dfs(y);
    }
}
```

​	During DFS, record the node index both when entering and when exiting the recursion. This yields a sequence of length $2n$, known as the **DFS order**. Each node $x$ appears exactly twice; denote these positions by $L[x]$ and $R[x]$. The interval $[L[x], R[x]]$ corresponds to the DFS order of the subtree rooted at $x$. Many tree-related problems convert subtree queries into interval queries using this property.

```cpp
void dfs(int x)
{
    vis[x]=true;
    order[cnt++]=x;
    for(int i=head[x];i>=0;i=edge[i].next)
    {
        int y=edge[i].to;
        if(vis[y]) continue;
        else dfs(y);
    }
    order[cnt++]=x;
}
```

​	To compute **Tree Depth**, assign depth $0$ to the root and propagate depth values top-down during DFS.

```cpp
void dfs(int x)
{
    vis[x]=true;
    for(int i=head[x];i>=0;i=edge[i].next)
    {
        int y=edge[i].to;
        if(vis[y]) continue;
        depth[y]=depth[x]+1;
        dfs(y);
    }
}
```

​	To compute **the Centroid of a Tree**, remove node $x$, producing several subtrees. Let $m(x)$ denote the size of the largest resulting component. The centroid is $\displaystyle p=\arg\min_{x\in T}m(x)$.

```cpp
void dfs(int x)
{
    vis[x]=true,size[x]=1;   // size of subtree rooted at x
    int max_part=0;
    for(int i=head[x];i>=0;i=edge[i].next)
    {
        int y=edge[i].to;
        if(vis[y]) continue;
        else dfs(y);
        size[x]+=size[y];
        max_part=max(max_part,size[y]);
    }
    max_part=max(max_part,n-size[x]);
    if(max_part<val)
    {
        pos=x;          // centroid
        val=max_part;   // corresponding max_part value
    }
}
```



### 3. Breadth-First Search

---

​	BFS ensures **Monotonicity**: nodes in layer $i$ are fully processed before any node in layer $i+1$. It also exhibits **Two-Segment Behavior**: at any moment, the queue contains nodes from at most two consecutive layers.

```cpp
void bfs()
{
    memset(d,0,sizeof(d));
    q.push(s),d[s]=1;
    while(!q.empty())
    {
        int x=q.front();q.pop();
        for(int i=head[x];i>=0;i=edge[i].next)
        {
            int y=edge[i].to;
            if(d[y]) continue;
            d[y]=d[x]+1;
            q.push(y);
        }
    }
}
// Time complexity O(n+m)
```

​	In a DAG, a **Topological Ordering** is a linear ordering such that for every directed edge $(x,y)$, $x$ appears before $y$. This can be computed using **Kahn’s Algorithm**. Precompute all indegrees `indegree[i]`, insert all zero-indegree nodes into a set, repeatedly remove any node $x$ from the set, delete all its outgoing edges, and insert new zero-indegree nodes. If the final sequence length is less than $n$, then **the graph contains a cycle**.

```cpp
bool topsort()
{
    for(int i=1;i<=n;i++)
    {
        if(indegree[i]==0) q.push(i);
    }
    while(!q.empty())
    {
        int x=q.front();q.pop();
        seq[cnt++]=x;
        for(int i=head[x];i>=0;i=edge[i].next)
        {
            int y=edge[i].to;
            if(--indegree[y]==0) q.push(y);
        }
    }
    return cnt==n;
}
```

​	To obtain the lexicographically maximum (or minimum) topological order, replace the queue with a max-heap (or min-heap).
