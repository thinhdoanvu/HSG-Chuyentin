# Chương 3: Cấu trúc dữ liệu cơ bản (STL/Built-in)

> Chương này đi sâu vào từng cấu trúc dữ liệu: cách khởi tạo, các thao tác thêm/xoá/truy cập, độ phức tạp từng thao tác, và quan trọng nhất — **khi nào nên dùng cấu trúc nào**. Đây là chương "tra cứu" sẽ dùng lại xuyên suốt toàn bộ khoá học.

---

## 3.1. Vector (Mảng động)

### Khởi tạo
```cpp
vector<int> a;                    // rỗng
vector<int> b(5);                 // 5 phần tử, giá trị mặc định 0
vector<int> c(5, 100);            // 5 phần tử, giá trị 100
vector<int> d = {1, 2, 3, 4, 5};  // khởi tạo trực tiếp bằng danh sách
vector<vector<int>> grid(3, vector<int>(4, 0)); // ma trận 3x4, toàn số 0
```
```python
a = []                       # rỗng
b = [0] * 5                  # 5 phần tử giá trị 0
c = [100] * 5                # 5 phần tử giá trị 100
d = [1, 2, 3, 4, 5]          # khởi tạo trực tiếp
grid = [[0]*4 for _ in range(3)]  # ma trận 3x4 (PHẢI dùng list comprehension, không dùng [[0]*4]*3)
```

### Thêm phần tử
```cpp
a.push_back(10);             // thêm vào CUỐI - O(1) khấu hao
a.insert(a.begin(), 5);      // thêm vào ĐẦU - O(n), phải dịch chuyển toàn bộ phần tử
a.insert(a.begin() + 2, 7);  // thêm vào giữa (vị trí 2) - O(n)
```
```python
a.append(10)          # thêm vào cuối - O(1) khấu hao
a.insert(0, 5)         # thêm vào đầu - O(n)
a.insert(2, 7)         # thêm vào giữa - O(n)
```

### Xoá phần tử
```cpp
a.pop_back();                       // xoá phần tử CUỐI - O(1)
a.erase(a.begin());                 // xoá phần tử ĐẦU - O(n)
a.erase(a.begin() + 2);             // xoá phần tử ở vị trí 2 - O(n)
a.erase(a.begin(), a.begin() + 3);  // xoá 1 đoạn [0, 3) - O(n)
```
```python
a.pop()        # xoá cuối - O(1)
a.pop(0)       # xoá đầu - O(n)
del a[2]       # xoá vị trí 2 - O(n)
del a[0:3]     # xoá đoạn [0,3) - O(n)
```

### Truy cập & duyệt
```cpp
int x = a[2];        // truy cập O(1), KHÔNG kiểm tra biên (lỗi âm thầm nếu out-of-range)
int y = a.at(2);     // truy cập O(1), CÓ kiểm tra biên (ném exception nếu sai) - dùng khi debug
for (int v : a) cout << v << " ";          // duyệt chỉ đọc
for (int &v : a) v *= 2;                    // duyệt có sửa giá trị (chú ý dấu &)
```
```python
x = a[2]              # O(1), Python tự kiểm tra biên, ném IndexError nếu sai
for v in a: print(v)  # duyệt chỉ đọc
a = [v * 2 for v in a] # "duyệt sửa" kiểu Python: tạo list mới
```

### Khi nào nên dùng Vector?
- Mặc định cho **hầu hết** trường hợp cần lưu dãy phần tử có thứ tự.
- Cần truy cập ngẫu nhiên theo chỉ số (random access) — Vector là lựa chọn nhanh nhất O(1).
- **Không nên dùng** khi cần thêm/xoá thường xuyên ở **đầu** hoặc **giữa** mảng với n lớn — khi đó nên chuyển sang `deque` (nếu chỉ thao tác 2 đầu) hoặc cấu trúc khác.

### Bài tập minh hoạ
Cho vector n số nguyên, thực hiện q truy vấn: mỗi truy vấn hoặc "thêm x vào cuối", hoặc "xoá phần tử cuối", hoặc "in phần tử ở vị trí k". Đảm bảo mọi thao tác đều O(1).

**Lời giải:**
```cpp
vector<int> a;
int q; cin >> q;
while (q--) {
    string type; cin >> type;
    if (type == "ADD") { int x; cin >> x; a.push_back(x); }
    else if (type == "REMOVE") a.pop_back();
    else { int k; cin >> k; cout << a[k] << "\n"; }
}
```

---

## 3.2. Stack (Ngăn xếp)

### Khởi tạo
```cpp
stack<int> st;                     // rỗng
stack<int> st2(deque<int>{1,2,3}); // khởi tạo từ container khác (ít dùng trong thi đấu)
```
```python
st = []   # Python không có class Stack riêng, dùng list là đủ (append/pop ở cuối là O(1))
```

