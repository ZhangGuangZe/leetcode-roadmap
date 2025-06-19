# 汉诺塔问题

## [面试题 08.06. 汉诺塔问题 🟢](https://leetcode.cn/problems/hanota-lcci/description/)

题目要求：将 `A` 柱上的所有盘子移动到 `C` 柱，移动过程中每次只能移动一个盘子，最终 `C` 柱上的所有盘子保持自上而下按升序排列。

### 递归

终止条件：当只有一个盘子时，直接直接从 `A` 柱移动到 `C` 柱。

递推过程：将 `A` 柱上的 `n - 1` 个盘子递归的移动到 `B` 柱，然后将 `A` 柱上的最后一个盘子移动到 `C` 柱，最后将 `B` 柱上的 `n - 1` 个盘子递归的移动到 `C` 柱。

``` js
var hanota = function (A, B, C) {
  const helper = (A, B, C, n) => {
    if (n === 1) {
      C.push(A.pop());
    } else {
      helper(A, C, B, n - 1);
      C.push(A.pop());
      helper(B, A, C, n - 1);
    }
  };
  helper(A, B, C, A.length);
};
```

时间复杂度：O(2^n)，空间复杂度：O(n)。