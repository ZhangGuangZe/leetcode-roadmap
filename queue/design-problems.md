# 设计问题

## [225. 用队列实现栈 🟢 ❤](https://leetcode.cn/problems/implement-stack-using-queues/description/)

题目要求：使用两个队列实现一个栈，并支持栈的 `push`、`top`、`pop` 和 `empty` 操作。

### 1. 两个队列 👍

使用两个队列来实现栈的操作，其中 `inQueue`  用于存储栈内的元素，`outQueue` 用于辅助实现栈的操作。

入栈时：我们首先将元素入队到 `outQueue`，然后将 `inQueue` 队列中的所有元素入队到 `outQueue` 队列，再将 `inQueue` 与 `outQueue` 队列互换，则 `inQueue` 的元素即为栈内的元素，队列的队首即为栈的栈顶。

出栈时：只需移除 `inQueue` 队列的队首元素并返回即可。

``` js
MyStack.prototype.push = function (x) {
  this.outQueue.push(x);
  while (this.inQueue.length > 0) {
    this.outQueue.push(this.inQueue.shift());
  }
  [this.inQueue, this.outQueue] = [this.outQueue, this.inQueue];
};

MyStack.prototype.pop = function () {
  return this.inQueue.shift();
};

MyStack.prototype.top = function () {
  return this.inQueue[0];
};

MyStack.prototype.empty = function () {
  return this.inQueue.length === 0;
};
```

### 2. 一个队列 👍

使用一个队列实现栈的操作，在 `push` 操作时调整元素顺序。

入栈时：将队列中除新元素外的所有元素依次出队重新入队，此时队首元素即为新入栈的元素，且队列的队首即为栈的栈顶。

``` js
var MyStack = function () {
  this.queue = [];
};

MyStack.prototype.push = function (x) {
  this.queue.push(x);
  for (let i = 0; i < this.queue.length - 1; i++) {
    this.queue.push(this.queue.shift());
  }
};

MyStack.prototype.pop = function () {
  return this.queue.shift();
};

MyStack.prototype.top = function () {
  return this.queue[0];
};

MyStack.prototype.empty = function () {
  return this.queue.length === 0;
};
```

## [622. 设计循环队列 🟡](https://leetcode.cn/problems/design-circular-queue/description/)

题目要求：设计一个循环队列，支持 `enQueue`、`deQueue`、`isEmpty`、`isFull` 等操作。

### 1. 循环数组 👍

使用求余的方式模拟循环数组，并预留一个空位来区分队空和队满两种情况。

定义指针 `front` 和 `rear`，分别指向队首和队尾。

``` js
var MyCircularQueue = function (k) {
  this.items = new Array(k + 1);
  this.size = k + 1;
  this.front = 0;
  this.rear = 0;
};

MyCircularQueue.prototype.enQueue = function (value) {
  if (this.isFull()) return false;
  this.items[this.rear] = value;
  this.rear = (this.rear + 1) % this.size;
  return true;
};

MyCircularQueue.prototype.deQueue = function () {
  if (this.isEmpty()) return false;
  this.front = (this.front + 1) % this.size;
  return true;
};

MyCircularQueue.prototype.Front = function () {
  if (this.isEmpty()) return -1;
  return this.items[this.front];
};

MyCircularQueue.prototype.Rear = function () {
  if (this.isEmpty()) return -1;
  return this.items[(this.rear - 1 + this.size) % this.size];
};

MyCircularQueue.prototype.isEmpty = function () {
  return this.front === this.rear;
};

MyCircularQueue.prototype.isFull = function () {
  return this.front === (this.rear + 1) % this.size;
};
```

### 2. 循环链表 👍

使用循环链表模拟循环队列。定义变量 `size` 表示队列的长度，变量 `head` 和 `tail` 分别表示队列的首和尾。

入队时，将新元素插入到队尾，并保持循环；出队时，将头节点删除。

``` js
var MyCircularQueue = function (k) {
  this.capacity = k;
  this.size = 0;
  this.head = null;
  this.tail = null;
};

MyCircularQueue.prototype.enQueue = function (value) {
  if (this.isFull()) return false;
  
  const newNode = new Node(value);
  if (this.isEmpty()) {
    this.head = newNode;
  } else {
    this.tail.next = newNode;
  }
  this.tail = newNode;
  this.tail.next = this.head; // 保持循环
  this.size++;

  return true;
};

MyCircularQueue.prototype.deQueue = function () {
  if (this.isEmpty()) return false;
  this.head = this.head.next;
  this.size--;
  return true;
};

MyCircularQueue.prototype.Front = function () {
  if (this.isEmpty()) return -1;
  return this.head.val;
};

MyCircularQueue.prototype.Rear = function () {
  if (this.isEmpty()) return -1;
  return this.tail.val;
};

MyCircularQueue.prototype.isEmpty = function () {
  return this.size === 0;
};

MyCircularQueue.prototype.isFull = function () {
  return this.size === this.capacity;
};

class Node {
  constructor(val, next = null) {
    this.val = val;
    this.next = next;
  }
}
```

