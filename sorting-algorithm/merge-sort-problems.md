# 归并排序问题

## [剑指 Offer 51. 数组中的逆序对 🔴](https://leetcode.cn/problems/shu-zu-zhong-de-ni-xu-dui-lcof/description/)

题目要求：返回数组中逆序对（前面的元素大于后面的元素）的个数。

### 1. 暴力法（超时）

枚举数组中的每一对元素，统计逆序对数量。

``` js
var reversePairs = function (record) {
  const n = record.length;
  let count = 0;

  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (record[i] > record[j]) count++;
    }
  }

  return count;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 归并排序 👍

在归并排序的合并过程中统计逆序对。当合并两个有序数组时，如果左子数组的当前元素大于右子数组的当前元素，则左子数组当前元素和后面的所有元素都能与右子数组的当前元素构成逆序对。

``` js
var reversePairs = function (record) {
  if (record.length < 2) return 0;

  const temp = [];
  const sort = (nums, lo, hi, temp) => {
    if (lo >= hi) return 0;

    let count = 0;

    const mid = Math.floor((lo + hi) / 2);
    count += sort(nums, lo, mid, temp); // 递归左子数组
    count += sort(nums, mid + 1, hi, temp); // 递归右子数组
    count += merge(nums, lo, mid, hi, temp); // 合并左右子数组并统计逆序对

    return count;
  };
  return sort(record, 0, record.length - 1, temp);
};

