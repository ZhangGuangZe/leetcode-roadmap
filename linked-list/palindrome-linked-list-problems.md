# 回文链表问题

## [234. 回文链表 🟢 ❤](https://leetcode.cn/problems/palindrome-linked-list/description/)

题目要求：判断给定的单向链表是否是回文链表。

### 1. 数组 + 双指针

遍历链表，依次将当前节点的值放入数组中，然后使用双指针从两边向中间遍历判断是否回文。

``` js
var isPalindrome = function (head) {
  const nums = [];
  let curNode = head;
  while (curNode) {
    nums.push(curNode.val);
    curNode = curNode.next;
  }

  let l = 0;
  let r = nums.length - 1;
  while (l < r) {
    if (nums[l] !== nums[r]) return false;
    l++;
    r--;
  }

  return true;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

### 2. 快慢指针 👍

首先使用快慢指针找到链表的中间节点，然后翻转链表的后半部分，最后逐个比较前半部分和后半部分节点的值。

``` js
var isPalindrome = function (head) {
  const middleNode = getMiddleNode(head);
  let tailNode = reverseList(middleNode.next);
  let curNode = head;

  while (tailNode) {
    if (curNode.val !== tailNode.val) return false;
    curNode = curNode.next;
    tailNode = tailNode.next;
  }

  return true;
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


