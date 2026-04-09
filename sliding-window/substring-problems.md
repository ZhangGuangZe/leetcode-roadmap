# 子串问题

## 异位子串

异位子串的定义是字符串中字符的组合顺序可以不同，但字符的种类和个数必须相同。

### [567. 字符串的排列 🟡 ❤](https://leetcode.cn/problems/permutation-in-string/description/)

题目要求：给定两个**仅包含小写字母**的字符串 `s1` 和 `s2`。判断字符串 `s2` 是否包含 `s1` 的排列之一，或者说 `s1` 是否是 `s2` 的异位子串。

#### 1. 排序比较（超时）

先对 `s1` 进行排序，然后遍历 `s2`，依次对长度为 `m` 的子串进行排序，判断排序后的子串与排序后的 `s1` 是否相同。

``` js
var checkInclusion = function (s1, s2) {
  const m = s1.length;
  const n = s2.length;
  if (m > n) return false;

  const s1Sorted = s1.split('').sort().join('');

  for (let i = 0; i <= n - m; i++) {
    const s2SubSorted = s2.slice(i, i + m).split('').sort().join('');
    if (s1Sorted === s2SubSorted) return true;
  }

  return false;
};
```

时间复杂度：O(n * mlogm)，空间复杂度：O(m)。

#### 2. 滑动窗口 + 数组计数 👍

维护一个固定大小的滑动窗口，每次窗口向右滑动一位，判断当前窗口与 `s1` 中每个字符出现的次数是否相同。

由于两个字符串中仅包含小写字母，所以分别使用两个数组记录 `s1` 和 `s2` 当前窗口内每个字符出现的次数。遍历 `s2`，判断当前窗口与 `s1` 中每个字符出现次数是否相同。如果相同，说明 `s1` 是 `s2` 的异位子串，返回 `true`；否则将窗口向右滑动一位，继续进行比较，直到找到异位子串或者滑动到 `s2` 的末尾为止。

``` js
var checkInclusion = function (s1, s2) {
  const m = s1.length;
  const n = s2.length;
  if (m > n) return false;

  const s1Count = new Array(26).fill(0);
  const s2Count = new Array(26).fill(0);
  // 统计 s1 和 s2 当前窗口字符出现的次数
  for (let i = 0; i < m; i++) {
    s1Count[s1.charCodeAt(i) - 'a'.charCodeAt()]++;
    s2Count[s2.charCodeAt(i) - 'a'.charCodeAt()]++;
  }

  for (let i = 0; i < n - m; i++) {
    if (isEqual(s1Count, s2Count)) return true;

    // 移动窗口左边界
    s2Count[s2.charCodeAt(i) - 'a'.charCodeAt()]--;
    // 移动窗口右边界
    s2Count[s2.charCodeAt(i + m) - 'a'.charCodeAt()]++;
  }

  // 判断最后一个窗口与 s1 中的字符出现的次数是否相等
  return isEqual(s1Count, s2Count);
};

const isEqual = (arr1, arr2) => {
  for (let i = 0; i < 26; i++) {
    if (arr1[i] !== arr2[i]) return false;
  }
  return true;
};
```

时间复杂度：O(m + n)，时间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

#### 3. 滑动窗口 + 哈希表计数 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，窗口内只能包含 `s1` 中的字符。使用一个哈希表来记录 `s1` 中每个字符出现的次数。

遍历 `s2`，每次向右移动右边界扩大窗口，将当前字符添加到窗口中，如果当前窗口的字符与 `s1` 某个排列不匹配，则向右移动左边界收缩窗口。如果当前窗口的长度等于 `m`，说明当成窗口中的字符串是 `s1` 的异位子串，则返回 `true`。

