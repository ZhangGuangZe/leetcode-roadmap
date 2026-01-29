# 旋转排序数组搜索问题

考察如何在局部有序的数组中使用二分查找。

## [33. 搜索旋转排序数组 🟡 ❤](https://leetcode.cn/problems/search-in-rotated-sorted-array/description/)

题目要求：在一个先升序再旋转的有序数组 `nums`（无重复元素）中查找目标值 `target`。如果存在返回它的下标，否则返回 `-1`。实现的算法时间复杂度必须为 `O(logn)`。

### 二分查找

我们使用二分查找，将数组分割成 `nums[lo, ..mid]` 和 `nums[mid + 1, ..hi]` 两部分，这时候可以发现，有一部分一定是有序的。因此，我们可以根据有序的那一部分，判断 `target` 是否在这一部分中：

如果左半部分是有序的：
  - 检查目标值是否在有序的左半部分。如果是则缩小范围在左半部分搜索，否则搜索右半部分。

如果右半部分是有序的：
  - 检查目标值是否在有序的右半部分。如果是则缩小范围在右半部分搜索，否则搜索左半部分。
  
虽然数组被旋转了，但通过比较中间元素与左右边界，总能确定一半是有序的。

``` js
var search = function (nums, target) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (nums[mid] === target) return mid;

    // 左半部分有序
    if (nums[lo] <= nums[mid]) {
      if (nums[lo] <= target && target < nums[mid]) {
        hi = mid - 1;
      } else {
        lo = mid + 1;
      }
    } else { // 右半部分有序
      if (nums[mid] < target && target <= nums[hi]) {
        lo = mid + 1;
      } else {
        hi = mid - 1;
      }
    }
  }

  return -1;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

## [81. 搜索旋转排序数组 II 🟡](https://leetcode.cn/problems/search-in-rotated-sorted-array-ii/description/)

题目要求：在一个先升序再旋转的有序数组 `nums`（可能包含重复元素）中查找目标值 `target`。如果存在返回 `true`，否则返回 `false`。实现的算法时间复杂度必须为 `O(logn)`。

### 二分查找

我们使用与上一题类似的二分查找方法。但由于数组中可能包含重复元素，这会影响二分判断左右部分是否有序，这需要增加特殊判断来处理重复元素。当 `nums[lo] === nums[mid]` 时，无法确定哪边有序，只能将 `lo` 向右移动一位以跳过重复元素缩小搜索范围。

``` js
var search = function (nums, target) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (nums[mid] === target) return true;

    // 无法判断哪边有序
    if (nums[lo] === nums[mid]) {
      lo++;
      continue;
    }

    // 左侧有序
    if (nums[lo] <= nums[mid]) {
      if (nums[lo] <= target && target < nums[mid]) {
        hi = mid - 1;
      } else {
        lo = mid + 1;
      }
    } else { // 右侧有序
      if (nums[mid] < target && target <= nums[hi]) {
        lo = mid + 1;
      } else {
        hi = mid - 1;
      }
    }
  }

  return false;
};
```

时间复杂度：O(logn) ，空间复杂度：O(1)。

## [153. 寻找旋转排序数组中的最小值 🟡](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array/description/)

题目要求：在一个先升序再旋转的有序数组 `nums`（无重复元素）中找到最小值。实现的算法时间复杂度必须为 `O(logn)`。

### 二分查找

我们使用二分查找，通过比较中间元素与右边界元素的大小关系来判断最小值在左半部分还是右半部分：

- 如果 `nums[mid] > nums[hi]`，说明最小值在右半部分。
- 如果 `nums[mid] < nums[hi]`，说明最小值在左半部分（包括 `mid`）。

最后返回 `nums[lo]` 即为最小值。

``` js
var findMin = function (nums) {
  let lo = 0;
  let hi = nums.length - 1;

  // 数组没有旋转
  if (nums[lo] < nums[hi]) return nums[lo];

  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (nums[mid] < nums[hi]) {
      hi = mid;
    } else {
      lo = mid + 1;
    }
  }

  return nums[lo];
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

## [154. 寻找旋转排序数组中的最小值 II 🔴 ❤](https://leetcode.cn/problems/find-minimum-in-rotated-sorted-array-ii/description/)

题目要求：在一个先升序再旋转的有序数组 `nums`（可能包含重复元素）中找到最小值。实现的算法时间复杂度必须为 `O(logn)`。

### 二分查找

我们使用与上一题类似的二分查找方法。但由于数组中可能包含重复元素，这会影响二分判断最小值所在的部分，这需要增加特殊判断来处理重复元素。当 `nums[mid] === nums[hi]` 时，无法确定最小值在哪边，只能将 `hi` 向左移动一位以跳过重复元素缩小搜索范围。

``` js
var findMin = function (nums) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (nums[mid] < nums[hi]) {
      hi = mid;
    } else if (nums[mid] > nums[hi]) {
      lo = mid + 1;
    } else {
      hi--;
    }
  }

  return nums[lo];
};
```

时间复杂度：O(logn) ，空间复杂度：O(1)。