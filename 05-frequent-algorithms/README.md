# 5. 회사 코테 빈출 알고리즘

## 5.1 DFS / BFS (그래프 탐색)

### DFS (깊이 우선 탐색)

```cpp
vector<vector<int>> adj;
vector<bool> visited;

// 재귀 DFS
void dfs(int node) {
    visited[node] = true;
    for (int next : adj[node]) {
        if (!visited[next]) {
            dfs(next);
        }
    }
}

// 스택 DFS (재귀 깊이 제한 시)
void dfsStack(int start) {
    stack<int> st;
    st.push(start);
    visited[start] = true;
    while (!st.empty()) {
        int node = st.top(); st.pop();
        for (int next : adj[node]) {
            if (!visited[next]) {
                visited[next] = true;
                st.push(next);
            }
        }
    }
}
```

### BFS (너비 우선 탐색) - 최단 거리

```cpp
// 가중치 없는 그래프에서 최단 거리
vector<int> bfs(int start, int n) {
    vector<int> dist(n + 1, -1);
    queue<int> q;
    dist[start] = 0;
    q.push(start);
    while (!q.empty()) {
        int node = q.front(); q.pop();
        for (int next : adj[node]) {
            if (dist[next] == -1) {
                dist[next] = dist[node] + 1;
                q.push(next);
            }
        }
    }
    return dist;
}
```

### 격자 BFS

```cpp
int n, m;
int board[1001][1001];
int dist[1001][1001];
const int dx[] = {-1, 1, 0, 0};
const int dy[] = {0, 0, -1, 1};

void gridBFS(int sx, int sy) {
    memset(dist, -1, sizeof(dist));
    queue<pair<int,int>> q;
    dist[sx][sy] = 0;
    q.push({sx, sy});
    while (!q.empty()) {
        auto [x, y] = q.front(); q.pop();
        for (int d = 0; d < 4; d++) {
            int nx = x + dx[d], ny = y + dy[d];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
            if (board[nx][ny] == 0 || dist[nx][ny] != -1) continue;
            dist[nx][ny] = dist[x][y] + 1;
            q.push({nx, ny});
        }
    }
}
```

**대표 문제**: BOJ 1260 (DFS와 BFS), BOJ 2178 (미로 탐색), BOJ 2667 (단지번호붙이기), BOJ 7576 (토마토)

---

## 5.2 Dynamic Programming

### Top-down (메모이제이션)

```cpp
int memo[1001];
memset(memo, -1, sizeof(memo));

int dp(int n) {
    if (n <= 1) return n;
    if (memo[n] != -1) return memo[n];
    return memo[n] = dp(n - 1) + dp(n - 2);
}
```

### Bottom-up (타뷸레이션)

```cpp
int dp[1001];
dp[0] = 0; dp[1] = 1;
for (int i = 2; i <= n; i++) {
    dp[i] = dp[i-1] + dp[i-2];
}
```

### 0/1 배낭 문제 (Knapsack)

```cpp
int n, W;  // 물건 수, 배낭 용량
int w[101], v[101];  // 무게, 가치
int dp[101][100001];

// dp[i][j] = i번째 물건까지 고려, 용량 j일 때 최대 가치
for (int i = 1; i <= n; i++) {
    for (int j = 0; j <= W; j++) {
        dp[i][j] = dp[i-1][j];  // i번째 물건 안 넣음
        if (j >= w[i]) {
            dp[i][j] = max(dp[i][j], dp[i-1][j-w[i]] + v[i]);  // 넣음
        }
    }
}
// 답: dp[n][W]

// 공간 최적화 (1차원)
int dp2[100001] = {};
for (int i = 1; i <= n; i++) {
    for (int j = W; j >= w[i]; j--) {  // 역순 주의!
        dp2[j] = max(dp2[j], dp2[j - w[i]] + v[i]);
    }
}
```

### LIS (최장 증가 부분수열) - O(N log N)

```cpp
vector<int> lis;
for (int i = 0; i < n; i++) {
    auto it = lower_bound(lis.begin(), lis.end(), arr[i]);
    if (it == lis.end()) {
        lis.push_back(arr[i]);
    } else {
        *it = arr[i];
    }
}
int lisLength = lis.size();
```

### LCS (최장 공통 부분수열)

```cpp
string a, b;
int dp[1001][1001] = {};
for (int i = 1; i <= a.size(); i++) {
    for (int j = 1; j <= b.size(); j++) {
        if (a[i-1] == b[j-1]) dp[i][j] = dp[i-1][j-1] + 1;
        else dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
    }
}
// 답: dp[a.size()][b.size()]
```

