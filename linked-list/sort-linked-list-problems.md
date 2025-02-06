# 排序链表问题

利用[排序算法](https://zhangguangze.github.io/blog/computer/algorithms/sorting.html)思想对链表进行排序。

## [147. 对链表进行插入排序 🟡](https://leetcode.cn/problems/insertion-sort-list/description/)

题目要求：使用插入排序算法对链表进行升序排序，并返回排序后的链表。

### 模拟

将链表分为已排序和未排序两个区间，每次从未排序区间当前第一个节点插入已排序区间合适的位置。

我们是使用 `lastSorted` 和 `curNode` 两个指针分别指向已排序的最后一个节点和未排序区间的当前节点。遍历链表，如果当前未排序区间的节点的值大于已排序区间的最后一个节点的值，则将当前节点插入已排序区间的末尾，否则从链表头节点开始遍历，将当前节点插入到已排序区间中合适的位置。

``` js
var insertionSortList = function (head) {
  if (!head.next) return head;

  const dummy = new ListNode('dummy');
  dummy.next = head;
  let lastSorted = head;
  let curNode = head.next;

  while (curNode) {
    if (lastSorted.val <= curNode.val) {
      lastSorted = lastSorted.next;
    } else {
      let precursor = dummy;
      while (precursor.next.val <= curNode.val) {
        precursor = precursor.next;
      }
      lastSorted.next = curNode.next;
      curNode.next = precursor.next;
      precursor.next = curNode;
    }
    curNode = lastSorted.next;
  }

  return dummy.next;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

## [148. 排序链表 🟡 ❤ ](https://leetcode.cn/problems/sort-list/description/)

题目要求：将给定的单向链表按升序排序，并返回排序后的链表。

### 1. 插入排序

思路与上一题相同，这里就不在赘述。

``` js
var sortList = function (head) {
  if (!head || !head.next) return head;

  const dummy = new ListNode('dummy');
  dummy.next = head;
  let lastSorted = head;
  let curNode = head.next;

  while (curNode) {
    if (lastSorted.val <= curNode.val) {
      lastSorted = lastSorted.next;
    } else {
      let precursor = dummy;
      while (precursor.next.val <= curNode.val) {
        precursor = precursor.next;
      }
      lastSorted.next = curNode.next;
      curNode.next = precursor.next;
      precursor.next = curNode;
    }
    curNode = lastSorted.next;
  }

  return dummy.next;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 归并排序（自上而下）👍

我们首先利用快慢指针来寻找中间节点，并将链表从中间节点分割为两条子链表；然后递归的分别对这两条子链表进行排序；最后再将这两条已排序的子链表合并，直到只有一条链表为止。

``` js
var sortList = function (head) {
  if (!head || !head.next) return head;
  
  // 1. 寻找中间节点并将其分割为两条子链表
  const middleNode = getMiddleNode(head);
  let l1 = head;
  let l2 = middleNode.next;
  middleNode.next = null;

  // 2. 分别对这这两条子链表进行排序
  l1 = sortList(l1);
  l2 = sortList(l2);

  // 3. 合并这两条已排序的链表
  return mergeTwoLists(l1, l2);
};

const getMiddleNode = head => {
  let slow = head;
  let fast = head;

  while (fast.next && fast.next.next) {
    slow = head.next;
    fast = fast.next.next;
  }

  return slow;
};

const mergeTwoLists = (l1, l2) => {
  const dummy = new ListNode('dummy');
  let curNode = dummy;

  while (l1 && l2) {
    if (l1.val < l2.val) {
      curNode.next = l1;
      l1 = l1.next;
    } else {
      curNode.next = l2;
      l2 = l2.next;
    }
    curNode = curNode.next;
  }
  curNode.next = l1 || l2;

  return dummy.next;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(logn)。

### 3. 归并排序（自下而上）👍

通过自下而上的方式进行归并排序，逐步将较小的子链表合并排序，两两合并，然后四四合并...，直到合并出为一条链表为止。

``` js
var sortList = function (head) {
  if (!head || !head.next) return head;
  
  let size = 0;
  let curNode = head;
  while (curNode) {
    size++;
    curNode = curNode.next;
  }

  let dummy = new ListNode('dummy');
  dummy.next = head;

  for (let i = 1; i < size; i *= 2) {
    let precursor = dummy;
    let curNode = dummy.next;

    while (curNode) {
      let l1 = curNode;
      let l2 = splitList(l1, i);
      curNode = splitList(l2, i);
      
      precursor.next = mergeTwoLists(l1, l2);
      while (precursor.next) {
        precursor = precursor.next;
      }
    }
  }

  return dummy.next;
};

const splitList = (head, len) => {
  if (!head) return null;
  let curNode = head;

  for (let i = 1; i < len && curNode.next; i++) {
    curNode = curNode.next;
  }

  const list = curNode.next;
  curNode.next = null;
  return list;
};

const mergeTwoLists = (l1, l2) => {
  // ...
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。