# Chương 1: Nhập môn Lập trình Thi đấu

## 1.1. Đọc đề và trích xuất bài toán lõi

**Vấn đề:** Đề bài luôn được "bọc" trong một câu chuyện. Kỹ năng đầu tiên không phải là thuật toán, mà là khả năng lột bỏ lớp vỏ ngôn ngữ để nhìn ra bài toán toán học/thuật toán thuần túy bên trong.

### Ví dụ 1 (Dễ) — Lột vỏ trực tiếp
> *"Bờm có N viên kẹo, viên thứ i có độ ngọt a[i]. Bờm muốn biết tổng độ ngọt của tất cả các viên kẹo."*

**Bài toán lõi:** Tính tổng của một mảng N số nguyên. → `sum(a[0..N-1])`.

### Ví dụ 2 (Trung bình) — Cần suy luận thêm 1 bước
> *"Một cửa hàng có N sản phẩm, giá sản phẩm thứ i là a[i]. Khách hàng muốn mua 2 sản phẩm khác nhau sao cho tổng giá đúng bằng S đồng. Hỏi có tồn tại cặp sản phẩm như vậy không?"*

**Bài toán lõi:** Cho mảng N số, kiểm tra tồn tại cặp (i, j) với i ≠ j sao cho `a[i] + a[j] = S`. Đây là bài toán "Two Sum" kinh điển — dấu hiệu nhận biết: "cặp", "tổng bằng giá trị cho trước".

### Ví dụ 3 (Khó) — Câu chuyện dài, nhiều lớp che giấu
> *"Trong một thành phố có N giao lộ và M con đường hai chiều nối giữa các giao lộ, mỗi con đường có một thời gian di chuyển. Một người đưa thư xuất phát từ giao lộ 1, cần đi đến giao lộ N để giao thư, sau đó phải quay lại giao lộ 1. Vì thời gian gấp, người đưa thư muốn tổng thời gian của cả hành trình đi và về là nhỏ nhất. Biết rằng người đưa thư có thể đi qua một con đường nhiều lần nếu cần."*

**Bài toán lõi:** Đây thực chất là bài toán tìm đường đi ngắn nhất 2 chiều trên đồ thị có trọng số không âm — vì đường đi ngắn nhất từ 1→N và N→1 trên đồ thị vô hướng có cùng độ dài, bài toán quy về: tìm đường đi ngắn nhất từ đỉnh 1 đến đỉnh N, rồi nhân đôi kết quả. → Thuật toán Dijkstra (Chương 12).

### Bài tập minh hoạ (tương tự) — tự luyện
> *"Một lớp có N học sinh xếp hàng theo một thứ tự cho trước, chiều cao học sinh thứ i là h[i]. Thầy giáo muốn chọn ra một nhóm học sinh đứng liên tiếp nhau sao cho chiều cao của nhóm đó tăng dần liên tục, và nhóm được chọn phải dài nhất có thể."*

**Lời giải:**
- Bài toán lõi: tìm đoạn con liên tiếp dài nhất mà mảng tăng dần (không phải dãy con bất kỳ như LIS — ở đây bắt buộc **liên tiếp**).
- Cách giải: duyệt tuyến tính, giữ độ dài đoạn tăng hiện tại, reset khi gặp điểm gãy.

```cpp
int longestIncreasingRun(vector<int> &h) {
    int n = h.size();
    int best = 1, cur = 1;
    for (int i = 1; i < n; i++) {
        if (h[i] > h[i-1]) cur++;
        else cur = 1;
        best = max(best, cur);
    }
    return best;
}
```
```python
def longest_increasing_run(h):
    best = cur = 1
    for i in range(1, len(h)):
        if h[i] > h[i-1]:
            cur += 1
        else:
            cur = 1
        best = max(best, cur)
    return best
```

---

## 1.2. Ước lượng độ phức tạp thuật toán từ giới hạn dữ liệu

**Nguyên tắc:** Máy chấm xử lý khoảng **10^8 phép tính/giây**. Dựa vào N trong đề để suy ngược độ phức tạp tối đa được phép.

