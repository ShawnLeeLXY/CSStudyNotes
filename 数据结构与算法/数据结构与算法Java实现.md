# 数据结构与算法Java实现

## 排序算法

### 插入排序

**插入排序**是一种**稳定排序**

基本思想：将一个元素插入一个有序数组中，使之成为更长的有序数组

```java
class Solution {
    public int[] sortArray(int[] nums) {
        insertionSort(nums);
        return nums;
    }

    private void insertionSort(int[] nums) {
        int n = nums.length;
        for (int i = 1; i < n; i++) {
            int temp = nums[i];
            int j = i;
            while (j > 0 && nums[j - 1] > temp) {
                nums[j] = nums[j - 1];
                j--;
            }
            nums[j] = temp;
        }
    }
}
```





### 快速排序

**快速排序**是一种**不稳定排序**

分治法（边分边治）

时间复杂度：平均 $O(NlogN)$，最坏 $O(N^2)$

空间复杂度：平均 $O(logN)$，最坏 $O(N)$

```java
class Solution {

    public int[] sortArray(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }

    private void quickSort(int[] nums, int left, int right) {
        if (left >= right) return;
        int pivot = nums[left];
        int lt = left;
        for (int i = left + 1; i <= right; i++) {
            if (nums[i] < pivot) swap(nums, ++lt, i);
        }
        swap(nums, left, lt);
        quickSort(nums, left, lt - 1);
        quickSort(nums, lt + 1, right);
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```



在数组已接近完全正序或倒序时，时间复杂度将退化成 $O(N^2)$，极端情况完全有序，递归树高为n，退化成了选择排序

最优的情况是每次选择基数pivot都是数组的中位数，这样可以使递归树高最小

优化方式：

- **随机选择切分元素**：每次partition的基准数选择随机一个数（或者直接选择数组中间的值），交换到数组开头

  如果数组中有大量重复元素，这种方式就失效了，这时可以采取以下其中一种优化方式：

  - **指针对撞**：将等于pivot的元素平均分到两边
  - **三向切分**：在遍历的过程中把待排序的部分分成三个区间，将等于pivot的元素集中到中间的区间

- 使用循坏结构代替左子数组的递归可以略微优化递归时的树高



采用指针对撞的快速排序：

```java
class Solution {

    private Random random;

    public int[] sortArray(int[] nums) {
        random = new Random();
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }

    private void quickSort(int[] nums, int left, int right) {
        if (left >= right) return;
        int ind = left + random.nextInt(right - left + 1);
        swap(nums, left, ind);
        int pivot = nums[left];
        int i = left + 1, j = right;
        while (i <= j) {
            while (i <= j && nums[i] < pivot) i++;
            while (i <= j && nums[j] > pivot) j--;
            if (i <= j) swap(nums, i++, j--);
        }
        swap(nums, left, j);
        quickSort(nums, left, j - 1);
        quickSort(nums, j + 1, right);
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```



采用三向切分的快速排序：

```java
class Solution {

    private Random random;

    public int[] sortArray(int[] nums) {
        random = new Random();
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }

    private void quickSort(int[] nums, int left, int right) {
        if (left >= right) return;
        int ind = left + random.nextInt(right - left + 1);
        swap(nums, left, ind);
        int pivot = nums[left];
        int lt = left, gt = right + 1, i = left + 1;
        while (i < gt) {
            if (nums[i] < pivot) swap(nums, ++lt, i++);
            else if (nums[i] == pivot) i++;
            else swap(nums, i, --gt);
        }
        swap(nums, left, lt);
        quickSort(nums, left, lt - 1);
        quickSort(nums, gt, right);
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```



快速排序为什么不稳定的：若数组中有和选择的pivot相等的数，且该数在pivot左侧，这时如果把大于等于pivot的数分到右侧，那么排序后该数就在pivot右侧了







## 并查集

并即合并union

查即查找find

通常使用**数组**作为数据结构的实现

