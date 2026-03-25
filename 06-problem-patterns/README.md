# 6. 유형별 문제풀이 패턴

## 6.1 구현 / 시뮬레이션

### 인식 방법
- "규칙대로 ~하라", "시뮬레이션", 격자 이동, 방향 전환
- 특별한 알고리즘보다 **정확한 구현**이 핵심

### 방향 배열 패턴

```cpp
// 상하좌우
const int dx[] = {-1, 1, 0, 0};
const int dy[] = {0, 0, -1, 1};

// 8방향
const int dx[] = {-1, -1, -1, 0, 0, 1, 1, 1};
const int dy[] = {-1, 0, 1, -1, 1, -1, 0, 1};

// 방향 회전 (시계: 우→하→좌→상)
// 0:우, 1:하, 2:좌, 3:상
const int dx[] = {0, 1, 0, -1};
const int dy[] = {1, 0, -1, 0};
int dir = 0;
dir = (dir + 1) % 4;  // 시계 90도 회전
dir = (dir + 3) % 4;  // 반시계 90도 회전
```

### 시뮬레이션 템플릿

```cpp
int n, m;
int board[101][101];

void simulate() {
    // 1. 상태 업데이트 (동시 처리가 필요하면 복사본 사용)
    int temp[101][101];
    memcpy(temp, board, sizeof(board));

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            // temp를 참조하여 board 업데이트
            // (동시 처리 보장)
        }
    }
}
```

### 핵심 팁
- 범위 체크를 항상 먼저: `0 <= nx && nx < n && 0 <= ny && ny < m`
- 동시에 일어나는 변화는 **복사본** 사용
- 좌표계 주의: 행(x)=세로, 열(y)=가로

**대표 문제**: BOJ 14503 (로봇 청소기), BOJ 15683 (감시), BOJ 14891 (톱니바퀴)

---

## 6.2 수학

### GCD (유클리드 호제법) & LCM

```cpp
int gcd(int a, int b) {
    while (b) { a %= b; swap(a, b); }
    return a;
}
int lcm(int a, int b) {
    return a / gcd(a, b) * b;  // 오버플로우 방지: 나누기 먼저
}

// 여러 수의 GCD/LCM
int g = arr[0];
for (int i = 1; i < n; i++) g = gcd(g, arr[i]);
```

### 소수 판별 & 에라토스테네스의 체

```cpp
// 단일 판별: O(√N)
bool isPrime(int n) {
    if (n < 2) return false;
    for (int i = 2; i * i <= n; i++) {
        if (n % i == 0) return false;
    }
    return true;
}

// 에라토스테네스의 체: O(N log log N)
const int MAX = 10000001;
bool sieve[MAX];  // true = 소수 아님 (합성수)

void eratosthenes() {
    sieve[0] = sieve[1] = true;
    for (int i = 2; i * i < MAX; i++) {
        if (!sieve[i]) {
            for (int j = i * i; j < MAX; j += i) {
                sieve[j] = true;
            }
        }
    }
}
```

### 조합론 (nCr)

```cpp
// 파스칼의 삼각형으로 nCr 구하기: O(N^2)
long long C[1001][1001];

void precompute() {
    for (int i = 0; i <= 1000; i++) {
        C[i][0] = 1;
        for (int j = 1; j <= i; j++) {
            C[i][j] = (C[i-1][j-1] + C[i-1][j]) % MOD;
        }
    }
}

// 팩토리얼 + 역원으로 nCr (MOD가 소수일 때)
long long fact[1000001], inv_fact[1000001];

long long power(long long a, long long b, long long mod) {
    long long ret = 1;
    a %= mod;
    while (b > 0) {
        if (b & 1) ret = ret * a % mod;
        a = a * a % mod;
        b >>= 1;
    }
    return ret;
}

void precompute2(int n) {
    fact[0] = 1;
    for (int i = 1; i <= n; i++) fact[i] = fact[i-1] * i % MOD;
    inv_fact[n] = power(fact[n], MOD - 2, MOD);
    for (int i = n - 1; i >= 0; i--) inv_fact[i] = inv_fact[i+1] * (i+1) % MOD;
}

long long nCr(int n, int r) {
    if (r < 0 || r > n) return 0;
    return fact[n] % MOD * inv_fact[r] % MOD * inv_fact[n-r] % MOD;
}
```

**대표 문제**: BOJ 2609 (최대공약수와 최소공배수), BOJ 1929 (소수 구하기), BOJ 11050 (이항 계수)

