# 合并数组问题

## [88. 合并两个有序数组 🟢 ❤](https://leetcode.cn/problems/merge-sorted-array/description/)

题目要求：给定两个**有序**整数数组 `nums1` 和 `nums2` ，将`nums2` 合并到 `nums1` 中，使 `nums1` 成为一个有序数组。

### 1. 暴力法

先将 `nums2` 所有元素插入到 `num1` 末尾，然后对 `num1` 进行排序。

``` js
var merge = function(nums1, m, nums2, n) {
  for (let i = 0; i < n; i++) {
    nums1[m + i] = nums2[i];
  }

  nums1.sort((a, b) => a - b);
};
```

时间复杂度：O((m + n)log(m + n))，空间复杂度：O(m + n)。

### 2. 双指针（额外空间）👍

利用两数组的有序性，依次从数组头部（从小到大）将较小的元素添加到新数组中，然后将新数组中的所有元素复制到 `nums1` 中。

``` js
var merge = function(nums1, m, nums2, n) {
  const sorted = [];
  let i = 0;
  let j = 0;

  // 循环遍历两个数组，将较小的元素添加到新数组中
  while (i < m && j < n) {
    if (nums1[i] < nums2[j]) {
      sorted.push(nums1[i++]);
    } else {
      sorted.push(nums2[j++]);
    }
  }

  // 将剩余的元素添加到新数组中
  while (i < m) sorted.push(nums1[i++]);
  while (j < n) sorted.push(nums2[j++]);

  // 将新数组中的所有元素复制到 nums1 中
  for (let i = 0; i < m + n; i++) {
    nums1[i] = sorted[i];
  }
};
```

时间复杂度：O(m + n)，空间复杂度：O(m + n)。

### 3. 双指针（原地）👍

利用双指针，在 `nums1` 中原地合并数组。从后向前（从大到小）遍历数组，每次将较大的元素从右向左依次放到 `nums1` 中，从而避免覆盖 `nums1` 中未处理的元素。

``` js
var merge = function(nums1, m, nums2, n) {
  let i = m - 1;
  let j = n - 1;
  let k = m + n - 1;

  while (j >= 0) {
    // nums1[k--] = (i >= 0 && nums1[i] > nums2[j]) ? nums1[i--] : nums2[j--];
    if (i >= 0 && nums1[i] > nums2[j]) {
      nums1[k] = nums1[i];
      i--;
    } else {
      nums1[k] = nums2[j];
      j--;
    }
    k--;
  }
};
```

时间复杂度：O(m + n)，空间复杂度：O(1)。

## [977. 有序数组的平方 🟢](https://leetcode.cn/problems/squares-of-a-sorted-array/)

题目要求：将给定的有序数组 `nums` 中的每个元素平方并返回一个按升序排列的新数组。

### 1. 暴力法

先将 `nums` 中的每个元素平方，然后对平方后的数组进行排序，最后返回排序后的数组。

``` js
var sortedSquares = function(nums) {
  return nums.map(item => item * item).sort((a, b) => a - b);
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

### 2. 双指针（从大到小）👍

由于数组已经按照升序排列，数组中所有负数平方后是降序排列的子数组，所有正数平方后是升序排列的子数组。我们可以将此题转化为合并两个有序数组的问题。

因为数组所有元素平方后最大元素可能在两端，所以使用双指针，从两端向中间（从大到小）遍历两个子数组，每次将较大的元素从右向左依次添加到新数组中。

``` js
var sortedSquares = function(nums) {
  const n = nums.length;
  let i = 0;
  let j = n - 1;
  let k = n - 1;
  const sorted = [];

  while (i <= j) {
    sorted[k--] = Math.abs(nums[i]) > Math.abs(nums[j]) ? nums[i++] ** 2 : nums[j--] ** 2;
  }

  return sorted;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 3. 双指针（从小到大）👍

我们可以根据两个子数组的特点，找到两子数组的最小元素，并将指针指向它们，从小到大的比较，将较小的元素依次添加到新数组中。

``` js
var sortedSquares = function(nums) {
  const n = nums.length;
  let j = 0;

  while (j < n && nums[j] < 0) j++;

  let i = j - 1;
  const sorted = [];

  while (i >= 0 && j < n) {
    if (Math.abs(nums[i]) < Math.abs(nums[j])) {
      sorted.push(nums[i--] ** 2);
    } else {
      sorted.push(nums[j++] ** 2);
    }
  }

  while (i >= 0) sorted.push(nums[i--] ** 2);
  while (j < n) sorted.push(nums[j++] ** 2);

  return sorted;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。
