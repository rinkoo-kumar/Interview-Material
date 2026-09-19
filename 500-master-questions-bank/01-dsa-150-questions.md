# 500 Master Questions Bank: Volume 1
## 150 DSA Interview Questions & Complete Answers (Python 3 & Java 17)

### Section 1: Arrays, Hashing & Prefix Sum (Q1–Q20)

#### Q1: Array vs dynamic array memory mechanics
- **Answer**: Fixed arrays allocate a contiguous memory block of fixed size. Dynamic arrays allocate a contiguous memory block, and when full, allocate a new block of double the size, copy elements over, and free the old block, giving amortized O(1) insertion.
- **Time Complexity**: O(1) access, Amortized O(1) insertion
- **Space Complexity**: O(N)

#### Q2: Dutch National Flag algorithm
- **Answer**: Sort an array of 0s, 1s, and 2s in one pass using three pointers (`low`, `mid`, `high`). `low` tracks the boundary of 0s, `high` tracks 2s, and `mid` scans the array.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)
```python
def sortColors(nums):
    low, mid, high = 0, 0, len(nums) - 1
    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1; mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:
            nums[high], nums[mid] = nums[mid], nums[high]
            high -= 1
```
```java
public void sortColors(int[] nums) {
    int low = 0, mid = 0, high = nums.length - 1;
    while (mid <= high) {
        if (nums[mid] == 0) {
            int temp = nums[low]; nums[low++] = nums[mid]; nums[mid++] = temp;
        } else if (nums[mid] == 1) {
            mid++;
        } else {
            int temp = nums[high]; nums[high--] = nums[mid]; nums[mid] = temp;
        }
    }
}
```

#### Q3: Trapping Rain Water (two-pointer proof)
- **Answer**: Maintain `left_max` and `right_max`. Move the pointer pointing to the smaller max inwards, safely adding `max - current_height` to the total trapped water because the other side is guaranteed to be bounded by a higher or equal bar.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q4: Sliding Window Minimum Window Substring O(N)
- **Answer**: Expand window with `right` pointer until all characters of target are found. Then shrink with `left` pointer to minimize the window while remaining valid. Keep track of the minimum window seen.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1) (26/128 character hash map)

#### Q5: Kadane's algorithm
- **Answer**: Iterate through the array keeping a running sum. If the running sum drops below zero, reset it to zero. Update the global maximum at each step.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q6: Subarray vs Subsequence vs Subset
- **Answer**: Subarray is a contiguous slice of an array. Subsequence is derived by deleting some elements without changing the order of the remaining elements. Subset is any combination of elements, regardless of order.
- **Time Complexity**: N/A
- **Space Complexity**: N/A

#### Q7: First missing positive (Cyclic Sort)
- **Answer**: Place each number `i` at index `i-1` if `1 <= i <= N`. Scan the array again; the first index `j` where `nums[j] != j + 1` gives the missing positive `j + 1`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q8: Anagram detection without sorting
- **Answer**: Use a frequency array of size 26. Increment counts for the first string and decrement for the second. If all counts are zero at the end, they are anagrams.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q9: Product of Array Except Self (prefix/suffix)
- **Answer**: Compute prefix products traversing left to right. Then traverse right to left computing suffix products, multiplying them with the prefix products in the result array to save space.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1) (excluding output array)

#### Q10: Subarray Sum Equals K (prefix sum + hash map)
- **Answer**: Maintain a running prefix sum and a hash map of `prefix_sum -> count`. At each step, if `current_sum - K` exists in the hash map, add its count to the result.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q11: Longest Consecutive Sequence O(N)
- **Answer**: Store all numbers in a HashSet. Iterate through the set; if `num - 1` is not in the set, it's the start of a sequence. Count upwards to find the length of the sequence.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q12: Rotate Array 3-reversal trick
- **Answer**: To rotate an array to the right by `k` steps: Reverse the entire array, reverse the first `k` elements, and finally reverse the remaining `N-k` elements.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q13: Two Sum O(N) hash map
- **Answer**: Iterate through the array. For each element, check if `target - element` is in the hash map. If so, return indices. Otherwise, add the element and its index to the map.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)
```python
def twoSum(nums, target):
    lookup = {}
    for i, num in enumerate(nums):
        if target - num in lookup:
            return [lookup[target - num], i]
        lookup[num] = i
```
```java
public int[] twoSum(int[] nums, int target) {
    Map<Integer, Integer> map = new HashMap<>();
    for (int i = 0; i < nums.length; i++) {
        if (map.containsKey(target - nums[i])) return new int[]{map.get(target - nums[i]), i};
        map.put(nums[i], i);
    }
    return new int[]{};
}
```

#### Q14: Group Anagrams 26-char tuple key
- **Answer**: Map each string to a character count tuple (or frequency string) of length 26. Use this tuple as the key in a hash map, appending original strings to the corresponding list.
- **Time Complexity**: O(N * K) where K is max string length.
- **Space Complexity**: O(N * K)

#### Q15: Top K Frequent Elements bucket sort O(N)
- **Answer**: Count frequencies using a hash map. Create an array of lists `buckets` where index represents frequency. Iterate backwards from max frequency to gather top K elements.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q16: Valid Palindrome two pointers
- **Answer**: Use left and right pointers. Skip non-alphanumeric characters. Compare characters (lowercased) at both pointers. If they differ, return false; otherwise, move pointers inward.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q17: Container With Most Water proof
- **Answer**: Use two pointers at the ends of the array. The area is limited by the shorter line. To find a potentially larger area, always move the pointer pointing to the shorter line inward.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q18: 3Sum O(N^2) sort + two pointers
- **Answer**: Sort the array. Iterate `i` from 0 to N-2. Use two pointers (`left=i+1`, `right=N-1`) to find pairs that sum to `-nums[i]`. Skip duplicates to avoid duplicate triplets.
- **Time Complexity**: O(N^2)
- **Space Complexity**: O(1) or O(N) depending on sorting algorithm.