## [641. 设计循环双端队列 🟡](https://leetcode.cn/problems/design-circular-deque/description/)

题目要求：设计一个双端队列，支持 `insertFront`、`insertLast`、`deleteFront`、`deleteLast`、`getFront`、`getRear`、`isEmpty` 和 `isFull` 操作。

### 1. 循环数组 👍

利用循环数组实现双端队列。需要浪费一个空间来处理队空和队满的情况。

使用两个指针 `front` 和 `rear`，分别指向队列的队首和队尾。

入队时：通过求余运算找到队首的上一个位置或队尾，然后插入元素；

出队时：将队首往后移动一位或将队尾往前移动一位。

``` js
var MyCircularDeque = function (k) {
  this.size = k + 1;
  this.items = new Array(this.size);
  this.front = 0;
  this.rear = 0;
};

MyCircularDeque.prototype.insertFront = function (value) {
  if (this.isFull()) return false;
  this.front = (this.front - 1 + this.size) % this.size;
  this.items[this.front] = value;
  return true;
};

MyCircularDeque.prototype.insertLast = function (value) {
  if (this.isFull()) return false;
  this.items[this.rear] = value;
  this.rear = (this.rear + 1) % this.size;
  return true;
};

MyCircularDeque.prototype.deleteFront = function () {
  if (this.isEmpty()) return false;
  this.front = (this.front + 1) % this.size;
  return true;
};

MyCircularDeque.prototype.deleteLast = function () {
  if (this.isEmpty()) return false;
  this.rear = (this.rear - 1 + this.size) % this.size;
  return true;
};

MyCircularDeque.prototype.getFront = function () {
  if (this.isEmpty()) return -1;
  return this.items[this.front];
};

MyCircularDeque.prototype.getRear = function () {
  if (this.isEmpty()) return -1;
  return this.items[(this.rear - 1 + this.size) % this.size];
};

MyCircularDeque.prototype.isEmpty = function () {
  return this.front === this.rear;
};

MyCircularDeque.prototype.isFull = function () {
  return this.front === (this.rear + 1) % this.size;
};
```

### 2. 双向链表 👍

使用双向链表模拟双端队列。定义变量 `capacity` 表示队列容量，变量 `size` 表示队列长度，指针 `head` 和 `tail` 分别指向链表的首和尾节点。

入队时：在双向链表头部或尾部插入节点；

出队时：删除双向链表的头节点或尾节点。

``` js
var MyCircularDeque = function (k) {
  this.capacity = k;
  this.size = 0;
  this.head = null;
  this.tail = null;
};

MyCircularDeque.prototype.insertFront = function (value) {
  if (this.isFull()) return false;

  const newNode = new Node(value);;
  if (this.isEmpty()) {
    this.head = newNode;
    this.tail = newNode;
  } else {
    newNode.next = this.head;
    this.head.prev = newNode;
    this.head = newNode;
  }
  this.size++;
  return true;
};

MyCircularDeque.prototype.insertLast = function (value) {
  if (this.isFull()) return false;

  const newNode = new Node(value);
  if (this.isEmpty()) {
    this.head = newNode;
    this.tail = newNode;
  } else {
    this.tail.next = newNode;
    newNode.prev = this.tail;
    this.tail = newNode;
  }
  this.size++;
  return true;
};

MyCircularDeque.prototype.deleteFront = function () {
  if (this.isEmpty()) return false;

  if (this.head === this.tail) { // 只有一个节点
    this.head = null;
    this.tail = null;
  } else {
    this.head = this.head.next;
    this.head.prev = null
  }
  this.size--;
  return true;
};

MyCircularDeque.prototype.deleteLast = function () {
  if (this.isEmpty()) return false;

  if (this.head === this.tail) { // 只有一个节点
    this.head = null;
    this.tail = null;
  } else {
    this.tail = this.tail.prev;
    this.tail.next = null;
  }
  this.size--;
  return true;
};

MyCircularDeque.prototype.getFront = function () {
  return !this.isEmpty() ? this.head.val : -1;
};

MyCircularDeque.prototype.getRear = function () {
  return !this.isEmpty() ? this.tail.val : -1;
};

MyCircularDeque.prototype.isEmpty = function () {
  return this.size === 0;
};

MyCircularDeque.prototype.isFull = function () {
  return this.size === this.capacity;
};

class Node {
  constructor(val, prev = null, next = null) {
    this.val = val;
    this.prev = prev;
    this.next = next;
  }
}
```

使用循环数组性能好，但处理循环下标的逻辑比较复杂；而使用链表逻辑清晰简单，灵活性高，但空间占用大。