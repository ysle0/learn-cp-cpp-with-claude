# 1. C++ 코딩테스트 필수 문법 & STL

## 1.1 입출력 최적화

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);  // C/C++ 입출력 동기화 해제
    cin.tie(nullptr);                   // cin과 cout 묶음 해제
    cout.tie(nullptr);

    int n;
    cin >> n;
    cout << n << '\n';  // endl 대신 '\n' 사용 (flush 방지)
    return 0;
}
```

### 핵심 포인트
- `sync_with_stdio(false)` 후에는 `scanf`/`printf`와 혼용 금지
- `endl`은 버퍼를 flush하므로 `'\n'`보다 느림
- 대량 입출력 시 `scanf`/`printf`가 더 빠를 수 있음

### 대량 입력 받기

```cpp
// 한 줄에 공백으로 구분된 정수 N개
int n;
cin >> n;
vector<int> v(n);
for (int i = 0; i < n; i++) cin >> v[i];

// 문자열 한 줄 읽기 (공백 포함)
string line;
getline(cin, line);

// 주의: cin >> 후 getline 사용 시 버퍼에 '\n' 남음
int x;
cin >> x;
cin.ignore();  // 버퍼의 '\n' 제거
getline(cin, line);
```

---

## 1.2 String Manipulation

### 주요 함수

```cpp
string s = "Hello World";

// substr(시작위치, 길이)
string sub = s.substr(0, 5);    // "Hello"
string sub2 = s.substr(6);      // "World"

// find (못 찾으면 string::npos 반환)
size_t pos = s.find("World");   // 6
if (s.find("xyz") == string::npos) {
    // 못 찾음
}

// 숫자 ↔ 문자열 변환
int num = stoi("123");           // string → int
long long ll = stoll("999999999999");  // string → long long
double d = stod("3.14");        // string → double
string str = to_string(123);    // int → string

// 문자열 비교
if (s == "Hello World") {}      // 값 비교
if (s < "Zello") {}             // 사전순 비교

// 문자 단위 조작
for (char& c : s) {
    c = tolower(c);  // 소문자로
    c = toupper(c);  // 대문자로
}

// erase & replace
s.erase(5, 1);                  // 5번 인덱스부터 1글자 삭제
s.replace(0, 5, "Hi");          // 0~4를 "Hi"로 교체
```

### stringstream 활용

```cpp
#include <sstream>

// 공백 기준 분리
string line = "10 20 30";
istringstream iss(line);
int a, b, c;
iss >> a >> b >> c;  // 10, 20, 30

// 구분자 기준 분리
string csv = "apple,banana,cherry";
istringstream iss2(csv);
string token;
while (getline(iss2, token, ',')) {
    cout << token << '\n';
}
```

---

## 1.3 STL Containers

### vector

```cpp
vector<int> v;                   // 빈 벡터
vector<int> v(n);                // n개, 0으로 초기화
vector<int> v(n, -1);            // n개, -1로 초기화
vector<vector<int>> v2d(n, vector<int>(m, 0));  // n×m 2차원

v.push_back(10);                 // O(1) amortized
v.pop_back();                    // O(1)
v.size();                        // O(1)
v.empty();                       // O(1)
v.front(); v.back();             // O(1)
v.clear();                       // O(n)
v.resize(100);                   // 크기 변경
v.erase(v.begin() + i);         // O(n) - i번째 원소 삭제

// 중복 제거
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());
```

### map / set (Red-Black Tree 기반, 정렬 유지)

```cpp
// map: key-value 쌍, key 기준 정렬
map<string, int> m;
m["apple"] = 3;                  // O(log n)
m.insert({"banana", 5});         // O(log n)
m.erase("apple");                // O(log n)
m.count("banana");               // 0 or 1, O(log n)
m.find("banana");                // iterator, O(log n)

// 순회 (key 기준 오름차순)
for (auto& [key, val] : m) {
    cout << key << ": " << val << '\n';
}