``` js
var checkInclusion = function (s1, s2) {
  const m = s1.length;
  const n = s2.length;
  if (m > n) return false;

  const map = new Map();
  for (const c of s1) {
    map.set(c, (map.get(c) ?? 0) + 1);
  }

  let l = 0;
  let r = 0;
  while (r < n) {
    // 扩大窗口
    map.set(s2[r], (map.get(s2[r]) ?? 0) - 1);

    // 当前窗口的字符与 s1 某个排列不匹配，向右移动窗口左边界收缩窗口
    while (map.get(s2[r]) < 0) {
      map.set(s2[l], map.get(s2[l]) + 1);
      l++;
    }

    // 找到异位子串
    if (r - l + 1 === m) return true;

    r++;
  }

  return false;
};
```

时间复杂度：O(m + n)，时间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

**使用数组计数访问效率更高，而使用哈希表计数的方法更加通用，可以处理更多字符。**

### [438. 找到字符串中所有字母异位词 🟡 ❤](https://leetcode.cn/problems/find-all-anagrams-in-a-string/description/)

题目要求：给定两个**仅包含小写字母**的字符串 `s` 和 `p`。找出 `s` 中所有 `p` 的异位子串，返回这些子串的起始索引。

#### 1. 排序比较（超时）

与上一题的思路相同，只不过这里要求返回的是所有符合条件的子串其实索引。

``` js
var findAnagrams = function (s, p) {
  const m = s.length;
  const n = p.length;
  if (m < p) return [];

  const res = [];
  const pSorted = p.split('').sort().join('');

  for (let i = 0; i <= m - n; i++) {
    const sSubSorted = s.slice(i, i + n).split('').sort().join('');
    if (sSubSorted === pSorted) res.push(i);
  }

  return res;
};
```

时间复杂度：O(n * mlogm)，空间复杂度：O(m)。

#### 2. 滑动窗口 + 数组计数 👍

思路与上一题相同，这里不在赘述。

``` js
var findAnagrams = function (s, p) {
  const m = s.length;
  const n = p.length;
  if (m < p) return [];

  const sCount = new Array(26).fill(0);
  const pCount = new Array(26).fill(0);
  const res = [];

  for (let i = 0; i < n; i++) {
    sCount[s.charCodeAt(i) - 'a'.charCodeAt()]++;
    pCount[p.charCodeAt(i) - 'a'.charCodeAt()]++;
  }

  for (let i = 0; i < m - n; i++) {
    if (isEqual(sCount, pCount)) res.push(i);

    // 移动窗口左边界
    sCount[s.charCodeAt(i) - 'a'.charCodeAt()]--;
    // 移动窗口右边界
    sCount[s.charCodeAt(i + n) - 'a'.charCodeAt()]++;
  }
  
  // 判断最后一个窗口与 p 中的字符出现的次数是否相等
  if (isEqual(sCount, pCount)) res.push(m - n);

  return res;
};

const isEqual = (arr1, arr2) => {
  for (let i = 0; i < 26; i++) {
    if (arr1[i] !== arr2[i]) return false;
  }
  return true;
};
```

时间复杂度：O(m + n)，时间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

#### 3. 滑动窗口 + 哈希表计数 👍

思路与上一题相同，这里不在赘述。

``` js
var findAnagrams = function (s, p) {
  const m = s.length;
  const n = p.length;
  if (m < p) return [];

  const map = new Map();
  for (const c of p) {
    map.set(c, (map.get(c) ?? 0) + 1);
  }

  let l = 0;
  let r = 0;
  const res = [];

  while (r < m) {
    // 扩大窗口
    map.set(s[r], (map.get(s[r]) ?? 0) - 1);

    // 当前窗口的字符与 p 某个排列不匹配，向右移动窗口左边界收缩窗口
    while (map.get(s[r]) < 0) {
      map.set(s[l], map.get(s[l]) + 1);
      l++;
    }

    // 找到异位子串
    if (r - l + 1 === n) res.push(l);

    r++;
  }

  return res;
};
```

时间复杂度：O(m + n)，时间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

## 最短子串

