# 矩阵遍历问题

## 对角线遍历

### [498. 对角线遍历 🟡](https://leetcode.cn/problems/diagonal-traverse/description/)

题目要求：在一个 `m × n` 的矩阵中，以对角线的遍历顺序返回矩阵中的所有元素。

#### 模拟

根据当前对角线的奇偶性来决定遍历方向。

- 偶数对角线（右上到左下）：先向右上方移动，直到遇到矩阵的上边界或右边界，然后改变移动方向。

- 奇数对角线（左下到右上）：先向左下放移动，直到遇到矩阵的下边界或左边界，然后改变移动方向。

``` js
var findDiagonalOrder = function (matrix) {
  const rows = matrix.length;
  const cols = matrix[0].length;
  const res = [];
  let row = 0;
  let col = 0;

  while (res.length < rows * cols) {
    res.push(matrix[row][col]);

    if ((row + col) % 2 === 0) { // 偶数对角线，右上到左下
      if (col === cols - 1) {
        row++;
      } else if (row === 0) {
        col++;
      } else {
        col++;
        row--;
      }
    } else { // 奇数对角线，左下到右上
      if (row === rows - 1) {
        col++;
      } else if (col === 0) {
        row++;
      } else {
        col--;
        row++;
      }
    }
  }

  return res;
};
```

时间复杂度：O(mn)，空间复杂度：O(1)。

## 螺旋遍历

### [54. 螺旋矩阵 🟡 ❤](https://leetcode.cn/problems/spiral-matrix/description/)

题目要求：在一个 `m × n` 的矩阵中，以顺时针螺旋的遍历顺序返回矩阵中的所有元素。

#### 模拟

模拟螺旋遍历的过程，通过维护四个变量（上、下、左、右）来控制遍历方向，根据当前方向和边界情况来决定下一步的移动方向。

``` js
var spiralOrder = function (matrix) {
  const rows = matrix.length;
  const cols = matrix[0].length;
  const res = [];
  let top = 0;
  let bottom = rows - 1;
  let left = 0;
  let right = cols - 1;

  while (top <= bottom && left <= right) {
    // 从左到右
    for (let i = left; i <= right; i++) {
      res.push(matrix[top][i]);
    }
    top++;

    // 从上到下
    for (let i = top; i <= bottom; i++) {
      res.push(matrix[i][right]);
    }
    right--;

    // 从右到左
    if (top <= bottom) {
      for (let i = right; i >= left; i--) {
        res.push(matrix[bottom][i]);
      }
      bottom--;
    }

    // 从下到上
    if (left <= right) {
      for (let i = bottom; i >= top; i--) {
        res.push(matrix[i][left]);
      }
      left++;
    }
  }

  return res;
};
```

时间复杂度：O(mn)，空间复杂度：O(1)。

### [59. 螺旋矩阵 II 🟡](https://leetcode.cn/problems/spiral-matrix-ii/description/)

题目要求：生成一个 `n x n` 的矩阵，矩阵中的元素从 `1` 到 `n * n` 的螺旋顺序填充。

#### 模拟

首先创建一个二维矩阵，然后通过维护四个变量（上、下、左、右）来控制填充方向，根据当前方向和边界情况来决定下一步的填充方向，依次将 `1` 到 `n * n` 的数值填充到矩阵中。

``` js
var generateMatrix = function (n) {
  const matrix = Array.from({ length: n }, () => Array(n).fill(0));
  let left = 0;
  let right = n - 1;
  let top = 0;
  let bottom = n - 1;
  let num = 1;

  while (num <= n * n) {
    // 从左向右
    for (let i = left; i <= right; i++) {
      matrix[top][i] = num++;
    }
    top++;

    // 从上到下
    for (let i = top; i <= bottom; i++) {
      matrix[i][right] = num++;
    }
    right--;

    // 从右到左
    for (let i = right; i >= left; i--) {
      matrix[bottom][i] = num++;
    }
    bottom--;

    // 从下到上
    for (let i = bottom; i >= top; i--) {
      matrix[i][left] = num++;
    }
    left++;
  }

  return matrix;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