### Thêm / Xoá / Truy cập
```cpp
st.push(10);          // thêm lên đỉnh - O(1)
st.pop();              // xoá phần tử đỉnh - O(1), KHÔNG trả về giá trị bị xoá
int top = st.top();    // xem giá trị đỉnh (không xoá) - O(1)
bool empty = st.empty(); // kiểm tra rỗng
int sz = st.size();
```
```python
st.append(10)   # push - O(1)
val = st.pop()   # pop - O(1), Python TRẢ VỀ giá trị bị xoá (khác C++)
top = st[-1]     # xem đỉnh (không xoá)
empty = len(st) == 0
```

> **Lưu ý quan trọng:** luôn kiểm tra `!st.empty()` trước khi gọi `top()`/`pop()` — gọi trên stack rỗng là lỗi runtime nghiêm trọng (undefined behavior trong C++, exception trong Python).

### Khi nào nên dùng Stack?
- Xử lý theo nguyên tắc "vào sau ra trước" (LIFO): kiểm tra ngoặc hợp lệ, undo/redo, duyệt DFS không đệ quy.
- Nền tảng cho kỹ thuật Monotonic Stack (Chương 6.4).
- Chuyển đổi biểu thức (infix ↔ postfix), tính giá trị biểu thức hậu tố.

### Bài tập minh hoạ
Tính giá trị của biểu thức hậu tố (Reverse Polish Notation), ví dụ: `"3 4 + 2 *"` → 14.

**Lời giải:**
```cpp
int evalRPN(vector<string> &tokens) {
    stack<int> st;
    for (auto &tok : tokens) {
        if (tok == "+" || tok == "-" || tok == "*" || tok == "/") {
            int b = st.top(); st.pop();
            int a = st.top(); st.pop();
            if (tok == "+") st.push(a + b);
            else if (tok == "-") st.push(a - b);
            else if (tok == "*") st.push(a * b);
            else st.push(a / b);
        } else {
            st.push(stoi(tok));
        }
    }
    return st.top();
}
```
```python
def eval_rpn(tokens):
    st = []
    for tok in tokens:
        if tok in "+-*/":
            b, a = st.pop(), st.pop()
            if tok == "+": st.append(a + b)
            elif tok == "-": st.append(a - b)
            elif tok == "*": st.append(a * b)
            else: st.append(int(a / b))  # chia lấy phần nguyên về phía 0
        else:
            st.append(int(tok))
    return st[-1]
```

---

## 3.3. Queue (Hàng đợi)

### Khởi tạo
```cpp
queue<int> q; // rỗng
```
```python
from collections import deque
q = deque()   # Python KHÔNG dùng list làm queue vì list.pop(0) là O(n) - PHẢI dùng deque
```

### Thêm / Xoá / Truy cập
```cpp
q.push(10);           // thêm vào CUỐI hàng đợi - O(1)
q.pop();               // xoá phần tử ĐẦU hàng đợi - O(1), không trả về giá trị
int front = q.front(); // xem phần tử đầu (không xoá)
int back = q.back();   // xem phần tử cuối (không xoá)
```
```python
q.append(10)      # thêm vào cuối - O(1)
val = q.popleft()  # xoá đầu, TRẢ VỀ giá trị - O(1)
front = q[0]
back = q[-1]
```

> **Lỗi thường gặp:** dùng `list.pop(0)` trong Python để mô phỏng queue — thao tác này là **O(n)** (phải dịch chuyển toàn bộ phần tử còn lại), khiến thuật toán tưởng O(n) thực chất chạy O(n²). Luôn dùng `collections.deque`.

### Khi nào nên dùng Queue?
- Xử lý theo nguyên tắc "vào trước ra trước" (FIFO): **bắt buộc** cho BFS (duyệt theo từng lớp/mức).
- Mô phỏng hàng đợi thực tế: xử lý tác vụ theo thứ tự đến trước.

### Bài tập minh hoạ
Đếm số bước ngắn nhất để di chuyển quân mã (knight) từ ô (0,0) đến ô (x,y) trên bàn cờ vô hạn.

**Lời giải:**
```cpp
int knightMoves(int x, int y) {
    int dx[] = {1,1,-1,-1,2,2,-2,-2}, dy[] = {2,-2,2,-2,1,-1,1,-1};
    map<pair<int,int>,int> dist;
    queue<pair<int,int>> q;
    dist[{0,0}] = 0; q.push({0,0});
    while (!q.empty()) {
        auto [cx, cy] = q.front(); q.pop();
        if (cx == x && cy == y) return dist[{cx,cy}];
        for (int d = 0; d < 8; d++) {
            pair<int,int> nxt = {cx+dx[d], cy+dy[d]};
            if (!dist.count(nxt)) { dist[nxt] = dist[{cx,cy}] + 1; q.push(nxt); }
        }
    }
    return -1;
}
```

---

## 3.4. Deque (Hàng đợi 2 đầu)

### Khởi tạo
```cpp
deque<int> dq; // rỗng
deque<int> dq2 = {1, 2, 3};
```
```python
from collections import deque
dq = deque()
dq2 = deque([1, 2, 3])
```