### [76. 最小覆盖子串 🔴 ❤](https://leetcode.cn/problems/minimum-window-substring/description/)

题目要求：给定两个**仅包含英文字母**的字符串 `s` 和 `t`。找出 `s` 中包含 `t` 中所有字符的最小子串，如果不存在则返回空字符串。

#### 1. 滑动窗口 + 数组计数 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界。由于两个字符串中仅包含英文字母，所以使用两个数组分别记录 `s` 和 `t` 当前窗口中每个字符出现的次数。通过不断调整窗口大小来寻找最小覆盖子串。

定义变量 `count` 统计窗口内包含多少个 `t` 中的字符，变量 `minLeft` 表示最小覆盖子串的起始索引，变量 `minLen` 表示最小覆盖子串的长度。

遍历 `s`，每次向右移动右边界扩大窗口，将当前字符添加到窗口中。如果当前字符在 `t` 中出现，则将 `count` 加一。如果窗口内包含 `t` 中的所有字符，需要向右移动左边界收缩窗口，并尝试找出最小覆盖子串。

``` js
var minWindow = function (s, t) {
  const m = s.length;
  const n = t.length;
  if (m < n) return '';
  
  let l = 0;
  let r = 0;
  let count = 0;
  let minLeft = 0;
  let minLen = Infinity;

  const sCount = new Array(128).fill(0);
  const tCount = new Array(128).fill(0);
  for (let i = 0; i < n; i++) {
    tCount[t.charCodeAt(i)]++;
  }

  while (r < m) {
    // 扩大窗口
    sCount[s.charCodeAt(r)]++;
    // 当前字符在 t 中出现
    if (tCount[s.charCodeAt(r)] >= sCount[s.charCodeAt(r)]) count++;

    // 满足条件，尝试收缩窗口找出最小覆盖子串
    while (count === n) {
      if (r - l + 1 < minLen) {
        minLen = r - l + 1;
        minLeft = l;
      }

      if (tCount[s.charCodeAt(l)] >= sCount[s.charCodeAt(l)]) count--;
      sCount[s.charCodeAt(l)]--;
      l++;
    }

    r++;
  }

  return minLen === Infinity ? '' : s.slice(minLeft, minLeft + minLen);
};

```

时间复杂度：O(m + n)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

#### 2. 滑动窗口 + 哈希表计数 👍

与上一题解思路相同，只不过这里只使用一个哈希表记录 `t` 中每个字符出现的次数。遍历 `s`，每次向右移动右边界扩大窗口，将当前字符添加到窗口中。如果当前字符在 `t` 中出现，则将 `count` 加一。如果窗口内包含 `t` 中的所有字符，需要向右移动左边界收缩窗口，并尝试找出最小覆盖子串。

``` js
var minWindow = function (s, t) {
  const m = s.length;
  const n = t.length;
  if (m < n) return '';
  
  let l = 0;
  let r = 0;
  let count = 0;
  let minLeft = 0;
  let minLen = Infinity;

  const map = new Map();
  for (const c of t) {
    map.set(c, (map.get(c) ?? 0) + 1);
  }

  while (r < m) {
    // 扩大窗口
    map.set(s[r], (map.get(s[r]) ?? 0) - 1);
    // 当前字符在 t 中出现
    if (map.get(s[r]) >= 0) count++;

    // 满足条件，尝试收缩窗口找出最小覆盖子串
    while (count === n) {
      if (r - l + 1 < minLen) {
        minLen = r - l + 1;
        minLeft = l;
      }

      map.set(s[l], map.get(s[l]) + 1);
      if (map.get(s[l]) > 0) count--;
      l++;
    }

    r++;
  }

  return minLen === Infinity ? '' : s.slice(minLeft, minLeft + minLen);
};
```

时间复杂度：O(m + n)，空间复杂度：O(m)。

## 最长子串

### [159. 至多包含两个不同字符的最长子串 🟡 🔒](https://leetcode.cn/problems/longest-substring-with-at-most-two-distinct-characters/description/)

