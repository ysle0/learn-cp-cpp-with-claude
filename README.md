# C++ 코딩테스트 준비 with Claude

> 프로그래머스 레벨2 70%+ → 레벨3 안정 + 회사 코테 합격을 목표로 한 학습 로드맵

---

## 목차

### 0. 스터디 플랜
- [현재 수준 진단 & 목표 설정](#0-스터디-플랜)
- 8주 로드맵 (주 단위 학습 계획)
- 일일 루틴 (문제 수, 복습 주기)
- 약점 파악 & 보완 전략

### 1. C++ 코딩테스트 필수 문법 & STL
- 입출력 최적화 (`ios_base::sync_with_stdio`, `cin.tie`)
- String manipulation (`substr`, `find`, `stoi`/`to_string`, `stringstream`)
- STL Containers (`vector`, `map`, `set`, `unordered_map`, `priority_queue`, `deque`, `stack`, `queue`)
- STL Algorithms (`sort`, `lower_bound`/`upper_bound`, `next_permutation`, `accumulate`, `unique`)
- Iterator 활용 & Range-based for
- `pair`, `tuple`, 구조체 정렬 (커스텀 comparator)

### 2. C++ 코딩테스트 Hack & 팁
- 매크로 & 타이핑 단축 (`#define`, `typedef`, `using`)
- 빈출 실수 모음 (overflow, 0-index vs 1-index, 범위 초과, 초기화 누락)
- N 범위 → 허용 시간복잡도 매핑 (빠른 접근법 판단)
- 디버깅 테크닉 (`assert`, 코너케이스 체크리스트)
- 입출력 포맷 트릭 (`'\n'` vs `endl`, `printf` 포맷팅)
- 자주 쓰는 코드 스니펫 / 템플릿

### 3. 기본 알고리즘 유형
- Brute Force / 완전탐색
- Sorting & Searching (이분탐색 포함)
- Two Pointer / Sliding Window
- Greedy
- Divide and Conquer
- Recursion & Backtracking

### 4. 핵심 자료구조
- Stack / Queue / Deque
- Hash Map / Hash Set
- Heap (Priority Queue)
- Tree / Binary Tree / BST
- Trie
- Union-Find (Disjoint Set)
- Segment Tree / Fenwick Tree (심화)

### 5. 회사 코테 빈출 알고리즘
- DFS / BFS (그래프 탐색)
- Dynamic Programming (Top-down / Bottom-up)
- 최단경로 (Dijkstra, Floyd-Warshall, Bellman-Ford)
- 위상정렬 (Topological Sort)
- 최소 신장 트리 (Kruskal, Prim)
- 문자열 매칭 (KMP, Rabin-Karp)
- Bit Masking
- 이분탐색 응용 (Parametric Search)

### 6. 유형별 문제풀이 패턴
- 구현 / 시뮬레이션
- 수학 (GCD, LCM, 소수 판별, 조합론)
- 좌표 / 격자 탐색 (BFS/DFS on grid)
- 투 포인터 + 정렬 조합
- DP 점화식 도출 패턴
- 그래프 모델링 패턴 (문제 → 그래프 변환)

### 7. 추천 문제풀이
- 난이도별 문제 리스트 (기본 / 중급 / 고급)
- 유형별 추천 문제 (BOJ / 프로그래머스 / LeetCode)
- `solutions/` 디렉토리에 직접 풀이 저장

---

## 디렉토리 구조

```
.
├── README.md
├── 00-study-plan/          # 스터디 플랜 & 진단
├── 01-cpp-essentials/      # C++ 필수 문법 & STL
├── 02-tips-and-hacks/      # 코테 팁 & 핵
├── 03-basic-algorithms/    # 기본 알고리즘
├── 04-data-structures/     # 핵심 자료구조
├── 05-frequent-algorithms/ # 회사 코테 빈출 알고리즘
├── 06-problem-patterns/    # 유형별 문제풀이 패턴
└── 07-solutions/           # 추천 문제 & 풀이
    ├── boj/
    ├── programmers/
    └── leetcode/
```

---

## 8주 스터디 플랜

> 현재 수준: 프로그래머스 레벨2 70%+ 풀이 가능
> 목표: 레벨3 안정 풀이 + 주요 기업 코테 통과

| 주차 | 주제 | 핵심 키워드 | 일일 문제 수 |
|------|------|------------|-------------|
| **1주차** | C++ 무기 점검 | STL 총정리, 입출력 최적화, 코드 템플릿 완성 | 2-3문제 |
| **2주차** | 완전탐색 & 백트래킹 강화 | Permutation, 조합, N-Queen, 부분집합 | 2-3문제 |
| **3주차** | DFS / BFS 마스터 | 그래프 탐색, 격자 탐색, 연결 요소, 최단거리 | 2-3문제 |
| **4주차** | DP 기초 → 중급 | 1차원 DP, 2차원 DP, 배낭문제, LIS | 2문제 (정리 중심) |
| **5주차** | DP 심화 + 그리디 | 구간 DP, 비트마스크 DP, 그리디 증명 패턴 | 2문제 |
| **6주차** | 그래프 심화 | 다익스트라, 위상정렬, 유니온파인드, MST | 2문제 |
| **7주차** | 이분탐색 & 투포인터 | Parametric Search, 슬라이딩 윈도우, 복합 문제 | 2-3문제 |
| **8주차** | 실전 모의고사 | 시간 제한 풀이, 약점 유형 집중 보완 | 3문제 (타이머) |

### 일일 루틴
1. **새 문제 풀기** (40-60분 제한, 못 풀면 풀이 확인 후 다음날 재도전)
2. **오답 복습** (2일 전 못 푼 문제 다시 풀기)
3. **개념 정리** (해당 주차 키워드 노트 작성)

### 레벨업 기준
- 레벨3 문제를 60분 내 50% 이상 풀 수 있으면 → 다음 단계로
- 특정 유형 정답률 30% 미만 → 해당 유형 1주 추가 집중

---

## How to Use

각 디렉토리의 `.md` 파일로 개념을 학습하고, `07-solutions/`에 직접 풀이를 추가하세요.

```bash
# 풀이 파일 추가 예시
# 07-solutions/boj/1234_problem_name.cpp
# 07-solutions/programmers/level3_problem_name.cpp
# 07-solutions/leetcode/0001_two_sum.cpp
```
