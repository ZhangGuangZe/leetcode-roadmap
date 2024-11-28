# 设计类问题

## [707. 设计链表 🟡](https://leetcode.cn/problems/design-linked-list/description/)

题目要求：使用单向链表或者双向链表设计并实现一个自己的链表。

功能如下：

- `get(index)`：返回下标 index 对应的值，如果下标不存在，返回 `-1`。
- `addAtHead(val)`：在链表头部插入一个值为 `val` 的节点。
- `addAtTail(val)`：在链表尾部插入一个值为 `val` 的节点。
- `addAtIndex(index, val)`：将值为 `val` 的节点插入到下标为 `index` 的位置，如果 `index` 等于链表的长度，新节点会被插入链表的尾部。如果 `index` 大于链表长度，则该节点不会插入到链表中。
- `deleteAtIndex(index)`：删除链表下标为 `index` 的节点。

### 1. 单向链表

``` js
class Node {
  constructor(val, next = null) {
    this.val = val;
    this.next = next;
  }
}

var MyLinkedList = function () {
  this.head = null;
  this.size = 0;
};

/** 
 * @param {number} index
 * @return {number}
 */
MyLinkedList.prototype.get = function (index) {
  if (index < 0 || index >= this.size) return -1;

  let curNode = this.head;
  for (let i = 0; i < index; i++) {
    curNode = curNode.next;
  }

  return curNode.val;
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtHead = function (val) {
  this.addAtIndex(0, val);
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtTail = function (val) {
  this.addAtIndex(this.size, val);
};

/** 
 * @param {number} index 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtIndex = function (index, val) {
  if (index >= 0 && index <= this.size) {
    const newNode = new Node(val);
    if (index === 0) { // 在链表头部插入节点
      newNode.next = this.head;
      this.head = newNode;
    } else { // 在其他位置插入节点
      let curNode = this.head;
      for (let i = 0; i < index - 1; i++) {
        curNode = curNode.next;
      }
      newNode.next = curNode.next;
      curNode.next = newNode;
    }
    this.size++;
  }
};

/** 
 * @param {number} index
 * @return {void}
 */
MyLinkedList.prototype.deleteAtIndex = function (index) {
  if (index >= 0 && index < this.size) {
    if (index === 0) { // 删除头节点
      this.head = this.head.next;
    } else { // 删除其他节点
      let curNode = this.head;
      for (let i = 0; i < index - 1; i++) {
        curNode = curNode.next;
      }
      curNode.next = curNode.next.next;
    }
    this.size--;
  }
};

```

### 2. 单向链表（哨兵优化） 👍

``` js
class Node {
  constructor(val, next = null) {
    this.val = val;
    this.next = next;
  }
}

var MyLinkedList = function () {
  this.head = new Node('dummy');
  this.size = 0;
};

/** 
 * @param {number} index
 * @return {number}
 */
MyLinkedList.prototype.get = function (index) {
  if (index < 0 || index >= this.size) return -1;

  let curNode = this.head.next;
  for (let i = 0; i < index; i++) {
    curNode = curNode.next;
  }

  return curNode.val;
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtHead = function (val) {
  this.addAtIndex(0, val);
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtTail = function (val) {
  this.addAtIndex(this.size, val);
};

/** 
 * @param {number} index 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtIndex = function (index, val) {
  if (index >= 0 && index <= this.size) {
    const newNode = new Node(val);
    let curNode = this.head;
    for (let i = 0; i < index; i++) {
      curNode = curNode.next;
    }
    newNode.next = curNode.next;
    curNode.next = newNode;
    this.size++;
  }
};

/** 
 * @param {number} index
 * @return {void}
 */
MyLinkedList.prototype.deleteAtIndex = function (index) {
  if (index >= 0 && index < this.size) {
    let curNode = this.head;
    for (let i = 0; i < index; i++) {
      curNode = curNode.next;
    }
    curNode.next = curNode.next.next;
    this.size--;
  }
};
```

### 3. 双向链表

