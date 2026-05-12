# DSA Patterns — Explanations + 2 Problems (JS & Python)

> Format note: Each pattern explanation follows the same structure as your **Fast & Slow Pointers** reference.

---

## 1) Two Pointers Pattern

### What is the Two Pointers Pattern?

The **Two Pointers** pattern is a technique where you use **two indices/pointers** to traverse a data structure (usually an array or string) in a coordinated way.

Typical pointer movements:

- **Opposite directions**: one from the start, one from the end (e.g., palindrome checks)
- **Same direction**: both move forward with a relationship (e.g., removing duplicates)

### Where is this pattern useful?

Common use cases include:

1. Searching pairs/triplets in a sorted array
2. Palindrome checks
3. Partitioning arrays based on a predicate
4. Minimizing/maximizing something across two ends (e.g., container with most water)
5. In-place transformations with O(1) extra space

### How to Recognize When to Apply Two Pointers

| Signal                  | Description                                                                       |
| ----------------------- | --------------------------------------------------------------------------------- |
| Sorted input            | Many two-pointer solutions rely on sorted arrays to decide which pointer to move. |
| Pair/triplet target     | You need pairs/triplets matching sum/condition.                                   |
| Opposite-end comparison | Palindrome / symmetric comparisons.                                               |
| O(1) space requirement  | You want in-place operations without extra memory.                                |

### How to Apply the Pattern — Step-by-Step

**Step 1: Initialize pointers**

```text
left = 0
right = n - 1
```

**Step 2: Iterate while left < right**

- Evaluate condition using `arr[left]` and `arr[right]`
- Decide which pointer to move

**Step 3: Move pointers smartly**

- If sum too small → move `left++`
- If sum too large → move `right--`

### Diagram (typical opposite-direction)

```text
index:  0   1   2   3   4   5
arr:   [a,  b,  c,  d,  e,  f]
        ^               ^
      left            right
```

### Benefits of Two Pointers

| Benefit           | Explanation                                  |
| ----------------- | -------------------------------------------- |
| O(n) time (often) | Replaces nested loops in many pair problems. |
| O(1) extra space  | In-place pointer movement.                   |
| Simple reasoning  | Clear movement rules based on condition.     |

### Example Use Case Patterns

| Use Case       | Question Type        | What to Observe                               |
| -------------- | -------------------- | --------------------------------------------- |
| Pair sum       | Array (often sorted) | Move pointers based on sum relative to target |
| Palindrome     | String/array         | Compare ends and move inward                  |
| Container area | Two ends             | Move the shorter side to improve area         |

---

### Problem A: Two Sum II (sorted array)

LeetCode: https://leetcode.com/problems/two-sum-ii-input-array-is-sorted/

#### JavaScript (Node.js runnable)

```javascript
function twoSum(numbers, target) {
  let left = 0;
  let right = numbers.length - 1;

  while (left < right) {
    const sum = numbers[left] + numbers[right];
    if (sum === target) return [left + 1, right + 1]; // 1-indexed
    if (sum < target) left++;
    else right--;
  }
  return [-1, -1];
}

// Demo
console.log(twoSum([2, 7, 11, 15], 9)); // [1,2]
```

#### Python (runnable)

```python
def two_sum(numbers, target):
    left, right = 0, len(numbers) - 1
    while left < right:
        s = numbers[left] + numbers[right]
        if s == target:
            return [left + 1, right + 1]  # 1-indexed
        if s < target:
            left += 1
        else:
            right -= 1
    return [-1, -1]


if __name__ == "__main__":
    print(two_sum([2, 7, 11, 15], 9))
```

---

### Problem B: Valid Palindrome

LeetCode: https://leetcode.com/problems/valid-palindrome/

#### JavaScript (Node.js runnable)

```javascript
//2002. Maximum Product of the Length of Two Palindromic Subsequences
//2108. Find First Palindromic String in the Array
//3035. Maximum Palindromes After Operations

function isPalindrome(s) {
  let left = 0;
  let right = s.length - 1;

  const isAlphaNum = (ch) => {
    const code = ch.charCodeAt(0);
    return (
      (code >= 48 && code <= 57) ||
      (code >= 65 && code <= 90) ||
      (code >= 97 && code <= 122)
    );
  };

  while (left < right) {
    while (left < right && !isAlphaNum(s[left])) left++;
    while (left < right && !isAlphaNum(s[right])) right--;

    if (s[left].toLowerCase() !== s[right].toLowerCase()) return false;
    left++;
    right--;
  }
  return true;
}

// Demo
console.log(isPalindrome('A man, a plan, a canal: Panama')); // true
console.log(isPalindrome('race a car')); // false
```

#### Python (runnable)

```python
def is_palindrome(s: str) -> bool:
    left, right = 0, len(s) - 1

    def is_alnum(ch: str) -> bool:
        return ch.isalnum()

    while left < right:
        while left < right and not is_alnum(s[left]):
            left += 1
        while left < right and not is_alnum(s[right]):
            right -= 1
        if s[left].lower() != s[right].lower():
            return False
        left += 1
        right -= 1
    return True


if __name__ == "__main__":
    print(is_palindrome("A man, a plan, a canal: Panama"))
    print(is_palindrome("race a car"))
```

---

## 2) Fast and Slow Pointers Pattern

### What is the Fast and Slow Pointers Pattern?

The **Fast and Slow Pointers** pattern (also known as **Floyd’s Cycle Detection Algorithm** or the **Tortoise and Hare technique**) is a two-pointer strategy used to efficiently traverse data structures where:

- elements are connected in a sequence (e.g., linked list, number transformations, circular arrays)
- you can “move” step-by-step through the structure

You use:

- **Slow pointer**: moves 1 step at a time
- **Fast pointer**: moves 2 steps at a time

### Where is this pattern useful?

This pattern is typically used in problems involving:

1. Cycle detection (in linked lists, number sequences, circular arrays)
2. Finding the middle of a list
3. Detecting palindromes in linked lists
4. Finding the start of a loop
5. Math-based problems that create a repetitive cycle (e.g., Happy Number)

### How to Recognize When to Apply Fast and Slow Pointers

| Signal              | Description                                                                                                |
| ------------------- | ---------------------------------------------------------------------------------------------------------- |
| Repetition          | You’re repeatedly transforming or traversing values (e.g., linked list traversal, square-sum of digits).   |
| Cycle possibility   | The structure or transformation could lead to a loop (like a cycle in a linked list or in a number chain). |
| Need for midpoint   | You need to find the middle element in one pass.                                                           |
| O(1) space required | You’re asked to solve in constant space - fast & slow pointers do not use extra memory.                    |

### How to Apply the Pattern — Step-by-Step

**Step 1: Initialize two pointers**

```text
slow = head
fast = head
```

**Step 2: Traverse the structure**

- move slow one step
- move fast two steps

```text
while fast != null and fast.next != null:
    slow = slow.next
    fast = fast.next.next
```

**Step 3: Watch for meeting point**

- if `slow == fast` → cycle exists
- if fast hits null → no cycle

**Step 4: Additional logic**
Depending on goal:

- for cycle start: reset slow=head and move both 1 step until meet
- for middle: when fast reaches end, slow is middle
- for number transforms: treat transform as “next”

### Diagram (cycle detection intuition)

```text
slow:  1 step
fast:  2 steps

If there is a loop, fast will eventually "lap" slow and meet.
```

### Benefits of Fast and Slow Pointers

| Benefit         | Explanation                                         |
| --------------- | --------------------------------------------------- |
| O(n) Time       | Linear traversal - no nested loops needed           |
| O(1) Space      | Only two pointers used - no extra storage           |
| No modification | Works on original structure without altering it     |
| Midpoint access | Efficient way to find middle of list                |
| Cycle detection | Elegant way to detect loop existence or entry point |

### Example Use Case Patterns

| Use Case            | Question Type   | What to Observe                       |
| ------------------- | --------------- | ------------------------------------- |
| Detect cycle        | Linked List     | Traverse nodes, check if slow == fast |
| Find loop start     | Linked List     | After meeting, reset slow=head        |
| Find middle         | Linked List     | Stop when fast hits end               |
| Happy Number        | Integer Problem | Apply next-transform repeatedly       |
| Circular array loop | Array + Modulo  | Move with (i + nums[i]) % n           |

---

### Problem A: Linked List Cycle

LeetCode: https://leetcode.com/problems/linked-list-cycle/

#### JavaScript (Node.js runnable)

```javascript
class ListNode {
  constructor(val) {
    this.val = val;
    this.next = null;
  }
}

function hasCycle(head) {
  let slow = head;
  let fast = head;

  while (fast && fast.next) {
    slow = slow.next;
    fast = fast.next.next;
    if (slow === fast) return true;
  }
  return false;
}

// Demo: 3 -> 2 -> 0 -> -4 -> back to 2
const a = new ListNode(3);
const b = new ListNode(2);
const c = new ListNode(0);
const d = new ListNode(-4);
a.next = b;
b.next = c;
c.next = d;
d.next = b;

console.log(hasCycle(a)); // true
```

#### Python (runnable)

```python
class ListNode:
    def __init__(self, val):
        self.val = val
        self.next = None


def has_cycle(head):
    slow = head
    fast = head
    while fast is not None and fast.next is not None:
        slow = slow.next
        fast = fast.next.next
        if slow is fast:
            return True
    return False


if __name__ == "__main__":
    a = ListNode(3)
    b = ListNode(2)
    c = ListNode(0)
    d = ListNode(-4)
    a.next = b
    b.next = c
    c.next = d
    d.next = b
    print(has_cycle(a))  # True
```

---

### Problem B: Happy Number

LeetCode: https://leetcode.com/problems/happy-number/

#### JavaScript (Node.js runnable)

```javascript
function nextNumber(n) {
  let sum = 0;
  while (n > 0) {
    const digit = n % 10;
    sum += digit * digit;
    n = Math.floor(n / 10);
  }
  return sum;
}

function isHappy(n) {
  let slow = n;
  let fast = n;

  do {
    slow = nextNumber(slow);
    fast = nextNumber(nextNumber(fast));
  } while (slow !== fast);

  return slow === 1;
}

// Demo
console.log(isHappy(19)); // true
console.log(isHappy(2)); // false
```

#### Python (runnable)

```python
def next_number(n: int) -> int:
    s = 0
    while n > 0:
        d = n % 10
        s += d * d
        n //= 10
    return s


def is_happy(n: int) -> bool:
    slow = n
    fast = n
    while True:
        slow = next_number(slow)
        fast = next_number(next_number(fast))
        if slow == fast:
            break
    return slow == 1


if __name__ == "__main__":
    print(is_happy(19))
    print(is_happy(2))
```

---

## 3) Sliding Window Pattern

### What is the Sliding Window Pattern?

The **Sliding Window** pattern is used to solve problems involving **contiguous subarrays/substrings**.
You maintain a window `[start..end]` and slide it across the data while updating some window state (sum, frequency map, etc.).

There are two main variants:

- **Fixed-size window** (exact length `k`)
- **Dynamic window** (grow/shrink based on constraints)

### Where is this pattern useful?

1. Longest/shortest substring with constraints
2. Subarray sums / averages of size k
3. Frequency and anagram checks
4. Minimizing windows (minimum window substring)
5. Problems requiring O(n) instead of O(n²)

### How to Recognize When to Apply Sliding Window

| Signal                   | Description                                         |
| ------------------------ | --------------------------------------------------- |
| Contiguous requirement   | Subarray/substring must be continuous.              |
| Optimize length          | “Longest”, “shortest”, “minimum”, “maximum” window. |
| Constraint within window | K distinct, sum <= X, counts, etc.                  |
| Naive is nested loops    | Brute force checks all subarrays.                   |

### How to Apply the Pattern — Step-by-Step

**Step 1: Initialize window**

```text
start = 0
windowState = ...
```

**Step 2: Expand window with end**
Add `arr[end]` into state.

**Step 3: Shrink window when invalid**
Move `start` forward while constraint is violated.

**Step 4: Track best answer**
Update max/min length or best value.

### Diagram

```text
start -> [ .... window .... ] <- end
```

### Benefits of Sliding Window

| Benefit                | Explanation                                            |
| ---------------------- | ------------------------------------------------------ |
| O(n) time              | Each pointer moves at most n steps.                    |
| Works with constraints | Easy to enforce “at most k distinct”, “sum <= X”, etc. |
| Clean state updates    | Maintain counts/sums incrementally.                    |

### Example Use Case Patterns

| Use Case                | Question Type | What to Observe                     |
| ----------------------- | ------------- | ----------------------------------- |
| Longest with constraint | String        | frequency map + shrink when invalid |
| Fixed window aggregate  | Array         | rolling sum / average               |
| Anagram finding         | String        | frequency match in window           |

---

### Problem A: Minimum Size Subarray Sum

