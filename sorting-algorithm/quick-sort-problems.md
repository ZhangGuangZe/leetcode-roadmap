# 快速排序算法

## [75. 颜色分类（荷兰国旗问题）🟡 ❤](https://leetcode.cn/problems/sort-colors/description/)

题目要求：**原地** 将数组排序，使得相同颜色的元素相邻，并按红色（`0`）、白色（`1`）、蓝色（`2`）顺序排列。

### 1. 计数

创建一个计数数组 `count`，用于记录数组中每个颜色的元素个数。然后遍历数组，根据每中颜色的个数，将元素重新填充到原数组中。

``` js
var sortColors = function (nums) {
  const count = [0, 0, 0];

  for (let i = 0; i < nums.length; i++) {
    count[nums[i]]++;
  }

  let j = 0;
  for (let i = 0; i < count.length; i++) {
    while (count[i]-- > 0) {
      nums[j++] = i;
    }
  }
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 2. 两次遍历

对数组进行两次遍历，第一次遍历将数组中的所有 `0` 交换到数组的开头，第二次遍历将数组中的所有 `1` 交换到所有 `0` 的后面。此时，所有的 `2` 都位于数组的末尾，排序完毕。

``` js
var sortColors = function (nums) {
  let j = 0;

  for (let i = 0; i < nums.length; i++) {
    if (nums[i] === 0) {
      [nums[i], nums[j]] = [nums[j], nums[i]];
      j++;
    }
  }

  for (let i = j; i < nums.length; i++) {
    if (nums[i] === 1) {
      [nums[i], nums[j]] = [nums[j], nums[i]];
      j++;
    }
  }
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 3. 双指针

使用指针 `p0` 来交换 `0`，`p2` 来交换 `2`。初始化时，`p0` 指向数组的开头，`p2` 指向数组的末尾。

遍历过程中，我们将所有 `0` 交换到数组开头，将所有 `2` 交换到数组末尾。

``` js
var sortColors = function (nums) {
  let p0 = 0;
  let p2 = nums.length - 1;

  for (let i = 0; i <= p2; i++) {
    while (nums[i] === 2 && i < p2) {
      [nums[i], nums[p2]] = [nums[p2], nums[i]];
      p2--;
    }

    if (nums[i] === 0) {
      [nums[i], nums[p0]] = [nums[p0], nums[i]];
      p0++;
    }
  }
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### 4. 三向切分 👍

我们定义三个指针 `lt`、`i`、`gt`，其中 `lt` 指向 `0` 区域的右边界，`gt` 指向 `2` 区域的左边界。`i` 指向当前待处理的元素。

遍历过程中：

- 如果当前元素等于 `0`，则将 `nums[i]` 和 `nums[lt]` 交换，同时将 `lt` 和 `i` 向右移动一位；
- 如果当前元素等于 `1`，则跳过当前元素，继续遍历下一个元素；
- 如果当前元素等于 `2`，则将 `nums[i]` 和 `nums[gt]` 交换，同时将 `gt` 向左移动一位；

遍历结束后，数组被分为三个不同的区域：`nums[0, lt - 1]` 是 `0`，`nums[lt, i - 1]` 是 `1`，`nums[gt + 1, n - 1]` 是 `2`。

这样，只需一次遍历即可将这些颜色重新排序。

``` js
var sortColors = function (nums) {
  let lt = 0;
  let i = 0;
  let gt = nums.length - 1;

  while (i <= gt) {
    if (nums[i] === 0) {
      [nums[lt], nums[i]] = [nums[i], nums[lt]];
      lt++;
      i++;
    } else if (nums[i] === 1) {
      i++;
    } else { // nums[i] === 2
      [nums[i], nums[gt]] = [nums[gt], nums[i]];
      gt--;
    }
  }
};
```

时间复杂度：O(n)，空间复杂度：O(1)。
