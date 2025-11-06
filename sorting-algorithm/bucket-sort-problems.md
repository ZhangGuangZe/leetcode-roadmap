# 桶排序问题

## [451. 根据字符出现频率排序 🟡](https://leetcode.cn/problems/sort-characters-by-frequency/description/)

题目要求：给定一个字符串 `s`，根据字符出现的频率对字符串进行排序，频率高的字符排在前面。

### 1. 哈希表 + 排序

首先使用哈希表统计每个字符的出现频率。然后将哈希表转换为数组，并根据频率进行排序。最后根据排序结果构建输出字符串。

``` js
var frequencySort = function (s) {
  const freqMap = new Map();
  for (const c of s) {
    freqMap.set(c, (freqMap.get(c) || 0) + 1);
  }

  const arr = Array.from(freqMap.entries()).sort((a, b) => b[1] - a[1]);

  let res = '';
  for (const [c, freq] of arr) {
    res += c.repeat(freq);
  }

  return res;
};
```

时间复杂度：O(klogk)，空间复杂度：O(n)。

### 2. 哈希表 + 桶排序 👍

同样使用哈希表统计字符频率，并记录最大出现频率。然后根据最大频率建桶并将字符依次放入桶中。最后根据频率从高到低遍历构建输出字符串。

``` js
var frequencySort = function (s) {
  const freqMap = new Map();
  let maxFreq = 0;

  for (const c of s) {
    const freq = (freqMap.get(c) ?? 0) + 1;
    freqMap.set(c, freq);
    maxFreq = Math.max(maxFreq, freq);
  }

  const buckets = Array.from({ length: maxFreq + 1 }, () => []);

  for (const [c, freq] of freqMap) {
    buckets[freq].push(c);
  }

  let res = '';
  for (let i = maxFreq; i > 0; i--) {
    for (const c of buckets[i]) {
      res += c.repeat(i);
    }
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [164. 最大间距 🟡](https://leetcode.cn/problems/maximum-gap/description/)

题目要求：给定一个无序的整数数组，找出数组排序后相邻元素之间的最大差值。空间和时间复杂度必须是线性的。

### 桶排序 👍

首先找出数组的最大和最小值。

然后根据最大间距计算桶的大小和数量，每个桶内只存储区间的最大值和最小值。

最后计算相邻桶之间的最大间距并更新最大间距，最大间距为：`后一个非空桶的 min - 前一个非空桶的 max`。

``` js
var maximumGap = function (nums) {
  const n = nums.length;
  if (n < 2) return 0;

  const max = Math.max(...nums);
  const min = Math.min(...nums);

  if (max === min) return 0; // 最大间距为0

  // 初始化桶
  const bucketSize = Math.max(1, Math.floor((max - min) / (n - 1)));
  const bucketCount = Math.floor((max - min) / bucketSize) + 1;
  const buckets = Array.from({ length: bucketCount }, () => ({ min: Infinity, max: -Infinity }));

  // 每个桶中存储最大值和最小值
  for (const num of nums) {
    const i = Math.floor((num - min) / bucketSize);
    const bucket = buckets[i];
    bucket.min = Math.min(bucket.min, num);
    bucket.max = Math.max(bucket.max, num);
  }

  // 计算最大间距
  let maxGap = 0;
  let prevMax = min;

  for (const bucket of buckets) {
    if (bucket.min === Infinity) continue;

    maxGap = Math.max(maxGap, bucket.min - prevMax);
    prevMax = bucket.max;
  }

  return maxGap;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [539. 最小时间差 🟡 👍](https://leetcode.cn/problems/minimum-time-difference/description/)

题目要求：给定一个时间点列表，找出其中两个时间点之间的最小时间差（以分钟为单位）。

### 1. 排序

首先将时间做转换为分钟，然后对分钟数组进行排序，最后比较相邻两个时间的差值，计算最小值。

我们还需要处理跨天的特殊情况。若时间点数量超过一天总分钟数（`1440`），根据**鸽巢原理**，必定有重复时间，可直接返回 `0`。

``` js
var findMinDifference = function (timePoints) {
  const n = timePoints.length;
  if (n > 1440) return 0; // 鸽巢原理优化

  const minutes = timePoints.map(time => {
    const [h, m] = time.split(':');
    return h * 60 + m * 1;
  }).sort((a, b) => a - b);

  let minDiff = Infinity;
  minutes.push(minutes[0] + 1440); // 处理跨天
  for (let i = 1; i <= n; i++) {
    minDiff = Math.min(minDiff, minutes[i] - minutes[i - 1]);
  }

  return minDiff;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

### 2. 桶排序 👍

创建一个长度为 `1440` 的布尔数组（桶），记录出现的时间。如果某个时间重复出现，直接返回 `0`。遍历桶，收集已经记录的时间点，计算这些相邻时间点的最小时间差。

``` js
var findMinDifference = function (timePoints) {
  const n = timePoints.length;
  if (n > 1440) return 0;

  const buckets = new Array(1440).fill(false);
  for (const time of timePoints) {
    const [h, m] = time.split(':');
    const minutes = h * 60 + m * 1;
    if (buckets[minutes]) return 0;
    buckets[minutes] = true;
  }
  
  const times = [];
  for (let i = 0; i < 1440; i++) {
    if (buckets[i]) times.push(i);
  }

  times.push(times[0] + 1440);

  let minDiff = Number.MAX_SAFE_INTEGER;
  for (let i = 1; i < times.length; i++) {
    minDiff = Math.min(minDiff, times[i] - times[i - 1]);
  }
  return minDiff;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。