LeetCode: https://leetcode.com/problems/minimum-size-subarray-sum/

#### JavaScript (Node.js runnable)

```javascript
function minSubArrayLen(target, nums) {
  let start = 0;
  let sum = 0;
  let best = Infinity;

  for (let end = 0; end < nums.length; end++) {
    sum += nums[end];
    while (sum >= target) {
      best = Math.min(best, end - start + 1);
      sum -= nums[start++];
    }
  }
  return best === Infinity ? 0 : best;
}

// Demo
console.log(minSubArrayLen(7, [2, 3, 1, 2, 4, 3])); // 2
```

#### Python (runnable)

```python
def min_subarray_len(target, nums):
    start = 0
    s = 0
    best = float('inf')

    for end, val in enumerate(nums):
        s += val
        while s >= target:
            best = min(best, end - start + 1)
            s -= nums[start]
            start += 1

    return 0 if best == float('inf') else best


if __name__ == "__main__":
    print(min_subarray_len(7, [2, 3, 1, 2, 4, 3]))
```

---

### Problem B: Longest Substring Without Repeating Characters

LeetCode: https://leetcode.com/problems/longest-substring-without-repeating-characters/

#### JavaScript (Node.js runnable)

```javascript
function lengthOfLongestSubstring(s) {
  const lastSeen = new Map();
  let start = 0;
  let best = 0;

  for (let end = 0; end < s.length; end++) {
    const ch = s[end];
    if (lastSeen.has(ch)) {
      start = Math.max(start, lastSeen.get(ch) + 1);
    }
    lastSeen.set(ch, end);
    best = Math.max(best, end - start + 1);
  }
  return best;
}

// Demo
console.log(lengthOfLongestSubstring('abcabcbb')); // 3
console.log(lengthOfLongestSubstring('bbbbb')); // 1
```

#### Python (runnable)

```python
def length_of_longest_substring(s: str) -> int:
    last_seen = {}
    start = 0
    best = 0

    for end, ch in enumerate(s):
        if ch in last_seen:
            start = max(start, last_seen[ch] + 1)
        last_seen[ch] = end
        best = max(best, end - start + 1)
    return best


if __name__ == "__main__":
    print(length_of_longest_substring("abcabcbb"))
    print(length_of_longest_substring("bbbbb"))
```

---

## 4) Merge Intervals Pattern

### What is the Merge Intervals Pattern?

The **Merge Intervals** pattern is used when you have a list of intervals and need to **merge overlapping** ones, or detect/compute overlaps.

Typical approach:

1. **Sort intervals** by start time
2. Traverse and merge when `current.start <= last.end`

### Where is this pattern useful?

1. Merging overlapping ranges
2. Insert interval into existing intervals
3. Meeting room scheduling
4. Finding intersections of interval lists
5. Reducing interval sets to non-overlapping minimal representation

### How to Recognize When to Apply Merge Intervals

| Signal                  | Description                                              |
| ----------------------- | -------------------------------------------------------- |
| “Overlapping intervals” | Clear indicator you need merging or intersection.        |
| Output must be disjoint | “Mutually exclusive intervals” / non-overlapping result. |
| Range scheduling        | Meetings, CPU load, resource usage time ranges.          |
| Interval insertion      | Add an interval and merge accordingly.                   |

### How to Apply the Pattern — Step-by-Step

**Step 1: Sort by start**

**Step 2: Start result with first interval**

**Step 3: For each next interval**

- if overlap: merge by `end = max(end, next.end)`
- else: push as a new interval

### Diagram (overlap)

```text
[1,4] overlaps [3,6]

1---4
    3---6
=> 1-----6
```

### Benefits of Merge Intervals

| Benefit              | Explanation                            |
| -------------------- | -------------------------------------- |
| O(n log n) total     | Sort dominates; merge pass is linear.  |
| Simple merging rule  | Compare starts to previous merged end. |
| Works for many tasks | Merge, insert, intersection variants.  |

### Example Use Case Patterns

| Use Case      | Question Type      | What to Observe                |
| ------------- | ------------------ | ------------------------------ |
| Merge         | Interval list      | Sort + merge overlaps          |
| Insert        | Interval list      | Merge around inserted interval |
| Intersections | Two interval lists | Two pointers + overlap math    |

---

### Problem A: Merge Intervals

LeetCode: https://leetcode.com/problems/merge-intervals/

#### JavaScript (Node.js runnable)

```javascript
function merge(intervals) {
  intervals.sort((a, b) => a[0] - b[0]);
  const merged = [];

  for (const [start, end] of intervals) {
    if (merged.length === 0 || merged[merged.length - 1][1] < start) {
      merged.push([start, end]);
    } else {
      merged[merged.length - 1][1] = Math.max(
        merged[merged.length - 1][1],
        end,
      );
    }
  }

  return merged;
}

// Demo
console.log(
  merge([
    [1, 3],
    [2, 6],
    [8, 10],
    [15, 18],
  ]),
);
```

#### Python (runnable)

```python
def merge(intervals):
    intervals.sort(key=lambda x: x[0])
    merged = []
    for start, end in intervals:
        if not merged or merged[-1][1] < start:
            merged.append([start, end])
        else:
            merged[-1][1] = max(merged[-1][1], end)
    return merged


if __name__ == "__main__":
    print(merge([[1,3],[2,6],[8,10],[15,18]]))
```

---

### Problem B: Insert Interval

LeetCode: https://leetcode.com/problems/insert-interval/

#### JavaScript (Node.js runnable)

```javascript
function insert(intervals, newInterval) {
  const res = [];
  let i = 0;
  let [start, end] = newInterval;

  // add all intervals ending before new interval starts
  while (i < intervals.length && intervals[i][1] < start) {
    res.push(intervals[i++]);
  }

  // merge all overlapping
  while (i < intervals.length && intervals[i][0] <= end) {
    start = Math.min(start, intervals[i][0]);
    end = Math.max(end, intervals[i][1]);
    i++;
  }
  res.push([start, end]);

  // add rest
  while (i < intervals.length) res.push(intervals[i++]);
  return res;
}

// Demo
console.log(
  insert(
    [
      [1, 3],
      [6, 9],
    ],
    [2, 5],
  ),
); // [[1,5],[6,9]]
```

#### Python (runnable)

```python
def insert(intervals, new_interval):
    res = []
    i = 0
    start, end = new_interval

    while i < len(intervals) and intervals[i][1] < start:
        res.append(intervals[i])
        i += 1

    while i < len(intervals) and intervals[i][0] <= end:
        start = min(start, intervals[i][0])
        end = max(end, intervals[i][1])
        i += 1

    res.append([start, end])

    while i < len(intervals):
        res.append(intervals[i])
        i += 1

    return res


if __name__ == "__main__":
    print(insert([[1,3],[6,9]],[2,5]))
```

---

## 5) Two Heaps Pattern

### What is the Two Heaps Pattern?

The **Two Heaps** pattern maintains:

- a **Max-Heap** for the smaller half of numbers
- a **Min-Heap** for the larger half of numbers

This lets you access:

- the largest element of the smaller half
- the smallest element of the larger half

Often used for dynamic medians.

### Where is this pattern useful?

1. Median of a stream
2. Sliding window median
3. Splitting data into two balanced halves
4. Scheduling / selecting K items with priorities
5. Problems needing both min and max access efficiently

### How to Recognize When to Apply Two Heaps

| Signal                | Description                                                  |
| --------------------- | ------------------------------------------------------------ |
| Need median online    | “stream”, “continuous insertion”, “median after each insert” |
| Need both sides       | smallest of large-half and largest of small-half             |
| Rebalance requirement | halves should stay almost equal in size                      |
| Priority behavior     | heap / priority queue is natural fit                         |

### How to Apply the Pattern — Step-by-Step

**Step 1: Insert into one heap**

- if number <= maxHeapTop → maxHeap
- else → minHeap

**Step 2: Rebalance**

- sizes differ by at most 1

**Step 3: Compute answer**

- if sizes equal: average of tops
- else: top of bigger heap

### Diagram

```text
maxHeap (smaller half)    minHeap (larger half)
      [ ... ]                  [ ... ]
       top=max                   top=min
```

### Benefits of Two Heaps

| Benefit          | Explanation                  |
| ---------------- | ---------------------------- |
| Efficient median | O(log n) insert, O(1) median |
| Handles streams  | Works incrementally          |
| Balanced halves  | Rebalancing keeps invariants |

### Example Use Case Patterns

| Use Case       | Question Type | What to Observe                  |
| -------------- | ------------- | -------------------------------- |
| Median stream  | Stream        | keep halves balanced             |
| Sliding median | Array window  | lazy deletion / multiset / heaps |

---

### Problem A: Find Median from Data Stream

LeetCode: https://leetcode.com/problems/find-median-from-data-stream/

#### JavaScript (Node.js runnable)

```javascript
class Heap {
  constructor(compare) {
    this.data = [];
    this.compare = compare; // returns true if a should be above b
  }
  size() {
    return this.data.length;
  }
  peek() {
    return this.data[0];
  }
  push(x) {
    this.data.push(x);
    this._siftUp(this.data.length - 1);
  }
  pop() {
    const top = this.data[0];
    const last = this.data.pop();
    if (this.data.length > 0) {
      this.data[0] = last;
      this._siftDown(0);
    }
    return top;
  }
  _siftUp(i) {
    while (i > 0) {
      const p = (i - 1) >> 1;
      if (this.compare(this.data[p], this.data[i])) break;
      [this.data[p], this.data[i]] = [this.data[i], this.data[p]];
      i = p;
    }
  }
  _siftDown(i) {
    const n = this.data.length;
    while (true) {
      let best = i;
      const l = i * 2 + 1;
      const r = i * 2 + 2;
      if (l < n && !this.compare(this.data[best], this.data[l])) best = l;
      if (r < n && !this.compare(this.data[best], this.data[r])) best = r;
      if (best === i) break;
      [this.data[i], this.data[best]] = [this.data[best], this.data[i]];
      i = best;
    }
  }
}

class MedianFinder {
  constructor() {
    this.maxHeap = new Heap((a, b) => a >= b); // max-heap
    this.minHeap = new Heap((a, b) => a <= b); // min-heap
  }

  addNum(num) {
    if (this.maxHeap.size() === 0 || num <= this.maxHeap.peek())
      this.maxHeap.push(num);
    else this.minHeap.push(num);

    // rebalance
    if (this.maxHeap.size() > this.minHeap.size() + 1)
      this.minHeap.push(this.maxHeap.pop());
    if (this.minHeap.size() > this.maxHeap.size())
      this.maxHeap.push(this.minHeap.pop());
  }

  findMedian() {
    if (this.maxHeap.size() === this.minHeap.size()) {
      return (this.maxHeap.peek() + this.minHeap.peek()) / 2;
    }
    return this.maxHeap.peek();
  }
}

// Demo
const mf = new MedianFinder();
mf.addNum(1);
mf.addNum(2);
console.log(mf.findMedian()); // 1.5
mf.addNum(3);
console.log(mf.findMedian()); // 2
```

#### Python (runnable)

```python
import heapq


class MedianFinder:
    def __init__(self):
        self.small = []  # max-heap via negatives
        self.large = []  # min-heap

    def addNum(self, num: int) -> None:
        if not self.small or num <= -self.small[0]:
            heapq.heappush(self.small, -num)
        else:
            heapq.heappush(self.large, num)

        # rebalance
        if len(self.small) > len(self.large) + 1:
            heapq.heappush(self.large, -heapq.heappop(self.small))
        if len(self.large) > len(self.small):
            heapq.heappush(self.small, -heapq.heappop(self.large))

    def findMedian(self) -> float:
        if len(self.small) == len(self.large):
            return (-self.small[0] + self.large[0]) / 2
        return float(-self.small[0])


if __name__ == "__main__":
    mf = MedianFinder()
    mf.addNum(1)
    mf.addNum(2)
    print(mf.findMedian())
    mf.addNum(3)
    print(mf.findMedian())
```

---

### Problem B: Sliding Window Median

LeetCode: https://leetcode.com/problems/sliding-window-median/

> Note: The full optimal solution uses **two heaps + lazy deletion**.

#### JavaScript (Node.js runnable)

