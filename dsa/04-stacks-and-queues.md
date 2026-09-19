# DSA Track: Dual Python & Java
## Chapter 4: Stacks, Queues & Monotonic Stack Mastery

Stacks and Queues manage ordered access constraints. The **Monotonic Stack** and **Monotonic Queue** patterns are crucial for solving range extrema and histogram geometry problems in optimal $O(N)$ time.

---

### 1. Language Best Practices: Python & Java Collections

- **In Python**: Avoid using `list` as a FIFO queue because `list.pop(0)` is $O(N)$ (requires shifting all elements in memory). Always use **`collections.deque`** (Double-Ended Queue implemented as a doubly-linked list of blocks), providing strictly **$O(1)$** `append`, `appendleft`, `pop`, and `popleft`.
- **In Java**: Avoid legacy `java.util.Stack` because its methods are `synchronized` (thread contention overhead). Use **`java.util.ArrayDeque`** for both Stacks and Queues:
  ```java
  Deque<Integer> stack = new ArrayDeque<>(); // As Stack: push(), pop(), peek()
  Deque<Integer> queue = new ArrayDeque<>(); // As Queue: offer(), poll(), peek()
  ```

**Real-World Industry Examples**:
- **Compiler expression parsing**: Parsers like ANTLR use stacks to evaluate deeply nested expressions and abstract syntax trees via recursive descent or shunting-yard.
- **Undo/Redo systems**: VS Code and Photoshop manage states using two stacks. When you undo, current state pushes to the redo stack and pops from the undo stack.
- **Browser Call Stack & CPU Pipelines**: The V8 JS engine utilizes call stacks to track function execution context. CPU instruction pipelines use hardware-level queues to maintain instruction order.

> 💡 **Interview Tip**
> If you are asked to implement a Queue using Stacks, or a Stack using Queues, the interviewer is testing your understanding of LIFO vs FIFO properties. Always clarify your primary language's best queue implementation (`deque` for Python, `ArrayDeque` for Java).

