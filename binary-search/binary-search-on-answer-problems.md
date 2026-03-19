# 二分答案问题

## [875. 爱吃香蕉的珂珂 🟡 ❤](https://leetcode.cn/problems/koko-eating-bananas/description/)

题目要求：珂珂有 `n` 堆香蕉，第 `i` 堆有 `piles[i]` 个香蕉，珂珂需要在给定的时间 `h` 内吃完所有香蕉，请找出并返回最小的吃香蕉速度 `k`。

### 1. 暴力枚举（超时）

我们可以从速度 `1` 开始枚举，逐渐增加速度到 `max(piles)`，对于每个速度 `k`，计算吃完所有香蕉所需的总小时数。若总小时数不超过 `h`，则返回当前 `k`；否则继续尝试更大的 `k`。

``` js
var minEatingSpeed = function (piles, h) {
  const maxPile = Math.max(...piles);

  for (let k = 1; k <= maxPile; k++) {
    let hours = 0;

    for (const pile of piles) {
      hours += Math.ceil(pile / k); // 向上取整（少于 `k` 根香蕉时，吃完后一个小时内不在吃更多香蕉）
    }

    if (hours <= h) return k;
  }
};
```

时间复杂度：O(n * max(piles))，空间复杂度：O(1)。

### 2. 二分查找 👍

我们可以使用二分查找来优化上述过程。在速度范围 `[1, max(piles)]` 内查找最小的 `k`。

在每次二分查找过程中，对于每个候选值 `mid`，计算所需的总小时数，若不超过 `h`，则说明 `mid` 可能偏大（或刚好），尝试缩小右边界以寻找更小的 `k`；否则需要增大 `k`，即缩小左边界。

``` js
var minEatingSpeed = function (piles, h) {
  let lo = 1;
  let hi = Math.max(...piles);

  const getHours = k => {
    let hours = 0;

    for (const pile of piles) {
      hours += Math.ceil(pile / k);
    }

    return hours;
  };

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (getHours(mid) <= h) {
      hi = mid - 1; // 当前速度可行，尝试更小的速度
    } else {
      lo = mid + 1; // 当前速度太慢，需要更大的速度
    }
  }

  return lo; // 最小可行速度
};
```

时间复杂度：O(n log(max(piles)))，空间复杂度：O(1)。

## [1870. 准时到达的列车最小时速 🟡](https://leetcode.cn/problems/minimum-speed-to-arrive-on-time/description/)

题目要求：给定一个表示每趟列车行驶距离的整数数组 `dist`，每趟列车只能整点发车，搭乘下一趟列车可能需要等待一段时间。求搭乘所有列车能在 `hour` 小时内到达办公室的最小整数速度。如果无法准时到达则返回 `-1`。

### 1. 暴力枚举（超时）

我们可以从速度 `1` 开始枚举，逐渐增加速度到一个较大的上限（如 `10^7`），对于每个速度，计算搭乘所有列车所需的总小时数。若总小时数不超过 `hour`，则返回当前速度；否则继续尝试更大的速度。

``` js
var minSpeedOnTime = function (dist, hour) {
  const maxSpeed = 10_000_000; // 最大速度不超过 10^7

  for (let speed = 1; speed <= maxSpeed; speed++) {
    let time = 0;

    for (let i = 0; i < dist.length; i++) {
      if (i === dist.length - 1) {
        time += dist[i] / speed; // 最后一趟列车无需向上取整
      } else {
        time += Math.ceil(dist[i] / speed); // 前 n - 1 趟列车需要等待
      }
    }

    if (time <= hour) return speed;
  }

  return -1;
};
```

时间复杂度：O(n * max(speed))，空间复杂度：O(1)。

### 2. 二分查找 👍

我们可以使用二分查找来优化上述过程。在速度范围 `[1, 10^7]` 内查找最小的速度。

对于每个候选速度，如果总时间不超过 `hour`，则尝试更小的速度；否则需要更大的速度。

