# Assignmnet 18 - Searching and Sorting

---

Q1. Given an array of `intervals` where `intervals[i] = [starti, endi]`, merge all overlapping intervals, and return *an array of the non-overlapping intervals that cover all the intervals in the input*.

**Example 1:**

```
Input: intervals = [[1,3],[2,6],[8,10],[15,18]]
Output: [[1,6],[8,10],[15,18]]
Explanation: Since intervals [1,3] and [2,6] overlap, merge them into [1,6].

```

**Example 2:**

```
Input: intervals = [[1,4],[4,5]]
Output: [[1,5]]
Explanation: Intervals [1,4] and [4,5] are considered overlapping.

```

**Constraints:**

- `1 <= intervals.length <= 10000`
- `intervals[i].length == 2`
- `0 <= starti <= endi <= 10000`

**Solution 1:**

```javascript
function mergeIntervals(intervals) {
	intervals.sort((a, b) => a[0] - b[0]);

	const merged = [];
	for (const interval of intervals) {
		if (merged.length === 0 || interval[0] > merged[merged.length - 1][1]) {
			merged.push(interval);
		} else {
			merged[merged.length - 1][1] = Math.max(
				merged[merged.length - 1][1],
				interval[1]
			);
		}
	}

	return merged;
}

const intervals1 = [
	[1, 3],
	[2, 6],
	[8, 10],
	[15, 18],
];
console.log(mergeIntervals(intervals1));
// Output: [[1, 6], [8, 10], [15, 18]]

const intervals2 = [
	[1, 4],
	[4, 5],
];
console.log(mergeIntervals(intervals2));
// Output: [[1, 5]]
```

---

