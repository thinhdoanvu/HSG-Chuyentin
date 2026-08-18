# Chương 11: Nền tảng đồ thị

---

## 11.1. Biểu diễn đồ thị (Ma trận kề và Danh sách kề)

**Nêu bài toán:** Cho một mạng lưới giao thông gồm n thành phố và m con đường hai chiều nối giữa các thành phố. Hãy lưu trữ thông tin mạng lưới này trong máy tính sao cho có thể trả lời nhanh câu hỏi "thành phố A có đường nối trực tiếp tới thành phố B không?" và "liệt kê tất cả các thành phố nối trực tiếp với thành phố A".

**Phân tích vấn đề:** Đây là bài toán nền tảng của toàn bộ lý thuyết đồ thị — cách ta **lưu trữ** đồ thị trong bộ nhớ sẽ quyết định tốc độ của mọi thuật toán chạy trên đó sau này. Cần cân nhắc giữa tốc độ truy vấn "có cạnh hay không" và lượng bộ nhớ sử dụng, đặc biệt khi n hoặc m rất lớn.

**Giải pháp 1 — Ma trận kề (Adjacency Matrix):** Dùng một mảng 2 chiều `ke[i][j]`, với `ke[i][j] = true` nếu có cạnh nối trực tiếp giữa đỉnh `i` và đỉnh `j`.

**Khó khăn của ma trận kề:** Bộ nhớ cần dùng luôn là O(n²), bất kể đồ thị có bao nhiêu cạnh thực sự. Với n = 100.000, ma trận kề cần tới 10^10 ô nhớ — hoàn toàn không khả thi, ngay cả khi đồ thị chỉ có vài trăm cạnh.

**Giải pháp 2 — Danh sách kề (Adjacency List):** Với mỗi đỉnh, chỉ lưu **danh sách các đỉnh kề trực tiếp** với nó (thay vì lưu đủ n giá trị true/false cho mọi đỉnh khác). Bộ nhớ sử dụng là O(n + m) — chỉ phụ thuộc vào số cạnh thực sự tồn tại, không phụ thuộc vào n².

**Khi nào dùng ma trận kề, khi nào dùng danh sách kề?**
- Ma trận kề: phù hợp khi đồ thị **dày đặc** (m gần bằng n²) và n nhỏ (thường n ≤ 1000-2000), cần kiểm tra "có cạnh hay không" cực nhanh O(1).
- Danh sách kề: phù hợp với **đa số bài toán thực tế**, đặc biệt khi n lớn hoặc đồ thị thưa (m nhỏ hơn nhiều so với n²) — đây là lựa chọn mặc định trong lập trình thi đấu.

### Minh hoạ lời giải chi tiết

Với đồ thị 4 đỉnh, các cạnh: (0-1), (0-2), (1-2), (2-3):

**Ma trận kề:**
```
     0  1  2  3
0  [ 0  1  1  0 ]
1  [ 1  0  1  0 ]
2  [ 1  1  0  1 ]
3  [ 0  0  1  0 ]
```

**Danh sách kề:**
```
Đỉnh 0: [1, 2]
Đỉnh 1: [0, 2]
Đỉnh 2: [0, 1, 3]
Đỉnh 3: [2]
```