| Giới hạn N | Độ phức tạp chấp nhận được | Gợi ý thuật toán |
|---|---|---|
| N ≤ 10 | O(N!), O(2^N · N) | Sinh hoán vị, brute-force |
| N ≤ 20–22 | O(2^N) | Bitmask DP, duyệt tập con |
| N ≤ 100–500 | O(N³) | DP 3 chiều, Floyd-Warshall |
| N ≤ 2.000–5.000 | O(N²) | DP 2 chiều, brute-force cặp |
| N ≤ 10^6 | O(N log N) | Sort, Segment Tree |
| N ≤ 10^7–10^8 | O(N) | Duyệt tuyến tính, prefix sum |
| N ≤ 10^18 | O(log N) hoặc O(1) | Lũy thừa nhanh, công thức đóng |

### Ví dụ 1 (Dễ) — Đọc trực tiếp từ bảng
Đề cho N ≤ 5.000, yêu cầu đếm số cặp (i, j) thoả điều kiện nào đó.
→ N ≤ 5.000 → O(N²) chấp nhận được → duyệt 2 vòng lặp lồng nhau là đủ, không cần tối ưu thêm.

### Ví dụ 2 (Trung bình) — Cần phân biệt 2 giới hạn trong cùng 1 đề
Đề cho N ≤ 10^5 (số phần tử) nhưng Q ≤ 10^5 (số truy vấn), mỗi truy vấn hỏi tổng một đoạn.
→ Nếu tính tổng đoạn trực tiếp mỗi truy vấn: O(Q·N) = 10^10 → **TLE chắc chắn**.
→ Cần tiền xử lý O(N) (Prefix Sum, Chương 5) để mỗi truy vấn chỉ mất O(1) → tổng O(N + Q) = 2×10^5, an toàn.

### Ví dụ 3 (Khó) — Giới hạn "ẩn" đòi hỏi suy luận qua nhiều bước
Đề cho N ≤ 10^5 phần tử, K ≤ N, yêu cầu tìm tổng lớn nhất của đúng K đoạn con rời nhau, tổng độ dài không giới hạn.
→ Thoạt nhìn dễ nhầm là bài O(N²) (thử mọi cách chọn K đoạn), nhưng N=10^5 loại ngay khả năng O(N²) (10^10 phép tính).
→ Buộc phải nghĩ tới DP tuyến tính theo N với trạng thái phụ K: `dp[i][k]` = phương án tốt nhất xét i phần tử đầu, đã chọn k đoạn — độ phức tạp O(N·K), chỉ chấp nhận được nếu K cũng nhỏ (ví dụ K ≤ 500) — đây là kỹ năng "đọc đồng thời nhiều giới hạn" để suy ra đúng độ phức tạp cho phép.

### Bài tập minh hoạ (tương tự) — tự luyện
> *"Cho N ≤ 2×10^5 số nguyên. Có Q ≤ 2×10^5 truy vấn, mỗi truy vấn hỏi: phần tử nhỏ nhất lớn hơn hoặc bằng x là bao nhiêu? (x cho trước theo từng truy vấn)"*

**Lời giải:**
- N, Q ≤ 2×10^5 → phải đạt tổng độ phức tạp O((N+Q) log N).
- Brute-force mỗi truy vấn duyệt O(N) → tổng O(N·Q) = 4×10^10 → TLE.
- Giải pháp: sort mảng 1 lần O(N log N), mỗi truy vấn dùng tìm kiếm nhị phân O(log N) → tổng O((N+Q) log N) ≈ 2×10^5 × 18 ≈ 3.6×10^6, an toàn.

```cpp
sort(a.begin(), a.end());
// mỗi truy vấn x:
auto it = lower_bound(a.begin(), a.end(), x);
int result = (it != a.end()) ? *it : -1;
```
```python
import bisect
a.sort()
# mỗi truy vấn x:
pos = bisect.bisect_left(a, x)
result = a[pos] if pos < len(a) else -1
```

---

## 1.3. I/O nhanh (Fast Input/Output)

**Vấn đề:** Với N lớn (≥ 10^5), I/O mặc định có thể chậm tới mức làm thuật toán đúng vẫn bị TLE.

### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    ios_base::sync_with_stdio(false);
    cin.tie(NULL);

    int n;
    cin >> n;
    vector<int> a(n);
    for (int &x : a) cin >> x;

    for (int x : a) cout << x << " ";
    cout << "\n";
    return 0;
}
```

### Python
```python
import sys
input = sys.stdin.readline

n = int(input())
a = list(map(int, input().split()))
print(*a)
```

Với N cực lớn (≥ 10^6), đọc toàn bộ 1 lần bằng con trỏ:
```python
import sys
data = sys.stdin.read().split()
idx = 0
n = int(data[idx]); idx += 1
a = list(map(int, data[idx:idx+n])); idx += n
```

---

## 1.4. Kỹ năng debug và test biên

### Ví dụ 1 (Dễ) — Quên xử lý N = 0 hoặc N = 1
```cpp
// SAI: giả sử luôn có ít nhất 2 phần tử để so sánh
int findMax(vector<int> &a) {
    int mx = a[0];
    for (int i = 1; i < a.size(); i++) mx = max(mx, a[i]);
    return mx;
}
// Nếu a rỗng (a.size() == 0), a[0] gây lỗi truy cập ngoài vùng nhớ (undefined behavior)
```
**Sửa:** luôn kiểm tra `if (a.empty()) return ...;` trước khi truy cập phần tử đầu tiên.

### Ví dụ 2 (Trung bình) — Tràn số (overflow)
```cpp
int n = 100000;
int a[100000]; // mỗi phần tử tới 10^9
int sum = 0;
for (int i = 0; i < n; i++) sum += a[i];
// SAI: tổng có thể lên tới 10^5 * 10^9 = 10^14, vượt quá giới hạn int (~2.1*10^9)
```
**Sửa:** dùng `long long sum = 0;` khi tổng có khả năng vượt quá ~2×10^9.

### Ví dụ 3 (Khó) — Lỗi tinh vi khi dùng cấu trúc dữ liệu tham chiếu (Python)
```python
# SAI: tạo ma trận 2D, nhưng 3 dòng cùng tham chiếu 1 list
grid = [[0] * 3] * 3
grid[0][0] = 1
print(grid)  # [[1, 0, 0], [1, 0, 0], [1, 0, 0]] <- cả 3 dòng đều đổi!
```
**Sửa:**
```python
grid = [[0] * 3 for _ in range(3)]  # mỗi dòng là 1 list riêng biệt
```

### Kỹ thuật Stress Testing
```python
import random

def brute_force(arr):
    return sorted(arr)

def optimized(arr):
    return sorted(arr)  # thay bằng thuật toán cần kiểm tra

for trial in range(1000):
    n = random.randint(1, 10)
    arr = [random.randint(1, 100) for _ in range(n)]
    if brute_force(arr[:]) != optimized(arr[:]):
        print("MISMATCH! Test case:", arr)
        break
else:
    print("Tất cả 1000 test đều khớp!")
