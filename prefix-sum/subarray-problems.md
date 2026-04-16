# 子数组问题

## [525. 连续数组 🟡 ❤](https://leetcode.cn/problems/contiguous-array/description/)

题目要求：给定一个只包含 `0` 和 `1` 的数组，找出一个连续子数组，使得其中 `0` 和 `1` 的数量相同，并返回该子数组的最大长度。

### 1. 暴力法（超时）

双重循环枚举所有可能的子数组，统计子数组中 `0` 和 `1` 的数量，如果相等则更新最大长度。

``` js
var findMaxLength = function (nums) {
  const n = nums.length;
  let maxLen = 0;

  for (let i = 0; i < n; i++) {
    let zero = 0;
    let one = 0;

    for (let j = i; j < n; j++) {
      if (nums[j] === 0) zero++;
      else one++;

      if (zero === one) {
        maxLen = Math.max(maxLen, j - i + 1);
      }
    }
  }

  return maxLen;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 前缀和 + 哈希表

将 `0` 视为 `-1`，`1` 视为 `1`，则问题转化为寻找和为 `0` 的最长子数组。使用前缀和计算累加和，并用哈希表记录每个前缀和第一次出现的位置。如果两个位置的前缀和相同，那么这两个位置之间的子数组和为 `0`，即 `0` 和 `1` 数量相等。

``` js
var findMaxLength = function (nums) {
  const n = nums.length;
  let maxLen = 0;
  let prefixSum = 0;
  const map = new Map();
  map.set(0, -1);

  for (let i = 0; i < n; i++) {
    prefixSum += nums[i] === 1 ? 1 : -1;
    if (map.has(prefixSum)) {
      maxLen = Math.max(maxLen, i - map.get(prefixSum));
    } else {
      map.set(prefixSum, i);
    }
  }

  return maxLen;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [560. 和为 K 的子数组 🟡 ❤](https://leetcode.cn/problems/subarray-sum-equals-k/description/)

题目要求：给定一个整数数组 `nums` 和一个整数 `k`，统计并返回数组中连续子数组的和等于 `k` 的子数组数量。

### 1. 暴力法

枚举所有子数组，统计和为 `k` 的子数组的数量。

``` js
var subarraySum = function (nums, k) {
  const n = nums.length;
  let count = 0;

  for (let i = 0; i < n; i++) {
    let sum = 0;

    for (let j = i; j < n; j++) {
      sum += nums[j];
      if (sum === k) count++;
    }
  }

  return count;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 前缀和

使用前缀和预处理当前元素和，`prefixSum[i]` 表示前 `i` 个元素的和，对于任意子数组 `[i, j]`，其和为 `prefixSum[j] - prefixSum[i-1]`。我们可以在遍历数组时，用哈希表记录每个前缀和出现的次数。当遍历到位置 `j` 时，当前前缀和为 `sum`，我们检查哈希表中是否存在前缀和为 `sum - k` 的记录，如果存在，则说明存在以 `j` 结尾的子数组和为 `k`，累加其出现次数。

``` js
var subarraySum = function (nums, k) {
  const n = nums.length;
  let count = 0;
  let prefixSum = 0;
  const map = new Map();
  map.set(0, 1); // 前缀和为0出现1次

  for (const num of nums) {
    prefixSum += num;

    if (map.has(prefixSum - k)) {
      count += map.get(prefixSum - k);
    }
    map.set(prefixSum, (map.get(prefixSum) ?? 0) + 1);
  }

  return count;
};
```

## [325.和等于 k 的最长子数组长度 🟡 🔒](https://leetcode.cn/problems/maximum-size-subarray-sum-equals-k/description/)

题目要求：给定一个整数数组 `nums` 和一个整数 `k`，返回数组中连续子数组的和等于 `k` 的最长子数组的长度。如果不存在这样的子数组，则返回 `0`。

### 1. 暴力法

通过两层循环枚举所有可能的子数组，对于每个子数组计算其和，如果和等于 `k` 则更新最大长度。

``` js
var maxSubArrayLen = function (nums, k) {
  const n = nums.length;
  let maxLen = 0;

  for (let i = 0; i < n; i++) {
    let sum = 0;
    for (let j = i; j < n; j++) {
      sum += nums[j];
      if (sum === k) {
        res = Math.max(res, j - i + 1);
      }
    }
  }
  
  return maxLen;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 前缀和 + 哈希表

使用前缀和来快速计算子数组的和。为了得到最长长度，我们需要记录某个前缀和第一次出现的位置。

定义 `prefixSum` 为从数组开头到当前位置的元素之和。对于任意子数组 `[i, j]`，其和为 `prefixSum[j] - prefixSum[i-1]`。

我们可以在遍历数组时，用哈希表记录每个前缀和第一次出现的索引。

当遍历到位置 `i` 时，当前前缀和为 `prefixSum`，我们检查哈希表中是否存在前缀和为 `prefixSum - k` 的记录，如果存在，则说明存在以 `i` 结尾的子数组和为 `k`，长度为 `i - (map.get(prefixSum - k))`，更新最大长度。

``` js
var maxSubArrayLen = function (nums, k) {
  const n = nums.length;
  let maxLen = 0;
  const map = new Map();
  map.set(0, -1);
  let prefixSum = 0;

  for (let i = 0; i < n; i++) {
    prefixSum += nums[i];
    
    if (map.has(prefixSum - k)) {
      maxLen = Math.max(maxLen, i - map.get(prefixSum - k));
    }

    // 只记录第一次出现的位置，保证区间尽可能长
    if (!map.has(prefixSum)) {
      map.set(prefixSum, i);
    }
  }

  return maxLen;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [974. 和可被 K 整除的子数组 🟡](https://leetcode.cn/problems/subarray-sums-divisible-by-k/description/)

题目要求：给定一个整数数组 `nums` 和一个整数 `k`，统计并返回数组中连续子数组的和能被 `k` 整除的子数组数量。

### 1. 暴力法（超时）

枚举所有连续子数组，对于每个子数组计算其和，判断是否能被 `k` 整除。

``` js
var subarraysDivByK = function (nums, k) {
  const n = nums.length;
  let count = 0;

  for (let i = 0; i < n; i++) {
    let sum = 0;

    for (let j = i; j < n; j++) {
      sum += nums[j];

      if (sum % k === 0) count++;
    }
  }

  return count;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 前缀和 + 哈希表

使用前缀和计算累加和，根据同余定理：如果两个前缀和除以 k 的余数相同，那么这两个前缀和之间的子数组和能被 `k` 整除。因此，我们可以用哈希表记录每个余数出现的次数。遍历数组时，计算当前前缀和除以 `k` 的余数（注意处理负数的情况），然后检查哈希表中是否有相同的余数，有则说明存在以当前位置结尾的子数组和能被 `k` 整除，累加其出现次数。

``` js
var subarraysDivByK = function (nums, k) {
  let count = 0;
  let prefixSum = 0;
  const map = new Map();
  map.set(0, 1);

  for (const num of nums) {
    prefixSum += num;
    let r = ((prefixSum % k) + k) % k; // 处理负数取模
    if (map.has(r)) {
      count += map.get(r);
    }
    map.set(r, (map.get(r) ?? 0) + 1);
  }

  return count;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [523. 连续的子数组和 🟡](https://leetcode.cn/problems/continuous-subarray-sum/description/)

题目要求：给定一个整数数组 `nums` 和一个整数 `k`，判断是否存在一个长度至少为 `2` 的连续子数组，使得该子数组的元素和是 `k` 的倍数。如果存在，返回 `true`，否则返回 `false`。

### 1. 暴力法（超时）

枚举所有长度 ≥ 2 的连续子数组，对于每个子数组，计算其元素和，判断是否能被 `k` 整除。

``` js
var checkSubarraySum = function (nums, k) {
  const n = nums.length;

  for (let i = 0; i < n - 1; i++) {
    let sum = nums[i];

    for (let j = i + 1; j < n; j++) {
      sum += nums[j];

      if (sum % k === 0) return true;
    }
  }

  return false;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 前缀和 + 哈希表

使用前缀和计算累加和，根据同余定理：如果两个前缀和除以 `k` 的余数相同，且这两个前缀和对应的索引差至少为 `2`，那么这两个前缀和之间的子数组和能被 `k` 整除。因此，可以用哈希表记录每个余数第一次出现的索引。遍历数组时，计算当前前缀和除以 `k` 的余数（注意处理负数的情况），然后检查哈希表中是否有相同的余数。如果有，且当前索引与哈希表中存储的索引差至少为 `2`，则返回 `true`。否则，如果当前余数不在哈希表中，则将其索引存入哈希表。

注意：需要初始化哈希表，存入余数 `0` 对应的索引为 `-1`，这样当前缀和本身能被 `k` 整除且长度至少为 `2` 时也能正确处理。

``` js
var checkSubarraySum = function (nums, k) {
  const map = new Map();
  map.set(0, -1); // 初始化余数为0时第一次出现的位置
  let prefixSum = 0;

  for (let i = 0; i < nums.length; i++) {
    prefixSum += nums[i];
    let r = ((prefixSum % k) + k) % k; // 处理负数取模
    
    if (map.has(r)) {
      if (i - map.get(r) >= 2) return true;
    } else {
      map.set(r, i); // 只记录最早出现的下标
    }
  }

  return false;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [862. 和至少为 K 的最短子数组🔴](https://leetcode.cn/problems/shortest-subarray-with-sum-at-least-k/description/)

题目要求：给定一个整数数组 `nums`（可能包含负数）和一个整数 `k`，找出 `nums` 中和至少为 `k` 的最短非空连续子数组，并返回其长度。如果不存在这样的子数组，返回 `-1`。

### 1. 暴力法（超时）

枚举所有可能的连续子数组，计算每个子数组的和，如果和至少为 k，则更新最短长度。

``` js
var shortestSubarray = function (nums, k) {
  const n = nums.length;
  let minLen = Infinity;

  for (let i = 0; i < n; i++) {
    let sum = 0;

    for (let j = i; j < n; j++) {
      sum += nums[j];

      if (sum >= k) {
        minLen = Math.min(minLen, j - i + 1);
        break;
      }
    }
  }

  return minLen === Infinity ? -1 : minLen;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 前缀和 + 单调双端队列

我们维护一个计算数组前缀和的数组 `prefixSum`，`prefixSum[i]` 表示前 `i` 个元素的和，另外还需要维护一个前缀和递增的单调队列 `deque`，队列存储的是前缀和的下标。

我们遍历前缀和数组，对于每一个下标 `i`：

  - 如果 `prefixSum[i] - prefixSum[deque[0]] >= k`，说明当前有一个可行解，更新最短长度。此时，我们需要将队首元素出队，直到队列为空或者不满足条件为止。

  - 如果 `prefixSum[deque.at(-1)] >= prefixSum[i]，我们需要维持队列的单调递增性质，所以将队尾元素出队。

  - 将当前下标加入队列。

``` js
var shortestSubarray = function (nums, k) {
  const n = nums.length;
  let minLen = Infinity;
  const prefixSum = new Array(n + 1).fill(0);
  const deque = [];

  for (let i = 0; i < n; i++) {
    prefixSum[i + 1] = prefixSum[i] + nums[i];
  }

  for (let i = 0; i <= n; i++) {
    // 检查是否存在满足条件的子数组
    while (deque.length > 0 && prefixSum[i] - prefixSum[deque[0]] >= k) {
      minLen = Math.min(minLen, i - deque.shift()); // 更新最短长度
    }
    // 维护单调递增队列
    while (deque.length > 0 && prefixSum[deque.at(-1)] >= prefixSum[i]) {
      deque.pop(); // 移除较大的前缀和下标
    }
    deque.push(i);
  }

  return minLen === Infinity ? -1 : minLen;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。