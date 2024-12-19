## 反转链表问题

### [206. 反转链表 🟢 ❤](https://leetcode.cn/problems/reverse-linked-list/description/)

题目要求：将单向链表反转，并返回反转后的链表。

#### 1. 递归 👍

终止条件：链表为空或只有一个节点，直接返回该节点。

递推过程：将当前节点的后继节点的 `next` 指针指向当前节点，然后再将当前节点的 `next` 指针改为 `null`。

``` js
var reverseList = function (head) {
  if (!head || !head.next) return head;

  const newHead = reverseList(head.next);
  head.next.next = head;
  head.next = null;

  return newHead;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

#### 2. 迭代 👍

使用两个变量 `precursor` 和 `curNode`，分别用于记录当前节点的前驱节点和当前节点。

遍历链表，将当前节点的 `next` 指针指向其前驱节点，并更新前驱节点和当前节点。直到链表遍历完成。

``` js
var reverseList = function (head) {
  let precursor = null;
  let curNode = head;

  while (curNode) {
    const temp = curNode.next;
    curNode.next = precursor;
    precursor = curNode;
    curNode = temp;
  }

  return precursor;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [92. 反转链表 II 🟡 ❤](https://leetcode.cn/problems/reverse-linked-list-ii/)

题目要求：在单向链表中反转 `left` 到 `right` 范围内的所有节点，并返回反转后的链表。

#### 1. 模拟

先查找待反转子链表的前驱节点和尾节点，然后切断子链表，最后反转并拼接子链表。

拼接的思路是：把待反转子链表的前驱节点的 `next` 指针指向反转后的链表头节点，再把反转后的链表尾节点的 `next` 指针指向原链表的后继节点。

``` js
var reverseBetween = function (head, left, right) {
  const dummy = new ListNode('dummy');
  dummy.next = head;

  // 查找待反转子链表前驱节点
  let precursor = dummy;
  for (let i = 0; i < left - 1; i++) {
    precursor = precursor.next;
  }
  // 查找待反转子链表尾节点
  let tail = precursor;
  for (let i = 0; i < right - left + 1; i++) {
    tail = tail.next;
  }

  // 切断子链表
  let successor = tail.next;
  let curNode = precursor.next;
  precursor.next = null;
  tail.next = null;
  
  // 反转并拼接子链表
  precursor.next = reverseList(curNode);
  curNode.next = successor;

  return dummy.next;
};

const reverseList = head => {
  if (!head || !head.next) return head;

  const newHead = reverseList(head.next);
  head.next.next = head;
  head.next = null;

  return newHead;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

#### 2. 一次遍历 👍

上一种解法最坏需要遍历两次链表，我们可以在遍历链表时依次将待反转子链表当前节点反转到待反转子链表头部，从而在一次遍历中完成反转。

``` js
var reverseBetween = function (head, left, right) {
  const dummy = new ListNode('dummy');
  dummy.next = head;

  // 查找待反转子链表前驱节点
  let precursor = dummy;
  for (let i = 0; i < left - 1; i++) {
    precursor = precursor.next;
  }
  
  // 依次将待反转子链表当前节点反转到待反转子链表头部
  let curNode = precursor.next;
  for (let i = 0; i < right - left; i++) {
    const successor = curNode.next;
    curNode.next = successor.next;
    successor.next = precursor.next;
    precursor.next = successor;
  }

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [24. 两两交换链表中的节点 🟡](https://leetcode.cn/problems/swap-nodes-in-pairs/description/)

题目要求：在单向链表中两两交换相邻的节点，并返回交换后的链表。

#### 1. 递归

终止条件：链表为空或只有一个节点，直接返回该节点。

递推过程：当链表只有两个节点时，将第一个节点的 `next` 指针指向第二个节点，再将第二个节点的 `next` 指针指向第一个节点，最后返回第二个节点。

``` js
var swapPairs = function (head) {
  if (!head || !head.next) return head;

  const firstNode = head;
  const secondNode = head.next;
  firstNode.next = swapPairs(secondNode.next);
  secondNode.next = firstNode;

  return secondNode;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

#### 2. 迭代

使用两个变量 `precursor` 和 `curNode`，分别用于记录当前节点的前驱节点和当前节点。

遍历链表，依次交换当前相邻的两个节点，直到链表遍历完成。

``` js
var swapPairs = function (head) {
  const dummy = new ListNode('dummy');
  dummy.next = head;

  let precursor = dummy;
  let curNode = dummy.next;
  while (curNode && curNode.next) {
    const firstNode = curNode;
    const secondNode = curNode.next;

    // 交换相邻节点
    firstNode.next = secondNode.next;
    secondNode.next = firstNode;
    precursor.next = secondNode;

    // 更新指针
    precursor = firstNode;
    curNode = firstNode.next;
  }

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [25. K 个一组翻转链表 🔴](https://leetcode.cn/problems/reverse-nodes-in-k-group/description/)

题目要求：在单向链表中，对每 `k` 个节点一组进行反转，并返回反转后的链表。如果剩余节点数量不足 `k` 个，则无需反转。

#### 模拟

遍历链表，将链表节点按 `k` 个一组分为 `n` 个子链表，依次反转子链表，并拼接子链表。

``` js
var reverseKGroup = function (head, k) {
  const dummy = new ListNode('dummy');
  dummy.next = head;
  let precursor = dummy;
  let curNode = dummy.next;

  while (curNode) {
    // 将链表节点按 k 个一组分为 n 个子链表
    let tail = precursor;
    for (let i = 0; i < k; i++) {
      tail = tail.next;
      // 剩余节点少于 k 个
      if (!tail) return dummy.next;
    }

    const successor = tail.next;
    // 切断子链表
    tail.next = null;

    // 反转子链表并拼接子链表
    precursor.next = reverseList(curNode);
    curNode.next = successor;

    // 更新指针
    precursor = curNode;
    curNode = successor;
  }

  return dummy.next;
};

const reverseList = head => {
  if (!head || !head.next) return head;

  const newHead = reverseList(head.next);
  head.next.next = head;
  head.next = null;

  return newHead;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。
