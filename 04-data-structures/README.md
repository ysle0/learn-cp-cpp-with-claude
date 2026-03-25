# 4. 핵심 자료구조

## 4.1 Stack / Queue / Deque

### Stack 활용: 괄호 매칭

```cpp
bool isValid(string s) {
    stack<char> st;
    for (char c : s) {
        if (c == '(' || c == '[' || c == '{') {
            st.push(c);
        } else {
            if (st.empty()) return false;
            char top = st.top(); st.pop();
            if (c == ')' && top != '(') return false;
            if (c == ']' && top != '[') return false;
            if (c == '}' && top != '{') return false;
        }
    }
    return st.empty();
}
```

### 단조 스택 (Monotone Stack)

```cpp
// 각 원소의 오른쪽에서 처음으로 큰 수 (NGE)
vector<int> nge(int n, vector<int>& arr) {
    vector<int> result(n, -1);
    stack<int> st;  // 인덱스 저장
    for (int i = 0; i < n; i++) {
        while (!st.empty() && arr[st.top()] < arr[i]) {
            result[st.top()] = arr[i];
            st.pop();
        }
        st.push(i);
    }
    return result;
}
```

### Queue 활용: BFS

```cpp
// BFS 기본 패턴
queue<pair<int,int>> q;
q.push({startX, startY});
visited[startX][startY] = true;

while (!q.empty()) {
    auto [x, y] = q.front(); q.pop();
    for (int d = 0; d < 4; d++) {
        int nx = x + dx[d], ny = y + dy[d];
        if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
        if (visited[nx][ny]) continue;
        visited[nx][ny] = true;
        q.push({nx, ny});
    }
}
```

### Deque 활용: 슬라이딩 윈도우 최솟값/최댓값

```cpp
// 크기 K 윈도우의 최솟값 (O(N))
deque<int> dq;  // 인덱스 저장, 값이 단조 증가하도록 유지
for (int i = 0; i < n; i++) {
    // 윈도우 범위 벗어난 원소 제거
    while (!dq.empty() && dq.front() <= i - k) dq.pop_front();
    // 현재 값보다 큰 원소 제거 (단조성 유지)
    while (!dq.empty() && arr[dq.back()] >= arr[i]) dq.pop_back();
    dq.push_back(i);
    if (i >= k - 1) {
        cout << arr[dq.front()] << ' ';  // 윈도우 최솟값
    }
}
```

---

## 4.2 Hash Map / Hash Set

### unordered_map 활용

```cpp
// 빈도수 세기
unordered_map<int, int> freq;
for (int x : arr) freq[x]++;

// 두 수의 합 (Two Sum)
unordered_map<int, int> seen;  // 값 → 인덱스
for (int i = 0; i < n; i++) {
    int complement = target - arr[i];
    if (seen.count(complement)) {
        // seen[complement]와 i가 답
    }
    seen[arr[i]] = i;
}
```

### 시간복잡도

| 연산 | 평균 | 최악 |
|------|------|------|
| insert | O(1) | O(n) |
| find/count | O(1) | O(n) |
| erase | O(1) | O(n) |

> **팁**: 최악 O(n) 해킹을 방지하려면 `map` (O(log n) 보장)을 쓰는 것이 안전할 수 있음.

---

## 4.3 Heap (Priority Queue)

### 기본 사용법

```cpp
// Max-heap (기본)
priority_queue<int> maxpq;

// Min-heap
priority_queue<int, vector<int>, greater<int>> minpq;

// 커스텀 비교 (거리 기준 min-heap)
using Node = pair<int, int>;  // {거리, 노드번호}
priority_queue<Node, vector<Node>, greater<Node>> pq;
```

### 힙을 활용한 K번째 큰 수

```cpp
// 배열에서 K번째로 큰 수 (min-heap, 크기 K 유지)
priority_queue<int, vector<int>, greater<int>> minpq;
for (int x : arr) {
    minpq.push(x);
    if (minpq.size() > k) minpq.pop();
}
int kthLargest = minpq.top();
```

---

## 4.4 Tree / Binary Tree

### 트리 순회

```cpp
struct TreeNode {
    int val;
    TreeNode *left, *right;
};

// 전위 순회 (Pre-order): 루트 → 왼쪽 → 오른쪽
void preorder(TreeNode* node) {
    if (!node) return;
    cout << node->val << ' ';
    preorder(node->left);
    preorder(node->right);
}

// 중위 순회 (In-order): 왼쪽 → 루트 → 오른쪽
void inorder(TreeNode* node) {
    if (!node) return;
    inorder(node->left);
    cout << node->val << ' ';
    inorder(node->right);
}

// 후위 순회 (Post-order): 왼쪽 → 오른쪽 → 루트
void postorder(TreeNode* node) {
    if (!node) return;
    postorder(node->left);
    postorder(node->right);
    cout << node->val << ' ';
}

// 레벨 순회 (BFS)
void levelOrder(TreeNode* root) {
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        TreeNode* node = q.front(); q.pop();
        cout << node->val << ' ';
        if (node->left) q.push(node->left);
        if (node->right) q.push(node->right);
    }
}
```