一般有quick-find算法和quick-union算法



### quick-find算法

find：直接返回 $O(1)$

union: 需要遍历数组 $O(N)$​

quick-find实现代码：

```java
public class UnionFind {
    private int[] root;

    public UnionFind(int size) {
        root = new int[size];
        for (int i = 0; i < size; i++) {
            root[i] = i;
        }
    }

    public int find(int x) {
        return root[x];
    }

    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            for (int i = 0; i < root.length; i++) {
                if (root[i] == rootY) {
                    root[i] = rootX;
                }
            }
        }
    }

    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
}
```





### quick-union算法

find：$O(1)$​​​~$O(N)$​​ 或 $O(H)$​​ 这里的H是树的高度

union：$O(1)$​​​~$O(N)$​​ 或 $O(H)$​ 

quick-union实现代码：

```java
public class UnionFind {
    private int[] root;

    public UnionFind(int size) {
        root = new int[size];
        for (int i = 0; i < size; i++) {
            root[i] = i;
        }
    }

    public int find(int x) {
        while (x != root[x]) {
            x = root[x];
        }
        return x;
    }

    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            root[rootY] = rootX;
        }
    }

    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
}
```





### quick-union的按秩合并优化

按秩合并（weighted）优化：按某种规则优化union操作

比如在union操作时，使树高度更大的作为根节点

find：$O(logN)$​

union：$O(logN)$​​

按秩合并优化代码：

```java
public class UnionFind {
    private int[] root;
    private int[] rank;

    public UnionFind(int size) {
        root = new int[size];
        rank = new int[size];
        for (int i = 0; i < size; i++) {
            root[i] = i;
            rank[i] = 1;
        }
    }

    public int find(int x) {
        while (x != root[x]) {
            x = root[x];
        }
        return x;
    }

    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            if (rank[rootX] > rank[rootY]) {
                root[rootY] = rootX;
            } else if (rank[rootX] < rank[rootY]) {
                root[rootX] = rootY;
            } else {
                root[rootY] = rootX;
                rank[rootX] += 1;
            }
        }
    }

    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
}
```





### quick-union的路径压缩优化

路径压缩（path compression）优化：在find时将查找过的路径压缩到长度为1，即所有非根节点都与根节点直接相连

find：$O(\alpha(N))$~$O(logN)$​

union：$O(\alpha(N))$~$O(logN)$

路径压缩优化代码：

```java
public class UnionFind {
    private int[] root;

    public UnionFind(int size) {
        root = new int[size];
        for (int i = 0; i < size; i++) {
            root[i] = i;
        }
    }

    public int find(int x) {
        if (x == root[x]) {
            return x;
        }
        return root[x] = find(root[x]);
    }

    public void union(int x, int y) {
        int rootX = find(x);
        int rootY = find(y);
        if (rootX != rootY) {
            root[rootY] = rootX;
        }
    }

    public boolean connected(int x, int y) {
        return find(x) == find(y);
    }
}
```



并查集在各种情况下的时间复杂度：

| 优化                | 平均时间复杂度 | 最坏时间复杂度 |
| ------------------- | -------------- | -------------- |
| 无优化              | $O(logN)$      | $O(N)$         |
| 按秩合并            | $O(logN)$      | $O(logN)$      |
| 路径压缩            | $O(\alpha(N))$ | $O(logN)$      |
| 按秩合并 + 路径压缩 | $O(\alpha(N))$ | $O(\alpha(N))$ |

其中$\alpha(x)$为反阿克曼函数，在人类可观测的N内（宇宙中粒子总数），$\alpha(x)$不会超过5







## 最小生成树相关算法

基本概念：

**生成树**：无向图中，具有该图的**全部顶点**且**边数最少**的连通子图

**最小生成树**：加权无向图中**总权值最小**的生成树

**切分**：将图切为两个部分

**横切边**：如果一条边连接的两个顶点属于切分的两个部分，这个边称为**横切边**