Với danh sách kề, tổng số phần tử lưu trữ trên toàn bộ danh sách chỉ là `2×m = 8` (mỗi cạnh vô hướng xuất hiện 2 lần, 1 lần ở mỗi đầu), trong khi ma trận kề luôn tốn `n² = 16` ô nhớ dù đồ thị này chỉ có 4 cạnh.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 4; // số đỉnh
    int m = 4; // số cạnh

    // Cách 1: Ma trận kề
    vector<vector<bool>> maTranKe(n, vector<bool>(n, false));

    // Cách 2: Danh sách kề
    vector<vector<int>> danhSachKe(n);

    vector<pair<int,int>> danhSachCanh = {{0,1}, {0,2}, {1,2}, {2,3}};

    for (int i = 0; i < (int)danhSachCanh.size(); i++) {
        int u = danhSachCanh[i].first;
        int v = danhSachCanh[i].second;

        // Cập nhật ma trận kề (đồ thị vô hướng nên đối xứng)
        maTranKe[u][v] = true;
        maTranKe[v][u] = true;

        // Cập nhật danh sách kề (mỗi cạnh vô hướng thêm vào cả 2 chiều)
        danhSachKe[u].push_back(v);
        danhSachKe[v].push_back(u);
    }

    // Kiểm tra có cạnh (0,2) hay không bằng ma trận kề - O(1)
    cout << "Co canh (0,2): " << (maTranKe[0][2] ? "co" : "khong") << endl;

    // Liệt kê các đỉnh kề với đỉnh 2 bằng danh sách kề
    cout << "Cac dinh ke voi dinh 2: ";
    for (int i = 0; i < (int)danhSachKe[2].size(); i++) {
        cout << danhSachKe[2][i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
n = 4
m = 4

ma_tran_ke = [[False] * n for _ in range(n)]
danh_sach_ke = [[] for _ in range(n)]

danh_sach_canh = [(0, 1), (0, 2), (1, 2), (2, 3)]

for u, v in danh_sach_canh:
    ma_tran_ke[u][v] = True
    ma_tran_ke[v][u] = True

    danh_sach_ke[u].append(v)
    danh_sach_ke[v].append(u)

print("Co canh (0,2):", "co" if ma_tran_ke[0][2] else "khong")

print("Cac dinh ke voi dinh 2:", end=" ")
for dinh in danh_sach_ke[2]:
    print(dinh, end=" ")
print()
```

### Ví dụ 1 (Dễ) — Biểu diễn đồ thị có trọng số

**Phân tích:** Khi cạnh có trọng số (ví dụ khoảng cách, chi phí), ta cần lưu thêm thông tin trọng số đi kèm mỗi cạnh — dùng `pair<int,int>` (đỉnh kề, trọng số) trong danh sách kề.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 4;
    vector<vector<pair<int,int>>> danhSachKeCoTrongSo(n); // (đỉnh kề, trọng số)

    vector<tuple<int,int,int>> canhCoTrongSo = {
        {0, 1, 5}, {0, 2, 3}, {1, 2, 2}, {2, 3, 7}
    };

    for (int i = 0; i < (int)canhCoTrongSo.size(); i++) {
        int u = get<0>(canhCoTrongSo[i]);
        int v = get<1>(canhCoTrongSo[i]);
        int trongSo = get<2>(canhCoTrongSo[i]);

        danhSachKeCoTrongSo[u].push_back(make_pair(v, trongSo));
        danhSachKeCoTrongSo[v].push_back(make_pair(u, trongSo));
    }

    for (int i = 0; i < (int)danhSachKeCoTrongSo[2].size(); i++) {
        int dinhKe = danhSachKeCoTrongSo[2][i].first;
        int trongSo = danhSachKeCoTrongSo[2][i].second;
        cout << "Dinh 2 noi voi dinh " << dinhKe << " trong so " << trongSo << endl;
    }

    return 0;
}
```

#### PYTHON
```python
n = 4
danh_sach_ke_co_trong_so = [[] for _ in range(n)]

canh_co_trong_so = [(0, 1, 5), (0, 2, 3), (1, 2, 2), (2, 3, 7)]

for u, v, trong_so in canh_co_trong_so:
    danh_sach_ke_co_trong_so[u].append((v, trong_so))
    danh_sach_ke_co_trong_so[v].append((u, trong_so))

for dinh_ke, trong_so in danh_sach_ke_co_trong_so[2]:
    print("Dinh 2 noi voi dinh", dinh_ke, "trong so", trong_so)
```

### Ví dụ 2 (Trung bình) — Đồ thị có hướng và tính bậc vào/bậc ra

Bài toán: cho đồ thị có hướng, hãy tính "bậc ra" (số cạnh xuất phát từ đỉnh) và "bậc vào" (số cạnh đi tới đỉnh) của mỗi đỉnh.

**Phân tích:** Với đồ thị có hướng, danh sách kề chỉ lưu **1 chiều** (từ đỉnh xuất phát tới đỉnh đích) — khác với đồ thị vô hướng lưu cả 2 chiều. Bậc ra của 1 đỉnh chính là độ dài danh sách kề của đỉnh đó; bậc vào cần đếm riêng bằng một mảng phụ.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 4;
    vector<vector<int>> danhSachKeCoHuong(n);
    vector<int> bacVao(n, 0);

    vector<pair<int,int>> canhCoHuong = {{0,1}, {0,2}, {1,2}, {2,3}, {3,0}};

    for (int i = 0; i < (int)canhCoHuong.size(); i++) {
        int u = canhCoHuong[i].first;
        int v = canhCoHuong[i].second;

        danhSachKeCoHuong[u].push_back(v); // chỉ thêm 1 chiều
        bacVao[v] = bacVao[v] + 1;
    }

    for (int i = 0; i < n; i++) {
        int bacRa = (int)danhSachKeCoHuong[i].size();
        cout << "Dinh " << i << ": bac ra = " << bacRa << ", bac vao = " << bacVao[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
n = 4
danh_sach_ke_co_huong = [[] for _ in range(n)]
bac_vao = [0] * n

canh_co_huong = [(0, 1), (0, 2), (1, 2), (2, 3), (3, 0)]

for u, v in canh_co_huong:
    danh_sach_ke_co_huong[u].append(v)
    bac_vao[v] = bac_vao[v] + 1

for i in range(n):
    bac_ra = len(danh_sach_ke_co_huong[i])
    print("Dinh", i, ": bac ra =", bac_ra, ", bac vao =", bac_vao[i])
```

### Ví dụ 3 (Khó) — Biểu diễn đồ thị nén cho lưới ô vuông (Grid Graph)

Bài toán: cho một lưới ô vuông kích thước n×m, mỗi ô có thể đi được (0) hoặc chướng ngại vật (1). Coi mỗi ô là 1 đỉnh, có cạnh giữa 2 ô liền kề (trên/dưới/trái/phải) nếu cả 2 ô đều đi được. Hãy biểu diễn đồ thị này một cách hiệu quả **mà không cần xây dựng danh sách kề tường minh**.

**Phân tích:** Xây dựng danh sách kề tường minh cho lưới n×m sẽ tốn O(n×m) bộ nhớ và thời gian khởi tạo — với lưới lớn (ví dụ 1000×1000 = 10^6 ô), việc này vẫn khả thi nhưng có thể tối ưu hơn: thay vì lưu danh sách kề, ta **tính trực tiếp** các đỉnh kề khi cần, dùng công thức chuyển đổi giữa toạ độ (hàng, cột) và chỉ số đỉnh duy nhất, kết hợp mảng "4 hướng di chuyển".

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int n, m;
vector<vector<int>> luoi;

int chuyenToaDoThanhChiSo(int hang, int cot) {
    return hang * m + cot;
}

vector<int> layDinhKe(int hang, int cot) {
    vector<int> ketQua;
    int dHang[] = {0, 0, 1, -1};
    int dCot[] = {1, -1, 0, 0};

    for (int huong = 0; huong < 4; huong++) {
        int hangMoi = hang + dHang[huong];
        int cotMoi = cot + dCot[huong];

        bool hopLe = (hangMoi >= 0 && hangMoi < n && cotMoi >= 0 && cotMoi < m);
        if (hopLe == true && luoi[hangMoi][cotMoi] == 0) {
            ketQua.push_back(chuyenToaDoThanhChiSo(hangMoi, cotMoi));
        }
    }

    return ketQua;
}

int main() {
    n = 3; m = 3;
    luoi = {
        {0, 0, 1},
        {0, 1, 0},
        {0, 0, 0}
    };

    vector<int> dinhKeCua_1_1 = layDinhKe(1, 1); // sẽ rỗng vì (1,1) là chướng ngại vật
    vector<int> dinhKeCua_0_0 = layDinhKe(0, 0);

    cout << "So dinh ke voi (0,0): " << dinhKeCua_0_0.size() << endl;

    return 0;
}
```

#### PYTHON
```python
n = 3
m = 3
luoi = [
    [0, 0, 1],
    [0, 1, 0],
    [0, 0, 0],
]


def chuyen_toa_do_thanh_chi_so(hang, cot):
    return hang * m + cot


def lay_dinh_ke(hang, cot):
    ket_qua = []
    d_hang = [0, 0, 1, -1]
    d_cot = [1, -1, 0, 0]

    for huong in range(4):
        hang_moi = hang + d_hang[huong]
        cot_moi = cot + d_cot[huong]

        hop_le = (0 <= hang_moi < n) and (0 <= cot_moi < m)
        if hop_le and luoi[hang_moi][cot_moi] == 0:
            ket_qua.append(chuyen_toa_do_thanh_chi_so(hang_moi, cot_moi))

    return ket_qua


dinh_ke_cua_0_0 = lay_dinh_ke(0, 0)
print("So dinh ke voi (0,0):", len(dinh_ke_cua_0_0))
```

**Bài tập minh hoạ:** Cho danh sách cạnh của một đồ thị vô hướng, hãy chuyển đổi nó thành ma trận kề, rồi từ ma trận kề chuyển ngược lại thành danh sách kề — để rèn luyện việc chuyển đổi qua lại giữa 2 cách biểu diễn.

**Lời giải:**

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 4;
    vector<pair<int,int>> canh = {{0,1},{1,2},{2,3},{0,3}};

    vector<vector<bool>> maTran(n, vector<bool>(n, false));
    for (int i = 0; i < (int)canh.size(); i++) {
        int u = canh[i].first, v = canh[i].second;
        maTran[u][v] = true;
        maTran[v][u] = true;
    }

    vector<vector<int>> danhSachKe(n);
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (maTran[i][j] == true) {
                danhSachKe[i].push_back(j);
            }
        }
    }

    for (int i = 0; i < n; i++) {
        cout << "Dinh " << i << ": ";
        for (int j = 0; j < (int)danhSachKe[i].size(); j++) {
            cout << danhSachKe[i][j] << " ";
        }
        cout << endl;
    }

    return 0;
}
```

#### PYTHON
```python
n = 4
canh = [(0, 1), (1, 2), (2, 3), (0, 3)]

ma_tran = [[False] * n for _ in range(n)]
for u, v in canh:
    ma_tran[u][v] = True
    ma_tran[v][u] = True

danh_sach_ke = [[] for _ in range(n)]
for i in range(n):
    for j in range(n):
        if ma_tran[i][j] == True:
            danh_sach_ke[i].append(j)

for i in range(n):
    print("Dinh", i, ":", danh_sach_ke[i])
```

---

## 11.2. DFS, BFS và các ứng dụng

**Nêu bài toán:** Cho một đồ thị và một đỉnh xuất phát, hãy xác định tất cả các đỉnh có thể tới được từ đỉnh xuất phát đó (gọi là "duyệt đồ thị").

**Phân tích vấn đề:** Đây là bài toán nền tảng nhất trên đồ thị — gần như mọi thuật toán đồ thị phức tạp hơn (đường đi ngắn nhất, cây khung, thành phần liên thông...) đều xây dựng trên nền tảng của việc "duyệt qua các đỉnh có thể tới được". Có 2 chiến lược duyệt cơ bản: đi **sâu** nhất có thể trước khi quay lại (DFS), hoặc đi theo **từng lớp** một, gần nhất trước (BFS).

**Giải pháp 1 — DFS (Depth-First Search — Tìm kiếm theo chiều sâu):** Từ đỉnh hiện tại, đi tới 1 đỉnh kề chưa thăm, tiếp tục đi sâu từ đỉnh đó, chỉ quay lại khi không còn đỉnh kề nào chưa thăm. Cài đặt tự nhiên bằng đệ quy (giống Chương 7).

**Giải pháp 2 — BFS (Breadth-First Search — Tìm kiếm theo chiều rộng):** Từ đỉnh hiện tại, thăm **tất cả** các đỉnh kề trực tiếp trước, rồi mới thăm các đỉnh kề của các đỉnh đó (theo từng "lớp" tăng dần khoảng cách). Cài đặt bằng Queue (Chương 3).

**Khi nào dùng DFS, khi nào dùng BFS?**
- BFS: **bắt buộc** khi cần tìm đường đi **ngắn nhất theo số cạnh** trên đồ thị không trọng số — vì BFS duyệt đúng theo thứ tự khoảng cách tăng dần.
- DFS: phù hợp khi chỉ cần kiểm tra "có tới được hay không" (không quan tâm độ dài đường đi), hoặc khi cần duyệt theo cấu trúc đệ quy tự nhiên (như DFS trên cây ở Chương 9), hoặc cho các bài toán như tìm chu trình, sắp xếp tô-pô (mục 11.3, 11.4).

### Minh hoạ lời giải chi tiết

Với đồ thị: 0-1, 0-2, 1-3, 2-3, 3-4, xuất phát từ đỉnh 0:

**DFS (đi sâu):** 0 → 1 (đi sâu) → 3 (đi sâu) → 2 (thăm qua cạnh 3-2, hoặc quay lui rồi thăm qua 0-2 tuỳ thứ tự danh sách kề) → 4. Thứ tự thăm có thể là: `0, 1, 3, 2, 4` hoặc `0, 1, 3, 4, 2` tuỳ thứ tự lưu trong danh sách kề.

**BFS (đi theo lớp):** Lớp 0 (khoảng cách 0): `{0}`. Lớp 1 (khoảng cách 1, kề trực tiếp với 0): `{1, 2}`. Lớp 2 (khoảng cách 2): `{3}` (kề với cả 1 và 2). Lớp 3 (khoảng cách 3): `{4}`. Thứ tự thăm: `0, 1, 2, 3, 4`, và ta biết ngay khoảng cách từ 0 tới 4 là 3 cạnh.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKe;
vector<bool> daTham;

void dfs(int dinhHienTai) {
    daTham[dinhHienTai] = true;
    cout << dinhHienTai << " ";

    for (int i = 0; i < (int)danhSachKe[dinhHienTai].size(); i++) {
        int dinhKe = danhSachKe[dinhHienTai][i];
        if (daTham[dinhKe] == false) {
            dfs(dinhKe);
        }
    }
}

vector<int> bfs(int dinhXuatPhat, int n) {
    vector<int> khoangCach(n, -1);
    queue<int> hangDoi;

    khoangCach[dinhXuatPhat] = 0;
    hangDoi.push(dinhXuatPhat);

    while (!hangDoi.empty()) {
        int dinhHienTai = hangDoi.front();
        hangDoi.pop();

        for (int i = 0; i < (int)danhSachKe[dinhHienTai].size(); i++) {
            int dinhKe = danhSachKe[dinhHienTai][i];
            if (khoangCach[dinhKe] == -1) {
                khoangCach[dinhKe] = khoangCach[dinhHienTai] + 1;
                hangDoi.push(dinhKe);
            }
        }
    }

    return khoangCach;
}

int main() {
    int n = 5;
    danhSachKe.assign(n, {});
    daTham.assign(n, false);

    vector<pair<int,int>> canh = {{0,1},{0,2},{1,3},{2,3},{3,4}};
    for (int i = 0; i < (int)canh.size(); i++) {
        int u = canh[i].first, v = canh[i].second;
        danhSachKe[u].push_back(v);
        danhSachKe[v].push_back(u);
    }

    cout << "Thu tu DFS: ";
    dfs(0);
    cout << endl;

    vector<int> khoangCach = bfs(0, n);
    cout << "Khoang cach tu dinh 0: ";
    for (int i = 0; i < n; i++) {
        cout << khoangCach[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque

danh_sach_ke = []
da_tham = []


def dfs(dinh_hien_tai):
    da_tham[dinh_hien_tai] = True
    print(dinh_hien_tai, end=" ")

    for dinh_ke in danh_sach_ke[dinh_hien_tai]:
        if da_tham[dinh_ke] == False:
            dfs(dinh_ke)


def bfs(dinh_xuat_phat, n):
    khoang_cach = [-1] * n
    hang_doi = deque()

    khoang_cach[dinh_xuat_phat] = 0
    hang_doi.append(dinh_xuat_phat)

    while len(hang_doi) > 0:
        dinh_hien_tai = hang_doi.popleft()

        for dinh_ke in danh_sach_ke[dinh_hien_tai]:
            if khoang_cach[dinh_ke] == -1:
                khoang_cach[dinh_ke] = khoang_cach[dinh_hien_tai] + 1
                hang_doi.append(dinh_ke)

    return khoang_cach


n = 5
danh_sach_ke = [[] for _ in range(n)]
da_tham = [False] * n

canh = [(0, 1), (0, 2), (1, 3), (2, 3), (3, 4)]
for u, v in canh:
    danh_sach_ke[u].append(v)
    danh_sach_ke[v].append(u)

print("Thu tu DFS: ", end="")
dfs(0)
print()

khoang_cach = bfs(0, n)
print("Khoang cach tu dinh 0:", khoang_cach)
```

### Ví dụ 1 (Dễ) — Đếm số thành phần liên thông (Connected Components)

Bài toán: cho đồ thị vô hướng có thể không liên thông (một số đỉnh không tới được từ đỉnh khác), hãy đếm số "cụm" đỉnh liên thông với nhau (thành phần liên thông).

**Phân tích:** Chạy DFS (hoặc BFS) từ mọi đỉnh **chưa được thăm**; mỗi lần bắt đầu 1 lượt DFS/BFS mới, ta tăng biến đếm thành phần liên thông lên 1.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKe;
vector<bool> daTham;

void dfsDanhDauLienThong(int u) {
    daTham[u] = true;
    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int v = danhSachKe[u][i];
        if (daTham[v] == false) {
            dfsDanhDauLienThong(v);
        }
    }
}

