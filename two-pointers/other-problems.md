# 其他问题

## [243. 最短单词距离 🟢 🔒](https://leetcode.cn/problems/shortest-word-distance/description/)

题目要求：在给定的字符串列表中找到两个指定单词之间的最短距离。

### 双指针

遍历数组，使用两个指针 `i` 和 `j` 分别记录 `word1` 和 `word2` 在数组中的位置，然后计算两个指针之间的距离，更新最短距离。

``` js
var shortestDistance = function (wordsDict, word1, word2) {
  let res = Infinity;
  let i = -1;
  let j = -1;

  for (let k = 0; k < wordsDict.length; k++) {
    if (wordsDict[k] === word1) {
      i = k;
    } else if (wordsDict[k] === word2) {
      j = k;
    }

    if (i !== -1 && j !== -1) {
      res = Math.min(res, Math.abs(i - j));
    }
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [581. 最短无序连续子数组 🟡](https://leetcode.cn/problems/shortest-unsorted-continuous-subarray/description/)

题目要求：在数组中找到一个最短无序子数组，将其排序后，使整个数组升序排列，返回该子数组的长度。

### 1. 暴力法

双重循环遍历数组，找出数组的第一个和最后一个逆序对的位置，它们的距离即为最短子数组的长度。

``` js
var findUnsortedSubarray = function (nums) {
  const n = nums.length;
  let l = n;
  let r = 0;

  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (nums[i] > nums[j]) {
        l = Math.min(l, i);
        r = Math.max(r, j);
      }
    }
  }

  return r - l > 0 ? r - l + 1 : 0;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 排序

将数组复制一份到新数组中并将其排序，通过比较新数组和原数组，找出第一个和最后一个不同元素的位置，它们的距离即为最短子数组的长度。

优化：我们可以从左向右扫描第一个逆序对的位置，从右向左扫描最后一个逆序对的位置，从而优化搜索逆序对的性能。

``` js
var findUnsortedSubarray = function (nums) {
  const sorted = [...nums].sort((a, b) => a - b);

  const n = nums.length;
  let l = 0;
  let r = n - 1;

  while (l < n && nums[l] === sorted[l]) l++;
  while (r >= 0 && nums[r] === sorted[r]) r--;

  return r - l > 0 ? r - l + 1 : 0;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

### 3. 单调栈 👍

首先维护一个单调递增的栈，从左向右扫描数组，如果当前元素小于栈顶元素，则找到第一个逆序对，将栈顶元素出栈，并更新最短子数组的左边界，否则将当前元素的索引入栈。

然后维护一个单调递减的栈，从右向左扫描数组，如果当前元素大于栈顶元素，则找到最后一个逆序对，将栈顶元素出栈，并更新最短子数组的右边界，否则将当前元素的索引入栈。

``` js
var findUnsortedSubarray = function (nums) {
  const n = nums.length;
  let l = n;
  let r = 0;
  let stack = [];
  
  for (let i = 0; i < n; i++) {
    while (stack.length > 0 && nums[i] < nums[stack.at(-1)]) {
      l = Math.min(l, stack.pop());
    }
    stack.push(i);
  }

  stack = [];
  for (let i = n - 1; i >= 0; i--) {
    while (stack.length > 0 && nums[i] > nums[stack.at(-1)]) {
      r = Math.max(r, stack.pop());
    }
    stack.push(i);
  }

  return r - l > 0 ? r - l + 1 : 0;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 4. 双指针 👍

在遍历过程中可以同时跟踪数组中的最大值和最小值，并根据它们的位置来判断无序子数组的左右边界。

从左到右扫描记录 `max`，如果当前元素小于 `max`，则更新右边界；从右到左扫描记录 `min`，如果当前元素大于 `min`，则更新左边界。遍历完成后可以通过左右边界来得到最短子数组的长度。

``` js
var findUnsortedSubarray = function (nums) {
  const n = nums.length;
  let l = 0;
  let r = -1;
  let max = nums[0];
  let min = nums[n - 1];

  for (let i = 0; i < n; i++) {
    max = Math.max(max, nums[i]);
    min = Math.min(min, nums[n - i - 1]);

    if (max > nums[i]) r = i;
    if (min < nums[n - i - 1]) l = n - i - 1;
  }

  return r - l + 1;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。