#### Q19: 4Sum general reduction
- **Answer**: Sort the array. Use two nested loops for the first two numbers. Use two pointers for the remaining two. Skip duplicates at every level to ensure unique quadruplets.
- **Time Complexity**: O(N^3)
- **Space Complexity**: O(1)

#### Q20: Majority Element Boyer-Moore voting
- **Answer**: Maintain a candidate and a count. If count is 0, candidate = current element. If current == candidate, increment count, else decrement. The surviving candidate is the majority element.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

### Section 2: Linked Lists (Q21–Q40)

#### Q21: Reverse Linked List iterative + recursive
- **Answer**: Iterative: Maintain `prev`, `curr`, `next`. `curr.next = prev`, shift pointers forward. Recursive: recurse to end, make `head.next.next = head`, `head.next = None`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1) iterative, O(N) recursive stack
```python
def reverseList(head):
    prev, curr = None, head
    while curr:
        nxt = curr.next
        curr.next = prev
        prev = curr
        curr = nxt
    return prev
```
```java
public ListNode reverseList(ListNode head) {
    ListNode prev = null, curr = head;
    while (curr != null) {
        ListNode nxt = curr.next;
        curr.next = prev;
        prev = curr;
        curr = nxt;
    }
    return prev;
}
```

#### Q22: Merge Two Sorted Lists dummy node
- **Answer**: Use a dummy head. Compare nodes from both lists, attach the smaller node to the merged list, and advance the respective pointer. Attach any remaining nodes at the end.
- **Time Complexity**: O(N + M)
- **Space Complexity**: O(1)

#### Q23: Linked List Cycle I Floyd detection proof
- **Answer**: Use a slow pointer (1 step) and a fast pointer (2 steps). If there is a cycle, the fast pointer will eventually lap the slow pointer and they will meet. If fast reaches null, there is no cycle.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q24: Linked List Cycle II cycle entry proof
- **Answer**: Find intersection using Floyd's cycle detection. Then reset one pointer to the head and move both pointers 1 step at a time; they will meet at the cycle's starting node.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q25: Find intersection of two linked lists
- **Answer**: Two pointers start at the heads of lists A and B. When a pointer reaches the end, redirect it to the head of the other list. They will eventually meet at the intersection node.
- **Time Complexity**: O(N + M)
- **Space Complexity**: O(1)

#### Q26: Remove Nth from end (fast/slow gap)
- **Answer**: Use a dummy node. Advance a fast pointer N steps ahead. Then move both fast and slow pointers until fast reaches the end. Slow will be just before the target node. `slow.next = slow.next.next`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q27: Reorder List (find middle + reverse + merge)
- **Answer**: 1) Find middle using slow/fast pointers. 2) Reverse the second half of the list. 3) Merge the two halves by alternating nodes.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q28: Copy List with Random Pointer (hash map)
- **Answer**: Two passes. First pass: create deep copies of all nodes and store mapping `original -> copy` in a hash map. Second pass: assign `next` and `random` pointers for the copies using the hash map.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q29: Add Two Numbers (digit carry)
- **Answer**: Use a dummy node. Traverse both lists simultaneously, adding values and a `carry`. Create a new node with `sum % 10` and update `carry = sum / 10`. Handle trailing carry.
- **Time Complexity**: O(max(N, M))
- **Space Complexity**: O(max(N, M))

#### Q30: LRU Cache (Hash + Doubly Linked List O(1))
- **Answer**: Hash map maps keys to DLL nodes for O(1) access. DLL maintains order of usage (head = MRU, tail = LRU). Move to head on access/update. Remove from tail on eviction.
- **Time Complexity**: O(1) get and put
- **Space Complexity**: O(Capacity)
```python
class Node:
    def __init__(self, key, val):
        self.key, self.val = key, val
        self.prev = self.next = None

class LRUCache:
    def __init__(self, capacity: int):
        self.cap = capacity
        self.cache = {}
        self.left, self.right = Node(0, 0), Node(0, 0)
        self.left.next, self.right.prev = self.right, self.left
    
    def _remove(self, node):
        node.prev.next, node.next.prev = node.next, node.prev
        
    def _insert(self, node):
        prev, nxt = self.right.prev, self.right
        prev.next = nxt.prev = node
        node.prev, node.next = prev, nxt

    def get(self, key: int) -> int:
        if key in self.cache:
            self._remove(self.cache[key])
            self._insert(self.cache[key])
            return self.cache[key].val
        return -1

    def put(self, key: int, value: int) -> None:
        if key in self.cache:
            self._remove(self.cache[key])
        self.cache[key] = Node(key, value)
        self._insert(self.cache[key])
        if len(self.cache) > self.cap:
            lru = self.left.next
            self._remove(lru)
            del self.cache[lru.key]
```

#### Q31: Min Stack O(1) twin stack
- **Answer**: Maintain two stacks. One normal stack for elements, and one `min_stack` that stores the minimum element seen so far at that stack level.
- **Time Complexity**: O(1) for all operations
- **Space Complexity**: O(N)

#### Q32: Flatten Multilevel Doubly Linked List
- **Answer**: Perform a DFS (or use a stack). When a node has a child, insert the flattened child list between the node and `node.next`. Update all `prev` and `next` pointers accordingly.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1) iterative, O(N) recursive stack

#### Q33: Palindrome Linked List O(1) space
- **Answer**: Find the middle using slow/fast pointers. Reverse the second half. Compare the first half and the reversed second half. (Optional: restore the list).
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q34: Merge K Sorted Lists (heap approach)
- **Answer**: Push the head node of each list into a min-heap. Pop the minimum node, append it to the result, and push `node.next` into the heap. Repeat until heap is empty.
- **Time Complexity**: O(N log K)
- **Space Complexity**: O(K)

