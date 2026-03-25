# 2. C++ 코딩테스트 Hack & 팁

## 2.1 매크로 & 타이핑 단축

```cpp
#include <bits/stdc++.h>
using namespace std;

// 타입 단축
using ll = long long;
using pii = pair<int, int>;
using vi = vector<int>;

// 반복문 단축
#define rep(i, n) for (int i = 0; i < (n); i++)
#define rep1(i, n) for (int i = 1; i <= (n); i++)
#define all(v) (v).begin(), (v).end()

// 자주 쓰는 상수
const int INF = 1e9;
const ll LLINF = 1e18;
const int MOD = 1e9 + 7;
const int dx[] = {-1, 1, 0, 0};  // 상하좌우
const int dy[] = {0, 0, -1, 1};

// 사용 예시
vi v(n);
rep(i, n) cin >> v[i];
sort(all(v));
```

---

## 2.2 빈출 실수 모음

### 1) 정수 오버플로우

```cpp
// ❌ 오버플로우
int a = 100000, b = 100000;
int result = a * b;  // int 범위 초과! (10^10 > 2*10^9)

// ✅ long long 사용
long long result = (long long)a * b;
// 또는
long long result = 1LL * a * b;
```

**오버플로우가 발생하는 흔한 상황:**
- N이 10만 이상일 때 N*N
- 합계를 구할 때 (최대 10^5 * 10^9 = 10^14)
- 조합/팩토리얼 계산
- 중간 계산 결과가 커지는 경우

### 2) 0-index vs 1-index

```cpp
// ❌ 혼동
int arr[101];  // 1-index로 쓸 거면 크기 +1
for (int i = 1; i <= n; i++) cin >> arr[i];
// arr[0]은 미사용, arr[n]까지 사용

// 주의: vector는 기본 0-index
vector<int> v(n);
for (int i = 0; i < n; i++) cin >> v[i];
```

### 3) 배열 범위 초과

```cpp
// ❌ 격자 탐색에서 범위 체크 누락
int board[100][100];
int nx = x + dx[i];
int ny = y + dy[i];
board[nx][ny];  // nx, ny가 음수이거나 범위 밖이면 UB!

// ✅ 범위 체크 먼저
if (nx >= 0 && nx < n && ny >= 0 && ny < m) {
    board[nx][ny];
}
```

### 4) 초기화 누락

```cpp
// ❌ visited 배열 초기화 안 함
bool visited[1001];  // 쓰레기 값!

// ✅ 초기화
bool visited[1001] = {};           // 모두 false
memset(visited, false, sizeof(visited));
fill(visited, visited + 1001, false);

// ❌ 여러 테스트케이스에서 초기화 안 함
int T;
cin >> T;
while (T--) {
    // 매 테스트케이스마다 배열 초기화 필요!
    memset(visited, false, sizeof(visited));
    // ...
}
```

### 5) 기타 흔한 실수

```cpp
// ❌ 정렬 comparator에서 <= 사용
sort(v.begin(), v.end(), [](int a, int b) {
    return a <= b;  // strict weak ordering 위반 → 런타임 에러!
});

// ✅ < 사용
sort(v.begin(), v.end(), [](int a, int b) {
    return a < b;
});

// ❌ 나눗셈에서 0으로 나누기
int avg = sum / cnt;  // cnt가 0이면?

// ❌ 음수 나머지
int mod = -7 % 3;  // C++에서 -1 (언어마다 다름)
int mod = ((-7 % 3) + 3) % 3;  // 항상 양수로 만들기
```

---

## 2.3 N 범위 → 허용 시간복잡도

코딩테스트에서 1초 ≈ 10^8 연산 기준:

| N 범위 | 허용 시간복잡도 | 알고리즘 |
|--------|---------------|---------|
| N ≤ 10 | O(N!) | 순열, 완전탐색 |
| N ≤ 20 | O(2^N) | 비트마스크, 부분집합 |
| N ≤ 25 | O(2^(N/2)) | Meet in the Middle |
| N ≤ 50 | O(N^4) | 4중 반복 (드물지만 가능) |
| N ≤ 500 | O(N^3) | 플로이드-워셜, 행렬 곱셈 |
| N ≤ 5,000 | O(N^2) | 버블 정렬, 2중 반복 |
| N ≤ 100,000 | O(N log N) | 정렬, 이분탐색, 세그먼트 트리 |
| N ≤ 1,000,000 | O(N) | 투포인터, 슬라이딩 윈도우 |
| N ≤ 10,000,000 | O(N) | 에라토스테네스의 체 |
| N > 10^8 | O(log N), O(1) | 수학, 이분탐색 |

