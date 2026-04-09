# 设计问题

## [232. 用栈实现队列 🟢 ❤](https://leetcode.cn/problems/implement-queue-using-stacks/description/)

题目要求：使用两个栈实现一个支持 `push`、`pop`、`peek`、和 `empty` 操作的队列。

我们使用一个用于存储的 `inStack` 栈，一个用于执行队列操作的 `outStack` 栈。

### 1. 入队时迁移

入队时：我们先将 `outStack` 中的元素全部移到 `inStack` 中，然后将新元素 `x` 入队到 `outStack` 中，最后再把 `inStack` 中的元素全部移到 `outStack` 中。

出队时：我们直接从 `outStack` 中出队即可。

``` js
var MyQueue = function () {
  this.inStack = [];
  this.outStack = [];
};

MyQueue.prototype.push = function (x) {
  while (this.outStack.length > 0) {
    this.inStack.push(this.outStack.pop());
  }
  this.outStack.push(x);
  while (this.inStack.length > 0) {
    this.outStack.push(this.inStack.pop());
  }
};

MyQueue.prototype.pop = function () {
  return this.outStack.pop();
};

MyQueue.prototype.peek = function () {
  return this.outStack.at(-1);
};

MyQueue.prototype.empty = function () {
  return this.outStack.length === 0;
};
```

时间复杂度：`push` 操作为 O(n)，`pop`、`peek` 和 `empty` 操作都为 O(1)。

### 2. 惰性迁移 👍

入队时：将元素 `x` 入队到 `inStack` 中。

出队时：如果 `outStack` 为空，则将 `inStack` 中的元素全部移到 `outStack` 中，再从 `outStack` 中出队。

``` js
var MyQueue = function () {
  this.inStack = [];
  this.outStack = [];
};

MyQueue.prototype.push = function (x) {
  this.inStack.push(x);
};

MyQueue.prototype.pop = function () {
  if (this.outStack.length === 0) {
    while (this.inStack.length > 0) {
      this.outStack.push(this.inStack.pop());
    }
  }
  return this.outStack.pop();
};

MyQueue.prototype.peek = function () {
  if (this.outStack.length === 0) {
    while (this.inStack.length > 0) {
      this.outStack.push(this.inStack.pop());
    }
  }
  return this.outStack.at(-1);
};

MyQueue.prototype.empty = function () {
  return this.inStack.length === 0 && this.outStack.length === 0;
};
```

时间复杂度：`push` 和 `empty` 操作为 O(1)；由于每个元素最多被压入两次栈，所以 `pop`、`peek` 操作均摊为 O(1)。

## [155. 最小栈 🟡 ❤](https://leetcode.cn/problems/min-stack/description/)

题目要求：设计一个支持 `push`、`pop`、`top` 和 `getMin` 操作，并能在常数时间内查找到最小元素的栈。

### 1. 两个栈 👍

我们使用两个栈 `stack` 和 `minStack`，分别用于存储数据和当前栈中的最小值。

入栈时：如果 `stack` 为空，则将当前元素同时压入 `stack` 和 `minStack` 中。如果当前元素小于等于 `minStack` 的栈顶元素，则将当前元素压入 `minStack` 中。

出栈时：如果 `stack` 出栈的元素等于 `minStack` 的栈顶元素，则将 `minStack` 的栈顶元素出栈。

``` js
var MinStack = function () {
  this.stack = [];
  this.minStack = [];
};

MinStack.prototype.push = function (val) {
  this.stack.push(val);
  if (this.minStack.length === 0 || val <= this.minStack.at(-1)) {
    this.minStack.push(val);
  }
};

MinStack.prototype.pop = function () {
  const top = this.stack.pop();
  if (top === this.minStack.at(-1)) {
    this.minStack.pop();
  }
};

MinStack.prototype.top = function () {
  return this.stack.at(-1);
};

MinStack.prototype.getMin = function () {
  return this.minStack.at(-1);
};
```

### 2. 一个栈

思路与上一个方法相同，只是将当前元素与最小元素组合成对象作为栈元素。

``` js
var MinStack = function () {
  this.stack = [];
};

MinStack.prototype.push = function (val) {
  this.stack.push({
    data: val,
    min: !this.stack.length ? val : Math.min(val, this.getMin())
  });
};

MinStack.prototype.pop = function () {
  this.stack.pop();
};

MinStack.prototype.top = function () {
  return this.stack.at(-1).data;
};

MinStack.prototype.getMin = function () {
  return this.stack.at(-1).min;
};
```

## [1472. 设计浏览器历史记录 🟡](https://leetcode.cn/problems/design-browser-history/description/)

题目要求：设计一个浏览器历史记录系统，支持访问新页面、后退和前进操作。

### 1. 两个栈 👍

我们可以使用两个栈分别存储历史记录和前进记录。

访问页面时：将新页面存入后退记录中，并清空前进记录。

后退时：将后退记录栈顶元素放入前进记录中，返回后退后的页面。

前进时：将前进记录栈顶元素放入后退记录中，返回前进后的页面。

``` js
var BrowserHistory = function (homepage) {
  this.history = [homepage];
  this.forwardStack = [];
};

BrowserHistory.prototype.visit = function (url) {
  this.history.push(url);
  this.forwardStack = [];
};

BrowserHistory.prototype.back = function (steps) {
  while (this.history.length > 1 && steps > 0) {
    this.forwardStack.push(this.history.pop());
    steps--;
  }
  return this.history.at(-1);
};

BrowserHistory.prototype.forward = function (steps) {
  while (this.forwardStack.length > 0 && steps > 0) {
    this.history.push(this.forwardStack.pop());
    steps--;
  }
  return this.history.at(-1);
};
```

### 2. 动态数组

维护一个历史记录列表和一个指针，指针指向当前页面。

访问新页面时：清除当前页面之后的所有历史记录，并将新页面添加到末尾。

后退和前进操作则调整指针位置，确保不超过历史记录的边界。

``` js
var BrowserHistory = function (homepage) {
  this.history = [homepage];
  this.currIndex = 0;
};

BrowserHistory.prototype.visit = function (url) {
  this.history = this.history.slice(0, this.currIndex + 1);
  this.history.push(url);
  this.currIndex = this.history.length - 1;
};

BrowserHistory.prototype.back = function (steps) {
  this.currIndex = Math.max(0, this.currIndex - steps);
  return this.history[this.currIndex];
};

BrowserHistory.prototype.forward = function (steps) {
  this.currIndex = Math.min(this.history.length - 1, this.currIndex + steps);
  return this.history[this.currIndex];
};
```