#### Q35: Sort List (merge sort O(N log N) O(1) space)
- **Answer**: Use bottom-up merge sort. Iteratively merge lists of size 1, 2, 4, etc. Keep track of the tail to append the newly merged segments.
- **Time Complexity**: O(N log N)
- **Space Complexity**: O(1)

#### Q36: Rotate List by K
- **Answer**: Find the length and make the list circular by connecting tail to head. Find the new tail at `length - k % length - 1`, break the circle, and return the new head.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q37: Partition List around value
- **Answer**: Create two dummy heads: `before` (nodes < x) and `after` (nodes >= x). Traverse the original list, assigning nodes to the respective list. Merge `before` with `after.next`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q38: Swap Nodes in Pairs
- **Answer**: Use a dummy head. Traverse with a pointer `prev`. For the next two nodes `first` and `second`, wire `prev.next = second`, `first.next = second.next`, and `second.next = first`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q39: Reverse Nodes in K-Group
- **Answer**: Count nodes to ensure there are `k` nodes left. Reverse `k` nodes using the standard reversal logic. Recursively or iteratively connect the reversed segment with the rest of the list.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1) iterative

#### Q40: Delete Node without head reference
- **Answer**: Copy the value of the next node into the current node (`node.val = node.next.val`). Delete the next node by updating the pointer (`node.next = node.next.next`).
- **Time Complexity**: O(1)
- **Space Complexity**: O(1)

### Section 3: Stacks, Queues & Monotonic Structures (Q41–Q60)

#### Q41: Valid Parentheses
- **Answer**: Use a stack. Push opening brackets. On encountering a closing bracket, pop from the stack and check if it matches the corresponding opening bracket.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q42: Daily Temperatures (monotonic stack)
- **Answer**: Maintain a monotonically decreasing stack of indices. For each temperature, if it's warmer than the temp at the index on the top of the stack, pop the stack and calculate the difference in indices.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)
```python
def dailyTemperatures(temperatures):
    res = [0] * len(temperatures)
    stack = []
    for i, t in enumerate(temperatures):
        while stack and t > temperatures[stack[-1]]:
            idx = stack.pop()
            res[idx] = i - idx
        stack.append(i)
    return res
```

#### Q43: Largest Rectangle in Histogram (monotonic stack proof)
- **Answer**: Maintain a stack of strictly increasing bar indices. When a smaller bar is encountered, pop elements to calculate areas (height of popped * width between current index and new top of stack).
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q44: Sliding Window Maximum (monotonic deque)
- **Answer**: Use a deque storing indices. Keep it strictly decreasing by removing smaller elements from the back. Remove elements outside the window from the front. Front is always the max.
- **Time Complexity**: O(N)
- **Space Complexity**: O(K)

#### Q45: Evaluate Reverse Polish Notation
- **Answer**: Iterate tokens. Push numbers to stack. When an operator is found, pop the top two numbers, apply the operator, and push the result back to the stack.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q46: Basic Calculator II (precedence handling)
- **Answer**: Use a stack to track numbers to be added. On `*` or `/`, pop the last number, calculate, and push the result. On `-`, push negative. Sum the stack at the end.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q47: Implement Queue using Stacks (amortized O(1))
- **Answer**: Use two stacks: `push_stack` and `pop_stack`. Push to `push_stack`. On pop/peek, if `pop_stack` is empty, transfer all elements from `push_stack` to `pop_stack`.
- **Time Complexity**: Amortized O(1) push/pop
- **Space Complexity**: O(N)

#### Q48: Implement Stack using Queues
- **Answer**: Use a single queue. On push, enqueue the element, then dequeue and enqueue all previous elements to reverse the order so the newly added element is at the front.
- **Time Complexity**: O(N) push, O(1) pop
- **Space Complexity**: O(N)

#### Q49: Decode String (nested brackets with stack)
- **Answer**: Push characters onto a stack. When `]` is found, pop to build the string inside the brackets, pop the `[`, pop the number, and push the repeated string back.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q50: Asteroid Collision
- **Answer**: Iterate asteroids. If moving right (+), push to stack. If moving left (-), while stack top is positive and smaller, pop stack. If top equals magnitude, pop both. If stack empty or top is negative, push the left-moving asteroid.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q51: Next Greater Element
- **Answer**: Use a monotonically decreasing stack mapping elements to their next greater element in a hash map. Build the result for `nums1` using the map.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q52: Remove K Digits (greedy monotonic stack)
- **Answer**: Iterate digits. While `k > 0`, stack is not empty, and the current digit is smaller than the stack top, pop the stack and decrement `k`. Push current digit. Handle leading zeros.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q53: Trapping Rain Water (stack variant)
- **Answer**: Keep a monotonically decreasing stack of indices. When a taller bar is found, pop the stack, the new top is the left boundary, calculate bounded water, and repeat.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q54: Car Fleet problem
- **Answer**: Sort cars by starting position descending. Calculate time to destination `(target - pos) / speed`. If a car takes longer than the fleet ahead of it, it forms a new fleet.
- **Time Complexity**: O(N log N)
- **Space Complexity**: O(N)

#### Q55: Online Stock Span
- **Answer**: Use a monotonically decreasing stack storing `(price, span)`. Pop elements smaller or equal to current price, adding their spans to the current span.
- **Time Complexity**: Amortized O(1) per call
- **Space Complexity**: O(N)

#### Q56: Circular Deque design
- **Answer**: Use an array of fixed size with `front` and `rear` pointers. Manage boundaries with modulo arithmetic `(ptr + 1) % capacity` or `(ptr - 1 + capacity) % capacity`.
- **Time Complexity**: O(1) operations
- **Space Complexity**: O(K)

