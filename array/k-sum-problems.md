# 元素和问题

## 两数之和

### 🟢 [1. 两数之和](https://leetcode.cn/problems/two-sum/description/)

题目要求：在数组中找出和为 `target` 的两个元素，并返回它们的索引。

#### 1. 暴力法

两层循环，判断外层当前元素与内层当前元素之和是否等于 `target`。

``` js
var twoSum = function(nums, target) {
  const n = nums.length;
  
  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (nums[i] + nums[j] === target) return [i, j];
    }
  }
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 👍 2. 哈希表

我们可以使用哈希表来存储数组中的元素及其索引。遍历数组元素，判断 `target - nums[i]` 是否在哈希表中。如果存在，则返回当前元素的索引和哈希表中对应元素的索引。

``` js
var twoSum = function(nums, target) {
  const map = new Map();

  for (let i = 0; i < nums.length; i++) {
    const diff = target - nums[i];
    if (map.has(diff)) return [map.get(diff), i];
    map.set(nums[i], i);
  }
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 🟢 [剑指 Offer 57. 和为s的两个数字](https://leetcode.cn/problems/he-wei-sde-liang-ge-shu-zi-lcof/description/)

题目要求：在一个**升序排列**的数组中找出和为 `target` 两个元素。

#### 1. 暴力法（超时）

思路与上一题相同，只不过这里要求返回的是数组元素。仅用于参考。

``` js
var twoSum = function (nums, target) {
  const n = nums.length;

  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (nums[i] + nums[j] === target) return [nums[i], nums[j]];
    }
  }
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 2. 哈希表

我们可以使用集合来存储数组元素。如果 `target` 与当前元素的差值存在于集合中，则返回当前元素和差值。

``` js
var twoSum = function (nums, target) {
  const seen = new Set()

  for (const num of nums) {
    const diff = target - num;
    if (seen.has(diff)) return [num, diff];
    seen.add(num);
  }
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

#### 👍 3. 双指针

由于数组是升序排列的，我们可以使用双指针来解决此题。首先分别定义 `l` 和 `r` 两个指针，初始化时分别指向数组的第一个和最后一个元素，然后每次计算两指针对应元素和，并根据其与 `target` 的关系移动指针，直到找到和为 `target` 的两个元素为止。

``` js
var twoSum = function (nums, target) {
  let l = 0;
  let r = nums.length - 1;

  while (l < r) {
    const sum = nums[l] + nums[r];
    if (sum === target) return [nums[l], nums[r]];
    sum < target ? l++ : r--;
  }
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 🟢 ❤ [167. 两数之和 II - 输入有序数组](https://leetcode.cn/problems/two-sum-ii-input-array-is-sorted/description/)

题目要求：在一个**索引从 1 开始**的**升序排列**数组中，找出和为 `target` 的两个元素，并返回它们的索引。

设计的解决方案必须只使用常数级额外空间。

#### 1. 暴力法（超时）

思路与前两题相同，只不过这里要求返回的是从 1 开始的索引，仅用于参考。

``` js
var twoSum = function(nums, target) {
  const n = nums.length;

  for (let i = 0; i < n - 1; i++) {
    for (let j = i + 1; j < n; j++) {
      if (nums[i] + nums[j] === target) return [i + 1, j + 1];
    }
  }
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 2. 二分查找

由于输入数组是升序排列的，我们利用二分查找思想，在数组中查找 `target` 与 当前元素的差值，从而优化查找的时间。

``` js
var twoSum = function(nums, target) {
  const n = nums.length;

  for (let i = 0; i < n; i++) {
    const num = nums[i];
    const diff = target - num;
    const index = binarySearch(nums, diff, i + 1, n - 1);
    if (index > 0) {
      return [i + 1, index + 1];
    }
  }
};

const binarySearch =(nums, target, lo, hi) => {
  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);
    const num = nums[mid];

    if (num === target) return mid;
    else if (num < target) lo = mid + 1;
    else hi = mid - 1;
  }
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。

#### 👍 3. 双指针

为了进一步优化查找时间，根据数组的有序性，我们可以使用双指针来解决此题。思路与上一题相同，这里就不在赘述。

``` js
var twoSum = function(nums, target) {
  const n = numbers.length;
  let l = 0;
  let r = n - 1;

  while (l < r) {
    const sum = numbers[l] + numbers[r];
    if (sum === target) return [l + 1, r + 1];
    sum < target l++ : r--;
  }
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 🟡 [面试题 16.24 数对和](https://leetcode.cn/problems/pairs-with-sum-lcci/description/)

题目要求：在数组中找出所有和为 `target` 的数对（一个元素只能组成一个数对）。

#### 👍 1. 哈希表计数

利用哈希表记录数组元素及其出现的次数。遍历数组，如果 `target` 与当前元素的差值存在于哈希表中并且出现次数大于 `0`，则将当前元素与差值组成数对，并将其在哈希表中出现的次数减 `1`。否则将当前元素在哈希表中出现的次数加 `1`。

``` js
var pairSums = function (nums, target) {
  const map = new Map();
  const res = [];

  for (let i = 0; i < nums.length; i++) {
    const num = nums[i];
    const diff = target - num;
    if (map.has(diff) && map.get(diff) > 0) {
      res.push([diff, num]);
      map.set(diff, map.get(diff) - 1);
    } else {
      map.set(num, (map.get(num) || 0) + 1);
    }
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

#### 👍 2. 排序 + 双指针

首先对数组进行排序，然后通过双指针方法寻找数对。双指针的思路与前几题的思路类似，都是计算指针对应元素的和，并根据其与 `target` 的关系移动指针。只不过当找到数对时，需要移动指针，避免让一个元素出现多次。

``` js
var pairSums = function (nums, target) {
  nums.sort((a, b) => a - b);

  const res = [];
  let l = 0;
  let r = nums.length - 1;

  while (l < r) {
    const sum = nums[l] + nums[r];
    if (sum === target) res.push([nums[l++], nums[r--]]);
    sum < target l++ : r--;
  }

  return res;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。

## 三数之和

### 🟡 ❤ [15. 三数之和](https://leetcode.cn/problems/3sum/description/)

题目要求：在数组中找出所有和为 `0` 的三元组，且不能包含重复三元组。

#### 1. 排序 + 双指针

先对数组进行排序，以方便跳过重复元素。然后固定一个元素，使用双指针在剩余的元素中寻找另外两个元素，使得三元组的和为 `0`。

在枚举过程中，如果当前元素大于 `0`，则后续的元素都大于 `0`，因此不可能找到和为 `0` 的三元组，可以提前结束循环。并且我们需要跳过重复元素，以避免出现重复三元组。

``` js
var threeSum = function (nums) {
  nums.sort((a, b) => a - b);
  
  const n = nums.length;
  const res = [];

  for (let i = 0; i < n - 2; i++) {
    if (nums[i] > 0) break; // 剪枝
    if (i > 0 && nums[i] === nums[i - 1]) continue; // 去重

    let l = i + 1;
    let r = n - 1;

    while (l < r) {
      const sum = nums[i] + nums[l] + nums[r];
      
      if (sum === 0) {
        res.push([nums[i], nums[l], nums[r]]);
        while (l < r && nums[l] === nums[l + 1]) l++; // 去重
        while (l < r && nums[r] === nums[r - 1]) r--; // 去重
        l++;
        r--;
      } else if (sum < 0) {
        l++;
      } else {
        r--;
      }
    }
  };

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 🟡 [16. 最接近的三数之和](https://leetcode.cn/problems/3sum-closest/description/)

题目要求：在数组中找出与 `target` 最接近的三元组和。

#### 1. 暴力法

三重循环，尝试所有可能的三数组合，记录与 `target` 的差值，并返回差值最小的那个。

``` js
var threeSumClosest = function(nums, target) {
  let res = Infinity;
  const n = nums.length;

  for (let i = 0; i < n - 2; i++) {
    for (let j = i + 1; j < n - 1; j++) {
      for (let k = j + 1; k < n; k++) {
        const sum = nums[i] + nums[j] + nums[k];
        if (Math.abs(sum - target) < Math.abs(res - target)) {
          res = sum;
        }
      }
    }
  }

  return res;
};
```

时间复杂度：O(n³)，空间复杂度：O(1)。

#### 👍 2. 排序 + 双指针

先对数组进行排序，以方便跳过重复元素。然后固定一个元素，使用双指针在剩余元素中寻找另外两个元素，找出最接近 `target` 的和。

``` js
var threeSumClosest = function(nums, target) {
  nums.sort((a, b) => a - b);

  let res = Infinity;
  const n = nums.length;

  for (let i = 0; i < n - 2; i++) {
    if (i > 0 && nums[i] === nums[i - 1]) continue; // 去重

    let l = i + 1;
    let r = n - 1;

    while (l < r) {
      const sum = nums[i] + nums[l] + nums[r];
      if (sum === target) return sum;

      if (Math.abs(sum - target) < Math.abs(res - target)) {
        res = sum;
      }
      
      if (sum < target) {
        l++;
        while (l < r && nums[l] === nums[l - 1]) l++; // 去重
      } else {
        r--;
        while (l < r && nums[r] === nums[r + 1]) r--; // 去重
      }
    }
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

## 四数之和

### 🟡 [18. 四数之和](https://leetcode.cn/problems/4sum/description/)

题目要求：在数组中找出所有和为 `target` 的四元组，且不能包含重复四元组。

#### 1. 排序 + 双指针

先对数组进行排序，然后通过固定两个元素，使用双指针在剩余元素中寻找另外两个元素，找出等于 `target` 的和。

在提前确认一个和两个数之后可以通过剪枝优化枚举过程。

``` js
var fourSum = function (nums, target) {
  nums.sort((a, b) => a - b);

  const n = nums.length;
  const res = [];

  for (let i = 0; i < n - 3; i++) {
    if (nums[i] + nums[i + 1] + nums[i + 2] + nums[i + 3] > target) break; // 剪枝
    if (nums[i] + nums[n - 3] + nums[n - 2] + nums[n - 1] < target) continue; // 剪枝
    if (i > 0 && nums[i] === nums[i - 1]) continue; // 去重

    for (let j = i + 1; j < n - 2; j++) {
      if (nums[i] + nums[j] + nums[j + 1] + nums[j + 2] > target) break; // 剪枝
      if (nums[i] + nums[j] + nums[n - 2] + nums[n - 1] < target) continue; // 剪枝
      if (j > i + 1 && nums[j] === nums[j - 1]) continue; // 去重

      let l = j + 1;
      let r = n - 1;

      while (l < r) {
        const sum = nums[i] + nums[j] + nums[l] + nums[r];

        if (sum === target) {
          res.push([nums[i], nums[j], nums[l], nums[r]]);
          while (l < r && nums[l] === nums[l + 1]) l++; // 去重
          while (l < r && nums[r] === nums[r - 1]) r--; // 去重
          l++;
          r++;
        } else if (sum < target) {
          l++;
        } else {
          r--;
        }
      }
    }
  }

  return res;
};
```

时间复杂度：O(n³)，空间复杂度：O(1)。

#### 2. k-sum

我们可以将其分解为求解三数之和的问题。

``` js
var fourSum = function (nums, target) {
  nums.sort((a, b) => a - b);
  const res = [];
  kSum(nums, target, 4, 0, [], res);
  return res;
};

const kSum = (nums, target, k, start, cur, res) => {
  if (k === 2) {
    let l = start;
    let r = nums.length - 1;

    while (l < r) {
      const sum = nums[l] + nums[r];
      if (sum === target) {
        res.push(cur.concat([nums[l], nums[r]]));
        while (l < r && nums[l] === nums[l + 1]) l++;
        while (l < r && nums[r] === nums[r - 1]) r--;
        l++;
        r--;
      } else if (sum < target) {
        l++;
      } else {
        r--;
      }
    }
  } else {
    for (let i = start; i < nums.length; i++) {
      if (i > start && nums[i] === nums[i - 1]) continue;
      kSum(nums, target - nums[i], k - 1, i + 1, cur.concat(nums[i]), res);
    }
  }
};
```

### 🟡 [454. 四数相加 II](https://leetcode.cn/problems/4sum-ii/)

题目要求：在四个数组中找出有多少个四元组的和为 `0`。

#### 1. 暴力法（超时）

枚举四个数组中的每个元素，计算和为 `0` 的四元组数量。仅用于参考。

``` js
var fourSumCount = function(nums1, nums2, nums3, nums4) {
  let count = 0;

  for (let i = 0; i < nums1.length; i++) {
    for (let j = 0; j < nums2.length; j++) {
      for (let k = 0; k < nums3.length; k++) {
        for (let l = 0; l < nums4.length; l++) {
          if (nums1[i] + nums2[j] + nums3[k] + nums4[l] === 0) count++;
        }
      }
    }
  }

  return count;
};
```

时间复杂度：O(n⁴)，空间复杂度：O(1)。

#### 👍 2. 分组哈希

我们使用哈希表来存储 `num1` 和 `num2` 的和并记录每个和出现的次数，然后遍历计算`num3` 和 `num4` 的和，如果存在对应的和，则将次数加到结果中。

``` js
var fourSumCount = function(nums1, nums2, nums3, nums4) {
  let count = 0;
  const map = new Map();

  for (const a of nums1) {
    for (const b of nums2) {
      const sum = a + b;
      map.set(sum, (map.get(sum) || 0) + 1);
    }
  }

  for (const c of nums3) {
    for (const d of nums4) {
      const sum = -(c + d);
      if (map.has(sum)) count += map.get(sum);
    }
  }

  return count;
};
```

时间复杂度：O(n²)，空间复杂度：O(n)。
