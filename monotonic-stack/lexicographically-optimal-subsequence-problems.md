# 字典序最优子序列问题

## [316. 去除重复字母 🟡](https://leetcode.cn/problems/remove-duplicate-letters/description/)

题目要求：将给定字符串 `s` 中的重复字母删除，使得每个字母只出现一次，返回字典序最小的子序列。

### 单调栈 👍

我们维护一个单调递增栈，使用 `lastIndex` 记录每个字母最后出现的位置，使用 `seen` 记录栈中是否存在该字母。

遍历字符串，对于每一个字母：

- 如果当前字母已经在栈中，则跳过；
- 如果栈顶字母大于当前字母，并且栈顶字母后续还会出现，则将栈顶字母弹出。

遍历结束后，将栈中的字母拼接成字符串，即可得到字典序最小的子序列。

``` js
var removeDuplicateLetters = function (s) {
  const stack = [];
  const lastIndex = new Map();
  const seen = new Set();

  for (let i = 0; i < s.length; i++) {
    lastIndex.set(s[i], i);
  }

  for (let i = 0; i < s.length; i++) {
    const c = s[i];
    if (seen.has(c)) continue;
    while (stack.length > 0 && stack.at(-1) > c && lastIndex.get(stack.at(-1)) > i) {
      seen.delete(stack.pop());
    }
    stack.push(c);
    seen.add(c);
  }

  return stack.join('');
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [402. 移掉 K 位数字 🟡](https://leetcode.cn/problems/remove-k-digits/description/)

题目要求：在一个以字符串表示的非负整数 `num` 中删除 `k` 位数字，使得剩下的数字最小，并以字符串的形式返回结果。

### 单调栈 + 贪心 👍

我们维护一个单调递增栈。

遍历数字字符串，对于每一个数字，依次将其入栈，如果遇到更小的数字，并且还可以删除数字，则将栈顶数字弹出。

遍历结束后，若还需要删除数字，则直接从末尾删除多余数字（单调递增）。最后处理前导零并返回结果。

``` js
var removeKdigits = function (num, k) {
  const stack = [];

  for (const digit of num) {
    while (stack.length > 0 && k > 0 && stack.at(-1) > digit) {
      stack.pop();
      k--;
    }
    stack.push(digit);
  }

	while (k-- > 0) stack.pop();

  // let res = '';
  // let isLeadingZeros = true;

  // for (const dight of stack) {
  //   if (isLeadingZeros && dight === '0') continue;
  //   isLeadingZeros = false;
  //   res += dight;
  // }

  // return res || '0';
	return stack.join('').replace(/^0+/, '') || '0';
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [1673. 找出最具竞争力的子序列 🟡](https://leetcode.cn/problems/find-the-most-competitive-subsequence/description/)

题目要求：在数组 `nums` 中找出长度为 `k` 且字典序最小的子序列。

### 单调栈 + 贪心 👍

我们维护一个单调递增的栈，用于保存当前最优子序列。

遍历数组 `nums`，对于每个元素 `num`：

- 如果栈顶元素大于当前元素，并且剩余的元素够用，则弹出栈顶元素；
- 如果栈中的元素不够 `k` 个，则直接将当前元素入栈。

遍历结束后，栈中的元素即为长度为 `k` 的且字典序最小的子序列。

``` js
var mostCompetitive = function (nums, k) {
  const n = nums.length;
  const stack = [];

  for (let i = 0; i < n; i++) {
    while (stack.length > 0 && stack.at(-1) > nums[i] && stack.length + n - i > k) stack.pop();
    if (stack.length < k) stack.push(nums[i]);
  }

  return stack;
};
```

时间复杂度：O(n)，空间复杂度：O(k)。