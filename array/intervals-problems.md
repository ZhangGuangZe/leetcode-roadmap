# 区间问题

## [252. 会议室 🟢 🔒](https://leetcode.cn/problems/meeting-rooms/description/)

题目要求：给定一组会议时间区间，判断这些区间是否重叠。如果没有重叠，则可以参加所有会议。

### 1. 暴力法

遍历会议区间，检查每个区间是否有重叠，如果当前区间的开始时间小于另一个区间的结束时间，并且当前区间的结束时间大于另一个区间的开始时间，则说明区间重叠。

``` js
var canAttendMeetings = function (intervals) {
  const n = intervals.length;

  for (let i = 0; i < n; i++) {
    for (let j = i + 1; j < n; j++) {
      if (intervals[i][0] < intervals[j][1] && intervals[i][1] > intervals[j][0]) return false;
    }
  }

  return true;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 排序 👍

首先根据区间的开始时间区间列表排序，然后遍历排序好的区间，检查当前区间与前一个区间是否重叠。如果当前区间的开始时间小于前一个会议的结束时间，则说明区间重叠。

``` js
var canAttendMeetings = function (intervals) {
  intervals.sort((a, b) => a[0] - b[0]);

  for (let i = 1; i < intervals.length; i++) {
    if (intervals[i][0] < intervals[i - 1][1]) return false;
  }

  return true;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

## [253. 会议室 II 🟡 ❤ 🔒](https://leetcode.cn/problems/meeting-rooms-ii/description/)

题目要求：给定一组会议时间区间，计算在同一时间有多少个会议室被使用。即包含多少个重叠的区间。

### 1. 暴力法

对每个会议，计算与其重叠的会议数量，并记录最大值。

``` js
var minMeetingRooms = function (intervals) {
  const n = intervals.length;
  let res = 0;

  for (let i = 0; i < n; i++) {
    let count = 0;
    
    for (let j = 0; j < n; j++) {
      if (i !== j && intervals[i][0] < intervals[j][1] && intervals[i][1] > intervals[j][0]) count++;
    }

    res = Math.max(res, count);
  }

  return res;
}
```

时间复杂度：O(n²)，空间复杂度：O(1)。

### 2. 贪心算法 👍

分别使用两个数组存储每个会议的开始时间和结束时间，然后对这两个数组进行排序。通过遍历开始时间数组，检查每个会议的开始时间是否小于当前会议的最早结束时间，如果小于，则说明有重叠，需要增加会议室数量。

``` js
var minMeetingRooms = function (intervals) {
  const starts = [];
  const ends = [];

  for (const [start, end] of intervals) {
    starts.push(start);
    ends.push(end);
  }

  starts.sort((a, b) => a - b);
  ends.sort((a, b) => a - b);

  let res = 0;
  let j = 0;

  for (let i = 0; i < starts.length; i++) {
    if (starts[i] < ends[j]) res++;
    else j++;
  }

  return res;
}
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

### 3. 差分数组 👍

差分数组是一种通过存储相邻元素的变化量来高效处理频繁的区间更新操作的数据结构。

创建一个差分数组，记录每个时间点的会议数量。遍历会议时间区间，将每个会议的开始时间和结束时间分别加 `1` 和减 `1`，表示会议开始和结束。最后遍历差分数组，找到每个时间点的会议数量，并记录最大值。

``` js
var minMeetingRooms = function (intervals) {
  const diff = new Array(1000010).fill(0);

  for (const [start, end] of intervals) {
    diff[start]++;
    diff[end]--;
  }

  let currRooms = 0;
  let res = 0;

  for (let i = 0; i < diff.length; i++) {
    currRooms += diff[i];
    res = Math.max(res, currRooms);
  }

  return res;
}
```

时间复杂度：O(n)，空间复杂度：O(n)。

## [56. 合并区间 🟡](https://leetcode.cn/problems/merge-intervals/description/)

题目要求：在若干个区间集合中，合并所有重叠的区间并返回合并后的新区间。

### 模拟

首先根据每个区间的开始区间进行排序，然后遍历区间集合，如果当前区间的开始区间小于等于上一个区间的结束区间，说明重叠需要合并区间；否则将当前区间加入到结果集中。

``` js
var merge = function (intervals) {
  intervals.sort((a, b) => a[0] - b[0]);

  const n = intervals.length;
  const res = [intervals[0]];

  for (let i = 1; i < n; i++) {
    const prev = res.at(-1); // res[res.length - 1]
    const curr = intervals[i];

    if (prev[1] < curr[0]) {
      res.push(curr);
    } else {
      prev[1] = Math.max(prev[1], curr[1]);
    }
  }

  return res;
};
```

时间复杂度：O(nlogn)，空间复杂度：O(n)。

## [57. 插入区间 🟡](https://leetcode.cn/problems/insert-interval/description/)

题目要求：将一个新的区间插入到一个无重叠且有序的区间列表中，并保持区间的无重叠和有序性。

### 模拟

在使用模拟思路解题之前，我们可以先将新区间插入到区间列表中，然后使用合并区间的思路处理。这里就不在赘述，下面介绍通过模拟的解题思路。

遍历区间列表，找到插入区间的位置，将新区间插入到合适位置并处理重叠区间。

``` js
var insert = function (intervals, newInterval) {
  if (!intervals.length) return [newInterval];

  const res = [];
  const n = intervals.length;
  let i = 0;

  // 将所有在 newInterval 前的区间添加到结果中
  while (i < n && intervals[i][1] < newInterval[0]) res.push(intervals[i++]);

  // 合并所有重叠区间
  while (i < n && intervals[i][0] <= newInterval[1]) {
    newInterval = [
      Math.min(intervals[i][0], newInterval[0]),
      Math.max(intervals[i][1], newInterval[1])
    ];
    i++;
  }
  res.push(newInterval);

  // 将所有在 newInterval 后的区间添加到结果中
  while (i < n) res.push(intervals[i++]);

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## [986. 区间列表的交集 🟡 ❤](https://leetcode.cn/problems/interval-list-intersections/description/)

题目要求：在**有序的**两个区间列表中，找出并返回它们所有相交（重叠）的区间。

### 1. 暴力法

两层循环遍历两个区间列表的所有区间。通过比较两个区间的最大开始区间和最小结束区间，如果开始区间小于等于结束区间，则表示有交集，将交集区间添加到结果中。

``` js
var intervalIntersection = function (firstList, secondList) {
  const res = [];

  for (const first of firstList) {
    for (const second of secondList) {
      const [firstStart, firstEnd] = first;
      const [secondStart, secondEnd] = second;
      const start = Math.max(firstStart, secondStart);
      const end = Math.min(firstEnd, secondEnd);

      if (start <= end) res.push([start, end]);
    }
  }

  return res;
};
```

时间复杂度：O(m * n)，空间复杂度：O(1)。

### 2. 双指针 👍

由于区间列表是有序的，我们可以使用双指针的方法来遍历两个区间列表，避免一些不必要的计算。

维护两个指针，分别遍历两个区间列表，逐一比较并找出重叠的部分。通过比较两个区间的最大开始区间和最小结束区间，如果开始区间小于等于结束区间，则表示有交集，将交集区间添加到结果中。如果 `firstList` 当前区间的结束区间小于 `secondList` 当前区间的结束区间，则移动 `i` 指针，否则移动 `j` 指针。

``` js
var intervalIntersection = function (firstList, secondList) {
  const res = [];
  let i = 0;
  let j = 0;

  while (i < firstList.length && j < secondList.length) {
    const [firstStart, firstEnd] = firstList[i];
    const [secondStart, secondEnd] = secondList[j];

    const start = Math.max(firstStart, secondStart);
    const end = Math.min(firstEnd, secondEnd);
    if (start <= end) res.push([start, end]);

    firstEnd < secondEnd ? i++ : j++;
  }

  return res;
};
```

时间复杂度：O(m + n)，空间复杂度：O(1)。

## [228. 汇总区间 🟢](https://leetcode.cn/problems/summary-ranges/description/)

题目要求：将一个无重复元素且**有序的**的数组汇总成连续数字的区间表示。

### 双指针

使用两个指针分别表示当前区间的起始位置和结束位置。从头到尾遍历数组，检查当前数字与下一个数字是否连续。如果不连续或者 `end` 指针到达数组边界，则将当前区间添加到结果中，并更新 `start` 指针作为下一个区间的起始位置。否则继续向后移动 `end` 指针，直到遍历完数组元素为止。

``` js
var summaryRanges = function (nums) {
  const res = [];
  const n = nums.length; 
  let start = 0;

  for (let end = 0; end < n; end++) {
    if (end === n - 1 || nums[end] + 1 !== nums[end + 1]) {
      if (start === end) {
        res.push(`${nums[start]}`);
      } else {
        res.push(`${nums[start]}->${nums[end]}`);
      }
      start = end + 1;
    }
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。