int demThanhPhanLienThong(int n) {
    daTham.assign(n, false);
    int soLuong = 0;

    for (int i = 0; i < n; i++) {
        if (daTham[i] == false) {
            dfsDanhDauLienThong(i);
            soLuong = soLuong + 1;
        }
    }

    return soLuong;
}

int main() {
    int n = 6;
    danhSachKe.assign(n, {});

    vector<pair<int,int>> canh = {{0,1},{1,2},{3,4}}; // đỉnh 5 cô lập
    for (int i = 0; i < (int)canh.size(); i++) {
        int u = canh[i].first, v = canh[i].second;
        danhSachKe[u].push_back(v);
        danhSachKe[v].push_back(u);
    }

    cout << demThanhPhanLienThong(n) << endl; // 3 thành phần: {0,1,2}, {3,4}, {5}

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke = []
da_tham = []


def dfs_danh_dau_lien_thong(u):
    da_tham[u] = True
    for v in danh_sach_ke[u]:
        if da_tham[v] == False:
            dfs_danh_dau_lien_thong(v)


def dem_thanh_phan_lien_thong(n):
    global da_tham
    da_tham = [False] * n
    so_luong = 0

    for i in range(n):
        if da_tham[i] == False:
            dfs_danh_dau_lien_thong(i)
            so_luong = so_luong + 1

    return so_luong


n = 6
danh_sach_ke = [[] for _ in range(n)]

canh = [(0, 1), (1, 2), (3, 4)]
for u, v in canh:
    danh_sach_ke[u].append(v)
    danh_sach_ke[v].append(u)

print(dem_thanh_phan_lien_thong(n))  # 3 thành phần
```

### Ví dụ 2 (Trung bình) — BFS trên lưới ô vuông (kết hợp Ví dụ 3, mục 11.1)

Bài toán: cho lưới ô vuông có ô trống và ô chướng ngại vật, tìm đường đi ngắn nhất (theo số bước di chuyển) từ ô xuất phát tới ô đích.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int bfsTrenLuoi(vector<vector<int>> luoi, pair<int,int> xuatPhat, pair<int,int> dich) {
    int n = (int)luoi.size();
    int m = (int)luoi[0].size();

    vector<vector<int>> khoangCach(n, vector<int>(m, -1));
    queue<pair<int,int>> hangDoi;

    khoangCach[xuatPhat.first][xuatPhat.second] = 0;
    hangDoi.push(xuatPhat);

    int dHang[] = {0, 0, 1, -1};
    int dCot[] = {1, -1, 0, 0};

    while (!hangDoi.empty()) {
        pair<int,int> hienTai = hangDoi.front();
        hangDoi.pop();

        int hang = hienTai.first, cot = hienTai.second;

        for (int huong = 0; huong < 4; huong++) {
            int hangMoi = hang + dHang[huong];
            int cotMoi = cot + dCot[huong];

            bool hopLe = (hangMoi >= 0 && hangMoi < n && cotMoi >= 0 && cotMoi < m);
            if (hopLe && luoi[hangMoi][cotMoi] == 0 && khoangCach[hangMoi][cotMoi] == -1) {
                khoangCach[hangMoi][cotMoi] = khoangCach[hang][cot] + 1;
                hangDoi.push(make_pair(hangMoi, cotMoi));
            }
        }
    }

    return khoangCach[dich.first][dich.second];
}

int main() {
    vector<vector<int>> luoi = {
        {0, 0, 0},
        {1, 1, 0},
        {0, 0, 0}
    };

    cout << bfsTrenLuoi(luoi, make_pair(0,0), make_pair(2,2)) << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def bfs_tren_luoi(luoi, xuat_phat, dich):
    n = len(luoi)
    m = len(luoi[0])

    khoang_cach = [[-1] * m for _ in range(n)]
    hang_doi = deque()

    khoang_cach[xuat_phat[0]][xuat_phat[1]] = 0
    hang_doi.append(xuat_phat)

    d_hang = [0, 0, 1, -1]
    d_cot = [1, -1, 0, 0]

    while len(hang_doi) > 0:
        hang, cot = hang_doi.popleft()

        for huong in range(4):
            hang_moi = hang + d_hang[huong]
            cot_moi = cot + d_cot[huong]

            hop_le = (0 <= hang_moi < n) and (0 <= cot_moi < m)
            if hop_le and luoi[hang_moi][cot_moi] == 0 and khoang_cach[hang_moi][cot_moi] == -1:
                khoang_cach[hang_moi][cot_moi] = khoang_cach[hang][cot] + 1
                hang_doi.append((hang_moi, cot_moi))

    return khoang_cach[dich[0]][dich[1]]


luoi = [
    [0, 0, 0],
    [1, 1, 0],
    [0, 0, 0],
]

print(bfs_tren_luoi(luoi, (0, 0), (2, 2)))
```

### Ví dụ 3 (Khó) — Multi-source BFS (BFS đa nguồn)

Bài toán: cho lưới ô vuông có nhiều "nguồn lửa" (điểm bắt đầu cháy) cùng lúc, lửa lan ra 4 hướng mỗi phút. Hãy tính thời gian để lửa lan tới mỗi ô trong lưới.

**Phân tích:** Thay vì chạy BFS riêng cho từng nguồn lửa (tốn O(số_nguồn × n × m)), ta có thể đưa **tất cả** các nguồn lửa vào hàng đợi **cùng lúc, ngay từ đầu**, với khoảng cách khởi tạo đều là 0. BFS khi đó tự động lan toả từ tất cả các nguồn đồng thời, đạt đúng thời gian lan tới từng ô mà không cần chạy nhiều lượt BFS riêng biệt.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> bfsDaNguon(vector<vector<int>> luoi, vector<pair<int,int>> cacNguon) {
    int n = (int)luoi.size();
    int m = (int)luoi[0].size();

    vector<vector<int>> khoangCach(n, vector<int>(m, -1));
    queue<pair<int,int>> hangDoi;

    // Đưa TẤT CẢ các nguồn vào hàng đợi ngay từ đầu, khoảng cách đều là 0
    for (int i = 0; i < (int)cacNguon.size(); i++) {
        int hang = cacNguon[i].first, cot = cacNguon[i].second;
        khoangCach[hang][cot] = 0;
        hangDoi.push(cacNguon[i]);
    }

    int dHang[] = {0, 0, 1, -1};
    int dCot[] = {1, -1, 0, 0};

    while (!hangDoi.empty()) {
        pair<int,int> hienTai = hangDoi.front();
        hangDoi.pop();

        int hang = hienTai.first, cot = hienTai.second;

        for (int huong = 0; huong < 4; huong++) {
            int hangMoi = hang + dHang[huong];
            int cotMoi = cot + dCot[huong];

            bool hopLe = (hangMoi >= 0 && hangMoi < n && cotMoi >= 0 && cotMoi < m);
            if (hopLe && luoi[hangMoi][cotMoi] == 0 && khoangCach[hangMoi][cotMoi] == -1) {
                khoangCach[hangMoi][cotMoi] = khoangCach[hang][cot] + 1;
                hangDoi.push(make_pair(hangMoi, cotMoi));
            }
        }
    }

    return khoangCach;
}

int main() {
    vector<vector<int>> luoi = {
        {0, 0, 0, 0},
        {0, 0, 0, 0},
        {0, 0, 0, 0}
    };

    vector<pair<int,int>> cacNguon = {{0,0}, {2,3}};

    vector<vector<int>> ketQua = bfsDaNguon(luoi, cacNguon);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        for (int j = 0; j < (int)ketQua[i].size(); j++) {
            cout << ketQua[i][j] << " ";
        }
        cout << endl;
    }

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def bfs_da_nguon(luoi, cac_nguon):
    n = len(luoi)
    m = len(luoi[0])

    khoang_cach = [[-1] * m for _ in range(n)]
    hang_doi = deque()

    for hang, cot in cac_nguon:
        khoang_cach[hang][cot] = 0
        hang_doi.append((hang, cot))

    d_hang = [0, 0, 1, -1]
    d_cot = [1, -1, 0, 0]

    while len(hang_doi) > 0:
        hang, cot = hang_doi.popleft()

        for huong in range(4):
            hang_moi = hang + d_hang[huong]
            cot_moi = cot + d_cot[huong]

            hop_le = (0 <= hang_moi < n) and (0 <= cot_moi < m)
            if hop_le and luoi[hang_moi][cot_moi] == 0 and khoang_cach[hang_moi][cot_moi] == -1:
                khoang_cach[hang_moi][cot_moi] = khoang_cach[hang][cot] + 1
                hang_doi.append((hang_moi, cot_moi))

    return khoang_cach


luoi = [
    [0, 0, 0, 0],
    [0, 0, 0, 0],
    [0, 0, 0, 0],
]

cac_nguon = [(0, 0), (2, 3)]

ket_qua = bfs_da_nguon(luoi, cac_nguon)
for hang in ket_qua:
    print(hang)
```

**Bài tập minh hoạ:** Cài đặt DFS **không dùng đệ quy** (dùng stack tường minh — Chương 3), để tránh lỗi tràn stack khi đồ thị có dạng chuỗi dài (n > 10^5).

**Lời giải:**

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> dfsKhongDeQuy(vector<vector<int>> danhSachKe, int dinhXuatPhat, int n) {
    vector<bool> daTham(n, false);
    vector<int> thuTuTham;
    stack<int> nganXep;

    nganXep.push(dinhXuatPhat);

    while (!nganXep.empty()) {
        int dinhHienTai = nganXep.top();
        nganXep.pop();

        if (daTham[dinhHienTai] == true) {
            continue; // đã thăm rồi (có thể bị đẩy vào stack nhiều lần), bỏ qua
        }

        daTham[dinhHienTai] = true;
        thuTuTham.push_back(dinhHienTai);

        // Đẩy các đỉnh kề CHƯA THĂM vào stack (thứ tự duyệt DFS-lặp có thể khác DFS-đệ quy)
        for (int i = 0; i < (int)danhSachKe[dinhHienTai].size(); i++) {
            int dinhKe = danhSachKe[dinhHienTai][i];
            if (daTham[dinhKe] == false) {
                nganXep.push(dinhKe);
            }
        }
    }

    return thuTuTham;
}

int main() {
    int n = 5;
    vector<vector<int>> danhSachKe(n);
    vector<pair<int,int>> canh = {{0,1},{0,2},{1,3},{2,3},{3,4}};
    for (int i = 0; i < (int)canh.size(); i++) {
        int u = canh[i].first, v = canh[i].second;
        danhSachKe[u].push_back(v);
        danhSachKe[v].push_back(u);
    }

    vector<int> ketQua = dfsKhongDeQuy(danhSachKe, 0, n);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def dfs_khong_de_quy(danh_sach_ke, dinh_xuat_phat, n):
    da_tham = [False] * n
    thu_tu_tham = []
    ngan_xep = [dinh_xuat_phat]

    while len(ngan_xep) > 0:
        dinh_hien_tai = ngan_xep.pop()

        if da_tham[dinh_hien_tai] == True:
            continue

        da_tham[dinh_hien_tai] = True
        thu_tu_tham.append(dinh_hien_tai)

        for dinh_ke in danh_sach_ke[dinh_hien_tai]:
            if da_tham[dinh_ke] == False:
                ngan_xep.append(dinh_ke)

    return thu_tu_tham


n = 5
danh_sach_ke = [[] for _ in range(n)]
canh = [(0, 1), (0, 2), (1, 3), (2, 3), (3, 4)]
for u, v in canh:
    danh_sach_ke[u].append(v)
    danh_sach_ke[v].append(u)

ket_qua = dfs_khong_de_quy(danh_sach_ke, 0, n)
print(ket_qua)
```

---

## 11.3. Kiểm tra chu trình và tô màu 2 phía (Bipartite Check)

**Nêu bài toán:** Cho một đồ thị có hướng, hãy kiểm tra xem đồ thị đó có tồn tại **chu trình** (đường đi khép kín, xuất phát và quay lại đúng đỉnh ban đầu) hay không.

**Phân tích vấn đề:** Việc kiểm tra chu trình rất quan trọng trong thực tế — ví dụ khi các công việc có ràng buộc phụ thuộc lẫn nhau (công việc A phải làm trước công việc B), nếu tồn tại chu trình phụ thuộc, không thể sắp xếp thứ tự thực hiện hợp lý (dẫn tới mục 11.4 — sắp xếp tô-pô, chỉ thực hiện được khi đồ thị **không** có chu trình).

**Giải pháp đơn thuần:** Với đồ thị có hướng, thử liệt kê tất cả các đường đi có thể có từ mỗi đỉnh, kiểm tra xem có đường nào quay lại chính nó không — độ phức tạp có thể rất lớn nếu làm không khéo léo.

**Cách tiếp cận đúng — DFS với 3 trạng thái màu:** Gán cho mỗi đỉnh 1 trong 3 trạng thái: **trắng** (chưa thăm), **xám** (đang trong quá trình xử lý — tức đang nằm trên "đường đi hiện tại" của DFS), **đen** (đã xử lý xong hoàn toàn, không còn liên quan). Nếu trong quá trình DFS, ta gặp một cạnh dẫn tới đỉnh đang có màu **xám**, nghĩa là đỉnh đó nằm trên đường đi hiện tại — đây chính là dấu hiệu của một chu trình.

### Minh hoạ lời giải chi tiết

Với đồ thị có hướng: 0→1, 1→2, 2→0 (một chu trình), bắt đầu DFS từ đỉnh 0:

| Bước | Đỉnh đang xét | Trạng thái trước | Hành động | Trạng thái sau |
|---|---|---|---|---|
| 1 | 0 | trắng | Bắt đầu xử lý 0 | xám |
| 2 | 1 (kề với 0) | trắng | Bắt đầu xử lý 1 | xám |
| 3 | 2 (kề với 1) | trắng | Bắt đầu xử lý 2 | xám |
| 4 | 0 (kề với 2) | **xám** | Phát hiện đỉnh 0 đang xám -> **CÓ CHU TRÌNH** | (dừng, báo có chu trình) |

Nếu không có cạnh 2→0 (không có chu trình), sau khi xử lý xong đỉnh 2 (không còn đỉnh kề nào chưa xử lý xong), đỉnh 2 chuyển sang màu **đen**, rồi quay lui về đỉnh 1, xử lý xong chuyển đen, quay lui về đỉnh 0, xử lý xong chuyển đen — không phát hiện chu trình nào.

### Code khung mẫu (Kiểm tra chu trình trên đồ thị có hướng)

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKe;
vector<int> trangThai; // 0 = trắng, 1 = xám, 2 = đen

bool dfsKiemTraChuTrinh(int u) {
    trangThai[u] = 1; // chuyển sang xám (đang xử lý)

    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int v = danhSachKe[u][i];

        if (trangThai[v] == 1) {
            return true; // gặp đỉnh đang xám -> có chu trình
        }

        if (trangThai[v] == 0) {
            bool coChuTrinhTrongNhanhCon = dfsKiemTraChuTrinh(v);
            if (coChuTrinhTrongNhanhCon == true) {
                return true;
            }
        }
        // Nếu trangThai[v] == 2 (đen), bỏ qua - đã xử lý xong, chắc chắn không tạo chu trình mới
    }

    trangThai[u] = 2; // xử lý xong hoàn toàn, chuyển sang đen
    return false;
}

