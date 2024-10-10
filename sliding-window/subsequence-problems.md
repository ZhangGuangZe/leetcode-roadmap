# 子序列问题

## [剑指 Offer 57 - II. 和为s的连续正数序列 🟢 ❤](https://leetcode.cn/problems/he-wei-sde-lian-xu-zheng-shu-xu-lie-lcof/description/)

题目要求：找出所有和为 `target` 的连续正整数子序列。

### 1. 暴力法

枚举所有可能的子序列，检查其和与 `target` 是否相等。如果是，将子序列添加到结果列表中；否则从下一个元素开始继续寻找符合条件的子序列。

``` js
var fileCombination = function (target) {
  const res = [];

  for (let i = 1; i < target / 2; i++) {
    let sum = i;
    let j = i + 1;

    while (sum < target) {
      sum += j;
      j++;
    }

    if (sum === target) {
      const subsequence = [];
      for (let k = i; k < j; k++) {
        subsequence.push(k);
      }
      res.push(subsequence);
    }
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(k)。

### 2. 滑动窗口 👍

定义变量 `l` 和 `r`，分别表示滑动窗口的左右边界。每次根据当前窗口的和与 `target` 的大小关系，来移动滑动窗口的左右边界，并找出符合条件的子序列。

- 如果 `sum < target`，需要扩大窗口，将右边界向右移动一位；
- 如果 `sum > target`，需要缩小窗口，将左边界向右移动一位；
- 如果 `sum == target`，需要记录子序列，并将窗口左边界向右移动一位。

``` js
var fileCombination = function (target) {
  const res = [];
  let l = 1;
  let r = 1;
  let sum = 0;

  while (l < target / 2) {
    if (sum === target) {
      const subsequence = [];
      for (let i = l; i < r; i++) {
        subsequence.push(i);
      }
      res.push(subsequence);
    }

    if (sum > target) {
      sum -= l;
      l++;
    } else {
      sum += r;
      r++;
    }
  }

  return res;
};
```

我们可以对以上代码结构进行一些优化。

``` js
var fileCombination = function (target) {
  const res = [];
  let l = 1;
  let r = 1;
  let sum = 0;

  while (l < target / 2) {
    // 扩大窗口
    sum += r;

    // 满足条件，尝试收缩窗口
    while (sum > target) {
      sum -= l;
      l++;
    }

    if (sum === target) {
      const subsequence = [];
      for (let i = l; i <= r; i++) {
        subsequence.push(i);
      }
      res.push(subsequence);
    }

    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(k)。

