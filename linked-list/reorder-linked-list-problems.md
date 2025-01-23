# 重排链表问题

将链表的节点重新排列，使得链表节点按特定的规则排列。 

## [86. 分隔链表 🟡 ❤](https://leetcode.cn/problems/partition-list/description/)

题目要求：给定一个链表和一个特定值 `x`，对链表进行分隔（排列），使得所有小于 `x` 的节点都排在大于或等于 `x` 的节点之前。

### 模拟

我们使用两个虚拟节点 `smallDummy` 和 `largeDummy` 分别维护两个链表，用于存储小于 `x` 的节点和大于等于 `x` 的节点。然后遍历链表，将当前节点与 `x` 进行比较，将节点插入到对应的链表中。最后将两个链表拼接完成重排。

``` js
var partition = function (head, x) {
  const smallDummy = new ListNode('smallDummy');
  const largeDummy = new ListNode('largeDummy');
  let small = smallDummy;
  let large = largeDummy;

  while (head) {
    if (head.val < x) {
      small.next = head;
      small = small.next;
    } else {
      large.next = head;
      large = large.next;
    }
    head = head.next;
  }

  large.next = null; // 切断与原链表的引用，避免形成环
  small.next = largeDummy.next; // 拼接链表
  
  return smallDummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [143. 重排链表 🟡 ❤](https://leetcode.cn/problems/reorder-list/description/)

题目要求：将给定的单向链表原地按回文顺序从外向内重新排列。

### 模拟

我们首先使用快慢指针寻找链表的中间节点；然后从中间节点将链表拆分为前后两个部分，并将后半部分链表反转；最后交替合并这两部分。

``` js
var reorderList = function (head) {
  if (!head.next || !head.next.next) return head;

  // 1. 寻找中间节点
  const middleNode = getMiddleNode(head);
  let l1 = head;
  let l2 = middleNode.next;
  
  // 2. 拆分链表并反转链表后半部分
  middleNode.next = null;
  l2 = reverseList(l2);

  // 3. 合并链表
  while (l1 && l2) {
    const temp1 = l1.next;
    const temp2 = l2.next;

    l1.next = l2;
    l2.next = temp1;

    l1 = temp1;
    l2 = temp2;
  }
};

const getMiddleNode = head => {
  let slow = head;
  let fast = head;

  while (fast.next && fast.next.next) {
    slow = slow.next;
    fast = fast.next.next;
  }

  return slow;
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

时间复杂度：O(n)，空间复杂度：O(1)。

## [328. 奇偶链表 🟡](https://leetcode.cn/problems/odd-even-linked-list/description/)

题目要求：将给定的单向链表原地按奇偶顺序重新排列，使得所有奇数节点位于偶数节点之前。

### 模拟

我们维护两个指针 `oddNode` 和 `evenNode`，分别指向奇数节点和偶数节点，并使用变量 `evenHead` 作为偶数链表的头节点，而 `head` 作为奇数链表的头节点。

遍历链表，根据当前链表的奇偶性进行判断，将节点插入到对应的链表中。遍历完成后，将奇数链表与偶数链表拼接完成重排。

``` js
var oddEvenList = function (head) {
  if (!head) return null;

  let oddNode = head;
  let evenNode = head.next;
  const evenHead = evenNode;

  while (evenNode && evenNode.next) {
    oddNode.next = evenNode.next;
    oddNode = oddNode.next;
    evenNode.next = oddNode.next;
    evenNode = evenNode.next;
  }
  oddNode.next = evenHead;

  return head;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。