### Thêm / Xoá / Truy cập (cả 2 đầu, đều O(1))
```cpp
dq.push_back(10);   dq.push_front(20);   // thêm cuối / thêm đầu
dq.pop_back();       dq.pop_front();      // xoá cuối / xoá đầu
int f = dq.front();  int b = dq.back();
int mid = dq[2];     // deque CÓ hỗ trợ truy cập ngẫu nhiên O(1), khác với list liên kết
```
```python
dq.append(10);       dq.appendleft(20)
dq.pop();             dq.popleft()
f = dq[0];            b = dq[-1]
mid = dq[2]           # O(1) do deque của Python cài đặt bằng mảng theo khối (block array)
```

### Khi nào nên dùng Deque?
- Cần thao tác thêm/xoá ở **cả 2 đầu** hiệu quả O(1) — Vector không làm được điều này ở đầu (O(n)).
- Nền tảng cho Sliding Window, Monotonic Queue (Chương 6.5).
- Có thể dùng thay Stack hoặc Queue nếu không chắc sẽ cần thao tác đầu nào trong tương lai.

### Bài tập minh hoạ
Cho dãy thao tác thêm số vào đầu hoặc cuối 1 dãy, sau mỗi thao tác in ra tổng của phần tử đầu và phần tử cuối hiện tại.

**Lời giải:**
```cpp
deque<int> dq;
int q; cin >> q;
while (q--) {
    string type; int x; cin >> type >> x;
    if (type == "FRONT") dq.push_front(x);
    else dq.push_back(x);
    cout << dq.front() + dq.back() << "\n";
}
```

---

## 3.5. Set (Tập hợp có thứ tự — cây đỏ-đen)

### Khởi tạo
```cpp
set<int> s;                        // rỗng, mặc định tăng dần
set<int, greater<int>> s2;         // giảm dần
set<int> s3 = {5, 1, 8, 3};        // {1, 3, 5, 8} - tự động sắp xếp, tự loại trùng
```
```python
# Python không có sẵn set có thứ tự tự động duy trì kiểu cây - dùng SortedList
from sortedcontainers import SortedList
s = SortedList()
s2 = SortedList([5, 1, 8, 3])  # [1, 3, 5, 8]
```

### Thêm / Xoá / Tìm kiếm
```cpp
s.insert(10);              // O(log n) - nếu đã tồn tại thì không thêm trùng
s.erase(10);                // O(log n) - xoá theo GIÁ TRỊ
s.erase(s.find(10));        // O(log n) - xoá theo ITERATOR (an toàn hơn nếu đã có iterator sẵn)
bool exist = s.count(10);   // O(log n) - trả về 0 hoặc 1 (set không có phần tử trùng)

auto it = s.lower_bound(5); // O(log n) - iterator tới phần tử ĐẦU TIÊN >= 5
auto it2 = s.upper_bound(5);// O(log n) - iterator tới phần tử ĐẦU TIÊN > 5
```
```python
s.add(10)                # O(log n)
s.discard(10)              # O(log n) - xoá, KHÔNG lỗi nếu không tồn tại
s.remove(10)                # O(log n) - xoá, LỖI nếu không tồn tại (khác discard)
exist = 10 in s              # O(log n)

pos = s.bisect_left(5)      # vị trí phần tử đầu tiên >= 5
pos2 = s.bisect_right(5)    # vị trí phần tử đầu tiên > 5
```

### Khi nào nên dùng Set (có thứ tự)?
- Cần duy trì tập hợp **luôn ở trạng thái đã sắp xếp** trong khi liên tục thêm/xoá.
- Cần truy vấn "cận trên/cận dưới" (`lower_bound`/`upper_bound`) — Unordered Set **không** hỗ trợ việc này.
- Cần duyệt phần tử theo thứ tự tăng/giảm dần.
- **Không nên dùng** nếu chỉ cần kiểm tra tồn tại/không cần thứ tự — dùng Unordered Set (mục 3.7) sẽ nhanh hơn.

### Bài tập minh hoạ
Xử lý q truy vấn: thêm số x, xoá số x, hoặc hỏi "số nhỏ nhất trong tập hiện tại lớn hơn x là bao nhiêu?".

**Lời giải:**
```cpp
set<int> s;
int q; cin >> q;
while (q--) {
    string type; cin >> type;
    if (type == "ADD") { int x; cin >> x; s.insert(x); }
    else if (type == "REMOVE") { int x; cin >> x; s.erase(x); }
    else {
        int x; cin >> x;
        auto it = s.upper_bound(x);
        cout << (it != s.end() ? *it : -1) << "\n";
    }
}
```

---

## 3.6. Map (Bảng ánh xạ có thứ tự — cây đỏ-đen)

### Khởi tạo
```cpp
map<string,int> mp;                    // rỗng, key tự động sắp xếp tăng dần
map<string,int> mp2 = {{"An", 90}, {"Binh", 85}};
```
```python
# Python dict giữ thứ tự CHÈN (từ Python 3.7+), KHÔNG tự sắp xếp theo key
# Nếu cần key tự sắp xếp -> dùng sortedcontainers.SortedDict
from sortedcontainers import SortedDict
mp = SortedDict()
mp2 = SortedDict({"An": 90, "Binh": 85})
```