**切分定理**：在一幅连通加权无向图中，给定任意的**切分**，如果有一条**横切边**的权值严格小于所有其他横切边，则这条边必然属于图的**最小生成树**中的一条边



### Kruskal算法

**Kruskal算法**是求解加权无向图的最小生成树的一种算法

步骤：

1. 所有边**从小到大**排序
2. 依次加入最小生成树中，**形成环则跳过**（可以用并查集判断是否成环）
3. 直到选择出 **N-1** 条边为止（顶点数为N）

用到了贪心的思想

时间复杂度：$O(ElogE)$

空间复杂度：$O(V)$



演示代码：

```java
/**
 * LeetCode第1584题 连接所有点的最小费用
 */
class Solution {

    class UnionFind {

        private int[] lead;
        private int[] height;

        public UnionFind(int n) {
            lead = new int[n];
            height = new int[n];
            for (int i = 0; i < n; i++) {
                lead[i] = i;
                height[i] = 1;
            }
        }

        public int find(int x) {
            if (x == lead[x]) return x;
            return find(lead[x]);
        }

        public void union(int x, int y) {
            int leadX = find(x);
            int leadY = find(y);
            if (height[leadX] > height[leadY]) {
                lead[leadY] = leadX;
            } else if (height[leadX] < height[leadY]) {
                lead[leadX] = leadY;
            } else {
                lead[leadY] = leadX;
                height[leadX]++;
            }
        }

        public boolean isConnected(int x, int y) {
            return find(x) == find(y);
        }

    }

    public int minCostConnectPoints(int[][] points) {
        int n = points.length;
        if (n <= 1) return 0;
        PriorityQueue<int[]> pq = new PriorityQueue<>((x, y) -> x[2] - y[2]);
        for (int i = 0; i < n; i++) {
            for (int j = i + 1; j < n; j++) {
                int cost = Math.abs(points[i][0] - points[j][0]) + Math.abs(points[i][1] - points[j][1]);
                pq.add(new int[]{i, j, cost});
            }
        }
        UnionFind unionFind = new UnionFind(n);
        int res = 0, cnt = 0;
        while (cnt < n - 1) {
            int[] edge = pq.poll();
            if (!unionFind.isConnected(edge[0], edge[1])) {
                unionFind.union(edge[0], edge[1]);
                res += edge[2];
                cnt++;
            }
        }
        return res;
    }

}
```





### Prim算法

**Prim算法**是求解加权无向图的最小生成树的另一种算法

步骤：

1. 创建一个数组visied，**已访问过**的顶点位置为true，**未访问过**的顶点位置为false，再随机选取一个顶点使其在visited中对应位置为true
2. 将visited的所有顶点看成一个**整体**，选取所有与其相连的边中**权值最小**的一条边，将该边的另一个顶点在visited中对应的位置设为true
3. 重复步骤3，直到visited中为true的元素数量为 **N**（或连接次数为N-1）

用到了贪心的思想和切分定理

时间复杂度：

- 普通二叉堆：$O(ElogV)$
- 斐波那契堆：$O(E+VlogV)$

空间复杂度：$O(V)$



Kruskal算法和Prim算法的区别：

- Kruskal算法是通过增加边数来扩大最小生成树
- Prim算法是通过增加顶点来扩大最小生成树



代码演示：

```java
/**
 * LeetCode第1584题 连接所有点的最小费用
 */
class Solution {

    public int minCostConnectPoints(int[][] points) {
        int n = points.length;
        if (n <= 1) return 0;
        PriorityQueue<int[]> pq = new PriorityQueue<>((x, y) -> x[2] - y[2]);
        boolean[] visited = new boolean[n];
        visited[0] = true;
        for (int i = 1; i < n; i++) {
            int cost = Math.abs(points[0][0] - points[i][0]) + Math.abs(points[0][1] - points[i][1]);
            pq.add(new int[]{0, i, cost});
        }
        int res = 0, cnt = 0;
        while (cnt < n - 1) {
            int[] edge = pq.poll();
            if (!visited[edge[1]]) {
                visited[edge[1]] = true;
                res += edge[2];
                for (int i = 0; i < n; i++) {
                    if (visited[i]) continue;
                    int cost = Math.abs(points[edge[1]][0] - points[i][0]) + Math.abs(points[edge[1]][1] - points[i][1]);
                    pq.add(new int[]{edge[1], i, cost});
                }
                cnt++;
            }
        }
        return res;
    }

}
```







