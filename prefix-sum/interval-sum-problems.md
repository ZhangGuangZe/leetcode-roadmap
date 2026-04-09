# 区间和问题

## [303. 区域和检索 - 数组不可变 🟢 ❤](https://leetcode.cn/problems/range-sum-query-immutable/description/)

题目要求：设计一个类，用于存储整数数组，并实现 `sumRange()`，计算数组下标 `left` 到 `right` 的元素和（包含两端）。数组不可变，`sumRange()` 会被多次调用。

### 1. 暴力法

每次通过循环累加计算范围内的元素和。

``` js
var NumArray = function (nums) {
  this.nums = nums;
};

NumArray.prototype.sumRange = function (left, right) {
  let sum = 0;

  for (let i = left; i <= right; i++) {
    sum += this.nums[i];
  }

  return sum;
};
```

时间复杂度：初始化 `O(1)`，`sumRange()` `O(n)`，空间复杂度：`O(1)`。

### 2. 前缀和 👍

由于暴力法 `sumRange()`  每次检索的时间复杂度为 `O(n)`，我们可以使用前缀和将其优化到 `O(1)`。
初始化时计算前缀和数组 `prefixSum`，`prefixSum[i]` 表示前 `i` 个元素的和。则区间和可通过 `prefixSum[right+1] - prefixSum[left]` 快速得出。

``` js
var NumArray = function (nums) {
  const n = nums.length;
  this.prefixSum = new Array(n + 1).fill(0);

  for (let i = 0; i <= n; i++) {
    this.prefixSum[i + 1] = this.prefixSum[i] + nums[i];
  }
};

NumArray.prototype.sumRange = function (left, right) {
  return this.prefixSum[right + 1] - this.prefixSum[left];
};
```

时间复杂度：初始化 `O(n)`，`sumRange()` `O(1)`，空间复杂度：`O(n)`。

## [304. 二维区域和检索 - 矩阵不可变 🟡 ❤](https://leetcode.cn/problems/range-sum-query-2d-immutable/description/)

题目要求：设计一个类，初始化时接收一个二维整数矩阵 `matrix`，实现 `sumRegion()`，计算矩阵中左上角到右上角所围成的子矩阵的元素和（包含边界）。矩阵不可变，`sumRegion` 会被多次调用。

### 1. 暴力法（超时）

每次调用 `sumRegion()` 时，遍历子矩阵的每一行每一列，累加所有元素。

``` js
var NumMatrix = function (matrix) {
  this.matrix = matrix;
};

NumMatrix.prototype.sumRegion = function (row1, col1, row2, col2) {
  let sum = 0;

  for (let row = row1; row <= row2; row++) {
    for (let col = col1; col <= col2; col++) {
      sum += this.matrix[row][col];
    }
  }

  return sum;
};
```

时间复杂度：初始化 `O(1)`，`sumRegion()` `O(mn)` ，空间复杂度：`O(mn)`。

### 2. 前缀和 👍

初始化时计算二维前缀和数组 `prefixSum`，`prefixSum[i + 1][j + 1]` 表示从第 `i` 行第 `j` 列所有元素之和。

前缀和公式为：`prefixSum[i + 1][j + 1] = prefixSum[i][j + 1] + prefixSum[i + 1][j] - prefixSum[i][j] + matrix[i][j]`。

子矩阵求和公式为：`sumRegion = prefixSum[row2 + 1][col2 + 1] - prefixSum[row1][col2 + 1] - prefixSum[row2 + 1][col1] + prefixSum[row1][col1]`

``` js
var NumMatrix = function (matrix) {
  const m = matrix.length;
  const n = matrix[0].length;
  this.prefixSum = Array.from({ length: m + 1}, () => new Array(n + 1).fill(0));

  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      // 前缀和 = (A + B) + （A + C) - A + D
      this.prefixSum[i + 1][j + 1] = this.prefixSum[i][j + 1] + this.prefixSum[i + 1][j] - this.prefixSum[i][j] + matrix[i][j];
    }
  }
};

NumMatrix.prototype.sumRegion = function (row1, col1, row2, col2) {
  // 区间和 = 右下角子区间和 - 上方矩形 - 左方矩形 + 左上角重复减去部分
  return this.prefixSum[row2 + 1][col2 + 1] - this.prefixSum[row1][col2 + 1] - this.prefixSum[row2 + 1][col1] + this.prefixSum[row1][col1];
};
```