### Thêm / Xoá / Truy cập
```cpp
mp["Chi"] = 95;              // thêm mới hoặc CẬP NHẬT nếu key đã tồn tại - O(log n)
mp["Diem"]++;                // TỰ TẠO key "Diem" với giá trị mặc định 0 rồi +1 -> cẩn thận side-effect này!
mp.erase("An");                // xoá theo key - O(log n)

if (mp.count("An")) { ... }    // kiểm tra tồn tại KHÔNG tự tạo key - O(log n)
if (mp.find("An") != mp.end()) { ... } // cách kiểm tra khác, tương đương count()

for (auto &[key, val] : mp) cout << key << ": " << val << "\n"; // duyệt theo thứ tự key tăng dần
```
```python
mp["Chi"] = 95
mp["Diem"] = mp.get("Diem", 0) + 1   # cách AN TOÀN, tránh KeyError khi key chưa tồn tại
del mp["An"]                          # xoá theo key, LỖI nếu không tồn tại

exist = "An" in mp                    # kiểm tra tồn tại, KHÔNG tự tạo key
for key, val in mp.items(): print(key, val)
```

> **Cạm bẫy kinh điển C++:** `mp["key"]` sẽ **tự động tạo** key đó với giá trị mặc định (0 cho `int`) nếu chưa tồn tại — ngay cả khi bạn chỉ định **đọc** giá trị (`if (mp["An"] > 0)`), thao tác này đã âm thầm thêm 1 phần tử mới vào map. Dùng `count()` hoặc `find()` nếu chỉ muốn kiểm tra mà không muốn tạo key mới.

### Khi nào nên dùng Map (có thứ tự)?
- Tương tự Set nhưng cần lưu **giá trị đi kèm** mỗi key (đếm tần suất, ánh xạ tên → điểm số).
- Cần duyệt theo thứ tự key hoặc tìm cận trên/dưới theo key.
- **Không nên dùng** nếu không cần thứ tự — Unordered Map (mục 3.8) nhanh hơn cho tra cứu thuần tuý.

### Bài tập minh hoạ
Đếm tần suất xuất hiện của từng từ trong văn bản, in kết quả theo thứ tự **bảng chữ cái**.

**Lời giải:**
```cpp
map<string,int> freq;
string word;
while (cin >> word) freq[word]++;
for (auto &[w, cnt] : freq) cout << w << ": " << cnt << "\n"; // tự động theo alphabet
```

---

## 3.7. Unordered Set (Bảng băm — hash table)

### Khởi tạo
```cpp
unordered_set<int> us;
unordered_set<int> us2 = {5, 1, 8, 3}; // thứ tự lưu trữ KHÔNG xác định, không sắp xếp
```
```python
s = set()
s2 = {5, 1, 8, 3}
```

### Thêm / Xoá / Tìm kiếm
```cpp
us.insert(10);      // O(1) trung bình
us.erase(10);        // O(1) trung bình
bool exist = us.count(10); // O(1) trung bình
```
```python
s.add(10)
s.discard(10)   # không lỗi nếu chưa có
s.remove(10)     # lỗi nếu chưa có
exist = 10 in s
```

### Khi nào nên dùng Unordered Set?
- Chỉ cần kiểm tra tồn tại/loại trùng, **không quan tâm thứ tự** — nhanh hơn `set` (O(1) so với O(log n)).
- **Cảnh báo quan trọng:** trên Codeforces, tồn tại "anti-hash test" cố tình tạo dữ liệu làm `unordered_set<int>` bị hash collision hàng loạt, khiến độ phức tạp suy biến O(n) mỗi thao tác. Cách phòng tránh: dùng custom hash function, hoặc an toàn nhất là dùng `set` nếu n không quá lớn.

### Bài tập minh hoạ
Cho 2 mảng, tìm các phần tử xuất hiện trong mảng A nhưng **không** xuất hiện trong mảng B.

**Lời giải:**
```cpp
vector<int> difference(vector<int> &A, vector<int> &B) {
    unordered_set<int> setB(B.begin(), B.end());
    vector<int> result;
    for (int x : A) if (!setB.count(x)) result.push_back(x);
    return result;
}
```

---

## 3.8. Unordered Map (Bảng băm — hash table)

### Khởi tạo
```cpp
unordered_map<string,int> ump;
```
```python
mp = {}   # dict của Python vốn là hash table
from collections import defaultdict
mp2 = defaultdict(int)  # tự động giá trị mặc định 0 cho key chưa tồn tại - tránh KeyError
```

### Thêm / Xoá / Truy cập
```cpp
ump["An"] = 90;             // O(1) trung bình
ump.erase("An");             // O(1) trung bình
bool exist = ump.count("An"); // O(1) trung bình, không tự tạo key
```
```python
mp["An"] = 90
del mp["An"]
exist = "An" in mp
mp2["Chi"] += 1   # với defaultdict, không cần kiểm tra tồn tại trước
```

### Khi nào nên dùng Unordered Map?
- Đếm tần suất, tra cứu nhanh khi **không** cần duyệt theo thứ tự key — trường hợp dùng phổ biến nhất trong lập trình thi đấu.
- Tương tự Unordered Set, cẩn trọng "anti-hash test" nếu key là số nguyên trên Codeforces.