```javascript
// Two heaps + lazy deletion
class Heap {
  constructor(compare) {
    this.data = [];
    this.compare = compare;
  }
  size() {
    return this.data.length;
  }
  peek() {
    return this.data[0];
  }
  push(x) {
    this.data.push(x);
    this._up(this.data.length - 1);
  }
  pop() {
    const top = this.data[0];
    const last = this.data.pop();
    if (this.data.length) {
      this.data[0] = last;
      this._down(0);
    }
    return top;
  }
  _up(i) {
    while (i > 0) {
      const p = (i - 1) >> 1;
      if (this.compare(this.data[p], this.data[i])) break;
      [this.data[p], this.data[i]] = [this.data[i], this.data[p]];
      i = p;
    }
  }
  _down(i) {
    const n = this.data.length;
    while (true) {
      let best = i;
      const l = i * 2 + 1,
        r = i * 2 + 2;
      if (l < n && !this.compare(this.data[best], this.data[l])) best = l;
      if (r < n && !this.compare(this.data[best], this.data[r])) best = r;
      if (best === i) break;
      [this.data[i], this.data[best]] = [this.data[best], this.data[i]];
      i = best;
    }
  }
}

function medianSlidingWindow(nums, k) {
  const small = new Heap((a, b) => a[0] >= b[0]); // max heap by value
  const large = new Heap((a, b) => a[0] <= b[0]); // min heap by value
  const delayed = new Map();
  let smallSize = 0;
  let largeSize = 0;

  const addDelayed = (key) => delayed.set(key, (delayed.get(key) || 0) + 1);

  const prune = (heap) => {
    while (heap.size()) {
      const [val, idx] = heap.peek();
      const key = val + '#' + idx;
      const cnt = delayed.get(key) || 0;
      if (cnt === 0) break;
      heap.pop();
      if (cnt === 1) delayed.delete(key);
      else delayed.set(key, cnt - 1);
    }
  };

  const rebalance = () => {
    if (smallSize > largeSize + 1) {
      large.push(small.pop());
      smallSize--;
      largeSize++;
      prune(small);
    } else if (smallSize < largeSize) {
      small.push(large.pop());
      largeSize--;
      smallSize++;
      prune(large);
    }
  };

  const insert = (val, idx) => {
    if (small.size() === 0 || val <= small.peek()[0]) {
      small.push([val, idx]);
      smallSize++;
    } else {
      large.push([val, idx]);
      largeSize++;
    }
    rebalance();
  };

  const erase = (val, idx) => {
    const key = val + '#' + idx;
    addDelayed(key);
    if (val <= small.peek()[0]) {
      smallSize--;
      if (val === small.peek()[0] && idx === small.peek()[1]) prune(small);
    } else {
      largeSize--;
      if (large.size() && val === large.peek()[0] && idx === large.peek()[1])
        prune(large);
    }
    rebalance();
  };

  const getMedian = () => {
    if (k % 2 === 1) return small.peek()[0];
    return (small.peek()[0] + large.peek()[0]) / 2;
  };

  const ans = [];
  for (let i = 0; i < nums.length; i++) {
    insert(nums[i], i);
    if (i >= k - 1) {
      prune(small);
      prune(large);
      ans.push(getMedian());
      const outIdx = i - (k - 1);
      erase(nums[outIdx], outIdx);
    }
  }
  return ans;
}

// Demo
console.log(medianSlidingWindow([1, 3, -1, -3, 5, 3, 6, 7], 3)); // [1,-1,-1,3,5,6]
```

#### Python (runnable)

```python
import heapq
from collections import defaultdict


def median_sliding_window(nums, k):
    small = []  # max-heap via negatives: (-val, idx)
    large = []  # min-heap: (val, idx)
    delayed = defaultdict(int)
    small_size = 0
    large_size = 0

    def prune(heap):
        while heap:
            val, idx = heap[0]
            key = (val, idx)
            if delayed[key] == 0:
                break
            delayed[key] -= 1
            if delayed[key] == 0:
                del delayed[key]
            heapq.heappop(heap)

    def rebalance():
        nonlocal small_size, large_size
        if small_size > large_size + 1:
            val, idx = heapq.heappop(small)
            heapq.heappush(large, (-val, idx))
            small_size -= 1
            large_size += 1
            prune(small)
        elif small_size < large_size:
            val, idx = heapq.heappop(large)
            heapq.heappush(small, (-val, idx))
            large_size -= 1
            small_size += 1
            prune(large)

    def add(val, idx):
        nonlocal small_size, large_size
        if not small or val <= -small[0][0]:
            heapq.heappush(small, (-val, idx))
            small_size += 1
        else:
            heapq.heappush(large, (val, idx))
            large_size += 1
        rebalance()

    def remove(val, idx):
        nonlocal small_size, large_size
        key_small = (-val, idx)
        key_large = (val, idx)
        # mark delayed with the representation that will appear on top
        if small and val <= -small[0][0]:
            small_size -= 1
            delayed[key_small] += 1
            if small and (-val, idx) == small[0]:
                prune(small)
        else:
            large_size -= 1
            delayed[key_large] += 1
            if large and (val, idx) == large[0]:
                prune(large)
        rebalance()

    def median():
        if k % 2 == 1:
            return float(-small[0][0])
        return (-small[0][0] + large[0][0]) / 2

    res = []
    for i, v in enumerate(nums):
        add(v, i)
        if i >= k - 1:
            prune(small)
            prune(large)
            res.append(median())
            out = i - (k - 1)
            remove(nums[out], out)
    return res


if __name__ == "__main__":
    print(median_sliding_window([1,3,-1,-3,5,3,6,7], 3))
```

---

## 6) K-Way Merge Pattern

### What is the K-Way Merge Pattern?

The **K-Way Merge** pattern is used when you are given **K sorted lists/arrays** and need to merge or traverse them in sorted order.

Key idea:

- Push the first element of each list into a **min-heap**.
- Repeatedly extract the smallest and push the next element from that same list.

### Where is this pattern useful?

1. Merge K sorted lists
2. Kth smallest across sorted lists
3. Smallest range covering K lists
4. Sorted traversal of a matrix (rows sorted)
5. Any “merge multiple sorted sources” scenario

### How to Recognize When to Apply K-Way Merge

| Signal                 | Description                              |
| ---------------------- | ---------------------------------------- |
| Multiple sorted inputs | K sorted arrays/lists/matrix rows        |
| Need global order      | merge into one sorted output             |
| Kth smallest           | across multiple sorted sources           |
| Heap is natural        | always need current smallest among heads |

### How to Apply the Pattern — Step-by-Step

**Step 1:** push head of each list `(value, listIndex, elementIndex)` into min-heap

**Step 2:** pop smallest, append to result

**Step 3:** push next element from the same list

**Step 4:** repeat until heap empty

### Diagram

```text
List1: 1 4 7
List2: 2 5 8
List3: 3 6 9

Heap initially: (1,L1) (2,L2) (3,L3)
Pop smallest each time and push next from that list.
```

### Benefits of K-Way Merge

| Benefit       | Explanation                        |
| ------------- | ---------------------------------- |
| Efficient     | O(N log K) vs sorting all together |
| Scales with K | heap keeps only K heads            |
| General       | works for lists/arrays/matrix rows |

### Example Use Case Patterns

| Use Case       | Question Type   | What to Observe              |
| -------------- | --------------- | ---------------------------- |
| Merge          | K sorted lists  | always pop min head          |
| Kth smallest   | K sorted arrays | count pops until k           |
| Smallest range | K lists         | track current max + min head |

---

### Problem A: Merge k Sorted Lists

LeetCode: https://leetcode.com/problems/merge-k-sorted-lists/

#### JavaScript (Node.js runnable)

```javascript
class ListNode {
  constructor(val) {
    this.val = val;
    this.next = null;
  }
}

class MinHeap {
  constructor() {
    this.data = [];
  }
  size() {
    return this.data.length;
  }
  peek() {
    return this.data[0];
  }
  push(x) {
    this.data.push(x);
    this._up(this.data.length - 1);
  }
  pop() {
    const top = this.data[0];
    const last = this.data.pop();
    if (this.data.length) {
      this.data[0] = last;
      this._down(0);
    }
    return top;
  }
  _up(i) {
    while (i > 0) {
      const p = (i - 1) >> 1;
      if (this.data[p].val <= this.data[i].val) break;
      [this.data[p], this.data[i]] = [this.data[i], this.data[p]];
      i = p;
    }
  }
  _down(i) {
    const n = this.data.length;
    while (true) {
      let best = i;
      const l = i * 2 + 1,
        r = i * 2 + 2;
      if (l < n && this.data[l].val < this.data[best].val) best = l;
      if (r < n && this.data[r].val < this.data[best].val) best = r;
      if (best === i) break;
      [this.data[i], this.data[best]] = [this.data[best], this.data[i]];
      i = best;
    }
  }
}

function mergeKLists(lists) {
  const heap = new MinHeap();
  for (const node of lists) if (node) heap.push(node);

  const dummy = new ListNode(0);
  let tail = dummy;

  while (heap.size()) {
    const node = heap.pop();
    tail.next = node;
    tail = tail.next;
    if (node.next) heap.push(node.next);
  }
  tail.next = null;
  return dummy.next;
}

// Demo helpers
function buildList(arr) {
  const dummy = new ListNode(0);
  let t = dummy;
  for (const v of arr) {
    t.next = new ListNode(v);
    t = t.next;
  }
  return dummy.next;
}
function listToArray(head) {
  const out = [];
  while (head) {
    out.push(head.val);
    head = head.next;
  }
  return out;
}

const l1 = buildList([1, 4, 5]);
const l2 = buildList([1, 3, 4]);
const l3 = buildList([2, 6]);
console.log(listToArray(mergeKLists([l1, l2, l3]))); // [1,1,2,3,4,4,5,6]
```

#### Python (runnable)

```python
import heapq


class ListNode:
    def __init__(self, val=0, next=None):
        self.val = val
        self.next = next


def merge_k_lists(lists):
    heap = []
    counter = 0
    for node in lists:
        if node:
            heapq.heappush(heap, (node.val, counter, node))
            counter += 1

    dummy = ListNode(0)
    tail = dummy

    while heap:
        _, _, node = heapq.heappop(heap)
        tail.next = node
        tail = tail.next
        if node.next:
            heapq.heappush(heap, (node.next.val, counter, node.next))
            counter += 1

    tail.next = None
    return dummy.next


def build_list(arr):
    dummy = ListNode(0)
    t = dummy
    for v in arr:
        t.next = ListNode(v)
        t = t.next
    return dummy.next


def to_array(head):
    out = []
    while head:
        out.append(head.val)
        head = head.next
    return out


if __name__ == "__main__":
    l1 = build_list([1, 4, 5])
    l2 = build_list([1, 3, 4])
    l3 = build_list([2, 6])
    merged = merge_k_lists([l1, l2, l3])
    print(to_array(merged))
```

---

### Problem B: Kth Smallest Element in a Sorted Matrix

LeetCode: https://leetcode.com/problems/kth-smallest-element-in-a-sorted-matrix/

#### JavaScript (Node.js runnable)

```javascript
class MinHeap {
  constructor() {
    this.data = [];
  }
  size() {
    return this.data.length;
  }
  push(x) {
    this.data.push(x);
    this._up(this.data.length - 1);
  }
  pop() {
    const top = this.data[0];
    const last = this.data.pop();
    if (this.data.length) {
      this.data[0] = last;
      this._down(0);
    }
    return top;
  }
  _up(i) {
    while (i > 0) {
      const p = (i - 1) >> 1;
      if (this.data[p][0] <= this.data[i][0]) break;
      [this.data[p], this.data[i]] = [this.data[i], this.data[p]];
      i = p;
    }
  }
  _down(i) {
    const n = this.data.length;
    while (true) {
      let best = i;
      const l = i * 2 + 1,
        r = i * 2 + 2;
      if (l < n && this.data[l][0] < this.data[best][0]) best = l;
      if (r < n && this.data[r][0] < this.data[best][0]) best = r;
      if (best === i) break;
      [this.data[i], this.data[best]] = [this.data[best], this.data[i]];
      i = best;
    }
  }
}

function kthSmallest(matrix, k) {
  const n = matrix.length;
  const heap = new MinHeap();
  for (let r = 0; r < n; r++) {
    heap.push([matrix[r][0], r, 0]);
  }
  let val = null;
  for (let i = 0; i < k; i++) {
    const [v, r, c] = heap.pop();
    val = v;
    if (c + 1 < matrix[r].length) heap.push([matrix[r][c + 1], r, c + 1]);
  }
  return val;
}

// Demo
console.log(
  kthSmallest(
    [
      [1, 5, 9],
      [10, 11, 13],
      [12, 13, 15],
    ],
    8,
  ),
); // 13
```

#### Python (runnable)

```python
import heapq


def kth_smallest(matrix, k):
    n = len(matrix)
    heap = []
    for r in range(n):
        heapq.heappush(heap, (matrix[r][0], r, 0))

    val = None
    for _ in range(k):
        val, r, c = heapq.heappop(heap)
        if c + 1 < len(matrix[r]):
            heapq.heappush(heap, (matrix[r][c + 1], r, c + 1))
    return val


if __name__ == "__main__":
    print(kth_smallest([[1,5,9],[10,11,13],[12,13,15]], 8))
```

---

## 7) Top K Elements Pattern

### What is the Top K Elements Pattern?

