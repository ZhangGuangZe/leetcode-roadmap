# 基础查找问题

## [704. 二分查找 🟢](https://leetcode-cn.com/problems/binary-search/)

题目要求： 在一个升序排列的整数数组中，查找目标值 `target` 并返回其索引。如果目标值不存在于数组中，返回 `-1`。

### 二分查找

通过不断缩小搜索范围来高效地找到目标值。

``` js
var search = function (nums, target) {
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

  return -1;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

## [374. 猜数字大小 🟢](https://leetcode-cn.com/problems/guess-number-higher-or-lower/)

题目要求：在 `1` 到 `n` 范围中猜随机选择数字 `peek`，根据系统提示（`0` 表示猜对了，`-1` 表示猜大了，`1` 猜小了）猜出选择的数字。

### 二分查找

我们使用二分查找来猜数字，通过不断调整猜测范围来找到正确的数字。

- 如果猜中了，则返回当前猜测的数字。
- 如果猜大了，则往当前猜测数字的左侧继续猜。
- 如果猜小了，则往当前猜测数字的右侧继续猜。

``` js
var guessNumber = function (n) {
  let lo = 1;
  let hi = n;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);
    const res = guess(mid);

    if (res === 0) return mid;
    if (res < 0) {
      hi = mid -1;
    } else {
      lo = mid + 1;
    }
  }
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。