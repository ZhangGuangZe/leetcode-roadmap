# 最大矩形问题

## [84. 柱状图中最大的矩形 🔴 ❤](https://leetcode.cn/problems/largest-rectangle-in-histogram/description/)

题目要求：在柱状图中，找出能够构成的最大矩形，计算并返回其面积。

### 1. 暴力法（超时）

枚举所有可能的矩形区域，每个区域的高度取决于高度最小的柱子，然后计数矩形的面积。

``` js
var largestRectangleArea = function (heights) {
  const n = heights.length;
  let maxArea = 0;

  for (let i = 0; i < n; i++) {
    let minHeight = Infinity;
    for (let j = i; j < n; j++) {
      minHeight = Math.min(minHeight, heights[j]);
      maxArea = Math.max(maxArea, minHeight * (j - i + 1));
    }
  }

  return maxArea;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 单调栈 👍

我们维护一个单调递增的栈，栈中存储柱子的下标。

遍历所有柱子，对于每根柱子，如果当前柱子的高度小于栈顶柱子的高度，则弹出栈顶柱子，并计算以该柱子为高度的最大矩形面积；否则将当前柱子的下标入栈。

``` js
var largestRectangleArea = function (heights) {
  const n = heights.length;
  const stack = [];
  let maxArea = 0;

  for (let i = 0; i <= n; i++) {
    // 右侧增加一个高度为0的柱子便于清空栈计算剩余柱子的面积
    const curHeight = i === n ? 0 : heights[i];

    while (stack.length > 0 && heights[stack.at(-1)] > curHeight) {
      const height = heights[stack.pop()];
      // 左侧增加一个高度为0的柱子防止栈为空
      const width = stack.length === 0 ? i : i - stack.at(-1) - 1;
      maxArea = Math.max(maxArea, height * width);
    }
    stack.push(i);
  }

  return maxArea;
};
```

我们可以在柱子两边增加一个高度为 `0` 的柱子作为哨兵，从而简化代码逻辑，避免额外的边界判断和处理。这样就不需要额外判断栈是否为空，也不需要单独处理遍历结束后栈中剩余的柱子。

``` js
var largestRectangleArea = function (heights) {
  heights = [0, ...heights, 0]; // 增加哨兵柱子
  const n = heights.length;
  const stack = [];
  let maxArea = 0;

  for (let i = 0; i < n; i++) {
    while (stack.length > 0 && heights[stack.at(-1)] > heights[i]) {
      const height = heights[stack.pop()];
      const width = i - stack.at(-1) - 1;
      maxArea = Math.max(maxArea, height * width);
    }
    stack.push(i);
  }

  return maxArea;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [85. 最大矩形 🔴 ❤](https://leetcode.cn/problems/maximal-rectangle/description/)

题目要求：在一个仅包含 `0` 和 `1` 的二维矩阵中，找出只包含 `1` 的最大矩形，计算并返回其面积。

### 1. 暴力法

枚举所有可能的矩形区域，然后检查每个区域的元素是否都为 `1`，并更新最大矩形面积。

遍历矩阵，对于每个单元格，尝试以当前单元格为左上角，向下和向右扩展，检查当前区域的元素是否都为 `1`，如果符合条件则更新最大矩形面积。

``` js
var maximalRectangle = function (matrix) {
  const rows = matrix.length;
  const cols = matrix[0].length;
  let maxArea = 0;

  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      if (matrix[row][col] === '0') continue;
      let maxWidth = cols;
      for (let i = row; i < rows; i++) {
        for (let j = col; j < maxWidth; j++) {
          if (matrix[i][j] === '0') {
            maxWidth = j;
            break;
          }
        }
        const width = maxWidth - col;
        const height = i - row + 1;
        maxArea = Math.max(maxArea, width * height);
      }
    }
  }

  return maxArea;
};
```

时间复杂度：O(m²n²)，空间复杂度：O(1)。

### 2. 单调栈 👍

我们可以将此问题转换为在多个柱状图中找到最大矩形面积的问题。遍历每一行，对于每一行的每个元素，如果当前元素为 `1`，则将当前列的高度加 1；如果当前元素为 `0`，则将当前列的高度重置为 0。

我们维护一个单调递增栈。如果当前行列的高度小于栈顶元素对应柱子的高度，则将当前行列的下标出栈，然后计算并更新以当前列为高的最大矩形面积；否则将当前列的下标入栈。

``` js
var maximalRectangle = function (matrix) {
  const rows = matrix.length;
  const cols = matrix[0].length;
  const heights = new Array(cols).fill(0);
  let maxArea = 0;

  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      heights[col] = matrix[row][col] === '1' ? heights[col] + 1 : 0;
    }
    maxArea = Math.max(maxArea, largestRectangleArea(heights));
  }

  return maxArea;
};

const largestRectangleArea = heights => {
  heights = [0, ...heights, 0];
  const stack = [];
  let maxArea = 0;

  for (let i = 0; i < heights.length; i++) {
    while (stack.length > 0 && heights[stack.at(-1)] > heights[i]) {
      const height = heights[stack.pop()];
      const width = i - stack.at(-1) - 1;
      maxArea = Math.max(maxArea, height * width);
    }
    stack.push(i);
  }

  return maxArea;
};
```

时间复杂度：O(mn)，空间复杂度：O(n)。