# DSA Track: Practical Code Solutions Pack 2
## Trees, Graphs, BFS/DFS & Heaps (Python & Java)

> 🧭 **Navigation**: [⬅️ Previous: Solutions Pack 1: Arrays & Strings](solutions-pack-1-arrays-strings-sliding-window.md) | [🏠 DSA Track Index](README.md) | [➡️ Next: Solutions Pack 3: DP & Backtracking](solutions-pack-3-dp-and-backtracking.md)

---

This pack provides complete, runnable, production-grade implementations for the hardest Graph, Tree, and Heap problems asked at Google, Uber, Meta, and Amazon.

---

### 1. Word Ladder (LeetCode 127 - Hard)

Given two words `beginWord` and `endWord`, and a dictionary `wordList`, return the number of words in the shortest transformation sequence.

> 📖 [LeetCode Problem 127](https://leetcode.com/problems/word-ladder/)

> 📺 **Video References**
> - [NeetCode: Word Ladder](https://www.youtube.com/watch?v=h9iTnkgv05E)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Bidirectional BFS | $O(M^2 \times N)$ | $O(M \times N)$ |
*(Where $M$ is word length and $N$ is number of words)*

#### Python 3 Implementation:
```python
from collections import deque

def ladder_length(begin_word: str, end_word: str, word_list: list[str]) -> int:
    """Return the shortest one-letter word-change sequence length, or zero."""
    word_set = set(word_list)
    if end_word not in word_set:
        return 0

    # Bidirectional BFS sets
    begin_set = {begin_word}
    end_set = {end_word}
    length = 1

    while begin_set and end_set:
        # Always expand the smaller frontier to minimize search space
        if len(begin_set) > len(end_set):
            begin_set, end_set = end_set, begin_set

        next_set = set()
        for word in begin_set:
            word_chars = list(word)
            for i in range(len(word_chars)):
                original_char = word_chars[i]
                for c in 'abcdefghijklmnopqrstuvwxyz':
                    word_chars[i] = c
                    target = "".join(word_chars)

                    if target in end_set:
                        return length + 1
                    if target in word_set:
                        next_set.add(target)
                        word_set.remove(target)  # Mark visited

                word_chars[i] = original_char

        begin_set = next_set
        length += 1

    return 0

# Test:
if __name__ == "__main__":
    assert ladder_length("hit", "cog", ["hot", "dot", "dog", "lot", "log", "cog"]) == 5
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
import java.util.*;

public class WordLadder {
    public int ladderLength(String beginWord, String endWord, List<String> wordList) {
        Set<String> wordSet = new HashSet<>(wordList);
        if (!wordSet.contains(endWord)) return 0;

        Set<String> beginSet = new HashSet<>();
        Set<String> endSet = new HashSet<>();
        beginSet.add(beginWord);
        endSet.add(endWord);
        int length = 1;

        while (!beginSet.isEmpty() && !endSet.isEmpty()) {
            if (beginSet.size() > endSet.size()) {
                Set<String> temp = beginSet;
                beginSet = endSet;
                endSet = temp;
            }

            Set<String> nextSet = new HashSet<>();
            for (String word : beginSet) {
                char[] chars = word.toCharArray();
                for (int i = 0; i < chars.length; i++) {
                    char old = chars[i];
                    for (char c = 'a'; c <= 'z'; c++) {
                        chars[i] = c;
                        String target = new String(chars);

                        if (endSet.contains(target)) return length + 1;
                        if (wordSet.contains(target)) {
                            nextSet.add(target);
                            wordSet.remove(target);
                        }
                    }
                    chars[i] = old;
                }
            }
            beginSet = nextSet;
            length++;
        }
        return 0;
    }
    
    public static void main(String[] args) {
        WordLadder wl = new WordLadder();
        System.out.println(wl.ladderLength("hit", "cog", Arrays.asList("hot", "dot", "dog", "lot", "log", "cog"))); // Expected: 5
    }
}
```

---

### 2. Serialize and Deserialize Binary Tree (LeetCode 297 - Hard)

Design an algorithm to serialize and deserialize a binary tree.

> 📖 [LeetCode Problem 297](https://leetcode.com/problems/serialize-and-deserialize-binary-tree/)

> 📺 **Video References**
> - [NeetCode: Serialize and Deserialize Binary Tree](https://www.youtube.com/watch?v=u4JAi2JJhI8)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Pre-Order DFS with Delimiters | $O(N)$ | $O(N)$ |

#### Python 3 Implementation:
```python
class TreeNode:
    """Represent one binary-tree value and its child links."""

    def __init__(self, x):
        """Create a tree node with value x and no children."""
        self.val = x
        self.left = None
        self.right = None

class Codec:
    """Convert a binary tree to text and rebuild it from that text."""

    def serialize(self, root: TreeNode) -> str:
        """Turn a tree into a string that preserves its structure."""
        """Encodes a tree to a single string."""
        vals = []
        def dfs(node):
            """Write this node and recursively write its children."""
            if not node:
                vals.append("#")
                return
            vals.append(str(node.val))
            dfs(node.left)
            dfs(node.right)
        dfs(root)
        return ",".join(vals)

    def deserialize(self, data: str) -> TreeNode:
        """Rebuild the tree represented by the serialized string."""
        """Decodes your encoded data to tree."""
        tokens = iter(data.split(","))
        def dfs():
            """Read the next value and rebuild its subtree."""
            val = next(tokens)
            if val == "#":
                return None
            node = TreeNode(int(val))
            node.left = dfs()
            node.right = dfs()
            return node
        return dfs()

if __name__ == "__main__":
    codec = Codec()
    root = TreeNode(1)
    root.left = TreeNode(2)
    root.right = TreeNode(3)
    root.right.left = TreeNode(4)
    root.right.right = TreeNode(5)
    ser = codec.serialize(root)
    assert ser == "1,2,#,#,3,4,#,#,5,#,#"
    deser = codec.deserialize(ser)
    assert deser.val == 1 and deser.right.val == 3
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
import java.util.*;

class TreeNode {
    int val;
    TreeNode left;
    TreeNode right;
    TreeNode(int x) { val = x; }
}

public class Codec {
    public String serialize(TreeNode root) {
        StringBuilder sb = new StringBuilder();
        buildString(root, sb);
        return sb.toString();
    }

    private void buildString(TreeNode node, StringBuilder sb) {
        if (node == null) {
            sb.append("#,");
        } else {
            sb.append(node.val).append(",");
            buildString(node.left, sb);
            buildString(node.right, sb);
        }
    }

    public TreeNode deserialize(String data) {
        Queue<String> nodes = new ArrayDeque<>(Arrays.asList(data.split(",")));
        return buildTree(nodes);
    }

    private TreeNode buildTree(Queue<String> nodes) {
        String val = nodes.poll();
        if ("#".equals(val)) return null;

        TreeNode node = new TreeNode(Integer.parseInt(val));
        node.left = buildTree(nodes);
        node.right = buildTree(nodes);
        return node;
    }
    
    public static void main(String[] args) {
        Codec codec = new Codec();
        TreeNode root = new TreeNode(1);
        root.left = new TreeNode(2);
        root.right = new TreeNode(3);
        root.right.left = new TreeNode(4);
        root.right.right = new TreeNode(5);
        String ser = codec.serialize(root);
        System.out.println("Serialized: " + ser); // Expected: 1,2,#,#,3,4,#,#,5,#,#,
        TreeNode deser = codec.deserialize(ser);
        System.out.println("Deserialized Root: " + deser.val); // Expected: 1
    }
}
```

---

### 3. Alien Dictionary (LeetCode 269 - Hard)

There is a foreign language which uses the Latin alphabet. Given a list of words sorted lexicographically by the rules of this language, return the alien alphabet order.

> 📖 [LeetCode Problem 269](https://leetcode.com/problems/alien-dictionary/)

> 📺 **Video References**
> - [NeetCode: Alien Dictionary](https://www.youtube.com/watch?v=6kTZYvNNyps)

| Algorithm | Time Complexity | Space Complexity |
|-----------|----------------|------------------|
| Topological Sort (Kahn's / DFS) | $O(C)$ | $O(U + E)$ |
*(Where $C$ is total length of all words, $U$ is unique letters, and $E$ is edges)*

#### Python 3 Implementation:
```python
from collections import defaultdict, deque

def alien_order(words: list[str]) -> str:
    """Return a letter order consistent with the sorted alien-language words."""
    adj = {c: set() for word in words for c in word}
    indegree = {c: 0 for c in adj}

    for i in range(len(words) - 1):
        w1, w2 = words[i], words[i + 1]
        min_len = min(len(w1), len(w2))
        # Edge case: Prefix invalid order e.g. ["apple", "app"]
        if len(w1) > len(w2) and w1[:min_len] == w2[:min_len]:
            return ""

        for j in range(min_len):
            if w1[j] != w2[j]:
                if w2[j] not in adj[w1[j]]:
                    adj[w1[j]].add(w2[j])
                    indegree[w2[j]] += 1
                break

    queue = deque([c for c in indegree if indegree[c] == 0])
    result = []

    while queue:
        c = queue.popleft()
        result.append(c)
        for neighbor in adj[c]:
            indegree[neighbor] -= 1
            if indegree[neighbor] == 0:
                queue.append(neighbor)

    return "".join(result) if len(result) == len(adj) else ""

# Test:
if __name__ == "__main__":
    assert alien_order(["wrt", "wrf", "er", "ett", "rftt"]) == "wertf"
    print("Python tests passed!")
```

#### Java 17 Implementation:
```java
import java.util.*;

public class AlienDictionary {
    public String alienOrder(String[] words) {
        Map<Character, Set<Character>> adj = new HashMap<>();
        Map<Character, Integer> indegree = new HashMap<>();

        for (String word : words) {
            for (char c : word.toCharArray()) {
                adj.putIfAbsent(c, new HashSet<>());
                indegree.putIfAbsent(c, 0);
            }
        }

        for (int i = 0; i < words.length - 1; i++) {
            String w1 = words[i], w2 = words[i + 1];
            int minLen = Math.min(w1.length(), w2.length());

            if (w1.length() > w2.length() && w1.startsWith(w2)) return "";

            for (int j = 0; j < minLen; j++) {
                char c1 = w1.charAt(j), c2 = w2.charAt(j);
                if (c1 != c2) {
                    if (!adj.get(c1).contains(c2)) {
                        adj.get(c1).add(c2);
                        indegree.put(c2, indegree.get(c2) + 1);
                    }
                    break;
                }
            }
        }

        Queue<Character> queue = new ArrayDeque<>();
        for (char c : indegree.keySet()) {
            if (indegree.get(c) == 0) queue.offer(c);
        }

        StringBuilder sb = new StringBuilder();
        while (!queue.isEmpty()) {
            char c = queue.poll();
            sb.append(c);
            for (char neighbor : adj.get(c)) {
                indegree.put(neighbor, indegree.get(neighbor) - 1);
                if (indegree.get(neighbor) == 0) {
                    queue.offer(neighbor);
                }
            }
        }

        return sb.length() == indegree.size() ? sb.toString() : "";
    }
    
    public static void main(String[] args) {
        AlienDictionary ad = new AlienDictionary();
        System.out.println(ad.alienOrder(new String[]{"wrt", "wrf", "er", "ett", "rftt"})); // Expected: "wertf"
    }
}
```

---

> 🧭 **Navigation**: [⬅️ Previous: Solutions Pack 1: Arrays & Strings](solutions-pack-1-arrays-strings-sliding-window.md) | [🏠 DSA Track Index](README.md) | [➡️ Next: Solutions Pack 3: DP & Backtracking](solutions-pack-3-dp-and-backtracking.md)
