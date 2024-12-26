# 链表遍历问题

## [剑指 Offer 06. 从尾到头打印链表 🟢](https://leetcode.cn/problems/cong-wei-dao-tou-da-yin-lian-biao-lcof/description/)

题目要求：倒序返回单向链表所有节点的值。

### 1. 递归

终止条件：链表为空，返回空数组。

递推过程：在递推过程中依次将当前节点的值存入数组中。

``` js
var reversePrint = function (head) {
  if (!head) return [];

  const res = reversePrint(head.next);
  res.push(head.val);

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 2. 迭代

从头到尾遍历链表，将每个节点的值依次存入数组中，然后将数组反转即为倒序后的结果。

``` js
var reversePrint = function (head) {
  const res = [];

  while (head) {
    res.push(head.val);
    head = head.next;
  }

  return res.reverse();
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [剑指 Offer 22. 链表中倒数第k个节点 🟢](https://leetcode.cn/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/description/)

题目要求：返回单向链表中的倒数第 `k` 个节点。

### 1. 暴力法（两次遍历）

由于单向链表不能从尾节点开始遍历，所以需要先从头节点遍历统计链表长度，然后再次从头到尾遍历 `n - k` 个节点即可找到倒数第 `k` 个节点。

``` js
var getKthFromEnd = function (head, k) {
  let n = 0;
  let curNode = head;
  // 统计链表长度
  while (curNode) {
    curNode = curNode.next;
    n++;
  }

  curNode = head;
  n -= k;
  // 找到倒数第 k 个节点
  while (n-- > 0) {
    curNode = curNode.next;
  }

  return curNode;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 2. 快慢指针 👍

我们可以定义两个指针快指针 `fast` 和慢指针 `slow`，初始时均指向 `head`。

通过让两个指针之间保持固定距离，巧妙地一次遍历即可找出倒数第 `k` 个节点。

首先让 `fast` 先移动 `k` 步，这样 `fast` 和 `slow` 之间的距离正好是 `k`。

然后让 `fast` 和 `slow` 同时向后移动一步，直到 `fast` 到达链表末尾，由于 `fast` 和 `slow` 之间始终保持 `k` 的距离，所以当 `fast` 到达链表末尾时，`slow` 正好指向倒数第 `k` 个节点。

``` js
var getKthFromEnd = function (head, k) {
  let fast = head;
  let slow = head;

  while (k-- > 0) fast = fast.next;

  while (fast) {
    fast = fast.next;
    slow = slow.next;
  }

  return slow;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [876. 链表的中间结点 🟢 ❤](https://leetcode.cn/problems/middle-of-the-linked-list/description/)

题目要求：在单向链表中找出并返回中间节点，如果链表有偶数个节点，返回第二个中间节点。

### 1. 暴力法（两次遍历）

首先遍历一次链表统计链表长度 `n`，并根据其计算中间节点的位置（`n/2`），然后再次遍历查找并返回中间节点。

``` js
var middleNode = function (head) {
  let n = 0;
  let curNode = head;
  // 统计链表长度
  while (curNode) {
    n++;
    curNode = curNode.next;
  }
  // 计算中间节点位置
  let mid = Math.floor(n / 2);

  curNode = head;
  // 遍历查找中间节点
  while (mid-- > 0) {
    curNode = curNode.next;
  }

  return curNode;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 2. 快慢指针 👍

定义两个指针快指针 `fast` 和慢指针 `slow` ，初始化时均指向头节点 `head`。

遍历链表时，`fast` 每次移动两步，`slow` 每次移动一步。当 `fast` 到达链表末尾时，`fast` 和 `slow` 之间的差值正好是链表长度的一半，所以 `slow` 所指向的即为链表的中间节点。

``` js
var middleNode = function (head) {
  let fast = head;
  let slow = head;

  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
  }

  return slow;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

**思考：如果链表有偶数个节点时，返回第一个中间节点该如何处理？**

只需让快慢指针少移动一步即可得到第一个中间节点。

``` js
var middleNode = function (head) {
  let fast = head;
  let slow = head;

  while (fast.next && fast.next.next) {
    fast = fast.next.next;
    slow = slow.next;
  }

  return slow;
};
```
