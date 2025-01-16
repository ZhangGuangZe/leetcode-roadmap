# 合并链表问题

## [21. 合并两个有序链表 🟢 ❤](https://leetcode.cn/problems/merge-two-sorted-lists/description/)

题目要求：将两个升序列表合并为一个新的升序链表，并返回合并后的新链表。

### 1. 递归

终止条件：如果一个链表为空，则直接返回另一个链表。

递推过程：通过两个链表 `l1` 和 `l2` 当前节点的值，我们选择较小的节点添加到新链表中，并递归的处理剩余部分。如果 `l1.val < l2.val`，递归的将 `l1.next` 和 `l2` 剩余的部分合并；否则，递归的将 `l1` 和 `l2.next` 剩余的部分合并。

``` js
var mergeTwoLists = function (list1, list2) {
  if (!list1 || !list2) return list1 || list2;

  if (list1.val < list2.val) {
    list1.next = mergeTwoLists(list1.next, list2);
    return list1;
  } else {
    list2.next = mergeTwoLists(list1, list2.next);
    return list2;
  }
};
```

时间复杂度：O(m + n)，空间复杂度：O(m + n)。

### 2. 迭代 👍

我们先创建一个哨兵节点作为新链表的头结点；然后循环遍历这两个链表，比较两链表当前节点的值，将较小的节点添加到新链表中；继续遍历链表直到一个链表为空为止，最后将另一个链表的剩余部分添加到新链表中。

``` js
var mergeTwoLists = function (list1, list2) {
  const dummy = new ListNode('dummy');
  let curNode = dummy;

  while (list1 && list2) {
    if (list1.val < list2.val) {
      curNode.next = list1;
      list1 = list1.next;
    } else {
      curNode.next = list2;
      list2 = list2.next;
    }
    curNode = curNode.next;
  }
  curNode.next = list1 || list2;

  return dummy.next;
};
```

时间复杂度：O(m + n)，空间复杂度：O(1)。

## [23. 合并K个升序链表 🔴 ❤](https://leetcode.cn/problems/merge-k-sorted-lists/description/)

题目要求：将 `k` 个升序链表合并为一个升序链表，并返回合并后的新链表。

### 1. 暴力法（逐条合并）

将每条链表与结果链表逐条合并，直到合并为一条链表为止。

``` js
var mergeKLists = function (lists) {
  if (!lists.length) return null;

  let list = lists[0];
  for (let i = 1; i < lists.length; i++) {
    list = mergeTwoLists(list, lists[i]);
  }

  return list;
};

const mergeTwoLists = (l1, l2) => {
  // 上题迭代解法...
};
```

时间复杂度：O(nk)，空间复杂度：O(1)。

### 2. 分治法（两两合并）👍

将 `k` 条链表两两合并，直到合并为一条链表为止。

``` js
var mergeKLists = function (lists) {
  if (!lists.length) return null;

  return merge(lists, 0, lists.length - 1);
};

const mergeTwoLists = (l1, l2) => {
  // 上题迭代解法...
};

const merge = (arr, lo, hi) => {
  if (lo === hi) return arr[lo];

  const mid = Math.floor((lo + hi) / 2);
  const l1 = merge(arr, lo, mid);
  const l2 = merge(arr, mid + 1, hi);

  return mergeTwoLists(l1, l2);
};
```

时间复杂度：O(nlogk)，空间复杂度：O(logk)。

TODO，关于分治法的更多信息，我们将在后面的相关专题中详细介绍。

### 3. 优先队列 👍

我们使用优先队列（最小堆）来维护所有链表的头结点。依次从最小堆中取出最小的节点，并将其加到结果链表中，如果当前节点的后继节点不为空，则将后继节点添加到最小堆中。重复以上直到最小堆为空为止。最后返回结果链表。

``` js
var mergeKLists = function (lists) {
  if (!lists.length) return null;

  const minHeap = new Heap((a, b) => a.val - b.val);
  const dummy = new ListNode('dummy');
  let curNode = dummy;

  for (const list of lists) {
    if (list) minHeap.insert(list);
  }

  while (!minHeap.isEmpty()) {
    const node = minHeap.remove();
    curNode.next = node;
    curNode = curNode.next;
    if (node.next) minHeap.insert(node.next);
  }

  return dummy.next;
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