The **Top K Elements** pattern is used when you need to find the **K largest**, **K smallest**, or **K most frequent** elements.

Most commonly, we use a **heap (priority queue)**:

- Use a **min-heap of size K** to keep track of K largest items
- Use a **max-heap** (or min-heap with negation) for K smallest
- For “most frequent”, combine **hash map (frequency)** + **heap**

### Where is this pattern useful?

1. Kth largest/smallest element
2. Top K frequent numbers/words
3. K closest points
4. Sorting or selecting under constraints
5. Streaming scenarios where you can’t store everything sorted

### How to Recognize When to Apply Top K Elements

| Signal                 | Description                                                  |
| ---------------------- | ------------------------------------------------------------ |
| “Top K” / “Kth”        | You must select K elements, not fully sort all.              |
| Large input            | Sorting O(n log n) might be too slow; heap gives O(n log k). |
| Frequency-based        | Need most frequent elements (hash map + heap).               |
| Maintain running top K | Stream of values.                                            |

### How to Apply the Pattern — Step-by-Step

**Step 1:** Decide heap type (min-heap for top K largest)

**Step 2:** Push elements, keep heap size <= K

**Step 3:** Heap now contains the answer set (or root is kth element)

### Diagram

```text
Keep a min-heap of size K

Insert x
If heap.size > K -> pop smallest

Remaining K items = top K largest
```

### Benefits of Top K Elements

| Benefit             | Explanation                                |
| ------------------- | ------------------------------------------ |
| Efficient           | O(n log k) vs O(n log n) sorting           |
| Works for streaming | Maintain heap incrementally                |
| Flexible            | Applies to kth, frequent, closest problems |

### Example Use Case Patterns

| Use Case       | Question Type | What to Observe              |
| -------------- | ------------- | ---------------------------- |
| Kth largest    | Array         | min-heap size K; root is kth |
| Top K frequent | Array/strings | frequency map + heap         |

---

### Problem A: Kth Largest Element in an Array

LeetCode: https://leetcode.com/problems/kth-largest-element-in-an-array/

#### JavaScript (Node.js runnable)

```javascript
class MinHeap {
  constructor() {
    this.data = [];
  }
  size() {
    return this.data.length;
  }
  peek() {
    return this.data[0];
  }
  push(x) {
    this.data.push(x);
    this._up(this.data.length - 1);
  }
  pop() {
    const top = this.data[0];
    const last = this.data.pop();
    if (this.data.length) {
      this.data[0] = last;
      this._down(0);
    }
    return top;
  }
  _up(i) {
    while (i > 0) {
      const p = (i - 1) >> 1;
      if (this.data[p] <= this.data[i]) break;
      [this.data[p], this.data[i]] = [this.data[i], this.data[p]];
      i = p;
    }
  }
  _down(i) {
    const n = this.data.length;
    while (true) {
      let best = i;
      const l = i * 2 + 1;
      const r = i * 2 + 2;
      if (l < n && this.data[l] < this.data[best]) best = l;
      if (r < n && this.data[r] < this.data[best]) best = r;
      if (best === i) break;
      [this.data[i], this.data[best]] = [this.data[best], this.data[i]];
      i = best;
    }
  }
}

function findKthLargest(nums, k) {
  const heap = new MinHeap();
  for (const x of nums) {
    heap.push(x);
    if (heap.size() > k) heap.pop();
  }
  return heap.peek();
}

// Demo
console.log(findKthLargest([3, 2, 1, 5, 6, 4], 2)); // 5
```

#### Python (runnable)

```python
import heapq


def find_kth_largest(nums, k):
    heap = []
    for x in nums:
        heapq.heappush(heap, x)
        if len(heap) > k:
            heapq.heappop(heap)
    return heap[0]


if __name__ == "__main__":
    print(find_kth_largest([3, 2, 1, 5, 6, 4], 2))
```

---

### Problem B: Top K Frequent Elements

LeetCode: https://leetcode.com/problems/top-k-frequent-elements/

#### JavaScript (Node.js runnable)

```javascript
function topKFrequent(nums, k) {
  const freq = new Map();
  for (const x of nums) freq.set(x, (freq.get(x) || 0) + 1);

  // min-heap by frequency
  class MinHeap {
    constructor() {
      this.data = [];
    }
    size() {
      return this.data.length;
    }
    peek() {
      return this.data[0];
    }
    push(x) {
      this.data.push(x);
      this._up(this.data.length - 1);
    }
    pop() {
      const top = this.data[0];
      const last = this.data.pop();
      if (this.data.length) {
        this.data[0] = last;
        this._down(0);
      }
      return top;
    }
    _up(i) {
      while (i > 0) {
        const p = (i - 1) >> 1;
        if (this.data[p][1] <= this.data[i][1]) break;
        [this.data[p], this.data[i]] = [this.data[i], this.data[p]];
        i = p;
      }
    }
    _down(i) {
      const n = this.data.length;
      while (true) {
        let best = i;
        const l = i * 2 + 1;
        const r = i * 2 + 2;
        if (l < n && this.data[l][1] < this.data[best][1]) best = l;
        if (r < n && this.data[r][1] < this.data[best][1]) best = r;
        if (best === i) break;
        [this.data[i], this.data[best]] = [this.data[best], this.data[i]];
        i = best;
      }
    }
  }

  const heap = new MinHeap();
  for (const [num, count] of freq.entries()) {
    heap.push([num, count]);
    if (heap.size() > k) heap.pop();
  }
  return heap.data.map((x) => x[0]);
}

// Demo
console.log(topKFrequent([1, 1, 1, 2, 2, 3], 2)); // [1,2]
```

#### Python (runnable)

```python
import heapq
from collections import Counter


def top_k_frequent(nums, k):
    freq = Counter(nums)
    heap = []
    for num, count in freq.items():
        heapq.heappush(heap, (count, num))
        if len(heap) > k:
            heapq.heappop(heap)
    return [num for _, num in heap]


if __name__ == "__main__":
    print(top_k_frequent([1, 1, 1, 2, 2, 3], 2))
```

---

## 8) Modified Binary Search Pattern

### What is the Modified Binary Search Pattern?

The **Modified Binary Search** pattern applies binary search ideas to arrays that are:

- rotated
- have duplicates
- are “nearly sorted”
- require finding a boundary/first/last occurrence

### Where is this pattern useful?

1. Search in rotated sorted arrays
2. First/last occurrence
3. Finding minimum in rotated array
4. Peak element / mountain array
5. Boundary problems (lower_bound / upper_bound)

### How to Recognize When to Apply Modified Binary Search

| Signal            | Description                                       |
| ----------------- | ------------------------------------------------- |
| Sorted structure  | array/matrix is sorted or partially sorted        |
| Rotated           | “rotated sorted array” wording                    |
| Boundary          | “first/last position”, “smallest index where ...” |
| Log time required | expects O(log n)                                  |

### How to Apply the Pattern — Step-by-Step

1. Choose `mid = left + (right-left)//2`
2. Determine which side is sorted (for rotations)
3. Narrow the search space based on condition

### Diagram

```text
left ..... mid ..... right
Each step discards half of the search space
```

### Benefits

| Benefit                    | Explanation                  |
| -------------------------- | ---------------------------- |
| O(log n)                   | discards half each iteration |
| Works beyond normal search | rotations/boundaries         |

---

### Problem A: Search in Rotated Sorted Array

LeetCode: https://leetcode.com/problems/search-in-rotated-sorted-array/

#### JavaScript (Node.js runnable)

```javascript
function search(nums, target) {
  let left = 0;
  let right = nums.length - 1;

  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2);
    if (nums[mid] === target) return mid;

    // left side sorted
    if (nums[left] <= nums[mid]) {
      if (nums[left] <= target && target < nums[mid]) right = mid - 1;
      else left = mid + 1;
    } else {
      // right side sorted
      if (nums[mid] < target && target <= nums[right]) left = mid + 1;
      else right = mid - 1;
    }
  }
  return -1;
}

// Demo
console.log(search([4, 5, 6, 7, 0, 1, 2], 0)); // 4
```

#### Python (runnable)

```python
def search(nums, target):
    left, right = 0, len(nums) - 1
    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] == target:
            return mid

        if nums[left] <= nums[mid]:
            if nums[left] <= target < nums[mid]:
                right = mid - 1
            else:
                left = mid + 1
        else:
            if nums[mid] < target <= nums[right]:
                left = mid + 1
            else:
                right = mid - 1
    return -1


if __name__ == "__main__":
    print(search([4, 5, 6, 7, 0, 1, 2], 0))
```

---

### Problem B: Find First and Last Position of Element in Sorted Array

LeetCode: https://leetcode.com/problems/find-first-and-last-position-of-element-in-sorted-array/

#### JavaScript (Node.js runnable)

```javascript
function searchRange(nums, target) {
  const first = bound(nums, target, true);
  if (first === -1) return [-1, -1];
  const last = bound(nums, target, false);
  return [first, last];
}

function bound(nums, target, isFirst) {
  let left = 0;
  let right = nums.length - 1;
  let ans = -1;
  while (left <= right) {
    const mid = left + Math.floor((right - left) / 2);
    if (nums[mid] === target) {
      ans = mid;
      if (isFirst) right = mid - 1;
      else left = mid + 1;
    } else if (nums[mid] < target) {
      left = mid + 1;
    } else {
      right = mid - 1;
    }
  }
  return ans;
}

// Demo
console.log(searchRange([5, 7, 7, 8, 8, 10], 8)); // [3,4]
```

#### Python (runnable)

```python
def search_range(nums, target):
    first = bound(nums, target, True)
    if first == -1:
        return [-1, -1]
    last = bound(nums, target, False)
    return [first, last]


def bound(nums, target, is_first):
    left, right = 0, len(nums) - 1
    ans = -1
    while left <= right:
        mid = left + (right - left) // 2
        if nums[mid] == target:
            ans = mid
            if is_first:
                right = mid - 1
            else:
                left = mid + 1
        elif nums[mid] < target:
            left = mid + 1
        else:
            right = mid - 1
    return ans


if __name__ == "__main__":
    print(search_range([5, 7, 7, 8, 8, 10], 8))
```

---

## 9) Subsets Pattern

### What is the Subsets Pattern?

The **Subsets** pattern generates **all combinations** (and sometimes permutations) of a given set.

Two common ways:

- **Iterative BFS-style building**: start with `[[]]` and add each number to existing subsets
- **Backtracking / DFS**: include/exclude decisions

### Where is this pattern useful?

1. Generate all subsets
2. Subsets with duplicates
3. Permutations
4. Combinations
5. Decision-tree enumeration problems

### How to Recognize When to Apply Subsets

| Signal                      | Description           |
| --------------------------- | --------------------- |
| “all subsets” / “power set” | direct mapping        |
| combinations/permutations   | enumeration           |
| small n (<= 20 typically)   | output is exponential |

### How to Apply the Pattern — Step-by-Step

Iterative:

1. Start with `subsets=[[]]`
2. For each `num`, clone existing subsets and add `num`

### Diagram

```text
Start: [[]]
Add 1 -> [[], [1]]
Add 2 -> [[], [1], [2], [1,2]]
...
```

### Benefits

| Benefit  | Explanation                      |
| -------- | -------------------------------- |
| Simple   | iterative approach is easy       |
| Flexible | adapt to duplicates/permutations |

---

### Problem A: Subsets

LeetCode: https://leetcode.com/problems/subsets/

#### JavaScript (Node.js runnable)

```javascript
function subsets(nums) {
  const res = [[]];
  for (const num of nums) {
    const size = res.length;
    for (let i = 0; i < size; i++) {
      res.push([...res[i], num]);
    }
  }
  return res;
}

// Demo
console.log(subsets([1, 2, 3]));
```

#### Python (runnable)

```python
def subsets(nums):
    res = [[]]
    for num in nums:
        for i in range(len(res)):
            res.append(res[i] + [num])
    return res


if __name__ == "__main__":
    print(subsets([1, 2, 3]))
```

---

### Problem B: Permutations

LeetCode: https://leetcode.com/problems/permutations/

#### JavaScript (Node.js runnable)

```javascript
function permute(nums) {
  const res = [];
  const used = new Array(nums.length).fill(false);

  function backtrack(path) {
    if (path.length === nums.length) {
      res.push([...path]);
      return;
    }
    for (let i = 0; i < nums.length; i++) {
      if (used[i]) continue;
      used[i] = true;
      path.push(nums[i]);
      backtrack(path);
      path.pop();
      used[i] = false;
    }
  }

  backtrack([]);
  return res;
}

// Demo
console.log(permute([1, 2, 3]));
```

#### Python (runnable)