``` js
class Node {
  constructor(val, next = null, prev = null) {
    this.val = val;
    this.next = next;
    this.prev = prev;
  }
}

var MyLinkedList = function () {
  this.head = null;
  this.tail = null;
  this.size = 0;
};

/** 
 * @param {number} index
 * @return {number}
 */
MyLinkedList.prototype.get = function (index) {
  if (index < 0 || index >= this.size) return -1;

  let curNode;
  if (index < this.size / 2) {
    curNode = this.head;
    for (let i = 0; i < index; i++) {
      curNode = curNode.next;
    }
  } else {
    curNode = this.tail;
    for (let i = this.size - 1; i > index; i--) {
      curNode = curNode.prev;
    }
  }

  return curNode.val;
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtHead = function (val) {
  this.addAtIndex(0, val);
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtTail = function (val) {
  this.addAtIndex(this.size, val);
};

/** 
 * @param {number} index 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtIndex = function (index, val) {
  if (index >= 0 && index <= this.size) {
    const newNode = new Node(val);
    if (index === 0) { // 从链表头部插入节点
      if (this.size === 0) { // 空链表
        this.head = newNode;
        this.tail = newNode;
      } else {
        newNode.next = this.head;
        this.head.prev = newNode;
        this.head = newNode;
      }
    } else if (index === this.size) { // 从链表头部插入节点
      if (this.size === 0) {
        this.head = newNode;
        this.tail = newNode;
      } else {
        newNode.prev = this.tail;
        this.tail.next = newNode;
        this.tail = newNode;
      }
    } else { // 从其他位置插入节点
      let curNode;
      if (index < this.size / 2) {
        curNode = this.head;
        for (let i = 0; i < index; i++) {
          curNode = curNode.next;
        }
      } else {
        curNode = this.tail;
        for (let i = this.size - 1; i > index; i--) {
          curNode = curNode.prev;
        }
      }
      newNode.next = curNode;
      curNode.prev.next = newNode;
      newNode.prev = curNode.prev;
      curNode.prev = newNode;
    }
    this.size++;
  }
};

/** 
 * @param {number} index
 * @return {void}
 */
MyLinkedList.prototype.deleteAtIndex = function (index) {
  if (index >= 0 && index < this.size) {
    if (index === 0) { // 删除头节点
      this.head = this.head.next;
      if (this.head) {
        this.head.prev = null;
      } else {
        this.tail = null;
      }
    } else if (index === this.size - 1) { // 删除尾节点
      this.tail = this.tail.prev;
      if (this.tail) {
        this.tail.next = null;
      } else {
        this.head = null;
      }
    } else { // 删除其他位置的节点
      let curNode;
      if (index < this.size / 2) {
        curNode = this.head;
        for (let i = 0; i < index; i++) {
          curNode = curNode.next;
        }
      } else {
        curNode = this.tail;
        for (let i = this.size - 1; i > index; i--) {
          curNode = curNode.prev;
        }
      }
      curNode.prev.next = curNode.next;
      curNode.next.prev = curNode.prev;
    }
    this.size--;
  }
};
```

### 4. 双向链表（哨兵优化） 👍

``` js
class Node {
  constructor(val, next = null, prev = null) {
    this.val = val;
    this.next = next;
    this.prev = prev;
  }
}

var MyLinkedList = function () {
  this.head = new Node('dummyHead');
  this.tail = new Node('dummyTail');
  this.head.next = this.tail;
  this.tail.prev = this.head;
  this.size = 0;
};

/** 
 * @param {number} index
 * @return {number}
 */
MyLinkedList.prototype.get = function (index) {
  if (index < 0 || index >= this.size) return -1;

  let curNode;
  if (index < this.size / 2) {
    curNode = this.head.next;
    for (let i = 0; i < index; i++) {
      curNode = curNode.next;
    }
  } else {
    curNode = this.tail.prev;
    for (let i = this.size - 1; i > index; i--) {
      curNode = curNode.prev;
    }
  }

  return curNode.val;
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtHead = function (val) {
  this.addAtIndex(0, val);
};

/** 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtTail = function (val) {
  this.addAtIndex(this.size, val);
};

/** 
 * @param {number} index 
 * @param {number} val
 * @return {void}
 */
MyLinkedList.prototype.addAtIndex = function (index, val) {
  if (index >= 0 && index <= this.size) {
    const newNode = new Node(val);
    let precursor;
    let successor;
    if (index < this.size / 2) {
      precursor = this.head;
      for (let i = 0; i < index; i++) {
        precursor = precursor.next;
      }
      successor = precursor.next;
    } else {
      successor = this.tail;
      for (let i = this.size; i > index; i--) {
        successor = successor.prev;
      }
      precursor = successor.prev;
    }
    newNode.next = successor;
    newNode.prev = precursor;
    successor.prev = newNode;
    precursor.next = newNode;
    this.size++;
  }
};

/** 
 * @param {number} index
 * @return {void}
 */
MyLinkedList.prototype.deleteAtIndex = function (index) {
  if (index >= 0 && index < this.size) {
    let precursor;
    let successor;
    if (index < this.size / 2) {
      precursor = this.head;
      for (let i = 0; i < index; i++) {
        precursor = precursor.next;
      }
      successor = precursor.next.next;
    } else {
      successor = this.tail;
      for (let i = this.size - 1; i > index; i--) {
        successor = successor.prev;
      }
      precursor = successor.prev.prev;
    }
    precursor.next = successor;
    successor.prev = precursor;
    this.size--;
  }
};
```