题目要求：在一个**仅包含英文字母**的字符串 `s` 中找出最多包含两个不同字符的最长子串的长度。

#### 1. 暴力法

双层循环枚举所有子串，统计每个子串中不同字符的数量，判断子串是否至少包含两个不同字符，记录最多包含两个不同字符的最长子串的长度。

``` js
var lengthOfLongestSubstringTwoDistinct = function (s) {
  const n = s.length;
  let res = 0;

  for (let i = 0; i < n; i++) {
    const seen = new Set();
    for (let j = i; j < n; j++) {
      seen.add(s[j]);

      if (seen.size > 2) break;

      res = Math.max(res, j - i + 1);
    }
  }
}
```

时间复杂度：O(n²)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

#### 2. 滑动窗口 + 哈希表 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，窗口中仅包含两种不同的字符。使用哈希表记录当前窗口不同字符的数量。

遍历 `s`，每次向右移动右边界扩大窗口，将当前字符添加到窗口中，并更新当前子串的长度。如果当前窗口中不同字符数量超过两个，则向右移动左边界收缩窗口，直到窗口中只包含两个不同字符为止。

``` js
var lengthOfLongestSubstringTwoDistinct = function (s) {
  const n = s.length;
  const map = new Map();
  let l = 0;
  let r = 0;
  let res = 0;

  while (r < n) {
    // 扩大窗口
    map.set(s[r], (map.get(s[r]) ?? 0) + 1);

    // 当前窗口中不同字符数量超过两个，则向右移动左边界收缩窗口
    while (map.size > 2) {
      map.set(s[l], map.get(s[l]) - 1);
      if (map.get(s[l]) === 0) {
        map.delete(s[l]);
      }
      l++;
    }

    res = Math.max(res, r - l + 1);

    r++;
  }

  return res;
}
```

时间复杂度：O(n)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

### [340. 至多包含 K 个不同字符的最长子串 🟡 🔒](https://leetcode.cn/problems/longest-substring-with-at-most-k-distinct-characters/description/)

题目要求：在给定字符串 `s` 中找出最多包含 `k` 个不同字符的最长子串的长度。

解题思路与上一题相同，这里就不在赘述。

#### 1. 暴力法

``` js
var lengthOfLongestSubstringKDistinct = function (s, k) {
  const n = s.length;
  let res = 0;

  for (let i = 0; i < n; i++) {
    const seen = new Set();
    for (let j = i; j < n; j++) {
      seen.add(s[j]);

      if (seen.size > k) break;

      res = Math.max(res, j - i + 1);
    }
  }

  return res;
}
```

#### 2. 滑动窗口 + 哈希表 👍

``` js
var lengthOfLongestSubstringKDistinct = function (s, k) {
  const n = s.length;
  const map = new Map();
  let l = 0;
  let r = 0;
  let res = 0;

  while (r < n) {
    // 扩大窗口
    map.set(s[r], (map.get(s[r]) ?? 0) + 1);

    // 当前窗口中不同字符数量超过 k 个，则向右移动左边界收缩窗口
    while (map.size > k) {
      map.set(s[l], map.get(s[l]) - 1);
      if (map.get(s[l]) === 0) {
        map.delete(s[l]);
      }
      l++;
    }

    res = Math.max(res, r - l + 1);

    r++;
  }

  return res;
}
```

### [395. 至少有 K 个重复字符的最长子串 🟡](https://leetcode.cn/problems/longest-substring-with-at-least-k-repeating-characters/description/)

题目要求：在一个**仅包含小写字母**的字符串 `s` 中找出至少包含 `k` 个重复字符的最长子串的长度。

#### 1. 暴力法

双层循环枚举所有子串，统计每个子串中每个字符出现的次数，判断子串中的每个字符出现的次数是否都大于等于 `k`，记录至少包含 `k` 个重复字符的最长子串的长度。