```python
def permute(nums):
    res = []
    used = [False] * len(nums)

    def backtrack(path):
        if len(path) == len(nums):
            res.append(path[:])
            return
        for i in range(len(nums)):
            if used[i]:
                continue
            used[i] = True
            path.append(nums[i])
            backtrack(path)
            path.pop()
            used[i] = False

    backtrack([])
    return res


if __name__ == "__main__":
    print(permute([1, 2, 3]))
```

---

## 10) Greedy Techniques Pattern

### What is the Greedy Pattern?

The **Greedy** pattern builds a solution by making the **best local choice** at each step, hoping it leads to a global optimum.

### Where is this pattern useful?

1. Interval scheduling / selection
2. Jump game reachability
3. Minimizing operations with local decisions
4. Partitioning problems
5. When proof of optimal substructure exists

### How to Recognize When to Apply Greedy

| Signal                   | Description                            |
| ------------------------ | -------------------------------------- |
| “minimize” or “maximize” | optimize a metric                      |
| local choice seems safe  | e.g., take earliest finishing interval |
| proofs often exist       | exchange argument, invariant           |

### How to Apply — Step-by-Step

1. Identify the metric to optimize
2. Define local rule (e.g., farthest reach)
3. Maintain invariant and iterate

### Diagram (Jump Game reach)

```text
Keep track of farthest index reachable so far
```

---

### Problem A: Jump Game

LeetCode: https://leetcode.com/problems/jump-game/

#### JavaScript (Node.js runnable)

```javascript
function canJump(nums) {
  let farthest = 0;
  for (let i = 0; i < nums.length; i++) {
    if (i > farthest) return false;
    farthest = Math.max(farthest, i + nums[i]);
  }
  return true;
}

// Demo
console.log(canJump([2, 3, 1, 1, 4])); // true
console.log(canJump([3, 2, 1, 0, 4])); // false
```

#### Python (runnable)

```python
def can_jump(nums):
    farthest = 0
    for i, step in enumerate(nums):
        if i > farthest:
            return False
        farthest = max(farthest, i + step)
    return True


if __name__ == "__main__":
    print(can_jump([2, 3, 1, 1, 4]))
    print(can_jump([3, 2, 1, 0, 4]))
```

---

### Problem B: Partition Labels

LeetCode: https://leetcode.com/problems/partition-labels/

#### JavaScript (Node.js runnable)

```javascript
function partitionLabels(s) {
  const last = new Map();
  for (let i = 0; i < s.length; i++) last.set(s[i], i);

  const res = [];
  let start = 0;
  let end = 0;
  for (let i = 0; i < s.length; i++) {
    end = Math.max(end, last.get(s[i]));
    if (i === end) {
      res.push(end - start + 1);
      start = i + 1;
    }
  }
  return res;
}

// Demo
console.log(partitionLabels('ababcbacadefegdehijhklij')); // [9,7,8]
```

#### Python (runnable)

```python
def partition_labels(s: str):
    last = {ch: i for i, ch in enumerate(s)}
    res = []
    start = 0
    end = 0
    for i, ch in enumerate(s):
        end = max(end, last[ch])
        if i == end:
            res.append(end - start + 1)
            start = i + 1
    return res


if __name__ == "__main__":
    print(partition_labels('ababcbacadefegdehijhklij'))
```

---

## 11) Backtracking Pattern

### What is the Backtracking Pattern?

**Backtracking** explores all possible choices recursively, and **undoes** a choice when it leads to an invalid or completed state.

### Where is this pattern useful?

1. Combinations / subsets / permutations
2. Constraint satisfaction (N-Queens, Sudoku)
3. Path finding in grids
4. Partitioning strings
5. Decision tree enumeration

### How to Recognize When to Apply Backtracking

| Signal             | Description               |
| ------------------ | ------------------------- |
| Need all solutions | “return all”, “list all”  |
| constraints        | choices must follow rules |
| exponential search | DFS with pruning          |

### How to Apply — Step-by-Step

1. Choose a decision
2. Recurse
3. Undo decision (backtrack)

### Diagram

```text
Decision Tree
  choose A -> recurse -> undo
  choose B -> recurse -> undo
```

---

### Problem A: Combination Sum

LeetCode: https://leetcode.com/problems/combination-sum/

#### JavaScript (Node.js runnable)

```javascript
function combinationSum(candidates, target) {
  const res = [];
  candidates.sort((a, b) => a - b);

  function dfs(start, remaining, path) {
    if (remaining === 0) {
      res.push([...path]);
      return;
    }
    for (let i = start; i < candidates.length; i++) {
      const x = candidates[i];
      if (x > remaining) break;
      path.push(x);
      dfs(i, remaining - x, path);
      path.pop();
    }
  }

  dfs(0, target, []);
  return res;
}

// Demo
console.log(combinationSum([2, 3, 6, 7], 7)); // [[2,2,3],[7]]
```

#### Python (runnable)

```python
def combination_sum(candidates, target):
    candidates.sort()
    res = []

    def dfs(start, remaining, path):
        if remaining == 0:
            res.append(path[:])
            return
        for i in range(start, len(candidates)):
            x = candidates[i]
            if x > remaining:
                break
            path.append(x)
            dfs(i, remaining - x, path)
            path.pop()

    dfs(0, target, [])
    return res


if __name__ == "__main__":
    print(combination_sum([2, 3, 6, 7], 7))
```

---

### Problem B: Palindrome Partitioning

LeetCode: https://leetcode.com/problems/palindrome-partitioning/

#### JavaScript (Node.js runnable)

```javascript
function partition(s) {
  const res = [];

  function isPal(l, r) {
    while (l < r) {
      if (s[l++] !== s[r--]) return false;
    }
    return true;
  }

  function dfs(start, path) {
    if (start === s.length) {
      res.push([...path]);
      return;
    }
    for (let end = start; end < s.length; end++) {
      if (!isPal(start, end)) continue;
      path.push(s.slice(start, end + 1));
      dfs(end + 1, path);
      path.pop();
    }
  }

  dfs(0, []);
  return res;
}

// Demo
console.log(partition('aab')); // [['a','a','b'],['aa','b']]
```

#### Python (runnable)

```python
def partition(s: str):
    res = []

    def is_pal(l, r):
        while l < r:
            if s[l] != s[r]:
                return False
            l += 1
            r -= 1
        return True

    def dfs(start, path):
        if start == len(s):
            res.append(path[:])
            return
        for end in range(start, len(s)):
            if not is_pal(start, end):
                continue
            path.append(s[start : end + 1])
            dfs(end + 1, path)
            path.pop()

    dfs(0, [])
    return res


if __name__ == "__main__":
    print(partition('aab'))
```

---

## 12) Dynamic Programming Pattern

### What is the Dynamic Programming (DP) Pattern?

**Dynamic Programming** solves problems by breaking them into overlapping subproblems and storing results.

Two main styles:

- **Top-down (memoization)**
- **Bottom-up (tabulation)**

### Where is this pattern useful?

1. Optimization (min/max)
2. Counting ways
3. Sequences (LIS)
4. Knapsack-like problems
5. Grid paths

### How to Recognize When to Apply DP

| Signal                      | Description                 |
| --------------------------- | --------------------------- |
| overlapping subproblems     | repeated states             |
| optimal substructure        | answer from smaller answers |
| constraints suggest O(n\*m) | DP table possible           |

### How to Apply — Step-by-Step

1. Define state `dp[i]` or `dp[i][j]`
2. Write transition
3. Initialize base cases
4. Compute in correct order

### Diagram

```text
dp[i] depends on dp[i-1], dp[i-2] ...
```

---

### Problem A: Climbing Stairs

LeetCode: https://leetcode.com/problems/climbing-stairs/

#### JavaScript (Node.js runnable)

```javascript
function climbStairs(n) {
  if (n <= 2) return n;
  let a = 1;
  let b = 2;
  for (let i = 3; i <= n; i++) {
    const c = a + b;
    a = b;
    b = c;
  }
  return b;
}

// Demo
console.log(climbStairs(5)); // 8
```

#### Python (runnable)

```python
def climb_stairs(n: int) -> int:
    if n <= 2:
        return n
    a, b = 1, 2
    for _ in range(3, n + 1):
        a, b = b, a + b
    return b


if __name__ == "__main__":
    print(climb_stairs(5))
```

---

### Problem B: Coin Change

LeetCode: https://leetcode.com/problems/coin-change/

#### JavaScript (Node.js runnable)

```javascript
function coinChange(coins, amount) {
  const dp = new Array(amount + 1).fill(Infinity);
  dp[0] = 0;
  for (const coin of coins) {
    for (let a = coin; a <= amount; a++) {
      dp[a] = Math.min(dp[a], dp[a - coin] + 1);
    }
  }
  return dp[amount] === Infinity ? -1 : dp[amount];
}

// Demo
console.log(coinChange([1, 2, 5], 11)); // 3
```

#### Python (runnable)

```python
def coin_change(coins, amount):
    dp = [10**9] * (amount + 1)
    dp[0] = 0
    for coin in coins:
        for a in range(coin, amount + 1):
            dp[a] = min(dp[a], dp[a - coin] + 1)
    return -1 if dp[amount] >= 10**9 else dp[amount]


if __name__ == "__main__":
    print(coin_change([1, 2, 5], 11))
```

---

## 13) Cyclic Sort Pattern

### What is the Cyclic Sort Pattern?

The **Cyclic Sort** pattern is used for arrays containing numbers in a known range (commonly `1..n` or `0..n`).
The key idea is to place each number at its **correct index** by swapping until the current index has the correct value.

### Where is this pattern useful?

1. Finding missing numbers
2. Finding duplicates
3. Smallest missing positive
4. Corrupt pair (duplicate + missing)
5. In-place rearrangement with O(1) extra space

### How to Recognize When to Apply Cyclic Sort

| Signal                 | Description      |
| ---------------------- | ---------------- |
| Values in a range      | e.g., 1..n, 0..n |
| Need missing/duplicate | common variants  |
| In-place required      | O(1) extra space |

### How to Apply — Step-by-Step

For range `1..n`:

1. Iterate `i` from 0..n-1
2. Correct index for value `v` is `v-1`
3. If `nums[i] != nums[nums[i]-1]`, swap
4. Else `i++`

### Diagram

```text
nums = [3, 1, 2]
i=0 -> value 3 should be at index 2 -> swap
```

### Benefits

| Benefit    | Explanation                           |
| ---------- | ------------------------------------- |
| O(n) time  | each element swapped to correct place |
| O(1) space | in-place swaps                        |

---

### Problem A: Find All Numbers Disappeared in an Array

LeetCode: https://leetcode.com/problems/find-all-numbers-disappeared-in-an-array/

#### JavaScript (Node.js runnable)

```javascript
function findDisappearedNumbers(nums) {
  // cyclic sort style
  let i = 0;
  while (i < nums.length) {
    const correctIdx = nums[i] - 1;
    if (nums[i] !== nums[correctIdx]) {
      [nums[i], nums[correctIdx]] = [nums[correctIdx], nums[i]];
    } else {
      i++;
    }
  }

  const missing = [];
  for (let idx = 0; idx < nums.length; idx++) {
    if (nums[idx] !== idx + 1) missing.push(idx + 1);
  }
  return missing;
}

// Demo
console.log(findDisappearedNumbers([4, 3, 2, 7, 8, 2, 3, 1])); // [5,6]
```

#### Python (runnable)

```python
def find_disappeared_numbers(nums):
    i = 0
    while i < len(nums):
        correct = nums[i] - 1
        if nums[i] != nums[correct]:
            nums[i], nums[correct] = nums[correct], nums[i]
        else:
            i += 1

    missing = []
    for idx, v in enumerate(nums):
        if v != idx + 1:
            missing.append(idx + 1)
    return missing


if __name__ == "__main__":
    print(find_disappeared_numbers([4, 3, 2, 7, 8, 2, 3, 1]))
```

---

### Problem B: Find the Duplicate Number

LeetCode: https://leetcode.com/problems/find-the-duplicate-number/

> Note: This can be solved via Fast/Slow pointers too. Here we show a cyclic-placement approach.

#### JavaScript (Node.js runnable)

```javascript
function findDuplicate(nums) {
  let i = 0;
  while (i < nums.length) {
    const correctIdx = nums[i] - 1;
    if (nums[i] !== nums[correctIdx]) {
      [nums[i], nums[correctIdx]] = [nums[correctIdx], nums[i]];
    } else {
      if (i !== correctIdx) return nums[i];
      i++;
    }
  }
  return -1;
}

// Demo
console.log(findDuplicate([1, 3, 4, 2, 2])); // 2
```

#### Python (runnable)

```python
def find_duplicate(nums):
    i = 0
    while i < len(nums):
        correct = nums[i] - 1
        if nums[i] != nums[correct]:
            nums[i], nums[correct] = nums[correct], nums[i]
        else:
            if i != correct:
                return nums[i]
            i += 1
    return -1


if __name__ == "__main__":
    print(find_duplicate([1, 3, 4, 2, 2]))
```

