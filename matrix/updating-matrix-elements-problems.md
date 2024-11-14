# 更新矩阵元素问题

## [73. 矩阵置零 🟡 ❤](https://leetcode.cn/problems/set-matrix-zeroes/description/)

题目要求：在一个 `m x n` 的矩阵中，**原地**将元素为 `0` 所在行和列的所有元素置为 `0`。

### 1. 额外空间-矩阵（不符合题目要求）

``` js
var setZeroes = function (matrix) {
  const rows = matrix.length;
  const cols = matrix[0].length;
  const copyMatrix = Array.from({ length: rows }, () => Array(cols).fill(0));

  // 拷贝矩阵
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      copyMatrix[row][col] = matrix[row][col];
    }
  }

  // 将元素为零的所在行所在列置零
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      if (matrix[row][col] === 0) {
        // 置零所在行
        for (let row = 0; row < rows; row++) {
          copyMatrix[row][col] = 0;
        }
        // 置零所在列
        for (let col = 0; col < cols; col++) {
          copyMatrix[row][col] = 0;
        }
      }
    }
  }

  // 还原矩阵
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      matrix[row][col] = copyMatrix[row][col];
    }
  }
};
```

时间复杂度：O(mn)，空间复杂度：O(mn)。

### 2. 额外空间-两个数组（不符合题目要求）

为了优化空间复杂度，使用两个数组分别记录矩阵中的哪些行和哪些列需要被置零。然后再遍历矩阵，根据两个数组中的信息更新原矩阵。

``` js
var setZeroes = function (matrix) {
  const rows = matrix.length;
  const cols = matrix[0].length;
  const rowZeros = new Array(rows).fill(false);
  const colZeros = new Array(cols).fill(false);

  // 使用两个数组标记需要置零的行和列
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      if (matrix[row][col] === 0) {
        rowZeros[row] = true;
        colZeros[col] = true;
      }
    }
  }

  // 根据标记对行和列进行置零
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      if (rowZeros[row] || colZeros[col]) {
        matrix[row][col] = 0;
      }
    }
  }
};
```

时间复杂度：O(mn)，空间复杂度：O(m + n)。

### 3. 原地标记 👍

直接使用矩阵的第一行和第一列来标记，为了防止第一行和第一列的值因为做标记而被覆盖，需要使用两个变量标记第一行和第一列是否包含零。

``` js
var setZeroes = function (matrix) {
  const m = matrix.length;
  const n = matrix[0].length;
  let isFirstRowZero = false;
  let isFirstColZero = false;

  // 检查第一行是否包含 0
  for (let j = 0; j < n; j++) {
    if (matrix[0][j] === 0) {
      isFirstRowZero = true;
      break;
    }
  }

  // 检查第一列是否包含 0
  for (let i = 0; i < m; i++) {
    if (matrix[i][0] === 0) {
      isFirstColZero = true;
      break;
    }
  }

  // 使用第一行和第一列作为标记
  for (let i = 1; i < m; i++) {
    for (let j = 1; j < n; j++) {
      if (matrix[i][j] === 0) {
        matrix[0][j] = 0;
        matrix[i][0] = 0;
      }
    }
  }

  // 根据标记对矩阵置零
  for (let i = 1; i < m; i++) {
    for (let j = 1; j < n; j++) {
      if (matrix[0][j] === 0 || matrix[i][0] === 0) {
        matrix[i][j] = 0;
      }
    }
  }

  // 如果第一行包含 0，则置零
  if (isFirstRowZero) {
    for (let j = 0; j < n; j++) {
      matrix[0][j] = 0;
    }
  }

  // 如果第一列包含 0，则置零
  if (isFirstColZero) {
    for (let i = 0; i < m; i++) {
      matrix[i][0] = 0;
    }
  }
};
```

时间复杂度：O(mn)，空间复杂度：O(1)。

## [289. 生命游戏 🟡](https://leetcode.cn/problems/game-of-life/description/)

题目要求：在一个 `m × n` 的矩阵中，按照生命游戏的规则将所有细胞当前状态同步更新到下一个状态。

### 1. 额外空间 👍

使用一个新的矩阵来保存所有细胞的原始状态，方向数组 `directions` 来遍历细胞周围八个方向的细胞，根据原始状态在矩阵中同步更新所有细胞的下一个状态。


``` js
var gameOfLife = function (board) {
  const directions = [[-1, -1], [-1, 0], [-1, 1], [0, 1], [1, 1], [1, 0], [1, -1], [0, -1]]; // ↖ ↑ ↗ → ↘ ↓ ↙ ←
  const rows = board.length;
  const cols = board[0].length;
  const copyBoard = Array.from({ length: rows }, () => Array(cols).fill(0));

  // 拷贝原始状态
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      copyBoard[row][col] = board[row][col];
    }
  }

  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      let liveCount = 0;

      for (const [dx, dy] of directions) {
        const newRow = row + dx;
        const newCol = col + dy;

        if (
          (newRow >= 0 && newRow < rows) &&
          (newCol >= 0 && newCol < cols) &&
          copyBoard[newRow][newCol] === 1
        ) {
          liveCount++;
        }
      }

      if (copyBoard[row][col] === 1 && (liveCount < 2 || liveCount > 3)) { // 规则 1、3
        board[row][col] = 0;
      } else if (copyBoard[row][col] === 0 && liveCount === 3) { // 规则 4
        board[row][col] = 1;
      }
    }
  }
};
```

时间复杂度：O(mn)，空间复杂度：O(mn)。

### 2. 原地标记 👍

利用两种特殊的状态来标记细胞的变化，从而在不使用额外空间的情况下进行原地修改。

活细胞变死细胞使用 `-1` 表示，死细胞变成活细胞使用 `2` 表示。

在标记完成后，可以再次遍历矩阵，将所有标记的状态更新到最终结果中。

``` js
var gameOfLife = function (board) {
  const directions = [[-1, -1], [-1, 0], [-1, 1], [0, 1], [1, 1], [1, 0], [1, -1], [0, -1]]; // ↖ ↑ ↗ → ↘ ↓ ↙ ←
  const rows = board.length;
  const cols = board[0].length;

  // 标记状态
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      let liveCount = 0;

      for (const [dx, dy] of directions) {
        const newRow = row + dx;
        const newCol = col + dy;

        if (
          (newRow >= 0 && newRow < rows) &&
          (newCol >= 0 && newCol < cols) &&
          Math.abs(board[newRow][newCol]) === 1
        ) {
          liveCount++;
        }
      }

      if (board[row][col] === 1 && (liveCount < 2 || liveCount > 3)) { // 规则 1、3
        board[row][col] = -1; // 过去活现在死
      } else if (board[row][col] === 0 && liveCount === 3) { // 规则 4
        board[row][col] = 2; // 过去死现在或
      }
    }
  }

  // 还原状态
  for (let row = 0; row < rows; row++) {
    for (let col = 0; col < cols; col++) {
      if (board[row][col] === -1) {
        board[row][col] = 0;
      } else if (board[row][col] === 2) {
        board[row][col] = 1;
      }
    }
  }
};
```

时间复杂度：O(mn)，空间复杂度：O(1)。