```

---

## Tổng kết Chương 1

- Kỹ năng nền tảng nhất: **đọc đề đúng** (trích xuất bài toán lõi) và **ước lượng độ phức tạp** trước khi code.
- Luôn đọc giới hạn dữ liệu đầu tiên — đó là kim chỉ nam chọn thuật toán.
- I/O nhanh là điều kiện cần chứ không đủ — sai độ phức tạp thì I/O nhanh không cứu được.
- Luôn tự test edge case (N=0, N=1, số âm, tràn số, tham chiếu list trong Python) trước khi nộp bài.

---

## Bài tập cuối chương — Chương 1 (12 bài, có lời giải)

---

### Bài 1 — Watermelon (Codeforces 4A) — Dễ

**Đề bài (diễn giải):** Hai bạn nhỏ mua một quả dưa hấu nặng w kg. Họ muốn chia quả dưa thành 2 phần, mỗi phần có khối lượng là số nguyên **chẵn** (không cần bằng nhau), và mỗi phần phải có khối lượng dương (> 0). Hỏi có thể chia được như vậy không?

**Input:** một số nguyên w (1 ≤ w ≤ 100).
**Output:** in `YES` nếu chia được, `NO` nếu không.

**Phân tích:** Tổng 2 số chẵn luôn là số chẵn → nếu w lẻ, chắc chắn "NO". Nếu w chẵn, chỉ cần tách w = 2 + (w-2); (w-2) cũng là số chẵn nếu w chẵn. Nhưng cần cả 2 phần dương → w = 2 không chia được (vì 2 = 1+1, mà 1 là số lẻ; hoặc 2 = 0+2 nhưng 0 không dương) → w phải chẵn **và** w > 2.

**Lời giải:**
```cpp
#include <iostream>
using namespace std;
int main() {
    int w; cin >> w;
    cout << ((w % 2 == 0 && w > 2) ? "YES" : "NO") << "\n";
}
```
```python
w = int(input())
print("YES" if w % 2 == 0 and w > 2 else "NO")
```

---

### Bài 2 — Weird Algorithm (CSES 1068) — Dễ

**Đề bài (diễn giải):** Cho số nguyên n. Áp dụng lặp lại quy tắc: nếu n chẵn thì n = n/2; nếu n lẻ thì n = 3n+1. Lặp cho đến khi n = 1. In ra toàn bộ dãy số n trải qua, kể cả giá trị đầu và giá trị 1 ở cuối (đây là bài toán liên quan đến giả thuyết Collatz nổi tiếng trong toán học).

**Input:** một số nguyên n (1 ≤ n ≤ 10^6).
**Output:** dãy các giá trị n trải qua, cách nhau bởi khoảng trắng.

**Phân tích:** Mô phỏng trực tiếp theo đúng quy tắc đề cho, không cần tối ưu gì thêm — bài rèn kỹ năng vòng lặp + I/O cơ bản.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    long long n; cin >> n;
    cout << n;
    while (n != 1) {
        if (n % 2 == 0) n /= 2;
        else n = 3 * n + 1;
        cout << " " << n;
    }
    cout << "\n";
}
```
```python
n = int(input())
print(n, end="")
while n != 1:
    n = n // 2 if n % 2 == 0 else 3 * n + 1
    print("", n, end="")
print()
```

---

### Bài 3 — Two Sum (LeetCode 1) — Dễ

**Đề bài (diễn giải):** Cho mảng số nguyên `nums` và một số nguyên `target`. Tìm chỉ số của 2 phần tử có tổng đúng bằng `target`. Đề đảm bảo luôn có đúng 1 cặp thoả mãn, và không được dùng lại cùng 1 phần tử 2 lần.

**Input:** mảng `nums`, số nguyên `target`.
**Output:** cặp chỉ số (i, j) thoả `nums[i] + nums[j] = target`.

**Phân tích:** Brute-force O(n²) (thử mọi cặp) chấp nhận được nếu n nhỏ, nhưng cách tối ưu O(n): dùng hashmap lưu (giá trị → chỉ số) đã duyệt qua, với mỗi phần tử kiểm tra xem `target - nums[i]` đã xuất hiện chưa.

**Lời giải:**
```cpp
vector<int> twoSum(vector<int> &nums, int target) {
    unordered_map<int,int> seen; // giá trị -> chỉ số
    for (int i = 0; i < (int)nums.size(); i++) {
        int need = target - nums[i];
        if (seen.count(need)) return {seen[need], i};
        seen[nums[i]] = i;
    }
    return {-1, -1};
}
```
```python
def two_sum(nums, target):
    seen = {}
    for i, x in enumerate(nums):
        need = target - x
        if need in seen:
            return [seen[need], i]
        seen[x] = i
    return [-1, -1]
```

---

### Bài 4 — Theatre Square (Codeforces 1A) — Dễ-Trung bình

**Đề bài (diễn giải):** Một quảng trường hình chữ nhật kích thước n × m mét cần được lát kín bằng các viên gạch vuông kích thước a × a mét. Các viên gạch phải đặt song song với cạnh quảng trường, không được cắt vỡ gạch (có thể lát dư ra ngoài rìa quảng trường nếu cần), các viên gạch không được chồng lên nhau. Tìm số viên gạch tối thiểu cần dùng để lát kín toàn bộ quảng trường.

