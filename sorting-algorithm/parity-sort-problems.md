# 奇偶排序问题

## [905. 按奇偶排序数组 🟢 ❤](https://leetcode.cn/problems/sort-array-by-parity/description/)

题目要求：将给定数组 `nums` 中的所有偶数移动到数组的前半部分，奇数移动到数组的后半部分。

### 1. 两次遍历

使用一个数组存储排序后的结果，第一次遍历收集偶数，第二次遍历收集奇数。

``` js
var sortArrayByParity = function (nums) {
  const res = [];

  for (const num of nums) {
    if (num % 2 === 0) res.push(num);
  }

  for (const num of nums) {
    if (num % 2 === 1) res.push(num);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 2. 双指针 + 原地交换 👍

使用两个指针 `l` 和 `r`，分别指向数组的开头和末尾。`l` 指针从左向右开始寻找奇数，`r` 指针从右向左寻找偶数。交换两指针对应元素，直到 `l` 和 `r` 相遇。  

``` js
var sortArrayByParity = function (nums) {
  let l = 0;
  let r = nums.length - 1;

  while (l < r) {
    while (l < r && nums[l] % 2 === 0) l++;
    while (l < r && nums[r] % 2 === 1) r--;
    if (l < r) [nums[l], nums[r]] = [nums[r], nums[l]];
  }

  return nums;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [922. 按奇偶排序数组 II 🟢](https://leetcode.cn/problems/sort-array-by-parity-ii/description/)

题目要求：将一个一半是奇数，一半是偶数的数组进行排序，使得奇数下标对应奇数元素，偶数下标对应偶数元素。

### 1. 一次遍历

维护一个数组 `res` 和两个用于表示奇偶位置的下标 `i` 和 `j`。

遍历数组，将偶数元素放置在奇数位置，将奇数元素放置在偶数位置。

``` js
var sortArrayByParityII = function (nums) {
  const res = [];
  let i = 0;
  let j = 1;

  for (const num of nums) {
    if (num % 2 === 0) {
      res[i] = num;
      i += 2;
    } else {
      res[j] = num;
      j += 2;
    }
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 2. 双指针 + 原地交换 👍

定义两个指针 `i` 和 `j`，分别表示奇数下标和偶数下标，初始化时指向 `0` 和 `1` 的位置。

当偶数下标 `i` 指向奇数元素，奇数下标 `j` 指向偶数元素时，原地交换这两个元素。

``` js
var sortArrayByParityII = function (nums) {
  const n = nums.length;
  let i = 0;
  let j = 1;

  while (i < n) {
    while (i < n && nums[i] % 2 === 0) i += 2;
    while (j < n && nums[j] % 2 === 1) j += 2;
    if (i < n && j < n) [nums[i], nums[j]] = [nums[j], nums[i]];
  }

  return nums;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。