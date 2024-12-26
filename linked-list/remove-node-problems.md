# 删除链表节点问题

## 删除值等于指定值的节点

### [剑指 Offer 18. 删除链表的节点 🟢](https://leetcode.cn/problems/shan-chu-lian-biao-de-jie-dian-lcof/description/)

题目要求：在一个单向链表中删除值为指定值的节点（节点的值互不相同），返回删除后的链表。

#### 1. 递归 👍

终止条件：链表为空时返回 `null`。

递推过程：如果当前节点的值等于指定值，则删除当前节点，否则保留当前节点。

``` js
var deleteNode = function(head, val) {
  if (!head) return null;

  head.next = deleteNode(head.next, val);

  return head.val === val ? head.next : head;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

#### 2. 迭代 👍

这里需要考虑删除头节点和其它节点的情况。如果删除的是头节点，我们只需返回头节点的下一个节点；否则就需要遍历链表，找到要删除的节点，并删除它。

``` js
var deleteNode = function (head, val) {
  if (head && head.val === val) return head.next;
  
  let curNode = head;

  while (curNode && curNode.next) {
    if (curNode.next.val === val) {
      curNode.next = curNode.next.next;
    } else {
      curNode = curNode.next;
    }
  }

  return head;
};
```

我们可以使用哨兵节点来简化边界条件的处理，从而只考虑删除其它节点的情况。

``` js
var deleteNode = function(head, val) {
  const dummy = new ListNode('dummy');
  dummy.next = head;

  let curNode = dummy;
  while (curNode.next) {
    if (curNode.next.val === val) {
      curNode.next = curNode.next.next;
    } else {
      curNode = curNode.next;
    }
  }

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [203. 移除链表元素 🟢 ❤](https://leetcode.cn/problems/remove-linked-list-elements/)

题目要求：在一个单向链表中删除所有值为指定值的节点，返回删除后的链表。

#### 1. 递归 👍

此题的递归解法与上一题相同，这里就不在赘述。

#### 2. 迭代 👍

与上一题不同的是，此题需要处理重复节点的情况。我们通过循环遍历删除值为指定值的所有节点。

``` js
var removeElements = function (head, val) {
  // 删除头节点
  while (head && head.val === val) {
    head = head.next;
  }

  // 删除其他节点
  let curNode = head;
  while (curNode && curNode.next) {
    if (curNode.next.val === val) {
      curNode.next = curNode.next.next;
    } else {
      curNode = curNode.next;
    }
  }

  return head;
};
```

同样可以使用哨兵节点来简化边界条件的处理，从而只考虑删除其它节点的情况。

``` js
var removeElements = function (head, val) {
  const dummy = new ListNode('dummy');
  dummy.next = head;
  
  // 删除其他节点
  let curNode = dummy;
  while (curNode.next) {
    if (curNode.next.val === val) {
      curNode.next = curNode.next.next;
    } else {
      curNode = curNode.next;
    }
  }

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [3217. 从链表中移除在数组中存在的节点 🟡](https://leetcode.cn/problems/delete-nodes-from-linked-list-present-in-array/description/)

题目要求：在一个单向链表中删除所有存在于数组 `nums` 中的节点，返回删除后的链表。

#### 1. 暴力法（超时）

遍历链表，检查数组中是否存在当前节点的值，如果存在则删除当前节点，否则继续遍历链表。重复以上过程，直到链表遍历结束为止。

``` js
var modifiedList = function (nums, head) {
  const dummy = new ListNode('dummy');
  dummy.next = head;
  let curNode = dummy;

  while (curNode.next) {
    if (nums.includes(curNode.next.val)) {
      curNode.next = curNode.next.next;
    } else {
      curNode = curNode.next;
    }
  }

  return dummy.next;
};
```

时间复杂度：O(mn)，空间复杂度：O(1)。

#### 2. 哈希表 👍

我们可以将数组转换为哈希表来快速查找数组中的元素。遍历链表，如果当前节点的值在哈希表中，则删除当前节点，否则继续遍历链表。重复以上过程，直到链表遍历结束为止。

``` js
var modifiedList = function (nums, head) {
  const dummy = new ListNode('dummy');
  dummy.next = head;
  const seen = new Set(nums);
  let curNode = dummy;

  while (curNode.next) {
    if (seen.has(curNode.next.val)) {
      curNode.next = curNode.next.next;
    } else {
      curNode = curNode.next;
    }
  }

  return dummy.next;
};
```

时间复杂度：O(m + n)，空间复杂度：O(m)。

## 删除指定节点

### [19. 删除链表的倒数第 N 个结点 🟡 ❤](https://leetcode.cn/problems/remove-nth-node-from-end-of-list/description/)

题目要求：在一个单向链表中删除倒数第 `n` 个节点，返回删除后的链表。

#### 1. 暴力法

首先遍历链表，获取链表长度；然后再次遍历链表，找到倒数第 `n` 个节点的前驱节点（`length - n`）；最后执行删除操作。

``` js
var removeNthFromEnd = function (head, n) {
  let length = 0;
  let curNode = head;
  while (curNode) {
    length++;
    curNode = curNode.next;
  }

  const dummy = new ListNode('dummy');
  dummy.next = head;
  curNode = dummy;
  for (let i = 0; i < length - n; i++) {
    curNode = curNode.next;
  }
  curNode.next = curNode.next.next;

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

#### 2. 快慢指针 👍

我们定义两个指针慢指针 `slow` 和快指针 `fast`，初始化是均指向哨兵节点。

`fast` 指针先移动 `n` 步，然后 `slow` 指针和 `fast` 指针同时移动，直到 `fast` 指针移动到链表尾节点。此时 `slow` 指针恰好指向倒数第 `n` 个节点的前驱节点。通过快慢指针即可一次遍历完成删除操作。

``` js
var removeNthFromEnd = function (head, n) {
  let dummy = new ListNode('dummy');
  dummy.next = head;
  let slow = dummy;
  let fast = dummy;

  while (n-- > 0) fast = fast.next;

  while (fast.next) {
    slow = slow.next;
    fast = fast.next;
  }
  slow.next = slow.next.next;

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [2095. 删除链表的中间节点 🟡](https://leetcode.cn/problems/delete-the-middle-node-of-a-linked-list/description/)

题目要求：在单向链表中删除中间节点，返回删除后的链表。

#### 1. 暴力法

首先遍历链表，获取链表长度；然后再次遍历链表，找到中间节点的前驱节点；最后执行删除操作。

``` js
var deleteMiddle = function (head) {
  let n = 0;
  let curNode = head;
  while (curNode) {
    n++;
    curNode = curNode.next;
  }

  let mid = Math.floor(n / 2);
  const dummy = new ListNode('dummy');
  dummy.next = head;
  curNode = dummy;
  while (mid-- > 0) {
    curNode = curNode.next;
  }
  curNode.next = curNode.next.next;

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

#### 2. 快慢指针 👍

我们定义两个指针慢指针 `slow` 和快指针 `fast`，初始化是均指向哨兵节点。

在链表遍历过程中，`slow` 每次向后移动一步，`fast` 每次向后移动两步。当 `fast` 移动到链表尾节点时，`slow` 恰好指向中间节点的前驱节点。通过快慢指针即可一次遍历完成删除操作。

``` js
var deleteMiddle = function (head) {
  const dummy = new ListNode('dummy');
  dummy.next = head;

  let slow = dummy;
  let fast = dummy;

  while (fast.next && fast.next.next) {
    slow = slow.next;
    fast = fast.next.next;
  }
  slow.next = slow.next.next;

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [237. 删除链表中的节点 🟡 ❤](https://leetcode.cn/problems/delete-node-in-a-linked-list/description/)

题目要求：在一个不知道链表头节点并且节点值都是唯一的单向链表中，删除链表中指定节点 `node`（该节点不是链表的尾节点）。

#### 暴力法

将当前节点的值替换为后继节点的值，然后删除后继节点。

``` js
var deleteNode = function (node) {
  node.val = node.next.val;
  node.next = node.next.next;
};
```

时间复杂度：O(1)，空间复杂度：O(1)。

### [2487. 从链表中移除节点 🟡](https://leetcode.cn/problems/remove-nodes-from-linked-list/description/)

题目要求：在一个单向链表中删除所有值小于其后继节点的节点，返回删除后的链表。

#### 1. 递归

终止条件：链表为空时返回 `null`。

递推过程：如果当前节点的值小于其后继节点的值，则删除当前节点，否则保留当前节点。

``` js
var removeNodes = function (head) {
  if (!head) return null;

  head.next = removeNodes(head.next);

  return (head.next && head.val < head.next.val) ? head.next : head;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

#### 2. 两次反转 👍

首先对链表进行反转（反转链表原理的可以参考[反转链表问题](./reverse-linked-list-problems.md)），然后我们就可以将问题转换为删除链表中所有值小于前驱节点的节点，最后再反转链表。

``` js
var removeNodes = function (head) {
  head = reverseList(head);

  let curNode = head;

  while (curNode.next) {
    if (curNode.val > curNode.next.val) {
      curNode.next = curNode.next.next;
    } else {
      curNode = curNode.next;
    }
  }

  return reverseList(head);
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

## 删除重复节点

### [83. 删除排序链表中的重复元素 🟢](https://leetcode.cn/problems/remove-duplicates-from-sorted-list/description/)

题目要求：在一个**有序**的单向链表中删除重复节点，返回删除后的链表。

#### 1. 递归

终止条件：链表为空或链表只有一个节点时返回该节点。

递推过程：如果当前节点的值与后继节点的值相等，则删除后继节点，否则保留当前节点。

``` js
var deleteDuplicates = function (head) {
  if (!head || !head.next) return head;

  head.next = deleteDuplicates(head.next);

  return head.val === head.next.val ? head.next : head;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

#### 2. 迭代 👍

遍历链表，对于每个节点，如果当前节点的值与后继节点的值相等，则删除后继节点；否则继续遍历节点。重复以上过程，直到链表遍历结束为止。

``` js
var deleteDuplicates = function (head) {
  if (!head) return null;

  let curNode = head;
  while (curNode.next) {
    if (curNode.val === curNode.next.val) {
      curNode.next = curNode.next.next;
    } else {
      curNode = curNode.next;
    }
  }

  return head;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [82. 删除排序链表中的重复元素 II 🟡](https://leetcode.cn/problems/remove-duplicates-from-sorted-list-ii/description/)

题目要求：在一个**有序**的单向链表中删除所有重复的节点，返回删除后的链表。

#### 迭代 👍

遍历链表，检查是否存在重复节点，如果存在重复节点，则删除所有重复节点。

删除所有重复节点的思路是：将第一个重复节点的前驱节点指向最后一个重复节点的后继节点。

``` js
var deleteDuplicates = function (head) {
  const dummy = new ListNode('dummy');
  dummy.next = head;

  let precursor = dummy;
  let curNode = dummy.next;
  while (curNode) {
    if (curNode.next && curNode.val === curNode.next.val) {
      while (curNode.next && curNode.val === curNode.next.val) {
        curNode = curNode.next;
      }
      // 跳过所有重复节点
      precursor.next = curNode.next;
    } else {
      precursor = precursor.next;
    }
    curNode = curNode.next;
  }

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [面试题 02.01. 移除重复节点 🟢](https://leetcode.cn/problems/remove-duplicate-node-lcci/description/)

题目要求：在一个**无序**的单向链表中删除重复的节点，返回删除后的链表。

#### 1. 暴力法

使用双层循环遍历链表，外层循环遍历链表，内层循环遍历链表的剩余部分，如果当前节点的值与后继节点的值相等，则删除后继节点。

``` js
var removeDuplicateNodes = function (head) {
  if (!head) return null;

  let curNode = head;
  
  while (curNode) {
    let successor = curNode;
    while (successor.next) {
      if (curNode.val === successor.next.val) {
        successor.next = successor.next.next;
      } else {
        successor = successor.next;
      }
    }
    curNode = curNode.next;
  }

  return head;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 2. 哈希表 👍

使用哈希表 `Set` 存储链表中已经出现的节点值。遍历链表，如果当前节点的值已经存在于 `Set` 中，则删除当前节点，否则将当前节点的值添加到 `Set` 中，继续遍历链表。

``` js
var removeDuplicateNodes = function (head) {
  if (!head) return null;
  
  const seen = new Set();
  seen.add(head.val);
  let curNode = head;

  while (curNode.next) {
    if (seen.has(curNode.next.val)) {
      curNode.next = curNode.next.next;
    } else {
      seen.add(curNode.next.val);
      curNode = curNode.next;
    }
  }

  return head;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### [1836. 从未排序的链表中移除重复元素 🟡 🔒](https://leetcode.cn/problems/remove-duplicates-from-an-unsorted-linked-list/description/)

题目要求：在一个**无序**的单向链表中删除所有重复的节点，返回删除后的链表。

#### 哈希表 👍

使用哈希表 `Map` 统计每个节点的值出现的次数。然后遍历链表，删除出现次数大于 `1` 的节点。

``` js
var deleteDuplicatesUnsorted = function (head) {
  const map = new Map();
  let curNode = head;
  // 统计节点值出现的数量
  while (curNode) {
    map.set(curNode.val, (map.get(curNode.val) ?? 0) + 1);
    curNode = curNode.next;
  }

  const dummy = new ListNode('dummy');
  dummy.next = head;
  curNode = dummy;

  // 删除重复节点
  while (curNode.next) {
    if (map.get(curNode.next.val) > 1) {
      curNode.next = curNode.next.next;
    } else {
      curNode = curNode.next;
    }
  }

  return dummy.next;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。
