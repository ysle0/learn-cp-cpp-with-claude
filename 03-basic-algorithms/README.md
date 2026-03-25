# 3. 기본 알고리즘 유형

## 3.1 Brute Force / 완전탐색

### 개념
가능한 모든 경우를 탐색하여 답을 찾는 방법. N이 작을 때(≤ 20) 가장 확실한 접근법.

### 순열 (Permutation)

```cpp
// next_permutation 사용
vector<int> v = {1, 2, 3};
sort(v.begin(), v.end());
do {
    // v[0], v[1], v[2] 사용
} while (next_permutation(v.begin(), v.end()));

// 재귀로 직접 구현
vector<int> perm;
vector<bool> used(n, false);

void permutation(int depth) {
    if (depth == n) {
        // perm 사용
        return;
    }
    for (int i = 0; i < n; i++) {
        if (used[i]) continue;
        used[i] = true;
        perm.push_back(i);
        permutation(depth + 1);
        perm.pop_back();
        used[i] = false;
    }
}
```

### 조합 (Combination)

```cpp
// N개 중 R개 선택
vector<int> chosen;

void combination(int start, int r) {
    if (r == 0) {
        // chosen 사용
        return;
    }
    for (int i = start; i < n; i++) {
        chosen.push_back(i);
        combination(i + 1, r - 1);
        chosen.pop_back();
    }
}
// combination(0, r) 호출
```

### 부분집합 (비트마스크)

```cpp
// N개 원소의 모든 부분집합: 2^N개
for (int mask = 0; mask < (1 << n); mask++) {
    for (int i = 0; i < n; i++) {
        if (mask & (1 << i)) {
            // i번째 원소 선택됨
        }
    }
}
```

**시간복잡도**: 순열 O(N!), 조합 O(nCr), 부분집합 O(2^N)

**대표 문제**: BOJ 15649~15652 (N과 M 시리즈), BOJ 1182 (부분수열의 합)

---

## 3.2 Sorting & Searching

### 커스텀 정렬

```cpp
// 다중 조건 정렬
struct Student {
    string name;
    int kor, eng, math;
};

sort(students.begin(), students.end(), [](const Student& a, const Student& b) {
    if (a.kor != b.kor) return a.kor > b.kor;    // 국어 내림차순
    if (a.eng != b.eng) return a.eng < b.eng;    // 영어 오름차순
    if (a.math != b.math) return a.math > b.math; // 수학 내림차순
    return a.name < b.name;                        // 이름 오름차순
});
```

### 이분탐색 (Binary Search)

```cpp
// 직접 구현
int binarySearch(vector<int>& v, int target) {
    int lo = 0, hi = v.size() - 1;
    while (lo <= hi) {
        int mid = lo + (hi - lo) / 2;  // 오버플로우 방지
        if (v[mid] == target) return mid;
        else if (v[mid] < target) lo = mid + 1;
        else hi = mid - 1;
    }
    return -1;  // 못 찾음
}

// STL 사용
// lower_bound: >= target인 첫 위치
// upper_bound: > target인 첫 위치
auto it = lower_bound(v.begin(), v.end(), target);
if (it != v.end() && *it == target) {
    int idx = it - v.begin();
}
```

**시간복잡도**: 정렬 O(N log N), 이분탐색 O(log N)

**대표 문제**: BOJ 10825 (국영수), BOJ 1920 (수 찾기), BOJ 10816 (숫자 카드 2)

---

## 3.3 Two Pointer / Sliding Window

### 투 포인터 (두 포인터가 같은 방향)

```cpp
// 부분합이 S 이상인 최소 길이 구간
int minLen = INF;
int sum = 0;
int left = 0;
for (int right = 0; right < n; right++) {
    sum += arr[right];
    while (sum >= S) {
        minLen = min(minLen, right - left + 1);
        sum -= arr[left++];
    }
}
```

### 투 포인터 (양쪽에서 접근)

```cpp
// 정렬된 배열에서 두 수의 합이 target인 쌍 찾기
sort(v.begin(), v.end());
int left = 0, right = n - 1;
while (left < right) {
    int sum = v[left] + v[right];
    if (sum == target) {
        // 찾음!
        left++; right--;
    } else if (sum < target) {
        left++;
    } else {
        right--;
    }
}
```

### 슬라이딩 윈도우 (고정 크기)

```cpp
// 크기 K인 연속 부분배열의 최대 합
int sum = 0;
for (int i = 0; i < k; i++) sum += arr[i];  // 초기 윈도우

int maxSum = sum;
for (int i = k; i < n; i++) {
    sum += arr[i] - arr[i - k];  // 한 칸 슬라이드
    maxSum = max(maxSum, sum);
}
```

