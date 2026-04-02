# 其他问题

## [540. 有序数组中的单一元素 🟡 👍](https://leetcode.cn/problems/single-element-in-a-sorted-array/description/)

题目要求：在一个有序数组中，除了一个元素只出现一次，其余元素都出现两次，找出并返回那个只出现一次的元素。

实现的算法必须满足 `O(log n)` 的时间复杂度和 `O(1)` 的空间复杂度。

### 二分查找

利用数组有序的特性，通过二分查找比较中间元素与其相邻元素的位置关系，确定单一元素的位置。

1. 我们通过观察可以发现，成对元素在单一元素出现之前是奇偶下标排列的。

- 如果 `mid` 的下标为偶数，并且其对应的元素与下一个元素相等，那么单一元素在右侧，否则在左侧（包含 `mid`）；
- 如果 `mid` 的下标为奇数，并且其对应的元素与上一个元素相等，那么单一元素在右侧，否则在左侧。

``` js
var singleNonDuplicate = function (nums) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (mid % 2 === 0) {
      if (nums[mid] === nums[mid + 1]) {
        lo = mid + 2;
      } else {
        hi = mid;
      }
    } else {
      if (nums[mid] === nums[mid - 1]) {
        lo = mid + 1;
      } else {
        hi = mid;
      }
    }
  }

  return nums[lo];
};
```

2. 不过，以上代码存在代码冗余，我们可以始终让 `mid` 为偶数，这样只需判断 `mid` 对应元素与下一个元素是否相等，来统一调整左右边界。

``` js
var singleNonDuplicate = function (nums) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo < hi) {
    let mid = Math.floor((lo + hi) / 2);

    if (mid % 2 === 1) mid--; // 保证 mid 为偶数

    if (nums[mid] === nums[mid + 1]) {
      lo = mid + 2;
    } else {
      hi = mid;
    }
  }

  return nums[lo];
};
```

3. 我们还可以通过异或运算，`mid ^ 1`：

- 当 `mid` 为偶数时，`mid ^ 1` 等于`mid + 1`；当 `mid` 为奇数时，`mid ^ 1` 等于`mid - 1` ，如果它们相等，那么单一元素在右侧，否则在左侧（包括 `mid`）。

``` js
var singleNonDuplicate = function (nums) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (nums[mid] === nums[mid ^ 1]) {
      lo = mid + 1;
    } else {
      hi = mid;
    }
  }

  return nums[lo];
};
```

时间复杂度：O(log n)，空间复杂度：O(1)。

## [1894. 找到需要补充粉笔的学生编号 🟡](https://leetcode.cn/problems/find-the-student-that-will-replace-the-chalk/description/)

题目要求：给定一个数组 `chalk`，表示每个学生依次消耗的粉笔数量，以及一个整数 `k` 表示现有粉笔总数。学生按顺序循环使用粉笔，当粉笔不足以满足当前学生需求时，返回该学生的编号。

### 1. 暴力法（超时）

直接模拟粉笔分配过程，循环遍历学生数组，每次减去当前学生所需的粉笔数，直到剩余粉笔数不足，返回当前学生的下标。

``` js
var chalkReplacer = function (chalk, k) {
  const n = chalk.length;
  let i = 0;

  while (k >= chalk[i % n]) {
    k -= chalk[i % n];
    i++;
  }

  return i % n;
};
```

由于每一轮学生消耗粉笔的数量是重复的，所以我们可以先遍历计算一轮循环所需粉笔数量 `sum`，然后用 `k` 对 `sum` 取模求余，得到最后一轮实际的粉笔数量，再对其进行遍历，找到第一个需求大于剩余粉笔的学生。

``` js
var chalkReplacer = function (chalk, k) {
  const sum = chalk.reduce((a, b) => a + b, 0);
  k %= sum;

  for (let i = 0; i < chalk.length; i++) {
    if (k < chalk[i]) return i;
    k -= chalk[i];
  }

  /* let i = 0;

  while (k >= chalk[i]) {
    k -= chalk[i];
    i++;
  }

  return i; */
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 2. 前缀和 + 二分查找

对于上一题解的第二次遍历，我们可以使用二分查找来进行加速。

在对 `chalk` 的遍历过程中，我们先构建前缀和数组 `prefixSum`，`prefixSum[i]` 表示前 `i + 1` 个学生总消耗，同样也需要取模求出最后一轮剩余的粉笔数量，然后使用二分查找在前缀和数组中找到第一个需求大于剩余粉笔的学生。

在每次二分查找的过程中，我们比较当前学生需要的粉笔数量与剩余的粉笔数量 `k`。如果当前学生需要的粉笔数量小于等于剩余的粉笔数量，则说明当前学生的编号可能是解。然后，我们继续在左半部分继续查找；如果当前学生需要的粉笔数量大于剩余的粉笔数量，则说明解位于右半部分，我们继续在右半部分查找。

``` js
var chalkReplacer = function (chalk, k) {
  const n = chalk.length;
  const prefixSum = new Array(n).fill(0);
  prefixSum[0] = chalk[0];

  for (let i = 1; i < n; i++) {
    prefixSum[i] = prefixSum[i - 1] + chalk[i];
  }

  k %= prefixSum[n - 1];
  let lo = 0;
  let hi = n - 1;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);
    if (prefixSum[mid] > k) {
      hi = mid - 1;
    } else {
      lo = mid + 1;
    }
  }

  return lo;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

