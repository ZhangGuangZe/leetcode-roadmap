# 翻转数组问题

## [189. 轮转数组 🟡 ❤](https://leetcode.cn/problems/rotate-array/description/)

题目要求：将数组中的元素向右移动 `k` 个位置，其中 `k` 是非负数。

### 1. 暴力法（超时）

循环遍历数组，将每个元素向右移动 `k` 个位置。如果 `k` 超过数组的长度，可以对 `k` 取模，减少不必要的轮转次数。

``` js
var rotate = function (nums, k) {
  const n = nums.length;
  k %= n;
  
  for (let i = 0; i < k; i++) {
    // nums.unshift(nums.pop());
    const temp = nums[n - 1];
    for (let j = n - 1; j > 0; j--) {
      nums[j] = nums[j - 1];
    }
    nums[0] = temp;
  }
};
```

时间复杂度：O(n * k)，空间复杂度：O(1)。

### 2. 额外空间

使用一个额外的数组来存储轮转后的结果，然后将结果复制回原数组。

``` js
var rotate = function (nums, k) {
  const n = nums.length;
  const temp = new Array(n);

  for (let i = 0; i < n; i++) {
    temp[(i + k) % n] = nums[i];
  }

  for (let i = 0; i < n; i++) {
    nums[i] = temp[i];
  }
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 3. 原地旋转 👍

使用三次反转操作来完成轮转。首先反转整个数组，然后反转 `[0, k - 1]` 区间中的元素，再反转 `[k, n - 1]` 区间中的元素。

``` js
var rotate = function (nums, k) {
  const n = nums.length;
  k %= n;
  
  const reverse = (start, end) => {
    while (start < end) {
      // [nums[start++], nums[end--]] = [nums[end], nums[start]];
      [nums[start], nums[end]] = [nums[end], nums[start]];
      start++;
      end--;
    }
  };

  reverse(0, n - 1);
  reverse(0, k - 1);
  reverse(k, n - 1);
};
```

时间复杂度：O(n)，空间复杂度：O(1)。