#### Q57: Jump Game II greedy
- **Answer**: Track the `farthest` reach and `current_end` of jumps. When iterating reaches `current_end`, increment jump count and update `current_end = farthest`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q58: Backspace String Compare O(1) space
- **Answer**: Iterate both strings backwards. Maintain a skip count for `#`. Skip characters if skip count > 0. Compare valid characters.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q59: Maximum Frequency Stack
- **Answer**: Hash map for element frequencies. Hash map of stacks grouping elements by frequency. Keep track of `max_freq`. Pop from the stack at `max_freq`.
- **Time Complexity**: O(1) operations
- **Space Complexity**: O(N)

#### Q60: Score of Parentheses
- **Answer**: Maintain a score stack or just use depth calculation: when encountering `()`, add `1 << depth` to the total score.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1) (with depth counting)

### Section 4: Binary Search (Q61–Q80)

#### Q61: Binary search invariant and overflow-safe midpoint
- **Answer**: Loop while `low <= high`. Calculate mid as `low + (high - low) / 2` to avoid integer overflow. Update bounds to `mid + 1` or `mid - 1`.
- **Time Complexity**: O(log N)
- **Space Complexity**: O(1)

#### Q62: Search in Rotated Sorted Array
- **Answer**: Identify the sorted half. If `nums[low] <= nums[mid]`, left is sorted: check if target is in left range. Otherwise, right is sorted: check if target is in right range.
- **Time Complexity**: O(log N)
- **Space Complexity**: O(1)
```python
def search(nums, target):
    l, r = 0, len(nums) - 1
    while l <= r:
        m = l + (r - l) // 2
        if nums[m] == target: return m
        if nums[l] <= nums[m]:
            if nums[l] <= target < nums[m]: r = m - 1
            else: l = m + 1
        else:
            if nums[m] < target <= nums[r]: l = m + 1
            else: r = m - 1
    return -1
```

#### Q63: Find Minimum in Rotated Sorted Array
- **Answer**: Compare `mid` with `right`. If `nums[mid] > nums[right]`, the min is in the right half (`low = mid + 1`). Otherwise, the min is in the left half including mid (`high = mid`).
- **Time Complexity**: O(log N)
- **Space Complexity**: O(1)

#### Q64: Binary Search on Solution Space (Koko Eating Bananas)
- **Answer**: Search space for eating speed `k` is `1` to `max(piles)`. Use binary search to find the minimum `k` such that Koko can eat all bananas within `h` hours.
- **Time Complexity**: O(N log(Max(P)))
- **Space Complexity**: O(1)

#### Q65: Median of Two Sorted Arrays O(log min(M,N))
- **Answer**: Binary search on the smaller array to partition both arrays such that all elements on the left are <= elements on the right. Find max of lefts and min of rights.
- **Time Complexity**: O(log(min(M, N)))
- **Space Complexity**: O(1)

#### Q66: Find Peak Element
- **Answer**: Compare `mid` to `mid+1`. If `nums[mid] < nums[mid+1]`, the peak is to the right (`low = mid + 1`). Else, it is to the left or is mid (`high = mid`).
- **Time Complexity**: O(log N)
- **Space Complexity**: O(1)

#### Q67: Search a 2D Matrix
- **Answer**: Treat the 2D matrix as a 1D sorted array of length `M * N`. Index mapping: `row = mid / cols`, `col = mid % cols`. Run standard binary search.
- **Time Complexity**: O(log(M * N))
- **Space Complexity**: O(1)

#### Q68: Time Based Key-Value Store
- **Answer**: Store values as a list of `(timestamp, value)` tuples in a hash map for each key. Since timestamps strictly increase, use binary search to find the floor of the target timestamp.
- **Time Complexity**: O(log N) for get
- **Space Complexity**: O(N)

#### Q69: Split Array Largest Sum
- **Answer**: Binary search the answer between `max(nums)` and `sum(nums)`. For a given `mid`, check if array can be split into <= `k` subarrays such that no sum exceeds `mid`.
- **Time Complexity**: O(N log(Sum - Max))
- **Space Complexity**: O(1)

#### Q70: Capacity to Ship Packages Within D Days
- **Answer**: Binary search the capacity between `max(weights)` and `sum(weights)`. Greedily sum weights; if sum > mid, start a new day. Check if total days <= D.
- **Time Complexity**: O(N log(Sum - Max))
- **Space Complexity**: O(1)

#### Q71: Minimum Number of Days to Make M Bouquets
- **Answer**: Binary search the day between `min(bloomDay)` and `max(bloomDay)`. Iterate array checking if adjacent flowers are bloomed to form `k` flowers for `m` bouquets.
- **Time Complexity**: O(N log(Max - Min))
- **Space Complexity**: O(1)

#### Q72: Find K Closest Elements
- **Answer**: Binary search to find the start of the window of size `k`. Compare elements `A[mid]` and `A[mid + k]` relative to `x` to shift the bounds.
- **Time Complexity**: O(log(N - K) + K)
- **Space Complexity**: O(1) (excluding result)

#### Q73: Search in sorted array of unknown size
- **Answer**: Find bounds first. Initialize `high = 1`, while `reader.get(high) < target`, double `high`. Then perform binary search between `high/2` and `high`.
- **Time Complexity**: O(log T) where T is target index
- **Space Complexity**: O(1)

#### Q74: Find First and Last Position (two binary searches)
- **Answer**: Perform binary search twice. First time, bias left (`if target == nums[mid]: high = mid - 1`) to find the first occurrence. Second time, bias right to find the last occurrence.
- **Time Complexity**: O(log N)
- **Space Complexity**: O(1)

#### Q75: Count Negatives in Sorted Matrix
- **Answer**: Start at the bottom-left or top-right. If the element is negative, all elements to its right are negative (count them and move up). Else, move right.
- **Time Complexity**: O(M + N)
- **Space Complexity**: O(1)