---

## 14) Topological Sort Pattern

### What is the Topological Sort Pattern?

**Topological Sort** orders nodes in a **Directed Acyclic Graph (DAG)** such that for every edge `u -> v`, `u` appears before `v`.

Common technique: **Kahn’s Algorithm**

1. Compute in-degree of each node
2. Push all nodes with in-degree 0 into a queue
3. Pop node, reduce in-degree of its neighbors

### Where is this pattern useful?

1. Course prerequisite scheduling
2. Task ordering with dependencies
3. Build systems / compilation order
4. Detecting cycles in directed graphs
5. Any “must do A before B” constraints

### How to Recognize When to Apply Topological Sort

| Signal         | Description                             |
| -------------- | --------------------------------------- |
| Dependencies   | “A depends on B”                        |
| Directed edges | prerequisites or ordering constraints   |
| Need ordering  | return an order or detect impossibility |

### How to Apply — Step-by-Step

1. Build adjacency list
2. Build indegree counts
3. Queue all `indegree==0`
4. Process queue and build order

### Diagram

```text
Edges: 0 -> 1 -> 2
Order: 0,1,2
```

---

### Problem A: Course Schedule (cycle detection)

LeetCode: https://leetcode.com/problems/course-schedule/

#### JavaScript (Node.js runnable)

```javascript
function canFinish(numCourses, prerequisites) {
  const graph = Array.from({ length: numCourses }, () => []);
  const indeg = new Array(numCourses).fill(0);

  for (const [a, b] of prerequisites) {
    graph[b].push(a);
    indeg[a]++;
  }

  const q = [];
  for (let i = 0; i < numCourses; i++) if (indeg[i] === 0) q.push(i);

  let taken = 0;
  while (q.length) {
    const node = q.shift();
    taken++;
    for (const nxt of graph[node]) {
      indeg[nxt]--;
      if (indeg[nxt] === 0) q.push(nxt);
    }
  }

  return taken === numCourses;
}

// Demo
console.log(canFinish(2, [[1, 0]])); // true
console.log(
  canFinish(2, [
    [1, 0],
    [0, 1],
  ]),
); // false
```

#### Python (runnable)

```python
from collections import deque


def can_finish(num_courses, prerequisites):
    graph = [[] for _ in range(num_courses)]
    indeg = [0] * num_courses
    for a, b in prerequisites:
        graph[b].append(a)
        indeg[a] += 1

    q = deque([i for i in range(num_courses) if indeg[i] == 0])
    taken = 0

    while q:
        node = q.popleft()
        taken += 1
        for nxt in graph[node]:
            indeg[nxt] -= 1
            if indeg[nxt] == 0:
                q.append(nxt)

    return taken == num_courses


if __name__ == "__main__":
    print(can_finish(2, [(1, 0)]))
    print(can_finish(2, [(1, 0), (0, 1)]))
```

---

### Problem B: Course Schedule II (return order)

LeetCode: https://leetcode.com/problems/course-schedule-ii/

#### JavaScript (Node.js runnable)

```javascript
function findOrder(numCourses, prerequisites) {
  const graph = Array.from({ length: numCourses }, () => []);
  const indeg = new Array(numCourses).fill(0);
  for (const [a, b] of prerequisites) {
    graph[b].push(a);
    indeg[a]++;
  }

  const q = [];
  for (let i = 0; i < numCourses; i++) if (indeg[i] === 0) q.push(i);

  const order = [];
  while (q.length) {
    const node = q.shift();
    order.push(node);
    for (const nxt of graph[node]) {
      indeg[nxt]--;
      if (indeg[nxt] === 0) q.push(nxt);
    }
  }

  return order.length === numCourses ? order : [];
}

// Demo
console.log(
  findOrder(4, [
    [1, 0],
    [2, 0],
    [3, 1],
    [3, 2],
  ]),
);
```

#### Python (runnable)

```python
from collections import deque


def find_order(num_courses, prerequisites):
    graph = [[] for _ in range(num_courses)]
    indeg = [0] * num_courses
    for a, b in prerequisites:
        graph[b].append(a)
        indeg[a] += 1

    q = deque([i for i in range(num_courses) if indeg[i] == 0])
    order = []

    while q:
        node = q.popleft()
        order.append(node)
        for nxt in graph[node]:
            indeg[nxt] -= 1
            if indeg[nxt] == 0:
                q.append(nxt)

    return order if len(order) == num_courses else []


if __name__ == "__main__":
    print(find_order(4, [(1, 0), (2, 0), (3, 1), (3, 2)]))
```

---

## 15) Matrices Pattern

### What is the Matrices Pattern?

The **Matrices** pattern includes common grid/matrix techniques:

- directional traversal (up/down/left/right)
- boundary management (spiral)
- in-place transforms (rotate)
- BFS/DFS on grid

### Where is this pattern useful?

1. Spiral traversal
2. Matrix rotation
3. Flood fill / island counting
4. Dynamic programming on grids
5. Search in row/column sorted matrices

### How to Recognize When to Apply Matrix Techniques

| Signal          | Description                     |
| --------------- | ------------------------------- |
| 2D input        | grid/matrix                     |
| move directions | up/down/left/right              |
| boundaries      | top/bottom/left/right shrinking |

### Diagram (spiral boundaries)

```text
top -> ...
left ... right
... <- bottom
```

---

### Problem A: Spiral Matrix

LeetCode: https://leetcode.com/problems/spiral-matrix/

#### JavaScript (Node.js runnable)

```javascript
function spiralOrder(matrix) {
  const res = [];
  let top = 0;
  let bottom = matrix.length - 1;
  let left = 0;
  let right = matrix[0].length - 1;

  while (top <= bottom && left <= right) {
    for (let c = left; c <= right; c++) res.push(matrix[top][c]);
    top++;
    for (let r = top; r <= bottom; r++) res.push(matrix[r][right]);
    right--;
    if (top <= bottom) {
      for (let c = right; c >= left; c--) res.push(matrix[bottom][c]);
      bottom--;
    }
    if (left <= right) {
      for (let r = bottom; r >= top; r--) res.push(matrix[r][left]);
      left++;
    }
  }

  return res;
}

// Demo
console.log(
  spiralOrder([
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9],
  ]),
);
```

#### Python (runnable)

```python
def spiral_order(matrix):
    res = []
    top, bottom = 0, len(matrix) - 1
    left, right = 0, len(matrix[0]) - 1

    while top <= bottom and left <= right:
        for c in range(left, right + 1):
            res.append(matrix[top][c])
        top += 1

        for r in range(top, bottom + 1):
            res.append(matrix[r][right])
        right -= 1

        if top <= bottom:
            for c in range(right, left - 1, -1):
                res.append(matrix[bottom][c])
            bottom -= 1

        if left <= right:
            for r in range(bottom, top - 1, -1):
                res.append(matrix[r][left])
            left += 1

    return res


if __name__ == "__main__":
    print(spiral_order([[1, 2, 3], [4, 5, 6], [7, 8, 9]]))
```

---

### Problem B: Rotate Image

LeetCode: https://leetcode.com/problems/rotate-image/

#### JavaScript (Node.js runnable)

```javascript
function rotate(matrix) {
  const n = matrix.length;
  // transpose
  for (let i = 0; i < n; i++) {
    for (let j = i + 1; j < n; j++) {
      [matrix[i][j], matrix[j][i]] = [matrix[j][i], matrix[i][j]];
    }
  }
  // reverse each row
  for (let i = 0; i < n; i++) matrix[i].reverse();
  return matrix;
}

// Demo
const m = [
  [1, 2, 3],
  [4, 5, 6],
  [7, 8, 9],
];
console.log(rotate(m));
```

#### Python (runnable)

```python
def rotate(matrix):
    n = len(matrix)
    for i in range(n):
        for j in range(i + 1, n):
            matrix[i][j], matrix[j][i] = matrix[j][i], matrix[i][j]
    for i in range(n):
        matrix[i].reverse()
    return matrix


if __name__ == "__main__":
    m = [[1, 2, 3], [4, 5, 6], [7, 8, 9]]
    print(rotate(m))
```

---

## 16) Stacks Pattern

### What is the Stacks Pattern?

The **Stack** pattern (LIFO) is used when you need to process items in **reverse order**, track **nested structure**, or maintain a **monotonic stack**.

### Where is this pattern useful?

1. Parentheses validation
2. Next greater element
3. Largest rectangle in histogram
4. Evaluate expressions
5. Backtracking-like undo behavior

### How to Recognize When to Apply Stacks

| Signal               | Description       |
| -------------------- | ----------------- |
| Nested/brackets      | (), {}, []        |
| Next greater/smaller | monotonic stack   |
| Need undo            | last-in-first-out |

### Diagram

```text
push -> [a,b,c] -> pop returns c
```

---

### Problem A: Valid Parentheses

LeetCode: https://leetcode.com/problems/valid-parentheses/

#### JavaScript (Node.js runnable)

```javascript
function isValid(s) {
  const stack = [];
  const match = new Map([
    [')', '('],
    [']', '['],
    ['}', '{'],
  ]);

  for (const ch of s) {
    if (ch === '(' || ch === '[' || ch === '{') stack.push(ch);
    else {
      if (stack.length === 0) return false;
      if (stack.pop() !== match.get(ch)) return false;
    }
  }
  return stack.length === 0;
}

// Demo
console.log(isValid('()[]{}')); // true
console.log(isValid('(]')); // false
```

#### Python (runnable)

```python
def is_valid(s: str) -> bool:
    stack = []
    match = {')': '(', ']': '[', '}': '{'}
    for ch in s:
        if ch in '([{':
            stack.append(ch)
        else:
            if not stack:
                return False
            if stack.pop() != match[ch]:
                return False
    return len(stack) == 0


if __name__ == "__main__":
    print(is_valid('()[]{}'))
    print(is_valid('(]'))
```

---

### Problem B: Daily Temperatures

LeetCode: https://leetcode.com/problems/daily-temperatures/

#### JavaScript (Node.js runnable)

```javascript
function dailyTemperatures(temperatures) {
  const res = new Array(temperatures.length).fill(0);
  const stack = []; // indices, monotonic decreasing

  for (let i = 0; i < temperatures.length; i++) {
    while (
      stack.length &&
      temperatures[i] > temperatures[stack[stack.length - 1]]
    ) {
      const j = stack.pop();
      res[j] = i - j;
    }
    stack.push(i);
  }

  return res;
}

// Demo
console.log(dailyTemperatures([73, 74, 75, 71, 69, 72, 76, 73]));
```

#### Python (runnable)

```python
def daily_temperatures(temperatures):
    res = [0] * len(temperatures)
    stack = []

    for i, t in enumerate(temperatures):
        while stack and t > temperatures[stack[-1]]:
            j = stack.pop()
            res[j] = i - j
        stack.append(i)

    return res


if __name__ == "__main__":
    print(daily_temperatures([73, 74, 75, 71, 69, 72, 76, 73]))
```

---

## 17) Graphs Pattern

### What is the Graphs Pattern?

Graph problems model relationships as nodes + edges.
Core techniques:

- BFS/DFS traversal
- shortest path (Dijkstra)
- connected components
- union-find

### Where is this pattern useful?

1. Connectivity / components
2. Path existence
3. Shortest path
4. Cycle detection
5. Bipartite checking

### How to Recognize Graph Problems

| Signal              | Description           |
| ------------------- | --------------------- |
| nodes/edges         | direct representation |
| “connected”         | components            |
| “minimum cost path” | shortest path         |

---

### Problem A: Number of Islands

LeetCode: https://leetcode.com/problems/number-of-islands/

#### JavaScript (Node.js runnable)

```javascript
function numIslands(grid) {
  const rows = grid.length;
  const cols = grid[0].length;
  const dirs = [
    [1, 0],
    [-1, 0],
    [0, 1],
    [0, -1],
  ];

  function dfs(r, c) {
    if (r < 0 || c < 0 || r >= rows || c >= cols || grid[r][c] !== '1') return;
    grid[r][c] = '0';
    for (const [dr, dc] of dirs) dfs(r + dr, c + dc);
  }

  let count = 0;
  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      if (grid[r][c] === '1') {
        count++;
        dfs(r, c);
      }
    }
  }
  return count;
}

// Demo
console.log(
  numIslands([
    ['1', '1', '0', '0', '0'],
    ['1', '1', '0', '0', '0'],
    ['0', '0', '1', '0', '0'],
    ['0', '0', '0', '1', '1'],
  ]),
);
```

#### Python (runnable)

