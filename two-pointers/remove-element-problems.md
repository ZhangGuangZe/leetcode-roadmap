# 移除元素问题

## 移除重复元素

### [26. 删除有序数组中的重复项 🟢](https://leetcode.cn/problems/remove-duplicates-from-sorted-array/description/)

题目要求：在一个**升序排列**的数组中**原地**（不能使用额外空间）且**稳定**（保持元素的先后顺序）的删除重复项，返回删除后数组的新长度。

#### 1. 暴力法

遍历数组，遇到重复元素时将后续所有元素向前移动一位，然后缩减数组长度。

``` js
var removeDuplicates = function (nums) {
  let n = nums.length;

  for (let i = 1; i < n; i++) {
    if (nums[i - 1] === nums[i]) {
      for (let j = i + 1; j < n; j++) {
        nums[j - 1] = nums[j];
      }
      n--;
      i--;
    }
  }

  return n;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 2. 双指针 👍

由于数组是升序排列的，所以数组中重复元素的位置是连续的，我们可以使用双指针的方式来删除重复元素。

定义两个指针，`i` 指针用于记录下一个不重复元素的位置，`j` 指针用于遍历数组元素。因为第一个元素是不重复的，不需要处理，所以 `i` 指针从 `1` 开始记录后续的不重复元素。

遍历数组，对于每个元素，比较当前元素与当前记录的上一个不重复元素是否相同，如果相同则跳过当前元素；否则将当前元素放到下一个重复元素的位置，并更新下一个不重复元素的位置。重复此过程，直到遍历结束为止。

``` js
var removeDuplicates = function (nums) {
  let i = 1;
  let j = 1;

  while (j < nums.length) {
    if (nums[i - 1] !== nums[j]) {
      nums[i++] = nums[j];
    }
    j++;
  }

  return i;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

### [80. 删除有序数组中的重复项 II 🟡](https://leetcode.cn/problems/remove-duplicates-from-sorted-array-ii/description/)

题目要求：在一个**升序排列**的数组中**原地**删除重复项，使得每个元素**最多出现两次**。返回删除后数组的新长度。

#### 双指针 👍

定义两个指针，`i` 用于记录下一个重复两次元素的位置， `j` 指针用于遍历数组元素。因为每个元素最多可以出现两次，无需进行任何处理，所以将 `i` 指针初始化为 `2`。

从第三个元素遍历数组，如果当前元素与当前记录的前两个元素不同，则将当前元素放到下一个重复两次元素的位置，并更新下一个重复两次元素的位置。重复此过程，直到遍历结束为止。

``` js
var removeDuplicates = function (nums) {
  const n = nums.length;
  if (n < 3) return n;

  let i = 2;
  let j = 2;

  while (j < n) {
    if (nums[i - 2] !== nums[j]) {
      nums[i++] = nums[j];
    }
    j++;
  }

  return i;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

## 移除指定元素

### [27. 移除元素 🟢](https://leetcode.cn/problems/remove-element/description/)

题目要求：在一个数组中**原地**移除指定元素 `val`，返回移除后数组的新长度。

#### 1. 暴力法

遍历数组，遇到指定元素时将后面的所有元素向前移动一位，然后缩减数组长度。

``` js
var removeElement = function (nums, val) {
  let n = nums.length;

  for (let i = 0; i < n; i++) {
    if (nums[i] === val) {
      for (let j = i + 1; j < n; j++) {
        nums[j - 1] = nums[j];
      }
      n--;
      i--;
    }
  }

  return n;
};
```

时间复杂度：O(n²)，空间复杂度：O(1)。

#### 2. 双指针（原地覆盖） 👍

定义两个指针，`i` 指针用于记录非指定元素的位置，`j` 指针用于跟踪数组元素。遍历数组，如果当前元素等于 `val`，则跳过当前元素；否则将不等于 `val` 的元素向前移动以覆盖与 `val` 相同的元素。

``` js
var removeElement = function (nums, val) {
  let i = 0;

  for (let j = 0; j < nums.length; j++) {
    if (nums[j] !== val) {
      nums[i++] = nums[j];
    }
  }

  return i;
};
```

该方法**适用于需要保持元素相对顺序且移除元素较多的场景**。

时间复杂度：O(n)，空间复杂度：O(1)。

#### 3. 双指针（原地交换） 👍

定义 `l` 和 `r` 两个指针，分别指向数组的开头和结尾。遍历数组，如果 `l` 指针对应元素等于 `val`，将两指针对应元素交换，然后将 `r` 指针左移一位。否则将 `l` 指针右移一位继续寻找与 `val` 相同的元素。重复以上操作，直到遍历完所有元素为止。

不过由于我们并不关心 `r` 指针后面的元素，所以只需使用 `r` 指针对应元素替换与 `val` 相同的元素即可，从而减少不必要的赋值操作。

``` js
var removeElement = function (nums, val) {
  let l = 0;
  let r = nums.length - 1;

  while (l <= r) {
    if (nums[l] === val) {
      nums[l] = nums[r--];
    } else {
      l++;
    }
  }

  return l;
};
```

该方法**适用于不需要保持元素相对顺序且移除元素较少的场景**。

时间复杂度：O(n)，空间复杂度：O(1)。

### [283. 移动零 🟢](https://leetcode.cn/problems/move-zeroes/description/)

题目要求：在一个数组中将所有 `0` **原地**移动到数组的末尾，同时保持非零元素的相对顺序不变。

#### 1. 暴力法（两次遍历）

遍历数组，先将所有非零的元素依次移动到数组前面，然后将剩余位置全部置为 `0`。

``` js
var moveZeroes = function (nums) {
  let i = 0;

  for (let j = 0; j < nums.length; j++) {
    if (nums[j] !== 0) {
      nums[i++] = nums[j];
    }
  }

  while (i < nums.length) nums[i++] = 0;
};
```

时间复杂度：O(n)，空间复杂度：O(1)。

#### 2. 双指针（原地交换） 👍

使用 `i` 指针跟踪非零元素的位置,  `j` 指针遍历数组元素。通过交换非零元素和 `0` 元素的位置，将所有 `0` 移动到数组末尾。

``` js
var moveZeroes = function (nums) {
  let i = 0;
  let j = 0;

  while (j < nums.length) {
    if (nums[j] !== 0) {
      [nums[i++], nums[j]] = [nums[j], nums[i]];
    }
    j++;
  }
};
```

时间复杂度：O(n)，空间复杂度：O(1)。