bool doThiCoChuTrinh(int n) {
    trangThai.assign(n, 0);

    for (int i = 0; i < n; i++) {
        if (trangThai[i] == 0) {
            if (dfsKiemTraChuTrinh(i) == true) {
                return true;
            }
        }
    }

    return false;
}

int main() {
    int n = 3;
    danhSachKe.assign(n, {});
    danhSachKe[0].push_back(1);
    danhSachKe[1].push_back(2);
    danhSachKe[2].push_back(0); // tạo chu trình 0->1->2->0

    cout << (doThiCoChuTrinh(n) ? "Co chu trinh" : "Khong co chu trinh") << endl;

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke = []
trang_thai = []


def dfs_kiem_tra_chu_trinh(u):
    trang_thai[u] = 1

    for v in danh_sach_ke[u]:
        if trang_thai[v] == 1:
            return True

        if trang_thai[v] == 0:
            co_chu_trinh_trong_nhanh_con = dfs_kiem_tra_chu_trinh(v)
            if co_chu_trinh_trong_nhanh_con == True:
                return True

    trang_thai[u] = 2
    return False


def do_thi_co_chu_trinh(n):
    global trang_thai
    trang_thai = [0] * n

    for i in range(n):
        if trang_thai[i] == 0:
            if dfs_kiem_tra_chu_trinh(i) == True:
                return True

    return False


n = 3
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0].append(1)
danh_sach_ke[1].append(2)
danh_sach_ke[2].append(0)

print("Co chu trinh" if do_thi_co_chu_trinh(n) else "Khong co chu trinh")
```

### Ví dụ 1 (Dễ) — Kiểm tra chu trình trên đồ thị vô hướng

**Phân tích:** Với đồ thị **vô hướng**, cách kiểm tra chu trình đơn giản hơn — chỉ cần dùng DFS với 2 trạng thái (thăm/chưa thăm), và **luôn nhớ đỉnh cha** (đỉnh vừa đi tới đỉnh hiện tại) để tránh nhầm cạnh "quay lại cha" (là bình thường, vì đồ thị vô hướng) với chu trình thực sự.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKeVoHuong;
vector<bool> daThamVoHuong;

bool dfsKiemTraChuTrinhVoHuong(int u, int cha) {
    daThamVoHuong[u] = true;

    for (int i = 0; i < (int)danhSachKeVoHuong[u].size(); i++) {
        int v = danhSachKeVoHuong[u][i];

        if (v == cha) {
            continue; // bỏ qua cạnh quay lại cha - đây là bình thường trong đồ thị vô hướng
        }

        if (daThamVoHuong[v] == true) {
            return true; // gặp đỉnh đã thăm (không phải cha) -> có chu trình
        }

        if (dfsKiemTraChuTrinhVoHuong(v, u) == true) {
            return true;
        }
    }

    return false;
}

int main() {
    int n = 4;
    danhSachKeVoHuong.assign(n, {});
    daThamVoHuong.assign(n, false);

    vector<pair<int,int>> canh = {{0,1},{1,2},{2,0}}; // tạo chu trình
    for (int i = 0; i < (int)canh.size(); i++) {
        int u = canh[i].first, v = canh[i].second;
        danhSachKeVoHuong[u].push_back(v);
        danhSachKeVoHuong[v].push_back(u);
    }

    cout << (dfsKiemTraChuTrinhVoHuong(0, -1) ? "Co chu trinh" : "Khong co chu trinh") << endl;

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke_vo_huong = []
da_tham_vo_huong = []


def dfs_kiem_tra_chu_trinh_vo_huong(u, cha):
    da_tham_vo_huong[u] = True

    for v in danh_sach_ke_vo_huong[u]:
        if v == cha:
            continue

        if da_tham_vo_huong[v] == True:
            return True

        if dfs_kiem_tra_chu_trinh_vo_huong(v, u) == True:
            return True

    return False


n = 4
danh_sach_ke_vo_huong = [[] for _ in range(n)]
da_tham_vo_huong = [False] * n

canh = [(0, 1), (1, 2), (2, 0)]
for u, v in canh:
    danh_sach_ke_vo_huong[u].append(v)
    danh_sach_ke_vo_huong[v].append(u)

print("Co chu trinh" if dfs_kiem_tra_chu_trinh_vo_huong(0, -1) else "Khong co chu trinh")
```

### Ví dụ 2 (Trung bình) — Kiểm tra đồ thị 2 phía (Bipartite Graph)

Bài toán: một đồ thị được gọi là "2 phía" (bipartite) nếu có thể tô màu tất cả các đỉnh bằng 2 màu, sao cho 2 đỉnh kề nhau luôn khác màu. Hãy kiểm tra một đồ thị vô hướng cho trước có phải đồ thị 2 phía hay không.

**Phân tích:** Dùng DFS (hoặc BFS) để tô màu — bắt đầu tô đỉnh xuất phát bằng màu 0, mỗi đỉnh kề được tô màu **ngược lại** với đỉnh hiện tại. Nếu trong quá trình tô, gặp một đỉnh đã có màu nhưng **trùng** với màu dự kiến gán (thay vì ngược lại), đồ thị không phải 2 phía.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKeBipartite;
vector<int> mauCuaDinh; // -1 = chưa tô, 0 hoặc 1 = 2 màu

bool dfsToMauBipartite(int u, int mauHienTai) {
    mauCuaDinh[u] = mauHienTai;

    for (int i = 0; i < (int)danhSachKeBipartite[u].size(); i++) {
        int v = danhSachKeBipartite[u][i];

        if (mauCuaDinh[v] == -1) {
            bool ketQua = dfsToMauBipartite(v, 1 - mauHienTai); // màu ngược lại
            if (ketQua == false) {
                return false;
            }
        } else if (mauCuaDinh[v] == mauHienTai) {
            return false; // 2 đỉnh kề nhau cùng màu -> không phải bipartite
        }
    }

    return true;
}

bool kiemTraBipartite(int n) {
    mauCuaDinh.assign(n, -1);

    for (int i = 0; i < n; i++) {
        if (mauCuaDinh[i] == -1) {
            if (dfsToMauBipartite(i, 0) == false) {
                return false;
            }
        }
    }

    return true;
}

