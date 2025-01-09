# 环形链表问题

## [141. 环形链表 🟢 ❤](https://leetcode.cn/problems/linked-list-cycle/description/)

题目要求：判断链表中是否有环。

### 1. 哈希表

利用哈希表来存储访问过的节点。遍历链表时检查当前节点是否出现在哈希表中。如果出现过，则说明链表中存在环。

``` js
var hasCycle = function (head) {
  const visited = new Set();
  let curNode = head;

  while (curNode) {
    if (visited.has(curNode)) return true;
    visited.add(curNode);
    curNode = curNode.next;
  }

  return false;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 2. 快慢指针 👍

我们定义两个指针慢指针 `slow` 和快指针 `fast`，初始时都指向链表的头节点。

`slow` 每次移动一步，`fast` 每次移动两步。如果存在环，`fast` 最终会与 `slow` 相遇。

``` js
var hasCycle = function (head) {
  let slow = head;
  let fast = head;

  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;

    if (slow === fast) return true;
  }

  return false;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [142. 环形链表 II 🟡 ❤](https://leetcode.cn/problems/linked-list-cycle-ii/description/)

在给定的的链表中找出入环的第一个节点，如果链表中没有环，则返回 `null`。

### 1. 哈希表

```js
var detectCycle = function (head) {
  const visited = new Set();
  let curNode = head;

  while (curNode) {
    if (visited.has(curNode)) return curNode;
    visited.add(curNode);
    curNode = curNode.next;
  }

  return null;
};
```

空间复杂度：O(n)，时间复杂度：O(n)。

### 2. 快慢指针 👍

首先使用快慢指针判断是否有环。如果有环，快指针回到头节点，快慢指针同时每次移动一步，直到两者相遇，相遇的节点即为入环节点。

```js
var detectCycle = function (head) {
  let slow = head;
  let fast = head;

  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;

    if (slow === fast) {
      fast = head;

      while (slow !== fast) {
        slow = slow.next;
        fast = fast.next;
      }

      return slow;
    }
  }

  return null;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [160. 相交链表 🟢 ❤](https://leetcode.cn/problems/intersection-of-two-linked-lists/description/)

题目要求：找出两个链表相交的起始节点。如果两个链表不相交，则返回 `null`。

### 1. 暴力法

遍历一个链表，对于每个节点，再遍历另一个链表，检查两个链表的节点是否相同。如果相同，则该节点即为相交的起始节点。

``` js
var getIntersectionNode = function (headA, headB) {
  let curNodeA = headA;

  while (curNodeA) {
    let curNodeB = headB;

    while (curNodeB) {
      if (curNodeA === curNodeB) return curNodeA;
      curNodeB = curNodeB.next;
    }
    curNodeA = curNodeA.next;
  }

  return null;
};
```

时间复杂度：O(mn)，空间复杂度：O(1)。

### 2. 哈希表

我们首先遍历其中一条链表，将当前节点存入哈希表中，然后遍历另一个链表，如果某一个节点在哈希表中出现过，那么该节点即为相交起始节点。

``` js
var getIntersectionNode = function (headA, headB) {
  const visited = new Set();
  let curNode = headA;

  while (curNode) {
    visited.add(curNode);
    curNode = curNode.next;
  }

  curNode = headB;
  while (curNode) {
    if (visited.has(curNode)) return curNode;
    curNode = curNode.next;
  }

  return null;
};
```

时间复杂度：O(m + n)，空间复杂度：O(m)。

### 3. 双指针 👍

使用两个指针 `curNodeA` 和 `curNodeB` ，分别从两个链表的头节点开始遍历。

当一个链表遍历完时，指针移动到另一个链表的头节点，继续遍历。

如果链表相交，两个指针最终会在交点处相遇。如果没有交点，它们会同时到达链表末尾并返回 `null`。

``` js
var getIntersectionNode = function (headA, headB) {
  let curNodeA = headA;
  let curNodeB = headB;

  while (curNodeA !== curNodeB) {
    curNodeA = curNodeA ? curNodeA.next : headB;
    curNodeB = curNodeB ? curNodeB.next : headA;
  }

  return curNodeA;
};
```

时间复杂度：O(m + n)，空间复杂度：O(1)。