### 배열로 트리 표현 (코딩테스트에서 자주 사용)

```cpp
// 부모-자식 관계로 주어지는 경우
int n;
vector<vector<int>> children(n + 1);
int parent[100001];

// 입력: 각 노드의 부모
for (int i = 2; i <= n; i++) {
    cin >> parent[i];
    children[parent[i]].push_back(i);
}
```

---

## 4.5 Trie

### 구현

```cpp
struct TrieNode {
    TrieNode* children[26] = {};
    bool isEnd = false;
};

class Trie {
    TrieNode* root;
public:
    Trie() : root(new TrieNode()) {}

    void insert(const string& word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx])
                node->children[idx] = new TrieNode();
            node = node->children[idx];
        }
        node->isEnd = true;
    }

    bool search(const string& word) {
        TrieNode* node = root;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return node->isEnd;
    }

    bool startsWith(const string& prefix) {
        TrieNode* node = root;
        for (char c : prefix) {
            int idx = c - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return true;
    }
};
```

**시간복잡도**: 삽입/검색 O(L), L = 문자열 길이

**대표 문제**: BOJ 5052 (전화번호 목록), LeetCode 208 (Implement Trie)

---

## 4.6 Union-Find (Disjoint Set)

### 구현 (경로 압축 + 랭크 최적화)

```cpp
struct UnionFind {
    vector<int> parent, rank_;

    UnionFind(int n) : parent(n + 1), rank_(n + 1, 0) {
        iota(parent.begin(), parent.end(), 0);  // parent[i] = i
    }

    int find(int x) {
        if (parent[x] != x)
            parent[x] = find(parent[x]);  // 경로 압축
        return parent[x];
    }

    bool unite(int a, int b) {
        a = find(a); b = find(b);
        if (a == b) return false;  // 이미 같은 집합
        if (rank_[a] < rank_[b]) swap(a, b);
        parent[b] = a;
        if (rank_[a] == rank_[b]) rank_[a]++;
        return true;
    }

    bool connected(int a, int b) {
        return find(a) == find(b);
    }
};
```

**시간복잡도**: find/unite 거의 O(1) (아커만 역함수)

**대표 문제**: BOJ 1717 (집합의 표현), BOJ 1976 (여행 가자), BOJ 4195 (친구 네트워크)

---

## 4.7 Segment Tree

### 구간 합 세그먼트 트리

```cpp
struct SegTree {
    int n;
    vector<long long> tree;

    SegTree(int n) : n(n), tree(4 * n, 0) {}

    void build(vector<int>& arr, int node, int start, int end) {
        if (start == end) {
            tree[node] = arr[start];
            return;
        }
        int mid = (start + end) / 2;
        build(arr, 2*node, start, mid);
        build(arr, 2*node+1, mid+1, end);
        tree[node] = tree[2*node] + tree[2*node+1];
    }

    void update(int node, int start, int end, int idx, int val) {
        if (start == end) {
            tree[node] = val;
            return;
        }
        int mid = (start + end) / 2;
        if (idx <= mid) update(2*node, start, mid, idx, val);
        else update(2*node+1, mid+1, end, idx, val);
        tree[node] = tree[2*node] + tree[2*node+1];
    }

    long long query(int node, int start, int end, int l, int r) {
        if (r < start || end < l) return 0;  // 범위 밖
        if (l <= start && end <= r) return tree[node];  // 완전 포함
        int mid = (start + end) / 2;
        return query(2*node, start, mid, l, r) +
               query(2*node+1, mid+1, end, l, r);
    }

    // 편의 함수
    void build(vector<int>& arr) { build(arr, 1, 0, n-1); }
    void update(int idx, int val) { update(1, 0, n-1, idx, val); }
    long long query(int l, int r) { return query(1, 0, n-1, l, r); }
};
```

### Fenwick Tree (BIT) - 더 간결한 구현

```cpp
struct FenwickTree {
    int n;
    vector<long long> tree;

    FenwickTree(int n) : n(n), tree(n + 1, 0) {}

    void update(int i, long long delta) {
        for (++i; i <= n; i += i & (-i))
            tree[i] += delta;
    }

    long long query(int i) {  // [0, i] 구간 합
        long long sum = 0;
        for (++i; i > 0; i -= i & (-i))
            sum += tree[i];
        return sum;
    }

    long long query(int l, int r) {  // [l, r] 구간 합
        return query(r) - (l > 0 ? query(l - 1) : 0);
    }
};
```

**시간복잡도**: 업데이트 O(log N), 쿼리 O(log N)

**대표 문제**: BOJ 2042 (구간 합 구하기), BOJ 2357 (최솟값과 최댓값)
