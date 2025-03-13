# 简化路径问题

## [71. 简化路径 🟡](https://leetcode.cn/problems/simplify-path/description/)

题目要求：将 Unix 风格的绝对路径转换为更加简洁的**规范路径**。

### 栈

首先，将路径以 `/` 分割成若干个部分。

然后，遍历这些分割的部分，对于每一个部分，我们使用栈来保存有效路径的部分：

- 如果遇到有效的目录，则入栈；
- 如果遇到 `..`，则将栈顶元素出栈，表示返回上一级目录；
- 如果遇到 ` ` 或者 `.` 则无需处理，因为 `.` 表示当前目录。

最后，将有效目录按顺序拼接起来，并在最前面增加根目录 `/`。

``` js
var simplifyPath = function (path) {
  const strs = path.split('/');
  const stack = [];

  for (const str of strs) {
    if (str === '' || str === '.') continue;
    if (str === '..') {
      if (stack.length > 0) stack.pop();
    } else {
      stack.push(str);
    }
  }

  return '/' + stack.join('/');
};
```

时间复杂度：O(n)，空间复杂度：O(n)。