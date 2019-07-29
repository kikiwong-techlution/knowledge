- 实现一个strStr（）函数，在haystack字符串中找出needle字符串出现的第一个位置，如果不存在则返回0
```javascript
//method-1
var strStr = function(haystack, needle) {
    if(needle === '') return 0
    const length=needle.length
    let index=0
    
    while(index+length <= haystack.length){
        let str=haystack.slice(index,index+needle.length)
        if(str===needle){
            return index
        }
        index++
    }
    return -1
};

//method-2
var strStr = function(haystack, needle) {
    return haystack.indexOf(needle)
};
```
- 在排序数组中查找元素的第一个和最后一个位置  
给定一个按照升序排列的整数数组 nums，和一个目标值 target。找出给定目标值在数组中的开始位置和结束位置  
如果数组中不存在目标值，返回 [-1, -1]。你的算法时间复杂度必须是 O(log n) 级别。
```javascript
var searchRange = function(nums, target) {
  let result = [-1, -1];
  let len = nums.length;
  if (len === 0) return result;
  let l = 0, r = len - 1;
  while (l < r) {
    let mid = (l + r) / 2 | 0;
    if (target <= nums[mid]) r = mid;
    else l = mid + 1
  }
  if (nums[l] !== target) return result;
  result[0] = l;

  r = len - 1;
  while(l < r) {
    let mid = (l + r) / 2 | 0;
    if (target >= nums[mid]) l = mid + 1
    else r = mid;
  }
  if (nums[r] === target) result[1] = r
  else result[1] = r - 1
  return result;
};
```
- 合并两个有序数组  
  给定两个有序整数数组  nums1 和 nums2，将 nums2 合并到  nums1  中，使得  num1 成为一个有序数组。  
  说明:  
  初始化  nums1 和 nums2 的元素数量分别为  m 和 n。
  你可以假设  nums1  有足够的空间（空间大小大于或等于  m + n）来保存 nums2 中的元素。

```javascript
//method-1 有bug
var merge = function(nums1, m, nums2, n) {
  nums1.splice(m, n, ...nums2);
  nums1.sort((a, b) => a - b);
  return nums1;
};

//method-2
var merge = function(nums1, m, nums2, n) {
  let count1 = 0;
  let count2 = 0;
  let len = nums1.length;
  while (count1 < m && count2 < n) {
    if (nums1[count1] > nums2[count2]) {
      nums1.push(nums2[count2++]);
    } else {
      nums1.push(nums1[count1++]);
    }
  }

  if (count1 < m) {
    nums1.splice(nums1.length, 0, ...nums1.slice(count1, m));
  }

  if (count2 < n) {
    nums1.splice(nums1.length, 0, ...nums2.slice(count2, n));
  }
  nums1.splice(0, len);
  return nums1;
};
```

- 找到所有数组中消失的数据

```javascript
//method-1
var findDisappearedNumbers = function(nums) {
  let arr = [];
  let res = [];
  for (let i = 1; i <= nums.length; i++) {
    arr[i] = i;
  }
  let map = new Map();
  nums.forEach(key => {
    map.set(key);
  });
  arr.map(item => {
    if (!map.has(item)) {
      res.push(item);
    }
  });

  return res;
};
var nums = [4, 3, 2, 7, 8, 2, 3, 1];
console.log(findDisappearedNumbers(nums));

//method-2
// 创建一个输入数组长度的数组，填充0，然后遍历输入数组
// 把输入数组的每一项的值作为创建数组的下标
// 然后遍历创建数组，其中值为0的就是没有的元素，把其下标值+1填充到返回数组中：
var findDisappearedNumbers = function(nums) {
  let map = new Array(nums.length),
    arr = [];
  map.fill(0);
  for (let i = 0; i < nums.length; i++) map[nums[i] - 1]++;
  for (let i = 0; i < map.length; i++) if (map[i] === 0) arr.push(i + 1);
  return arr;
};
console.log(findDisappearedNumbers(nums));
```

- 高度检查器

```javascript
var heightChecker = function(heights) {
  var arr = [...heights];
  arr.sort((a, b) => a - b);
  var count = 0;
  for (let i = 0; i < arr.length; i++) {
    if (arr[i] !== heights[i]) {
      count++;
    }
  }
  return count;
};
var heights = [1, 1, 4, 2, 1, 3];
console.log(heightChecker(heights));
```

- 按奇偶排序数组