``` js
var minSpeedOnTime = function (dist, hour) {
  const n = dist.length;
  if (n > Math.ceil(hour)) return -1; // 剪枝：列车数量超过允许的最大时间，直接返回 -1

  const getTime = speed => {
    let time = 0;

    for (let i = 0; i < n; i++) {
      time += (i === n - 1) ? dist[i] / speed : Math.ceil(dist[i] / speed);
    }

    return time;
  };

  let lo = 1;
  let hi = 10_000_000; // 最大速度不超过 10^7

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (getTime(mid) <= hour) {
      hi = mid - 1; // 当前速度可行，尝试查找更小的速度
    } else {
      lo = mid + 1; // 当前速度太慢，需要更大的速度
    }
  }

  return lo;
};
```

时间复杂度：O(nlog(max(speed)))，空间复杂度：O(1)。

## [410. 分割数组的最大值 🔴](https://leetcode.cn/problems/split-array-largest-sum/description/)

题目要求：给定一个非负整数数组 `nums` 和一个整数 `k`，将数组分割成 `k` 个非空连续子数组，返回所有分割方案中各组和的最大值的的最小值。

### 1. 动态规划

状态定义：

`dp[i][j]` 表示将前 `i` 个元素分割成 `j` 个子数组时，各子数组和的最大值的最小值。

状态转移方程：

$$
dp[i][j]=
\begin{cases}
min(max(前 m 个数分割成 j - 1 段的最大和, 第 m + 1 到 i 个数的和的最大值))
\end{cases}
$$

``` js
var splitArray = function (nums, k) {
  const n = nums.length;
  const prefixSum = new Array(n + 1).fill(0);
  for (let i = 1; i <= n; i++) {
    prefixSum[i] = prefixSum[i - 1] + nums[i - 1];
  }

  const dp = Array.from({ length: n + 1}, () => new Array(k + 1).fill(Infinity));
  dp[0][0] = 0;

  for (let i = 1; i <= n; i++) {
    for (let j = 1; j <= Math.min(i, k); j++) {
      // 尝试所有分割方式
      for (let m = 0; m < i; m++) {
        // 寻找前 m 个数分割成 j-1 段的最大和与第 m+1 到 i 个数的和的最大值
        dp[i][j] = Math.min(dp[i][j], Math.max(dp[m][j - 1], prefixSum[i] - prefixSum[m]));
      }
    }
  }

  return dp[n][k];
};
```

时间复杂度：O(n² * k)，空间复杂度：O(nk)。

TODO：关于动态规划的更多信息，我们将在后面的相关专题中详细介绍。现在看不懂也没关系，可以先跳过，直接看下面的二分查找解法。

### 2. 二分查找 👍

在子数组和的范围 `[max(nums), sum(nums)]` 内查找最小的最大子数组和。

在每次二分查找过程中，对于每个候选值 `mid`，贪心地将数组分成若干段，使得每段的和不超过 `mid`，计算需要的段数。如果段数不超过 `k`，则说明 `mid` 可行，可以尝试更小的值；否则需要更大的值。

``` js
var splitArray = function (nums, k) {
  const getCount = maxSum => {
    let count = 1;
    let sum = 0;

    for (const num of nums) {
      if (sum + num > maxSum) {
        count++;
        sum = num;
      } else {
        sum += num;
      }
    }

    return count;
  };

  let lo = Math.max(...nums); // 子数组和的最小值至少是数组中的最大值
  let hi = nums.reduce((a, b) => a + b, 0); // 子数组和的最大值至多是数组所有元素之和

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);
    const count = getCount(mid); // 计算在最大子数组和为 mid 的情况下需要分割成多少个子数组

    if (count <= k) {
      hi = mid - 1; // 尝试更小的最大子数组和
    } else {
      lo = mid + 1; // 增加最大子数组和
    }
  }

  return lo;
};
```

时间复杂度：O(nlog(sum(nums)))，空间复杂度：O(1)。