---

## 6.3 좌표 / 격자 탐색

### 인식 방법
- 2차원 격자, 미로, 섬 개수, 영역 찾기
- "상하좌우 이동", "연결된 영역"

### 플러드 필 (Flood Fill) - 연결 요소 세기

```cpp
int n, m;
int board[1001][1001];
bool visited[1001][1001];
const int dx[] = {-1, 1, 0, 0};
const int dy[] = {0, 0, -1, 1};

void bfs(int sx, int sy) {
    queue<pair<int,int>> q;
    visited[sx][sy] = true;
    q.push({sx, sy});
    while (!q.empty()) {
        auto [x, y] = q.front(); q.pop();
        for (int d = 0; d < 4; d++) {
            int nx = x + dx[d], ny = y + dy[d];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
            if (visited[nx][ny] || board[nx][ny] == 0) continue;
            visited[nx][ny] = true;
            q.push({nx, ny});
        }
    }
}

int countRegions() {
    int cnt = 0;
    for (int i = 0; i < n; i++)
        for (int j = 0; j < m; j++)
            if (!visited[i][j] && board[i][j] == 1) {
                bfs(i, j);
                cnt++;
            }
    return cnt;
}
```

### 격자 최단 거리 (BFS)

```cpp
int shortestPath(int sx, int sy, int ex, int ey) {
    int dist[1001][1001];
    memset(dist, -1, sizeof(dist));
    queue<pair<int,int>> q;
    dist[sx][sy] = 0;
    q.push({sx, sy});
    while (!q.empty()) {
        auto [x, y] = q.front(); q.pop();
        if (x == ex && y == ey) return dist[x][y];
        for (int d = 0; d < 4; d++) {
            int nx = x + dx[d], ny = y + dy[d];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
            if (dist[nx][ny] != -1 || board[nx][ny] == 0) continue;
            dist[nx][ny] = dist[x][y] + 1;
            q.push({nx, ny});
        }
    }
    return -1;  // 도달 불가
}
```

**대표 문제**: BOJ 2667 (단지번호붙이기), BOJ 2178 (미로 탐색), BOJ 7576 (토마토)

---

## 6.4 투 포인터 + 정렬 조합

### 인식 방법
- "두 수/세 수의 합", "구간의 합이 K 이상/이하"
- 정렬 후 양쪽에서 접근

### 세 수의 합 (3Sum)

```cpp
// 정렬 + 투포인터: O(N^2)
sort(arr.begin(), arr.end());
for (int i = 0; i < n - 2; i++) {
    if (i > 0 && arr[i] == arr[i-1]) continue;  // 중복 건너뛰기
    int left = i + 1, right = n - 1;
    while (left < right) {
        long long sum = (long long)arr[i] + arr[left] + arr[right];
        if (sum == target) {
            // 찾음! {arr[i], arr[left], arr[right]}
            left++; right--;
            while (left < right && arr[left] == arr[left-1]) left++;
        } else if (sum < target) {
            left++;
        } else {
            right--;
        }
    }
}
```

### Meet in the Middle

```cpp
// N ≤ 40일 때 부분합: 반으로 나눠서 각각 2^(N/2) 구함
vector<long long> leftSums, rightSums;

void generateSums(vector<int>& arr, int start, int end, vector<long long>& sums) {
    int len = end - start;
    for (int mask = 0; mask < (1 << len); mask++) {
        long long sum = 0;
        for (int i = 0; i < len; i++)
            if (mask & (1 << i)) sum += arr[start + i];
        sums.push_back(sum);
    }
}
// 이후 rightSums 정렬 → leftSum에 대해 이분탐색으로 target - leftSum 찾기
```

**대표 문제**: BOJ 3273 (두 수의 합), BOJ 7453 (합이 0인 네 정수), BOJ 1208 (부분수열의 합 2)

---

## 6.5 DP 점화식 도출 패턴

### 패턴 1: "~까지의 최소/최대" → `dp[i] = min/max(...)`

```cpp
// 계단 오르기: dp[i] = i번째 계단까지의 최대 점수
dp[1] = stair[1];
dp[2] = stair[1] + stair[2];
for (int i = 3; i <= n; i++) {
    dp[i] = max(dp[i-2] + stair[i],
                dp[i-3] + stair[i-1] + stair[i]);
}
```

### 패턴 2: "경우의 수" → `dp[i] += dp[j]`