**Input:** 3 số nguyên n, m, a (1 ≤ n, m, a ≤ 10^9).
**Output:** số viên gạch tối thiểu.

**Phân tích:** Số viên gạch cần theo mỗi chiều là `ceil(n/a)` và `ceil(m/a)` — nhân lại với nhau. Vì n, m, a có thể lên tới 10^9, kết quả có thể vượt quá giới hạn `int` → phải dùng `long long`.

**Lời giải:**
```cpp
#include <iostream>
using namespace std;
int main() {
    long long n, m, a;
    cin >> n >> m >> a;
    long long tiles_n = (n + a - 1) / a; // ceil(n/a)
    long long tiles_m = (m + a - 1) / a;
    cout << tiles_n * tiles_m << "\n";
}
```
```python
n, m, a = map(int, input().split())
tiles_n = (n + a - 1) // a
tiles_m = (m + a - 1) // a
print(tiles_n * tiles_m)
```

---

### Bài 5 — Missing Number (CSES 1083) — Dễ-Trung bình

**Đề bài (diễn giải):** Cho các số nguyên từ 1 đến n, nhưng thiếu đúng 1 số. Cho một mảng chứa n-1 số còn lại (không theo thứ tự cụ thể). Tìm số bị thiếu.

**Input:** số nguyên n (2 ≤ n ≤ 2×10^5), sau đó n-1 số nguyên phân biệt trong khoảng [1, n].
**Output:** số bị thiếu.

**Phân tích:** Dùng công thức tổng 1..n = n(n+1)/2, trừ đi tổng các số đã cho → ra số bị thiếu. Cần `long long` vì n(n+1)/2 có thể lớn (n ≤ 2×10^5 → tổng ≈ 2×10^10).

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int n; cin >> n;
    long long expected = (long long)n * (n + 1) / 2;
    long long actual = 0, x;
    for (int i = 0; i < n - 1; i++) { cin >> x; actual += x; }
    cout << expected - actual << "\n";
}
```
```python
n = int(input())
expected = n * (n + 1) // 2
actual = sum(map(int, input().split()))
print(expected - actual)
```

---

### Bài 6 — Increasing Array (CSES 1094) — Trung bình

**Đề bài (diễn giải):** Cho mảng n số nguyên. Mỗi lần thao tác, có thể tăng 1 phần tử bất kỳ thêm 1 đơn vị. Tìm số thao tác tối thiểu để mảng trở thành không giảm (mỗi phần tử ≥ phần tử đứng trước).

**Input:** n, sau đó n số nguyên.
**Output:** số thao tác tối thiểu.

**Phân tích:** Duyệt từ trái sang phải, nếu phần tử hiện tại nhỏ hơn phần tử trước, cần tăng nó lên bằng phần tử trước — cộng dồn số lần tăng cần thiết, đồng thời cập nhật giá trị phần tử đó thành giá trị đã tăng để so sánh cho bước sau.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int n; cin >> n;
    vector<long long> a(n);
    for (auto &x : a) cin >> x;
    long long ops = 0;
    for (int i = 1; i < n; i++) {
        if (a[i] < a[i-1]) {
            ops += a[i-1] - a[i];
            a[i] = a[i-1];
        }
    }
    cout << ops << "\n";
}
```
```python
n = int(input())
a = list(map(int, input().split()))
ops = 0
for i in range(1, n):
    if a[i] < a[i-1]:
        ops += a[i-1] - a[i]
        a[i] = a[i-1]
print(ops)
```

---

### Bài 7 — Contains Duplicate (LeetCode 217) — Trung bình

**Đề bài (diễn giải):** Cho mảng số nguyên, kiểm tra xem có giá trị nào xuất hiện ít nhất 2 lần hay không.

**Input:** mảng `nums`.
**Output:** `true`/`false`.

**Phân tích:** Dùng `set`/hashset để kiểm tra trùng lặp trong 1 lượt duyệt O(n), thay vì so sánh từng cặp O(n²).