#### Q76: Single Element in Sorted Array
- **Answer**: Binary search. Ensure `mid` is even. If `nums[mid] == nums[mid+1]`, the single element is in the right half (`low = mid + 2`). Else, left half (`high = mid`).
- **Time Complexity**: O(log N)
- **Space Complexity**: O(1)

#### Q77: Minimum Eating Speed (Koko variant)
- **Answer**: Identical to Q64. Use binary search on the speed `k`.
- **Time Complexity**: O(N log M)
- **Space Complexity**: O(1)

#### Q78: Aggressive Cows (SPOJ classic)
- **Answer**: Sort stall positions. Binary search the answer (minimum distance). For a `mid`, greedily place cows in stalls ensuring gap >= `mid`.
- **Time Complexity**: O(N log N)
- **Space Complexity**: O(1)

#### Q79: Random Pick with Weight (binary search on prefix sum)
- **Answer**: Compute prefix sums of weights. Pick a random number between `1` and `total_sum`. Binary search the prefix sums to find the corresponding index.
- **Time Complexity**: O(N) init, O(log N) pick
- **Space Complexity**: O(N)

#### Q80: Find Duplicate Number (binary search on count)
- **Answer**: Binary search the value space `[1, N]`. For `mid`, count numbers <= `mid`. If count > `mid`, duplicate is <= `mid`, else > `mid`. (Can also use Floyd's cycle detection).
- **Time Complexity**: O(N log N)
- **Space Complexity**: O(1)

### Section 5: Trees & Tries (Q81–Q100)

#### Q81: Tree traversals (inorder/preorder/postorder) iterative and recursive
- **Answer**: Recursive is trivial via DFS. Iterative inorder: push lefts to stack, pop, process, move right.
- **Time Complexity**: O(N)
- **Space Complexity**: O(H) where H is height
```python
def inorderTraversal(root):
    res, stack = [], []
    curr = root
    while curr or stack:
        while curr:
            stack.append(curr)
            curr = curr.left
        curr = stack.pop()
        res.append(curr.val)
        curr = curr.right
    return res
```

#### Q82: Level Order Traversal BFS
- **Answer**: Use a queue. Push root. While queue is not empty, process all nodes at the current level (using initial queue size), collect values, and push their children.
- **Time Complexity**: O(N)
- **Space Complexity**: O(W) where W is max width

#### Q83: Maximum Depth / Minimum Depth
- **Answer**: Max Depth: Return `1 + max(depth(left), depth(right))`. Min Depth: If left or right is null, return `1 + depth of non-null child`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(H)

#### Q84: Diameter of Binary Tree (global max update)
- **Answer**: Keep a global `max_diameter`. DFS returns height of a node. Update `max_diameter = max(max_diameter, left_height + right_height)`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(H)

#### Q85: Lowest Common Ancestor of Binary Tree
- **Answer**: DFS. Return root if it matches P or Q. Search left and right. If both return non-null, root is LCA. If only one returns non-null, propagate it up.
- **Time Complexity**: O(N)
- **Space Complexity**: O(H)

#### Q86: Lowest Common Ancestor of BST
- **Answer**: Use BST properties. If both P and Q are smaller than root, go left. If both are greater, go right. Otherwise, the current root is the LCA.
- **Time Complexity**: O(H)
- **Space Complexity**: O(1) iterative

#### Q87: Validate BST (range propagation)
- **Answer**: DFS passing down `min_val` and `max_val`. A node's value must be strictly between these limits. For left child, `max_val = node.val`. For right child, `min_val = node.val`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(H)

#### Q88: Invert Binary Tree
- **Answer**: Swap the left and right children of the root, then recursively invert the left and right subtrees.
- **Time Complexity**: O(N)
- **Space Complexity**: O(H)

#### Q89: Symmetric Tree
- **Answer**: Recursive function comparing two nodes `t1` and `t2`. Return true if both are null, false if one is null, or false if values differ. Recurse: `compare(t1.left, t2.right)` and `compare(t1.right, t2.left)`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(H)

#### Q90: Binary Tree Maximum Path Sum
- **Answer**: DFS returns max path sum without branching (node + max(left, right, 0)). Update a global max with `node.val + max(left, 0) + max(right, 0)`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(H)

#### Q91: Serialize and Deserialize Binary Tree
- **Answer**: Serialize: Preorder DFS, append "null" for null nodes. Deserialize: Split string by comma, use an iterator, pop next value; if "null", return None, else build Node and recurse for left and right.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q92: Construct Binary Tree from Preorder and Inorder
- **Answer**: Preorder's first element is the root. Find this root in inorder array to divide into left and right subtrees. Recursively build subtrees. Use a hash map for O(1) inorder lookups.
- **Time Complexity**: O(N)
- **Space Complexity**: O(N)

#### Q93: Binary Tree Right Side View
- **Answer**: BFS level order traversal. At each level, append the last processed node's value to the result array.
- **Time Complexity**: O(N)
- **Space Complexity**: O(W) (queue size)

#### Q94: Count Complete Tree Nodes
- **Answer**: If height of left child == height of right child, left is perfect; nodes = `2^h - 1` + right count. Else, right is perfect; nodes = `2^(h-1) - 1` + left count.
- **Time Complexity**: O(log^2 N)
- **Space Complexity**: O(log N) recursive stack

#### Q95: Flatten Binary Tree to Linked List
- **Answer**: Reverse post-order traversal (right, left, root). Keep track of `prev` node. Set `node.right = prev`, `node.left = None`, update `prev = node`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(H)

#### Q96: Implement Trie (insert, search, startsWith)
- **Answer**: TrieNode with a dictionary of children and `is_word` boolean. Search iterates character by character traversing the dictionary.
- **Time Complexity**: O(L) for operations (L is word length)
- **Space Complexity**: O(N * L) total characters

#### Q97: Word Search II (Trie + backtracking)
- **Answer**: Build a Trie from words. Backtrack (DFS) on the matrix, traversing the Trie simultaneously. Mark cells visited. Optimize by removing found words from Trie.
- **Time Complexity**: O(M * N * 4^L)
- **Space Complexity**: O(K) Trie size

#### Q98: Design Add and Search Words
- **Answer**: Trie implementation. For `search` with `.`, iterate all possible children and recursively search the remaining string.
- **Time Complexity**: Add O(L), Search O(26^L) worst case for wildcards
- **Space Complexity**: O(N * L)

#### Q99: Replace Words with Trie prefix
- **Answer**: Build a Trie of the dictionary words. For each word in the sentence, traverse the Trie; return the prefix once `is_word` is true.
- **Time Complexity**: O(N * L)
- **Space Complexity**: O(N * L)

#### Q100: Longest Word in Dictionary using Trie
- **Answer**: Build Trie. DFS/BFS the Trie prioritizing nodes with `is_word` == true. Or sort dictionary lexicographically and check if prefix `word[:-1]` exists in a set.
- **Time Complexity**: O(N * L)
- **Space Complexity**: O(N * L)

### Section 6: Heaps & Graphs (Q101–Q120)

#### Q101: Heapify O(N) proof vs N insertions O(N log N)
- **Answer**: Heapify builds a heap bottom-up. Most nodes are at the bottom and sift down 0 or 1 levels. Sum of work is `N * 1/2 * 1 + N * 1/4 * 2...` which converges to O(N). Insertions top-down take O(N log N).
- **Time Complexity**: O(N)
- **Space Complexity**: O(1) in-place

#### Q102: Kth Largest Element in Array (min-heap size K)
- **Answer**: Maintain a min-heap of size K. Iterate array, pushing elements. If heap size > K, pop. The root is the Kth largest.
- **Time Complexity**: O(N log K)
- **Space Complexity**: O(K)

#### Q103: Top K Frequent Elements (min-heap or bucket sort)
- **Answer**: Covered in Q15. Alternative: Count frequencies, push `(freq, num)` to min-heap of size K.
- **Time Complexity**: O(N log K)
- **Space Complexity**: O(N)

#### Q104: Find Median from Data Stream (two heaps)
- **Answer**: Maintain a max-heap for the smaller half and a min-heap for the larger half. Rebalance if lengths differ by more than 1. Median is the top of the larger heap, or avg of both tops.
- **Time Complexity**: O(log N) insert, O(1) find
- **Space Complexity**: O(N)

#### Q105: K Closest Points to Origin
- **Answer**: Use a max-heap of size K storing `(distance, x, y)`. When size > K, pop. Leftover elements are the K closest.
- **Time Complexity**: O(N log K)
- **Space Complexity**: O(K)

#### Q106: Merge K Sorted Lists
- **Answer**: Covered in Q34. Push head of each list to min-heap, pop min, push next node.

#### Q107: Task Scheduler (idle time calculation)
- **Answer**: Count max frequency task. The formula is `(max_freq - 1) * (n + 1) + count_of_max_freq_tasks`. Return `max(len(tasks), formula)`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1) (26 letters)

