# 二分数值问题

在某些问题中，二分查找并不是直接在数组索引上进行，而是针对数值范围进行搜索。这类问题通常涉及找到满足某种条件的最小或最大数值。以下是一些典型的二分数值问题及其解决方案。

## [69. x 的平方根 🟡 ❤](https://leetcode.cn/problems/sqrtx/description/)

题目要求：计算并返回非负整数 `x` 的算术平方根，结果只保留整数部分（向下取整），不能使用内置函数。

### 1. 暴力法

从 `0` 开始逐个尝试整数平方，直到平方值超过 `x`。

``` js
var mySqrt = function (x) {
  let i = 0;

  while (i * i <= x) i++;
  
  return i - 1;
};
```

时间复杂度：O(√x)，空间复杂度：O(1)。

### 2. 二分查找 👍

利用二分查找在数值范围 `[0, x]` 内查找平方根小于等于 `x` 的最大整数。

``` js
var mySqrt = function (x) {
  let lo = 0;
  let hi = x;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (mid * mid === x) return mid;
    if (mid * mid < x) {
      lo = mid + 1;
    } else {
      hi = mid - 1;
    }
  }

  return hi;
};
```

时间复杂度：O(logx)，空间复杂度：O(1)。

### 3. 牛顿迭代法 👍

牛顿迭代法是一种用于求解数值的方法，这种方法在实际中广泛应用于数值计算和优化问题。对于平方根问题，我们可以将其转化为求解方程 `f(y) = y² - x = 0`。

当 `r * r` 足够接近 `x` 时，迭代停止，返回 `r` 的整数部分作为平方根的近似值。

``` js
var mySqrt = function (x) {
  if (x === 0) return 0;

  let r = x;

  while (r * r > x) {
    r = Math.floor((r + x / r) / 2);
  }

  return r;
};
```

时间复杂度：O(logx)，收敛速度更快，空间复杂度：O(1)。

## [367. 有效的完全平方数 🟢](https://leetcode.cn/problems/valid-perfect-square/description/)

题目要求：给定一个正整数 `num`，判断它是否是某个整数的平方数（`x² === num`）。不能使用内置函数。

### 暴力法

从 `1` 开始逐个尝试整数平方，直到平方值等于或超过 `num`。

``` js
var isPerfectSquare = function (num) {
  for (let i = 1; i * i <= num; i++) {
    if (i * i === num) return true;
  }

  return false;
};
```

时间复杂度：O(√n)，空间复杂度：O(1)。

### 二分查找 👍

使用二分查找优化搜索过程，在数值范围 `[1, num]` 内查找平方等于 `num` 的整数：

- 如果 `mid * mid === num`，则说明 `mid` 是 `num` 的完全平方数，返回 `true`；
- 如果 `mid * mid < num`，则说明该整数小了，则向右查找；
- 否则向左查找。

``` js
var isPerfectSquare = function (num) {
  if (num < 2) return true;

  let lo = 1;
  let hi = num;

  while (lo <= hi) {
    const mid = Math.floor((lo + hi) / 2);

    if (mid * mid === num) return true;
    if (mid * mid < num) {
      lo = mid + 1;
    } else {
      hi = mid - 1;
    }
  }

  return false;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。

### 牛顿迭代法 👍

同样可以使用牛顿迭代法来判断一个数是否为完全平方数。我们可以通过迭代逼近平方根，并检查其平方是否等于 `num`。

``` js
var isPerfectSquare = function (num) {
  if (num < 2) return true;
  
  let x = num;

  while (x * x > num) {
    x = Math.floor((x + num / x) / 2);
  }

  return x * x === num;
};
```

时间复杂度：O(logn)，空间复杂度：O(1)。