// set: 중복 없는 정렬된 집합
set<int> s;
s.insert(10);                    // O(log n)
s.erase(10);                     // O(log n)
s.count(10);                     // O(log n)
s.lower_bound(10);               // >= 10인 첫 번째 iterator
s.upper_bound(10);               // > 10인 첫 번째 iterator
```

### unordered_map / unordered_set (Hash 기반)

```cpp
// 평균 O(1), 최악 O(n)
unordered_map<string, int> um;
um["key"] = 100;
um.count("key");

unordered_set<int> us;
us.insert(10);
us.count(10);

// 주의: 정렬 순서 보장 안 됨
// 주의: 커스텀 타입 사용 시 해시 함수 필요
```

### priority_queue (Heap)

```cpp
// 기본: max-heap (큰 값이 top)
priority_queue<int> maxpq;
maxpq.push(10);                  // O(log n)
maxpq.top();                     // O(1) - 최댓값
maxpq.pop();                     // O(log n)

// min-heap (작은 값이 top)
priority_queue<int, vector<int>, greater<int>> minpq;

// pair 사용 시: first 기준 → second 기준 정렬
priority_queue<pair<int,int>> pq;  // first가 큰 것이 top
```

### stack / queue / deque

```cpp
// Stack (LIFO)
stack<int> st;
st.push(10); st.pop(); st.top(); st.empty(); st.size();

// Queue (FIFO)
queue<int> q;
q.push(10); q.pop(); q.front(); q.back(); q.empty();

// Deque (양쪽 삽입/삭제 O(1))
deque<int> dq;
dq.push_front(1); dq.push_back(2);
dq.pop_front();   dq.pop_back();
dq.front();       dq.back();
dq[0];  // 인덱스 접근 O(1)
```

### 컨테이너 시간복잡도 요약

| 컨테이너 | 삽입 | 삭제 | 검색 | 접근 |
|----------|------|------|------|------|
| vector | O(1)* / O(n) | O(n) | O(n) | O(1) |
| map/set | O(log n) | O(log n) | O(log n) | - |
| unordered_map/set | O(1)* | O(1)* | O(1)* | - |
| priority_queue | O(log n) | O(log n) | - | O(1) top |
| stack/queue | O(1) | O(1) | - | O(1) top/front |
| deque | O(1) | O(1) | O(n) | O(1) |

*amortized / average

---

## 1.4 STL Algorithms

### sort

```cpp
vector<int> v = {3, 1, 4, 1, 5};

// 오름차순 (기본)
sort(v.begin(), v.end());

// 내림차순
sort(v.begin(), v.end(), greater<int>());

// 커스텀 정렬 (람다)
sort(v.begin(), v.end(), [](int a, int b) {
    return a > b;  // 내림차순
});

// 구조체 정렬
struct Student {
    string name;
    int score;
};
vector<Student> students;
sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
    if (a.score != b.score) return a.score > b.score;  // 점수 내림차순
    return a.name < b.name;  // 이름 오름차순
});

// stable_sort: 동일 키의 상대 순서 유지
stable_sort(v.begin(), v.end());
```

### lower_bound / upper_bound (이분탐색, 정렬된 배열에서만)

```cpp
vector<int> v = {1, 2, 3, 3, 3, 5, 7};
// 반드시 정렬되어 있어야 함!

// lower_bound: >= val인 첫 번째 위치
auto it = lower_bound(v.begin(), v.end(), 3);  // v[2]
int idx = it - v.begin();  // 2

// upper_bound: > val인 첫 번째 위치
auto it2 = upper_bound(v.begin(), v.end(), 3);  // v[5]

// 특정 값의 개수
int cnt = upper_bound(v.begin(), v.end(), 3) - lower_bound(v.begin(), v.end(), 3);  // 3

// 존재 여부
bool exists = binary_search(v.begin(), v.end(), 3);  // true
```

### next_permutation

```cpp
vector<int> v = {1, 2, 3};
// 반드시 정렬된 상태에서 시작!
sort(v.begin(), v.end());

