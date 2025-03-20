# 删除所有相邻重复项问题

## [1047. 删除字符串中的所有相邻重复项 🟢](https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string/description/)

题目要求：在给定字符串 `s` 中，删除所有相邻重复的字符，并返回删除后的字符串。

### 1. 暴力法（超时）

遍历字符串 `s`，寻找相邻的重复字符，并将它们删除。然后继续从头开始遍历，重复以上操作，直到没有相邻重复字符为止。

``` js
var removeDuplicates = function (s) {
  let flag = true;

  while (flag) {
    flag = false;
    let i = 0;

    while (i < s.length - 1) {
      if (s[i] === s[i + 1]) {
        s = s.slice(0, i) + s.slice(i + 2); // 删除相邻重复字符
        flag = true;
      } else {
        i++;
      }
    }
  }

  return s;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 栈 👍

使用栈来存储当前字符。遍历字符串 `s`，对于每个字符：

- 如果栈为空或者栈顶字符不等于当前字符，则将当前字符入栈；
- 否则，我们通过弹出栈顶字符来删除这些相邻重复项。

遍历完成后，栈中剩下的字符就是删除所有相邻重复项后的结果，我们只需将栈中的字符拼接为字符串并返回即可。

``` js
var removeDuplicates = function (s) {
  const stack = [];

  for (const c of s) {
    if (stack.length > 0 && stack.at(-1) === c) {
      stack.pop();
    } else {
      stack.push(c);
    }
  }

  return stack.join('');
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [1209. 删除字符串中的所有相邻重复项 II 🟡](https://leetcode.cn/problems/remove-all-adjacent-duplicates-in-string-ii/description/)

题目要求：在给定字符串 `s` 中，删除所有相邻重复 `k` 次的重复字符，并返回删除后的字符串。

### 栈

我们使用栈来存储遇到的字符及其出现的次数。

遍历字符串 `s`：
- 如果当前字符与栈顶元素相同，则将栈顶元素出现的次数加一；
- 否则将当前字符入栈并将其出现次数置为 `1`；
- 当栈顶元素出现的次数等于 `k` 时，则将栈顶元素弹出。

遍历完成后，将栈中的字符出现的次数拼接成最终的字符串。

``` js
var removeDuplicates = function (s, k) {
  const stack = [];

  for (const c of s) {
    if (stack.length > 0 && stack.at(-1).char === c) {
      stack.at(-1).count++;
    } else {
      stack.push({ char: c , count: 1 });
    }
    
    if (stack.at(-1).count === k) stack.pop();
  }

  return stack.map(({ char, count }) => char.repeat(count)).join('');
};
```

时间复杂度：O(n)，空间复杂度：O(n);