**대표 문제**: BOJ 12865 (평범한 배낭), BOJ 11053 (가장 긴 증가하는 부분 수열), BOJ 9251 (LCS)

---

## 5.3 최단경로

### Dijkstra (양의 가중치, 단일 출발)

```cpp
// O(E log V)
using pii = pair<int, int>;  // {거리, 노드}
vector<vector<pii>> adj;     // adj[u] = {{cost, v}, ...}

vector<int> dijkstra(int start, int n) {
    vector<int> dist(n + 1, INF);
    priority_queue<pii, vector<pii>, greater<pii>> pq;
    dist[start] = 0;
    pq.push({0, start});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;  // 이미 더 짧은 경로 발견됨
        for (auto [cost, v] : adj[u]) {
            if (dist[u] + cost < dist[v]) {
                dist[v] = dist[u] + cost;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

### Floyd-Warshall (모든 쌍 최단 경로)

```cpp
// O(V^3), V ≤ 500
int dist[501][501];

void floyd(int n) {
    // 초기화: dist[i][j] = 직접 연결 비용 (없으면 INF), dist[i][i] = 0
    for (int k = 1; k <= n; k++) {        // 경유 노드
        for (int i = 1; i <= n; i++) {    // 출발
            for (int j = 1; j <= n; j++) { // 도착
                if (dist[i][k] != INF && dist[k][j] != INF) {
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
                }
            }
        }
    }
}
```

### Bellman-Ford (음의 가중치 허용)

```cpp
// O(VE), 음수 사이클 검출 가능
struct Edge { int from, to, cost; };

vector<int> bellmanFord(int start, int n, vector<Edge>& edges) {
    vector<int> dist(n + 1, INF);
    dist[start] = 0;

    // V-1번 반복
    for (int i = 0; i < n - 1; i++) {
        for (auto& [u, v, w] : edges) {
            if (dist[u] != INF && dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
            }
        }
    }

    // 음수 사이클 검출 (V번째 반복에서 갱신 발생하면 음수 사이클 존재)
    for (auto& [u, v, w] : edges) {
        if (dist[u] != INF && dist[u] + w < dist[v]) {
            // 음수 사이클 존재!
            return {};
        }
    }
    return dist;
}
```

**대표 문제**: BOJ 1753 (최단경로), BOJ 11404 (플로이드), BOJ 11657 (타임머신)

---

## 5.4 위상정렬 (Topological Sort)

### Kahn's Algorithm (BFS 기반)

```cpp
// 진입차수가 0인 노드부터 시작
vector<int> topologicalSort(int n, vector<vector<int>>& adj) {
    vector<int> indegree(n + 1, 0);
    for (int u = 1; u <= n; u++)
        for (int v : adj[u])
            indegree[v]++;

    queue<int> q;
    for (int i = 1; i <= n; i++)
        if (indegree[i] == 0) q.push(i);

    vector<int> order;
    while (!q.empty()) {
        int node = q.front(); q.pop();
        order.push_back(node);
        for (int next : adj[node]) {
            if (--indegree[next] == 0)
                q.push(next);
        }
    }

    // order.size() != n이면 사이클 존재
    return order;
}
```

**시간복잡도**: O(V + E)

**대표 문제**: BOJ 2252 (줄 세우기), BOJ 1766 (문제집)

---

## 5.5 최소 신장 트리 (MST)

### Kruskal (Union-Find 사용)

```cpp
struct Edge {
    int u, v, cost;
    bool operator<(const Edge& o) const { return cost < o.cost; }
};

// UnionFind는 4.6절 참고

int kruskal(int n, vector<Edge>& edges) {
    sort(edges.begin(), edges.end());
    UnionFind uf(n);
    int totalCost = 0, edgeCount = 0;

    for (auto& [u, v, cost] : edges) {
        if (uf.unite(u, v)) {
            totalCost += cost;
            if (++edgeCount == n - 1) break;
        }
    }
    return totalCost;
}
```

### Prim (Priority Queue 사용)

```cpp
int prim(int n, vector<vector<pair<int,int>>>& adj) {
    vector<bool> inMST(n + 1, false);
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    pq.push({0, 1});  // {비용, 노드}
    int totalCost = 0, cnt = 0;

    while (!pq.empty() && cnt < n) {
        auto [cost, u] = pq.top(); pq.pop();
        if (inMST[u]) continue;
        inMST[u] = true;
        totalCost += cost;
        cnt++;
        for (auto [w, v] : adj[u]) {
            if (!inMST[v]) pq.push({w, v});
        }
    }
    return totalCost;
}
```

**시간복잡도**: Kruskal O(E log E), Prim O(E log V)

**대표 문제**: BOJ 1197 (최소 스패닝 트리), BOJ 1922 (네트워크 연결)

---

## 5.6 문자열 매칭 (KMP)

### 실패 함수 + 패턴 매칭

```cpp
// 실패 함수 (부분 일치 테이블)
vector<int> getFailure(const string& pattern) {
    int m = pattern.size();
    vector<int> fail(m, 0);
    int j = 0;
    for (int i = 1; i < m; i++) {
        while (j > 0 && pattern[i] != pattern[j])
            j = fail[j - 1];
        if (pattern[i] == pattern[j])
            fail[i] = ++j;
    }
    return fail;
}