#### Q108: Design Twitter (top K feed using heap)
- **Answer**: Keep mapping of user to followees, and user to tweets `(timestamp, tweet_id)`. For feed, collect latest tweets from user and followees, use min-heap (size 10) to find 10 most recent.
- **Time Complexity**: O(N log 10) -> O(N)
- **Space Complexity**: O(U + T)

#### Q109: IPO Maximize Capital (two heaps greedy)
- **Answer**: Min-heap for capital requirements, max-heap for profits. Move projects from min-heap to max-heap if their capital <= current capital. Pop max-heap to do project.
- **Time Complexity**: O(N log N)
- **Space Complexity**: O(N)

#### Q110: Number of Islands (BFS/DFS grid)
- **Answer**: Iterate grid. When finding a '1', increment island count and trigger DFS/BFS to mark all connected '1's as '0' (visited).
- **Time Complexity**: O(M * N)
- **Space Complexity**: O(M * N)
```python
def numIslands(grid):
    def dfs(i, j):
        if 0 <= i < len(grid) and 0 <= j < len(grid[0]) and grid[i][j] == '1':
            grid[i][j] = '0'
            list(map(dfs, (i+1, i-1, i, i), (j, j, j+1, j-1)))
    
    count = 0
    for i in range(len(grid)):
        for j in range(len(grid[0])):
            if grid[i][j] == '1':
                dfs(i, j)
                count += 1
    return count
```

#### Q111: Clone Graph (DFS + hash map)
- **Answer**: Hash map `old_node -> new_node`. DFS: if node in map, return map[node]. Else, create clone, put in map, iterate neighbors appending `dfs(neighbor)` to clone's neighbors.
- **Time Complexity**: O(V + E)
- **Space Complexity**: O(V)

#### Q112: Course Schedule I (cycle detection)
- **Answer**: Graph cycle detection. Use an array to track states: 0=unvisited, 1=visiting, 2=visited. If DFS encounters state 1, cycle exists.
- **Time Complexity**: O(V + E)
- **Space Complexity**: O(V + E)

#### Q113: Course Schedule II (topological sort Kahn's)
- **Answer**: Compute in-degrees. Enqueue nodes with in-degree 0. While queue not empty, pop node, append to result, decrement in-degree of neighbors. If neighbor in-degree becomes 0, enqueue.
- **Time Complexity**: O(V + E)
- **Space Complexity**: O(V + E)

#### Q114: Pacific Atlantic Water Flow (reverse BFS)
- **Answer**: BFS/DFS from the Pacific border and Atlantic border inwards to find reachable cells. Return cells that are reachable by both oceans.
- **Time Complexity**: O(M * N)
- **Space Complexity**: O(M * N)