```javascript
var sortArrayByParityII = function(A) {
  let len = A.length;
  let j = len - 2;
  for (let i = len - 1; i >= 0; i = i - 2) {
    if (A[i] % 2 !== i % 2) {
      while (A[j] % 2 === j % 2) {
        j = j - 2;
      }
      [A[i], A[j]] = [A[j], A[i]];
    }
  }
  return A;
};

/*
 * 0. 循环A数组
 * 1. 找出偶数位的奇数。
 * 2. 在1的情况下，找出奇数位的偶数
 * 3. 将1/2中找到的数，对调一下
 * 4. 继续循环A数组，执行1-3的过程
 * @param {number[]} A
 * @return {number[]}
 */
var sortArrayByParityII = function(A) {
  let j = 1;
  for (let i = 0; i < A.length - 1; i++) {
    if (!(i % 2) && A[i] % 2) {
      // 偶数位上的奇数
      // 找出奇数位上的偶数
      while (j < A.length && A[j] % 2) {
        j = j + 2;
      }
      [A[i], A[j]] = [A[j], A[i]];
    }
  }
  return A;
};

/*
 * 1. 找到偶数位上的奇数，奇数位上的偶数，并分别存储在两个数组中
 * 2. 将两个新数组中的数字，在A数组中对调位子
 * @param {number[]} A
 * @return {number[]}
 */
var sortArrayByParityII = function(A) {
  let len = A.length;
  const odds = []; // 偶数位上的奇数
  const evens = []; // 奇数位上的偶数

  for (let i = 0; i < len; i++) {
    if (!(i % 2) && A[i] % 2) {
      odds.push(i);
    } else if (i % 2 && !(A[i] % 2)) {
      evens.push(i);
    }
    if (evens.length && odds.length) {
      let even = evens.pop();
      let odd = odds.pop();
      [A[even], A[odd]] = [A[odd], A[even]];
    }
  }
  return A;
};
```

- 有序数组的平方

```javascript
//method-1
var sortedSquares = function(A) {
  return A.map(item => {
    return Math.pow(item, 2);
  }).sort((a, b) => a - b);
};

//method-2
var sortedSquares = function(A) {
  for (let i = 0, len = A.length; i < len; i++) {
    A[i] = A[i] * A[i];
  }
  function compare(val1, val2) {
    if (val1 > val2) {
      return 1;
    } else if (val1 < val2) {
      return -1;
    } else {
      return 0;
    }
  }
  A.sort(compare);
  return A;
};

A = [-4, -1, 0, 3, 10];
console.log(sortedSquares(A));
```

- 翻转图像
  先水平翻转，再将 1 变成 0，0 变成 1。

```javascript
//method-1
var flipAndInvertImage = function(A) {
  return A.map(item => item.reverse().map(val => val ^ 1));
};
var A = [[1, 1, 0], [1, 0, 1], [0, 0, 0]];
console.log(flipAndInvertImage(A));

//method-2
var flipAndInvertImage = function(A) {
  for (let i = 0; i < A.length; i++) {
    A[i].reverse();
    for (let j = 0; j < A.length; j++) {
      A[i][j] = A[i][j] == 1 ? 0 : 1;
    }
  }
  return A;
};
var A = [[1, 1, 0], [1, 0, 1], [0, 0, 0]];
console.log(flipAndInvertImage(A));
```

- 按奇偶排序数组

```javascript
var sortArrayByParity = function(A) {
  const res = [];
  A.forEach(element => {
    if (element % 2 == 0) {
      res.unshift(element);
    } else {
      res.push(element);
    }
  });
  return res;
};
var A = [3, 1, 2, 4];
console.log(sortArrayByParity(A));
```

- Majority Element 求众数 2

```javascript
var majorityElement = function(nums) {
  let len = nums.length / 3;
  let map = new Map();
  let majorArr = [];
  nums.forEach(item => {
    if (map.get(item)) {
      map.set(item, map.get(item) + 1);
    } else {
      map.set(item, 1);
    }
  });
  map.forEach((value, key) => {
    if (value > len) {
      majorArr.push(key);
    }
  });
  return majorArr;
};
var nums = [1, 2, 2, 3, 3, 3];
console.log(majorityElement(nums));
```

- Majority Element 求众数 1

```javascript
var majorityElement = function(nums) {
  if (nums.length === 1) {
    return nums[0];
  }
  const map = new Map();
  for (let n of nums) {
    if (!map.get(n)) {
      map.set(n, 1);
    } else {
      map.set(n, map.get(n) + 1);
      if (map.get(n) > nums.length / 2) {
        return n;
      }
    }
  }
};
var nums = [2, 2, 1, 1, 2, 2];
console.log(majorityElement(nums));
```

- Search Insert Position
  - 注意边界条件