TODO:关于前缀和的更多信息，我们将在下一专题进行详细介绍。

## [面试题 10.05 稀疏数组搜索 🟢](https://leetcode.cn/problems/sparse-array-search-lcci/description/)

题目要求：在一个可能包含空字符串的**有序**字符串数组中查找目标字符串。如果存在则返回其下标，否则返回 `-1`。

### 二分查找

由于字符串数组是有序的，但存在空字符串，使用二分查找时需要处理空字符串的情况。

在二分查找过程中，我们提前收缩边界跳过空字符串，如果 `mid` 遇到空字符串，我们无法直接进行比较，可以左右扩散查找最近非空字符串再比较。

``` js
var findString = function (words, s) {
  let lo = 0;
  let hi = words.length - 1;

  while (lo <= hi) {
    // 跳过空字符串
    while (lo <= hi && words[lo] === '') lo++;
    while (lo <= hi && words[hi] === '') hi--;

    let mid = Math.floor((lo + hi) / 2);
    let l = mid;
    let r = mid;
    while (true) {
      if (l < lo && r > hi) return -1;
      if (l >= lo && words[l] !== '') {
        mid = l;
        break;
      }
      if (r <= hi && words[r] !== '') {
        mid = r;
        break;
      }
      l--;
      r++;
    }

    if (words[mid] === s) return mid;
    if (words[mid] > s) {
      hi = mid - 1;
    } else {
      lo = mid + 1;
    }
  }

  return -1;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

## [面试题 08.03 魔术索引 🟢](https://leetcode.cn/problems/magic-index-lcci/description/)

题目要求：在一个**有序**的数组中（可能存在重复元素）找到下标与对应元素一致的魔法索引并将其返回。如果没有则返回 `-1`，若有多个则返回索引值最小的一个。

### 1. 暴力法

线性遍历，寻找与下标对应的第一个元素。

``` js
var findMagicIndex = function (nums) {
  for (let i = 0; i < nums.length; i++) {
    if (nums[i] === i) return i;
  }

  return -1;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 2. 跳跃法

利用数组有序性进行跳跃，减少不必要的遍历，提前找出最小索引。

``` js
var findMagicIndex = function (nums) {
  let i = 0;

  while (i < nums.length) {
    if (nums[i] === i) return i;

    // i = nums[i] > i ? nums[i] : i + 1;
    i = Math.max(nums[i], i + 1); // 跳过不可能的索引
  }

  return -1;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 3. 二分查找（递归剪枝）

由于题目可能存在重复元素，使用标准的二分查找并不能保证找到最小的魔法索引，所以我们需要使用递归剪枝来收缩搜索空间。为了能够更早找出最小索引，我们先搜索左半部分，然后检查中间元素，最后搜索右半部分。递归的重复以上过程，直到找到最小索引为止。

在搜索左右半部分时，我们需要使用剪枝来跳过不可能存在魔法索引的区间，防止算法退化。

``` js
var findMagicIndex = function (nums) {
  const binarySearch = (lo, hi) => {
    if (lo > hi) return -1;

    const mid = Math.floor((lo + hi) / 2);
    let l = binarySearch(lo, Math.min(mid - 1, nums[mid]));
    if (l !== -1) return l;

    if (nums[mid] === mid) return mid;

    return binarySearch(Math.max(mid + 1, nums[mid]), hi);
  };
  return binarySearch(0, nums.length - 1);
};
```

时间复杂度：O(logn)，空间复杂度：O(logn)。

## [剑指 Offer 53 - II 0～n-1中缺失的数字 🟢](https://leetcode.cn/problems/que-shi-de-shu-zi-lcof/description/)

题目要求：给定一个包含 `0` 到 `n-1` 的有序数组，找出其中缺失的数字。

如果给定数组是无序的，可以使用 [268. 丢失的数字](/array/missing-number-problems.md#268-丢失的数字--) 的解题思路来解题，但题目给定的是一个有序数组，所以我们可以使用二分查找来解题。

### 二分查找

利用数组有序的特性，通过二分查找比较中间元素与其下标的位置关系，确定缺失数字的位置。

每次计算中间元素的下标 `mid`，比较 `nums[mid]` 与 `mid`，如果相等，说明缺失元素在右侧；否则在左侧。最后返回左边界。

``` js
var missingNumber = function(nums) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);
    
    if (nums[mid] === mid) {
      lo = mid + 1;
    } else {
      hi = mid - 1;
    }
  }

  return lo;
};
```