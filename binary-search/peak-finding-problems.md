# 寻找峰值问题

## [852. 山脉数组的峰顶索引 🟡 ❤](https://leetcode.cn/problems/peak-index-in-a-mountain-array/description/) 

题目要求：在一个山脉数组（先递增后递减的数组）中找到峰顶元素并返回其下标。实现的算法时间复杂度为 `O(logn)`。

### 二分查找

每次二分查找比较中间元素与其右侧相邻元素的大小关系，从而确定峰顶所在的方向：

- 如果 `nums[mid] < nums[mid + 1]`，说明峰值再右侧。
- 否则说明峰值在左侧（包括中间元素）。

最后当 `lo` 与 `hi` 相遇时，说明我们找到了一个峰值元素。

``` js
var peakIndexInMountainArray = function (arr) {
  let lo = 0;
  let hi = arr.length - 1;

  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (arr[mid] < arr[mid + 1]) {
      lo = mid + 1;
    } else {
      hi = mid;
    }
  }

  return lo;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

## [162. 寻找峰值 🟡](https://leetcode.cn/problems/find-peak-element/description/)

题目要求：在一个数组中找到任意一个峰值元素（严格大于左右相邻元素的元素）并返回其下标。实现的算法时间复杂度为 `O(logn)`。

### 二分查找

其实此题的思路和题解与上一题相同，只不过此题存在多个峰值，我们只需找到任意一个即可。

``` js
var findPeakElement = function (nums) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (nums[mid] < nums[mid + 1]) {
      lo = mid + 1;
    } else {
      hi = mid;
    }
  }

  return lo;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。
