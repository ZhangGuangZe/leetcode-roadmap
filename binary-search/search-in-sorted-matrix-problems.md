# 二维矩阵搜索问题

## [378. 有序矩阵中第 K 小的元素 🟡](https://leetcode.cn/problems/kth-smallest-element-in-a-sorted-matrix/description/)

题目要求：在一个每行和每列都升序排序的矩阵中，找到排序后第 `k` 小的元素。实现的算法时间复杂度必须优于 `O(n²)` 。

### 1. 暴力法（不符合时间复杂度要求）

将矩阵中的所有元素收集到一个数组中，然后对该数组进行排序，最后返回第 `k` 小的元素

``` js
var kthSmallest = function (matrix, k) {
  /* const n = matrix.length;
  const nums = [];

  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      nums.push(matrix[i][j]);
    }
  }

  nums.sort((a, b) => a - b);
  return nums[k - 1]; */

  return matrix.flat().sort((a, b) => a - b)[k - 1];
};
```

时间复杂度：O(n²logn)，空间复杂度：O(n²)。

### 2. 二分查找 👍

根据矩阵行列有序的特点，我们可以得出，最小元素为 `matrix[0][0]`，最大元素为 `matrix[n-1][n-1]`，所以矩阵中的元素是从左上到右下递增的，那么 `k` 就在左上到右下之间。

我们根据数值范围进行二分查找。

首先从矩阵的最小元素和最大元素中计算一个中间值 `mid`。然后统计矩阵中小于或等于 `mid` 的元素个数 `count`。如果 `count < k`，则第 `k` 小的元素大于 `mid`，调整左边界；否则调整右边界。

统计个数时从左下角开始，根据当前值与 `mid` 的关系向上或向右移动。

``` js
var kthSmallest = function (matrix, k) {
  const n = matrix.length;
  let lo = matrix[0][0];
  let hi = matrix[n - 1][n - 1];

  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);
    const count = countLessEqual(matrix, mid, n);
    if (count < k) {
      lo = mid + 1;
    } else {
      hi = mid;
    }
  }

  return lo;
};

const countLessEqual = (matrix, mid, n) => {
  let row = n - 1;
  let col = 0;
  let count = 0;

  while (row >= 0 && col < n) {
    if (matrix[row][col] <= mid) {
      count += row + 1;
      col++;
    } else {
      row--;
    }
  }

  return count;
};

```

时间复杂度：O(nlogn)，空间复杂度：O(1)。