``` js
var longestSubstring = function (s, k) {
  const n = s.length;
  let res = 0;

  for (let i = 0; i < n; i++) {
    const map = new Map();
    for (let j = i; j < n; j++) {
      map.set(s[j], (map.get(s[j]) ?? 0) + 1);

      if (isValid(map, k)) {
        res = Math.max(res, j - i + 1);
      }
    }
  }

  return res;
};

const isValid = (map, k) => {
  for (const count of map.values()) {
    if (count < k) return false;
  }
  return true;
};
```

时间复杂度：O(n²)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

#### 2. 分治法

首先统计字符串中每个字符出现的次数，然后将第一个出现次数小于 `k` 的字符作为分隔符，对字符串进行分割，最后分别求解左右子串的最长子串长度，取最大值作为结果。

``` js
var longestSubstring = function (s, k) {
  const n = s.length;
  if (!n && k > n) return 0;

  const map = new Map();
  for (const c of s) {
    map.set(c, (map.get(c) ?? 0) + 1);
  }

  for (let i = 0; i < n; i++) {
    if (map.get(s[i]) < k) {
      const left = longestSubstring(s.slice(0, i), k);
      const right = longestSubstring(s.slice(i + 1), k);
      return Math.max(left, right);
    }
  }

  return n;
};
```

时间复杂度：O(n²)，空间复杂度：O(n)。

TODO，关于分治法的更多信息，我们将在后面的相关专题中详细介绍。

#### 3. 滑动窗口 + 哈希表 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界。使用哈希表统计窗口中每个字符出现的次数，保证窗口中的每个字符至少出现 `k` 次，以求得符合条件的最长子串。

枚举不同数量的字符作为窗口的目标。计算当前窗口内是否所有字符的出现次数都大于等于 `k`。

定义 `uniqueTarget` 变量表示窗口内允许不同字符出现的最大数量。在当前窗口中，`uniqueCount` 变量用于维护字符出现的次数，`countAtLeastK` 变量统计符合条件的字符数量。

遍历 `s`，每次向右移动右边界扩大窗口，将当前字符添加到窗口中。如果窗口内不同的字符数量大于窗口允许的最大不同字符数，则向右移动左边界收缩窗口。如果窗口中所有字符的出现的次数都满足条件则更新子串的长度。