**Lời giải:**
```cpp
bool containsDuplicate(vector<int> &nums) {
    unordered_set<int> seen;
    for (int x : nums) {
        if (seen.count(x)) return true;
        seen.insert(x);
    }
    return false;
}
```
```python
def contains_duplicate(nums):
    seen = set()
    for x in nums:
        if x in seen:
            return True
        seen.add(x)
    return False
```

---

### Bài 8 — Đoạn con tăng liên tiếp dài nhất (dạng đề HSG phổ biến) — Trung bình

**Đề bài (diễn giải):** Cho dãy n số nguyên là chiều cao của n học sinh xếp hàng theo thứ tự cho trước. Tìm độ dài đoạn con **liên tiếp** dài nhất mà chiều cao tăng dần nghiêm ngặt.

**Input:** n, sau đó n số nguyên.
**Output:** độ dài đoạn con tăng liên tiếp dài nhất.

**Phân tích:** Khác với LIS (Longest Increasing Subsequence — không cần liên tiếp, học ở Chương 8), bài này chỉ cần duyệt tuyến tính 1 lượt, giữ độ dài đoạn tăng hiện tại và reset khi gặp điểm gãy.

**Lời giải:**
```cpp
int longestIncreasingRun(vector<int> &h) {
    int n = h.size();
    int best = 1, cur = 1;
    for (int i = 1; i < n; i++) {
        cur = (h[i] > h[i-1]) ? cur + 1 : 1;
        best = max(best, cur);
    }
    return best;
}
```
```python
def longest_increasing_run(h):
    best = cur = 1
    for i in range(1, len(h)):
        cur = cur + 1 if h[i] > h[i-1] else 1
        best = max(best, cur)
    return best
```

---

### Bài 9 — Distinct Numbers (CSES 1616) — Trung bình

**Đề bài (diễn giải):** Cho mảng n số nguyên, đếm số lượng giá trị **phân biệt** (không tính trùng lặp) xuất hiện trong mảng.

**Input:** n, sau đó n số nguyên (giá trị có thể lên tới 10^9).
**Output:** số lượng giá trị phân biệt.

**Phân tích:** Bài rèn phản xạ chọn đúng cấu trúc dữ liệu: dùng `set`/`sort + unique` thay vì đếm bằng mảng đánh dấu (vì giá trị lên tới 10^9, không thể cấp phát mảng kích thước 10^9).

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int n; cin >> n;
    vector<int> a(n);
    for (auto &x : a) cin >> x;
    sort(a.begin(), a.end());
    int cnt = unique(a.begin(), a.end()) - a.begin();
    cout << cnt << "\n";
}
```
```python
n = int(input())
a = list(map(int, input().split()))
print(len(set(a)))
```

---

### Bài 10 — Restaurant Customers (CSES 1619) — Trung bình-Khó

**Đề bài (diễn giải):** Một nhà hàng có n khách hàng trong ngày, khách thứ i đến vào thời điểm `arrival[i]` và rời đi vào thời điểm `leave[i]`. Tìm số lượng khách hàng tối đa có mặt trong nhà hàng **cùng một lúc** tại bất kỳ thời điểm nào trong ngày.

**Input:** n, sau đó n dòng, mỗi dòng gồm `arrival[i]` và `leave[i]`.
**Output:** số khách tối đa cùng lúc.

**Phân tích:** Đây là bài toán "quét dòng" (sweep line) đơn giản: tạo danh sách sự kiện (+1 tại thời điểm đến, -1 tại thời điểm rời đi), sắp xếp theo thời gian (ưu tiên xử lý "rời đi" trước "đến" nếu trùng thời điểm, để tránh đếm dư khi khách rời đi và khách khác đến cùng lúc), rồi quét qua và cộng dồn.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int n; cin >> n;
    vector<pair<int,int>> events; // (thời điểm, loại: -1 rời đi, +1 đến)
    for (int i = 0; i < n; i++) {
        int a, b; cin >> a >> b;
        events.push_back({a, 1});
        events.push_back({b, -1});
    }
    sort(events.begin(), events.end()); // (b, -1) < (b, 1) tự nhiên vì -1 < 1
    int cur = 0, best = 0;
    for (auto &[t, type] : events) {
        cur += type;
        best = max(best, cur);
    }
    cout << best << "\n";
}
```
```python
n = int(input())
events = []
for _ in range(n):
    a, b = map(int, input().split())
    events.append((a, 1))
    events.append((b, -1))
events.sort()  # (b, -1) đứng trước (b, 1) tự nhiên
cur = best = 0
for t, typ in events:
    cur += typ
    best = max(best, cur)
print(best)
```

