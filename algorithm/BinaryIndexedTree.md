#### Lowbit

```
lowbit(n) = n & (~n + 1) = n & -n
```

#### BinaryIndexedTree

ask prefix sum:

```
int ask(int x) {
	int ans = 0;
	for(; x; x -= x & -x) ans += c[x];
	return ans;
}
``` 

add value `y` on arr[x]:
```
void add(int x, int y) {
	for(; x<=N; x += x & -x) c[x] += y;
}

```