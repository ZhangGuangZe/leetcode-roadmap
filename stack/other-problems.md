# 其它问题

## [946. 验证栈序列 🟡 ❤](https://leetcode.cn/problems/validate-stack-sequences/description/)

题目要求：给定两个序列 `pushed` 和 `popped` ，根据这两个序列在空栈上进行压入（`push`）和弹出(`pop`)操作，判断最后能否恢复成空栈。

### 模拟

模拟栈的 `push` 和 `pop` 操作，每次 `push` 后检查能否按照 `popped` 顺序弹出。

遍历 `pushed` 序列，依次将当前元素入栈，然后检查栈顶元素是否与 `popped` 序列的当前元素相等，如果相等则弹出栈顶元素，并将当前元素指向下一个元素。重复以上过程，直到栈为空或栈顶元素不等于 `popped` 序列的当前元素为止。

``` js
var validateStackSequences = function (pushed, popped) {
  let stack = [];
  let i = 0;

  for (const num of pushed) {
    stack.push(num);
    while (stack.length > 0 && stack.at(-1) === popped[i]) {
      stack.pop();
      i++;
    }
  }

  return stack.length === 0;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [735. 小行星碰撞 🟡 ❤](https://leetcode.cn/problems/asteroid-collision/description/)

题目要求：给定一组小行星以及它们的大小和移动方向，返回根据**碰撞规则**碰撞后剩余的小行星。

碰撞规则：两个小行星相互碰撞，较小的小行星会爆炸。如果两颗小行星大小相同，则两颗小行星都会爆炸。两颗移动方向相同的小行星，永远不会发生碰撞。

### 模拟

遍历小行星列表，对于每个小行星：

- 如果小行星向右移动（正数），则将其压入栈中。
- 如果小行星向左移动（负数），则需要判断是否与栈顶的小行星碰撞：
  - 如果栈顶小行星向右移动且大小小于当前小行星，则栈顶小行星爆炸（出栈），继续判断当前小行星是否与前面的小行星碰撞；
  - 如果栈顶小行星向右移动且大小等于当前小行星，则这两个小行星爆炸，栈顶小行星出栈；
  - 如果栈顶小行星向右移动且大小大于当前小行星，则当前小行星爆炸，继续遍历下一个小行星；
  - 如果栈为空，或者栈顶小行星向左移动，表示当前小行星不会发生碰撞，则将当前小行星压入栈中。

遍历结束后，栈中剩下的小行星就是碰撞后剩下的小行星。

``` js
var asteroidCollision = function (asteroids) {
  const stack = [];

  for (const asteroid of asteroids) {
    if (asteroid > 0) {
      stack.push(asteroid);
    } else {
      while (stack.length > 0 && stack.at(-1) > 0 && stack.at(-1) < -asteroid) {
        stack.pop();
      }
      if (stack.length > 0 && stack.at(-1) === -asteroid) {
        stack.pop();
      } else if (stack.length === 0 || stack.at(-1) < 0) {
        stack.push(asteroid);
      }
    }
  }

  return stack;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。