---

### Bài 11 — Sum of Two Values (CSES 1640) — Trung bình

**Đề bài (diễn giải):** Cho mảng n số nguyên và số x. Tìm 2 chỉ số (i, j), i ≠ j, sao cho `a[i] + a[j] = x`. In ra 1 cặp chỉ số hợp lệ bất kỳ (theo 1-indexed), hoặc "IMPOSSIBLE" nếu không tồn tại.

**Input:** n, x, sau đó n số nguyên.
**Output:** cặp chỉ số hoặc "IMPOSSIBLE".

**Phân tích:** Giống Bài 3 (Two Sum) nhưng yêu cầu output theo 1-indexed và có case không tồn tại — rèn kỹ năng đọc kỹ yêu cầu định dạng output, dễ sai sót dù thuật toán đúng.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int n; long long x; cin >> n >> x;
    vector<long long> a(n);
    for (auto &v : a) cin >> v;

    unordered_map<long long,int> seen; // giá trị -> chỉ số (1-indexed)
    for (int i = 0; i < n; i++) {
        long long need = x - a[i];
        if (seen.count(need)) {
            cout << seen[need] << " " << i + 1 << "\n";
            return 0;
        }
        seen[a[i]] = i + 1;
    }
    cout << "IMPOSSIBLE\n";
}
```
```python
n, x = map(int, input().split())
a = list(map(int, input().split()))
seen = {}
result = None
for i, v in enumerate(a):
    need = x - v
    if need in seen:
        result = (seen[need], i + 1)
        break
    seen[v] = i + 1
print(f"{result[0]} {result[1]}" if result else "IMPOSSIBLE")
```

---

### Bài 12 — Bài tổng hợp: nhiều giới hạn cùng lúc (minh hoạ, không trích từ đề cụ thể) — Khó

**Đề bài (tự thiết kế minh hoạ):** Cho mảng n số nguyên (n ≤ 2×10^5) và q truy vấn (q ≤ 2×10^5). Mỗi truy vấn cho 1 số x, yêu cầu trả lời: phần tử nhỏ nhất trong mảng có giá trị **lớn hơn hoặc bằng x** là bao nhiêu?

**Input:** n, mảng a; q; q dòng tiếp theo mỗi dòng 1 số x.
**Output:** q dòng kết quả tương ứng.

**Phân tích:** Đề có 2 giới hạn (n và q) đều ≤ 2×10^5 — cần đọc **đồng thời cả 2** để suy ra độ phức tạp cho phép: brute-force mỗi truy vấn O(n) → tổng O(n·q) = 4×10^10 → chắc chắn TLE. Giải pháp: sort mảng 1 lần O(n log n), mỗi truy vấn dùng tìm kiếm nhị phân O(log n) → tổng O((n+q) log n), an toàn.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;
int main() {
    int n; cin >> n;
    vector<long long> a(n);
    for (auto &v : a) cin >> v;
    sort(a.begin(), a.end());

    int q; cin >> q;
    while (q--) {
        long long x; cin >> x;
        auto it = lower_bound(a.begin(), a.end(), x);
        cout << (it != a.end() ? *it : -1) << "\n";
    }
}
```
```python
import bisect, sys
input = sys.stdin.readline

n = int(input())
a = sorted(map(int, input().split()))
q = int(input())
out = []
for _ in range(q):
    x = int(input())
    pos = bisect.bisect_left(a, x)
    out.append(str(a[pos]) if pos < len(a) else "-1")
print("\n".join(out))
```