int main() {
    int n = 4;
    danhSachKeBipartite.assign(n, {});

    vector<pair<int,int>> canh = {{0,1},{1,2},{2,3},{3,0}}; // chu trình độ dài 4 - vẫn là bipartite
    for (int i = 0; i < (int)canh.size(); i++) {
        int u = canh[i].first, v = canh[i].second;
        danhSachKeBipartite[u].push_back(v);
        danhSachKeBipartite[v].push_back(u);
    }

    cout << (kiemTraBipartite(n) ? "La bipartite" : "Khong phai bipartite") << endl;

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke_bipartite = []
mau_cua_dinh = []


def dfs_to_mau_bipartite(u, mau_hien_tai):
    mau_cua_dinh[u] = mau_hien_tai

    for v in danh_sach_ke_bipartite[u]:
        if mau_cua_dinh[v] == -1:
            ket_qua = dfs_to_mau_bipartite(v, 1 - mau_hien_tai)
            if ket_qua == False:
                return False
        elif mau_cua_dinh[v] == mau_hien_tai:
            return False

    return True


def kiem_tra_bipartite(n):
    global mau_cua_dinh
    mau_cua_dinh = [-1] * n

    for i in range(n):
        if mau_cua_dinh[i] == -1:
            if dfs_to_mau_bipartite(i, 0) == False:
                return False

    return True


n = 4
danh_sach_ke_bipartite = [[] for _ in range(n)]

canh = [(0, 1), (1, 2), (2, 3), (3, 0)]
for u, v in canh:
    danh_sach_ke_bipartite[u].append(v)
    danh_sach_ke_bipartite[v].append(u)

print("La bipartite" if kiem_tra_bipartite(n) else "Khong phai bipartite")
```

> **Ghi chú toán học quan trọng:** một đồ thị vô hướng là bipartite khi và chỉ khi nó **không chứa chu trình có độ dài lẻ**. Đây là lý do chu trình độ dài 4 (như ví dụ trên) vẫn là bipartite, nhưng chu trình độ dài 3 (tam giác) sẽ **không bao giờ** là bipartite.

### Ví dụ 3 (Khó) — Phát hiện chu trình và in ra chu trình cụ thể

Bài toán: không chỉ kiểm tra có chu trình hay không, mà còn phải **in ra** dãy đỉnh tạo thành 1 chu trình cụ thể (nếu có) trên đồ thị có hướng.

**Phân tích:** Mở rộng từ code khung mẫu — khi phát hiện cạnh dẫn tới đỉnh đang xám, ta cần lưu lại **đường đi hiện tại** (từ gốc DFS tới đỉnh đó) để trích ra đúng đoạn tạo thành chu trình. Dùng một mảng `duongDiHienTai` (hoặc `truocDo[]` lưu đỉnh cha trong cây DFS) để truy vết ngược lại.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKeInChuTrinh;
vector<int> trangThaiInChuTrinh;
vector<int> duongDiHienTai;
vector<int> chuTrinhTimDuoc;

bool dfsTimChuTrinh(int u) {
    trangThaiInChuTrinh[u] = 1;
    duongDiHienTai.push_back(u);

    for (int i = 0; i < (int)danhSachKeInChuTrinh[u].size(); i++) {
        int v = danhSachKeInChuTrinh[u][i];

        if (trangThaiInChuTrinh[v] == 1) {
            // Tìm vị trí của v trong duongDiHienTai để trích đoạn chu trình
            for (int j = 0; j < (int)duongDiHienTai.size(); j++) {
                if (duongDiHienTai[j] == v) {
                    for (int k = j; k < (int)duongDiHienTai.size(); k++) {
                        chuTrinhTimDuoc.push_back(duongDiHienTai[k]);
                    }
                    break;
                }
            }
            return true;
        }

        if (trangThaiInChuTrinh[v] == 0) {
            if (dfsTimChuTrinh(v) == true) {
                return true;
            }
        }
    }

    trangThaiInChuTrinh[u] = 2;
    duongDiHienTai.pop_back(); // quay lui, bỏ khỏi đường đi hiện tại
    return false;
}

int main() {
    int n = 4;
    danhSachKeInChuTrinh.assign(n, {});
    trangThaiInChuTrinh.assign(n, 0);

    danhSachKeInChuTrinh[0].push_back(1);
    danhSachKeInChuTrinh[1].push_back(2);
    danhSachKeInChuTrinh[2].push_back(3);
    danhSachKeInChuTrinh[3].push_back(1); // chu trình 1->2->3->1

    for (int i = 0; i < n; i++) {
        if (trangThaiInChuTrinh[i] == 0) {
            if (dfsTimChuTrinh(i) == true) {
                break;
            }
        }
    }

    cout << "Chu trinh tim duoc: ";
    for (int i = 0; i < (int)chuTrinhTimDuoc.size(); i++) {
        cout << chuTrinhTimDuoc[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke_in_chu_trinh = []
trang_thai_in_chu_trinh = []
duong_di_hien_tai = []
chu_trinh_tim_duoc = []


def dfs_tim_chu_trinh(u):
    trang_thai_in_chu_trinh[u] = 1
    duong_di_hien_tai.append(u)

    for v in danh_sach_ke_in_chu_trinh[u]:
        if trang_thai_in_chu_trinh[v] == 1:
            vi_tri = duong_di_hien_tai.index(v)
            for k in range(vi_tri, len(duong_di_hien_tai)):
                chu_trinh_tim_duoc.append(duong_di_hien_tai[k])
            return True

        if trang_thai_in_chu_trinh[v] == 0:
            if dfs_tim_chu_trinh(v) == True:
                return True

    trang_thai_in_chu_trinh[u] = 2
    duong_di_hien_tai.pop()
    return False


n = 4
danh_sach_ke_in_chu_trinh = [[] for _ in range(n)]
trang_thai_in_chu_trinh = [0] * n

danh_sach_ke_in_chu_trinh[0].append(1)
danh_sach_ke_in_chu_trinh[1].append(2)
danh_sach_ke_in_chu_trinh[2].append(3)
danh_sach_ke_in_chu_trinh[3].append(1)

for i in range(n):
    if trang_thai_in_chu_trinh[i] == 0:
        if dfs_tim_chu_trinh(i) == True:
            break

print("Chu trinh tim duoc:", chu_trinh_tim_duoc)
```

**Bài tập minh hoạ:** Cho đồ thị 2 phía, hãy chia các đỉnh thành 2 nhóm (theo màu tô được), rồi in ra danh sách đỉnh của từng nhóm.

**Lời giải:**

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    // Sử dụng lại kết quả mauCuaDinh đã tô ở Ví dụ 2
    int n = 4;
    // (Giả sử đã chạy kiemTraBipartite(n) và có mauCuaDinh)
    vector<int> mauCuaDinh = {0, 1, 0, 1}; // ví dụ kết quả tô màu

    vector<int> nhom0, nhom1;
    for (int i = 0; i < n; i++) {
        if (mauCuaDinh[i] == 0) {
            nhom0.push_back(i);
        } else {
            nhom1.push_back(i);
        }
    }

    cout << "Nhom 0: ";
    for (int i = 0; i < (int)nhom0.size(); i++) cout << nhom0[i] << " ";
    cout << endl;

    cout << "Nhom 1: ";
    for (int i = 0; i < (int)nhom1.size(); i++) cout << nhom1[i] << " ";
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
n = 4
mau_cua_dinh = [0, 1, 0, 1]

nhom_0 = []
nhom_1 = []
for i in range(n):
    if mau_cua_dinh[i] == 0:
        nhom_0.append(i)
    else:
        nhom_1.append(i)

print("Nhom 0:", nhom_0)
print("Nhom 1:", nhom_1)
```

---

## 11.4. Sắp xếp Tô-pô (Topological Sort)

**Nêu bài toán:** Có n công việc cần thực hiện, một số công việc phải hoàn thành **trước** các công việc khác (ví dụ: phải "mặc áo" trước khi "mặc áo khoác"). Hãy tìm một thứ tự thực hiện tất cả n công việc sao cho thoả mãn toàn bộ các ràng buộc phụ thuộc.

**Phân tích vấn đề:** Đây chính là bài toán sắp xếp tô-pô — biểu diễn mỗi công việc là 1 đỉnh, mỗi ràng buộc "A phải trước B" là 1 cạnh có hướng từ A tới B. Sắp xếp tô-pô **chỉ tồn tại** khi đồ thị là **DAG** (Directed Acyclic Graph — đồ thị có hướng không chu trình) — nếu có chu trình, không thể tìm được thứ tự hợp lệ (vì sẽ có ít nhất 2 công việc phải làm trước nhau một cách mâu thuẫn).

**Giải pháp 1 — DFS + ngăn xếp:** Chạy DFS, khi một đỉnh **xử lý xong hoàn toàn** (không còn đỉnh kề nào chưa xử lý), đẩy nó vào một ngăn xếp. Sau khi DFS xong toàn bộ đồ thị, lấy các đỉnh ra khỏi ngăn xếp theo thứ tự — đây chính là thứ tự tô-pô hợp lệ (đảo ngược thứ tự "hoàn thành" của DFS).

**Giải pháp 2 — Thuật toán Kahn (dùng BFS + bậc vào):** Tính bậc vào của mọi đỉnh. Đưa vào hàng đợi tất cả các đỉnh có bậc vào bằng 0 (không phụ thuộc vào công việc nào khác). Lấy dần các đỉnh ra khỏi hàng đợi, mỗi lần lấy 1 đỉnh, "xoá" các cạnh xuất phát từ nó (giảm bậc vào của các đỉnh kề); nếu bậc vào của 1 đỉnh kề giảm về 0, đưa nó vào hàng đợi.

### Minh hoạ lời giải chi tiết

Với đồ thị: 0→1, 0→2, 1→3, 2→3 (0 phải làm trước 1 và 2; cả 1 và 2 đều phải làm trước 3):

**Cách 1 — Kahn's Algorithm:**

| Bước | Bậc vào hiện tại | Hàng đợi | Đỉnh lấy ra | Thứ tự tô-pô |
|---|---|---|---|---|
| Khởi tạo | 0:[0], 1:[1], 2:[1], 3:[2] | [0] | - | [] |
| 1 | (giảm bậc vào của 1,2 sau khi lấy 0) 1:[0], 2:[0] | [1, 2] | 0 | [0] |
| 2 | (giảm bậc vào của 3 sau khi lấy 1) 3:[1] | [2] | 1 | [0, 1] |
| 3 | (giảm bậc vào của 3 sau khi lấy 2) 3:[0] | [3] | 2 | [0, 1, 2] |
| 4 | - | [] | 3 | [0, 1, 2, 3] |

Kết quả: thứ tự tô-pô hợp lệ là `[0, 1, 2, 3]`.

### Code khung mẫu (Thuật toán Kahn — BFS)

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> sapXepTopo(vector<vector<int>> danhSachKe, int n) {
    vector<int> bacVao(n, 0);

    for (int u = 0; u < n; u++) {
        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            int v = danhSachKe[u][i];
            bacVao[v] = bacVao[v] + 1;
        }
    }

    queue<int> hangDoi;
    for (int i = 0; i < n; i++) {
        if (bacVao[i] == 0) {
            hangDoi.push(i);
        }
    }

    vector<int> thuTuTopo;

    while (!hangDoi.empty()) {
        int dinhHienTai = hangDoi.front();
        hangDoi.pop();
        thuTuTopo.push_back(dinhHienTai);

        for (int i = 0; i < (int)danhSachKe[dinhHienTai].size(); i++) {
            int dinhKe = danhSachKe[dinhHienTai][i];
            bacVao[dinhKe] = bacVao[dinhKe] - 1;
            if (bacVao[dinhKe] == 0) {
                hangDoi.push(dinhKe);
            }
        }
    }

    if ((int)thuTuTopo.size() != n) {
        return {}; // đồ thị có chu trình, không tồn tại thứ tự tô-pô
    }

    return thuTuTopo;
}

int main() {
    int n = 4;
    vector<vector<int>> danhSachKe(n);
    danhSachKe[0] = {1, 2};
    danhSachKe[1] = {3};
    danhSachKe[2] = {3};

    vector<int> ketQua = sapXepTopo(danhSachKe, n);

    if (ketQua.empty()) {
        cout << "Do thi co chu trinh, khong sap xep duoc" << endl;
    } else {
        cout << "Thu tu topo: ";
        for (int i = 0; i < (int)ketQua.size(); i++) {
            cout << ketQua[i] << " ";
        }
        cout << endl;
    }

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def sap_xep_topo(danh_sach_ke, n):
    bac_vao = [0] * n

    for u in range(n):
        for v in danh_sach_ke[u]:
            bac_vao[v] = bac_vao[v] + 1

    hang_doi = deque()
    for i in range(n):
        if bac_vao[i] == 0:
            hang_doi.append(i)

    thu_tu_topo = []

    while len(hang_doi) > 0:
        dinh_hien_tai = hang_doi.popleft()
        thu_tu_topo.append(dinh_hien_tai)

        for dinh_ke in danh_sach_ke[dinh_hien_tai]:
            bac_vao[dinh_ke] = bac_vao[dinh_ke] - 1
            if bac_vao[dinh_ke] == 0:
                hang_doi.append(dinh_ke)

    if len(thu_tu_topo) != n:
        return []

    return thu_tu_topo


n = 4
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0] = [1, 2]
danh_sach_ke[1] = [3]
danh_sach_ke[2] = [3]

ket_qua = sap_xep_topo(danh_sach_ke, n)

if len(ket_qua) == 0:
    print("Do thi co chu trinh, khong sap xep duoc")
else:
    print("Thu tu topo:", ket_qua)
```

### Ví dụ 1 (Dễ) — Sắp xếp Tô-pô bằng DFS

**Phân tích:** Cài đặt thay thế cho thuật toán Kahn, dùng DFS + ngăn xếp như đã mô tả ở "Giải pháp 1".

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKeTopoDFS;
vector<bool> daThamTopoDFS;
stack<int> nganXepKetQua;

void dfsTopo(int u) {
    daThamTopoDFS[u] = true;

    for (int i = 0; i < (int)danhSachKeTopoDFS[u].size(); i++) {
        int v = danhSachKeTopoDFS[u][i];
        if (daThamTopoDFS[v] == false) {
            dfsTopo(v);
        }
    }

    nganXepKetQua.push(u); // đẩy vào ngăn xếp SAU KHI đã xử lý xong hoàn toàn đỉnh u
}

vector<int> sapXepTopoBangDFS(int n) {
    daThamTopoDFS.assign(n, false);

    for (int i = 0; i < n; i++) {
        if (daThamTopoDFS[i] == false) {
            dfsTopo(i);
        }
    }

    vector<int> ketQua;
    while (!nganXepKetQua.empty()) {
        ketQua.push_back(nganXepKetQua.top());
        nganXepKetQua.pop();
    }

    return ketQua;
}

int main() {
    int n = 4;
    danhSachKeTopoDFS.assign(n, {});
    danhSachKeTopoDFS[0] = {1, 2};
    danhSachKeTopoDFS[1] = {3};
    danhSachKeTopoDFS[2] = {3};

    vector<int> ketQua = sapXepTopoBangDFS(n);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke_topo_dfs = []
da_tham_topo_dfs = []
ngan_xep_ket_qua = []


def dfs_topo(u):
    da_tham_topo_dfs[u] = True

    for v in danh_sach_ke_topo_dfs[u]:
        if da_tham_topo_dfs[v] == False:
            dfs_topo(v)

    ngan_xep_ket_qua.append(u)


def sap_xep_topo_bang_dfs(n):
    global da_tham_topo_dfs, ngan_xep_ket_qua
    da_tham_topo_dfs = [False] * n
    ngan_xep_ket_qua = []

    for i in range(n):
        if da_tham_topo_dfs[i] == False:
            dfs_topo(i)

    ket_qua = []
    while len(ngan_xep_ket_qua) > 0:
        ket_qua.append(ngan_xep_ket_qua.pop())

    return ket_qua


n = 4
danh_sach_ke_topo_dfs = [[] for _ in range(n)]
danh_sach_ke_topo_dfs[0] = [1, 2]
danh_sach_ke_topo_dfs[1] = [3]
danh_sach_ke_topo_dfs[2] = [3]

ket_qua = sap_xep_topo_bang_dfs(n)
print(ket_qua)
```

### Ví dụ 2 (Trung bình) — Kiểm tra tính duy nhất của thứ tự Tô-pô

Bài toán: cho một DAG, hãy kiểm tra xem thứ tự tô-pô của nó có **duy nhất** hay không (chỉ có đúng 1 cách sắp xếp hợp lệ).

**Phân tích:** Thứ tự tô-pô là duy nhất khi và chỉ khi **tại mọi thời điểm** trong thuật toán Kahn, hàng đợi luôn chỉ chứa **đúng 1 đỉnh** (không có 2 đỉnh nào "cùng sẵn sàng" tại một thời điểm — nếu có, ta có thể hoán đổi thứ tự giữa chúng, tạo ra 2 cách sắp xếp khác nhau).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

bool thuTuTopoDuyNhat(vector<vector<int>> danhSachKe, int n) {
    vector<int> bacVao(n, 0);
    for (int u = 0; u < n; u++) {
        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            bacVao[danhSachKe[u][i]] = bacVao[danhSachKe[u][i]] + 1;
        }
    }

    queue<int> hangDoi;
    for (int i = 0; i < n; i++) {
        if (bacVao[i] == 0) hangDoi.push(i);
    }

    int soLuongDaXuLy = 0;

    while (!hangDoi.empty()) {
        if (hangDoi.size() > 1) {
            return false; // có nhiều hơn 1 lựa chọn tại thời điểm này -> không duy nhất
        }

        int dinhHienTai = hangDoi.front();
        hangDoi.pop();
        soLuongDaXuLy = soLuongDaXuLy + 1;

        for (int i = 0; i < (int)danhSachKe[dinhHienTai].size(); i++) {
            int v = danhSachKe[dinhHienTai][i];
            bacVao[v] = bacVao[v] - 1;
            if (bacVao[v] == 0) hangDoi.push(v);
        }
    }

    return soLuongDaXuLy == n; // đảm bảo không có chu trình
}

int main() {
    int n = 3;
    vector<vector<int>> danhSachKe(n);
    danhSachKe[0] = {1};
    danhSachKe[1] = {2};

    cout << (thuTuTopoDuyNhat(danhSachKe, n) ? "Duy nhat" : "Khong duy nhat") << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def thu_tu_topo_duy_nhat(danh_sach_ke, n):
    bac_vao = [0] * n
    for u in range(n):
        for v in danh_sach_ke[u]:
            bac_vao[v] = bac_vao[v] + 1

    hang_doi = deque()
    for i in range(n):
        if bac_vao[i] == 0:
            hang_doi.append(i)

    so_luong_da_xu_ly = 0

    while len(hang_doi) > 0:
        if len(hang_doi) > 1:
            return False

        dinh_hien_tai = hang_doi.popleft()
        so_luong_da_xu_ly = so_luong_da_xu_ly + 1

        for v in danh_sach_ke[dinh_hien_tai]:
            bac_vao[v] = bac_vao[v] - 1
            if bac_vao[v] == 0:
                hang_doi.append(v)

    return so_luong_da_xu_ly == n


n = 3
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0] = [1]
danh_sach_ke[1] = [2]

print("Duy nhat" if thu_tu_topo_duy_nhat(danh_sach_ke, n) else "Khong duy nhat")
```

### Ví dụ 3 (Khó) — Tìm đường đi dài nhất trong DAG bằng Sắp xếp Tô-pô

Bài toán: cho một DAG có trọng số trên cạnh, hãy tìm độ dài đường đi **dài nhất** xuất phát từ 1 đỉnh cho trước (khác với đường đi ngắn nhất sẽ học ở Chương 12).

**Phân tích:** Trên DAG, bài toán đường đi dài nhất/ngắn nhất có thể giải bằng quy hoạch động **theo đúng thứ tự tô-pô** — vì thứ tự tô-pô đảm bảo khi xử lý đỉnh `u`, mọi đỉnh có thể "đi tới" `u` (có cạnh hướng vào `u`) đã được xử lý xong trước đó.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<long long> timDuongDiDaiNhatTrenDAG(vector<vector<pair<int,int>>> danhSachKe, int n, int dinhXuatPhat) {
    // Bước 1: sắp xếp tô-pô (dùng thuật toán Kahn)
    vector<int> bacVao(n, 0);
    for (int u = 0; u < n; u++) {
        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            bacVao[danhSachKe[u][i].first] = bacVao[danhSachKe[u][i].first] + 1;
        }
    }

    queue<int> hangDoi;
    for (int i = 0; i < n; i++) {
        if (bacVao[i] == 0) hangDoi.push(i);
    }

    vector<int> thuTuTopo;
    while (!hangDoi.empty()) {
        int u = hangDoi.front(); hangDoi.pop();
        thuTuTopo.push_back(u);
        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            int v = danhSachKe[u][i].first;
            bacVao[v] = bacVao[v] - 1;
            if (bacVao[v] == 0) hangDoi.push(v);
        }
    }

    // Bước 2: DP theo đúng thứ tự tô-pô
    vector<long long> duongDiDaiNhat(n, LLONG_MIN);
    duongDiDaiNhat[dinhXuatPhat] = 0;

    for (int i = 0; i < (int)thuTuTopo.size(); i++) {
        int u = thuTuTopo[i];
        if (duongDiDaiNhat[u] == LLONG_MIN) {
            continue; // đỉnh này chưa tới được từ dinhXuatPhat
        }

        for (int j = 0; j < (int)danhSachKe[u].size(); j++) {
            int v = danhSachKe[u][j].first;
            int trongSo = danhSachKe[u][j].second;

            long long ungCu = duongDiDaiNhat[u] + trongSo;
            if (ungCu > duongDiDaiNhat[v]) {
                duongDiDaiNhat[v] = ungCu;
            }
        }
    }

    return duongDiDaiNhat;
}

