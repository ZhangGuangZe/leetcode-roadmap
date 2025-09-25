# 摆动排序算法问题

## [280. 摆动排序 🟡 ❤ 🔒](https://leetcode.cn/problems/wiggle-sort/description/)

题目要求：给你一个的整数数组 `nums`, 将该数组重新排序后使 `nums[0] <= nums[1] >= nums[2] <= nums[3]...`，即奇数下标对应的元素大于等于相邻偶数下标对应的元素，从而形成波浪顺序。

### 1. 排序 + 间隔交换

首先对数组进行排序，然后遍历数组，从下标 `1` 开始每隔一个元素交换相邻元素。

``` js
var wiggleSort = function (nums) {
  nums.sort((a, b) => a - b);
  for (let i = 1; i < nums.length - 1; i += 2) {
    [nums[i], nums[i + 1]] = [nums[i + 1], nums[i]];
  }
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。

### 2. 一次遍历 + 交换 👍

遍历数组，根据当前下标的奇偶性决定是否交换相邻元素。

``` js
var wiggleSort = function (nums) {
  for (let i = 0; i < nums.length - 1; i++) {
    if ((i % 2 === 0 && nums[i] > nums[i + 1]) || (i % 2 === 1 && nums[i] < nums[i + 1])) {
      [nums[i], nums[i + 1]] = [nums[i + 1], nums[i]];
    }
  }
}
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [324. 摆动排序 II 🟡](https://leetcode.cn/problems/wiggle-sort-ii/description/)

题目要求：将给定数组重新排列为奇数下标对应元素要严格大于偶数下标对应元素。

### 模拟

首先将数组排序并存储到新数组中，然后遍历数组，将较小的元素放置在奇数下标位置，较大的元素放置在偶数下标位置。这样从大到小交错填充原数组，从而避免出现重复元素相邻。

``` js
var wiggleSort = function (nums) {
  const sorted = [...nums].sort((a, b) => a - b);
  const n = nums.length;
  let l = Math.floor((n + 1) / 2) - 1;
  let r = n - 1;

  for (let i = 0; i < n; i++) {
    nums[i] = i % 2 === 0 ? sorted[l--] : sorted[r--];
  } 
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。