# Trie Tree
- https://www.geeksforgeeks.org/trie-insert-and-search/
- https://www.geeksforgeeks.org/advantages-trie-data-structure/

## Representation
```java
class TrieNode {
	// 26 childNodes for alphabet
	TrieNode[] childNodes;
	// if this node represents a word
	boolean isLeaf;
	// optional fields if need support deletion
	/**
	 * value of this TrieNode
	 */
	char value;
	TrieNode parent;

	public TrieNode(TrieNode parent, char value) {
		this.parent = parent;
		this.value = value;
	}

	public TrieNode getChild(char value) {
		return childNodes[getIndex(value)];
	}

	public void addChild(TrieNode child) {
		childNodes[getIndex(child.value)] = child;
	}

	public void deleteChild(char value) {
		childNodes[getIndex(value)] = null;
	}

	public boolean hasChild() {
		for (TrieNode c : childNodes) {
			if (c != null) { return true; }
		}
		return false;
	}

	int getIndex(char value) {
		return 'a' - value;
	}
}
```

## Search
```java
public boolean search(String word) {
	TrieNode cur = root;
	for (char c : word.toCharArray()) {
		TrieNode child = cur.getChild(c);
		if (child == null) { return false; }
		cur = child;
	}
	return child.isLeaf;
}

```

## Insert
```java
public void insert(String word) {
	TrieNode cur = root;
	for (char c : word:toCharArray()) {
		TrieNode child = cur.getChild(c);
		if (child == null) {
			child = new TrieNode(cur, c);
			cur.addChild(child);
		}
		cur = child;
	}
}
```
## Delete
```java
public void delete(String word) {
	TrieNode lastNode = findLastNode(word);
	while(lastNode != null) {
		// if Deletion is word, not prefix:
		if(lastNode.isLeaf && lastNode.hasChild()) {
			lastNode.isLeaf = false;
			return;
		}

		TrieNode parent = lastNode.parent;
		if (parent == null) {
			lastNode.isLeaf = false;
			return;
		}
		parent.deleteChild(lastNode.value);
		if(parent.hasChild()) {
			break;
		}
		lastNode = parent;
	}
}
```
## Problems

- https://leetcode.com/problems/implement-trie-prefix-tree/
- https://leetcode.com/problems/word-search-ii
  - Avoid passing mutable current state in recursive DFS -- backtracking is so easily to get wrong with mutable current state being passed in nested calls.
  - TrieTree deletion impl: avoid prune all words when delete a word which is also a prefix for other words
  - Optimizations: Leverage TrieNode to avoid O(L) retrieve for each DSF search
  - hasChild from O(26) to O(1) via dynamic maintain the child count 