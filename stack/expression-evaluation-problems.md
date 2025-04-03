# 表达式求值问题

## [150. 逆波兰表达式求值 🟡 ❤](https://leetcode.cn/problems/evaluate-reverse-polish-notation/description/)

题目要求：为给定的逆波兰表达式求值并返回计算结果。

### 栈

由于逆波兰表达式是一种后缀表达式，操作符在操作数之后，所以时候使用栈来处理。

我们遍历表达式，对于每个 `token`：

- 如果遇到操作数则将其入栈；
- 如果遇到操作符则从栈中弹出两个操作数，并根据操作符计算结果，然后将结果入栈。

最后栈中剩下的唯一元素就是表达式的值。

``` js
var evalRPN = function (tokens) {
  const stack = [];

  for (const token of tokens) {
    if (token === '+' || token === '-' || token === '*' || token === '/') {
      const num2 = stack.pop();
      const num1 = stack.pop();
      let result;

      switch (token) {
        case '+':
          result = num1 + num2;
          break;
        case '-':
          result = num1 - num2;
          break;
        case '*':
          result = num1 * num2;
          break;
        case '/':
          result = Math.trunc(num1 / num2); // 去掉小数部分，也可以使用 (num1 / num2) | 0 或者 ~~(num1 / num2)
          break;
      }
      stack.push(result);
    } else {
      stack.push(Number(token));
    }
  }

  return stack.pop();
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [227. 基本计算器 II 🟡](https://leetcode.cn/problems/basic-calculator-ii/description/)

题目要求：给定一个包含加减乘除操作符的表达式，计算并返回表达式的结果。

### 栈

我们使用栈来处理操作符的优先级，比使用变量 `sign` 记录当前操作数之前的操作符。

遍历表达式，对于每一个字符。如果当前字符是数字，则需要将其解析为完整的操作数，然后根据当前操作符来决定如何处理操作数：

- 如果遇到 `+`，则将当前操作数入栈；
- 如果遇到 `-`，则将当前操作数取负数入栈；
- 如果遇到 `*` 和 `/`，则弹出栈顶操作数与当前操作数进行计算，然后将计算结果入栈。

最后栈中剩下的元素都是需要加减操作的操作数，只需全部求和即可得到表达式的结果。

``` js
var calculate = function (s) {
  const stack = [];
  let sign = '+';
  let num = 0;

  for (let i = 0; i < s.length; i++) {
    const c = s[i];
    if (isDigit(c)) {
      num = num * 10 + Number(c);
    }
    if ((!isDigit(c) && c !== ' ') || i === s.length - 1) {
      if (sign === '+') {
        stack.push(num);
      } else if (sign === '-') {
        stack.push(-num);
      } else if (sign === '*') {
        stack.push(stack.pop() * num);
      } else if (sign === '/') {
        stack.push(Math.trunc(stack.pop() / num));
      }
      sign = c;
      num = 0;
    }
  }

  return stack.reduce((a, b) => a + b, 0);
};

const isDigit = c => {
  return c >= '0' && c <= '9';
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [224. 基本计算器 🔴](https://leetcode.cn/problems/basic-calculator/description/)

题目要求：给定一个包含加减操作符和括号的表达式，计算并返回表达式的结果。

### 栈

我们使用栈来处理操作符和括号的优先级，使用变量 `sign` 记录当前的符号。遍历表达式，对于每个字符：

- 如果遇到数字，则将其解析为完整的操作数，并将其累加到结果中；
- 如果遇到 `+` 和 `-` 时，则更新当前符号；
- 如果遇到 `(`，则将当前结果和符号入栈，并重置当前结果和符号；
- 如果遇到 `)`，则从栈中弹出结果和操作符，并计算结果。

``` js
var calculate = function (s) {
  const stack = [];
  let sign = 1;
  let res = 0;

  for (let i = 0; i < s.length; i++) {
    const c = s[i];

    if (c === ' ') continue;
    if (isDigit(c)) {
      let num = Number(c);
      while (i + 1 < s.length && isDigit(s[i + 1])) {
        num = num * 10 + Number(s[i + 1]);
        i++;
      }
      res += sign * num;
    } else if (c === '+') {
      sign = 1;
    } else if (c === '-') {
      sign = -1;
    } else if (c === '(') {
      stack.push(res);
      stack.push(sign);
      res = 0;
      sign = 1;
    } else if (c === ')') {
      res = res * stack.pop() + stack.pop();
    }
  }

  return res;
};

const isDigit = c => {
  return c >= '0' && c <= '9';
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [394. 字符串解码 🟡](https://leetcode.cn/problems/decode-string/description/)

题目要求：根据给定规则对编码的字符串进行解码并返回解码后的字符串。

### 栈

我们可以使用栈来存储字符和重复的次数。遍历字符串 `s`，对于每个字符：

- 如果遇到数字，则将其解析为完整的字符重复次数；
- 如果遇到字母，则将其累加到结果中；
- 如果遇到 `[`，表示一个新的重复块开始，将当前字符和需要重复的次数入栈，并重置当前字符和重复次数；
- 如果遇到 `]`，表示重复块结束，从栈中弹出字符和重复次数，将它们拼接起来得到当前解码结果。

``` js
var decodeString = function (s) {
  const stack = [];
  let res = '';
  let num = 0;

  for (const c of s) {
    if (isDigit(c)) {
      num = num * 10 + Number(c);
    } else if (c === '[') {
      stack.push([res, num]);
      res = '';
      num = 0;
    } else if (c === ']') {
      const [preStr, count] = stack.pop();
      res = preStr + res.repeat(count);
    } else {
      res += c;
    }
  }

  return res;
};

const isDigit = c => {
  return c >= '0' && c <= '9';
};
```

时间复杂度：O(n)，空间复杂度：O(n)。