### 빠른 판단법

```
문제를 읽고 → N 확인 → 위 표에서 허용 복잡도 확인 → 알고리즘 선택
```

---

## 2.4 디버깅 테크닉

### assert 활용

```cpp
#include <cassert>

int binarySearch(vector<int>& v, int target) {
    assert(!v.empty());  // 빈 배열이면 즉시 종료
    int lo = 0, hi = v.size() - 1;
    assert(lo <= hi);
    // ...
}
```

### 디버깅 출력 매크로

```cpp
#ifdef LOCAL
#define debug(x) cerr << #x << " = " << (x) << '\n'
#define debugv(v) { cerr << #v << " = ["; for(auto& x : v) cerr << x << " "; cerr << "]\n"; }
#else
#define debug(x)
#define debugv(v)
#endif

// 사용
debug(n);       // n = 5
debugv(v);      // v = [1 2 3 4 5 ]
```

### 코너케이스 체크리스트

제출 전에 반드시 확인:

- [ ] N = 0 또는 N = 1인 경우
- [ ] 최솟값 / 최댓값 경계
- [ ] 음수 입력
- [ ] 모든 원소가 같은 경우
- [ ] 이미 정렬된 / 역순 정렬된 경우
- [ ] 빈 입력
- [ ] 오버플로우 가능성 (long long 필요?)
- [ ] 0-index / 1-index 혼동
- [ ] 여러 테스트케이스에서 초기화

---

## 2.5 입출력 포맷 트릭

```cpp
// '\n' vs endl
cout << n << '\n';   // ✅ 빠름
cout << n << endl;   // ❌ flush 발생, 느림

// printf 포맷팅
printf("%d\n", n);              // 정수
printf("%lld\n", ll_val);      // long long
printf("%.2f\n", 3.14159);     // 소수점 2자리: 3.14
printf("%04d\n", 42);          // 0 패딩: 0042
printf("%10d\n", 42);          // 우측 정렬:         42
printf("%-10d|\n", 42);        // 좌측 정렬: 42        |

// cout 포맷팅
cout << fixed << setprecision(2) << 3.14159 << '\n';  // 3.14

// 빠른 입력 (대량 데이터)
// 방법 1: scanf
int n;
scanf("%d", &n);

// 방법 2: getchar 기반 빠른 입력
inline int readInt() {
    int x = 0, f = 1;
    char c = getchar();
    while (c < '0' || c > '9') { if (c == '-') f = -1; c = getchar(); }
    while (c >= '0' && c <= '9') { x = x * 10 + (c - '0'); c = getchar(); }
    return x * f;
}
```

---

## 2.6 코딩테스트 템플릿

```cpp
#include <bits/stdc++.h>
using namespace std;

using ll = long long;
using pii = pair<int, int>;
using vi = vector<int>;

const int INF = 1e9;
const ll LLINF = 1e18;
const int MOD = 1e9 + 7;
const int dx[] = {-1, 1, 0, 0};
const int dy[] = {0, 0, -1, 1};

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(nullptr);



    return 0;
}
```

### GCD / LCM

```cpp
// C++17 이상
#include <numeric>
int g = gcd(12, 8);   // 4
int l = lcm(12, 8);   // 24

// 직접 구현
int gcd(int a, int b) {
    while (b) { a %= b; swap(a, b); }
    return a;
}
int lcm(int a, int b) {
    return a / gcd(a, b) * b;  // 오버플로우 방지
}
```

### 2차원 배열 입력

```cpp
int n, m;
cin >> n >> m;
vector<vector<int>> board(n, vector<int>(m));
for (int i = 0; i < n; i++)
    for (int j = 0; j < m; j++)
        cin >> board[i][j];

// 문자 격자
vector<string> grid(n);
for (int i = 0; i < n; i++) cin >> grid[i];
// grid[i][j]로 접근
```

### 그래프 입력

```cpp
int n, m;  // 노드 수, 간선 수
cin >> n >> m;
vector<vector<int>> adj(n + 1);  // 1-indexed
for (int i = 0; i < m; i++) {
    int u, v;
    cin >> u >> v;
    adj[u].push_back(v);
    adj[v].push_back(u);  // 무방향 그래프
}
```