## [146. LRU 缓存 🟡 ❤](https://leetcode.cn/problems/lru-cache/description/)

题目要求：设计并实现一个满足 `LRU (最近最少使用) 缓存` 功能的数据结构。

功能如下：

- `get(key)`：如果关键字 `key` 存在于缓存中，则获取返回 `key` 对应的值，否则返回 `-1`。
- `put(key, value)`：如果关键字 `key` 已经存在，则修改 `key` 对应的值 `value`；如果 `key` 不存在，则插入该键值对。当缓存容量达到上限时，则应该删除最久未使用的键值对。

以上两个方法的时间复杂度必须为 `O(1)`。

### 1. 哈希表

利用哈希表按照插入顺序来存储键值对（先插入的键值对在哈希表头部，后插入的键值对在哈希表尾部）的特性，模拟缓存的存储和更新过程。

`get` 操作：

- 如果 `key` 存在，需要先将键值对从哈希表中删除，然后再重新插入，保证该数据是最近使用的。
- 如果 `key` 不存在，则返回 `-1`。

`put` 操作：

- 如果 `key` 存在，将其键值对删除并重新插入。
- 如果容量满了，则删除哈希表中最久未使用的键值对，然后再插入新的键值对。

``` js
/**
 * @param {number} capacity
 */
var LRUCache = function (capacity) {
  this.capacity = capacity;
  this.cache = new Map();
};

/** 
 * @param {number} key
 * @return {number}
 */
LRUCache.prototype.get = function (key) {
  if (!this.cache.has(key)) return -1;

  const val = this.cache.get(key);
  // 将键值对移动到哈希表尾部
  this.cache.delete(key);
  this.cache.set(key, val);
  return val;
};

/** 
 * @param {number} key 
 * @param {number} value
 * @return {void}
 */
LRUCache.prototype.put = function (key, value) {
  if (this.cache.has(key)) {
    this.cache.delete(key);
  } else if (this.cache.size >= this.capacity) {
    this.cache.delete(this.cache.keys().next().value); // 删除最久未使用的键值对
  }
  this.cache.set(key, value);
};
```

### 2. 哈希表 + 双向链表 👍

结合链表的有序性和哈希表的快速查找。靠近双向链表头部的数据为最近使用的，而靠近双向链表尾部的数据为最近未使用的。

`get` 操作：

- 如果 `key` 存在，将键值对移动到双向链表的头部（删除当前节点，然后在链表头部重新插入）。
- 如果 `key` 不存在，则返回 `-1`。

`put` 操作：

- 如果 `key` 存在，将键值对移动到双向链表头部。
- 如果 `key` 不存在，将键值对插入到双向链表头部。如果容量满了，则删除最近最少使用数据，也就是双向链表尾节点，并删除哈希表中的键值。

``` js
var LRUCache = function (capacity) {
  this.capacity = capacity;
  this.cache = new Map();
  this.list = new DoublyLinkedList();
};

/** 
 * @param {number} key
 * @return {number}
 */
LRUCache.prototype.get = function (key) {
  if (!this.cache.has(key)) return -1;

  const node = this.cache.get(key);
  this.list.remove(node);
  this.list.prepend(node);

  return node.val.value;
};

/** 
 * @param {number} key 
 * @param {number} value
 * @return {void}
 */
LRUCache.prototype.put = function (key, value) {
  if (this.cache.has(key)) {
    const node = this.cache.get(key);
    node.val.value = value;
    // 将键值对移动到双向链表的头部
    this.list.remove(node);
    this.list.prepend(node);
  } else {
    const node = new Node({ key, value });
    this.cache.set(key, node);
    this.list.prepend(node); // 将键值对插入到双向链表的头部
    if (this.cache.size > this.capacity) {
      const removedNode = this.list.removeTail();
      this.cache.delete(removedNode.val.key); // 删除最近最少使用的键值对
    }
  }
};

class Node {
  constructor(val, prev = null, next = null) {
    this.val = val;
    this.prev = prev;
    this.next = next;
  }
}

class DoublyLinkedList {
  constructor() {
    this.size = 0;
    this.head = new Node('dummyHead');
    this.tail = new Node('dummyTail');
    this.head.next = this.tail;
    this.tail.prev = this.head;
  }

  prepend(node) {
    node.next = this.head.next;
    this.head.next.prev = node;
    node.prev = this.head;
    this.head.next = node;
    this.size++;
  }

  removeTail() {
    return this.remove(this.tail.prev);
  }

  remove(node) {
    node.prev.next = node.next;
    node.next.prev = node.prev;
    this.size--;
    return node;
  }
}
```
