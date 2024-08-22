# 缺失元素问题

## [268. 丢失的数字 🟢 ❤](https://leetcode.cn/problems/missing-number/description/)

题目要求：在一个包含 `n` 个数且元素在 `[0, n]` 区间的数组中，找出缺失的那个数字。

### 1. 暴力法

对于 `[0, n]` 区间内的每一个数字，我们遍历数组，检查当前数字是否存在数组中，如果不存在则该数字就是丢失的数字。

``` js
var missingNumber = function(nums) {
  for (let i = 0; i <= nums.length; i++) {
    let flag = false;

    for (const num of nums) {
      if (i === num) {
        flag = true;
        break;
      }
    }

    if (!flag) return i;
  }
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 排序

为了优化性能，我们可以先对数组进行排序，然后遍历数组，如果当前数字与数组元素不对应则该数字即为丢失的数字。

``` js
var missingNumber = function(nums) {
  nums.sort((a, b) => a - b);

  for (let i = 0; i <= nums.length; i++) {
    if (i !== nums[i]) return i;
  }
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。

### 3. 哈希表

先将所有数字存入哈希表中，然后在 `[0, n]` 区间内查找不在集合中的数字。

``` js
var missingNumber = function(nums) {
  const seen = new Set(nums);

  for (let i = 0; i <= nums.length; i++) {
    if (!seen.has(i)) return i;
  }
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 4. 位运算 👍

利用位运算中异或运算的特性：相同的数字异或为 0，任何数与 0 异或为其自身。遍历数组元素，消除相同的数字，最后剩下的数字即为丢失的数字。

``` js
var missingNumber = function(nums) {
  const n = nums.length;
  let res = n;

  for (let i = 0; i < n; i++) {
    res ^= i ^ nums[i];
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 5. 数学 👍

利用高斯求和公式计算 `[0, n]` 区间所有数字的总和，然后减去数组中所有数字的总和得到的即为丢失的数字。

``` js
var missingNumber = function(nums) {
  const n = nums.length;
  return (n * (n + 1) / 2) - nums.reduce((a, b) => a + b, 0);
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## 448. [找到所有数组中消失的数字 🟢](https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array/description/)

题目要求：在一个包含 `n` 个数且元素在 `[1, n]` 区间的数组中，找出缺失的所有数字。

### 1. 暴力法（超时）

对于 `[1, n]` 区间内的每一个数字，我们遍历数组，检查当前数字是否存在数组中，如果不在数组中则将其记录为消失的数字。

``` js
var findDisappearedNumbers = function(nums) {
  const n = nums.length;
  const res = [];

  for (let i = 1; i <= n; i++) {
    let flag = false;

    for (const num of nums) {
      if (i === num) {
        flag = true;
        break;
      }
    }

    if (!flag) res.push(i);
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 哈希表

利用空间换时间的思想。先将所有数字存入哈希表中，然后在 `[1, n]` 之间查找不在集合中的数字，将其记录为消失的数字。

``` js
var findDisappearedNumbers = function(nums) {
  const seen = new Set(nums);
  const res = [];

  for (let i = 1; i <= nums.length; i++) {
    if (!seen.has(i)) res.push(i);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 3. 原地哈希 👍

“一个萝卜一个坑”，以索引作为哈希表的键，元素作为哈希表的值。先将元素与索引不对应的元素原地交换到对应位置，然后遍历数组，将与数组元素不对应的数字记录为消失的数字。

``` js
var findDisappearedNumbers = function(nums) {
  const n = nums.length;
  const res = [];

  for (let i = 0; i < n; i++) {
    while (nums[i] !== nums[nums[i] - 1]) {
      [nums[nums[i] - 1], nums[i]] = [nums[i], nums[nums[i] - 1]]; // 原地交换
    }
  }

  for (let i = 0; i < n; i++) {
    if (nums[i] !== i + 1) res.push(i + 1);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 4. 标记法 👍

通过标记数组元素的方式，将出现过的数字对应索引的元素标记为负数，然后遍历数组，将未标记（大于 `0`）的元素记录为消失的数字。

``` js
var findDisappearedNumbers = function(nums) {
  const n = nums.length;
  const res = [];

  for (let i = 0; i < n; i++) {
    const index = Math.abs(nums[i]) - 1;
    if (nums[index] > 0) nums[index] = -nums[index]; // 原地标记为负数
  }

  for (let i = 0; i < n; i++) {
    if (nums[i] > 0) res.push(i + 1);
  }

  return res;
};
```

也可以将出现过的数字对应索引的元素标记为大于 `n` 的数，将未标记（小于等于 `n`）的元素记录为消失的数字。

``` js
var findDisappearedNumbers = function(nums) {
  const n = nums.length;
  const res = [];

  for (let i = 0; i < n; i++) {
    const index = (nums[i] - 1) % n;
    nums[index] += n; // 原地标记为大于 n 的数
  }

  for (let i = 0; i < n; i++) {
    if (nums[i] <= n) res.push(i + 1);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [41. 缺失的第一个正数 🔴](https://leetcode.cn/problems/first-missing-positive/)

题目要求：在一个未排序的整数数组中，找出缺失的第一个正整数。

限制：设计的算法时间复杂度为 `O(n)` 并且只能使用常数级别的额外空间。

没有出现的正整数在 `[1, n + 1]` 区间内，如果 1 ~ n 之间的正整数都出现了，那么缺失的正整数就是 `n + 1`，否则缺失的正整数在 1 ~ n 之间。

### 1. 暴力法（超时且不符合时间复杂度限制）

从 1 开始检查当前正整数是否存在于数组中，直到找到第一个缺失的正整数为止。

``` js
var firstMissingPositive = function(nums) {
  const n = nums.length;

  for (let i = 1; i <= n; i++) {
    let flag = false;

    for (const num of nums) {
      if (i === num) {
        flag = true;
        break;
      }
    }

    if (!flag) return i;
  }

  return n + 1;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 哈希表（不符合空间复杂度限制）

将数组所有元素存入哈希表中，然后从 1 开始遍历数组，判断当前数字是否在哈希表中。

``` js
var firstMissingPositive = function(nums) {
  const n = nums.length;
  const seen = new Set(nums);

  for (let i = 1; i <= n; i++) {
    if (!seen.has(i)) return i;
  }

  return n + 1;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 3. 原地哈希 👍

先将 `[1, n]` 区间内的所有元素原地交换到索引对应位置，然后遍历数组，找出第一个不在对应位置的元素即为缺失的正数。

``` js
var firstMissingPositive = function(nums) {
  const n = nums.length;

  for (let i = 0; i < n; i++) {
    while (nums[i] > 0 && nums[i] <= n && nums[i] !== nums[nums[i] - 1]) {
      [nums[nums[i] - 1], nums[i]] = [nums[i], nums[nums[i] - 1]];
    }
  }

  for (let i = 0; i < n; i++) {
    if (nums[i] !== i + 1) return i + 1;
  }

  return n + 1;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 4. 标记法 👍

由于数组中存在 `0` 和非负整数，需要将它们标记为 `n + 1` 的正整数。然后将 `[1, n]` 区间内的所有元素标记负数，最后遍历数组，找出第一个未标记的位置即为第一个缺失的正数。

``` js
var firstMissingPositive = function(nums) {
  const n = nums.length;

  for (let i = 0; i < n; i++) {
    if (nums[i] <= 0) nums[i] = n + 1; // 原地标记非正整数
  }

  for (let i = 0; i < n; i++) {
    const index = Math.abs(nums[i]) - 1;
    if (index >= n) continue;
    if (nums[index] > 0) nums[index] = -nums[index]; // 原地标记正整数
  }

  for (let i = 0; i < n; i++) {
    if (nums[i] > 0) return i + 1;
  }

  return n + 1;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

