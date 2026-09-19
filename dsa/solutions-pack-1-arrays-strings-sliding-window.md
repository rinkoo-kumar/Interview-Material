# DSA Track: Practical Code Solutions Pack 1
## Arrays, Strings, Prefix Sum & Sliding Window (Python & Java)

> 🧭 **Navigation**: [⬅️ Previous: 11: Top 50 LeetCode Patterns](11-top-50-dsa-interview-problems.md) | [🏠 DSA Track Index](README.md) | [➡️ Next: Solutions Pack 2: Trees & Graphs](solutions-pack-2-trees-graphs-heaps.md)

---

This pack provides complete, runnable, production-grade implementations for the most frequently asked Array, String, and Sliding Window interview questions at Google, Meta, Amazon, and Microsoft.

---

### 1. Product of Array Except Self (LeetCode 238)

Given an integer array `nums`, return an array `answer` such that `answer[i]` is equal to the product of all the elements of `nums` except `nums[i]`. Solve in $O(N)$ without using division.

> 📖 [LeetCode Problem 238](https://leetcode.com/problems/product-of-array-except-self/)

> 📺 **Video References**
> - [NeetCode: Product of Array Except Self](https://www.youtube.com/watch?v=bNvIQI2wAOA)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Two Passes (Prefix & Suffix) | $O(N)$ | $O(1)$ auxiliary |

#### Python 3 Implementation:
```python
def product_except_self(nums: list[int]) -> list[int]:
    n = len(nums)
    res = [1] * n

    # Pass 1: Prefix products stored directly in result array
    prefix = 1
    for i in range(n):
        res[i] = prefix
        prefix *= nums[i]

    # Pass 2: Multiply by suffix products in reverse
    postfix = 1
    for i in range(n - 1, -1, -1):
        res[i] *= postfix
        postfix *= nums[i]

    return res

# Test:
if __name__ == "__main__":
    assert product_except_self([1, 2, 3, 4]) == [24, 12, 8, 6]
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
import java.util.Arrays;

public class ProductExceptSelf {
    public int[] productExceptSelf(int[] nums) {
        int n = nums.length;
        int[] res = new int[n];

        // 1. Prefix products
        int prefix = 1;
        for (int i = 0; i < n; i++) {
            res[i] = prefix;
            prefix *= nums[i];
        }

        // 2. Multiply by suffix products
        int postfix = 1;
        for (int i = n - 1; i >= 0; i--) {
            res[i] *= postfix;
            postfix *= nums[i];
        }

        return res;
    }
    
    public static void main(String[] args) {
        ProductExceptSelf pes = new ProductExceptSelf();
        int[] result = pes.productExceptSelf(new int[]{1, 2, 3, 4});
        System.out.println(Arrays.toString(result)); // Expected: [24, 12, 8, 6]
    }
}
```

---

### 2. Subarray Sum Equals K (LeetCode 560)

Given an array of integers `nums` and an integer `k`, return the total number of continuous subarrays whose sum equals `k`.

- **Key Insight**: Running Prefix Sum + Hash Map counting frequencies of past prefix sums. If `current_sum - k` exists in map, we found matching subarrays!

> 📖 [LeetCode Problem 560](https://leetcode.com/problems/subarray-sum-equals-k/)

> 📺 **Video References**
> - [NeetCode: Subarray Sum Equals K](https://www.youtube.com/watch?v=fFVZt-6sgyo)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Prefix Sum + Hash Map | $O(N)$ | $O(N)$ |

#### Python 3 Implementation:
```python
from collections import defaultdict

def subarray_sum(nums: list[int], k: int) -> int:
    count = 0
    current_sum = 0
    prefix_map = defaultdict(int)
    prefix_map[0] = 1  # Base case: empty subarray has sum 0

    for num in nums:
        current_sum += num
        # If (current_sum - k) was seen before, add its frequency
        if (current_sum - k) in prefix_map:
            count += prefix_map[current_sum - k]
        prefix_map[current_sum] += 1

    return count

# Test:
if __name__ == "__main__":
    assert subarray_sum([1, 1, 1], 2) == 2
    assert subarray_sum([1, 2, 3], 3) == 2
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
import java.util.HashMap;
import java.util.Map;

public class SubarraySumEqualsK {
    public int subarraySum(int[] nums, int k) {
        int count = 0;
        int currentSum = 0;
        Map<Integer, Integer> prefixMap = new HashMap<>();
        prefixMap.put(0, 1);

        for (int num : nums) {
            currentSum += num;
            if (prefixMap.containsKey(currentSum - k)) {
                count += prefixMap.get(currentSum - k);
            }
            prefixMap.put(currentSum, prefixMap.getOrDefault(currentSum, 0) + 1);
        }

        return count;
    }
    
    public static void main(String[] args) {
        SubarraySumEqualsK ssk = new SubarraySumEqualsK();
        System.out.println(ssk.subarraySum(new int[]{1, 1, 1}, 2)); // Expected: 2
        System.out.println(ssk.subarraySum(new int[]{1, 2, 3}, 3)); // Expected: 2
    }
}
```

---

### 3. Minimum Window Substring (LeetCode 76 - Hard)

Given two strings `s` and `t`, return the minimum window substring of `s` such that every character in `t` (including duplicates) is included in the window.

> 📖 [LeetCode Problem 76](https://leetcode.com/problems/minimum-window-substring/)

> 📺 **Video References**
> - [NeetCode: Minimum Window Substring](https://www.youtube.com/watch?v=jRmVSAnFlM8)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Dynamic Sliding Window | $O(N)$ | $O(M)$ |
*(Where $N$ is size of `s` and $M$ is size of `t`)*

#### Python 3 Implementation:
```python
from collections import Counter

def min_window(s: str, t: str) -> str:
    if not s or not t:
        return ""

    target_counts = Counter(t)
    window_counts = {}
    required = len(target_counts)
    formed = 0

    left, right = 0, 0
    ans = (float("inf"), None, None)  # (window_len, left, right)

    while right < len(s):
        char = s[right]
        window_counts[char] = window_counts.get(char, 0) + 1

        if char in target_counts and window_counts[char] == target_counts[char]:
            formed += 1

        # Shrink window from left as much as possible
        while left <= right and formed == required:
            char = s[left]
            if (right - left + 1) < ans[0]:
                ans = (right - left + 1, left, right)

            window_counts[char] -= 1
            if char in target_counts and window_counts[char] < target_counts[char]:
                formed -= 1
            left += 1

        right += 1

    return "" if ans[0] == float("inf") else s[ans[1]:ans[2] + 1]

# Test:
if __name__ == "__main__":
    assert min_window("ADOBECODEBANC", "ABC") == "BANC"
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
import java.util.HashMap;
import java.util.Map;

public class MinimumWindowSubstring {
    public String minWindow(String s, String t) {
        if (s.isEmpty() || t.isEmpty()) return "";

        Map<Character, Integer> targetMap = new HashMap<>();
        for (char c : t.toCharArray()) {
            targetMap.put(c, targetMap.getOrDefault(c, 0) + 1);
        }

        Map<Character, Integer> windowMap = new HashMap<>();
        int required = targetMap.size();
        int formed = 0;

        int left = 0, right = 0;
        int minLen = Integer.MAX_VALUE;
        int minLeft = 0;

        while (right < s.length()) {
            char c = s.charAt(right);
            windowMap.put(c, windowMap.getOrDefault(c, 0) + 1);

            if (targetMap.containsKey(c) && windowMap.get(c).intValue() == targetMap.get(c).intValue()) {
                formed++;
            }

            while (left <= right && formed == required) {
                if ((right - left + 1) < minLen) {
                    minLen = right - left + 1;
                    minLeft = left;
                }

                char leftChar = s.charAt(left);
                windowMap.put(leftChar, windowMap.get(leftChar) - 1);
                if (targetMap.containsKey(leftChar) && windowMap.get(leftChar) < targetMap.get(leftChar)) {
                    formed--;
                }
                left++;
            }
            right++;
        }

        return minLen == Integer.MAX_VALUE ? "" : s.substring(minLeft, minLeft + minLen);
    }
    
    public static void main(String[] args) {
        MinimumWindowSubstring mws = new MinimumWindowSubstring();
        System.out.println(mws.minWindow("ADOBECODEBANC", "ABC")); // Expected: "BANC"
    }
}
```

---

### 4. Sort Colors / Dutch National Flag (LeetCode 75)

Given an array `nums` with $n$ objects colored red (0), white (1), or blue (2), sort them **in-place** in a single pass.

> 📖 [LeetCode Problem 75](https://leetcode.com/problems/sort-colors/)

> 📺 **Video References**
> - [NeetCode: Sort Colors](https://www.youtube.com/watch?v=4xbWSRIGfAQ)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Dijkstra's 3-Way Partitioning | $O(N)$ | $O(1)$ |

#### Python 3 Implementation:
```python
def sort_colors(nums: list[int]) -> None:
    low, mid, high = 0, 0, len(nums) - 1

    while mid <= high:
        if nums[mid] == 0:
            nums[low], nums[mid] = nums[mid], nums[low]
            low += 1
            mid += 1
        elif nums[mid] == 1:
            mid += 1
        else:  # nums[mid] == 2
            nums[mid], nums[high] = nums[high], nums[mid]
            high -= 1

# Test:
if __name__ == "__main__":
    arr = [2, 0, 2, 1, 1, 0]
    sort_colors(arr)
    assert arr == [0, 0, 1, 1, 2, 2]
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
import java.util.Arrays;

public class SortColors {
    public void sortColors(int[] nums) {
        int low = 0, mid = 0, high = nums.length - 1;

        while (mid <= high) {
            if (nums[mid] == 0) {
                swap(nums, low, mid);
                low++;
                mid++;
            } else if (nums[mid] == 1) {
                mid++;
            } else {
                swap(nums, mid, high);
                high--;
            }
        }
    }

    private void swap(int[] nums, int i, int j) {
        int temp = nums[i];
        nums[i] = nums[j];
        nums[j] = temp;
    }
    
    public static void main(String[] args) {
        SortColors sc = new SortColors();
        int[] arr = {2, 0, 2, 1, 1, 0};
        sc.sortColors(arr);
        System.out.println(Arrays.toString(arr)); // Expected: [0, 0, 1, 1, 2, 2]
    }
}
```

---

> 🧭 **Navigation**: [⬅️ Previous: 11: Top 50 LeetCode Patterns](11-top-50-dsa-interview-problems.md) | [🏠 DSA Track Index](README.md) | [➡️ Next: Solutions Pack 2: Trees & Graphs](solutions-pack-2-trees-graphs-heaps.md)
