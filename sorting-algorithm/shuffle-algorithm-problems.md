# 洗牌算法问题

## [384. 打乱数组 🟡](https://leetcode.cn/problems/shuffle-an-array/description/)

题目要求：设计一个类，支持将数组元素随机打乱（`shuffle()`）和将数组元素重置为初始状态（`reset()`）。

### 1. 暴力法

保存原始数组，用于 `reset()` 恢复初始状态。

在 `shuffle()` 时，创建一个数组的副本，每次从副本中随机抽取一个元素放入新数组中，并将抽取的元素从副本中移除，直到副本为空为止，新数组即为随机打乱的结果。

``` js
var Solution = function (nums) {
  this.nums = nums;
};

Solution.prototype.reset = function () {
  return this.nums;
};

Solution.prototype.shuffle = function () {
  const arr = [...this.nums];
  const res = [];

  while (arr.length > 0) {
    const index = Math.floor(Math.random() * arr.length);
    res.push(arr.splice(index, 1)[0]);
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(n)。

### 2. Fisher-Yates 洗牌算法 👍

从数组末尾开始，每次随机选择一个元素，然后将其与当前位置的元素交换。这样可以保证每个元素被随机置于数组中的任意位置。

``` js
var Solution = function (nums) {
  this.nums = nums;
};

Solution.prototype.reset = function () {
  return this.nums;
};

Solution.prototype.shuffle = function () {
  const res = [...this.nums];

  for (let i = res.length - 1; i > 0; i--) {
    const j = Math.floor(Math.random() * (i + 1));
    [res[i], res[j]] = [res[j], res[i]];
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。