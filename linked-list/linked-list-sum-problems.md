# 链表求和问题

## [2. 两数相加 🟡](https://leetcode.cn/problems/add-two-numbers/description/)

题目要求：给定两个用链表**反向表示**的整数，每个节点包含一位数字，个位在头节点。对这两个整数求和，并以相同的链表形式返回结果。

### 模拟

模拟两数相加的过程。使用哨兵节点作为结果链表的头节点，我们同时遍历这两个链表，从两条链表的头节点开始逐位计算它们的和并与进位 `carry` 相加，将当前位的数字加入链表，并更新进位。如果当前位没有值，则使用 `0` 补位。

``` js
var addTwoNumbers = function (l1, l2) {
  const dummy = new ListNode('dummy');
  let curNode = dummy;
  let carry = 0;

  while (l1 || l2 || carry > 0) {
    const x = l1?.val ?? 0;
    const y = l2?.val ?? 0;
    const sum = x + y + carry;

    // 将当位的数字加入链表
    curNode.next = new ListNode(sum % 10);
    // 进位
    carry = Math.floor(sum / 10);

    // 移动指针
    curNode = curNode.next;
    l1 &&= l1.next;
    l2 &&= l2.next;
  }

  return dummy.next;
};
```

时间复杂度：O(max(m, n))，空间复杂度：O(1)。

## [445. 两数相加 II 🟡 ❤](https://leetcode.cn/problems/add-two-numbers-ii/description/)

题目要求：给定两个用链表**正向表示**的整数，每个节点包含一位数字，个位在尾节点。对这两个整数求和，并以相同的链表形式返回结果。

### 暴力法

先将给定的两个链表反转，再使用上一题的解题思路从链表头节点求和，最后将结果链表反转即可得到正向表示的和。

``` js
var addTwoNumbers = function (l1, l2) {
  l1 = reverseList(l1);
  l2 = reverseList(l2);

  const dummy = new ListNode('dummy');
  let curNode = dummy;
  let carry = 0;

  while (l1 || l2 || carry > 0) {
    const x = l1?.val ?? 0;
    const y = l2?.val ?? 0;
    const sum = x + y + carry;

    curNode.next = new ListNode(sum % 10);
    carry = Math.floor(sum / 10);

    curNode = curNode.next;
    l1 &&= l1.next;
    l2 &&= l2.next;
  }

  return reverseList(dummy.next);
};

const reverseList = head => {
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

时间复杂度：O(max(m, n))，空间复杂度：O(1)。

### 栈

先将链表节点的值分别入栈，然后利用栈后进先出的特点，从栈顶开始逐位计算它们的和与 `carry` 相加，将当前位的值加入结果链表，并更新进位。由于结果需要正向表示，所以我们依次将当前位的从结果链表头部插入，从而避免反转结果链表。

``` js
var addTwoNumbers = function (l1, l2) {
  const s1 = [];
  const s2 = [];

  while (l1) {
    s1.push(l1.val);
    l1 = l1.next;
  }
  while (l2) {
    s2.push(l2.val);
    l2 = l2.next;
  }

  let head = null;
  let carry = 0;

  while (s1.length || s2.length || carry > 0) {
    const x = s1?.pop() ?? 0;
    const y = s2?.pop() ?? 0;
    const sum = x + y + carry;

    // 逐位将当前位的数字从结果链表头部插入
    const newNode = new ListNode(sum % 10);
    newNode.next = head;
    head = newNode;

    carry = Math.floor(sum / 10);
  }

  return head;
};
```

时间复杂度：O(max(m, n))，空间复杂度：O(m + n)。