#### The Idea

Avoid repeated computation, by maintaining a window, which can transfer sub-computations to a window move operation (which could be a constant cost).


Target problems: sub-array/sub-string problems. E.g. max sum of k-length sub-array, mim-window-substring contains T chars.

#### The algorithm

- define a window to maintain current key state to the problem

- define sliding operation: which could update current window with the right state

- define when & how to slide the window

#### A getting started problem: max sum of k-length sub-array

input: array A, int k.
output: max sum of all sub-arraies of A with length k.
constraints: A.length >= k, k > 0

**Naive nested loops solution:**

```java

// O(n * k)
int maxSum(int[] arr, int k) {
	int result = Integer.MIN_VALUE;

    // O(n)
	for(int i = 0; i < arr.length - k + 1; i ++) {
		int cur = 0;
		// O(k)
		for(int j = i; j < i + k; j ++) {
			cur += arr[j];
		}

		result = Math.max(result, cur);
	}

	return result;
}
```

**By the power of sliding window solution:**

- Define window: int curWin represents sum of current sub-array with length k.
- Window sliding: curWin += newElement - previousLeftMostElement

```java
// O(n + k) -> O(n) as n >= k
int maxSum(int[] arr, int k) {
	int result = Integer.MIN_VALUE;
	int curWin = 0;
    // Initiate window
    // O(k)
    for(int i = 0; i < k; i ++) {
    	curWin += arr[i];
    }

    result = curWin;
    // O(n)
    for(int i = k; i < arr.length; i ++) {
    	curWin += arr[i] - arr[i - k];
    	result = Math.max(result, curWin);
    }

    return result;
}
```

#### Another getting started problem: min-window substring

input: string S, string T
output: the min-length sub-string s of S, which contains all chars (includes duplicated) in T, empty if none such s exists.
constraints: S.length >= T.length > 0

Leetcode: https://leetcode.com/problems/minimum-window-substring/

- Define window: represents current sub-string. State: is cur window a potential solution
- Define sliding operation:
  - if cur window does not represents a potential solution, right-expand it untill window contains all chars in T
  - if cur window represents a potential solution, left-shrink it to explore other potential solutions

```java

class Window {
        public Map<Character, Integer> chars;
        public int left;
        public int right;
        private String s;

        public Window(String s) {
            this.chars = new HashMap<>();
            this.left = 0;
            this.right = 0;
            this.s = s;
            this.chars.put(s.charAt(left), 1);
        }

        public int length() {
            return right - left + 1;
        }

        public void expandRight() {
            this.right += 1;
            if(right < this.s.length()) {
                char newChar = s.charAt(right);
                this.chars.put(newChar, this.chars.getOrDefault(newChar, 0) + 1);
            }
        }

        public void shrinkLeft() {
            char oldChar = s.charAt(left);
            int newCount = this.chars.get(oldChar) - 1;
            if(newCount == 0) {
                this.chars.remove(oldChar);
            } else {
                this.chars.put(oldChar, newCount);
            }
            this.left += 1;
        }

        public boolean isPotentialSolution(Map<Character, Integer> t) {
            for(Map.Entry<Character, Integer> entry : t.entrySet()) {
                if(!this.chars.containsKey(entry.getKey())
                   || this.chars.get(entry.getKey()) < entry.getValue()) {
                    return false;
                }
            }
            
            return true;
        }
    }


    public String minWindow(String s, String t) {
        Window curWin = new Window(s);

        int minLength = s.length() + 1;
        int minLeft = 0;
        int minRight = 0;
        Map<Character, Integer> target = new HashMap<>();
        for(char c : t.toCharArray()) {
            target.put(c, target.getOrDefault(c, 0) + 1);
        }

        while(curWin.right < s.length()) {
            while(curWin.isPotentialSolution(target)) {
                if(curWin.length() < minLength) {
                    minLength = curWin.length();
                    minLeft = curWin.left;
                    minRight = curWin.right;
                }

                curWin.shrinkLeft();
            }

            curWin.expandRight();

        }

        return minLength > s.length() ? "" : s.substring(minLeft, minRight + 1);
    }
```