```javascript
/**
 * @param {number[]} nums
 * @param {number} target
 * @return {number}
 */
//method-1
var searchInsert = function(nums, target) {
  let res = nums.indexOf(target);
  if (res == -1) {
    for (let i = nums.length - 1; i > 0; i--) {
      if (nums[i] > target && nums[i - 1] < target) {
        return i;
      }
    }
    if (target > nums[nums.length - 1]) {
      return nums.length;
    }
    if (target == 0 || target < nums[0]) {
      return 0;
    }
  }
  return res;
};

//menthod-2
var searchInsert = function(nums, target) {
  if (nums.includes(target)) {
    return nums.indexOf(target);
  }
  return nums.findIndex(ele => ele > target) !== -1
    ? nums.findIndex(ele => ele > target)
    : nums.length;
};
console.log(searchInsert([0, 1, 2], 3));
```

- Valid Anagram

```javascript
var isAnagram = function(s, t) {
  s = s.split("").sort();
  t = t.split("").sort();
  if (s.join("") === t.join("")) {
    return true;
  }
  return false;
};
const s = "stoaall";
const t = "tosllaa";
console.log(isAnagram(s, t));
```

- 移除元素

```javascript
var removeElement = function(nums, val) {
  for (let i = 0; i < nums.length; i++) {
    if (val === nums[i]) {
      nums.splice(i, 1);
      i--;
    }
  }
  return nums.length;
};
let nums = [2, 3, 3, 1, 2];
let val = 3;
console.log(removeElement(nums, val));
```

- 三角形的最大周长：思路-先从小到大排序，a + b < c

```javascript
var largestPerimeter = function(A) {
  var sum = [];

  function compare(val1, val2) {
    if (val1 < val2) {
      return -1;
    } else if (val1 > val2) {
      return 1;
    } else {
      return 0;
    }
  }
  A.sort(compare);
  if (A.length < 3) {
    return 0;
  }
  for (let i = A.length - 1; i >= 2; i--) {
    //从最大的开始比较，因为要3个组成 所以i>=2;
    if (A[i] < A[i - 1] + A[i - 2]) {
      return A[i] + A[i - 1] + A[i - 2];
    }
  }
  return 0;
};
var A = [3, 6, 2, 3];
console.log(largestPerimeter(A));
```

- 有效的括号
  > Map 对象保存键/值对。任何值（对象或者原始值）都可以作为一个键或者一个值。  
  > Map 方法
  >
  > 1. Map.prototype.get(key) -返回指定键的值，不存在返回 undefined
  > 2. Map.prototype.has(key) -返回一个布尔值，表示 Map 的实例是否包含键的对应值
  > 3. Map.prototype.set(key，value) -设置指定键的值，并返回该 Map 对象

```javascript
//method-1
const MATCH = new Map([["(", ")"], ["{", "}"], ["[", "]"]]);
const isString = s => typeof s === "string";
const isEmptyString = s => isString(s) && s.length === 0;
const isEmptyArray = arr => Array.isArray(arr) && arr.length === 0;

var isValid = function(s) {
  if (!typeof s === "string") {
    //not String
    return false;
  }
  if (!typeof s === "string" && s.length === 0) {
    //not Empty
    return true;
  }

  const arr = s.split("");
  const open = [];
  for (const k of arr) {
    if (MATCH.has(k)) {
      open.push(k);
    } else {
      const right = MATCH.get(open.pop());
      const success = right === k;
      if (!success) {
        return false;
      }
    }
  }

  if (isEmptyArray(open)) {
    //isEmptyArray
    return true;
  }
  return false;
};

//method-2
var isValid2 = function(s) {
  str = s.split("");
  if (str.length === 0) {
    return true;
  }
  let first = str[0];
  if (str.length % 2 != 0 || [")", "]", "}"].indexOf(first) != -1) {
    return false;
  }
  let stack = [first];
  const MAP = {
    ")": "(",
    "]": "[",
    "}": "{"
  };
  for (let i = 1; i < str.length; i++) {
    let top = stack.length > 0 ? stack[stack.length - 1] : null;
    let now = str[i];
    if (MAP[now] === top) {
      stack.pop();
    } else {
      stack.push(now);
    }
  }
  return stack.length === 0;
};

console.log(isValid1("}({})"));
console.log(isValid2("({})"));
```

- 整数反转

```javascript
//method-1
function reverse1(x) {
  let res = parseInt(
    Math.abs(x)
      .toString()
      .split("")
      .reverse()
      .join("")
  );
  res = x > 0 ? res : -res;
  if (res > Math.pow(2, 31) - 1 || res < -Math.pow(2, 31)) {
    return 0;
  }
  return res;
}

//method-2
var reverse2 = function(x) {
  let numberToArray = String(Math.abs(x)).split("");
  let result = "";
  for (const i = 0; i < numberToArray.length; ) {
    result += numberToArray.pop();
  }
  result = x > 0 ? Number(result) : Number(result);
  if (result > Math.pow(2, 31) - 1 || x < -Math.pow(2, 31)) {
    return 0;
  }
  return result;
};
console.log(reverse1(-1144444));
console.log(reverse2(2225778800000055));
```
