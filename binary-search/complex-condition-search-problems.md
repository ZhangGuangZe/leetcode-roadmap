# 复杂条件问题

## [658. 找到 K 个最接近的元素 🟡](https://leetcode.cn/problems/find-k-closest-elements/description/)

题目要求：在一个**已排序**的整数数组 `arr` 中，找到 `k` 个最接近给定值 `x` 的元素，并按照升序返回。

### 1. 排序

首先对数组进行排序，排序的规则是先按照与 `x` 的距离升序排列，如果距离相等，则按照元素大小升序排列。然后取排序后的数组的前 `k` 个元素，再按照升序排序。

``` js
var findClosestElements = function (arr, k, x) {
  return arr.toSorted((a, b) => Math.abs(a - x) - Math.abs(b - x) || a - b).slice(0, k).sort((a, b) => a - b);
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

### 2. 双指针

因为数组已排序，可以使用双指针从两端向中间收缩，移除距离较远的那个元素，保留最接近的 `k` 个元素。

``` js
var findClosestElements = function (arr, k, x) {
  let l = 0;
  let r = arr.length - 1;

  while (r - l + 1 > k) {
    if (Math.abs(arr[l] - x) > Math.abs(arr[r] - x)) {
      l++;
    } else {
      r--;
    }
  }

  return arr.slice(l, r + 1);
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 3. 二分查找

由于有序数组最靠近 `x` 的 `k` 个数是一个连续子数组，所以使用二分查找查找大小为 `k` 的窗口的左边界，使窗口内的元素最接近 `x`。

每次二分查找，我们比较 `x - arr[mid]` 与 `arr[mid + k] - x`，如果前者大于后者，说明右侧距离更近，移动左边界，否则移动右边界。

``` js
var findClosestElements = function (arr, k, x) {
  let lo = 0;
  let hi = arr.length - k;

  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);
    if (x - arr[mid] > arr[mid + k] - x) {
      lo = mid + 1;
    } else {
      hi = mid;
    }
  }

  return arr.slice(lo, lo + k);
};
```

时间复杂度：O(log(n - k))，空间复杂度：O(1)。

## [4. 寻找两个正序数组的中位数 🔴](https://leetcode.cn/problems/median-of-two-sorted-arrays/description/)

题目要求：给定两个**已排序**的数组 `nums1` 和 `nums2`，找出并返回这两个数组的中位数，如果两数组总长度是奇数，返回中位数，如果是偶数，则返回中间两个数的平均数。要求算法的时间复杂度为 `O(log(m + n))`。

### 1. 合并 + 排序（不符合时间复杂度要求）

最直观的解法是先合并数组，然后排序后根据总长度是奇数还是偶数计算中位数。

``` js
var findMedianSortedArrays = function (nums1, nums2) {
  const merged = [...nums1, ...nums2];
  const len = merged.length;

  merged.sort((a, b) => a - b);

  if (len % 2 === 0) {
    return (merged[(len / 2) - 1] + merged[len / 2]) / 2;
  } else {
    return merged[Math.floor(len / 2)];
  }
};
```

时间复杂度：O((m + n)log(m + n))，空间复杂度：O(m + n)。

### 2. 双指针合并（不符合时间复杂度要求）

由于题目给的是两个有序数组，我们可以对上一个题解进行优化，利用有序数组的特性，使用双指针合并两个有序数组，然后找出中位数。

``` js
var findMedianSortedArrays = function (nums1, nums2) {
  const merged = mergeArrays(nums1, nums2);
  const len = merged.length;

  merged.sort((a, b) => a - b);

  if (len % 2 === 0) {
    return (merged[(len / 2) - 1] + merged[len / 2]) / 2;
  } else {
    return merged[Math.floor(len / 2)];
  }
};

const mergeArrays = (nums1, nums2) => {
  const sorted = [];
  const m = nums1.length;
  const n = nums2.length;
  let i = 0;
  let j = 0;

  while (i < m && j < n) {
    if (nums1[i] < nums1[j]) {
      sorted.push(nums1[i++]);
    } else {
      sorted.push(nums2[j++]);
    }
  }

  while (i < m) sorted.push(nums1[i++]);
  while (j < n) sorted.push(nums2[j++]);

  return sorted;
};
```

时间复杂度：O(m + n)，空间复杂度：O(m + n)。

### 3. 二分查找 👍

由于两个数组是正序的，中位数的左半部分和右半部分分别在两个数组中。通过二分查找在较短数组上确定分割点，然后将两个数组划分为左右两部分，使得左半部分的元素都小于右半部分的元素，同时左半部分的元素个数等于右半部分或多一个（当总元素个数为奇数时）。

``` js
var findMedianSortedArrays = function (nums1, nums2) {
  if (nums1.length > nums2.length) {
    [nums1, nums2] = [nums2, nums1]; // 始终保持第一个元素长度最小
  }

  const m = nums1.length;
  const n = nums2.length;
  const len = m + n;
  let lo = 0;
  let hi = m;

  while (lo <= hi) {
    const i = Math.floor((lo + hi) / 2); // nums1 切分位置
    const j = Math.floor((len + 1) / 2) - i; // nums2 切分位置

    const nums1L = i === 0 ? -Infinity : nums1[i - 1]; // nums1 左侧最大值
    const nums1R = i === m ? Infinity : nums1[i]; // nums1 右侧最小值
    const nums2L = j === 0 ? -Infinity : nums2[j - 1]; // nums2 左侧最大值
    const nums2R = j === n ? Infinity : nums2[j]; // nums2 右侧最小值

    // 找到合适的切分位置
    if (nums1L <= nums2R && nums2L <= nums1R) {
      if (len % 2 === 0) {
        return (Math.max(nums1L, nums2L) + Math.min(nums1R, nums2R)) / 2;
      } else {
        return Math.max(nums1L, nums2L);
      }
    } else if (nums1L > nums2R) {
      hi = i - 1; // 太大，左移
    } else {
      lo = i + 1; // 太小，右移
    }
  }

  return 0;
};
```

时间复杂度：O(log(min(m, n)))，空间复杂度：O(1)。