### Bài tập minh hoạ
(Đã làm ở Bài tập minh hoạ mục 2.3 file trước — Đếm số cặp có hiệu bằng k — chính là ứng dụng điển hình của Unordered Map.)

---

## 3.9. Priority Queue / Heap (Hàng đợi ưu tiên)

### Khởi tạo
```cpp
priority_queue<int> pq;                                   // MAX-HEAP mặc định
priority_queue<int, vector<int>, greater<int>> minPq;      // MIN-HEAP
priority_queue<pair<int,int>> pqPair;                       // so sánh theo .first trước

// Khởi tạo heap từ 1 mảng có sẵn - O(n), NHANH HƠN push từng phần tử (O(n log n))
vector<int> data = {5, 2, 8, 1};
priority_queue<int> pqFromVec(data.begin(), data.end());
```
```python
import heapq
pq = []                          # heapq của Python LUÔN là min-heap
heapq.heapify(pq)                 # biến 1 list thường thành heap tại chỗ - O(n)

data = [5, 2, 8, 1]
heapq.heapify(data)               # O(n), nhanh hơn heappush từng phần tử
```

### Thêm / Xoá / Truy cập
```cpp
pq.push(10);         // O(log n)
pq.pop();             // xoá phần tử ưu tiên cao nhất - O(log n)
int top = pq.top();   // xem phần tử ưu tiên cao nhất (không xoá) - O(1)
```
```python
heapq.heappush(pq, 10)     # O(log n)
val = heapq.heappop(pq)     # O(log n), TRẢ VỀ giá trị nhỏ nhất
top = pq[0]                  # xem phần tử nhỏ nhất - O(1)

# Mô phỏng MAX-HEAP: đảo dấu khi push/pop
heapq.heappush(pq, -10)
max_val = -heapq.heappop(pq)
```

### Khi nào nên dùng Priority Queue?
- Cần liên tục lấy ra phần tử **lớn nhất/nhỏ nhất** trong tập dữ liệu đang thay đổi động (thêm liên tục, không cần xoá tuỳ ý).
- Nền tảng bắt buộc cho: thuật toán Dijkstra (Chương 12), các bài toán lập lịch tham lam (Chương 25), merge k danh sách.
- **Không nên dùng** nếu cần xoá 1 phần tử **bất kỳ** (không phải phần tử ưu tiên cao nhất) — heap chuẩn không hỗ trợ hiệu quả, khi đó nên dùng `set`/`multiset`.

### Bài tập minh hoạ
Cho n công việc, mỗi công việc có thời gian xử lý `t[i]`. Chỉ có 1 máy chủ, xử lý tuần tự. Sắp xếp thứ tự xử lý để **tổng thời gian chờ** của tất cả công việc là nhỏ nhất.

**Lời giải:** Chiến lược tham lam: xử lý công việc có thời gian ngắn nhất trước (Shortest Job First) — chứng minh bằng exchange argument (Chương 25).
```cpp
long long minTotalWaitTime(vector<int> &t) {
    priority_queue<int, vector<int>, greater<int>> pq(t.begin(), t.end()); // min-heap
    long long currentTime = 0, totalWait = 0;
    while (!pq.empty()) {
        totalWait += currentTime;
        currentTime += pq.top();
        pq.pop();
    }
    return totalWait;
}
```

---

## Bảng so sánh tổng hợp — "Nên dùng cấu trúc nào?"

| Nhu cầu | Cấu trúc phù hợp | Vì sao |
|---|---|---|
| Lưu dãy có thứ tự, truy cập ngẫu nhiên | Vector | O(1) truy cập theo chỉ số |
| Thêm/xoá thường xuyên ở 2 đầu | Deque | O(1) cả 2 đầu, Vector chỉ nhanh ở cuối |
| LIFO (vào sau ra trước) | Stack | Đúng ngữ nghĩa, code rõ ràng |
| FIFO (vào trước ra trước) | Queue | Bắt buộc cho BFS |
| Kiểm tra tồn tại, không cần thứ tự | Unordered Set/Map | O(1) trung bình, nhanh nhất |
| Cần duyệt có thứ tự / tìm cận trên-dưới | Set/Map | O(log n) nhưng có `lower_bound` |
| Liên tục lấy min/max động | Priority Queue | O(log n) mỗi lần thêm/lấy ra |
| Cần xoá phần tử tuỳ ý khỏi tập có min/max | Multiset | Kết hợp được cả 2 khả năng |

---

## Tổng kết Chương 3

- **Vector** là mặc định cho hầu hết bài toán mảng — nhưng nhớ thêm/xoá ở đầu/giữa là O(n).
- **Stack/Queue/Deque** chọn theo đúng ngữ nghĩa thứ tự xử lý cần thiết, không dùng nhầm để tránh độ phức tạp bị suy biến (đặc biệt: Python `list.pop(0)` KHÔNG phải O(1)).
- **Set/Map (cây)** cho khả năng duyệt có thứ tự + tìm cận trên/dưới, đổi lại O(log n) thay vì O(1).
- **Unordered Set/Map (hash)** nhanh nhất cho tra cứu thuần tuý nhưng mất khả năng duyệt có thứ tự, và cần cẩn trọng anti-hash test.
- **Priority Queue** là công cụ không thể thay thế khi cần liên tục lấy min/max từ tập động — nhưng không hỗ trợ xoá phần tử tuỳ ý hiệu quả.
- **Kỹ năng cốt lõi của chương:** trước khi code, luôn liệt kê ra **các thao tác sẽ dùng** (thêm? xoá? tìm kiếm? cần thứ tự?) rồi tra bảng so sánh để chọn đúng cấu trúc — chọn sai cấu trúc dữ liệu là nguyên nhân phổ biến nhất gây TLE ở người mới học.