## 单源最短路径相关算法

**单源最短路径**：在加权图中，给定一个起点，求出它分别到其他顶点的最短路径

**松弛操作**：用起点到节点A的最短路长度加上从节点A到节点B的边的长度，去比较起点到节点B的最短路长度，如果前者小于后者，就用前者更新后者



### Dijkstra算法

**Dijkstra算法**解决的是加权有向图**单源最短路径**问题，其中该图的所有权值必须为**非负数**

步骤：

1. 将所有顶点分成两类：**已确定**从起点到当前点的最短路长度的顶点，以及**未确定**从起点到当前点的最短路长度的顶点
2. 每次从**未确定顶点**中取一个与起点**距离最短的点**，将它归类为**已确定顶点**，并用它对从起点到其他所有**未确定顶点**的做**松弛操作**
3. 重复步骤2直到所有点都被归类为**已确定顶点**

用了贪心思想

时间复杂度：斐波那契最小堆 $O(E+VlogV)$

空间复杂度：$O(V)$



代码演示：

```java
/**
 * LeetCode第743题 网络延迟时间
 */
class Solution {

    public int networkDelayTime(int[][] times, int n, int k) {
        final int INF = Integer.MAX_VALUE / 2;
        int[][] graph = new int[n][n];
        for (int i = 0; i < n; i++) {
            Arrays.fill(graph[i], INF);
        }
        for (int[] time : times) {
            graph[time[0] - 1][time[1] - 1] = time[2];
        }
        // dist用于记录与起点的距离
        int[] dist = new int[n];
        Arrays.fill(dist, INF);
        dist[k - 1] = 0;
        boolean[] certain = new boolean[n];
        for (int i = 0; i < n; i++) {
            // 从未确定顶点中找距离起点最短的点
            int x = -1;
            for (int j = 0; j < n; j++) {
                if (!certain[j] && (x == -1 || dist[j] < dist[x])) {
                    x = j;
                }
            }
            // 将该点归类为已确定
            certain[x] = true;
            // 松弛操作
            for (int j = 0; j < n; j++) {
                dist[j] = Math.min(dist[j], dist[x] + graph[x][j]);
            }
        }
        int maxTime = Arrays.stream(dist).max().getAsInt();
        return maxTime == INF ? -1 : maxTime;
    }

}
```





### Bellman-Ford算法

Bellman-Ford算法可用于解决加权有向图**单源最短路径**问题，其中该图的所有权值**可正可负**，且能检测该图是否存在**负权环**

负权环图：所有边的权值加起来为负数的环图



定理一：在一个有 **N** 个顶点的**非负权环图**中，两点之间的最短路径最多经过 **N-1** 条边

定理二：**负权环图**没有最短路径



Bellman-Ford算法主要思想：对一个无负权环的图，利用**动态规划**对所有边进行 N-1 次的松弛操作之后，就可以得到一个起点到所有其他顶点的最短距离

使用二维数组存储到达u点最多经过k条边的最短路径

状态转移方程：$dp[k][u]=min(dp[k][u],dp[k-1][v]+w(u,v))$

二维数组可以用**滚动数组**优化为：

- 两个数组，一个保存上次计算结果，一个保存本次计算结果
- 一个数组，循环N-1次退出（可能无法给出准确的k条边的最短路径，k < n）

在进行第N次松弛操作后，如果对于一条边edge(u, v)还存在 $dp[u] + w(u, v) < dp[v]$ 的情况，说明还存在更短的路径，也就说明图中存在**负权环**

