# 矩阵搜索问题

## [74. 搜索二维矩阵 🟡](https://leetcode.cn/problems/search-a-2d-matrix/description/)

题目要求：在一个每行从左到右递增排列，并且每行第一个元素大于前一行最后一个元素的 `m × n` 二维矩阵中搜索目标值 `target`，如果 `target` 在矩阵中，返回 `true`，否则返回 `false`。

### 1. 暴力法

遍历矩阵的每个元素，直到找到目标值或者遍历完所有元素为止。

``` js
var searchMatrix = function (matrix, target) {
  const rows = matrix.length;
  const cols = matrix[0].length;

  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      if (matrix[row][col] === target) return true;
    }
  }

  return false;
};
```

时间复杂度：O(mn)，空间复杂度：O(1)。

### 2. 线性查找 👍

由于矩阵是已排序的，我们可以从右上角或者左上角开始搜索，如果当前元素小于目标值，则向下移动一行；如果当前元素大于目标值，则向左移动一列。逐步缩小搜索范围。

``` js
var searchMatrix = function (matrix, target) {
  const rows = matrix.length;
  const cols = matrix[0].length;
  let row = 0;
  let col = cols - 1;

  while (row < rows && col >= 0) {
    if (matrix[row][col] === target) return true;
    matrix[row][col] < target ? row++ : col--;
  }

  return false;
};
```

时间复杂度：O(m + n)，空间复杂度：O(1)。

### 3. 二分查找 👍

由于矩阵是已排序的，我们可以将矩阵视为一维数组，然后使用二分查找搜索目标值。

``` js
var searchMatrix = function (matrix, target) {
  const rows = matrix.length;
  const cols = matrix[0].length;
  let lo = 0;
  let hi = rows * cols - 1;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);
    const midVal = matrix[Math.floor(mid / cols)][mid % cols];

    if (midVal === target) return true;
    if (midVal < target) lo = mid + 1;
    else hi = mid - 1;
  }

  return false;
};
```

时间复杂度：O(log(mn))，空间复杂度：O(1)。

## [240. 搜索二维矩阵 II 🟡 ❤](https://leetcode.cn/problems/search-a-2d-matrix-ii/description/)

题目要求：在一个每行从左到右、每一列从上到下递增排列的 `m × n` 矩阵中搜索目标值 `target`。如果 `target` 在矩阵中，返回 `true`，否则返回 `false`。

### 1. 暴力法（超时）

暴力法的思路与代码与上一题相同，这里就不在赘述。

### 2. 二分查找 👍

由于矩阵的每一行是递增的，可以对每一行单独进行二分查找，以确定目标值是否存在。

``` js
var searchMatrix = function (matrix, target) {
  const rows = matrix.length;
  const cols = matrix[0].length;

  for (const row of matrix) {
    if (row[cols - 1] < target) continue;
    if (row[0] > target) break;
    if (binarySearch(row, target) >= 0) return true;
  }

  return false;
};

const binarySearch = (nums, target) => {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (nums[mid] === target) return mid;
    if (nums[mid] < target) lo = mid + 1;
    else hi = mid - 1;
  }

  return -1;
};
```

时间复杂度：O(mlogn)，空间复杂度：O(1)。

### 3. 线性查找 👍

由于矩阵的每一行和每一列都是递增的，我们可以从右上角或左下角开始搜索，如果当前元素小于目标值，则向下移动一行；如果当前元素大于目标值，则向左移动一列。逐步缩小搜索范围。

``` js
var searchMatrix = function (matrix, target) {
  const rows = matrix.length;
  const cols = matrix[0].length;
  let row = 0;
  let col = cols - 1;

  while (row < rows && col >= 0) {
    if (matrix[row][col] === target) return true;
    matrix[row][col] < target ? row++ : col--;
  }

  return false;
};
```

时间复杂度：O(m + n)，空间复杂度：O(1)。