#### Q115: Word Ladder (BFS shortest path)
- **Answer**: Treat words as nodes. Edges exist if they differ by 1 char. Run BFS from `beginWord`. Maintain visited set. Return levels taken to reach `endWord`.
- **Time Complexity**: O(M^2 * N) M = word length
- **Space Complexity**: O(M^2 * N)

#### Q116: Network Delay Time (Dijkstra)
- **Answer**: Shortest path. Use a min-heap storing `(accumulated_time, node)`. Pop node, if unvisited, mark visited, relax edges pushing to heap. Max of shortest paths is the answer.
- **Time Complexity**: O(E log V)
- **Space Complexity**: O(V + E)

#### Q117: Cheapest Flights Within K Stops (Bellman-Ford)
- **Answer**: Run Bellman-Ford algorithm for `K + 1` iterations. Update a `prices` array based on a temporary snapshot of the previous iteration's array to avoid exceeding K stops.
- **Time Complexity**: O(E * K)
- **Space Complexity**: O(V)

#### Q118: Find the Town Judge
- **Answer**: Calculate net trust (in-degree minus out-degree). The town judge will have an out-degree of 0 and an in-degree of N-1 (net trust = N-1).
- **Time Complexity**: O(E)
- **Space Complexity**: O(V)

#### Q119: Redundant Connection (Union-Find)
- **Answer**: Use Disjoint Set Union (DSU) with path compression and union by rank. Iterate edges, union nodes. If an edge connects nodes already in the same set, it's redundant.
- **Time Complexity**: O(E * alpha(V)) roughly O(E)
- **Space Complexity**: O(V)

#### Q120: Accounts Merge (Union-Find)
- **Answer**: Map emails to IDs. DSU to group IDs of emails in the same account. Group emails by parent ID. Sort and append the account name.
- **Time Complexity**: O(N log N) sorting emails
- **Space Complexity**: O(N)

### Section 7: Dynamic Programming (Q121–Q150)

#### Q121: Climbing Stairs Fibonacci DP
- **Answer**: `dp[i] = dp[i-1] + dp[i-2]`. Optimize space by keeping only the last two values.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)
```python
def climbStairs(n):
    a, b = 1, 1
    for _ in range(n - 1):
        a, b = b, a + b
    return b
```

#### Q122: House Robber I and II
- **Answer**: House Robber I: `dp[i] = max(dp[i-1], dp[i-2] + nums[i])`. House Robber II: Array is circular. Run HR1 twice: `HR1(nums[:-1])` and `HR1(nums[1:])`, return the max.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q123: Longest Palindromic Substring (expand around center)
- **Answer**: For each character (and pair of characters), expand outwards while characters match. Keep track of the maximum length palindrome found.
- **Time Complexity**: O(N^2)
- **Space Complexity**: O(1)

#### Q124: Palindromic Substrings count
- **Answer**: Similar to Q123. Expand around each center (single char and pair) and add 1 to the count for every successful palindrome expansion.
- **Time Complexity**: O(N^2)
- **Space Complexity**: O(1)

#### Q125: Decode Ways
- **Answer**: `dp[i]` is number of ways to decode up to `i`. If `s[i] != '0'`, `dp[i] += dp[i-1]`. If `10 <= int(s[i-1:i+1]) <= 26`, `dp[i] += dp[i-2]`.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1) (optimizable from O(N))

#### Q126: Coin Change (bottom-up DP)
- **Answer**: `dp[amount] = min(dp[amount], dp[amount - coin] + 1)`. Initialize DP array with `infinity`, except `dp[0] = 0`.
- **Time Complexity**: O(S * C)
- **Space Complexity**: O(S)

#### Q127: Maximum Product Subarray
- **Answer**: Track `cur_max` and `cur_min` as multiplying by a negative swaps the max and min. Update variables for each element and keep global max.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q128: Word Break
- **Answer**: `dp[i]` is true if `s[:i]` can be segmented. For each `i`, check if `dp[j]` is true and `s[j:i]` is in the dictionary.
- **Time Complexity**: O(N^3)
- **Space Complexity**: O(N)

#### Q129: Longest Increasing Subsequence O(N log N) patience sort
- **Answer**: Build array `sub`. For each num, binary search its insertion point in `sub`. Replace if exists, else append. Length of `sub` is the LIS length.
- **Time Complexity**: O(N log N)
- **Space Complexity**: O(N)

#### Q130: Partition Equal Subset Sum (0/1 Knapsack)
- **Answer**: Target sum is `sum(nums) / 2`. DP array to track achievable sums. DP logic: iterate numbers, update sums array backwards to avoid using same element twice.
- **Time Complexity**: O(N * Sum)
- **Space Complexity**: O(Sum)
```python
def canPartition(nums):
    total = sum(nums)
    if total % 2 != 0: return False
    target = total // 2
    dp = set([0])
    for num in nums:
        dp.update([x + num for x in dp if x + num <= target])
    return target in dp
```

#### Q131: Target Sum (count ways with +/-)
- **Answer**: Subset sum problem: find subset P such that `sum(P) = (Target + sum(nums)) / 2`. Then apply 0/1 Knapsack to find number of ways.
- **Time Complexity**: O(N * Sum)
- **Space Complexity**: O(Sum)

#### Q132: Unique Paths I and II
- **Answer**: `dp[i][j] = dp[i-1][j] + dp[i][j-1]`. If obstacle grid (II), set `dp[i][j] = 0` for obstacle cells.
- **Time Complexity**: O(M * N)
- **Space Complexity**: O(N) (optimized 1D array)

#### Q133: Edit Distance (Levenshtein)
- **Answer**: 2D DP. If chars match, `dp[i][j] = dp[i-1][j-1]`. Else `1 + min(insert(dp[i][j-1]), delete(dp[i-1][j]), replace(dp[i-1][j-1]))`.
- **Time Complexity**: O(M * N)
- **Space Complexity**: O(M * N) (can optimize to O(N))

