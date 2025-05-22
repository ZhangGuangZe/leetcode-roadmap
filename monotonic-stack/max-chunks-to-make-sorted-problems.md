# 最多能完成排序的块问题

## [769. 最多能完成排序的块 🟡](https://leetcode.cn/problems/max-chunks-to-make-sorted/description/)

题目要求：给定一个元素从 0 ~ n-1 排列的无重复数组 `arr`，求将其最多分成多少块，能够将每个块单独排序后使整个数组升序排列。

### 1. 贪心 👍

由于数组 `arr` 是一个 0 ~ n-1 的排列，若已遍历过的数中的最大值 `max` 与当前元素的下标相等，说明可以进行一次分块，累加块数。

``` js
var maxChunksToSorted = function (arr) {
  let max = 0;
  let count = 0;

  for (let i = 0; i < arr.length; i++) {
    max = Math.max(max, arr[i]);
    if (max === i) count++;
  }

  return count;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 2. 单调栈 👍

我们维护一个单调递增的栈，栈中存储每个分块的最大值。

遍历数组，对于每个元素，如果栈顶元素大于当前元素，则它们分块不能使整个数组升序排列，所以弹出栈顶元素并更新最大值；否则将当前最大元素入栈。最后栈的大小即为最多分块的数量。

``` js
var maxChunksToSorted = function (arr) {
  const stack = [];

  for (const num of arr) {
    let max = num;
    while (stack.length > 0 && stack.at(-1) > num) {
      max = Math.max(max, stack.pop());
    }
    stack.push(max);
  }

  return stack.length;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [768. 最多能完成排序的块 II 🔴](https://leetcode.cn/problems/max-chunks-to-make-sorted-ii/description/)

题目要求：给定一个可能包含重复元素的数组 `arr`，求将其最多分成多少块，能够将每个块单独排序后使整个数组升序排列。

### 1. 前缀最大与后缀最小比较 👍

用前缀最大值与后缀最小值来判断在哪些位置可以合法分块排序。

首先预处理前缀最大值和后缀最小值。然后遍历数组，判断当前位置是否可以合理分块，如果当前位置左边最大值小于等于右边最小值，那么左边排好序，右边排好序，拼接起来可以得到一个升序数组。所以可以进行分块，则累加分块数量。

``` js
var maxChunksToSorted = function (arr) {
  const n = arr.length;
  const leftMax = new Array(n).fill(0);
  const rightMin = new Array(n).fill(0);
  let count = 1;

  leftMax[0] = arr[0];
  for (let i = 1; i < n; i++) {
    leftMax[i] = Math.max(leftMax[i - 1], arr[i]);
  }

  rightMin[n - 1] = arr[n - 1];
  for (let i = n - 2; i >= 0; i--) {
    rightMin[i] = Math.min(rightMin[i + 1], arr[i]);
  }

  for (let i = 0; i < n - 1; i++) {
    if (leftMax[i] <= rightMin[i + 1]) count++;
  }

  return count;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 2. 单调栈 👍

此题解与上一题的思路和解法相同，所以这里不再赘述。