时间复杂度：初始化 `O(mn)`，`sumRegion()` `O(1)`，空间复杂度：`O(mn)`。

## [724. 寻找数组的中心下标 🟢 ❤](https://leetcode.cn/problems/find-pivot-index/description/)

题目要求：在整数数组中找到中心下标，使得左侧所有元素之和等于右侧所有元素之和。如果不存在则返回 `-1`，存在多个则返回最左边的一个。

### 1. 暴力法

枚举所有可能的中心索引，计算左右两侧所有元素之和，比较是否相等。

``` js
var pivotIndex = function(nums) {
  const n = nums.length;

  for (let i = 0; i < n; i++) {
    let lSum = 0, rSum = 0;

    for (let j = 0; j < i; j++) {
      lSum += nums[j];
    }
    for (let k = i + 1; k < n; k++) {
      rSum += nums[k];
    }

    if (lSum === rSum) return i;
  }

  return -1;
};
```

时间复杂度：`O(n²)`，空间复杂度：`O(1)`。

### 2. 前缀和 👍

我们维护一个前缀和数组 `prefixSum`，`prefixSum[i]` 表示前 i 个元素之和。

遍历每个下标，利用前缀和快速计算左右侧和，对于每个下标 `i`：
- 左侧和为 `prefixSum[i]`；
- 右侧和为 `prefixSum[n] - prefixSum[i + 1]`。

``` js
var pivotIndex = function (nums) {
  const n = nums.length;
  const prefixSum = new Array(n + 1).fill(0);

  for (let i = 0; i < n; i++) {
    prefixSum[i + 1] = prefixSum[i] + nums[i];
  }

  for (let i = 0; i < n; i++) {
    const lSum = prefixSum[i];
    const rSum = prefixSum[n] - prefixSum[i + 1];

    if (lSum === rSum) return i;
  }

  return -1;
};
```

最后，我们可以使用两个滚动变量来计算左右两侧前缀和，从而将空间复杂度优化到 `O(1)`。

``` js
var pivotIndex = function (nums) {
  const n = nums.length;
  let lSum = 0;
  let rSum = nums.reduce((a, b) => a + b, 0);

  for (let i = 0; i < n; i++) {
    rSum -= nums[i];
    if (lSum === rSum) return i;
    lSum += nums[i];
  }

  return -1;
};
```

时间复杂度：`O(n)`，空间复杂度：`O(1)`。

## [238. 除自身以外数组的乘积 🟡 ❤](https://leetcode.cn/problems/product-of-array-except-self/description/)

题目要求：给定一个整数数组 `nums`，返回一个数组 `answer`，其中 `answer[i]` 等于数组中除 `nums[i]` 之外所有元素的乘积。

不能使用除法且实现的算法时间复杂度的 `O(n)`。

### 1. 暴力法（不符合题目要求）

对于每个元素，遍历数组计算其他所有元素的乘积。

``` js
var productExceptSelf = function (nums) {
  const n = nums.length;
  const answer = new Array(n);

  for (let i = 0; i < n; i++) {
    let product = 1;

    for (let j = 0; j < n; j++) {
      if (i !== j) product *= nums[j];
    }

    answer[i] = product;
  }

  return answer;
};
```

时间复杂度：`O(n²)`，空间复杂度：`O(1)`。

### 2. 左右乘积数组

维护两个数组 `left` 和 `right`，分别表示当前元素左侧所有元素的乘积和右侧所有元素的乘积，对于当前元素，`answer[i] = left[i] * right[i]`。