``` js
var longestSubstring = function (s, k) {
  const n = s.length;
  let res = 0;

  for (let uniqueTarget = 1; uniqueTarget <= 26; uniqueTarget++) {
    const map = new Map();
    let l = 0;
    let r = 0;
    let uniqueCount = 0;
    let countAtLeastK = 0;

    while (r < n) {
      // 扩大窗口
      if (!map.get(s[r])) uniqueCount++;
      map.set(s[r], (map.get(s[r]) ?? 0) + 1);
      if (map.get(s[r]) === k) countAtLeastK++;

      // 不满足要求，则向右移动左边界收缩窗口
      while (uniqueCount > uniqueTarget) {
        if (map.get(s[l]) === k) countAtLeastK--;
        map.set(s[l], map.get(s[l]) - 1);
        if (map.get(s[l]) === 0) uniqueCount--;
        l++;
      }
	  
      // 窗口内所有字符的都至少重复 k 次
      if (uniqueCount === uniqueTarget && uniqueCount === countAtLeastK) {
        res = Math.max(res, r - l + 1);
      }

      r++;
    }
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

### [424. 替换后的最长重复字符 🟡](https://leetcode.cn/problems/longest-repeating-character-replacement/description/)

题目要求：在一个**仅包含大写字母**的字符串 `s` 中，选择任意一个字符替换为其他任意大写字母，最多执行 `k` 次替换操作。返回包含相同字母的最长子串的长度。

#### 1. 暴力法（超时）

枚举所有可能的子串，使用哈希表统计当前子串每个字符出现的次数，计算子串中任意字符替换 `k` 次后包含相同字母的最长子串长度。

``` js
var characterReplacement = function (s, k) {
  const n = s.length;
  let res = 0;

  for (let i = 0; i < n; i++) {
    for (let j = i; j < n; j++) {
      const substr = s.slice(i, j + 1);
      const substrLen = substr.length;

      const map = new Map();
      for (const c of substr) {
        map.set(c, (map.get(c) ?? 0) + 1);
      }

      const maxCount = Math.max(...map.values());
      // 替换后不能使子串字符全部相同
      if (substrLen - maxCount > k) break;
      res = Math.max(res, substrLen);
    }
  }

  return res;
};
```

时间复杂度：O(n³)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

#### 2. 滑动窗口 + 数组 👍

定义两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，由于字符串中仅包含大写字母，所以使用长度为 `26` 的数组记录窗口内每个字符出现的次数。

遍历 `s`，每次向右移动右边界扩大窗口，然后更新窗口内字符出现的次数并尝试更新字符的最大出现次数，如果当前窗口任意字符替换 `k` 次后不能让所有字符相同，则向右移动左指针收缩窗口，并更新窗口内字符出现的次数。最后更新子字符串长度，重复以上操作直到遍历完 `s` 为止。

``` js
var characterReplacement = function (s, k) {
  const n = s.length;
  let res = 0;
  let l = 0;
  let r = 0;
  const count = new Array(26).fill(0);
  let maxCount = 0;

  while (r < n) {
    // 扩大窗口
    const c = s.charCodeAt(r) - 'A'.charCodeAt();
    count[c]++;
    maxCount = Math.max(maxCount, count[c]);

    // 窗口内除出现次数最多的字符外其它字符不能完全被替换，则向右移动左边界收缩窗口
    if (r - l + 1 - maxCount > k) {
      count[s.charCodeAt(l) - 'A'.charCodeAt()]--;
      l++;
    }

    res = Math.max(res, r - l + 1);

    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

## 无重复子串

### [3. 无重复字符的最长子串 🟡 ❤](https://leetcode.cn/problems/longest-substring-without-repeating-characters/description/)

题目要求：在给定字符串 `s` 中找出不包含重复字符的最长子串的长度。

#### 1. 暴力法

使用双层循环枚举所有子串，使用 `Set` 判断子串是否包含重复字符，记录不包含重复字符的最长子串的长度。

``` js
var lengthOfLongestSubstring = function (s) {
  const n = s.length;
  let res = 0;

  for (let i = 0; i < n; i++) {
    const seen = new Set();
    for (let j = i; j < n; j++) {
      if (seen.has(s[j])) break;
      seen.add(s[j]);
      
      res = Math.max(res, j - i + 1);
    }
  }

  return res;
};
```

时间复杂度：O(n²)，空间复杂度：O(n)。

#### 2. 滑动窗口 + Set 👍

维护一个可变大小的滑动窗口。使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界，窗口内不能包含重复的字符。使用 `Set` 判断当前窗口是否包含重复字符。

遍历 `s`，每次向右移动右边界扩大窗口，将当前字符添加到窗口中，并计算无重复字符字串的长度。如果当前窗口出现重复字符，则向右移动左边界收缩窗口。重复以上过程直到遍历完字符 `s` 为止。

``` js
var lengthOfLongestSubstring = function (s) {
  const n = s.length;
  let res = 0;
  let l = 0;
  let r = 0;
  const seen = new Set();

  while (r < n) {
    // 当前窗口出现重复字符，向右移动窗口左边界收缩窗口
    while (seen.has(s[r])) {
      seen.delete(s[l]);
      l++;
    }
	
    // 扩大窗口，并计算无重复字符字串的长度
    seen.add(s[r]);
    res = Math.max(res, r - l + 1);
    
    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。

#### 3. 滑动窗口 + 哈希表 👍

我们可以对收缩窗口进行优化，使用哈希表记录当前字符最后出现的位置，如果出现重复字符，我们直接将窗口左边界向右移动 `k` 位而不是一位，从而避免重复计算。

``` js
var lengthOfLongestSubstring = function (s) {
  const n = s.length;
  let res = 0;
  let l = 0;
  let r = 0;
  const map = new Map();

  while (r < n) {
    // 当前窗口出现重复字符，尝试收缩窗口
    if (map.has(s[r])) {
      l = Math.max(map.get(s[r]) + 1, l);
    }

    // 扩大窗口，并计算无重复字符字串的长度
    map.set(s[r], r);
    res = Math.max(res, r - l + 1);

    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(n)。


### [1100. 长度为 K 的无重复字符子串 🟡 🔒](https://leetcode.cn/problems/find-k-length-substrings-with-no-repeated-characters/description/)

题目要求：在一个**仅包含小写英文字母**的字符串 `s` 中，找出所有长度为 `k` 的无重复字符子串的个数。

#### 1. 暴力法

遍历遍历所有长度为 k 的子串，检查这些子串是否包含重复字符，统计无重复字符的子串个数。

``` js
var numKLenSubstrNoRepeats = function (s, k) {
  if (k > s.length) return 0;

  let res = 0;

  for (let i = 0; i <= s.length - k; i++) {
    let substr = s.slice(i, i + k);
    if (new Set(substr).size === k) res++;
  }

  return res;
};
```

时间复杂度：O(nk)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

#### 2. 滑动窗口 + Map 👍

维护一个固定大小的滑动窗口，使用哈希表 `map` 记录当前窗口中每个字符出现的次数。

每次向右滑动一位窗口，如果窗口的大小与 `k` 相同，则说明该子串是无重复字符子串，将更新符合条件的子串个数，继续寻找其它无重复字符子串，直到窗口滑动到 `s` 右侧为止。

``` js
var numKLenSubstrNoRepeats = function (s, k) {
  if (k > s.length) return 0;

  const n = s.length;
  let res = 0;
  const map = new Map();
  
  for (let i = 0; i < k; i++) {
    map.set(s[i], (map.get(s[i]) ?? 0) + 1);
  }
  if (map.size === k) res++;

  for (let i = k; i < n; i++) {
    // 移动窗口右边界
    map.set(s[i], (map.get(s[i]) ?? 0) + 1);

    // 移动窗口左边界
    map.set(s[i - k], map.get(s[i - k]) - 1);
    if (map.get(s[i - k]) === 0) map.delete(s[i - k]);
    
    if (map.size === k) res++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。

#### 3. 滑动窗口 + Set 👍

维护一个可变大小的滑动窗口，使用两个变量 `l` 和 `r` 分别表示滑动窗口的左右边界。使用哈希集合 `seen` 来检查窗口内的字符是否重复。

遍历 `s`，每次向右移动右边界扩大窗口，将当前字符添加到窗口中。

如果当前窗口出现重复字符，则向右移动左边界收缩窗口，直到窗口内没有重复字符为止。

如果当前窗口有 `k` 个不同的字符，则更新符合条件的子数组个数，并向右移动左边界收缩窗口继续寻找满足条件的子串。

``` js
var numKLenSubstrNoRepeats = function (s, k) {
  if (k > s.length) return 0;

  let res = 0;
  let l = 0;
  let r = 0;
  const seen = new Set();

  while (r < s.length) {
    // 不满足条件，向右滑动左指针收缩窗口
    while (seen.has(s[r])) {
      seen.delete(s[l]);
      l++;
    }

    // 扩大窗口
    seen.add(s[r]);

    // 满足条件，尝试向右滑动左指针收缩窗口找出符合条件的子串
    if (r - l + 1 === k) {
      res++;
      seen.delete(s[l]);
      l++;
    };

    r++;
  }

  return res;
};
```

时间复杂度：O(n)，空间复杂度：O(|Σ|)，|Σ| 为字符集的大小。