# 滑动窗口问题

## [933. 最近的请求次数 🟢 ❤](https://leetcode.cn/problems/number-of-recent-calls/description/)

题目要求：实现一个类来计算特定时间范围内最近的请求数量。

### 队列

使用队列维护请求时间。

每次将 `t` 入队，同时从队首开始，依次将不在 `[t-3000, t]` 范围内的元素出队，然后返回队列的大小。

``` js
var RecentCounter = function () {
  this.queue = [];
};

RecentCounter.prototype.ping = function (t) {
  this.queue.push(t);
  while (this.queue[0] < t - 3000) {
    this.queue.shift();
  }
  return this.queue.length;
};
```

## [346. 数据流中的移动平均值 🟢 ❤ 🔒](https://leetcode.cn/problems/moving-average-from-data-stream/description/)

题目要求：实现一个类来计算数据流中当前窗口的移动平均值。

### 1. 队列

我们使用一个队列来存储当前窗口的所有元素，同时用一个变量 `sum` 来记录当前窗口的元素和。
 
在调用 `next()` 时，若队列已满则将队首出队并更新 `sum`，然后将新元素入队并更新 `sum`，最后计算并返回平均值。

``` js
var MovingAverage = function (size) {
  this.size = size;
  this.queue = [];
  this.sum = 0;
};

MovingAverage.prototype.next = function (val) {
  if (this.queue.length === this.size) {
    this.sum -= this.queue.shift();
  }
  this.queue.push(val);
  this.sum += val;
  return this.sum / this.queue.length;
};
```

### 2. 循环数组 👍

我们使用数组来模拟循环队列，避免数据搬移带来的性能开销。

通过 `front` 和 `rear` 两个指针来跟踪队首和队尾，使用 `length` 变量记录当前队列的长度，`sum` 变量记录当前队列的元素和。

``` js
var MovingAverage = function (size) {
  this.items = new Array(size + 1);
  this.size = size + 1;
  this.front = 0;
  this.rear = 0;
  this.length = 0;
  this.sum = 0;
};

MovingAverage.prototype.next = function (val) {
  if (this.length === this.size - 1) {
    this.sum -= this.items[this.front];
    this.front = (this.front + 1) % this.size;
  } else {
    this.length++;
  }
  this.items[this.rear] = val;
  this.rear = (this.rear + 1) % this.size;
  this.sum += val;
  return this.sum / this.length;
};
```

## [239. 滑动窗口最大值 🔴 ❤](https://leetcode.cn/problems/sliding-window-maximum/description/)

题目要求：给定一个数组 `nums`，有一个大小为 `k` 的滑动窗口从数组的最左侧移动到最右侧，滑动窗口每次只向右移动一位，返回 `n - k + 1` 个滑动窗口中的最大值。

### 1. 暴力法

遍历每个窗口，寻找每个窗口的最大值，将其添加到结果数组中。

``` js
var maxSlidingWindow = function (nums, k) {
  const res = [];

  for (let i = 0; i < nums.length - k + 1; i++) {
    let max = nums[i];
    for (let j = i + 1; j < i + k; j++) {
      max = Math.max(max, nums[j]);
    }
    res.push(max);
  }

  return res;
};
```

时间复杂度：O(nk)，空间复杂度：O(k)。

### 2. 优先队列

我们可以使用优先队列（最大堆）来维护滑动窗口的最大值。

初始化时，我们先将前 `k - 1` 个元素插入到优先队列中。然后每次向右滑动窗口，将新元素插入优先队列，同时判断堆顶元素是否滑出窗口，如果滑出则将堆顶元素删除。最后将当前窗口最大值（堆顶元素）加入结果中。

``` js
var maxSlidingWindow = function (nums, k) {
  const maxHeap = new Heap((a, b) => b[0] - a[0]);
  const res = [];

  for (let i = 0; i < k - 1; i++) {
    maxHeap.insert([nums[i], i]);
  }

  for (let i = k - 1; i < nums.length; i++) {
    maxHeap.insert([nums[i], i]);
    while (i - maxHeap.peek()[1] >= k) maxHeap.remove();
    res.push(maxHeap.peek()[0]);
  }

  return res;
};

class Heap {
  constructor(comparator = Heap.comparator) {
    this.container = [];
    this.size = 0;
    this.comparator = comparator;
  }
  static comparator(a, b) {
    return a - b;
  }
  getLeftIndex(i) {
    return 2 * i;
  }
  getRightIndex(i) {
    return 2 * i + 1;
  }
  getParentIndex(i) {
    return Math.floor(i / 2);
  }
  insert(data) {
    this.container[++this.size] = data;
    this.heapifyUp(this.size);
  }
  remove() {
    if (this.isEmpty()) return;

    let val = this.peek();
    this.container[1] = this.container[this.size--];
    this.heapifyDown(1);
    return val;
  }
  peek() {
    if (!this.isEmpty()) return this.container[1];
  }
  isEmpty() {
    return this.size === 0;
  }
  swap(a, i, j) {
    [a[i], a[j]] = [a[j], a[i]];
  }
  heapifyUp(i) {
    let pos = i || this.container.length - 1;
    while (pos > 1 && this.comparator(this.container[this.getParentIndex(pos)], this.container[pos]) > 0) {
      this.swap(this.container, pos, this.getParentIndex(pos));
      pos = this.getParentIndex(pos);
    }
  }
  heapifyDown(i = 1) {
    while (2 * i <= this.size) {
      let pos = i;
      const left = this.getLeftIndex(i);
      const right = this.getRightIndex(i);
      if (left <= this.size && this.comparator(this.container[i], this.container[left]) > 0) pos = left;
      if (right <= this.size && this.comparator(this.container[pos], this.container[right]) > 0) pos = right;
      if (pos === i) break;
      this.swap(this.container, i, pos);
      i = pos;
    }
  }
}
```

时间复杂度：O(nlogk)，空间复杂度：O(k)。

TODO，关于优先队列的更多信息，我们将在后面的相关专题中详细介绍。

### 3. 双端队列 👍

我们维护一个单调递减的双端队列，队列中存储的是元素的下标。保证队首始终是当前窗口最大值的下标。

遍历数组元素，对于当前元素，我们首先判断队首元素是否滑出窗口。然后我们将当前元素与队尾元素比较，如果队尾元素小于等于当前元素，则将队尾元素出队，直到队尾元素大于当前元素或者队列为空，然后将当前元素的下标入队。最后将队首元素加入结果中。

``` js
var maxSlidingWindow = function (nums, k) {
  const deque = [];
  const res = [];

  for (let i = 0; i < nums.length; i++) {
    if (i - deque[0] >= k) deque.shift();

    while (deque.length > 0 && nums[i] >= nums[deque.at(-1)]) deque.pop();
    deque.push(i);

    if (i >= k - 1) res.push(nums[deque[0]]);
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(k)。