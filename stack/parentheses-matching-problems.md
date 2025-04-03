# 括号匹配问题

## [20. 有效的括号 🟢](https://leetcode.cn/problems/valid-parentheses/description/)

题目要求：给定一个只包含大中小括号的字符串 `s`，判断这些括号的组合是否有效。

### 栈

遍历字符串，对于每个字符：

- 如果遇到左括号，则将其对应的右括号压入栈中；
- 如果遇到右括号，则弹出栈顶元素并判断弹出的栈顶元素是否与当前元素是否匹配，如果不匹配或者栈为空，则返回 `false`。

遍历结束后，如果栈为空则说明所有括号都能成功匹配。

``` js
var isValid = function (s) {
  if (s.length % 2 === 1) return false; // 优化

  const map = new Map([
    ['(', ')'],
    ['[', ']'],
    ['{', '}']
  ]);
  const stack = [];

  for (const c of s) {
    if (map.has(c)) {
      stack.push(map.get(c));
    } else if (stack.pop() !== c) {
      return false;
    }
  }

  return stack.length === 0;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [32. 最长有效括号 🔴](https://leetcode.cn/problems/longest-valid-parentheses/description/)

题目要求：给定一个仅包含 `()` 的字符串 `s`，找出并返回最长有效括号子串的长度。

### 1. 暴力法（超时）

遍历字符串，枚举所有可能的子串，判断子串是否是有效的括号。如果有效，则更新最长有效括号的长度。

``` js
var longestValidParentheses = function (s) {
  let maxLen = 0;

  for (let i = 0; i < s.length - 1; i++) {
    for (let j = i + 2; j <= s.length; j += 2) {
      if (isValid(s.slice(i, j))) {
        maxLen = Math.max(maxLen, j - i);
      };
    }
  }

  return maxLen;
};

const isValid = s => {
  const stack = [];

  for (const c of s) {
    if (c === '(') {
      stack.push(')');
    } else if (stack.pop() !== c) {
      return false;
    }
  }

  return stack.length === 0;
};
```

时间复杂度：O(n³)，空间复杂度：O(n)。

### 2. 栈 👍

使用栈来存储 `(` 的下标，并为栈初始化一个哨兵元素 `-1`，用于处理栈为空时的边界条件。

遍历字符串，对于每个字符：

- 如果遇到 `(`，则将其下标入栈；
- 如果遇到 `)`，表示当前括号匹配，则弹出栈顶元素。
  - 如果栈为空，表示当前括号无法匹配，则将当前下标入栈；
  - 否则，计算并更新最长有效括号的长度。

``` js
var longestValidParentheses = function (s) {
  const stack = [-1];
  let maxLen = 0;
  
  for (let i = 0; i < s.length; i++) {
    if (s[i] === '(') {
      stack.push(i);
    } else {
      stack.pop();
      if (stack.length === 0) {
        stack.push(i);
      } else {
        maxLen = Math.max(maxLen, i - stack.at(-1));
      }
    }
  }

  return maxLen;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 3. 动态规划 👍

状态定义：`dp[i]` 表示以下标 `i` 结尾的最长有效括号子串的长度。将 `dp` 数组所有元素初始化为 `0`。

转移方程：

$$
dp[i]=
\begin{cases}
dp[i-2] + 2, & 当前字符是右括号并且前一个字符是左括号 \\
dp[i-1] + 2 + dp[i - dp[i-1] - 2], & 当前字符是右括号并且前一个字符是右括号，且 s[i - dp[i-1] - 1] 是左括号
\end{cases}
$$

``` js
var longestValidParentheses = function (s) {
  const n = s.length;
  const dp = new Array(n).fill(0);
  let maxLen = 0;
  
  for (let i = 1; i <= n; i++) {
    if (s[i] === ')') {
      if (s[i - 1] === '(') {
        dp[i] = (i >= 2 ? dp[i - 2] : 0) + 2;
      } else if (i - dp[i - 1] > 0 && s[i - dp[i - 1] - 1] === '(') {
        dp[i] = dp[i - 1] + ((i - dp[i - 1]) >= 2 ? dp[i - dp[i - 1] - 2] : 0) + 2;
      }
      maxLen = Math.max(maxLen, dp[i]);
    }
  }
  return maxLen;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

TODO：关于动态规划的更多信息，我们将在后面的相关专题中详细介绍。