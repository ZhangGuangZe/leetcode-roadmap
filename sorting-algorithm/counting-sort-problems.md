# 计数排序问题

## [1122. 数组相对排序 🟢 ❤](https://leetcode.cn/problems/relative-sort-array/description/)

题目要求：将 `arr1` 根据 `arr2` 的顺序进行排序，如果 `arr2` 中没有的元素则按升序排在最后。

### 1. map + 自定义排序

首先使用哈希表 `orderMap` 记录 `arr2` 中每个元素的位置。

然后根据如下自定义比较规则进行排序：

  * 如果两个元素都在 `arr2` 中，则按它们在 `arr2` 的相对顺序进行排序
  * 如果只有一个元素在 `arr2` 中，则将该元素排在前面
  * 如果两个元素都不在 `arr2` 中，则将它们进行升序排序。

``` js
var relativeSortArray = function (arr1, arr2) {
  const orderMap = new Map();
  arr2.forEach((num, index) => orderMap.set(num, index));

  return arr1.sort((a, b) => {
    if (orderMap.has(a) && orderMap.has(b)) {
      return orderMap.get(a) - orderMap.get(b);
    }
    if (orderMap.has(a)) {
      return -1;
    }
    if (orderMap.has(b)) {
      return 1;
    }
    return a - b;
  });
};
```

时间复杂度：O(nlogn)，空间复杂度：O(m)。

由于数组元素在 `[0, 1000] 范围内，所以我们可以考虑使用计数排序来解决此题。

### 2. 计数排序 👍

首先统计数组 `arr1` 中每个元素的出现次数；然后按照数组 `arr2` 中的顺序，将 `arr1` 中的元素按照出现次数放到结果数组中；最后我们将未在 `arr2` 中出现的元素按升序排列放入结果数组中。

``` js
var relativeSortArray = function (arr1, arr2) {
  const count = new Array(1001).fill(0);

  for (const num of arr1) {
    count[num]++;
  }

  const res = [];
  for (const num of arr2) {
    while (count[num]-- > 0) {
      res.push(num);
    }
  }

  for (let i = 0; i < count.length; i++) {
    while (count[i]-- > 0) {
      res.push(i);
    }
  }

  return res;
};
```

时间复杂度：O(n + k)，空间复杂度：O(k)。

## [274. H 指数 🟡](https://leetcode.cn/problems/h-index/description/)

题目要求：给定一个整数数组 `citations`，表示研究者的论文被引用的次数，计算研究者的 H 指数。

### 1. 排序

首先将 `citations` 按降序排序，然后从后向前遍历，找到最大的 `h` 值满足`citations[h-1] >= h` 条件，说明至少 `h` 篇论文被引用 `h` 次。最后如果没有找到这样的 `h` 值，说明所有论文都没有被引用，则返回 `0`。

``` js
var hIndex = function (citations) {
  citations.sort((a, b) => b - a);

  for (let h = citations.length; h >= 0; h--) {
    if (citations[h - 1] >= h) return h;
  }

  return 0;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(1)。

### 2. 计数排序 👍

首先创建一个长度为 `n + 1` 的数组 `count`，其中 `count[i]` 表示引用次数为 `i` 的论文篇数。

然后我们遍历数组 `citations`，将引用次数大于 `n` 的论文都当作引用次数为 `n` 的论文。然后统计每个引用次数对应的论文篇数。

最后从高引用次数向低引用次数累加，找到满足 H 指数条件的最大值。

``` js
var hIndex = function (citations) {
  const n = citations.length;
  const count = new Array(n + 1).fill(0);

  for (const c of citations) {
    if (c >= n) count[n]++;
    else count[c]++;
  }

  let total = 0;
  for (let h = n; h >= 0; h--) {
    total += count[h];
    if (total >= h) return h;
  }

  return 0;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。