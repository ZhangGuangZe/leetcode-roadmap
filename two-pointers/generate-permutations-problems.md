# 生成排列问题

## [31. 下一个排列 🟡 ❤](https://leetcode.cn/problems/next-permutation/description/)

题目要求：将给定序列按字典序**原地**生成下一个更大的排列。如果不存在下一个更大的排列，则将序列重新排列成字典序最小的排列。

### 双指针

1. 找到满足 `nums[i] < nums[i + 1]` 的最大索引 `i`。如果不存在这样的索引，则该排列是最后一个排列；
2. 找出大于 `i` 的最大索引 `j`，使得 `nums[i] < nums[j]`；
3. 将 `nums[i]` 的值与 `nums[j]` 的值交换；
4. 反转 [i + 1, n) 范围内的所有元素。

``` js
var nextPermutation = function (nums) {
  const n = nums.length;
  let i = n - 2;

  // step 1
  while (i >= 0 && nums[i] >= nums[i + 1]) i--;

  // step 2
  if (i >= 0) {
    let j = n - 1;
    while (j >= 0 && nums[i] >= nums[j]) j--;
    // step 3
    swap(nums, i, j);
  }

  // step 4
  let l = i + 1;
  let r = n - 1;
  while (l < r) {
    swap(nums, l++, r--);
  }
};

const swap = (a, i, j) => {
  [a[i], a[j]] = [a[j], a[i]];
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [556. 下一个更大元素 III 🟡](https://leetcode.cn/problems/next-greater-element-iii/description/)

题目要求：找到给定整数的下一个更大元素。如果不存在这样的元素或者这个元素在 32 位有符号整数范围外，则返回 -1。

### 双指针

首先将整数转换为数组，然后使用上一题的思路找出下一个排列，最后将序列转换为整数。

``` js
var nextGreaterElement = function (n) {
  const nums = n.toString().split('');
  const len = nums.length;

  let i = len - 2;
  while (i >= 0 && nums[i] >= nums[i + 1]) i--;

  if (i < 0) return -1;

  let j = len - 1;
  while (nums[i] >= nums[j]) j--;
  swap(nums, i, j);

  let l = i + 1;
  let r = len - 1;
  while (l < r) {
    swap(nums, l++, r--);
  }

  const res = Number.parseInt(nums.join(''));
  return res > 2 ** 31 - 1 ? -1 : res;
};

const swap = (a, i, j) => {
  [a[i], a[j]] = [a[j], a[i]];
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## 参考

[Wikipedia](https://en.wikipedia.org/wiki/Permutation#Generation_in_lexicographic_order)