```python
def num_islands(grid):
    rows, cols = len(grid), len(grid[0])
    dirs = [(1,0),(-1,0),(0,1),(0,-1)]

    def dfs(r, c):
        if r < 0 or c < 0 or r >= rows or c >= cols or grid[r][c] != '1':
            return
        grid[r][c] = '0'
        for dr, dc in dirs:
            dfs(r + dr, c + dc)

    count = 0
    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == '1':
                count += 1
                dfs(r, c)
    return count


if __name__ == "__main__":
    print(num_islands([
        ['1','1','0','0','0'],
        ['1','1','0','0','0'],
        ['0','0','1','0','0'],
        ['0','0','0','1','1'],
    ]))
```

---

### Problem B: Clone Graph

LeetCode: https://leetcode.com/problems/clone-graph/

#### JavaScript (Node.js runnable)

```javascript
class Node {
  constructor(val, neighbors = []) {
    this.val = val;
    this.neighbors = neighbors;
  }
}

function cloneGraph(node) {
  if (!node) return null;
  const map = new Map();

  function dfs(n) {
    if (map.has(n)) return map.get(n);
    const copy = new Node(n.val);
    map.set(n, copy);
    copy.neighbors = n.neighbors.map(dfs);
    return copy;
  }

  return dfs(node);
}

// Demo (small 2-node graph)
const n1 = new Node(1);
const n2 = new Node(2);
n1.neighbors = [n2];
n2.neighbors = [n1];
const cloned = cloneGraph(n1);
console.log(cloned.val, cloned.neighbors[0].val); // 1 2
```

#### Python (runnable)

```python
class Node:
    def __init__(self, val=0, neighbors=None):
        self.val = val
        self.neighbors = neighbors if neighbors is not None else []


def clone_graph(node):
    if node is None:
        return None
    mp = {}

    def dfs(n):
        if n in mp:
            return mp[n]
        copy = Node(n.val)
        mp[n] = copy
        copy.neighbors = [dfs(x) for x in n.neighbors]
        return copy

    return dfs(node)


if __name__ == "__main__":
    n1 = Node(1)
    n2 = Node(2)
    n1.neighbors = [n2]
    n2.neighbors = [n1]
    c = clone_graph(n1)
    print(c.val, c.neighbors[0].val)
```

---

## 18) DFS (Depth-First Search) Pattern

### What is the DFS Pattern?

**DFS** explores as deep as possible before backtracking.
It’s used on trees, graphs, and grids.

### Where is this pattern useful?

1. Path existence
2. Components / traversal
3. Tree recursion problems
4. Backtracking search
5. Topological sort (via DFS variant)

### How to Recognize When to Apply DFS

| Signal                 | Description                  |
| ---------------------- | ---------------------------- |
| Recursive structure    | trees/graphs                 |
| Need explore all paths | enumerate or check existence |
| Stack-like             | recursion or explicit stack  |

### Diagram

```text
DFS: go deep -> backtrack -> next branch
```

---

### Problem A: Path Sum

LeetCode: https://leetcode.com/problems/path-sum/

#### JavaScript (Node.js runnable)

```javascript
class TreeNode {
  constructor(val, left = null, right = null) {
    this.val = val;
    this.left = left;
    this.right = right;
  }
}

function hasPathSum(root, targetSum) {
  if (!root) return false;
  if (!root.left && !root.right) return root.val === targetSum;
  return (
    hasPathSum(root.left, targetSum - root.val) ||
    hasPathSum(root.right, targetSum - root.val)
  );
}

// Demo
const root = new TreeNode(
  5,
  new TreeNode(4, new TreeNode(11, new TreeNode(7), new TreeNode(2))),
  new TreeNode(8, new TreeNode(13), new TreeNode(4, null, new TreeNode(1))),
);
console.log(hasPathSum(root, 22)); // true
```

#### Python (runnable)

```python
class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def has_path_sum(root, target_sum):
    if root is None:
        return False
    if root.left is None and root.right is None:
        return root.val == target_sum
    return has_path_sum(root.left, target_sum - root.val) or has_path_sum(
        root.right, target_sum - root.val
    )


if __name__ == "__main__":
    root = TreeNode(
        5,
        TreeNode(4, TreeNode(11, TreeNode(7), TreeNode(2))),
        TreeNode(8, TreeNode(13), TreeNode(4, None, TreeNode(1))),
    )
    print(has_path_sum(root, 22))
```

---

### Problem B: Diameter of Binary Tree

LeetCode: https://leetcode.com/problems/diameter-of-binary-tree/

#### JavaScript (Node.js runnable)

```javascript
function diameterOfBinaryTree(root) {
  let best = 0;

  function depth(node) {
    if (!node) return 0;
    const l = depth(node.left);
    const r = depth(node.right);
    best = Math.max(best, l + r);
    return Math.max(l, r) + 1;
  }

  depth(root);
  return best;
}

// Demo using same TreeNode from above section
console.log(diameterOfBinaryTree(root));
```

#### Python (runnable)

```python
def diameter_of_binary_tree(root):
    best = 0

    def depth(node):
        nonlocal best
        if node is None:
            return 0
        l = depth(node.left)
        r = depth(node.right)
        best = max(best, l + r)
        return max(l, r) + 1

    depth(root)
    return best


if __name__ == "__main__":
    # reuse root from above
    print(diameter_of_binary_tree(root))
```

---

## 19) BFS (Breadth-First Search) Pattern

### What is the BFS Pattern?

**BFS** explores level-by-level (or distance-by-distance) using a **queue**.
It’s used for:

- shortest path in unweighted graphs
- level-order traversal in trees
- multi-source spreading problems

### Where is this pattern useful?

1. Level order traversal
2. Shortest path in unweighted graphs/grids
3. Rotting/spreading simulations
4. Minimum steps problems
5. Multi-source BFS

### How to Recognize When to Apply BFS

| Signal           | Description                   |
| ---------------- | ----------------------------- |
| “minimum steps”  | often BFS on unweighted graph |
| level order      | tree levels                   |
| spread over time | rotting oranges, fire spread  |

### Diagram

```text
Queue: push start -> pop -> push neighbors
Distance increases by levels
```

---

### Problem A: Rotting Oranges

LeetCode: https://leetcode.com/problems/rotting-oranges/

#### JavaScript (Node.js runnable)

```javascript
function orangesRotting(grid) {
  const rows = grid.length;
  const cols = grid[0].length;
  const q = [];
  let fresh = 0;

  for (let r = 0; r < rows; r++) {
    for (let c = 0; c < cols; c++) {
      if (grid[r][c] === 2) q.push([r, c]);
      if (grid[r][c] === 1) fresh++;
    }
  }

  const dirs = [
    [1, 0],
    [-1, 0],
    [0, 1],
    [0, -1],
  ];
  let minutes = 0;

  while (q.length && fresh > 0) {
    const size = q.length;
    for (let i = 0; i < size; i++) {
      const [r, c] = q.shift();
      for (const [dr, dc] of dirs) {
        const nr = r + dr;
        const nc = c + dc;
        if (nr < 0 || nc < 0 || nr >= rows || nc >= cols) continue;
        if (grid[nr][nc] !== 1) continue;
        grid[nr][nc] = 2;
        fresh--;
        q.push([nr, nc]);
      }
    }
    minutes++;
  }

  return fresh === 0 ? minutes : -1;
}

// Demo
console.log(
  orangesRotting([
    [2, 1, 1],
    [1, 1, 0],
    [0, 1, 1],
  ]),
); // 4
```

#### Python (runnable)

```python
from collections import deque


def oranges_rotting(grid):
    rows, cols = len(grid), len(grid[0])
    q = deque()
    fresh = 0

    for r in range(rows):
        for c in range(cols):
            if grid[r][c] == 2:
                q.append((r, c))
            elif grid[r][c] == 1:
                fresh += 1

    dirs = [(1, 0), (-1, 0), (0, 1), (0, -1)]
    minutes = 0

    while q and fresh > 0:
        for _ in range(len(q)):
            r, c = q.popleft()
            for dr, dc in dirs:
                nr, nc = r + dr, c + dc
                if nr < 0 or nc < 0 or nr >= rows or nc >= cols:
                    continue
                if grid[nr][nc] != 1:
                    continue
                grid[nr][nc] = 2
                fresh -= 1
                q.append((nr, nc))
        minutes += 1

    return minutes if fresh == 0 else -1


if __name__ == "__main__":
    print(oranges_rotting([[2, 1, 1], [1, 1, 0], [0, 1, 1]]))
```

---

### Problem B: Binary Tree Level Order Traversal

LeetCode: https://leetcode.com/problems/binary-tree-level-order-traversal/

#### JavaScript (Node.js runnable)

```javascript
class TreeNode {
  constructor(val, left = null, right = null) {
    this.val = val;
    this.left = left;
    this.right = right;
  }
}

function levelOrder(root) {
  if (!root) return [];
  const res = [];
  const q = [root];

  while (q.length) {
    const size = q.length;
    const level = [];
    for (let i = 0; i < size; i++) {
      const node = q.shift();
      level.push(node.val);
      if (node.left) q.push(node.left);
      if (node.right) q.push(node.right);
    }
    res.push(level);
  }
  return res;
}

// Demo
const rootBfs = new TreeNode(
  3,
  new TreeNode(9),
  new TreeNode(20, new TreeNode(15), new TreeNode(7)),
);
console.log(levelOrder(rootBfs)); // [[3],[9,20],[15,7]]
```

#### Python (runnable)

```python
from collections import deque


class TreeNode:
    def __init__(self, val=0, left=None, right=None):
        self.val = val
        self.left = left
        self.right = right


def level_order(root):
    if root is None:
        return []
    res = []
    q = deque([root])
    while q:
        level = []
        for _ in range(len(q)):
            node = q.popleft()
            level.append(node.val)
            if node.left:
                q.append(node.left)
            if node.right:
                q.append(node.right)
        res.append(level)
    return res


if __name__ == "__main__":
    root = TreeNode(3, TreeNode(9), TreeNode(20, TreeNode(15), TreeNode(7)))
    print(level_order(root))
```

---

## 20) Trie Pattern

### What is the Trie Pattern?

A **Trie (Prefix Tree)** stores strings character-by-character.
It supports efficient:

- prefix search
- word search
- autocomplete-like operations

### Where is this pattern useful?

1. Prefix matching
2. Word dictionary with wildcards
3. Autocomplete suggestions
4. Replace words by roots
5. Multi-word search in boards (Word Search II)

### How to Recognize When to Apply Trie

| Signal              | Description                    |
| ------------------- | ------------------------------ |
| “prefix”            | startsWith queries             |
| many string lookups | faster than scanning each time |
| wildcard search     | add/search with '.'            |

### Diagram

```text
root
 ├─ c ─ a ─ t (word)
 └─ c ─ a ─ r (word)
```

---

### Problem A: Implement Trie (Prefix Tree)

LeetCode: https://leetcode.com/problems/implement-trie-prefix-tree/

#### JavaScript (Node.js runnable)

```javascript
class TrieNode {
  constructor() {
    this.children = new Map();
    this.isEnd = false;
  }
}

class Trie {
  constructor() {
    this.root = new TrieNode();
  }

  insert(word) {
    let node = this.root;
    for (const ch of word) {
      if (!node.children.has(ch)) node.children.set(ch, new TrieNode());
      node = node.children.get(ch);
    }
    node.isEnd = true;
  }

  search(word) {
    let node = this.root;
    for (const ch of word) {
      if (!node.children.has(ch)) return false;
      node = node.children.get(ch);
    }
    return node.isEnd;
  }

  startsWith(prefix) {
    let node = this.root;
    for (const ch of prefix) {
      if (!node.children.has(ch)) return false;
      node = node.children.get(ch);
    }
    return true;
  }
}

// Demo
const t = new Trie();
t.insert('apple');
console.log(t.search('apple')); // true
console.log(t.search('app')); // false
console.log(t.startsWith('app')); // true
t.insert('app');
console.log(t.search('app')); // true
```

#### Python (runnable)

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False


class Trie:
    def __init__(self):
        self.root = TrieNode()

    def insert(self, word: str) -> None:
        node = self.root
        for ch in word:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    def search(self, word: str) -> bool:
        node = self.root
        for ch in word:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return node.is_end

    def startsWith(self, prefix: str) -> bool:
        node = self.root
        for ch in prefix:
            if ch not in node.children:
                return False
            node = node.children[ch]
        return True


if __name__ == "__main__":
    t = Trie()
    t.insert("apple")
    print(t.search("apple"))
    print(t.search("app"))
    print(t.startsWith("app"))
    t.insert("app")
    print(t.search("app"))
```

---

### Problem B: Replace Words

LeetCode: https://leetcode.com/problems/replace-words/

#### JavaScript (Node.js runnable)

```javascript
class TrieNode {
  constructor() {
    this.children = new Map();
    this.isEnd = false;
  }
}