---

## Bài tập cuối chương — Chương 3

### PHẦN A: ĐỀ BÀI (15 bài, không kèm lời giải)

**Bài 1 — Valid Parentheses (LeetCode 20) — Dễ**
Cho chuỗi chỉ chứa các ký tự ngoặc `(`, `)`, `{`, `}`, `[`, `]`. Kiểm tra chuỗi ngoặc có hợp lệ không.

**Bài 2 — Baseball Game (LeetCode 682) — Dễ**
Cho danh sách thao tác ghi điểm trận bóng chày (số nguyên, "C" huỷ điểm gần nhất, "D" nhân đôi điểm gần nhất, "+" cộng 2 điểm gần nhất). Tính tổng điểm cuối cùng.

**Bài 3 — Implement Queue using Stacks (LeetCode 232) — Dễ-Trung bình**
Cài đặt Queue chỉ bằng 2 Stack, hỗ trợ push, pop, peek, empty.

**Bài 4 — Min Stack (LeetCode 155) — Trung bình**
Thiết kế Stack hỗ trợ thêm thao tác lấy giá trị nhỏ nhất hiện có, tất cả O(1).

**Bài 5 — Design Circular Deque (LeetCode 641) — Trung bình**
Thiết kế deque vòng kích thước cố định k, hỗ trợ chèn/xoá 2 đầu, kiểm tra đầy/rỗng, tất cả O(1).

**Bài 6 — Intersection of Two Arrays (LeetCode 349) — Dễ**
Cho 2 mảng số nguyên, trả về các phần tử xuất hiện trong cả 2 mảng.

**Bài 7 — Contains Duplicate II (LeetCode 219) — Trung bình**
Cho mảng và số k, kiểm tra tồn tại 2 chỉ số i, j sao cho `nums[i]=nums[j]` và `|i-j|≤k`.

**Bài 8 — Group Anagrams (LeetCode 49) — Trung bình**
Cho danh sách chuỗi, gom nhóm các chuỗi là anagram của nhau.

**Bài 9 — Kth Largest Element in an Array (LeetCode 215) — Trung bình**
Tìm phần tử lớn thứ k trong mảng không sắp xếp.

**Bài 10 — Top K Frequent Elements (LeetCode 347) — Trung bình-Khó**
Tìm k phần tử xuất hiện nhiều lần nhất trong mảng.

**Bài 11 — Josephus Problem I (CSES 2162) — Trung bình-Khó**
n người đứng vòng tròn, đếm tới người thứ 2 và loại, lặp lại tới khi còn 1 người. In thứ tự bị loại.

**Bài 12 — Concert Tickets (CSES 1091) — Khó**
h loại vé với giá cho trước, n khách lần lượt tới, mỗi khách trả tối đa 1 số tiền. Tìm vé giá cao nhất không vượt quá số tiền khách có (mỗi vé bán 1 lần).

**Bài 13 — Traffic Lights (CSES 1163) — Khó**
Trên 1 đường thẳng dài x, đèn giao thông được thêm lần lượt vào các vị trí cho trước, sau mỗi lần thêm in ra khoảng cách lớn nhất giữa 2 đèn liền kề (hoặc đèn với đầu đường).

**Bài 14 — Sliding Window Median (LeetCode 480) — Khó**
Cho mảng và kích thước cửa sổ k, tìm trung vị (median) của mọi cửa sổ trượt kích thước k.

**Bài 15 — Design Twitter (LeetCode 355) — Khó**
Thiết kế hệ thống mini Twitter: đăng bài (kèm timestamp), theo dõi/bỏ theo dõi người dùng, lấy 10 bài đăng gần nhất từ những người mình theo dõi (kể cả chính mình) theo thứ tự thời gian giảm dần.

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Valid Parentheses</summary>

```cpp
bool isValid(string s) {
    stack<char> st;
    unordered_map<char,char> pairs = {{')','('},{']','['},{'}','{'}};
    for (char c : s) {
        if (pairs.count(c)) {
            if (st.empty() || st.top() != pairs[c]) return false;
            st.pop();
        } else st.push(c);
    }
    return st.empty();
}
```
</details>

<details>
<summary>Lời giải Bài 2 — Baseball Game</summary>

```cpp
int calPoints(vector<string> &ops) {
    vector<int> st;
    for (auto &op : ops) {
        if (op == "C") st.pop_back();
        else if (op == "D") st.push_back(st.back() * 2);
        else if (op == "+") st.push_back(st[st.size()-1] + st[st.size()-2]);
        else st.push_back(stoi(op));
    }
    int sum = 0;
    for (int x : st) sum += x;
    return sum;
}
```
</details>

