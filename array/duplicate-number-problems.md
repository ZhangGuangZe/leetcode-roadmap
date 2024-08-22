# 重复元素问题

## [217. 存在重复元素 🟢](https://leetcode.cn/problems/contains-duplicate/description/)

题目要求：判断在给定数组中是否存在重复元素。

### 1. 暴力法（超时）

双层循环遍历数组，对于每个元素，判断其在数组中是否存在相同的元素。

``` js
var containsDuplicate = function(nums) {
  const n = nums.length;

  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (nums[i] === nums[j]) return true;
    }
  }

  return false;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 排序 👍

先对数组进行排序，然后检查相邻的元素是否相同。

``` js
var containsDuplicate = function(nums) {
  nums.sort((a, b) => a - b);

  for (let i = 1; i < nums.length; i++) {
    if (nums[i - 1] === nums[i]) return true;
  }

  return false;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。

### 3. 哈希表 👍

遍历数组元素，判断当前元素是否存在于哈希表中，如果不存在，将其添加到哈希表中，否则有重复元素。

``` js
var containsDuplicate = function(nums) {
  const seen = new Set();

  for (const num of nums) {
    if (seen.has(num)) return true;
    seen.add(num);
  }

  return false;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [219. 存在重复元素 II 🟢](https://leetcode.cn/problems/contains-duplicate-ii/description/)

题目要求：判断在给定数组中是否存在重复元素并且重复元素的索引差小于等于 `k`。

### 1. 暴力法（超时）

双层循环遍历数组，对于每个元素，判断在数组中是否存在相同的元素且索引差小于等于 `k`。

``` js
var containsNearbyDuplicate = function(nums, k) {
  const n = nums.length;

  for (let i = 0; i < n; i++) {
    for (let j = i + 1; j < n; j++) {
      if (nums[i] === nums[j] && Math.abs(i - j) <= k) return true;
    }
  }

  return false;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 哈希表 👍

使用哈希表来存储每个元素的索引，如果发现重复元素则判断它们的索引差是否小于等于 `k`。

``` js
var containsNearbyDuplicate = function(nums, k) {
  const n = nums.length;
  const map = new Map();

  for (let i = 0; i < n; i++) {
    const num = nums[i];
    if (map.has(num) && Math.abs(i - map.get(num)) <= k) return true;
    map.set(num, i);
  }

  return false;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 3. 哈希表 + 滑动窗口 👍

维护一个大小为 `k` 的滑动窗口而无需将所有元素存入哈希表中。在遍历数组时，将元素添加到集合中，如果集合中的元素超过 `k` 个，就删除最左边的元素，直到找出重复元素为止。

``` js
var containsNearbyDuplicate = function(nums, k) {
  const n = nums.length;
  const seen = new Set();

  for (let i = 0; i < n; i++) {
    if (seen.has(nums[i])) return true;
    seen.add(nums[i]);

    if (seen.size > k) seen.delete(nums[i - k]);
  }

  return false;
};
```

时间复杂度：O(n)，空间复杂度：O(k)。

## [剑指 Offer 03 数组中重复的数字 🟢 ❤](https://leetcode.cn/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/description/)

题目要求：在一个包含 `n` 个数且元素在 `[0, n - 1]` 范围内的数组中，找出任意一个重复的数字。

### 1. 暴力法（超时）

双层循环遍历数组，对于每个元素，判断其在数组中是否存在相同的元素。

``` js
var findRepeatNumber = function (nums) {
  const n = nums.length;

  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (nums[i] === nums[j]) return nums[i];
    }
  }
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 排序 👍

先将数组进行排序，然后检查相邻的元素是否相同。

``` js
var findRepeatNumber = function (nums) {
  nums.sort((a, b) => a - b);

  for (let i = 1; i < nums.length; i++) {
    if (nums[i - 1] === nums[i]) return nums[i];
  }
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。

### 3. 哈希表 👍

遍历数组元素，判断当前元素是否在哈希表中，如果存在则该元素即为重复数字，返回该数字即可；如果哈希表中不存在该元素，则将其加入哈希表。继续遍历，直到找到重复数字为止。

``` js
var findRepeatNumber = function (nums) {
  const seen = new Set();

  for (const num of nums) {
    if (seen.has(num)) return num;
    seen.add(num);
  }
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 4. 原地哈希 👍

由于数组元素在 `[0, n - 1]` 范围内，可以先将当前元素与当前元素作为索引对应的元素进行交换，然后遍历数组，判断当前元素与索引是否相同，如果不同，则说明该元素是重复数字。

``` js
var findRepeatNumber = function (nums) {
  const n = nums.length;

  for (let i = 0; i < n; i++) {
    while (nums[i] !== nums[nums[i]]) {
      [nums[nums[i]], nums[i]] = [nums[i], nums[nums[i]]];
    }
  }
  for (let i = 0; i < n; i++) {
    if (nums[i] !== i) return nums[i];
  }
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [442. 数组中重复的数据 🟡 ❤](https://leetcode.cn/problems/find-all-duplicates-in-an-array/description/)

题目要求：在一个包含 `n` 个数且元素在 `[1, n]` 范围内元素只出现一次或两次的数组中，找出所有重复两次的数字。

限制：实现算法的时间复杂度必须为 `O(n)` 并且只能使用 `O(1)` 额外空间。

如果没有限制条件，可以使用暴力法、排序和哈希表来解题，前面的题目已经展示了这些方法的题解，以下仅展示符合限制条件的题解。

### 1. 原地哈希 👍

将每个数交换到以该数作为索引对应的位置上，然后遍历数组，将不在正确位置的数字记录为重复的数字。

``` js
var findDuplicates = function (nums) {
  const n = nums.length;
  const res = [];

  for (let i = 0; i < n; i++) {
    while (nums[i] !== nums[nums[i] - 1]) {
      [nums[nums[i] - 1], nums[i]] = [nums[i], nums[nums[i] - 1]];
    }
  }

  for (let i = 0; i < n; i++) {
    if (nums[i] !== i + 1) res.push(nums[i]);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 2. 标记法 👍

以当前元素作为索引，将索引对应的元素标记为负数，再次遇到当前元素作为索引对应的元素为负数时，则说明该元素是重复的，将其记录为重复元素。

``` js
var findDuplicates = function (nums) {
  const n = nums.length;
  const res = [];

  for (let i = 0; i < n; i++) {
    const index = Math.abs(nums[i]) - 1;

    if (nums[index] > 0) nums[index] = -nums[index];
    else res.push(Math.abs(nums[i]));
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [287. 寻找重复数 🟡 ❤](https://leetcode.cn/problems/find-the-duplicate-number/description/)

题目要求：在一个包含 `n + 1` 个数且元素在 `[1, n]` 范围内的数组中，找出唯一重复的数。

限制：实现的算法必须不能修改数组，并且只能使用 `O(1)` 的额外空间。

如果没有限制条件，并且空间允许，可以使用哈希表解题；如果可以修改原数组，可以使用排序、原地哈希和标记法解题。以下仅展示符合限制条件的题解，其它题解请参考上面的题解。

### 1. 二分查找 👍

既然使用暴力法会超时，我们可以使用二分查找来优化查询效率。

根据索引利用二分思想。如果 `[1, mid]` 中的数字个数大于索引 `mid`，那么重复的数字一定在 `[1, mid]` 中，否则重复的数字一定在 `[mid + 1, n]` 中。每次判断 `[1, mid]` 中的数字个数是否大于索引 `mid`，从而确定重复的数字在哪个区间中，进而缩小查找范围，直到找到重复的数字为止。

``` js
var findDuplicate = function (nums) {
  let lo = 0;
  let hi = nums.length - 1;

  while (lo < hi) {
    const mid = Math.floor((lo + hi) / 2);
    let count = 0;

    for (const num of nums) {
      if (num <= mid) count++;
    }

    if (count > mid) hi = mid;
    else lo = mid + 1;
  }

  return lo;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。

### 2. 快慢指针（Floyd 判圈算法）👍

将数组看做是环形链表，重复的数字就是环的入口。使用快慢指针，慢指针每次走一步，快指针每次走两步，如果慢指针和快指针相遇，说明有环；然后将慢指针重新置为起点，两指针每次同时走一步，再次相遇则为重复的数字。

``` js
var findDuplicate = function (nums) {
  let slow = 0;
  let fast = 0;

  do {
    slow = nums[slow];
    fast = nums[nums[fast]];
  } while (slow !== fast);

  slow = 0;

  while (slow !== fast) {
    slow = nums[slow];
    fast = nums[fast];
  }

  return slow;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [剑指 Offer 61 扑克牌中的顺子 🟢](https://leetcode.cn/problems/bu-ke-pai-zhong-de-shun-zi-lcof/description/)

题目要求：从若干扑克牌中随机抽 `5` 张牌，判断是不是顺子，即这 `5` 张牌是否连续。

### 1. 哈希表 👍

利用哈希表记录出现的数字。如果遇到大小王，则跳过。如果出现重复元，则不可能构成顺子。记录最小值和最大值，如果最大值和最小值之间的差值小于 `5`，则说明可以构成顺子。

``` js
var isStraight = function (nums) {
  const seen = new Set();
  let min = 14;
  let max = 0;

  for (const num of nums) {
    if (num === 0) continue; // 跳过大小王

    if (seen.has(num)) return false; // 处理重复元素
    seen.add(num);

    min = Math.min(min, num);
    max = Math.max(max, num);
  }

  return max - min < 5;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 2. 排序 👍

先对数组进行排序，然后检查是否可以通过大小王来补齐连续的数字，并检查是否有重复的扑克牌，最后检查最大值和最小值之间的差值是否小于 `5`。

``` js
var isStraight = function (nums) {
  nums.sort((a, b) => a - b);

  const n = nums.length;
  let joker = 0;

  for (let i = 0; i < n - 1; i++) {
    if (nums[i] === 0) {
      joker++;
      continue; // 记录大小王并跳过
    }

    if (nums[i] === nums[i + 1]) return false; // 处理重复元素
  }

  return nums[n - 1] - nums[joker] < 5;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。