int main() {
    int n = 5;
    vector<vector<pair<int,int>>> danhSachKe(n);
    danhSachKe[0].push_back({1, 3});
    danhSachKe[0].push_back({2, 2});
    danhSachKe[1].push_back({3, 4});
    danhSachKe[2].push_back({3, 1});
    danhSachKe[3].push_back({4, 5});

    vector<long long> ketQua = timDuongDiDaiNhatTrenDAG(danhSachKe, n, 0);
    for (int i = 0; i < n; i++) {
        cout << "Dinh " << i << ": " << ketQua[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def tim_duong_di_dai_nhat_tren_dag(danh_sach_ke, n, dinh_xuat_phat):
    bac_vao = [0] * n
    for u in range(n):
        for v, trong_so in danh_sach_ke[u]:
            bac_vao[v] = bac_vao[v] + 1

    hang_doi = deque()
    for i in range(n):
        if bac_vao[i] == 0:
            hang_doi.append(i)

    thu_tu_topo = []
    while len(hang_doi) > 0:
        u = hang_doi.popleft()
        thu_tu_topo.append(u)
        for v, trong_so in danh_sach_ke[u]:
            bac_vao[v] = bac_vao[v] - 1
            if bac_vao[v] == 0:
                hang_doi.append(v)

    duong_di_dai_nhat = [float('-inf')] * n
    duong_di_dai_nhat[dinh_xuat_phat] = 0

    for u in thu_tu_topo:
        if duong_di_dai_nhat[u] == float('-inf'):
            continue

        for v, trong_so in danh_sach_ke[u]:
            ung_cu = duong_di_dai_nhat[u] + trong_so
            if ung_cu > duong_di_dai_nhat[v]:
                duong_di_dai_nhat[v] = ung_cu

    return duong_di_dai_nhat


n = 5
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0].append((1, 3))
danh_sach_ke[0].append((2, 2))
danh_sach_ke[1].append((3, 4))
danh_sach_ke[2].append((3, 1))
danh_sach_ke[3].append((4, 5))

ket_qua = tim_duong_di_dai_nhat_tren_dag(danh_sach_ke, n, 0)
for i in range(n):
    print("Dinh", i, ":", ket_qua[i])
```

**Bài tập minh hoạ:** Cho danh sách các môn học và ràng buộc "môn A là tiên quyết của môn B", hãy in ra thứ tự học các môn hợp lệ, hoặc báo "Không thể hoàn thành" nếu tồn tại ràng buộc vòng tròn (chu trình).

**Lời giải:** Đây chính là ứng dụng trực tiếp của thuật toán Kahn (Code khung mẫu) — kiểm tra `thuTuTopo.size() != n` để phát hiện chu trình.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int soMon = 4;
    vector<vector<int>> danhSachKe(soMon);

    // Môn 0 là tiên quyết của môn 1; môn 1 là tiên quyết của môn 2, 3
    danhSachKe[0] = {1};
    danhSachKe[1] = {2, 3};

    vector<int> bacVao(soMon, 0);
    for (int u = 0; u < soMon; u++) {
        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            bacVao[danhSachKe[u][i]] = bacVao[danhSachKe[u][i]] + 1;
        }
    }

    queue<int> hangDoi;
    for (int i = 0; i < soMon; i++) {
        if (bacVao[i] == 0) hangDoi.push(i);
    }

    vector<int> thuTuHoc;
    while (!hangDoi.empty()) {
        int u = hangDoi.front(); hangDoi.pop();
        thuTuHoc.push_back(u);
        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            int v = danhSachKe[u][i];
            bacVao[v] = bacVao[v] - 1;
            if (bacVao[v] == 0) hangDoi.push(v);
        }
    }

    if ((int)thuTuHoc.size() != soMon) {
        cout << "Khong the hoan thanh" << endl;
    } else {
        cout << "Thu tu hoc: ";
        for (int i = 0; i < (int)thuTuHoc.size(); i++) {
            cout << thuTuHoc[i] << " ";
        }
        cout << endl;
    }

    return 0;
}
```

#### PYTHON
```python
from collections import deque

so_mon = 4
danh_sach_ke = [[] for _ in range(so_mon)]
danh_sach_ke[0] = [1]
danh_sach_ke[1] = [2, 3]

bac_vao = [0] * so_mon
for u in range(so_mon):
    for v in danh_sach_ke[u]:
        bac_vao[v] = bac_vao[v] + 1

hang_doi = deque()
for i in range(so_mon):
    if bac_vao[i] == 0:
        hang_doi.append(i)

thu_tu_hoc = []
while len(hang_doi) > 0:
    u = hang_doi.popleft()
    thu_tu_hoc.append(u)
    for v in danh_sach_ke[u]:
        bac_vao[v] = bac_vao[v] - 1
        if bac_vao[v] == 0:
            hang_doi.append(v)

if len(thu_tu_hoc) != so_mon:
    print("Khong the hoan thanh")
else:
    print("Thu tu hoc:", thu_tu_hoc)
```

---

## Tổng kết Chương 11

- **Biểu diễn đồ thị:** Danh sách kề là lựa chọn mặc định cho đa số bài toán (bộ nhớ O(n+m)); Ma trận kề chỉ nên dùng khi n nhỏ và cần kiểm tra cạnh cực nhanh O(1).
- **DFS:** đi sâu trước, cài đặt tự nhiên bằng đệ quy, phù hợp cho kiểm tra liên thông, tìm chu trình, sắp xếp tô-pô.
- **BFS:** đi theo từng lớp, cài đặt bằng Queue, **bắt buộc** dùng khi cần tìm đường đi ngắn nhất theo số cạnh trên đồ thị không trọng số — kỹ thuật multi-source BFS (nhiều nguồn cùng lúc) là một mở rộng quan trọng cần nhớ.
- **Kiểm tra chu trình:** dùng 3 trạng thái màu (trắng/xám/đen) trên đồ thị có hướng; dùng kỹ thuật "nhớ đỉnh cha" trên đồ thị vô hướng.
- **Kiểm tra Bipartite:** tô màu 2 màu bằng DFS/BFS, phát hiện mâu thuẫn khi 2 đỉnh kề nhau cùng màu — về mặt lý thuyết, tương đương với "không có chu trình độ dài lẻ".
- **Sắp xếp Tô-pô:** chỉ tồn tại trên DAG (đồ thị có hướng không chu trình) — có 2 cách cài đặt (DFS + ngăn xếp, hoặc Kahn's Algorithm dùng BFS + bậc vào), và là nền tảng để giải bài toán đường đi dài nhất/ngắn nhất trên DAG bằng DP.
- **Kỹ năng cốt lõi cần luyện:** trước khi code bất kỳ bài toán đồ thị nào, luôn xác định rõ: đồ thị có hướng hay vô hướng? có trọng số hay không? cần tìm đường đi ngắn nhất theo số cạnh (BFS) hay theo trọng số (Chương 12)? có khả năng chứa chu trình không (ảnh hưởng tới việc có dùng được sắp xếp tô-pô hay không)?

---

## Bài tập cuối chương — Chương 11

### PHẦN A: ĐỀ BÀI (16 bài, không kèm lời giải)

**Bài 1 — Counting Rooms (CSES 1192) — Dễ**
Cho một lưới ô vuông biểu diễn bản đồ căn phòng (ô trống và tường), hãy đếm số lượng phòng riêng biệt (thành phần liên thông) trong bản đồ.

**Bài 2 — Building Roads (CSES 1666) — Dễ**
Cho n thành phố và một số con đường sẵn có, hãy tìm số lượng con đường tối thiểu cần xây thêm để mọi thành phố đều liên thông với nhau, và in ra các con đường đó.

**Bài 3 — Message Route (CSES 1667) — Dễ**
Cho mạng lưới n máy tính, tìm đường đi ngắn nhất (theo số bước) từ máy tính 1 đến máy tính n, in ra đường đi cụ thể.

**Bài 4 — Number of Islands (LeetCode 200) — Dễ**
Cho một lưới ô vuông gồm '1' (đất) và '0' (nước), đếm số lượng đảo riêng biệt (các ô đất liền kề theo 4 hướng tạo thành 1 đảo).

**Bài 5 — Rotting Oranges (LeetCode 994) — Trung bình**
Cho lưới ô vuông với các trạng thái: ô trống, cam tươi, cam thối. Mỗi phút, cam thối làm thối các cam tươi liền kề (4 hướng). Tìm số phút tối thiểu để toàn bộ cam đều thối, hoặc -1 nếu không thể. (Gợi ý: Multi-source BFS, giống Ví dụ 3, mục 11.2.)

**Bài 6 — Labyrinth (CSES 1193) — Trung bình**
Giống Ví dụ 2, mục 11.2, nhưng yêu cầu in ra đường đi cụ thể (dãy các hướng di chuyển U/D/L/R) từ điểm A đến điểm B trong mê cung.

**Bài 7 — Round Trip (CSES 1669) — Trung bình**
Cho đồ thị vô hướng, tìm một chu trình bất kỳ (không lặp lại đỉnh trừ điểm đầu-cuối) và in ra các đỉnh của chu trình đó.

**Bài 8 — Course Schedule (LeetCode 207) — Trung bình**
Cho n môn học và danh sách các cặp môn tiên quyết, kiểm tra có thể hoàn thành tất cả các môn học hay không (tương đương kiểm tra đồ thị có chu trình hay không).

**Bài 9 — Course Schedule II (LeetCode 210) — Trung bình**
Giống Bài 8, nhưng yêu cầu in ra thứ tự học cụ thể nếu có thể hoàn thành.

**Bài 10 — Is Graph Bipartite? (LeetCode 785) — Trung bình**
Giống hệt Ví dụ 2, mục 11.3 đã trình bày (dùng để luyện tập lại).

**Bài 11 — Possible Bipartition (LeetCode 886) — Trung bình**
Cho n người và danh sách các cặp người "không ưa nhau" (không được ở cùng nhóm), kiểm tra có thể chia n người thành 2 nhóm thoả mãn ràng buộc hay không. (Gợi ý: đây chính là bài toán kiểm tra Bipartite với cách diễn giải khác.)

**Bài 12 — Word Ladder (LeetCode 127) — Khó**
Cho từ bắt đầu, từ kết thúc, và một danh sách từ điển, tìm độ dài đường biến đổi ngắn nhất từ từ bắt đầu tới từ kết thúc (mỗi bước chỉ được đổi 1 ký tự, kết quả mỗi bước phải là 1 từ hợp lệ trong từ điển). (Gợi ý: BFS trên đồ thị ẩn, mỗi từ là 1 đỉnh, cạnh nối 2 từ khác nhau đúng 1 ký tự.)

**Bài 13 — Longest Path in DAG (dạng phổ biến, mở rộng từ Ví dụ 3 mục 11.4) — Trung bình-Khó**
Giống hệt Ví dụ 3, mục 11.4 đã trình bày, nhưng yêu cầu tìm đường đi dài nhất **trong toàn bộ đồ thị** (không cố định đỉnh xuất phát) — cần thử tất cả các đỉnh làm điểm xuất phát, hoặc dùng 1 lượt DP duy nhất với giá trị khởi tạo phù hợp cho mọi đỉnh.

**Bài 14 — Course Schedule IV (LeetCode 1462) — Khó**
Cho n môn học và các ràng buộc tiên quyết trực tiếp, với nhiều truy vấn hỏi "môn A có phải tiên quyết (trực tiếp hoặc gián tiếp) của môn B hay không?". (Gợi ý: với mỗi đỉnh, dùng BFS/DFS để tìm tập tất cả các đỉnh "phụ thuộc" vào nó, lưu vào bảng tra cứu.)

**Bài 15 — Minimum Height Trees (LeetCode 310) — Khó**
Cho một cây (đồ thị vô hướng liên thông không chu trình), tìm (các) đỉnh làm gốc sao cho chiều cao của cây là nhỏ nhất. (Gợi ý: kỹ thuật "lột vỏ dần" — liên tục loại bỏ các đỉnh lá cho tới khi còn lại 1 hoặc 2 đỉnh, dùng BFS nhiều lớp.)

**Bài 16 — Bài toán tổng hợp: kiểm tra lịch thi có xung đột (tự thiết kế, dạng đề HSG) — Khó**
Cho n môn thi và m cặp môn "không thể thi cùng ca" (do trùng sinh viên đăng ký), hãy xác định số ca thi tối thiểu cần thiết sao cho không có 2 môn xung khắc nào thi cùng ca — biết rằng dữ liệu đảm bảo đồ thị xung khắc là Bipartite. (Gợi ý: kết hợp kiểm tra Bipartite mục 11.3 với việc đếm số nhóm màu.)

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Counting Rooms</summary>

Giống hệt hàm `demThanhPhanLienThong`/`dem_thanh_phan_lien_thong` ở Ví dụ 1, mục 11.2, áp dụng trên đồ thị ẩn từ lưới ô vuông (dùng cách xây dựng đỉnh kề như Ví dụ 3, mục 11.1).
</details>

<details>
<summary>Lời giải Bài 2 — Building Roads</summary>

```cpp
// Dùng dfsDanhDauLienThong (Ví dụ 1, mục 11.2) để tìm các thành phần liên thông.
// Với mỗi thành phần (trừ thành phần đầu tiên), in ra 1 cạnh nối đại diện của thành phần đó
// với đại diện của thành phần đầu tiên.
vector<vector<int>> danhSachKe;
vector<bool> daTham;

void dfs(int u) {
    daTham[u] = true;
    for (int v : danhSachKe[u]) if (!daTham[v]) dfs(v);
}

int main() {
    int n; cin >> n;
    // ... đọc cạnh, xây dựng danhSachKe
    daTham.assign(n+1, false);
    vector<int> daiDien;
    for (int i = 1; i <= n; i++) {
        if (!daTham[i]) {
            dfs(i);
            daiDien.push_back(i);
        }
    }
    cout << daiDien.size() - 1 << "\n";
    for (int i = 1; i < (int)daiDien.size(); i++) {
        cout << daiDien[0] << " " << daiDien[i] << "\n";
    }
}
```
</details>

<details>
<summary>Lời giải Bài 3 — Message Route</summary>

Dùng BFS như code khung mẫu mục 11.2, đồng thời lưu mảng `truocDo[]` (đỉnh cha trong cây BFS) để truy vết đường đi từ đích ngược về nguồn.

#### C++
```cpp
vector<int> truocDo(n, -1);
// Trong lúc BFS, mỗi khi cập nhật khoangCach[v], gán truocDo[v] = dinhHienTai;
// Sau khi BFS xong, truy vết từ đích: while (dinh != -1) { ketQua.push_back(dinh); dinh = truocDo[dinh]; }
// rồi đảo ngược ketQua.
```
</details>

<details>
<summary>Lời giải Bài 4 — Number of Islands</summary>

Giống hệt Ví dụ 1, mục 11.2, áp dụng trên lưới với DFS/BFS 4 hướng như Ví dụ 3, mục 11.1.
</details>

<details>
<summary>Lời giải Bài 5 — Rotting Oranges</summary>

Giống hệt Ví dụ 3, mục 11.2 (Multi-source BFS) — các cam thối là các "nguồn" ban đầu, kết quả là giá trị lớn nhất trong bảng khoảng cách (bỏ qua các ô -1 không tới được, kiểm tra còn cam tươi nào không tới được không để trả về -1).
</details>

<details>
<summary>Lời giải Bài 6 — Labyrinth (in đường đi)</summary>

Kết hợp BFS (Ví dụ 2, mục 11.2) với mảng `huongDiDen[hang][cot]` lưu hướng di chuyển dẫn tới ô đó, rồi truy vết ngược từ đích về xuất phát để in ra chuỗi hướng đi (đảo ngược thứ tự sau khi truy vết).
</details>

<details>
<summary>Lời giải Bài 7 — Round Trip</summary>

Giống hệt Ví dụ 3, mục 11.3 (in ra chu trình cụ thể), áp dụng phiên bản vô hướng: khi DFS gặp đỉnh đã thăm (không phải cha trực tiếp), trích đoạn từ đỉnh đó tới hiện tại trong đường đi DFS làm chu trình.
</details>

<details>
<summary>Lời giải Bài 8 — Course Schedule</summary>

Áp dụng `doThiCoChuTrinh`/`do_thi_co_chu_trinh` (Code khung mẫu, mục 11.3) trên đồ thị có hướng biểu diễn ràng buộc tiên quyết — trả về `!doThiCoChuTrinh(n)`.
</details>

<details>
<summary>Lời giải Bài 9 — Course Schedule II</summary>

Áp dụng `sapXepTopo`/`sap_xep_topo` (Code khung mẫu, mục 11.4) — nếu kết quả rỗng (chu trình tồn tại), trả về mảng rỗng; ngược lại trả về đúng thứ tự tô-pô tìm được.
</details>

<details>
<summary>Lời giải Bài 10 — Is Graph Bipartite?</summary>

Giống hệt hàm `kiemTraBipartite`/`kiem_tra_bipartite` ở Ví dụ 2, mục 11.3.
</details>

<details>
<summary>Lời giải Bài 11 — Possible Bipartition</summary>

Xây dựng đồ thị với cạnh nối các cặp "không ưa nhau", rồi áp dụng `kiemTraBipartite`/`kiem_tra_bipartite` y hệt Bài 10 — nếu là bipartite, chia được thành 2 nhóm hợp lệ.
</details>

<details>
<summary>Lời giải Bài 12 — Word Ladder</summary>

```cpp
int ladderLength(string beginWord, string endWord, vector<string> &wordList) {
    unordered_set<string> tuDien(wordList.begin(), wordList.end());
    if (tuDien.count(endWord) == 0) return 0;

    queue<pair<string,int>> hangDoi;
    hangDoi.push({beginWord, 1});
    unordered_set<string> daTham;
    daTham.insert(beginWord);

    while (!hangDoi.empty()) {
        auto [tuHienTai, buoc] = hangDoi.front(); hangDoi.pop();
        if (tuHienTai == endWord) return buoc;

        for (int i = 0; i < (int)tuHienTai.size(); i++) {
            string tuMoi = tuHienTai;
            for (char c = 'a'; c <= 'z'; c++) {
                tuMoi[i] = c;
                if (tuDien.count(tuMoi) > 0 && daTham.count(tuMoi) == 0) {
                    daTham.insert(tuMoi);
                    hangDoi.push({tuMoi, buoc + 1});
                }
            }
        }
    }
    return 0;
}
```
</details>

<details>
<summary>Lời giải Bài 13 — Longest Path in DAG (toàn đồ thị)</summary>

Áp dụng khung `timDuongDiDaiNhatTrenDAG`/`tim_duong_di_dai_nhat_tren_dag` ở Ví dụ 3, mục 11.4, nhưng khởi tạo `duongDiDaiNhat[i] = 0` cho **mọi** đỉnh (thay vì chỉ đỉnh xuất phát), coi mỗi đỉnh đều có thể là điểm bắt đầu — kết quả cuối là giá trị lớn nhất trong toàn bộ mảng `duongDiDaiNhat`.
</details>

<details>
<summary>Lời giải Bài 14 — Course Schedule IV</summary>

```cpp
vector<bool> reachable(n); // tính riêng cho từng đỉnh xuất phát bằng BFS/DFS
vector<vector<bool>> ketQua(n, vector<bool>(n, false));

for (int i = 0; i < n; i++) {
    vector<bool> daTham(n, false);
    queue<int> hangDoi; hangDoi.push(i); daTham[i] = true;
    while (!hangDoi.empty()) {
        int u = hangDoi.front(); hangDoi.pop();
        for (int v : danhSachKe[u]) {
            if (!daTham[v]) { daTham[v] = true; ketQua[i][v] = true; hangDoi.push(v); }
        }
    }
}
// Với mỗi truy vấn (a, b): trả về ketQua[a][b]
```
</details>

<details>
<summary>Lời giải Bài 15 — Minimum Height Trees</summary>

**Ý tưởng "lột vỏ dần":** liên tục loại bỏ các đỉnh lá hiện tại (bậc = 1), cho tới khi còn lại 1 hoặc 2 đỉnh — đó chính là (các) trọng tâm của cây, cũng là đáp án.

```cpp
vector<int> findMinHeightTrees(int n, vector<vector<int>> &edges) {
    if (n == 1) return {0};

    vector<vector<int>> danhSachKe(n);
    vector<int> bac(n, 0);
    for (auto &e : edges) {
        danhSachKe[e[0]].push_back(e[1]);
        danhSachKe[e[1]].push_back(e[0]);
        bac[e[0]]++; bac[e[1]]++;
    }

    queue<int> laHienTai;
    for (int i = 0; i < n; i++) if (bac[i] == 1) laHienTai.push(i);

    int soDinhConLai = n;
    vector<int> ketQua;

    while (soDinhConLai > 2) {
        int soLaHienTai = laHienTai.size();
        soDinhConLai -= soLaHienTai;
        for (int i = 0; i < soLaHienTai; i++) {
            int la = laHienTai.front(); laHienTai.pop();
            for (int hangXom : danhSachKe[la]) {
                bac[hangXom]--;
                if (bac[hangXom] == 1) laHienTai.push(hangXom);
            }
        }
    }

    while (!laHienTai.empty()) {
        ketQua.push_back(laHienTai.front());
        laHienTai.pop();
    }
    return ketQua;
}
```
</details>

<details>
<summary>Lời giải Bài 16 — Kiểm tra lịch thi xung đột</summary>

Xây dựng đồ thị xung khắc (cạnh giữa 2 môn không được thi cùng ca), áp dụng `kiemTraBipartite`/`kiem_tra_bipartite` (Ví dụ 2, mục 11.3). Vì đề đảm bảo đồ thị là bipartite, số ca thi tối thiểu chính là **2** (dùng đúng 2 nhóm màu tô được) — trừ khi có môn không xung khắc với môn nào, khi đó vẫn chỉ cần tối đa 2 ca vì bipartite luôn tô được bằng 2 màu.
</details>