const merge = (nums, lo, mid, hi, temp) => {
  let i = lo;
  let j = mid + 1;
  let k = lo;

  let count = 0;

  while (i <= mid && j <= hi) {
    if (nums[i] <= nums[j]) {
      temp[k++] = nums[i++];
    } else {
      temp[k++] = nums[j++];
      count += (mid - i + 1); // 统计逆序对
    }
  }

  while (i <= mid) temp[k++] = nums[i++];
  while (j <= hi) temp[k++] = nums[j++];

  for (let i = lo; i <= hi; i++) {
    nums[i] = temp[i];
  }

  return count;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

## [315. 计算右侧小于当前元素的个数 🔴](https://leetcode.cn/problems/count-of-smaller-numbers-after-self/description/)

题目要求：给定一个数组 `nums`，计算并返回每个元素右侧小于当前元素的个数。

### 1. 暴力法（超时）

遍历数组，对于每个元素，统计比它小的元素个数。

``` js
var countSmaller = function(nums) {
  const n = nums.length;
  const res = [];

  for (let i = 0; i < n; i++) {
    let count = 0;
    for (let j = i + 1; j < n; j++) {
      if (nums[i] > nums[j]) count++;
    }
    res.push(count);
  }

  return res;
};
```

时间复度：O(n²)，空间复杂度：O(n)。

### 2. 归并排序 👍

利用归并排序的思想，将元素和对应下标捆绑在一起，在归并的过程中，统计每个元素右侧小于它的数量。

``` js
var countSmaller = function (nums) {
  if (nums.length < 2) return [0];

  const indexs = nums.map((_, index) => index); // 记录原始索引
  const temp = []; // 临时数组用于存储排序后的元素
  const tempIndexs = []; // 临时数组用于存储索引
  const counts = Array(nums.length).fill(0); // 统计每个元素右侧小于它的个数

  const mergeSort = (nums, lo, hi, temp) => {
    if (lo >= hi) return;

    const mid = Math.floor((lo + hi) / 2);
    mergeSort(nums, lo, mid, temp); // 递归左半部分
    mergeSort(nums, mid + 1, hi, temp); // 递归右半部分
    merge(nums, lo, mid, hi, temp, tempIndexs, indexs, counts); // 合并两部分并统计逆序对
  };
  mergeSort(nums, 0, nums.length - 1, temp);

  return counts;
};

const merge = (nums, lo, mid, hi, temp, tempIndexs, indexs, counts) => {
  let i = lo;
  let j = mid + 1;
  let k = lo;

  while (i <= mid && j <= hi) {
    if (nums[i] <= nums[j]) {
      temp[k] = nums[i];
      tempIndexs[k] = indexs[i];
      counts[indexs[i]] += (j - mid - 1); // 累加右侧小于当前元素的个数
      i++;
    } else {
      temp[k] = nums[j];
      tempIndexs[k] = indexs[j];
      j++;
    }
    k++;
  }

  while (i <= mid) {
    temp[k] = nums[i];
    tempIndexs[k] = indexs[i];
    counts[indexs[i]] += (j - mid - 1); // 累加右侧小于当前元素的个数
    i++;
    k++;
  }

  while (j <= hi) {
    temp[k] = nums[j];
    tempIndexs[k] = indexs[j];
    j++;
    k++;
  }

  for (let i = lo; i <= hi; i++) {
    indexs[i] = tempIndexs[i];
    nums[i] = temp[i];
  }
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

## [493. 翻转对 🔴](https://leetcode.cn/problems/reverse-pairs/description/)

题目要求：给定一个数组 `nums`，返回数组中重要翻转对（`i < j` 且 `nums[i] > 2*nums[j]`）的个数。

### 1. 暴力法（超时）

枚举所有可能的翻转对，统计重要翻转对的数量

``` js
var reversePairs = function (nums) {
  let count = 0;

  for (let i = 0; i < nums.length - 1; i++) {
    for (let j = i + 1; j < nums.length; j++) {
      if (nums[i] > 2 * nums[j]) count++;
    }
  }

  return count;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 归并排序 👍

在归并过程中，先统计重要翻转对的数量，再进行合并操作。因为左右子数组是有序的，可以使用双指针快速统计翻转对的数量，然后统计跨区域的翻转对数量。

``` js
var reversePairs = function (nums) {
  if (nums.length < 2) return 0;

  const temp = [];
  return mergeSort(nums, 0, nums.length - 1, temp);
};

const mergeSort = (nums, lo, hi, temp) => {
  if (lo >= hi) return 0;

  let count = 0;
  let mid = Math.floor((lo + hi) / 2);
  count += mergeSort(nums, lo, mid, temp);
  count += mergeSort(nums, mid + 1, hi, temp);

  // 统计翻转对数量
  let j = mid + 1;
  for (let i = lo; i <= mid; i++) {
    while (j <= hi && nums[i] > 2 * nums[j]) j++;
    count += (j - (mid + 1));
  }

  // 合并两部分并统计逆序对
  merge(nums, lo, mid, hi, temp);
  return count;
};

const merge = (nums, lo, mid, hi, temp) => {
  let i = lo;
  let j = mid + 1;
  let k = lo;

  while (i <= mid && j <= hi) {
    if (nums[i] <= nums[j]) {
      temp[k++] = nums[i++];
    } else {
      temp[k++] = nums[j++];
    }
  }

  while (i <= mid) temp[k++] = nums[i++];
  while (j <= hi) temp[k++] = nums[j++];

  for (let i = lo; i <= hi; i++) {
    nums[i] = temp[i];
  }
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

## [327. 区间和的个数 🔴](https://leetcode.cn/problems/count-of-range-sum/description/)

题目要求：给定一个数组 `nums`，返回数组中区间和在 `[lower, upper]` 范围内的个数。

### 1. 暴力法（超时）

枚举所有可能的区间，计算每个区间和，并判断是否在 `[lower, upper]` 范围内，统计在区间和在指定范围内的数量。

``` js
var countRangeSum = function (nums, lower, upper) {
  const n = nums.length;
  let count = 0;

  for (let i = 0; i < n; i++) {
    for (let j = i; j < n; j++) {
      let sum = 0;
      for (let k = i; k <= j; k++) {
        sum += nums[k];
      }
      if (sum >= lower && sum <= upper) {
        count++;
      }
    }
  }

  return count;
};
```

时间复杂度：O(n³)，空间复杂度：O(n)。

### 2. 前缀和 + 归并排序 👍

首先将区间和转化为前缀和数组的差问题。

区间和 `sum(i..j) = preSum[j+1] - preSum[i]`，问题即可转化为统计满足 `lower <= preSum[j] - preSum[i] <= upper` 的数量。

然后对前缀和数组进行归并排序，在合并之前先统计左子数组元素与右子数组元素满足条件的数量，再合并统计跨区间满足条件的数量。

``` js
var countRangeSum = function (nums, lower, upper) {
  const n = nums.length;
  const prefixSum = Array(n + 1).fill(0);
  const temp = Array(n + 1).fill(0);

  for (let i = 0; i < n; i++) {
    prefixSum[i + 1] = prefixSum[i] + nums[i];
  }

  const mergeSort = (nums, lo, hi, temp) => {
    if (lo >= hi) return 0;

    let count = 0;
    const mid = Math.floor((lo + hi) / 2);
    count += mergeSort(nums, lo, mid, temp); // 递归左半部分
    count += mergeSort(nums, mid + 1, hi, temp); // 递归右半部分

    // 统计区间和的个数
    let l = mid + 1;
    let r = mid + 1;
    for (let i = lo; i <= mid; i++) {
      while (l <= hi && nums[l] - nums[i] < lower) l++;
      while (r <= hi && nums[r] - nums[i] <= upper) r++;
      count += (r - l);
    }

    // 合并两部分
    merge(nums, lo, mid, hi, temp);
    return count;
  };
  return mergeSort(prefixSum, 0, n, temp);
};

const merge = (nums, lo, mid, hi, temp) => {
  let i = lo;
  let j = mid + 1;
  let k = lo;

  while (i <= mid && j <= hi) {
    if (nums[i] <= nums[j]) {
      temp[k++] = nums[i++];
    } else {
      temp[k++] = nums[j++];
    }
  }

  while (i <= mid) temp[k++] = nums[i++];
  while (j <= hi) temp[k++] = nums[j++];

  for (let i = lo; i <= hi; i++) {
    nums[i] = temp[i];
  }
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。
