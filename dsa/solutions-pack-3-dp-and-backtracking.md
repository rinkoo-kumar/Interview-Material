# DSA Track: Practical Code Solutions Pack 3
## Dynamic Programming & Backtracking (Python & Java)

> 🧭 **Navigation**: [⬅️ Previous: Solutions Pack 2: Trees & Graphs](solutions-pack-2-trees-graphs-heaps.md) | [🏠 DSA Track Index](README.md) | End of Track ⇥

---

This pack provides complete, runnable, production-grade implementations for the most tricky Dynamic Programming and Backtracking interview questions asked at Google, Meta, Apple, and Netflix.

---

### 1. Word Break (LeetCode 139)

Given a string `s` and a dictionary of strings `wordDict`, return `true` if `s` can be segmented into a space-separated sequence of dictionary words.

- **Recurrence**:
  $$dp[i] = \text{True if } \exists j < i \text{ such that } dp[j] == \text{True and } s[j:i] \in \text{wordDict}$$

> 📖 [LeetCode Problem 139](https://leetcode.com/problems/word-break/)

> 📺 **Video References**
> - [NeetCode: Word Break](https://www.youtube.com/watch?v=Sx9NNgInc3A)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Bottom-Up DP | $O(N^2 \cdot K)$ | $O(N)$ |
*(Where $K$ is max word length in `wordDict`)*

#### Python 3 Implementation:
```python
def word_break(s: str, word_dict: list[str]) -> bool:
    word_set = set(word_dict)
    dp = [False] * (len(s) + 1)
    dp[0] = True  # Empty string is valid

    for i in range(1, len(s) + 1):
        for j in range(i):
            if dp[j] and s[j:i] in word_set:
                dp[i] = True
                break

    return dp[len(s)]

# Test:
if __name__ == "__main__":
    assert word_break("leetcode", ["leet", "code"]) == True
    assert word_break("catsandog", ["cats", "dog", "sand", "and", "cat"]) == False
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
import java.util.HashSet;
import java.util.List;
import java.util.Set;
import java.util.Arrays;

public class WordBreak {
    public boolean wordBreak(String s, List<String> wordDict) {
        Set<String> wordSet = new HashSet<>(wordDict);
        boolean[] dp = new boolean[s.length() + 1];
        dp[0] = true;

        for (int i = 1; i <= s.length(); i++) {
            for (int j = 0; j < i; j++) {
                if (dp[j] && wordSet.contains(s.substring(j, i))) {
                    dp[i] = true;
                    break;
                }
            }
        }

        return dp[s.length()];
    }
    
    public static void main(String[] args) {
        WordBreak wb = new WordBreak();
        System.out.println(wb.wordBreak("leetcode", Arrays.asList("leet", "code"))); // Expected: true
        System.out.println(wb.wordBreak("catsandog", Arrays.asList("cats", "dog", "sand", "and", "cat"))); // Expected: false
    }
}
```

---

### 2. Decode Ways (LeetCode 91)

A message containing letters from A-Z can be encoded into numbers using the mapping 'A' $\to$ "1", ..., 'Z' $\to$ "26". Given a string `s` of digits, return the number of ways to decode it.

> 📖 [LeetCode Problem 91](https://leetcode.com/problems/decode-ways/)

> 📺 **Video References**
> - [NeetCode: Decode Ways](https://www.youtube.com/watch?v=6aEyTjOwlJU)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| DP with Space Optimization | $O(N)$ | $O(1)$ |

#### Python 3 Implementation:
```python
def num_decodings(s: str) -> int:
    if not s or s[0] == '0':
        return 0

    # Space optimized DP: prev2 = dp[i-2], prev1 = dp[i-1]
    prev2, prev1 = 1, 1

    for i in range(1, len(s)):
        current = 0
        # Single digit decode (1-9)
        if s[i] != '0':
            current += prev1
        # Two digit decode (10-26)
        two_digit = int(s[i - 1:i + 1])
        if 10 <= two_digit <= 26:
            current += prev2

        prev2 = prev1
        prev1 = current

    return prev1

# Test:
if __name__ == "__main__":
    assert num_decodings("12") == 2
    assert num_decodings("226") == 3
    assert num_decodings("06") == 0
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
public class DecodeWays {
    public int numDecodings(String s) {
        if (s == null || s.length() == 0 || s.charAt(0) == '0') return 0;

        int prev2 = 1, prev1 = 1;

        for (int i = 1; i < s.length(); i++) {
            int current = 0;
            if (s.charAt(i) != '0') {
                current += prev1;
            }

            int twoDigit = Integer.parseInt(s.substring(i - 1, i + 1));
            if (twoDigit >= 10 && twoDigit <= 26) {
                current += prev2;
            }

            prev2 = prev1;
            prev1 = current;
        }

        return prev1;
    }
    
    public static void main(String[] args) {
        DecodeWays dw = new DecodeWays();
        System.out.println(dw.numDecodings("12")); // Expected: 2
        System.out.println(dw.numDecodings("226")); // Expected: 3
        System.out.println(dw.numDecodings("06")); // Expected: 0
    }
}
```

---

### 3. Regular Expression Matching (LeetCode 10 - Hard)

Given an input string `s` and a pattern `p`, implement regular expression matching with support for `.` (matches any single character) and `*` (matches zero or more of the preceding element).

> 📖 [LeetCode Problem 10](https://leetcode.com/problems/regular-expression-matching/)

> 📺 **Video References**
> - [NeetCode: Regular Expression Matching](https://www.youtube.com/watch?v=HAA8mgxlov8)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| 2D Dynamic Programming | $O(M \times N)$ | $O(M \times N)$ |

#### Python 3 Implementation:
```python
def is_match(s: str, p: str) -> bool:
    m, n = len(s), len(p)
    dp = [[False] * (n + 1) for _ in range(m + 1)]
    dp[0][0] = True

    # Deals with patterns like a*, a*b*, a*b*c* matching empty string
    for j in range(2, n + 1):
        if p[j - 1] == '*':
            dp[0][j] = dp[0][j - 2]

    for i in range(1, m + 1):
        for j in range(1, n + 1):
            if p[j - 1] == '.' or p[j - 1] == s[i - 1]:
                dp[i][j] = dp[i - 1][j - 1]
            elif p[j - 1] == '*':
                # Case 1: '*' acts as 0 occurrences of preceding char
                dp[i][j] = dp[i][j - 2]
                # Case 2: Preceding char matches s[i-1] -> '*' acts as 1+ occurrences
                if p[j - 2] == '.' or p[j - 2] == s[i - 1]:
                    dp[i][j] = dp[i][j] or dp[i - 1][j]

    return dp[m][n]

# Test:
if __name__ == "__main__":
    assert is_match("aa", "a*") == True
    assert is_match("ab", ".*") == True
    assert is_match("mississippi", "mis*is*p*.") == False
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
public class RegularExpressionMatching {
    public boolean isMatch(String s, String p) {
        int m = s.length(), n = p.length();
        boolean[][] dp = new boolean[m + 1][n + 1];
        dp[0][0] = true;

        for (int j = 2; j <= n; j++) {
            if (p.charAt(j - 1) == '*') {
                dp[0][j] = dp[0][j - 2];
            }
        }

        for (int i = 1; i <= m; i++) {
            for (int j = 1; j <= n; j++) {
                char sc = s.charAt(i - 1);
                char pc = p.charAt(j - 1);

                if (pc == '.' || pc == sc) {
                    dp[i][j] = dp[i - 1][j - 1];
                } else if (pc == '*') {
                    dp[i][j] = dp[i][j - 2];
                    char prevPatternChar = p.charAt(j - 2);
                    if (prevPatternChar == '.' || prevPatternChar == sc) {
                        dp[i][j] = dp[i][j] || dp[i - 1][j];
                    }
                }
            }
        }

        return dp[m][n];
    }
    
    public static void main(String[] args) {
        RegularExpressionMatching rem = new RegularExpressionMatching();
        System.out.println(rem.isMatch("aa", "a*")); // Expected: true
        System.out.println(rem.isMatch("ab", ".*")); // Expected: true
        System.out.println(rem.isMatch("mississippi", "mis*is*p*.")); // Expected: false
    }
}
```

---

> 🧭 **Navigation**: [⬅️ Previous: Solutions Pack 2: Trees & Graphs](solutions-pack-2-trees-graphs-heaps.md) | [🏠 DSA Track Index](README.md) | End of Track ⇥