```cpp
// 동전 교환: 금액 target을 만드는 경우의 수
dp[0] = 1;  // 0원을 만드는 방법: 아무것도 안 쓰기
for (int coin : coins) {
    for (int j = coin; j <= target; j++) {
        dp[j] += dp[j - coin];
    }
}
```

### 패턴 3: "부분 문제의 최적해" → 구간 DP

```cpp
// 행렬 곱셈 순서 / 파일 합치기
// dp[i][j] = i~j 구간을 합치는 최소 비용
for (int len = 2; len <= n; len++) {       // 구간 길이
    for (int i = 1; i + len - 1 <= n; i++) { // 시작점
        int j = i + len - 1;                  // 끝점
        dp[i][j] = INF;
        for (int k = i; k < j; k++) {        // 분할점
            dp[i][j] = min(dp[i][j], dp[i][k] + dp[k+1][j] + cost(i, j));
        }
    }
}
```

### DP 점화식 세우는 팁

1. **상태 정의**: dp[i]가 무엇을 의미하는지 명확히
2. **기저 조건**: dp[0], dp[1] 등 초기값 설정
3. **전이**: dp[i]를 이전 상태들로부터 어떻게 구하는지
4. **순서**: 전이에 필요한 값이 먼저 계산되도록

### 흔한 DP 상태 정의

| 문제 유형 | 상태 | 예시 |
|----------|------|------|
| 1차원 | dp[i]: i번째까지의 최적해 | 계단 오르기, 타일 채우기 |
| 2차원 | dp[i][j]: i번째 물건, 용량 j | 배낭 문제 |
| 문자열 | dp[i][j]: A의 i번째, B의 j번째 | LCS, 편집 거리 |
| 구간 | dp[i][j]: i~j 구간의 최적해 | 행렬 곱셈, 파일 합치기 |
| 비트마스크 | dp[mask][i]: 방문 상태, 현재 위치 | TSP |

**대표 문제**: BOJ 2579 (계단 오르기), BOJ 11066 (파일 합치기), BOJ 2098 (외판원 순회)

---

## 6.6 그래프 모델링 패턴

### 인식 방법
문제에서 직접 "그래프"를 언급하지 않아도, 다음 패턴이면 그래프로 변환:

| 문제 패턴 | 그래프 모델링 |
|----------|-------------|
| A에서 B로 이동 가능 | A → B 간선 |
| 상태 전이 (A 상태 → B 상태) | 상태를 노드로, 전이를 간선으로 |
| 격자에서 이동 | (x,y)를 노드로, 상하좌우를 간선으로 |
| 의존 관계 (A 후에 B) | A → B 방향 간선 (위상정렬) |
| 그룹핑/연결 여부 | Union-Find 또는 연결 요소 |

### 상태 공간 탐색

```cpp
// 예: "벽을 최대 1번 부수고 이동" → 상태에 벽 부순 횟수 포함
// dist[x][y][벽부순횟수]
int dist[1001][1001][2];

struct State {
    int x, y, broken;
};

void bfs() {
    memset(dist, -1, sizeof(dist));
    queue<State> q;
    dist[0][0][0] = 1;
    q.push({0, 0, 0});
    while (!q.empty()) {
        auto [x, y, b] = q.front(); q.pop();
        for (int d = 0; d < 4; d++) {
            int nx = x + dx[d], ny = y + dy[d];
            if (nx < 0 || nx >= n || ny < 0 || ny >= m) continue;
            // 빈 칸으로 이동
            if (board[nx][ny] == 0 && dist[nx][ny][b] == -1) {
                dist[nx][ny][b] = dist[x][y][b] + 1;
                q.push({nx, ny, b});
            }
            // 벽을 부수고 이동 (아직 안 부쨌을 때만)
            if (board[nx][ny] == 1 && b == 0 && dist[nx][ny][1] == -1) {
                dist[nx][ny][1] = dist[x][y][b] + 1;
                q.push({nx, ny, 1});
            }
        }
    }
}
```

### 핵심: 그래프 변환 사고법

1. **노드 정의**: 문제에서 "상태"가 무엇인가?
2. **간선 정의**: 한 상태에서 다른 상태로 어떻게 전이하는가?
3. **가중치**: 전이에 비용이 있는가?
4. **알고리즘 선택**: BFS(무가중치), 다익스트라(가중치), 위상정렬(순서) 등

**대표 문제**: BOJ 2206 (벽 부수고 이동하기), BOJ 17142 (연구소 3), BOJ 16236 (아기 상어)