时间复杂度：$O(VE)$

空间复杂度：$O(V)$



Dijkstra算法和Bellman-Ford算法的相同点：

- 都可用于求非负权值图的单源最短路径
- 都通过循环不断地做松弛操作来更新数组

不同点：

- Dijkstra算法不能求非负权值图的单源最短路径
- Dijkstra算法通过对距离排序，每次获取距离最短的点来做松弛操作
- Bellman-Ford算法通过动态规划依次遍历边来做松弛操作



代码演示：

```java
/**
 * LeetCode第787题 K站中转内最便宜的航班
 */
class Solution {

    private static final int INF = Integer.MAX_VALUE / 2;

    public int findCheapestPrice(int n, int[][] flights, int src, int dst, int k) {
        int[] preDp = new int[n];
        int[] curDp = new int[n];
        Arrays.fill(preDp, INF);
        Arrays.fill(curDp, INF);
        preDp[src] = 0;
        for (int i = 1; i <= k + 1; i++) {
            curDp[src] = 0;
            for (int[] flight : flights) {
                curDp[flight[1]] = Math.min(curDp[flight[1]], preDp[flight[0]] + flight[2]);
            }
            preDp = curDp.clone();
        }
        return curDp[dst] == INF ? -1 : curDp[dst];
    }

}
```



Bellman-Ford算法的缺陷：不同的遍历边的顺序计算效率不同



#### SPFA算法

SPFA（Shortest Path Faster Algorithm）算法是对Bellman-Ford算法的优化，弥补了不同的遍历边的顺序计算效率不同的缺陷

SPFA算法主要是通过**队列**来维护接下来要遍历边的起点，每次只有当某个顶点的最短距离更新之后，并且该顶点不在队列中，那么就将该顶点加入到队列中。一直循环以上步骤，直到**队列为空**，我们就可以终止算法。此时，就得到了图中其他顶点到给定顶点的最短距离







## 拓扑排序

**拓扑排序**使用于解决**有向无环图**的一种算法，它是对图中所有顶点按照先后顺序的一种线性排序

拓扑排序通常可以用**深度优先搜索**或**Khan算法**实现



### Khan算法

拓扑排序通常需要借助**队列**存储入度为0的元素

步骤：

1. 遍历所有顶点，找到第一个入度为0的顶点，将其加入队列
2. 队首元素出队，标记为已访问，将该顶点指向的所有顶点入度-1，将其中入度变为0的顶点加入队列
3. 重复步骤2直到所有元素都已被访问过

如果最后不存在入度为0的顶点，那就说明有环，不存在拓扑排序

时间复杂度：$O(V+E)$

空间复杂度：$O(V+E)$



代码演示：

```java
/**
 * LeetCode第210题 课程表 II
 */
class Solution {

    public int[] findOrder(int numCourses, int[][] prerequisites) {
        if (numCourses == 0) return new int[0];
        if (prerequisites == null || prerequisites.length == 0) {
            int[] order = new int[numCourses];
            for (int i = 0; i < numCourses; i++) {
                order[i] = i;
            }
            return order;
        }
        int[] inDegree = new int[numCourses];
        for (int[] pre : prerequisites) {
            inDegree[pre[0]]++;
        }
        Queue<Integer> queue = new LinkedList<>();
        for (int i = 0; i < numCourses; i++) {
            if (inDegree[i] == 0) {
                queue.offer(i);
            }
        }
        int ind = 0;
        int[] res = new int[numCourses];
        while (!queue.isEmpty()) {
            int x = queue.poll();
            res[ind++] = x;
            for (int[] pre : prerequisites) {
                if (pre[1] == x) {
                    if ((--inDegree[pre[0]]) == 0) {
                        queue.offer(pre[0]);
                    }
                }
            }
        }
        if (ind != numCourses) return new int[0];
        return res;
    }

}
```