// KMP 매칭
vector<int> kmp(const string& text, const string& pattern) {
    vector<int> fail = getFailure(pattern);
    vector<int> matches;
    int n = text.size(), m = pattern.size();
    int j = 0;
    for (int i = 0; i < n; i++) {
        while (j > 0 && text[i] != pattern[j])
            j = fail[j - 1];
        if (text[i] == pattern[j]) {
            if (j == m - 1) {
                matches.push_back(i - m + 1);  // 매칭 시작 위치
                j = fail[j];
            } else {
                j++;
            }
        }
    }
    return matches;
}
```

**시간복잡도**: O(N + M)

**대표 문제**: BOJ 1786 (찾기), BOJ 16916 (부분 문자열)

---

## 5.7 Bit Masking

### 비트 연산 기본

```cpp
// 비트 조작
int mask = 0;
mask |= (1 << i);     // i번째 비트 켜기
mask &= ~(1 << i);    // i번째 비트 끄기
mask ^= (1 << i);     // i번째 비트 토글
bool on = mask & (1 << i);  // i번째 비트 확인

// 모든 부분집합 순회
for (int sub = mask; sub > 0; sub = (sub - 1) & mask) {
    // sub는 mask의 부분집합
}
```

### 비트마스크 DP 예시 (외판원 문제, TSP)

```cpp
// dp[visited][current] = visited 상태에서 current에 있을 때 최소 비용
int n;
int cost[20][20];
int dp[1 << 20][20];

int tsp(int visited, int cur) {
    if (visited == (1 << n) - 1) {
        return cost[cur][0] ? cost[cur][0] : INF;  // 시작점으로 복귀
    }
    if (dp[visited][cur] != -1) return dp[visited][cur];

    int ret = INF;
    for (int next = 0; next < n; next++) {
        if (visited & (1 << next)) continue;  // 이미 방문
        if (!cost[cur][next]) continue;       // 길 없음
        ret = min(ret, cost[cur][next] + tsp(visited | (1 << next), next));
    }
    return dp[visited][cur] = ret;
}

// 호출: memset(dp, -1, sizeof(dp)); tsp(1, 0);
```

**대표 문제**: BOJ 11723 (집합), BOJ 2098 (외판원 순회)

---

## 5.8 이분탐색 응용 (Parametric Search)

### 패턴: "최솟값의 최댓값" 또는 "최댓값의 최솟값"

```cpp
// 예: 나무 자르기 - 절단기 높이의 최댓값
// "높이 H로 잘랐을 때 M 이상의 나무를 가져갈 수 있는가?"

bool canCut(vector<int>& trees, int H, int M) {
    long long total = 0;
    for (int t : trees) {
        if (t > H) total += t - H;
    }
    return total >= M;
}

int parametricSearch(vector<int>& trees, int M) {
    int lo = 0, hi = *max_element(trees.begin(), trees.end());
    int ans = 0;
    while (lo <= hi) {
        int mid = (lo + hi) / 2;
        if (canCut(trees, mid, M)) {
            ans = mid;      // 가능하면 더 높여보기
            lo = mid + 1;
        } else {
            hi = mid - 1;   // 불가능하면 낮추기
        }
    }
    return ans;
}
```

### Parametric Search 접근법

1. **답을 이분탐색**의 대상으로 설정
2. **결정 함수** 작성: "이 값이 가능한가?" → bool
3. lo, hi 범위 설정
4. 이분탐색으로 최적값 탐색

**핵심**: 결정 함수가 단조성(monotonic)을 가져야 함

**시간복잡도**: O(N log X), X = 탐색 범위

**대표 문제**: BOJ 2805 (나무 자르기), BOJ 2110 (공유기 설치), BOJ 1654 (랜선 자르기)