#### Q134: Longest Common Subsequence
- **Answer**: 2D DP. If characters match, `dp[i][j] = 1 + dp[i-1][j-1]`. If not, `max(dp[i-1][j], dp[i][j-1])`.
- **Time Complexity**: O(M * N)
- **Space Complexity**: O(N)

#### Q135: Best Time to Buy and Sell Stock I-IV
- **Answer**: Store max profit at `k` transactions. Use DP state machines. States: hold stock, empty (sold). Or simpler: keep `min_price` and calculate max difference for I.
- **Time Complexity**: O(N * K)
- **Space Complexity**: O(K)

#### Q136: Jump Game I and II
- **Answer**: I: Greedily track max reachable index. Return true if reachable >= last index. II: Track `current_end` and `farthest_reach` to count minimum jumps.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q137: N-Queens (backtracking with diagonal sets)
- **Answer**: Backtrack row by row. Maintain three sets: `cols`, `posDiag` (r+c), `negDiag` (r-c). Place queen, add to sets, recurse, remove from sets.
- **Time Complexity**: O(N!)
- **Space Complexity**: O(N^2)

#### Q138: Combination Sum (backtracking)
- **Answer**: DFS. Try picking a number and stay at the same index (reusable), or skip the number and move to the next index. Stop if sum > target.
- **Time Complexity**: O(2^(target/min_val))
- **Space Complexity**: O(target/min_val)

#### Q139: Subsets (power set)
- **Answer**: DFS backtracking. At each element, make a choice to either include it in the current subset or exclude it. Add all constructed subsets to the result.
- **Time Complexity**: O(N * 2^N)
- **Space Complexity**: O(N)

#### Q140: Permutations (backtracking)
- **Answer**: Swap elements to generate permutations in place, or maintain a `path` and `visited` array. Backtrack when path length equals array length.
- **Time Complexity**: O(N * N!)
- **Space Complexity**: O(N)
```python
def permute(nums):
    res = []
    def backtrack(start):
        if start == len(nums):
            res.append(nums[:])
            return
        for i in range(start, len(nums)):
            nums[start], nums[i] = nums[i], nums[start]
            backtrack(start + 1)
            nums[start], nums[i] = nums[i], nums[start]
    backtrack(0)
    return res
```

#### Q141: Word Search (DFS backtracking)
- **Answer**: Iterate grid. If char matches first letter, launch DFS backtracking marking cell visited (temporarily replacing char with `#`), checking 4 directions.
- **Time Complexity**: O(M * N * 3^L)
- **Space Complexity**: O(L)

#### Q142: Letter Combinations of Phone Number
- **Answer**: Hash map of digits to letters. Backtrack: loop through letters of the current digit, add to string, recurse to next digit.
- **Time Complexity**: O(4^N * N)
- **Space Complexity**: O(N)

#### Q143: Palindrome Partitioning
- **Answer**: Backtracking. Partition string at index `i`. If `s[:i]` is a palindrome, add it to path, recurse on `s[i:]`.
- **Time Complexity**: O(N * 2^N)
- **Space Complexity**: O(N)

#### Q144: Generate Parentheses
- **Answer**: Backtracking. Add `(` if `open < n`. Add `)` if `close < open`. Base case: string length is `2 * n`.
- **Time Complexity**: O(4^N / sqrt(N)) Catalan number
- **Space Complexity**: O(N)

#### Q145: Burst Balloons
- **Answer**: DP with divide and conquer. `dp[i][j]` is max coins from bursting balloons between i and j. Pick a balloon `k` to burst *last*. `dp[i][j] = dp[i][k] + dp[k][j] + nums[i]*nums[k]*nums[j]`.
- **Time Complexity**: O(N^3)
- **Space Complexity**: O(N^2)

#### Q146: Regular Expression Matching
- **Answer**: 2D DP `dp[i][j]`. If chars match or `p` is '.', `dp = dp[i-1][j-1]`. If `*`, consider 0 occurrences (`dp[i][j-2]`) or 1+ occurrences if preceding char matches `s[i]`.
- **Time Complexity**: O(M * N)
- **Space Complexity**: O(M * N)

#### Q147: Wildcard Matching
- **Answer**: Similar to regex. DP `dp[i][j]`. `?` matches single character. `*` can match empty (`dp[i][j-1]`) or sequence (`dp[i-1][j]`).
- **Time Complexity**: O(M * N)
- **Space Complexity**: O(M * N)

#### Q148: Minimum Window Substring (DP review)
- **Answer**: Sliding window (Q4 repeated conceptual). Expand right, meet conditions. Shrink left to optimize.
- **Time Complexity**: O(N)
- **Space Complexity**: O(1)

#### Q149: Interleaving String
- **Answer**: 2D DP. `dp[i][j]` means `s1[:i]` and `s2[:j]` form `s3[:i+j]`. True if `dp[i-1][j]` and `s1` matches `s3`, or `dp[i][j-1]` and `s2` matches `s3`.
- **Time Complexity**: O(M * N)
- **Space Complexity**: O(N)

#### Q150: Russian Doll Envelopes
- **Answer**: Sort envelopes by width ascending, then height descending. The problem reduces to finding the Longest Increasing Subsequence (LIS) on the heights array.
- **Time Complexity**: O(N log N)
- **Space Complexity**: O(N)
```python
def maxEnvelopes(envelopes):
    envelopes.sort(key=lambda x: (x[0], -x[1]))
    dp = []
    for _, h in envelopes:
        import bisect
        idx = bisect.bisect_left(dp, h)
        if idx == len(dp):
            dp.append(h)
        else:
            dp[idx] = h
    return len(dp)
```