**시간복잡도**: O(N)

**대표 문제**: BOJ 2003 (수들의 합 2), BOJ 2559 (수열), BOJ 1806 (부분합)

---

## 3.4 Greedy

### 개념
각 단계에서 지역적으로 최적인 선택을 하여 전역 최적해를 구하는 방법.
**핵심**: 그리디가 최적해를 보장한다는 증명(교환 논증, 귀류법)이 필요.

### 활동 선택 문제 (대표적 그리디)

```cpp
// 끝나는 시간이 빠른 순으로 정렬 → 겹치지 않는 최대 개수
struct Meeting {
    int start, end;
};

sort(meetings.begin(), meetings.end(), [](const Meeting& a, const Meeting& b) {
    if (a.end != b.end) return a.end < b.end;
    return a.start < b.start;
});

int count = 0, lastEnd = 0;
for (auto& m : meetings) {
    if (m.start >= lastEnd) {
        count++;
        lastEnd = m.end;
    }
}
```

### 그리디 판단 기준
1. **탐욕 선택 속성**: 현재 최선의 선택이 이후 선택에 영향을 주지 않음
2. **최적 부분 구조**: 부분 문제의 최적해가 전체 최적해를 구성

**시간복잡도**: 보통 O(N log N) (정렬 포함)

**대표 문제**: BOJ 1931 (회의실 배정), BOJ 11047 (동전 0), BOJ 13305 (주유소)

---

## 3.5 Divide and Conquer

### 개념
문제를 작은 부분으로 나누고, 각각을 해결한 뒤, 결과를 합치는 방법.

### 거듭제곱 (분할정복)

```cpp
// a^n mod m을 O(log n)에 계산
long long power(long long a, long long n, long long m) {
    long long result = 1;
    a %= m;
    while (n > 0) {
        if (n & 1) result = result * a % m;
        a = a * a % m;
        n >>= 1;
    }
    return result;
}
```

### 병합 정렬 아이디어 (inversion count)

```cpp
// 배열에서 역전 쌍의 수 세기
long long mergeCount(vector<int>& arr, int lo, int hi) {
    if (lo >= hi) return 0;
    int mid = (lo + hi) / 2;
    long long cnt = 0;
    cnt += mergeCount(arr, lo, mid);
    cnt += mergeCount(arr, mid + 1, hi);

    vector<int> temp;
    int i = lo, j = mid + 1;
    while (i <= mid && j <= hi) {
        if (arr[i] <= arr[j]) {
            temp.push_back(arr[i++]);
        } else {
            cnt += (mid - i + 1);  // 역전 쌍 카운트
            temp.push_back(arr[j++]);
        }
    }
    while (i <= mid) temp.push_back(arr[i++]);
    while (j <= hi) temp.push_back(arr[j++]);
    for (int k = lo; k <= hi; k++) arr[k] = temp[k - lo];
    return cnt;
}
```

**시간복잡도**: O(N log N)

**대표 문제**: BOJ 1629 (곱셈), BOJ 2447 (별 찍기 - 10)

---

## 3.6 Recursion & Backtracking

### 백트래킹 기본 구조

```cpp
void backtrack(int depth, /* 상태 */) {
    // 기저 조건
    if (depth == target) {
        // 답 처리
        return;
    }

    for (/* 선택지 */) {
        if (/* 가지치기: 유망하지 않으면 건너뛰기 */) continue;

        // 선택
        choose();

        // 재귀
        backtrack(depth + 1);

        // 선택 취소 (원상복구)
        unchoose();
    }
}
```

### N-Queen

```cpp
int n, ans = 0;
int col[15];  // col[i] = i번 행의 퀸이 놓인 열

bool canPlace(int row, int c) {
    for (int i = 0; i < row; i++) {
        if (col[i] == c) return false;              // 같은 열
        if (abs(col[i] - c) == abs(i - row)) return false;  // 대각선
    }
    return true;
}

void solve(int row) {
    if (row == n) {
        ans++;
        return;
    }
    for (int c = 0; c < n; c++) {
        if (!canPlace(row, c)) continue;
        col[row] = c;
        solve(row + 1);
    }
}
// solve(0) 호출
```

### 핵심 포인트
- **가지치기(pruning)** 가 성능의 핵심. 유망하지 않은 경우를 빨리 걸러내야 함
- 선택 → 재귀 → 선택 취소 패턴을 반드시 지킬 것
- `visited` 배열 등으로 중복 방문 방지

**시간복잡도**: 최악 O(N!), 가지치기에 따라 크게 줄어듦

**대표 문제**: BOJ 9663 (N-Queen), BOJ 15649 (N과 M), BOJ 14888 (연산자 끼워넣기)
