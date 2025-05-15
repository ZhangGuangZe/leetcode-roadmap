# 下一个更大元素问题

## [496. 下一个更大元素 I 🟢](https://leetcode.cn/problems/next-greater-element-i/description/)

题目要求：给定两个**没有重复元素**的数组 `nums1` 和 `nums2`，其中 `nums1` 是 `nums2` 的子集，找出 `nums1` 当前元素 `x` 在 `nums2` 中对应位置右侧第一个比 `x` 大的元素。

### 1. 暴力法

对于 `nums1` 中的每个元素，先找到它在 `nums2` 中对应的位置，然后从该位置向右遍历 `nums2`，找出第一个比它大的元素，并将其添加到结果中。如果没有找到，则查询结果为 `-1`。

``` js
var nextGreaterElement = function (nums1, nums2) {
  const res = [];

  for (const x of nums1) {
    // let i = -1;
    // for (let j = 0; i < nums2.length; j++) {
    //   if (nums2[j] === x) {
    //     i = j;
    //     break;
    //   }
    // }
    const i = nums2.indexOf(x);

    let nextGreater = -1;
    for (let j = i + 1; j < nums2.length; j++) {
      if (nums2[j] > x) {
        nextGreater = nums2[j];
        break;
      }
    }
    res.push(nextGreater);
  }

  return res;
};
```

时间复杂度：O(mn)，空间复杂度：O(n)。

由于使用暴力法在查找每个元素的下一个更大元素时需要重复遍历，所以我们可以使用单调栈来优化。

### 2. 单调栈 👍

我们维护一个单调递减的栈，并用哈希表预处理每个元素的下一个更大元素。

遍历数组 `nums2`，对于每个元素 `x`：

- 如果栈顶元素小于当前元素，则将栈顶元素出栈，并将当前元素作为栈顶元素的下一个更大元素存入哈希表中；
- 否则，将当前元素入栈。

最后遍历数组 `nums1`，从哈希表中获取每个元素的下一个更大元素，并添加到结果数组中。

``` js
var nextGreaterElement = function (nums1, nums2) {
  const res = [];
  const stack = [];
  const map = new Map();

  for (const x of nums2) {
    while (stack.length > 0 && stack.at(-1) < x) {
      map.set(stack.pop(), x);
    }
    stack.push(x);
  }

  for (const num of nums1) {
    res.push(map.get(num) ?? -1);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [503. 下一个更大元素 II 🟡](https://leetcode.cn/problems/next-greater-element-ii/description/)

题目要求：在一个**循环数组** `nums` 中，查找当前元素的下一个更大元素。

### 1. 暴力法

遍历数组 `nums`，对于每个元素 `num`，从当前位置的下一个位置开始，找到第一个比 `num` 大的元素，将其保存在结果数组中。

这里需要使用求余运算来模拟在循环数组中的查找下一个更大元素。

``` js
var nextGreaterElements = function (nums) {
  const n = nums.length;
  const res = new Array(n).fill(-1);

  for (let i = 0; i < n; i++) {
    for (let j = 1; j < n; j++) {
      const nextIndex = (i + j) % n;
      if (nums[nextIndex] > nums[i]) {
        res[i] = nums[nextIndex];
        break;
      }
    }
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 单调栈 👍

我们维护一个单调递减栈，栈中保存元素的下标。

遍历数组 `nums`，将下标的长度扩大为原来的两倍，然后通过求余运算来模拟在循环数组中的查找下一个更大元素。

如果栈顶元素小于当前元素，则将栈顶元素出栈，并将当前元素作为栈顶元素的下一个更大元素，存入结果数组中。否则，将当前元素的下标入栈。

``` js
var nextGreaterElements = function (nums) {
  const n = nums.length;
  const res = new Array(n).fill(-1);
  const stack = [];

  for (let i = 0; i < 2 * n - 1; i++) {
    const index = i % n;
    while (stack.length > 0 && nums[stack.at(-1)] < nums[index]) {
      res[stack.pop()] = nums[index];
    }
    stack.push(index);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [739. 每日温度 🟡 ❤](https://leetcode.cn/problems/daily-temperatures/description/)

题目要求：给定一个数组 `temperatures`，找出当前气温的下一个更高温度的天数，如果不存在，则再该位置用 `0` 表示。

### 1. 暴力法（超时）

遍历数组 `temperatures`，对于每个元素，从当前位置的下一个位置开始，找到第一个比当前元素气温高的元素，将其天数差保存在结果数组中。

``` js
var dailyTemperatures = function (temperatures) {
  const n = temperatures.length;
  const res = new Array(n).fill(0);

  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (temperatures[j] > temperatures[i]) {
        res[i] = j - i;
        break;
      }
    }
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 单调栈 👍

我们维护一个单调递减的栈，栈中保存元素的下标。

遍历数组 `temperatures`，对于每个元素：

- 如果栈顶元素的温度小于当前元素，则将栈顶元素出栈，然后更新天数并将其存入结果数组中。
- 否则，将当前元素的下标入栈。

``` js
var dailyTemperatures = function (temperatures) {
  const n = temperatures.length;
  const res = new Array(n).fill(0);
  const stack = [];

  for (let i = 0; i < n; i++) {
    while (stack.length > 0 && temperatures[stack.at(-1)] < temperatures[i]) {
      const prevIndex = stack.pop();
      res[prevIndex] = i - prevIndex;
    }
    stack.push(i);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [901. 股票价格跨度 🟡](https://leetcode.cn/problems/online-stock-span/description/)

题目要求：返回当日股价的跨度（前面不超过当日股价的最大连续天数）。

### 1. 暴力法

从当前元素向左遍历元素，判断前面的元素是否不超过当前元素，如果不超过则累加跨度，否则退出循环返回跨度。

``` js
var StockSpanner = function () {
  this.prices = [];
};

StockSpanner.prototype.next = function (price) {
  this.prices.push(price);
  let span = 1;

  for (let i = this.prices.length - 2; i >= 0; i--) {
    if (this.prices[i] > price) break;
    span++;
  }

  return span;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

在每次 `next()` 操作时，我们都需要重复遍历数组，我们可以使用单调栈来优化。

### 2. 单调栈 👍

我们可以将问题转换为：在当前元素左侧查找上一个更大的元素。

维护一个单调递减的栈，栈中保存当前股价以及当前股价的跨度。

每次 `next()` 操作时，如果栈顶股价不超过当前股价，则将栈顶股价出栈，并计算当前股价的跨度。否则将当前股价和及其跨度入栈。

``` js
var StockSpanner = function () {
  this.stack = [];
};

StockSpanner.prototype.next = function (price) {
  let span = 1;

  while (this.stack.length > 0 && this.stack.at(-1).price <= price) {
    span += this.stack.pop().span;
  }
  this.stack.push({price, span});

  return span;
};
```

时间复杂度：均摊 O(1)，空间复杂度：O(n)。
