# 边界查找问题

## [34. 在排序数组中查找元素的第一个和最后一个位置 🟡 ❤](https://leetcode.cn/problems/find-first-and-last-position-of-element-in-sorted-array/description/)

题目要求：给定一个按照升序排列的整数数组 `nums`，和一个目标值 `target`。找出给定目标值在数组中的第一次出现位置和最后一次出现位置。实现的算法时间复杂度必须为 `O(logn)`。

### 二分查找

分别使用两次二分查找来定位目标值的第一个和最后一个位置。

``` js
var searchRange = function (nums, target) {
  const firstIndex = findFirstIndex(nums, target);
  const lastIndex = findLastIndex(nums, target);

  return [firstIndex, lastIndex];
};

const findFirstIndex = (nums, target) => {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo <= hi) {
    let mid = Math.floor((lo + hi) / 2);
    if (nums[mid] < target) {
      lo = mid + 1;
    } else {
      hi = mid - 1;
    }
  }

  return lo < nums.length && nums[lo] === target ? lo : -1;
};

const findLastIndex = (nums, target) => {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo <= hi) {
    let mid = Math.floor((lo + hi) / 2);
    if (nums[mid] <= target) {
      lo = mid + 1;
    } else {
      hi = mid - 1;
    }
  }
  return hi >= 0 && nums[hi] === target ? hi : -1;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

## [35. 搜索插入位置 🟢 ❤](https://leetcode.cn/problems/search-insert-position/description/)

题目要求：给定一个排序数组 `nums` 和一个目标值 `target`，在给定排序数组中查找目标值并返回其索引。如果目标值未找到，返回它将会被按顺序插入的位置。实现的算法时间复杂度必须为 `O(logn)`。

### 1. 标准二分查找

使用标准的二分查找，最后返回 `lo` 即为插入的位置。

``` js
var searchInsert = function (nums, target) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);
    if (nums[mid] === target) return mid;
    if (nums[mid] < target) {
      lo = mid + 1;
    } else {
      hi = mid - 1;
    }
  }

  return lo;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

### 2. 左边界二分查找

查找第一个等于 `target` 的位置即为插入位置。

``` js
var searchInsert = function (nums, target) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);
    if (nums[mid] < target) {
      lo = mid + 1;
    } else {
      hi = mid - 1;
    }
  }

  return lo;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

## [278. 第一个错误的版本 🟢](https://leetcode.cn/problems/first-bad-version/description/)

题目要求：尽可能少的调用判断版本号是否错误的API `isBadVersion(version)`，找出第一个错误的版本。

### 1. 标准二分查找

使用标准的二分查找，根据 `isBadVersion(version)` 的结果更新边界：

- 如果当前版本是错误版本，则第一个坏版本在左边；
- 如果当前版本是正确版本，则第一个坏版本在右边。

最后当循环结束后，`lo` 即为我们要找的第一个错误版本。

``` js
var solution = function (isBadVersion) {
  return function (n) {
    let lo = 1;
    let hi = n;

    while (lo <= hi) {
      const mid = Math.floor((lo + hi) / 2);
      if (isBadVersion(mid)) {
        hi = mid - 1;
      } else {
        lo = mid + 1;
      }
    }

    return lo;
  };
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

### 2. 左边界二分查找

我们可以在标准二分查找的基础上进行优化，使用左边界二分查找来找到第一个错误版本，从而减少一次比较。

``` js
var solution = function (isBadVersion) {
  return function (n) {
    let lo = 1;
    let hi = n;

    while (lo < hi) {
      const mid = Math.floor((lo + hi) / 2);
      if (isBadVersion(mid)) {
        hi = mid;
      } else {
        lo = mid + 1;
      }
    }

    return lo;
  };
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。