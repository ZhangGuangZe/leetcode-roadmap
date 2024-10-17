# 子数组问题

## 所有子数组

### [713. 乘积小于 K 的子数组 🟡 ❤](https://leetcode.cn/problems/subarray-product-less-than-k/description/)

题目要求：在一个整数数组 `nums` 中，找出元素乘积小于 `k` 的所有子数组，并返回这些子数组的数目。

#### 1. 暴力法

枚举所有子数组，计数每个子数组的乘积，记录乘积小于 `k` 的子数组的数目。

``` js
var numSubarrayProductLessThanK = function (nums, k) {
  if (k <= 1) return 0;

  const n = nums.length;
  let res = 0;

  for (let i = 0; i < n; i++) {
    let prod = 1;

    for (let j = i; j < n; j++) {
      prod *= nums[j];
      if (prod >= k) break;
      res++;
    }
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 2. 滑动窗口 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，`prod` 变量用于记录滑动窗口内元素的乘积。

遍历数组，每次向右移动右边界扩大窗口，将当前元素加入滑动窗口，同时更新乘积。如果乘积大于等于 `k`，则向右移动左边界收缩窗口，直到乘积小于 `k` 为止。如果乘积小于 `k`，则更新乘积小于 `k` 的子数组的数目。

``` js
var numSubarrayProductLessThanK = function (nums, k) {
  if (k <= 1) return 0;
  
  const n = nums.length;
  let res = 0;
  let prod = 1;
  let l = 0;
  let r = 0;

  while (r < n) {
    // 扩大窗口，更新窗口内元素的乘积
    prod *= nums[r];

    // 不满足条件，向右移动左边界收缩窗口
    while (prod >= k) {
      prod /= nums[l];
      l++;
    }

    res += (r - l + 1); // 统计当前窗口所有子数组的数量

    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。


### [2962. 统计最大元素出现至少 K 次的子数组 🟡](https://leetcode.cn/problems/count-subarrays-where-max-element-appears-at-least-k-times/description/)

题目要求：在一个数组 `nums` 中，找出最大元素至少出现 `k` 次的所有子数组，并返回这些子数组的数目。

#### 1. 暴力法（超时）

首先找到数组中的最大元素，然后遍历所有子数组，对于每个子数组，统计其中最大元素出现的次数，如果次数大于等于 `k`，则更新子数组的数目。

``` js
var countSubarrays = function (nums, k) {
  const n = nums.length;
  let res = 0;
  const maxElement = Math.max(...nums);

  for (let i = 0; i < n; i++) {
    let maxCount = 0;

    for (let j = i; j < n; j++) {
      if (nums[j] === maxElement) {
        maxCount++;
      }

      if (maxCount >= k) res++;
    }
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 2. 滑动窗口 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，`maxCount` 用于记录滑动窗口中最大元素的出现次数。

遍历数组，每次向右移动右边界扩大窗口，将当前元素加入滑动窗口，同时更新滑动窗口中最大元素的出现次数。如果滑动窗口中最大元素的出现次数大于等于 `k`，则向右移动左边界收缩窗口，直到滑动窗口中最大元素的出现次数小于 `k` 为止。统计满足条件的子数组的数目。

``` js
var countSubarrays = function (nums, k) {
  const n = nums.length;
  let res = 0;
  const maxElement = Math.max(...nums);
  let maxCount = 0;
  let l = 0;
  let r = 0;

  while (r < n) {
    // 扩大窗口，统计当前窗口最大元素出现的次数
    if (nums[r] === maxElement) maxCount++;

    // 满足条件，尝试向右滑动左指针收缩窗口
    while (maxCount >= k) {
      if (nums[l] === maxElement) maxCount--;
      l++;
    }

    res += l; // 统计当前窗口满足某个特定条件的子数组的数量

    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## 最短子数组

### [209. 长度最小的子数组 🟡 ❤](https://leetcode.cn/problems/minimum-size-subarray-sum/description/)

题目要求：在一个正整数数组 `nums` 中找出和大于等于 `target` 的最短子数组并返回其长度。

#### 1. 暴力法（超时）

枚举所有子数组，计算子数组的和，判断是否大于等于 `target`，记录最短子数组的长度。

``` js
var minSubArrayLen = function (target, nums) {
  const n = nums.length;
  let res = Infinity;

  for (let i = 0; i < n; i++) {
    let sum = 0;

    for (let j = i; j < n; j++) {
      sum += nums[j];

      if (sum >= target) {
        res = Math.min(res, j - i + 1);
        break;
      }
    }
  }

  return res === Infinity ? 0 : res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 2. 前缀和 + 二分查找

我们使用前缀和 `prefixSums` 数组对 `nums` 进行预处理，其中 `prefixSums[i]` 表示 `nums` 前 i 个元素之和。这样 `prefixSums[i] - prefixSums[j]` 就表示从 `nums[j, ..., i]` 的子数组中所有元素的和。

如果 `prefixSums[i] - prefixSums[j] >= target`，则说明 `nums[j, ..., i]` 的是满足条件的子数组，但不一定就是最短的，我们需要继续向右移动 `j`，直到找到满足条件的子数组。我们可以使用双层循环到满足条件的子数组。

``` js
var minSubArrayLen = function (target, nums) {
  const n = nums.length;
  let res = Infinity;

  const prefixSums = new Array(n + 1).fill(0);
  for (let i = 1; i <= n; i++) {
    prefixSums[i] = prefixSums[i - 1] + nums[i - 1];
  }

  for (let i = 1; i <= n; i++) {
    for (let j = 0; j < i; j++) {
      if (prefixSums[i] - prefixSums[j] < target) break;
      res = Math.min(res, i - j);
    }
  }

  return res === Infinity ? 0 : res;
};
```

不过，因为数组是一个正整数数组，所以 `prefixSums` 是单调递增的，我们可以使用二分查找快速查找满足条件的最短子数组。

``` js
var minSubArrayLen = function (target, nums) {
  const n = nums.length;
  let res = Infinity;

  const prefixSums = new Array(n + 1).fill(0);
  for (let i = 1; i <= n; i++) {
    prefixSums[i] = prefixSums[i - 1] + nums[i - 1];
  }

  for (let i = 1; i <= n; i++) {
    const j = binarySearch(prefixSums, 0, i, prefixSums[i] - target);
    if (j > 0) {
      res = Math.min(res, i - j + 1);
    }
  }

  return res === Infinity ? 0 : res;
};

const binarySearch = (nums, lo, hi, target) => {
  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);
    if (nums[mid] <= target) lo = mid + 1;
    else hi = mid - 1;
  }

  return lo;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

TODO：关于前缀和和二分查找的更多信息，我们可以在后面的相关专题中详细介绍。

#### 3. 滑动窗口 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，`sum` 变量用于记录滑动窗口内元素的和。

遍历数组，每次向右移动右边界扩大窗口，将当前元素加入滑动窗口，同时更新滑动窗口内的元素和。如果滑动窗口内的元素和大于等于 `target`，则向右移动左边界收缩窗口，并尝试找出符合条件的最短子数组，直到滑动窗口内的元素和小于 `target` 为止。

``` js
var minSubArrayLen = function (target, nums) {
  const n = nums.length;
  let res = Infinity;
  let sum = 0;
  let l = 0;
  let r = 0;

  while (r < n) {
    // 扩大窗口，更新窗口内的元素和
    sum += nums[r];

    // 满足条件，尝试收缩窗口找出最短子数组
    while (sum >= target) {
      res = Math.min(res, r - l + 1);
      sum -= nums[l];
      l++;
    }

    r++;
  }

  return res === Infinity ? 0 : res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## 最长子数组

### [1004. 最大连续1的个数 III 🟡](https://leetcode.cn/problems/max-consecutive-ones-iii/description/)

题目要求：在一个二进制数组 `nums` 中，找出最多翻转 `k` 次 `0` 后连续 `1` 的最长子数组并返回其长度。

#### 1. 暴力法（超时）

枚举所有子数组，使用 `zeros` 和 `count` 变量记录当前子数组中 `0` 的个数和子数组的长度，如果 `zeros` 大于 `k`，则跳出循环。否则，更新最长子数组的长度。

``` js
var longestOnes = function (nums, k) {
  const n = nums.length;
  let res = 0;

  for (let i = 0; i < n; i++) {
    let zeros = 0;
    let count = 0;
    
    for (let j = i; j < n; j++) {
      if (nums[j] === 1) {
        count++;
      } else {
        zeros++;
        if (zeros > k) break;
        count++;
      }

      res = Math.max(res, count);
    }
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 2. 滑动窗口 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，`zeros` 变量用于记录滑动窗口中 `0` 的个数。

遍历数组，每次向右移动右边界扩大窗口，将当前元素加入滑动窗口，同时更新滑动窗口中 `0` 的个数。如果滑动窗口中 `0` 的个数大于 `k`，则向右移动左边界收缩窗口，直到滑动窗口中 `0` 的个数小于等于 `k` 为止。更新当前窗口连续 `1` 的最长子数组的长度。

``` js
var longestOnes = function (nums, k) {
  const n = nums.length;
  let res = 0;
  let zeros = 0;
  let l = 0;
  let r = 0;

  while (r < n) {
    // 扩大窗口，当前窗口 0 的个数
    if (nums[r] === 0) zeros++;

    // 不满足条件，向右移动左边界收缩窗口
    while (zeros > k) {
      if (nums[l] === 0) zeros--;
      l++;
    }

    res = Math.max(res, r - l + 1);

    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [2958. 最多 K 个重复元素的最长子数组 🟡](https://leetcode.cn/problems/length-of-longest-subarray-with-at-most-k-frequency/description/)

题目要求：在一个数组 `nums` 中，找出所有元素最多重复 `k` 次的最长子数组并返回其长度。

#### 1. 暴力法（超时）

枚举所有子数组，对于每个子数组，使用哈希表统计当前子数组中每个元素的出现次数，如果当前元素出现次数大于 `k`，则跳出循环。否则，更新最长子数组的长度。

``` js
var maxSubarrayLength = function (nums, k) {
  const n = nums.length;
  let res = 0;

  for (let i = 0; i < n; i++) {
    const map = new Map();

    for(let j = i; j < n; j++) {
      map.set(nums[j], (map.get(nums[j]) ?? 0) + 1);

      const maxCount = Math.max(...map.values());
      if (maxCount > k) break;
      res = Math.max(res, j - i + 1);
    }
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(n)。

#### 2. 滑动窗口 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，`map` 用于记录滑动窗口中每个元素的出现次数。

遍历数组，每次向右移动右边界扩大窗口，将当前元素加入滑动窗口，同时更新滑动窗口中当前元素的出现次数。如果滑动窗口中当前元素的出现次数大于 `k`，则向右移动左边界收缩窗口，直到滑动窗口中当前元素的出现次数小于等于 `k` 为止。更新当前窗口子数组的长度。

``` js
var maxSubarrayLength = function (nums, k) {
  const n = nums.length;
  let res = 0;
  const map = new Map();
  let l = 0;
  let r = 0;

  while (r < n) {
    // 扩大窗口，更新当前窗口元素出现的次数
    map.set(nums[r], (map.get(nums[r]) ?? 0) + 1);

    // 不满足条件，向右移动左边界收缩窗口
    while (map.get(nums[r]) > k) {
      map.set(nums[l], map.get(nums[l]) - 1);
      l++;
    }

    res = Math.max(res, r - l + 1);

    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## 无重复子数组

### [2461. 长度为 K 子数组中的最大和 🟡](https://leetcode.cn/problems/maximum-sum-of-distinct-subarrays-with-length-k/description/)

题目要求：在一个数组 `nums` 中，找出长度为 `k` 且不包含重复元素且子数组和最大的子数组，返回最大子数组和。如果不存在这样的子数组，返回 `0`。

#### 1. 暴力法（超时）

枚举所有长度为 `k` 的子数组，对于每个子数组，使用一个哈希表 `seen` 记录当前子数组中已经出现的元素，如果当前元素已经出现过，则将子数组和重置为 `0`，否则计算子数组和并更新最大子数组和。

``` js
var maximumSubarraySum = function (nums, k) {
  const n = nums.length;
  let res = 0;

  for (let i = 0; i <= n - k; i++) {
    let sum = 0;
    const seen = new Set();

    for (let j = 0; j < k; j++) {
      if (seen.has(nums[i + j])) {
        sum = 0;
        break;
      }
      seen.add(nums[i + j]);
      sum += nums[i + j];
    }

    res = Math.max(res, sum);
  }

  return res;
};
```

时间复杂度：O(nk)，空间复杂度：O(n)。

#### 2. 滑动窗口 + Map 👍

维护一个固定大小的滑动窗口。使用变量 `sum` 记录当前窗口元素和，哈希表 `map` 记录当前窗口中每个元素的出现次数。

每次窗口向右滑动一位，如果 `map` 的大小等于 `k`，则说明当前子数组没有重复元素，更新子数组的元素和。继续向右滑动窗口寻找满足条件的最大子数组和，直到窗口滑动到最右侧为止。

``` js
var maximumSubarraySum = function (nums, k) {
  const n = nums.length;
  let res = 0;
  let sum = 0;
  const map = new Map();

  for (let i = 0; i < k; i++) {
    sum += nums[i];
    map.set(nums[i], (map.get(nums[i]) ?? 0) + 1);
  }
  if (map.size === k) res = sum;

  for (let i = k; i < n; i++) {
    // 移动窗口右边界
    map.set(nums[i], (map.get(nums[i]) ?? 0) + 1);
    // 移动窗口左边界
    map.set(nums[i - k], map.get(nums[i - k]) - 1);
    if (map.get(nums[i - k]) === 0) map.delete(nums[i - k]);

    sum += nums[i] - nums[i - k];

    // 满足条件
    if (map.size === k) {
      res = Math.max(res, sum);
    }
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

#### 3. 滑动窗口 + Set 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，使用变量 `sum` 记录当前窗口元素和，哈希集合 `seen` 记录当前窗口中已经出现的元素。

遍历数组，每次向右移动右边界扩大窗口，将当前元素加入滑动窗口，同时计算当前窗口元素和。

如果当前元素已经在滑动窗口中，则向右移动左边界收缩窗口，直到滑动窗口中不再包含当前元素为止。

如果当前窗口大小为 `k` 且不包含重复元素，则更新最大子数组和，并向右移动左边界收缩窗口继续寻找满足条件的子数组。

``` js
var maximumSubarraySum = function (nums, k) {
  const n = nums.length;
  let res = 0;
  let sum = 0;
  let l = 0;
  let r = 0;
  const seen = new Set();

  while (r < n) {
    // 不满足条件，向右滑动左指针收缩窗口
    while (seen.has(nums[r])) {
      seen.delete(nums[l]);
      sum -= nums[l];
      l++;
    }

    // 扩大窗口
    seen.add(nums[r]);
    sum += nums[r];

    // 满足条件，尝试向右滑动左指针收缩窗口找出最大子数组和
    if (r - l + 1 === k) {
      res = Math.max(res, sum);
      seen.delete(nums[l]);
      sum -= nums[l];
      l++;
    }
    
    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## 最长公共子数组

### [718. 最长重复子数组 🟡](https://leetcode.cn/problems/maximum-length-of-repeated-subarray/description/)

题目要求：在两个数组 `nums1` 和 `nums2` 中，找到最长的公共子数组，并返回其长度。

#### 1. 暴力法（超时）

枚举所有可能的子数组，对于每个子数组，判断是否是公共子数组，如果是，则更新最长公共子数组的长度。

``` js
var findLength = function (nums1, nums2) {
  const m = nums1.length;
  const n = nums2.length;
  let res = 0;

  for (let i = 0; i < m; i++) {
    for (let j = 0; j < n; j++) {
      let k = 0;

      // 统计最长公共子数组的长度
      while (i + k < m && j + k < n && nums1[i + k] === nums2[j + k]) k++;

      res = Math.max(res, k);
    }
  }

  return res;
};
```

时间复杂度：O(n³)，空间复杂度：O(1)。

#### 2. 动态规划

我们使用一个二维数组 `dp` 来记录两个数组公共子数组的长度。

状态定义：`dp[i][j]` 表示 `nums1[0...i-1]` 和 `nums2[0...j-1]` 的最长公共子数组的长度。

状态转移方程：

$$
dp[i][j]=
\begin{cases}
0, & nums1[i - 1] \neq nums2[j - 1] \\
dp[i - 1][j - 1] + 1, & nums1[i - 1] = nums2[j - 1]
\end{cases}
$$

``` js
var findLength = function (nums1, nums2) {
  const m = nums1.length;
  const n = nums2.length;
  let res = 0;
  const dp = Array.from({ length: m + 1 }, () => new Array(n + 1).fill(0));

  for (let i = 1; i <= m; i++) {
    for (let j = 1; j <= n; j++) {
      if (nums1[i - 1] === nums2[j - 1]) {
        dp[i][j] = dp[i - 1][j - 1] + 1;
        res = Math.max(res, dp[i][j]);
      }
    }
  }

  return res;
};
```

时间复杂度：O(mn)，空间复杂度：O(mn)。

我们可以使用一维数组 `dp` 来优化空间复杂度。从右往左更新状态，避免因为覆盖问题造成错误。

``` js
var findLength = function (nums1, nums2) {
  const m = nums1.length;
  const n = nums2.length;
  const dp = new Array(n + 1).fill(0);
  let res = 0;

  for (let i = 1; i <= m; i++) {
    for (let j = n; j >= 1; j--) {
      if (nums1[i - 1] === nums2[j - 1]) {
        dp[j] = dp[j - 1] + 1;
        res = Math.max(res, dp[j]);
      } else {
        dp[j] = 0;
      }
    }
  }

  return res;
};
```

时间复杂度：O(mn)，空间复杂度：O(n)。

TODO：关于动态规划的更多信息，我们将在后面的相关专题中详细介绍。

#### 3. 滑动窗口 👍

通过将两个数组平移并对齐，计算不同对齐位置下公共子数组的长度，避免暴力枚举所有子数组。

首先，将两个数组对齐，将某个数组固定不动，逐步将另一个数组的起始位置向右滑动，从而找到两数组在不同滑动位置下的重叠部分。然后计算重叠部分公共子数组的长度。最后更新最大公共子数组的长度。

``` js
var findLength = function (nums1, nums2) {
  const m = nums1.length;
  const n = nums2.length;
  let res = 0;
  
  for (let i = 0; i < m; i++) {
    res = Math.max(res, maxCommonLength(nums1, nums2, i, 0));
  }

  for (let i = 0; i < n; i++) {
    res = Math.max(res, maxCommonLength(nums1, nums2, 0, i));
  }

  return res;
};

const maxCommonLength = (nums1, nums2, i, j) => {
  let res = 0;
  let k = 0;

  while (i < nums1.length && j < nums2.length) {
    if (nums1[i] === nums2[j]) {
      k++;
      res = Math.max(res, k);
    } else {
      k = 0;
    }
    i++;
    j++;
  }

  return res;
};
```

时间复杂度：O(mn)，空间复杂度：O(1)。
