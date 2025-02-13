# 复制链表问题

## 复制常规链表

### 1. 递归

终止条件：如果当前节点为空，则返回空。

递推过程：每次递归调用时，创建一个新的节点，并将当前节点的后继节点指向递归调用的结果，最后返回新节点。

``` js
var copyList = function (head) {
  if (!head) return null;
  
  const newNode = new Node(head.val);
  newNode.next = copyList(head.next);
  
  return newNode;
};
```

时间复杂度O(n)，空间复杂度O(n)。

### 2. 迭代

遍历链表，按照遍历的顺序创建链表节点，并将这些节点链接起来，形成一条新链表。

``` js
var copyList = function (head) {
  if (!head) return null;

  const dummy = new ListNode('dummy');
  let precursor = dummy;
  let curNode = head;

  while (curNode) {
    const newNode = new ListNode(curNode.val);
    precursor.next = newNode;
    curNode = curNode.next;
    precursor = newNode;
  }

  return dummy.next;
};
```

时间复杂度O(n)，空间复杂度O(1)。

## [138. 复制带随机指针的链表 🟡 ❤](https://leetcode.cn/problems/copy-list-with-random-pointer/description/)

题目要求：给定一个链表，每个节点包含一个额外增加的随机指针 `random`，该指针可以指向链表中的任何节点或 `null`。请对该链表进行深拷贝，并返回拷贝后的链表。

### 1. 递归 + 哈希表

由于随机指针的存在，在拷贝过程中可能会出现循环引用的情况，因此需要使用哈希表来存储已经拷贝过的节点。

终止条件：如果当前节点为空，则返回 `null`。

递推过程：如果当前节点不在哈希表中，我们首先对该节点进行拷贝，并将其添加到哈希表中；然后对当前节点的 `next` 和 `random` 指针进行拷贝。否则直接返回该节点。

``` js
var copyRandomList = function (head) {
  const map = new Map();

  const deepCopy = node => {
    if (!node) return null;

    if (!map.has(node)) {
      const newNode = new ListNode(node.val);
      map.set(node, newNode);
      newNode.next = deepCopy(node.next);
      newNode.random = deepCopy(node.random);
    }

    return map.get(node);
  };

  return deepCopy(head);
};
```

时间复杂度O(n)，空间复杂度O(n)。

### 2. 迭代 + 哈希表 👍

我们需要遍历两次链表。第一次遍历链表，拷贝当前节点并将其添加到哈希表中；第二次遍历链表，拷贝当前节点的 `next` 和 `random` 指针。最后返回拷贝后的链表。

``` js
var copyRandomList = function (head) {
  const map = new Map();
  let curNode = head;

  while (curNode) {
    map.set(curNode, new Node(curNode.val));
    curNode = curNode.next;
  }

  curNode = head;
  while (curNode) {
    map.get(curNode).next = map.get(curNode.next) ?? null;
    map.get(curNode).random = map.get(curNode.random) ?? null;
    curNode = curNode.next;
  }

  return map.get(head);
};
```

时间复杂度O(n)，空间复杂度O(1)。

以上两种解法都需要使用哈希表来记录每个节点的创建情况，我们可以使用原地拼接和节点拆分的方式来优化空间复杂度。

### 3. 原地拼接 + 节点拆分 👍

此解法需要三次遍历链表才能完成拷贝。

  - 第一次遍历需要将拷贝的节点作为当前节点的后继节点在原链表中进行拼接；

  - 第二次遍历在拷贝节点的基础上拷贝 `random` 指针；

  - 第三次遍历将拷贝链表从原链表中拆分出来并欢迎原链表。

``` js
var copyRandomList = function (head) {
  if (!head) return null;

  // 1. 拷贝节点并与原链表拼接
  let curNode = head;
  while (curNode)  {
    const copyNode = new ListNode(curNode.val);
    copyNode.next = curNode.next;
    curNode.next = copyNode;
    curNode = copyNode.next;
  }

  // 2. 拷贝随机指针
  curNode = head;
  while (curNode) {
    if (curNode.random) {
      curNode.next.random = curNode.random.next;
    }
    curNode = curNode.next.next;
  }

  // 3. 拆分/还原链表
  const dummy = new ListNode('dummy');
  let curNodeCopy = dummy;
  curNode = head;
  while (curNode) {
    curNodeCopy.next = curNode.next;
    curNode.next = curNode.next.next;
    curNodeCopy = curNodeCopy.next;
    curNode = curNode.next;
  }

  return dummy.next;
};
```

时间复杂度O(n)，空间复杂度O(1)。
