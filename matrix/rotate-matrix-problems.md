# 旋转矩阵问题

## [48. 旋转图像 🟡 ❤](https://leetcode.cn/problems/rotate-image/description/)

题目要求：将一个 `n x n` 的二维矩阵顺时针**原地**旋转 90 度。

### 1. 非原地旋转（不符合题意）

通过示例可以发现，对于矩阵中第 i 行的第 j 个元素，在旋转后，它出现在倒数第 i 列的第 j 个位置。

我们使用一个新矩阵来存储旋转后的矩阵。遍历原矩阵，依次将每个元素放置在旋转后的位置。将新矩阵中的值复制回原矩阵中。

``` js
var rotate = function (matrix) {
  const n = matrix.length;
  const rotatedMatrix = Array.from({ length: n }, () => Array(n).fill(0));

  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      rotatedMatrix[j][n - i - 1] = matrix[i][j];
    }
  }

  for (let i = 0; i < n; i++) {
    for (let j = 0; j < n; j++) {
      matrix[i][j] = rotatedMatrix[i][j];
    }
  }
};
```

时间复杂度：O(n²)，空间复杂度：O(n²)。

### 2. 逐层旋转 👍

逐层旋转矩阵，从外向内旋转，每次原地交换四个对角元素。

``` js
var rotate = function (matrix) {
  const n = matrix.length;

  for (let i = 0; i < Math.floor(n / 2); i++) {
    for (let j = i; j < n - i - 1; j++) {
      const temp = matrix[i][j];
      matrix[i][j] = matrix[n - j - 1][i];
      matrix[n - j - 1][i] = matrix[n - i - 1][n - j - 1];
      matrix[n - i - 1][n - j - 1] = matrix[j][n - i - 1];
      matrix[j][n - i - 1] = temp;
    }
  }
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 3. 两次翻转 👍

先对矩阵进行水平翻转，再对角线翻转。

``` js
var rotate = function (matrix) {
  const n = matrix.length;

  // 水平翻转
  for (let i = 0; i < Math.floor(n / 2); i++) {
    for (let j = 0; j < n; j++) {
      [matrix[i][j], matrix[n - i - 1][j]] = [matrix[n - i - 1][j], matrix[i][j]];
    }
  }

  // 对角线翻转
  for (let i = 0; i < n; i++) {
    for (let j = 0; j < i; j++) {
      [matrix[i][j], matrix[j][i]] = [matrix[j][i], matrix[i][j]];
    }
  }
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。