<details>
<summary>Lời giải Bài 3 — Implement Queue using Stacks</summary>

Dùng 2 stack: `inStack` để push, `outStack` để pop; khi `outStack` rỗng, đổ toàn bộ `inStack` sang (mỗi phần tử chỉ di chuyển 1 lần → khấu hao O(1), xem lại Chương 2.3).
```cpp
class MyQueue {
    stack<int> inStack, outStack;
    void moveIfNeeded() {
        if (outStack.empty())
            while (!inStack.empty()) { outStack.push(inStack.top()); inStack.pop(); }
    }
public:
    void push(int x) { inStack.push(x); }
    int pop() { moveIfNeeded(); int v = outStack.top(); outStack.pop(); return v; }
    int peek() { moveIfNeeded(); return outStack.top(); }
    bool empty() { return inStack.empty() && outStack.empty(); }
};
```
</details>

<details>
<summary>Lời giải Bài 4 — Min Stack</summary>

Dùng thêm 1 stack phụ lưu giá trị min tại mỗi thời điểm tương ứng.
```cpp
class MinStack {
    stack<int> st, minSt;
public:
    void push(int x) {
        st.push(x);
        minSt.push(minSt.empty() ? x : min(x, minSt.top()));
    }
    void pop() { st.pop(); minSt.pop(); }
    int top() { return st.top(); }
    int getMin() { return minSt.top(); }
};
```
</details>

<details>
<summary>Lời giải Bài 5 — Design Circular Deque</summary>

Dùng mảng cố định kích thước k + con trỏ `front` chạy vòng (modulo k).
```cpp
class MyCircularDeque {
    vector<int> buf; int front, size_, capacity;
public:
    MyCircularDeque(int k) : buf(k), front(0), size_(0), capacity(k) {}
    bool insertFront(int x) {
        if (size_ == capacity) return false;
        front = (front - 1 + capacity) % capacity;
        buf[front] = x; size_++; return true;
    }
    bool insertLast(int x) {
        if (size_ == capacity) return false;
        buf[(front + size_) % capacity] = x; size_++; return true;
    }
    bool deleteFront() {
        if (size_ == 0) return false;
        front = (front + 1) % capacity; size_--; return true;
    }
    bool deleteLast() {
        if (size_ == 0) return false;
        size_--; return true;
    }
};
```
</details>

<details>
<summary>Lời giải Bài 6 — Intersection of Two Arrays</summary>

```cpp
vector<int> intersection(vector<int> &a, vector<int> &b) {
    unordered_set<int> setA(a.begin(), a.end());
    vector<int> result;
    for (int x : b) if (setA.count(x)) { result.push_back(x); setA.erase(x); }
    return result;
}
```
</details>

<details>
<summary>Lời giải Bài 7 — Contains Duplicate II</summary>

```cpp
bool containsNearbyDuplicate(vector<int> &nums, int k) {
    unordered_map<int,int> lastIndex;
    for (int i = 0; i < (int)nums.size(); i++) {
        if (lastIndex.count(nums[i]) && i - lastIndex[nums[i]] <= k) return true;
        lastIndex[nums[i]] = i;
    }
    return false;
}
```
</details>

<details>
<summary>Lời giải Bài 8 — Group Anagrams</summary>

Sort ký tự mỗi chuỗi làm khoá, dùng map gom nhóm theo khoá.
```cpp
vector<vector<string>> groupAnagrams(vector<string> &strs) {
    unordered_map<string, vector<string>> groups;
    for (auto &s : strs) {
        string key = s;
        sort(key.begin(), key.end());
        groups[key].push_back(s);
    }
    vector<vector<string>> result;
    for (auto &[k, v] : groups) result.push_back(v);
    return result;
}
```
</details>

<details>
<summary>Lời giải Bài 9 — Kth Largest Element</summary>

Dùng min-heap kích thước k.
```cpp
int findKthLargest(vector<int> &nums, int k) {
    priority_queue<int, vector<int>, greater<int>> minHeap;
    for (int x : nums) {
        minHeap.push(x);
        if ((int)minHeap.size() > k) minHeap.pop();
    }
    return minHeap.top();
}
```
</details>

<details>
<summary>Lời giải Bài 10 — Top K Frequent Elements</summary>

Đếm tần suất bằng map, dùng min-heap kích thước k theo tần suất.
```cpp
vector<int> topKFrequent(vector<int> &nums, int k) {
    unordered_map<int,int> freq;
    for (int x : nums) freq[x]++;
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> minHeap;
    for (auto &[val, f] : freq) {
        minHeap.push({f, val});
        if ((int)minHeap.size() > k) minHeap.pop();
    }
    vector<int> result;
    while (!minHeap.empty()) { result.push_back(minHeap.top().second); minHeap.pop(); }
    return result;
}
```
</details>

<details>
<summary>Lời giải Bài 11 — Josephus Problem I</summary>

