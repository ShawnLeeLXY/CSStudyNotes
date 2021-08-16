# 数据结构与算法Java实现

## 排序算法

### 快速排序

挖坑填数+分治法（边分边治）

```java
class Solution {
    public int[] sortArray(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }

    private void quickSort(int[] nums, int left, int right) {
        if (left >= right) return;
        int i = left, j = right, pivot = nums[i];
        while (i < j) {
            while (i < j && nums[j] > pivot) j--;
            nums[i] = nums[j];
            while (i < j && nums[i] <= pivot) i++;
            nums[j] = nums[i];
        }
        nums[i] = pivot;
        quickSort(nums, left, i - 1);
        quickSort(nums, i + 1, right);
    }
}
```



优化：基准数选择数组中间的值，使用循坏结构代替左子数组的递归，将等于pivot的元素平均分到两边

```java
class Solution {
    public int[] sortArray(int[] nums) {
        quickSort(nums, 0, nums.length - 1);
        return nums;
    }

    private void quickSort(int[] nums, int left, int right) {
        while (left < right) {
            int i = left, j = right, pivot = nums[i + (j - i) / 2];
            while (i <= j) {
                while (i <= j && nums[i] < pivot) i++;
                while (i <= j && nums[j] > pivot) j--;
                if (i <= j) swap(nums, i++, j--);
            }
            quickSort(nums, i, right);
            right = j;
        }
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
}
```







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

find：$O(logN)$​

union：$O(logN)$

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