``` js
var productExceptSelf = function (nums) {
  const n = nums.length;
  const answer = new Array(n);
  const leftProduct = new Array(n).fill(1);
  const rightProduct = new Array(n).fill(1);

  for (let i = 1; i < n; i++) {
    leftProduct[i] = leftProduct[i - 1] * nums[i - 1];
  }

  for (let i = n - 2; i >= 0; i--) {
    rightProduct[i] = rightProduct[i + 1] * nums[i + 1];
  }

  for (let i = 0; i < n; i++) {
    answer[i] = leftProduct[i] * rightProduct[i];
  }

  return answer;
};
```

时间复杂度：`O(n)`，空间复杂度：`O(n)`。

### 3. 前缀和（空间优化）👍

第一次先从左到右遍历数组，将左侧乘积保存到输出数组中。

然后，我们从右到左遍历数组，使用一个变量 `rightProduct` 保存右侧乘积，并计算结果。

``` js
var productExceptSelf = function (nums) {
  const n = nums.length;
  const answer = new Array(n).fill(1);
  let rightProduct = 1;

  for (let i = 1; i < n; i++) {
    answer[i] = answer[i - 1] * nums[i - 1];
  }

  for (let i = n - 1; i >= 0; i--) {
    answer[i] *= rightProduct;
    rightProduct *= nums[i];
  }

  return answer;
};
```

时间复杂度：`O(n)`，空间复杂度：`O(1)`。

## [2132. 用邮票贴满网格图 🔴](https://leetcode.cn/problems/range-addition/description/)

题目要求：

给定一个由 `0` 和 `1` 组成的二维网格 `grid`，其中 `1` 表示被占用、`0` 表示空白。

再给定一个邮票的尺寸 `stampHeight × stampWidth`，邮票只能覆盖全是 `0` 的区域，且可以无限次使用。

判断是否可以用邮票覆盖所有空白格子。

### 二维前缀和 + 二维差分

使用二维前缀和快速判断一个矩形区域内是否有障碍物，可在 `O(1)` 计算任意子矩阵的和。

使用二维差分数组记录邮票的覆盖情况，遍历所有可能放置邮票的左上角，若当前子矩阵没有障碍物，则可以放置一个邮票。

对差分数组求前缀和得到每个格子被覆盖的次数，若某个原始为 `0` 的格子，最终被盖到次数为 `0`，则说明无法用邮票覆盖，返回 `false`；否则返回 `true`。

``` js
var possibleToStamp = function (grid, stampHeight, stampWidth) {
  const m = grid.length;
  const n = grid[0].length;
  const prefixSum = Array.from({ length: m + 1 }, () => new Array(n + 1).fill(0));
  const diff = Array.from({ length: m + 2 }, () => new Array(n + 2).fill(0)); // 多加一圈边界，方便差分操作

  // 使用二维前缀和快速判断一个矩形区域内是否有障碍物
  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      prefixSum[i + 1][j + 1] = prefixSum[i][j + 1] + prefixSum[i + 1][j] - prefixSum[i][j] + grid[i][j];
    }
  }

  // 使用二维差分数组记录邮票的覆盖情况
  for (let i = 0; i <= m - stampHeight; i++) {
    for (let j = 0; j <= n - stampWidth; j++) {
      const x1 = i + 1;
      const y1 = j + 1;
      const x2 = i + stampHeight;
      const y2 = j + stampWidth;
      const sum = prefixSum[x2][y2] - prefixSum[x1 - 1][y2] - prefixSum[x2][y1 - 1] + prefixSum[x1 - 1][y1 - 1];

      if (sum === 0) {
        diff[x1][y1] += 1;
        diff[x1][y2 + 1] -= 1;
        diff[x2 + 1][y1] -= 1;
        diff[x2 + 1][y2 + 1] += 1;
      }
    }
  }

  // 对差分数组求前缀和得到每个格子被覆盖的次数
  for (let i = 1; i <= m ; i++) {
    for (let j = 1; j <= n; j++) {
      // 恢复差分数组
      diff[i][j] += diff[i - 1][j] + diff[i][j - 1] - diff[i - 1][j - 1];
      // 检查原始格子是否为 0 且未被覆盖
      if (grid[i - 1][j - 1] === 0 && diff[i][j] === 0) return false;
    }
  }
  
  return true;
};
```

时间复杂度：`O(mn)`，空间复杂度：`O(mn)`。
