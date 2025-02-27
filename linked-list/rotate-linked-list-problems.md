# 旋转链表问题

## [61. 旋转链表 🟡](https://leetcode.cn/problems/rotate-list/description/)

题目要求：将链表中的每个节点向右旋转 `k` 个位置。

### 1. 循环链表 👍

将链表转换为循环链表，然后找到新的尾节点和头节点，并断开链表。

``` js
var rotateRight = function (head, k) {
  if (!head || k === 0) return head;

  // 统计链表长度并记录尾节点
  let n = 1;
  let tail = head;
  while (tail.next) {
    tail = tail.next;
    n++;
  }

  k %= n; // 避免多余旋转

  tail.next = head; // 将链表转换为循环链表

  // 寻找新的尾节点和头节点
  let newTail = head;
  for (let i = 0; i < n - k - 1; i++) {
    newTail = newTail.next;
  }
  const newHead = newTail.next;
  newTail.next = null; // 断开链表

  return newHead;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 2. 快慢指针

我们分别定义快指针 `fast` 和慢指针 `slow` 两个指针，初始化时均指向链表头节点。

`fast` 先移动 `k` 步，然后两个指针同时移动，直到 `fast` 到达链表尾部。此时 `slow` 所指向的节点就是新的尾节点，`slow` 的下一个节点就是新的头节点。

``` js
var rotateRight = function (head, k) {
  if (!head || k === 0) return head;

  // 统计链表长度并记录尾节点
  let n = 1;
  let tail = head;
  while (tail.next) {
    tail = tail.next;
    n++;
  }

  k %= n; // 避免多余旋转
  if (k === 0) return head;
  
  let fast = head;
  let slow = head;
  while (k-- > 0) fast = fast.next;

  while (fast.next) {
    slow = slow.next;
    fast = fast.next;
  }

  const newHead = slow.next;
  slow.next = null; // 断开链表
  fast.next = head; // 拼接链表

  return newHead;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。