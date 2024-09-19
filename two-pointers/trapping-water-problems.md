# 接雨水问题

## [11. 盛最多水的容器 🟡 ❤](https://leetcode.cn/problems/container-with-most-water/description/)

题目要求：计算由两条线组成的容器可以容纳的最大水量。

### 1. 暴力法（超时）

通过两层循环，遍历所有可能的组合，计算每个组合的容量的同时更新最大容量。

``` js
var maxArea = function (height) {
  const n = height.length;
  let max = 0;

  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      max = Math.max(max, Math.min(height[i], height[j]) * (j - i));
    }
  }

  return max;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 双指针 👍

使用左右两个指针从数组两端向中间靠拢，计算出最大容量。因为最大容量取决于较低的一边，所以每次将较低的一边向内移动，并更新最大容量。

``` js
var maxArea = function (height) {
  let max = 0;
  let l = 0;
  let r = height.length - 1;

  while (l < r) {
    max = Math.max(max, Math.min(height[l], height[r]) * (r - l));
    height[l] < height[r] ? l++ : r--;
  }

  return max;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [42. 接雨水 🔴 ❤](https://leetcode.cn/problems/trapping-rain-water/description/)

题目要求：给定一个数组表示地形，计算其可以容纳的雨水总量。

关键：只有**凹槽**（两边柱子高度高于当前位置）可以积水，所以需要先找出凹槽，然后计算每个凹槽可以容纳的雨水量。

### 1. 暴力法（超时）

遍历数组，逐个计算每个柱子可以容纳的雨水量，将它们累加得到总的雨水量。找出当前柱子左右两边最高柱子的高度，然后在它们之间选择最小值减去当前柱子的高度即可得到当前柱子可以容纳的雨水量。

``` js
var trap = function(height) {
  const n = height.length;
  if (n === 0) return 0;

  let res = 0;

  for (let i = 1; i < n - 1; i++) {
    let leftMax = 0;
    let rightMax = 0;

    for (let j = i; j >= 0; j--) {
      leftMax = Math.max(leftMax, height[j]);
    }

    for (let j = i; j < n; j++) {
      rightMax = Math.max(rightMax, height[j]);
    }

    res += Math.min(leftMax, rightMax) - height[i];
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 动态规划

通过空间换时间的思想，减少重复计算。首先从左往右和从右往左预处理每个柱子所遇到的最高柱子的高度，然后遍历数组计算当前柱子的积水量。

``` js
var trap = function(height) {
  const n = height.length;
  if (n === 0) return 0;

  let res = 0;
  const leftMax = new Array(n).fill(0);
  const rightMax = new Array(n).fill(0);

  leftMax[0] = height[0];
  for (let i = 1; i < n; i++) {
    leftMax[i] = Math.max(leftMax[i - 1], height[i]);
  }

  rightMax[n - 1] = height[n - 1];
  for (let i = n - 2; i >= 0; i--) {
    rightMax[i] = Math.max(rightMax[i + 1], height[i]);
  }

  for (let i = 1; i < n - 1; i++) {
    res += Math.min(leftMax[i], rightMax[i]) - height[i];
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 3. 双指针 👍

使用两个指针从两端向中间移动，在移动过程中通过两个变量更新当前位置左右两边遇到的最大柱子高度，从而避免记录高度的内存开销。由于当前位置能接的雨水量取决于左右两边高度的最小值决定，所以每次计算高度较低一端的雨水量，然后将指针向内移动。直到两指针相遇为止。

``` js
var trap = function (height) {
  const n = height.length;
  if (n === 0) return 0;

  let res = 0;
  let leftMax = 0;
  let rightMax = 0;
  let l = 0;
  let r = n - 1;

  while (l < r) {
    leftMax = Math.max(leftMax, height[l]);
    rightMax = Math.max(rightMax, height[r]);

    if (leftMax < rightMax) {
      res += leftMax - height[l++];
    } else {
      res += rightMax - height[r--];
    }
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 4. 单调栈 👍

维护一个单调递减栈，只有后面的柱子比前面高时可以接到雨水。从左到右遍历数组，如果栈顶元素小于当前元素，则出栈并计算当前柱子的积水面积；否则将当前索引入栈。重复以上操作，直到遍历结束为止。

``` js
var trap = function (height) {
  const n = height.length;
  if (n === 0) return 0;

  let res = 0;
  const stack = [];

  for (let i = 0; i < n; i++) {
    while (stack.length > 0 && height[i] > height[stack.at(-1)]) {
      const top = stack.pop();
      if (!stack.length) break;

      const left = stack.at(-1);
      const curWidth = i - left - 1;
      const curHeight = Math.min(height[left], height[i]) - height[top];
      res += curWidth * curHeight;
    }
    stack.push(i);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。