Dùng queue mô phỏng vòng tròn trực tiếp.
```cpp
vector<int> josephus(int n) {
    queue<int> q;
    for (int i = 1; i <= n; i++) q.push(i);
    vector<int> eliminated;
    while (q.size() > 1) {
        q.push(q.front()); q.pop();  // người sống, đưa về cuối
        eliminated.push_back(q.front()); q.pop(); // người bị loại
    }
    eliminated.push_back(q.front());
    return eliminated;
}
```
</details>

<details>
<summary>Lời giải Bài 12 — Concert Tickets</summary>

Dùng `multiset`, mỗi truy vấn dùng `upper_bound` tìm vé đắt nhất không vượt quá số tiền.
```cpp
multiset<int> tickets;
// ... đọc h loại vé, insert vào tickets
int budget; cin >> budget;
auto it = tickets.upper_bound(budget);
if (it == tickets.begin()) cout << -1 << "\n";
else { --it; cout << *it << "\n"; tickets.erase(it); }
```
</details>

<details>
<summary>Lời giải Bài 13 — Traffic Lights</summary>

Dùng `set` lưu vị trí đèn đã đặt + `multiset` lưu độ dài các khoảng — khi thêm đèn mới, tìm 2 đèn lân cận (dùng `lower_bound` trên set vị trí), xoá khoảng cũ, thêm 2 khoảng mới vào multiset độ dài, in ra `*multiset.rbegin()` (giá trị lớn nhất).
```cpp
set<int> positions = {0, x}; // 2 đầu đường
multiset<int> gaps = {x};     // khoảng cách ban đầu
for (int i = 0; i < n; i++) {
    int p; cin >> p;
    auto it = positions.lower_bound(p);
    int right = *it, left = *prev(it);
    gaps.erase(gaps.find(right - left));
    gaps.insert(p - left);
    gaps.insert(right - p);
    positions.insert(p);
    cout << *gaps.rbegin() << " ";
}
```
</details>

<details>
<summary>Lời giải Bài 14 — Sliding Window Median</summary>

Dùng 2 heap (max-heap cho nửa nhỏ, min-heap cho nửa lớn) cân bằng kích thước, kết hợp lazy deletion (đánh dấu phần tử đã ra khỏi cửa sổ, chỉ thực sự xoá khi nó lên đỉnh heap) vì heap chuẩn không hỗ trợ xoá phần tử tuỳ ý hiệu quả — đây là bài nâng cao, gợi ý dùng `multiset` thay vì tự cài 2 heap để đơn giản hoá:
```cpp
vector<double> medianSlidingWindow(vector<int> &nums, int k) {
    multiset<int> window(nums.begin(), nums.begin() + k);
    auto mid = next(window.begin(), k / 2);
    vector<double> result;

    for (int i = k; ; i++) {
        result.push_back(k % 2 ? *mid : ((double)*mid + *prev(mid)) / 2.0);
        if (i == (int)nums.size()) break;

        window.insert(nums[i]);
        if (nums[i] < *mid) mid--;

        if (nums[i-k] <= *mid) mid++;
        window.erase(window.lower_bound(nums[i-k]));
    }
    return result;
}
```
</details>

<details>
<summary>Lời giải Bài 15 — Design Twitter</summary>

Dùng `unordered_map<int, vector<pair<int,int>>>` lưu (timestamp, tweetId) cho mỗi user, `unordered_map<int, unordered_set<int>>` lưu danh sách follow, và priority_queue để gộp k danh sách bài đăng đã sắp theo thời gian (giống kỹ thuật merge k sorted lists, mục 2.4 chương trước).
```cpp
class Twitter {
    int timestamp = 0;
    unordered_map<int, vector<pair<int,int>>> tweets;     // userId -> [(time, tweetId)]
    unordered_map<int, unordered_set<int>> following;      // userId -> tập người đang follow

public:
    void postTweet(int userId, int tweetId) {
        tweets[userId].push_back({timestamp++, tweetId});
    }
    void follow(int followerId, int followeeId) { following[followerId].insert(followeeId); }
    void unfollow(int followerId, int followeeId) { following[followerId].erase(followeeId); }

    vector<int> getNewsFeed(int userId) {
        priority_queue<tuple<int,int,int>> pq; // (time, tweetId, listIndex trong nguồn)
        vector<vector<pair<int,int>>*> sources;
        sources.push_back(&tweets[userId]);
        for (int fid : following[userId]) sources.push_back(&tweets[fid]);

        for (int i = 0; i < (int)sources.size(); i++)
            if (!sources[i]->empty())
                pq.push({sources[i]->back().first, sources[i]->back().second, i});

        vector<int> result;
        vector<int> idx(sources.size(), 1); // đếm từ cuối lên (mới nhất trước)
        while (!pq.empty() && (int)result.size() < 10) {
            auto [t, tid, srcIdx] = pq.top(); pq.pop();
            result.push_back(tid);
            int nextPos = (int)sources[srcIdx]->size() - 1 - idx[srcIdx];
            if (nextPos >= 0) {
                pq.push({(*sources[srcIdx])[nextPos].first, (*sources[srcIdx])[nextPos].second, srcIdx});
                idx[srcIdx]++;
            }
        }
        return result;
    }
};
```
</details>