Q2. Given an array `nums` with `n` objects colored red, white, or blue, sort them **[in-place](https://en.wikipedia.org/wiki/In-place_algorithm)** so that objects of the same color are adjacent, with the colors in the order red, white, and blue.

We will use the integers `0`, `1`, and `2` to represent the color red, white, and blue, respectively.

You must solve this problem without using the library's sort function.

**Example 1:**

```
Input: nums = [2,0,2,1,1,0]
Output: [0,0,1,1,2,2]

```

**Example 2:**

```
Input: nums = [2,0,1]
Output: [0,1,2]

```

**Constraints:**

- `n == nums.length`
- `1 <= n <= 300`
- `nums[i]` is either `0`, `1`, or `2`.

**Solution 2:**

```javascript
function sortColors(nums) {
	let low = 0; // index for 0s (red)
	let mid = 0; // index for 1s (white)
	let high = nums.length - 1; // index for 2s (blue)

	while (mid <= high) {
		if (nums[mid] === 0) {
			// If the current element is 0, swap it with the element at the low index
			[nums[low], nums[mid]] = [nums[mid], nums[low]];
			low++;
			mid++;
		} else if (nums[mid] === 1) {
			// If the current element is 1, move to the next element
			mid++;
		} else {
			// If the current element is 2, swap it with the element at the high index
			[nums[mid], nums[high]] = [nums[high], nums[mid]];
			high--;
		}
	}
}

const nums1 = [2, 0, 2, 1, 1, 0];
sortColors(nums1);
console.log(nums1);
// Output: [0, 0, 1, 1, 2, 2]

const nums2 = [2, 0, 1];
sortColors(nums2);
console.log(nums2);
// Output: [0, 1, 2]
```

---

Q3. **First Bad Version Solution**

You are a product manager and currently leading a team to develop a new product. Unfortunately, the latest version of your product fails the quality check. Since each version is developed based on the previous version, all the versions after a bad version are also bad.

Suppose you have `n` versions `[1, 2, ..., n]` and you want to find out the first bad one, which causes all the following ones to be bad.

You are given an API `bool isBadVersion(version)` which returns whether `version` is bad. Implement a function to find the first bad version. You should minimize the number of calls to the API.

**Example 1:**

```
Input: n = 5, bad = 4
Output: 4
Explanation:
call isBadVersion(3) -> false
call isBadVersion(5) -> true
call isBadVersion(4) -> true
Then 4 is the first bad version.

```

**Example 2:**

```
Input: n = 1, bad = 1
Output: 1

```

**Constraints:**

- `1 <= bad <= n <= 2^31 - 1`

**Solution 3:**

```javascript
function firstBadVersion(n) {
	let left = 1;
	let right = n;

	while (left < right) {
		const mid = Math.floor((left + right) / 2);

		if (isBadVersion(mid)) {
			right = mid;
		} else {
			left = mid + 1;
		}
	}

	return left;
}

const n1 = 5;
const bad1 = 4;
console.log(firstBadVersion(n1)); // Output: 4

const n2 = 1;
const bad2 = 1;
console.log(firstBadVersion(n2)); // Output: 1
```

---

Q4. **Maximum Gap**

Given an integer array `nums`, return *the maximum difference between two successive elements in its sorted form*. If the array contains less than two elements, return `0`.

You must write an algorithm that runs in linear time and uses linear extra space.

**Example 1:**

```
Input: nums = [3,6,9,1]
Output: 3
Explanation: The sorted form of the array is [1,3,6,9], either (3,6) or (6,9) has the maximum difference 3.

```

**Example 2:**

```
Input: nums = [10]
Output: 0
Explanation: The array contains less than 2 elements, therefore return 0.

```

**Constraints:**

- `1 <= nums.length <= 10^5`
- `0 <= nums[i] <= 10^9`

Solution 4:

```javascript
function maximumGap(nums) {
	const n = nums.length;

	if (n < 2) {
		return 0;
	}

	const maxNum = Math.max(...nums);

	let exp = 1;
	const buckets = Array.from({ length: 10 }, () => []);
	const sorted = new Array(n);

	while (Math.floor(maxNum / exp) > 0) {
		for (let i = 0; i < n; i++) {
			const num = nums[i];
			const digit = Math.floor(num / exp) % 10;
			buckets[digit].push(num);
		}

		let idx = 0;
		for (let bucket of buckets) {
			while (bucket.length > 0) {
				sorted[idx++] = bucket.shift();
			}
		}

		exp *= 10;
	}

	let maxGap = 0;
	for (let i = 1; i < n; i++) {
		maxGap = Math.max(maxGap, sorted[i] - sorted[i - 1]);
	}

	return maxGap;
}

const nums1 = [3, 6, 9, 1];
console.log(maximumGap(nums1)); // Output: 3

const nums2 = [10];
console.log(maximumGap(nums2)); // Output: 0
```

---

Q5. Given an integer array `nums`, return `true` if any value appears **at least twice** in the array, and return `false` if every element is distinct.

**Example 1:**

```
Input: nums = [1,2,3,1]
Output: true

```

**Example 2:**

```
Input: nums = [1,2,3,4]
Output: false

```

**Example 3:**

```
Input: nums = [1,1,1,3,3,4,3,2,4,2]
Output: true

```

**Constraints:**

- `1 <= nums.length <= 10^5`
- `109 <= nums[i] <= 10^9`

**Solution 5:**

```javascript
function containsDuplicate(nums) {
	const set = new Set();

	for (const num of nums) {
		if (set.has(num)) {
			return true;
		}
		set.add(num);
	}

	return false;
}

const nums1 = [1, 2, 3, 1];
console.log(containsDuplicate(nums1));
// Output: true

const nums2 = [1, 2, 3, 4];
console.log(containsDuplicate(nums2));
// Output: false

const nums3 = [1, 1, 1, 3, 3, 4, 3, 2, 4, 2];
console.log(containsDuplicate(nums3));
// Output: true
```

---

Q6. **Minimum Number of Arrows to Burst Balloons**

There are some spherical balloons taped onto a flat wall that represents the XY-plane. The balloons are represented as a 2D integer array `points` where `points[i] = [xstart, xend]` denotes a balloon whose **horizontal diameter** stretches between `xstart` and `xend`. You do not know the exact y-coordinates of the balloons.

Arrows can be shot up **directly vertically** (in the positive y-direction) from different points along the x-axis. A balloon with `xstart` and `xend` is **burst** by an arrow shot at `x` if `xstart <= x <= xend`. There is **no limit** to the number of arrows that can be shot. A shot arrow keeps traveling up infinitely, bursting any balloons in its path.

Given the array `points`, return *the **minimum** number of arrows that must be shot to burst all balloons*.

**Example 1:**

```
Input: points = [[10,16],[2,8],[1,6],[7,12]]
Output: 2
Explanation: The balloons can be burst by 2 arrows:
- Shoot an arrow at x = 6, bursting the balloons [2,8] and [1,6].
- Shoot an arrow at x = 11, bursting the balloons [10,16] and [7,12].

```

**Example 2:**

```
Input: points = [[1,2],[3,4],[5,6],[7,8]]
Output: 4
Explanation: One arrow needs to be shot for each balloon for a total of 4 arrows.

```

**Example 3:**

```
Input: points = [[1,2],[2,3],[3,4],[4,5]]
Output: 2
Explanation: The balloons can be burst by 2 arrows:
- Shoot an arrow at x = 2, bursting the balloons [1,2] and [2,3].
- Shoot an arrow at x = 4, bursting the balloons [3,4] and [4,5].

```

**Constraints:**

- `1 <= points.length <= 10^5`
- `points[i].length == 2`
- `231 <= xstart < xend <= 2^31 - 1`

**Solution 6:**

```javascript
function findMinArrowShots(points) {
	if (!points.length) {
		return 0;
	}

	points.sort((a, b) => a[1] - b[1]); // Sort balloons based on end points

	let arrows = 1;
	let maxEnd = points[0][1];

	for (let i = 1; i < points.length; i++) {
		if (points[i][0] > maxEnd) {
			arrows++;
			maxEnd = points[i][1];
		}
	}

	return arrows;
}

const points1 = [
	[10, 16],
	[2, 8],
	[1, 6],
	[7, 12],
];
console.log(findMinArrowShots(points1)); // Output: 2

const points2 = [
	[1, 2],
	[3, 4],
	[5, 6],
	[7, 8],
];
console.log(findMinArrowShots(points2)); // Output: 4

const points3 = [
	[1, 2],
	[2, 3],
	[3, 4],
	[4, 5],
];
console.log(findMinArrowShots(points3)); // Output: 2
```

---

Q7. **Longest Increasing Subsequence**

Given an integer array `nums`, return \*the length of the longest **strictly increasing\***

**_subsequence_**

.

**Example 1:**

```
Input: nums = [10,9,2,5,3,7,101,18]
Output: 4
Explanation: The longest increasing subsequence is [2,3,7,101], therefore the length is 4.

```

**Example 2:**

```
Input: nums = [0,1,0,3,2,3]
Output: 4

```

**Example 3:**

```
Input: nums = [7,7,7,7,7,7,7]
Output: 1

```

**Constraints:**

- `1 <= nums.length <= 2500`
- `-10^4 <= nums[i] <= 10^4`

**Solution 7:**

```javascript
function lengthOfLIS(nums) {
	const n = nums.length;
	const dp = Array(n).fill(1);

	for (let i = 1; i < n; i++) {
		for (let j = 0; j < i; j++) {
			if (nums[i] > nums[j]) {
				dp[i] = Math.max(dp[i], dp[j] + 1);
			}
		}
	}

	let maxLength = 1;
	for (let i = 0; i < n; i++) {
		maxLength = Math.max(maxLength, dp[i]);
	}

	return maxLength;
}

const nums1 = [10, 9, 2, 5, 3, 7, 101, 18];
console.log(lengthOfLIS(nums1));
// Output: 4

const nums2 = [0, 1, 0, 3, 2, 3];
console.log(lengthOfLIS(nums2));
// Output: 4

const nums3 = [7, 7, 7, 7, 7, 7, 7];
console.log(lengthOfLIS(nums3));
// Output: 1
```

---

Q8. Given an array of `n` integers `nums`, a **132 pattern** is a subsequence of three integers `nums[i]`, `nums[j]` and `nums[k]` such that `i < j < k` and `nums[i] < nums[k] < nums[j]`.

Return `true` *if there is a **132 pattern** in* `nums`*, otherwise, return* `false`_._

**Example 1:**

```
Input: nums = [1,2,3,4]
Output: false
Explanation: There is no 132 pattern in the sequence.

```

**Example 2:**

```
Input: nums = [3,1,4,2]
Output: true
Explanation: There is a 132 pattern in the sequence: [1, 4, 2].

```

**Example 3:**

```
Input: nums = [-1,3,2,0]
Output: true
Explanation: There are three 132 patterns in the sequence: [-1, 3, 2], [-1, 3, 0] and [-1, 2, 0].

```

**Constraints:**

- `n == nums.length`
- `1 <= n <= 2 * 10^5`
- `-10^9 <= nums[i] <= 10^9`

**Solution 8:**

```javascript
function find132pattern(nums) {
	const n = nums.length;
	const stack = [];

	let potentialTwo = -Infinity;

	for (let i = n - 1; i >= 0; i--) {
		if (nums[i] < potentialTwo) {
			return true;
		}

		while (stack.length > 0 && nums[i] > stack[stack.length - 1]) {
			potentialTwo = stack.pop();
		}

		stack.push(nums[i]);
	}

	return false;
}

const nums1 = [1, 2, 3, 4];
console.log(find132pattern(nums1));
// Output: false

const nums2 = [3, 1, 4, 2];
console.log(find132pattern(nums2));
// Output: true

const nums3 = [-1, 3, 2, 0];
console.log(find132pattern(nums3));
// Output: true
```

---
