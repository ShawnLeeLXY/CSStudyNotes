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