do {
    for (int x : v) cout << x << ' ';
    cout << '\n';
} while (next_permutation(v.begin(), v.end()));
// 1 2 3 → 1 3 2 → 2 1 3 → 2 3 1 → 3 1 2 → 3 2 1
```

### 기타 유용한 알고리즘

```cpp
#include <numeric>

vector<int> v = {1, 2, 3, 4, 5};

// accumulate: 합계
int sum = accumulate(v.begin(), v.end(), 0);        // 15
long long llsum = accumulate(v.begin(), v.end(), 0LL);  // overflow 방지

// min_element, max_element
auto minIt = min_element(v.begin(), v.end());
auto maxIt = max_element(v.begin(), v.end());

// count, count_if
int cnt = count(v.begin(), v.end(), 3);
int cntEven = count_if(v.begin(), v.end(), [](int x) { return x % 2 == 0; });

// fill
fill(v.begin(), v.end(), 0);

// reverse
reverse(v.begin(), v.end());

// unique (정렬 후 사용, 중복 원소를 뒤로 밀어냄)
sort(v.begin(), v.end());
v.erase(unique(v.begin(), v.end()), v.end());

// rotate
rotate(v.begin(), v.begin() + 2, v.end());  // 왼쪽으로 2칸 회전
```

---

## 1.5 Iterator 활용 & Range-based for

```cpp
vector<int> v = {10, 20, 30, 40, 50};

// Range-based for (가장 간단)
for (int x : v) cout << x << ' ';          // 복사
for (int& x : v) x *= 2;                    // 참조로 수정
for (const auto& x : v) cout << x << ' ';   // 읽기 전용

// Iterator 활용
for (auto it = v.begin(); it != v.end(); ++it) {
    cout << *it << ' ';
}

// 역순 순회
for (auto it = v.rbegin(); it != v.rend(); ++it) {
    cout << *it << ' ';
}

// map 순회
map<string, int> m = {{"a", 1}, {"b", 2}};
for (auto& [key, val] : m) {  // C++17 structured binding
    cout << key << ": " << val << '\n';
}
```

---

## 1.6 pair, tuple, 구조체 정렬

### pair

```cpp
pair<int, int> p = {1, 2};
p.first;   // 1
p.second;  // 2

// make_pair
auto p2 = make_pair(3, 4);

// 비교: first 먼저 → second 비교
vector<pair<int,int>> vp = {{1,3}, {1,2}, {2,1}};
sort(vp.begin(), vp.end());  // {1,2}, {1,3}, {2,1}

// pair를 활용한 좌표 표현
using Point = pair<int, int>;
vector<Point> points;
```

### tuple

```cpp
tuple<int, string, double> t = {1, "hello", 3.14};
auto [id, name, score] = t;  // C++17 structured binding
get<0>(t);  // 1
get<1>(t);  // "hello"
```

### 구조체 + 커스텀 comparator

```cpp
struct Edge {
    int from, to, cost;
};

// 방법 1: 연산자 오버로딩
struct Edge {
    int from, to, cost;
    bool operator<(const Edge& other) const {
        return cost < other.cost;  // cost 오름차순
    }
};
vector<Edge> edges;
sort(edges.begin(), edges.end());

// 방법 2: 람다
sort(edges.begin(), edges.end(), [](const Edge& a, const Edge& b) {
    return a.cost < b.cost;
});

// 방법 3: priority_queue에서 커스텀 비교
struct Compare {
    bool operator()(const Edge& a, const Edge& b) {
        return a.cost > b.cost;  // min-heap (부등호 반대!)
    }
};
priority_queue<Edge, vector<Edge>, Compare> pq;
```

### 주의사항
- `priority_queue`의 comparator는 **부등호가 반대**임 (`>`가 min-heap)
- `sort`의 comparator에서 `<=`를 쓰면 안 됨 (strict weak ordering 위반)
- 구조체를 `map`/`set`의 key로 쓰려면 `operator<` 필요