function replaceWords(dictionary, sentence) {
  const root = new TrieNode();
  for (const w of dictionary) {
    let node = root;
    for (const ch of w) {
      if (!node.children.has(ch)) node.children.set(ch, new TrieNode());
      node = node.children.get(ch);
    }
    node.isEnd = true;
  }

  function findRoot(word) {
    let node = root;
    let prefix = '';
    for (const ch of word) {
      if (!node.children.has(ch)) return word;
      node = node.children.get(ch);
      prefix += ch;
      if (node.isEnd) return prefix;
    }
    return word;
  }

  return sentence
    .split(' ')
    .map((w) => findRoot(w))
    .join(' ');
}

// Demo
console.log(
  replaceWords(['cat', 'bat', 'rat'], 'the cattle was rattled by the battery'),
);
```

#### Python (runnable)

```python
class TrieNode:
    def __init__(self):
        self.children = {}
        self.is_end = False


def replace_words(dictionary, sentence):
    root = TrieNode()

    for w in dictionary:
        node = root
        for ch in w:
            if ch not in node.children:
                node.children[ch] = TrieNode()
            node = node.children[ch]
        node.is_end = True

    def find_root(word):
        node = root
        prefix = ""
        for ch in word:
            if ch not in node.children:
                return word
            node = node.children[ch]
            prefix += ch
            if node.is_end:
                return prefix
        return word

    return " ".join(find_root(w) for w in sentence.split())


if __name__ == "__main__":
    print(replace_words(["cat", "bat", "rat"], "the cattle was rattled by the battery"))
```

---

## 21) Hash Maps Pattern

### What is the Hash Maps Pattern?

A **Hash Map** stores key-value pairs for O(1) average lookup.
It is used to:

- count frequency
- track indices
- detect duplicates
- store prefixes (prefix sums)

### Where is this pattern useful?

1. Two Sum / index mapping
2. Frequency counting
3. Prefix sum counts
4. Grouping (anagrams)
5. Caching

### How to Recognize When to Apply Hash Maps

| Signal              | Description                     |
| ------------------- | ------------------------------- |
| Need fast lookup    | “find if exists”, “seen before” |
| Count occurrences   | frequencies                     |
| mapping index/value | store index for complement      |

---

### Problem A: Two Sum

LeetCode: https://leetcode.com/problems/two-sum/

#### JavaScript (Node.js runnable)

```javascript
function twoSum(nums, target) {
  const map = new Map();
  for (let i = 0; i < nums.length; i++) {
    const need = target - nums[i];
    if (map.has(need)) return [map.get(need), i];
    map.set(nums[i], i);
  }
  return [-1, -1];
}

// Demo
console.log(twoSum([2, 7, 11, 15], 9)); // [0,1]
```

#### Python (runnable)

```python
def two_sum(nums, target):
    mp = {}
    for i, x in enumerate(nums):
        need = target - x
        if need in mp:
            return [mp[need], i]
        mp[x] = i
    return [-1, -1]


if __name__ == "__main__":
    print(two_sum([2, 7, 11, 15], 9))
```

---

### Problem B: Subarray Sum Equals K

LeetCode: https://leetcode.com/problems/subarray-sum-equals-k/

#### JavaScript (Node.js runnable)

```javascript
function subarraySum(nums, k) {
  const count = new Map();
  count.set(0, 1);
  let prefix = 0;
  let ans = 0;

  for (const x of nums) {
    prefix += x;
    ans += count.get(prefix - k) || 0;
    count.set(prefix, (count.get(prefix) || 0) + 1);
  }

  return ans;
}

// Demo
console.log(subarraySum([1, 1, 1], 2)); // 2
```

#### Python (runnable)

```python
from collections import defaultdict


def subarray_sum(nums, k):
    count = defaultdict(int)
    count[0] = 1
    prefix = 0
    ans = 0
    for x in nums:
        prefix += x
        ans += count[prefix - k]
        count[prefix] += 1
    return ans


if __name__ == "__main__":
    print(subarray_sum([1, 1, 1], 2))
```

---

## 22) Union Find (DSU) Pattern

### What is the Union Find Pattern?

**Disjoint Set Union (Union-Find)** maintains a collection of disjoint sets with operations:

- `find(x)` → representative
- `union(a,b)` → merge sets

With **path compression** and **union by rank/size**, operations are almost O(1).

### Where is this pattern useful?

1. Connectivity in undirected graphs
2. Detecting cycles
3. Grouping components
4. Dynamic connectivity queries
5. Kruskal’s MST

### How to Recognize When to Apply DSU

| Signal                 | Description          |
| ---------------------- | -------------------- |
| “connected components” | union edges          |
| dynamic merges         | union operations     |
| cycle detection        | redundant connection |

---

### Problem A: Redundant Connection

LeetCode: https://leetcode.com/problems/redundant-connection/

#### JavaScript (Node.js runnable)

```javascript
class DSU {
  constructor(n) {
    this.parent = Array.from({ length: n + 1 }, (_, i) => i);
    this.rank = new Array(n + 1).fill(0);
  }
  find(x) {
    if (this.parent[x] !== x) this.parent[x] = this.find(this.parent[x]);
    return this.parent[x];
  }
  union(a, b) {
    let pa = this.find(a);
    let pb = this.find(b);
    if (pa === pb) return false;
    if (this.rank[pa] < this.rank[pb]) [pa, pb] = [pb, pa];
    this.parent[pb] = pa;
    if (this.rank[pa] === this.rank[pb]) this.rank[pa]++;
    return true;
  }
}

function findRedundantConnection(edges) {
  const n = edges.length;
  const dsu = new DSU(n);
  for (const [a, b] of edges) {
    if (!dsu.union(a, b)) return [a, b];
  }
  return [];
}

// Demo
console.log(
  findRedundantConnection([
    [1, 2],
    [1, 3],
    [2, 3],
  ]),
); // [2,3]
```

#### Python (runnable)

```python
def find_redundant_connection(edges):
    n = len(edges)
    parent = list(range(n + 1))
    rank = [0] * (n + 1)

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    def union(a, b):
        pa, pb = find(a), find(b)
        if pa == pb:
            return False
        if rank[pa] < rank[pb]:
            pa, pb = pb, pa
        parent[pb] = pa
        if rank[pa] == rank[pb]:
            rank[pa] += 1
        return True

    for a, b in edges:
        if not union(a, b):
            return [a, b]
    return []


if __name__ == "__main__":
    print(find_redundant_connection([[1, 2], [1, 3], [2, 3]]))
```

---

### Problem B: Number of Provinces

LeetCode: https://leetcode.com/problems/number-of-provinces/

#### JavaScript (Node.js runnable)

```javascript
class DSU {
  constructor(n) {
    this.parent = Array.from({ length: n }, (_, i) => i);
    this.rank = new Array(n).fill(0);
    this.count = n;
  }
  find(x) {
    if (this.parent[x] !== x) this.parent[x] = this.find(this.parent[x]);
    return this.parent[x];
  }
  union(a, b) {
    let pa = this.find(a);
    let pb = this.find(b);
    if (pa === pb) return;
    if (this.rank[pa] < this.rank[pb]) [pa, pb] = [pb, pa];
    this.parent[pb] = pa;
    if (this.rank[pa] === this.rank[pb]) this.rank[pa]++;
    this.count--;
  }
}

function findCircleNum(isConnected) {
  const n = isConnected.length;
  const dsu = new DSU(n);
  for (let i = 0; i < n; i++) {
    for (let j = i + 1; j < n; j++) {
      if (isConnected[i][j] === 1) dsu.union(i, j);
    }
  }
  return dsu.count;
}

// Demo
console.log(
  findCircleNum([
    [1, 1, 0],
    [1, 1, 0],
    [0, 0, 1],
  ]),
); // 2
```

#### Python (runnable)

```python
def find_circle_num(is_connected):
    n = len(is_connected)
    parent = list(range(n))
    rank = [0] * n
    count = n

    def find(x):
        while parent[x] != x:
            parent[x] = parent[parent[x]]
            x = parent[x]
        return x

    def union(a, b):
        nonlocal count
        pa, pb = find(a), find(b)
        if pa == pb:
            return
        if rank[pa] < rank[pb]:
            pa, pb = pb, pa
        parent[pb] = pa
        if rank[pa] == rank[pb]:
            rank[pa] += 1
        count -= 1

    for i in range(n):
        for j in range(i + 1, n):
            if is_connected[i][j] == 1:
                union(i, j)

    return count


if __name__ == "__main__":
    print(find_circle_num([[1, 1, 0], [1, 1, 0], [0, 0, 1]]))
```

---

## 23) Bitwise Manipulation Pattern

### What is the Bitwise Manipulation Pattern?

Bitwise operations (`&`, `|`, `^`, `~`, `<<`, `>>`) allow efficient tricks for:

- toggling/checking bits
- XOR properties
- masking

### Where is this pattern useful?

1. Single number / odd occurrences
2. Subset enumeration via bitmasks
3. Power-of-two checks
4. Bit counting
5. Low-level optimizations

### How to Recognize When to Apply Bitwise

| Signal                | Description |
| --------------------- | ----------- |
| “appears once”        | XOR trick   |
| bit constraints       | use mask    |
| binary representation | hints       |

---

### Problem A: Single Number

LeetCode: https://leetcode.com/problems/single-number/

#### JavaScript (Node.js runnable)

```javascript
function singleNumber(nums) {
  let x = 0;
  for (const n of nums) x ^= n;
  return x;
}

// Demo
console.log(singleNumber([2, 2, 1])); // 1
```

#### Python (runnable)

```python
def single_number(nums):
    x = 0
    for n in nums:
        x ^= n
    return x


if __name__ == "__main__":
    print(single_number([2, 2, 1]))
```

---

### Problem B: Number of 1 Bits

LeetCode: https://leetcode.com/problems/number-of-1-bits/

#### JavaScript (Node.js runnable)

```javascript
function hammingWeight(n) {
  let count = 0;
  while (n !== 0) {
    n = n & (n - 1);
    count++;
  }
  return count;
}

// Demo (treat as unsigned 32-bit)
console.log(hammingWeight(0b1011)); // 3
```

#### Python (runnable)

```python
def hamming_weight(n: int) -> int:
    count = 0
    while n:
        n &= n - 1
        count += 1
    return count


if __name__ == "__main__":
    print(hamming_weight(0b1011))
```

---

## 24) Mathematical Patterns (GCD, Sieve, Fast Exponentiation)

### What are Mathematical Patterns?

This group contains common math helpers:

- **GCD** (Euclid)
- **Sieve of Eratosthenes** (primes)
- **Fast exponentiation** (binary exponentiation)

### Where is this pattern useful?

1. Prime counting/generation
2. Modular exponentiation
3. Simplifying fractions via gcd
4. LCM and divisor problems
5. Large power computations

### Diagram (binary exponentiation)

```text
n in binary:
if bit is 1 -> multiply answer by current base
square base each step
```

---

### Problem A: Count Primes (Sieve)

LeetCode: https://leetcode.com/problems/count-primes/

#### JavaScript (Node.js runnable)

```javascript
function countPrimes(n) {
  if (n <= 2) return 0;
  const isPrime = new Array(n).fill(true);
  isPrime[0] = false;
  isPrime[1] = false;

  for (let p = 2; p * p < n; p++) {
    if (!isPrime[p]) continue;
    for (let x = p * p; x < n; x += p) isPrime[x] = false;
  }

  let count = 0;
  for (let i = 2; i < n; i++) if (isPrime[i]) count++;
  return count;
}

// Demo
console.log(countPrimes(10)); // 4 (2,3,5,7)
```

#### Python (runnable)

```python
def count_primes(n: int) -> int:
    if n <= 2:
        return 0
    is_prime = [True] * n
    is_prime[0] = is_prime[1] = False
    p = 2
    while p * p < n:
        if is_prime[p]:
            for x in range(p * p, n, p):
                is_prime[x] = False
        p += 1
    return sum(is_prime)


if __name__ == "__main__":
    print(count_primes(10))
```

---

### Problem B: Pow(x, n) (Fast Exponentiation)

LeetCode: https://leetcode.com/problems/powx-n/

#### JavaScript (Node.js runnable)

```javascript
function myPow(x, n) {
  let exp = n;
  let base = x;
  let ans = 1;
  if (exp < 0) {
    base = 1 / base;
    exp = -exp;
  }
  while (exp > 0) {
    if (exp & 1) ans *= base;
    base *= base;
    exp = Math.floor(exp / 2);
  }
  return ans;
}

// Demo
console.log(myPow(2.0, 10)); // 1024
console.log(myPow(2.0, -2)); // 0.25
```

#### Python (runnable)

```python
def my_pow(x: float, n: int) -> float:
    exp = n
    base = x
    ans = 1.0
    if exp < 0:
        base = 1.0 / base
        exp = -exp
    while exp > 0:
        if exp & 1:
            ans *= base
        base *= base
        exp //= 2
    return ans


if __name__ == "__main__":
    print(my_pow(2.0, 10))
    print(my_pow(2.0, -2))
```