> 📺 **Video References**
> - [NeetCode: Stack Patterns](https://www.youtube.com/watch?v=G0_I-ZF0S38)
> - [MIT 6.006: Stacks and Queues](https://www.youtube.com/watch?v=Zc54gFhdpLA&list=PLUl4u3cNGP61Oq3tWYp6V_F-5jb5L2iHb)

> 📖 **Must-Read Resources**
> - [Python `collections.deque` Documentation](https://docs.python.org/3/library/collections.html#collections.deque)
> - [Java `ArrayDeque` JavaDoc](https://docs.oracle.com/en/java/javase/17/docs/api/java.base/java/util/ArrayDeque.html)

---

### 2. The Monotonic Stack Pattern

A Monotonic Stack maintains elements in strict ascending or descending order.
- **Monotonic Decreasing Stack**: Finds the **Next Greater Element**. When a new element arrives that is larger than the stack top, we pop from the stack until the invariant is restored.
- **Why $O(N)$?**: Every element is pushed onto the stack exactly once and popped at most once. Total push/pop operations across the entire algorithm $\le 2N \implies \mathbf{O(N)}$.

```mermaid
graph TD
    Incoming["Incoming Element: 7"] --> Compare{"Compare with Stack Top: 4"}
    Compare -->|7 > 4 (Violates decreasing order)| Pop["Pop 4 -> Record 7 as Next Greater Element for 4!"]
    Pop --> CompareNext{"Compare with new Top: 6"}
    CompareNext -->|7 > 6| Pop2["Pop 6 -> Record 7 as Next Greater Element for 6!"]
    Pop2 --> Push["Push 7 onto Stack"]
```

> 💡 **Interview Tip**
> If a problem mentions "Next Greater Element", "Next Smaller Element", or asks for "Daily Temperatures", a Monotonic Stack is practically guaranteed to be the optimal $O(N)$ solution.

> 📺 **Video References**
> - [NeetCode: Monotonic Stack Explained](https://www.youtube.com/watch?v=Dq_ObZwTY_Q)
> - [William Fiset: Stacks](https://www.youtube.com/watch?v=2wVjt3vhGNc)

> 📖 **Must-Read Resources**
> - [GeeksForGeeks: Monotonic Stack Tutorial](https://www.geeksforgeeks.org/introduction-to-monotonic-stack-data-structure-and-algorithm-tutorials/)

---

### 3. Core Problems & Dual Implementations

#### Problem 1: Daily Temperatures (LeetCode 739)
Given an array of integers `temperatures`, return an array `answer` such that `answer[i]` is the number of days you have to wait after the $i^{\text{th}}$ day to get a warmer temperature.

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Monotonic Stack | $O(N)$ | $O(N)$ |

**Python 3 Implementation**:
```python
def daily_temperatures(temperatures: list[int]) -> list[int]:
    n = len(temperatures)
    result = [0] * n
    stack = []  # indices of decreasing temperatures

    for i, temp in enumerate(temperatures):
        while stack and temperatures[stack[-1]] < temp:
            prev_idx = stack.pop()
            result[prev_idx] = i - prev_idx
        stack.append(i)

    return result

if __name__ == "__main__":
    print(daily_temperatures([73,74,75,71,69,72,76,73])) # [1,1,4,2,1,1,0,0]
```

**Java 17 Implementation**:
```java
import java.util.ArrayDeque;
import java.util.Deque;
import java.util.Arrays;

public class DailyTemperatures {
    public int[] dailyTemperatures(int[] temperatures) {
        int n = temperatures.length;
        int[] result = new int[n];
        Deque<Integer> stack = new ArrayDeque<>(); // Stores indices

        for (int i = 0; i < n; i++) {
            while (!stack.isEmpty() && temperatures[stack.peek()] < temperatures[i]) {
                int prevIdx = stack.pop();
                result[prevIdx] = i - prevIdx;
            }
            stack.push(i);
        }
        return result;
    }
    
    public static void main(String[] args) {
        DailyTemperatures dt = new DailyTemperatures();
        System.out.println(Arrays.toString(dt.dailyTemperatures(new int[]{73,74,75,71,69,72,76,73})));
    }
}
```

---

#### Problem 2: Largest Rectangle in Histogram (LeetCode 84)
Given an array of integers `heights` representing the histogram's bar height where the width of each bar is 1, return the area of the largest rectangle in the histogram.

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Monotonic Stack | $O(N)$ | $O(N)$ |

**Python 3 Implementation**:
```python
def largest_rectangle_area(heights: list[int]) -> int:
    stack = []  # stores (index, height)
    max_area = 0

    for i, h in enumerate(heights):
        start = i
        while stack and stack[-1][1] > h:
            index, height = stack.pop()
            max_area = max(max_area, height * (i - index))
            start = index  # extend start backwards
        stack.append((start, h))

    # Process remaining bars extending to the end of the array
    n = len(heights)
    for index, height in stack:
        max_area = max(max_area, height * (n - index))

    return max_area
```

**Java 17 Implementation**:
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class LargestRectangleHistogram {
    public int largestRectangleArea(int[] heights) {
        int n = heights.length;
        Deque<Integer> stack = new ArrayDeque<>();
        int maxArea = 0;

        for (int i = 0; i <= n; i++) {
            int currentHeight = (i == n) ? 0 : heights[i];
            while (!stack.isEmpty() && heights[stack.peek()] > currentHeight) {
                int height = heights[stack.pop()];
                int width = stack.isEmpty() ? i : i - stack.peek() - 1;
                maxArea = Math.max(maxArea, height * width);
            }
            stack.push(i);
        }
        return maxArea;
    }
}
```

---

#### Problem 3: Sliding Window Maximum (LeetCode 239)
Given an array of integers `nums` and a sliding window of size `k`, return the max sliding window.

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Monotonic Queue | $O(N)$ | $O(K)$ |

**Python 3 Implementation**:
```python
from collections import deque

def max_sliding_window(nums: list[int], k: int) -> list[int]:
    q = deque()  # stores indices, values are strictly decreasing
    result = []

    for i, num in enumerate(nums):
        # 1. Remove indices outside current window [i - k + 1, i]
        if q and q[0] < i - k + 1:
            q.popleft()

        # 2. Maintain decreasing order: remove smaller elements from back
        while q and nums[q[-1]] < num:
            q.pop()

        q.append(i)

        # 3. Add to result once first window of size k is formed
        if i >= k - 1:
            result.append(nums[q[0]])

    return result
```

**Java 17 Implementation**:
```java
import java.util.ArrayDeque;
import java.util.Deque;

public class SlidingWindowMax {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int n = nums.length;
        int[] result = new int[n - k + 1];
        Deque<Integer> deque = new ArrayDeque<>();

        for (int i = 0; i < n; i++) {
            if (!deque.isEmpty() && deque.peekFirst() < i - k + 1) {
                deque.pollFirst();
            }

            while (!deque.isEmpty() && nums[deque.peekLast()] < nums[i]) {
                deque.pollLast();
            }

            deque.offerLast(i);

            if (i >= k - 1) {
                result[i - k + 1] = nums[deque.peekFirst()];
            }
        }
        return result;
    }
}
```
