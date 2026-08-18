# Chương 9: Quy hoạch động trên cấu trúc đặc biệt

---

## 9.1. Quy hoạch động trên cây (Tree DP)

**Nêu bài toán:** Cho một cây gồm n đỉnh (n-1 cạnh, không có chu trình), mỗi đỉnh có một "trọng số" đại diện cho lợi ích khi chọn đỉnh đó. Hãy chọn ra một tập hợp các đỉnh sao cho **không có 2 đỉnh nào được chọn là cha-con trực tiếp của nhau**, và tổng trọng số các đỉnh được chọn là lớn nhất có thể (bài toán "Independent Set trên cây").

**Phân tích vấn đề:** Đây là bài toán tối ưu trên cấu trúc cây — khác với mảng (có thứ tự tuyến tính rõ ràng), cây có cấu trúc phân nhánh, mỗi đỉnh có thể có nhiều đỉnh con. Ta cần một cách tổ chức DP phù hợp với cấu trúc phân nhánh này.

**Giải pháp đơn thuần:** Dùng backtracking (Chương 7), thử "chọn" hoặc "không chọn" từng đỉnh, kiểm tra ràng buộc ở cuối. Độ phức tạp O(2^n) — không khả thi với n lớn.

**Khó khăn với giải pháp đơn thuần:** Với n lên tới hàng chục nghìn đỉnh, O(2^n) hoàn toàn không thể chạy được.

**Cách tiếp cận mới — Tree DP:** Định nghĩa trạng thái DP tại **mỗi đỉnh**, với giá trị được tính dựa trên trạng thái DP của các đỉnh **con** của nó. Vì cây không có chu trình, ta có thể duyệt theo kiểu DFS: xử lý xong toàn bộ các đỉnh con trước, rồi mới tính được giá trị DP của đỉnh cha (gọi là duyệt **post-order**).

### Minh hoạ lời giải chi tiết

Với bài toán Independent Set, ta định nghĩa 2 trạng thái tại mỗi đỉnh `u`:
- `dp[u][0]` = tổng trọng số lớn nhất của tập con hợp lệ trong cây con gốc `u`, khi **không chọn** đỉnh `u`.
- `dp[u][1]` = tổng trọng số lớn nhất của tập con hợp lệ trong cây con gốc `u`, khi **có chọn** đỉnh `u`.

Công thức:
- `dp[u][0] = tổng của max(dp[con][0], dp[con][1])` với mọi con trực tiếp của `u` — không chọn `u` thì mỗi con được tự do chọn hoặc không.
- `dp[u][1] = trongSo[u] + tổng của dp[con][0]` với mọi con trực tiếp của `u` — nếu chọn `u`, mọi con trực tiếp **bắt buộc không được chọn**.

Với cây minh hoạ: đỉnh 0 (trọng số 3) là gốc, có 2 con là đỉnh 1 (trọng số 2) và đỉnh 2 (trọng số 5); đỉnh 1 có 1 con là đỉnh 3 (trọng số 1).

```
        0 (w=3)
       /       \
   1 (w=2)    2 (w=5)
      |
   3 (w=1)
```

Tính từ lá lên gốc (post-order):
- Đỉnh 3 (lá): `dp[3][0] = 0`, `dp[3][1] = 1`.
- Đỉnh 2 (lá): `dp[2][0] = 0`, `dp[2][1] = 5`.
- Đỉnh 1 (có con là đỉnh 3): `dp[1][0] = max(dp[3][0], dp[3][1]) = max(0,1) = 1`. `dp[1][1] = 2 + dp[3][0] = 2 + 0 = 2`.
- Đỉnh 0 (có con là đỉnh 1 và đỉnh 2): `dp[0][0] = max(dp[1][0],dp[1][1]) + max(dp[2][0],dp[2][1]) = max(1,2) + max(0,5) = 2 + 5 = 7`. `dp[0][1] = 3 + dp[1][0] + dp[2][0] = 3 + 1 + 0 = 4`.

Kết quả cuối cùng: `max(dp[0][0], dp[0][1]) = max(7, 4) = 7`.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKe;
vector<int> trongSo;
vector<array<long long,2>> dp; // dp[u][0], dp[u][1]

void dfsTinhDP(int u, int cha) {
    dp[u][0] = 0;
    dp[u][1] = trongSo[u];

    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) {
            continue; // bỏ qua cạnh quay lại cha, tránh đi vòng lại
        }

        dfsTinhDP(con, u); // đệ quy xử lý con trước

        dp[u][0] = dp[u][0] + max(dp[con][0], dp[con][1]);
        dp[u][1] = dp[u][1] + dp[con][0];
    }
}

int main() {
    int n = 4;
    danhSachKe.assign(n, {});
    trongSo = {3, 2, 5, 1};
    dp.assign(n, {0, 0});

    // Cạnh: 0-1, 0-2, 1-3
    danhSachKe[0].push_back(1); danhSachKe[1].push_back(0);
    danhSachKe[0].push_back(2); danhSachKe[2].push_back(0);
    danhSachKe[1].push_back(3); danhSachKe[3].push_back(1);

    dfsTinhDP(0, -1); // gốc là đỉnh 0, cha ảo là -1

    cout << max(dp[0][0], dp[0][1]) << endl;

    return 0;
}
```

#### PYTHON
```python
import sys
sys.setrecursionlimit(200000)

danh_sach_ke = []
trong_so = []
dp = []


def dfs_tinh_dp(u, cha):
    dp[u][0] = 0
    dp[u][1] = trong_so[u]

    for con in danh_sach_ke[u]:
        if con == cha:
            continue

        dfs_tinh_dp(con, u)

        dp[u][0] = dp[u][0] + max(dp[con][0], dp[con][1])
        dp[u][1] = dp[u][1] + dp[con][0]


n = 4
danh_sach_ke = [[] for _ in range(n)]
trong_so = [3, 2, 5, 1]
dp = [[0, 0] for _ in range(n)]

danh_sach_ke[0].append(1); danh_sach_ke[1].append(0)
danh_sach_ke[0].append(2); danh_sach_ke[2].append(0)
danh_sach_ke[1].append(3); danh_sach_ke[3].append(1)

dfs_tinh_dp(0, -1)

print(max(dp[0][0], dp[0][1]))
```

### Ví dụ 1 (Dễ) — Đếm số lượng đỉnh trong mỗi cây con

**Phân tích:** Đây là dạng Tree DP đơn giản nhất — `dp[u]` = số lượng đỉnh trong cây con gốc `u`, bằng 1 (chính nó) cộng tổng `dp[con]` của các con.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKe;
vector<int> soLuongDinhTrongCayCon;

void dfsDemDinh(int u, int cha) {
    soLuongDinhTrongCayCon[u] = 1;

    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) {
            continue;
        }
        dfsDemDinh(con, u);
        soLuongDinhTrongCayCon[u] = soLuongDinhTrongCayCon[u] + soLuongDinhTrongCayCon[con];
    }
}

int main() {
    int n = 5;
    danhSachKe.assign(n, {});
    soLuongDinhTrongCayCon.assign(n, 0);

    danhSachKe[0].push_back(1); danhSachKe[1].push_back(0);
    danhSachKe[0].push_back(2); danhSachKe[2].push_back(0);
    danhSachKe[1].push_back(3); danhSachKe[3].push_back(1);
    danhSachKe[1].push_back(4); danhSachKe[4].push_back(1);

    dfsDemDinh(0, -1);

    for (int i = 0; i < n; i++) {
        cout << "Dinh " << i << ": " << soLuongDinhTrongCayCon[i] << " dinh trong cay con" << endl;
    }

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke = []
so_luong_dinh_trong_cay_con = []


def dfs_dem_dinh(u, cha):
    so_luong_dinh_trong_cay_con[u] = 1

    for con in danh_sach_ke[u]:
        if con == cha:
            continue
        dfs_dem_dinh(con, u)
        so_luong_dinh_trong_cay_con[u] = so_luong_dinh_trong_cay_con[u] + so_luong_dinh_trong_cay_con[con]


n = 5
danh_sach_ke = [[] for _ in range(n)]
so_luong_dinh_trong_cay_con = [0] * n

danh_sach_ke[0].append(1); danh_sach_ke[1].append(0)
danh_sach_ke[0].append(2); danh_sach_ke[2].append(0)
danh_sach_ke[1].append(3); danh_sach_ke[3].append(1)
danh_sach_ke[1].append(4); danh_sach_ke[4].append(1)

dfs_dem_dinh(0, -1)

for i in range(n):
    print("Dinh", i, ":", so_luong_dinh_trong_cay_con[i], "dinh trong cay con")
```

### Ví dụ 2 (Trung bình) — Đường kính của cây (Tree Diameter)

Bài toán: cho một cây, tìm độ dài đường đi dài nhất giữa 2 đỉnh bất kỳ trong cây (gọi là "đường kính" của cây).

**Phân tích:** Với mỗi đỉnh `u`, đường đi dài nhất **đi qua** `u` (và nằm trong cây con gốc `u`) bằng tổng của 2 nhánh sâu nhất trong số các con của `u`. Ta tính `chieuCao[u]` = chiều cao lớn nhất từ `u` xuống một lá bất kỳ trong cây con của nó, đồng thời cập nhật đường kính bằng tổng 2 giá trị `chieuCao` lớn nhất trong số các con.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKe;
long long duongKinhLonNhat = 0;

int dfsTinhChieuCao(int u, int cha) {
    int chieuCaoLon1 = 0;
    int chieuCaoLon2 = 0;

    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) {
            continue;
        }

        int chieuCaoCuaCon = dfsTinhChieuCao(con, u) + 1;

        if (chieuCaoCuaCon > chieuCaoLon1) {
            chieuCaoLon2 = chieuCaoLon1;
            chieuCaoLon1 = chieuCaoCuaCon;
        } else if (chieuCaoCuaCon > chieuCaoLon2) {
            chieuCaoLon2 = chieuCaoCuaCon;
        }
    }

    long long duongDiQuaUyDinhNay = (long long)chieuCaoLon1 + (long long)chieuCaoLon2;
    if (duongDiQuaUyDinhNay > duongKinhLonNhat) {
        duongKinhLonNhat = duongDiQuaUyDinhNay;
    }

    return chieuCaoLon1;
}

int main() {
    int n = 6;
    danhSachKe.assign(n, {});

    danhSachKe[0].push_back(1); danhSachKe[1].push_back(0);
    danhSachKe[1].push_back(2); danhSachKe[2].push_back(1);
    danhSachKe[1].push_back(3); danhSachKe[3].push_back(1);
    danhSachKe[3].push_back(4); danhSachKe[4].push_back(3);
    danhSachKe[3].push_back(5); danhSachKe[5].push_back(3);

    dfsTinhChieuCao(0, -1);

    cout << "Duong kinh cay: " << duongKinhLonNhat << endl;

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke = []
duong_kinh_lon_nhat = [0]


def dfs_tinh_chieu_cao(u, cha):
    chieu_cao_lon_1 = 0
    chieu_cao_lon_2 = 0

    for con in danh_sach_ke[u]:
        if con == cha:
            continue

        chieu_cao_cua_con = dfs_tinh_chieu_cao(con, u) + 1

        if chieu_cao_cua_con > chieu_cao_lon_1:
            chieu_cao_lon_2 = chieu_cao_lon_1
            chieu_cao_lon_1 = chieu_cao_cua_con
        elif chieu_cao_cua_con > chieu_cao_lon_2:
            chieu_cao_lon_2 = chieu_cao_cua_con

    duong_di_qua_dinh_nay = chieu_cao_lon_1 + chieu_cao_lon_2
    if duong_di_qua_dinh_nay > duong_kinh_lon_nhat[0]:
        duong_kinh_lon_nhat[0] = duong_di_qua_dinh_nay

    return chieu_cao_lon_1


n = 6
danh_sach_ke = [[] for _ in range(n)]

danh_sach_ke[0].append(1); danh_sach_ke[1].append(0)
danh_sach_ke[1].append(2); danh_sach_ke[2].append(1)
danh_sach_ke[1].append(3); danh_sach_ke[3].append(1)
danh_sach_ke[3].append(4); danh_sach_ke[4].append(3)
danh_sach_ke[3].append(5); danh_sach_ke[5].append(3)

dfs_tinh_chieu_cao(0, -1)

print("Duong kinh cay:", duong_kinh_lon_nhat[0])
```

### Ví dụ 3 (Khó) — Đếm số cách tô màu cây với ràng buộc

Bài toán: cho một cây n đỉnh và k màu, hãy đếm số cách tô màu cho tất cả các đỉnh sao cho 2 đỉnh cha-con trực tiếp **không được cùng màu**.

**Phân tích:** Định nghĩa `dp[u]` = số cách tô màu hợp lệ cho cây con gốc `u` (không quan tâm màu cụ thể của `u`, chỉ đếm tổng số cách). Với mỗi con `v` của `u`, con `v` có `(k-1)` lựa chọn màu hợp lệ (khác màu `u`) nhân với `dp[v]` cách tô hợp lệ cho cây con của chính nó, nhưng vì `dp[v]` đã tính tổng trên **mọi màu có thể của v**, ta cần chia đều: số cách hợp lệ cho con v với điều kiện khác màu cha là `dp[v] × (k-1) / k`. Để tránh phép chia (dễ sai với modulo), ta định nghĩa lại trạng thái chặt chẽ hơn.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

const long long MOD = 1000000007;
vector<vector<int>> danhSachKe;
vector<long long> dp; // dp[u] = số cách tô hợp lệ cho cây con gốc u (tổng trên mọi màu của u)
int soMau;

void dfsTinhSoCachToMau(int u, int cha) {
    dp[u] = soMau; // đỉnh u có soMau lựa chọn màu, và ban đầu (chưa xét con) mỗi lựa chọn là 1 cách

    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) {
            continue;
        }

        dfsTinhSoCachToMau(con, u);

        // Với mỗi màu đã chọn của u, con có (soMau - 1) lựa chọn màu khác,
        // và trong (soMau) cách tô cây con của "con", trung bình có (soMau-1)/soMau phần khác màu cha
        // => công thức nhân trực tiếp: dp[u] *= dp[con] * (soMau - 1) / soMau
        // Để tránh chia, ta nhân dp[u] với (dp[con] / soMau) * (soMau - 1) bằng nghịch đảo modulo,
        // nhưng cách đơn giản hơn cho học sinh: chia trước khi nhân vì dp[con] luôn chia hết cho soMau
        // (vì dp[con] = soMau * (một số cách tô con đó với 1 màu cụ thể))
        long long soCachChoMotMauCuThe = dp[con] / soMau;
        dp[u] = (dp[u] * ((soCachChoMotMauCuThe * (soMau - 1)) % MOD)) % MOD;
    }
}

int main() {
    int n = 4;
    soMau = 3;
    danhSachKe.assign(n, {});
    dp.assign(n, 0);

    danhSachKe[0].push_back(1); danhSachKe[1].push_back(0);
    danhSachKe[0].push_back(2); danhSachKe[2].push_back(0);
    danhSachKe[1].push_back(3); danhSachKe[3].push_back(1);

    dfsTinhSoCachToMau(0, -1);

    cout << dp[0] << endl;

    return 0;
}
```

#### PYTHON
```python
MOD = 1000000007
danh_sach_ke = []
dp = []
so_mau = 0


def dfs_tinh_so_cach_to_mau(u, cha):
    dp[u] = so_mau

    for con in danh_sach_ke[u]:
        if con == cha:
            continue

        dfs_tinh_so_cach_to_mau(con, u)

        so_cach_cho_mot_mau_cu_the = dp[con] // so_mau
        dp[u] = (dp[u] * ((so_cach_cho_mot_mau_cu_the * (so_mau - 1)) % MOD)) % MOD


n = 4
so_mau = 3
danh_sach_ke = [[] for _ in range(n)]
dp = [0] * n

danh_sach_ke[0].append(1); danh_sach_ke[1].append(0)
danh_sach_ke[0].append(2); danh_sach_ke[2].append(0)
danh_sach_ke[1].append(3); danh_sach_ke[3].append(1)

dfs_tinh_so_cach_to_mau(0, -1)

print(dp[0])
```

> **Ghi chú:** ví dụ này minh hoạ một khó khăn thường gặp khi thiết kế Tree DP — đôi khi công thức "tự nhiên" liên quan đến phép chia, cần suy nghĩ kỹ để tránh sai số hoặc cần dùng nghịch đảo modulo (Chương 19) nếu phép chia không chia hết.

**Bài tập minh hoạ:** Cho một cây, tìm tổng khoảng cách từ một đỉnh gốc cho trước tới tất cả các đỉnh còn lại trong cây.

**Lời giải:** Đây là Tree DP đơn giản: `tongKhoangCach[u]` = tổng khoảng cách từ gốc tới mọi đỉnh trong cây con gốc `u`, cộng dồn qua DFS, mỗi cạnh đi qua cộng thêm `soLuongDinhTrongCayCon[con]` (mọi đỉnh trong cây con đó đều xa hơn 1 đơn vị).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKe;
vector<long long> khoangCachTuGoc;

void dfsTinhKhoangCach(int u, int cha, long long khoangCachHienTai) {
    khoangCachTuGoc[u] = khoangCachHienTai;

    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) {
            continue;
        }
        dfsTinhKhoangCach(con, u, khoangCachHienTai + 1);
    }
}

int main() {
    int n = 5;
    danhSachKe.assign(n, {});
    khoangCachTuGoc.assign(n, 0);

    danhSachKe[0].push_back(1); danhSachKe[1].push_back(0);
    danhSachKe[0].push_back(2); danhSachKe[2].push_back(0);
    danhSachKe[1].push_back(3); danhSachKe[3].push_back(1);
    danhSachKe[1].push_back(4); danhSachKe[4].push_back(1);

    dfsTinhKhoangCach(0, -1, 0);

    long long tongKhoangCach = 0;
    for (int i = 0; i < n; i++) {
        tongKhoangCach = tongKhoangCach + khoangCachTuGoc[i];
    }

    cout << "Tong khoang cach: " << tongKhoangCach << endl;

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke = []
khoang_cach_tu_goc = []


def dfs_tinh_khoang_cach(u, cha, khoang_cach_hien_tai):
    khoang_cach_tu_goc[u] = khoang_cach_hien_tai

    for con in danh_sach_ke[u]:
        if con == cha:
            continue
        dfs_tinh_khoang_cach(con, u, khoang_cach_hien_tai + 1)


n = 5
danh_sach_ke = [[] for _ in range(n)]
khoang_cach_tu_goc = [0] * n

danh_sach_ke[0].append(1); danh_sach_ke[1].append(0)
danh_sach_ke[0].append(2); danh_sach_ke[2].append(0)
danh_sach_ke[1].append(3); danh_sach_ke[3].append(1)
danh_sach_ke[1].append(4); danh_sach_ke[4].append(1)

dfs_tinh_khoang_cach(0, -1, 0)

print("Tong khoang cach:", sum(khoang_cach_tu_goc))
```

---

## 9.2. Kỹ thuật đổi gốc (Rerooting Technique)

**Nêu bài toán:** Cho một cây n đỉnh, với **mỗi** đỉnh `u`, hãy tính tổng khoảng cách từ `u` tới tất cả các đỉnh còn lại trong cây (yêu cầu tính cho **tất cả n đỉnh**, không chỉ 1 đỉnh gốc cố định như Bài tập minh hoạ ở mục 9.1).

**Phân tích vấn đề:** Nếu áp dụng lại đúng cách của mục 9.1 cho từng đỉnh làm gốc, ta tốn O(n) cho mỗi đỉnh, tổng độ phức tạp O(n²) — với n lớn (ví dụ 200.000) sẽ quá chậm.

**Giải pháp đơn thuần:** Chạy DFS riêng biệt n lần, mỗi lần coi 1 đỉnh khác nhau là gốc. Độ phức tạp O(n²).

**Khó khăn với giải pháp đơn thuần:** Với n = 200.000, O(n²) = 4×10^10 — không khả thi.

**Cách tiếp cận mới — Rerooting (đổi gốc):** Chạy DFS **2 lần** duy nhất. Lần đầu, tính kết quả cho 1 đỉnh gốc cố định (giống mục 9.1) — đây gọi là bước "DFS xuống" (down). Lần thứ hai, dùng kết quả đã có của đỉnh cha để **suy ra nhanh** kết quả của đỉnh con, dựa trên việc "nếu chuyển gốc từ cha sang con thì kết quả thay đổi như thế nào" — đây gọi là bước "DFS lên" hoặc "truyền xuống lại" (rerooting).

### Minh hoạ lời giải chi tiết

Với bài toán tổng khoảng cách, nhận xét quan trọng: khi ta "di chuyển gốc" từ đỉnh cha `u` sang đỉnh con `v` (giả sử cây con gốc `v` có `soLuongDinh[v]` đỉnh), mọi đỉnh **trong** cây con của `v` đều gần gốc mới hơn 1 đơn vị, còn mọi đỉnh **ngoài** cây con của `v` (tức là `n - soLuongDinh[v]` đỉnh) đều xa gốc mới hơn 1 đơn vị. Vậy:

```
tongKhoangCach[v] = tongKhoangCach[u] - soLuongDinh[v] + (n - soLuongDinh[v])
                   = tongKhoangCach[u] + n - 2 * soLuongDinh[v]
```

Công thức này cho phép tính `tongKhoangCach[v]` từ `tongKhoangCach[u]` (đỉnh cha) trong O(1), không cần DFS lại từ đầu.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKe;
vector<long long> soLuongDinhTrongCayCon;
vector<long long> tongKhoangCach;
int n;

void dfsBuoc1(int u, int cha, long long khoangCachHienTai) {
    soLuongDinhTrongCayCon[u] = 1;
    tongKhoangCach[0] = tongKhoangCach[0] + khoangCachHienTai; // đang tính cho gốc mặc định là đỉnh 0

    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) {
            continue;
        }
        dfsBuoc1(con, u, khoangCachHienTai + 1);
        soLuongDinhTrongCayCon[u] = soLuongDinhTrongCayCon[u] + soLuongDinhTrongCayCon[con];
    }
}

void dfsBuoc2(int u, int cha) {
    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) {
            continue;
        }

        // Suy ra tongKhoangCach[con] từ tongKhoangCach[u]
        tongKhoangCach[con] = tongKhoangCach[u] + n - 2 * soLuongDinhTrongCayCon[con];

        dfsBuoc2(con, u);
    }
}

int main() {
    n = 5;
    danhSachKe.assign(n, {});
    soLuongDinhTrongCayCon.assign(n, 0);
    tongKhoangCach.assign(n, 0);

    danhSachKe[0].push_back(1); danhSachKe[1].push_back(0);
    danhSachKe[0].push_back(2); danhSachKe[2].push_back(0);
    danhSachKe[1].push_back(3); danhSachKe[3].push_back(1);
    danhSachKe[1].push_back(4); danhSachKe[4].push_back(1);

    dfsBuoc1(0, -1, 0);   // tính cho gốc mặc định (đỉnh 0)
    dfsBuoc2(0, -1);      // suy ra kết quả cho mọi đỉnh khác

    for (int i = 0; i < n; i++) {
        cout << "Dinh " << i << ": tong khoang cach = " << tongKhoangCach[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
import sys
sys.setrecursionlimit(300000)

danh_sach_ke = []
so_luong_dinh_trong_cay_con = []
tong_khoang_cach = []
n = 0


def dfs_buoc_1(u, cha, khoang_cach_hien_tai):
    so_luong_dinh_trong_cay_con[u] = 1
    tong_khoang_cach[0] = tong_khoang_cach[0] + khoang_cach_hien_tai

    for con in danh_sach_ke[u]:
        if con == cha:
            continue
        dfs_buoc_1(con, u, khoang_cach_hien_tai + 1)
        so_luong_dinh_trong_cay_con[u] = so_luong_dinh_trong_cay_con[u] + so_luong_dinh_trong_cay_con[con]


def dfs_buoc_2(u, cha):
    for con in danh_sach_ke[u]:
        if con == cha:
            continue

        tong_khoang_cach[con] = tong_khoang_cach[u] + n - 2 * so_luong_dinh_trong_cay_con[con]

        dfs_buoc_2(con, u)


n = 5
danh_sach_ke = [[] for _ in range(n)]
so_luong_dinh_trong_cay_con = [0] * n
tong_khoang_cach = [0] * n

danh_sach_ke[0].append(1); danh_sach_ke[1].append(0)
danh_sach_ke[0].append(2); danh_sach_ke[2].append(0)
danh_sach_ke[1].append(3); danh_sach_ke[3].append(1)
danh_sach_ke[1].append(4); danh_sach_ke[4].append(1)

dfs_buoc_1(0, -1, 0)
dfs_buoc_2(0, -1)

for i in range(n):
    print("Dinh", i, ": tong khoang cach =", tong_khoang_cach[i])
```

### Ví dụ 1 (Dễ) — Đếm số đỉnh "gần hơn nửa cây" khi đổi gốc

**Phân tích:** Dùng lại `soLuongDinhTrongCayCon` đã tính ở bước 1 — với mỗi cạnh `(u, con)`, nếu coi `con` là gốc mới, số đỉnh "phía cây con của con" là `soLuongDinhTrongCayCon[con]`, còn phía "ngoài" là `n - soLuongDinhTrongCayCon[con]`. Đây là bài rèn phản xạ sử dụng lại thông tin đã tính ở bước 1 để suy luận nhanh, không cần DFS lại.

#### C++
```cpp
// Sử dụng lại soLuongDinhTrongCayCon đã tính ở dfsBuoc1 phía trên
void inSoDinhMoiPhiaKhiDoiGoc(int u, int cha, int n) {
    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) {
            continue;
        }

        long long phiaConCua = soLuongDinhTrongCayCon[con];
        long long phiaBenNgoai = n - phiaConCua;

        cout << "Canh (" << u << "," << con << "): phia con = " << phiaConCua
             << ", phia ngoai = " << phiaBenNgoai << endl;

        inSoDinhMoiPhiaKhiDoiGoc(con, u, n);
    }
}
```

#### PYTHON
```python
def in_so_dinh_moi_phia_khi_doi_goc(u, cha, n):
    for con in danh_sach_ke[u]:
        if con == cha:
            continue

        phia_con_cua = so_luong_dinh_trong_cay_con[con]
        phia_ben_ngoai = n - phia_con_cua

        print("Canh (", u, ",", con, "): phia con =", phia_con_cua, ", phia ngoai =", phia_ben_ngoai)

        in_so_dinh_moi_phia_khi_doi_goc(con, u, n)
```

### Ví dụ 2 (Trung bình) — Tìm trọng tâm của cây (Centroid) bằng Rerooting

Bài toán: tìm đỉnh `u` sao cho khi coi `u` là gốc, kích thước của cây con lớn nhất trong số các cây con trực tiếp của `u` là **nhỏ nhất có thể** (đỉnh này gọi là "trọng tâm" của cây).

**Phân tích:** Với mỗi đỉnh `u`, ta cần biết kích thước của **mọi** nhánh xuất phát từ `u` (không chỉ các nhánh "xuống" như DFS thông thường, mà cả nhánh "lên" phía cha). Kỹ thuật rerooting cho phép tính "kích thước phần còn lại của cây khi bỏ nhánh xuống `con`" chính là `n - soLuongDinhTrongCayCon[con]` — đây thực chất là kích thước nhánh "lên" khi đứng tại `con`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> danhSachKe;
vector<int> soLuongDinhTrongCayCon;
int n;
int trongTam = -1;
long long kichThuocNhanhLonNhatNhoNhat = LLONG_MAX;

void dfsTinhKichThuoc(int u, int cha) {
    soLuongDinhTrongCayCon[u] = 1;
    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) continue;
        dfsTinhKichThuoc(con, u);
        soLuongDinhTrongCayCon[u] += soLuongDinhTrongCayCon[con];
    }
}

void dfsTimTrongTam(int u, int cha) {
    long long kichThuocNhanhLonNhat = 0;

    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) continue;
        kichThuocNhanhLonNhat = max(kichThuocNhanhLonNhat, (long long)soLuongDinhTrongCayCon[con]);
    }

    // Nhánh "lên" phía cha, kích thước = n trừ đi toàn bộ cây con gốc u
    if (cha != -1) {
        long long kichThuocNhanhLen = n - soLuongDinhTrongCayCon[u];
        kichThuocNhanhLonNhat = max(kichThuocNhanhLonNhat, kichThuocNhanhLen);
    }

    if (kichThuocNhanhLonNhat < kichThuocNhanhLonNhatNhoNhat) {
        kichThuocNhanhLonNhatNhoNhat = kichThuocNhanhLonNhat;
        trongTam = u;
    }

    for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
        int con = danhSachKe[u][i];
        if (con == cha) continue;
        dfsTimTrongTam(con, u);
    }
}

int main() {
    n = 6;
    danhSachKe.assign(n, {});
    soLuongDinhTrongCayCon.assign(n, 0);

    danhSachKe[0].push_back(1); danhSachKe[1].push_back(0);
    danhSachKe[1].push_back(2); danhSachKe[2].push_back(1);
    danhSachKe[1].push_back(3); danhSachKe[3].push_back(1);
    danhSachKe[3].push_back(4); danhSachKe[4].push_back(3);
    danhSachKe[3].push_back(5); danhSachKe[5].push_back(3);

    dfsTinhKichThuoc(0, -1);
    dfsTimTrongTam(0, -1);

    cout << "Trong tam cua cay la dinh: " << trongTam << endl;

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke = []
so_luong_dinh_trong_cay_con = []
n = 0
trong_tam = [-1]
kich_thuoc_nhanh_lon_nhat_nho_nhat = [float('inf')]


def dfs_tinh_kich_thuoc(u, cha):
    so_luong_dinh_trong_cay_con[u] = 1
    for con in danh_sach_ke[u]:
        if con == cha:
            continue
        dfs_tinh_kich_thuoc(con, u)
        so_luong_dinh_trong_cay_con[u] += so_luong_dinh_trong_cay_con[con]


def dfs_tim_trong_tam(u, cha):
    kich_thuoc_nhanh_lon_nhat = 0

    for con in danh_sach_ke[u]:
        if con == cha:
            continue
        kich_thuoc_nhanh_lon_nhat = max(kich_thuoc_nhanh_lon_nhat, so_luong_dinh_trong_cay_con[con])

    if cha != -1:
        kich_thuoc_nhanh_len = n - so_luong_dinh_trong_cay_con[u]
        kich_thuoc_nhanh_lon_nhat = max(kich_thuoc_nhanh_lon_nhat, kich_thuoc_nhanh_len)

    if kich_thuoc_nhanh_lon_nhat < kich_thuoc_nhanh_lon_nhat_nho_nhat[0]:
        kich_thuoc_nhanh_lon_nhat_nho_nhat[0] = kich_thuoc_nhanh_lon_nhat
        trong_tam[0] = u

    for con in danh_sach_ke[u]:
        if con == cha:
            continue
        dfs_tim_trong_tam(con, u)


n = 6
danh_sach_ke = [[] for _ in range(n)]
so_luong_dinh_trong_cay_con = [0] * n

danh_sach_ke[0].append(1); danh_sach_ke[1].append(0)
danh_sach_ke[1].append(2); danh_sach_ke[2].append(1)
danh_sach_ke[1].append(3); danh_sach_ke[3].append(1)
danh_sach_ke[3].append(4); danh_sach_ke[4].append(3)
danh_sach_ke[3].append(5); danh_sach_ke[5].append(3)

dfs_tinh_kich_thuoc(0, -1)
dfs_tim_trong_tam(0, -1)

print("Trong tam cua cay la dinh:", trong_tam[0])
```

### Ví dụ 3 (Khó) — Tổng trọng số đường đi dài nhất từ mỗi đỉnh (Rerooting với 2 giá trị lớn nhất)

Bài toán: cho cây có trọng số trên cạnh, với **mỗi đỉnh** `u`, hãy tìm khoảng cách xa nhất từ `u` tới một đỉnh bất kỳ khác trong cây.

**Phân tích:** Đây là dạng Rerooting phức tạp hơn Ví dụ 2, mục 9.1 (đường kính) — thay vì chỉ cần biết đường kính của toàn cây, ta cần biết cho **từng đỉnh**. Kỹ thuật: tại bước 1 (DFS xuống), với mỗi đỉnh `u`, tính `xaNhat1[u]` (khoảng cách xa nhất đi xuống) và `xaNhat2[u]` (khoảng cách xa thứ 2, đi theo nhánh con khác với nhánh tạo ra `xaNhat1`). Ở bước 2 (Rerooting), với mỗi đỉnh con `v` của `u`, khoảng cách xa nhất "đi lên qua u" được suy ra từ khoảng cách xa nhất tại `u` — nhưng phải cẩn thận: nếu nhánh xa nhất của `u` chính là đi qua `v`, ta phải dùng `xaNhat2[u]` thay vì `xaNhat1[u]`, để tránh đi lại chính nhánh đó.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<pair<int,int>>> danhSachKe; // (đỉnh kề, trọng số cạnh)
vector<long long> xaNhat1, xaNhat2;
vector<int> dinhTaoXaNhat1; // đỉnh con nào tạo ra giá trị xaNhat1[u]
vector<long long> xaNhatKhiDoiGoc;
int n;

void dfsBuoc1(int u, int cha) {
    xaNhat1[u] = 0;
    xaNhat2[u] = 0;
    dinhTaoXaNhat1[u] = -1;

    for (auto &canh : danhSachKe[u]) {
        int con = canh.first;
        int trongSoCanh = canh.second;
        if (con == cha) continue;

        dfsBuoc1(con, u);
        long long khoangCachQuaCon = xaNhat1[con] + trongSoCanh;

        if (khoangCachQuaCon > xaNhat1[u]) {
            xaNhat2[u] = xaNhat1[u];
            xaNhat1[u] = khoangCachQuaCon;
            dinhTaoXaNhat1[u] = con;
        } else if (khoangCachQuaCon > xaNhat2[u]) {
            xaNhat2[u] = khoangCachQuaCon;
        }
    }
}

void dfsBuoc2(int u, int cha, long long xaNhatTuPhiaCha) {
    xaNhatKhiDoiGoc[u] = max(xaNhat1[u], xaNhatTuPhiaCha);

    for (auto &canh : danhSachKe[u]) {
        int con = canh.first;
        int trongSoCanh = canh.second;
        if (con == cha) continue;

        long long xaNhatCuaUKhiKhongDiQuaCon;
        if (dinhTaoXaNhat1[u] == con) {
            xaNhatCuaUKhiKhongDiQuaCon = xaNhat2[u];
        } else {
            xaNhatCuaUKhiKhongDiQuaCon = xaNhat1[u];
        }

        long long xaNhatTruyenXuongCon = max(xaNhatCuaUKhiKhongDiQuaCon, xaNhatTuPhiaCha) + trongSoCanh;

        dfsBuoc2(con, u, xaNhatTruyenXuongCon);
    }
}

int main() {
    n = 5;
    danhSachKe.assign(n, {});
    xaNhat1.assign(n, 0);
    xaNhat2.assign(n, 0);
    dinhTaoXaNhat1.assign(n, -1);
    xaNhatKhiDoiGoc.assign(n, 0);

    danhSachKe[0].push_back({1, 2}); danhSachKe[1].push_back({0, 2});
    danhSachKe[0].push_back({2, 3}); danhSachKe[2].push_back({0, 3});
    danhSachKe[1].push_back({3, 1}); danhSachKe[3].push_back({1, 1});
    danhSachKe[1].push_back({4, 4}); danhSachKe[4].push_back({1, 4});

    dfsBuoc1(0, -1);
    dfsBuoc2(0, -1, 0);

    for (int i = 0; i < n; i++) {
        cout << "Dinh " << i << ": khoang cach xa nhat = " << xaNhatKhiDoiGoc[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
danh_sach_ke = []
xa_nhat_1 = []
xa_nhat_2 = []
dinh_tao_xa_nhat_1 = []
xa_nhat_khi_doi_goc = []
n = 0


def dfs_buoc_1(u, cha):
    xa_nhat_1[u] = 0
    xa_nhat_2[u] = 0
    dinh_tao_xa_nhat_1[u] = -1

    for con, trong_so_canh in danh_sach_ke[u]:
        if con == cha:
            continue

        dfs_buoc_1(con, u)
        khoang_cach_qua_con = xa_nhat_1[con] + trong_so_canh

        if khoang_cach_qua_con > xa_nhat_1[u]:
            xa_nhat_2[u] = xa_nhat_1[u]
            xa_nhat_1[u] = khoang_cach_qua_con
            dinh_tao_xa_nhat_1[u] = con
        elif khoang_cach_qua_con > xa_nhat_2[u]:
            xa_nhat_2[u] = khoang_cach_qua_con


def dfs_buoc_2(u, cha, xa_nhat_tu_phia_cha):
    xa_nhat_khi_doi_goc[u] = max(xa_nhat_1[u], xa_nhat_tu_phia_cha)

    for con, trong_so_canh in danh_sach_ke[u]:
        if con == cha:
            continue

        if dinh_tao_xa_nhat_1[u] == con:
            xa_nhat_cua_u_khi_khong_di_qua_con = xa_nhat_2[u]
        else:
            xa_nhat_cua_u_khi_khong_di_qua_con = xa_nhat_1[u]

        xa_nhat_truyen_xuong_con = max(xa_nhat_cua_u_khi_khong_di_qua_con, xa_nhat_tu_phia_cha) + trong_so_canh

        dfs_buoc_2(con, u, xa_nhat_truyen_xuong_con)


n = 5
danh_sach_ke = [[] for _ in range(n)]
xa_nhat_1 = [0] * n
xa_nhat_2 = [0] * n
dinh_tao_xa_nhat_1 = [-1] * n
xa_nhat_khi_doi_goc = [0] * n

danh_sach_ke[0].append((1, 2)); danh_sach_ke[1].append((0, 2))
danh_sach_ke[0].append((2, 3)); danh_sach_ke[2].append((0, 3))
danh_sach_ke[1].append((3, 1)); danh_sach_ke[3].append((1, 1))
danh_sach_ke[1].append((4, 4)); danh_sach_ke[4].append((1, 4))

dfs_buoc_1(0, -1)
dfs_buoc_2(0, -1, 0)

for i in range(n):
    print("Dinh", i, ": khoang cach xa nhat =", xa_nhat_khi_doi_goc[i])
```

**Bài tập minh hoạ:** Cho một cây, với mỗi đỉnh `u`, hãy tính số lượng đỉnh nằm trong khoảng cách không quá k từ `u` (sử dụng ý tưởng rerooting để tính nhanh cho mọi đỉnh).

**Lời giải:** Đây là bài mở rộng, gợi ý dùng kỹ thuật tương tự Ví dụ 3 nhưng thay giá trị "xa nhất" bằng "đếm số đỉnh trong khoảng cách k" — có thể cần kết hợp thêm cấu trúc dữ liệu như mảng cộng dồn theo độ sâu (Centroid Decomposition, kỹ thuật nâng cao hơn phạm vi chương này, học sinh khá giỏi có thể tự tìm hiểu thêm).

---

## 9.3. Quy hoạch động với trạng thái Bitmask (Bitmask DP)

**Nêu bài toán:** Có n công việc và n người, mỗi người `i` làm công việc `j` tốn chi phí `chiPhi[i][j]`. Mỗi người chỉ được giao đúng 1 công việc, mỗi công việc chỉ được giao cho đúng 1 người. Hãy tìm cách phân công để tổng chi phí là nhỏ nhất (bài toán phân công tối ưu — Assignment Problem).

**Phân tích vấn đề:** Với n người, số cách phân công có thể có là n! — nếu n = 15, con số này đã lên tới hơn 10^12, không thể duyệt hết bằng backtracking thuần tuý. Tuy nhiên nhận thấy: trạng thái quan trọng nhất không phải là "đã phân công theo thứ tự nào" mà là "**tập hợp** công việc nào đã được giao" — với n ≤ 20, ta có thể biểu diễn tập hợp này bằng một số nguyên nhị phân (bitmask), mỗi bit đại diện cho 1 công việc đã/chưa được giao.

**Giải pháp đơn thuần:** Backtracking thử tất cả các cách phân công. Độ phức tạp O(n!).

**Khó khăn với giải pháp đơn thuần:** Với n = 15, n! đã vượt 10^12 — không khả thi dù n không lớn.

**Cách tiếp cận mới — Bitmask DP:** Định nghĩa `dp[mask]` = chi phí nhỏ nhất khi đã phân công các công việc trong tập hợp `mask` cho những người đầu tiên (số người đã phân công bằng số bit 1 trong `mask`). Độ phức tạp trở thành O(2^n × n) — với n = 20, khoảng 20 triệu × 20 = 400 triệu phép tính, chạy được trong giới hạn thời gian cho phép.

### Các thao tác bit cơ bản cần nắm vững

| Thao tác | Cú pháp C++ | Cú pháp Python | Ý nghĩa |
|---|---|---|---|
| Kiểm tra bit thứ i có bật không | `(mask >> i) & 1` | `(mask >> i) & 1` | Trả về 0 hoặc 1 |
| Bật bit thứ i | `mask \| (1 << i)` | `mask \| (1 << i)` | Đặt bit i thành 1 |
| Tắt bit thứ i | `mask & ~(1 << i)` | `mask & ~(1 << i)` | Đặt bit i thành 0 |
| Đếm số bit 1 | `__builtin_popcount(mask)` | `bin(mask).count('1')` | Số lượng phần tử trong tập |
| Tập hợp đầy đủ n phần tử | `(1 << n) - 1` | `(1 << n) - 1` | Mask có đủ n bit đều là 1 |

### Minh hoạ lời giải chi tiết

Với n = 3 người và 3 công việc, `chiPhi[i][j]`:

```
        Việc 0  Việc 1  Việc 2
Người 0:   9       2       7
Người 1:   6       4       3
Người 2:   5       8       1
```

`dp[mask]` = chi phí nhỏ nhất để phân công cho `popcount(mask)` người đầu tiên (người 0, 1, ..., popcount(mask)-1), với `mask` là tập việc đã giao.

- `dp[000] = 0` (chưa phân công ai).
- Xét `dp[001]` (đã giao việc 0 cho người 0): `dp[001] = dp[000] + chiPhi[0][0] = 0 + 9 = 9`.
- Xét `dp[010]` (đã giao việc 1 cho người 0): `dp[010] = dp[000] + chiPhi[0][1] = 0 + 2 = 2`.
- Xét `dp[011]` (đã giao 2 việc {0,1} cho người 0 và người 1): có 2 cách đạt tới đây — từ `dp[001]` giao thêm việc 1 cho người 1 (`9 + chiPhi[1][1] = 9+4=13`), hoặc từ `dp[010]` giao thêm việc 0 cho người 1 (`2 + chiPhi[1][0] = 2+6=8`). Lấy nhỏ hơn: `dp[011] = 8`.
- ... (tiếp tục tương tự cho các mask còn lại)

Cuối cùng `dp[111]` (đã giao đủ cả 3 việc cho cả 3 người) chính là đáp án.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int giaiBaiToanPhanCong(vector<vector<int>> chiPhi, int n) {
    int soLuongMask = 1 << n;
    vector<long long> dp(soLuongMask, LLONG_MAX);
    dp[0] = 0;

    for (int mask = 0; mask < soLuongMask; mask++) {
        if (dp[mask] == LLONG_MAX) {
            continue; // trạng thái này chưa đạt được, bỏ qua
        }

        int nguoiHienTai = __builtin_popcount(mask); // số bit 1 = số người đã phân công
        if (nguoiHienTai == n) {
            continue; // đã phân công đủ hết, không cần xét tiếp
        }

        for (int viec = 0; viec < n; viec++) {
            bool viecDaGiao = (mask >> viec) & 1;
            if (viecDaGiao == true) {
                continue; // việc này đã giao cho ai đó rồi
            }

            int maskMoi = mask | (1 << viec);
            long long chiPhiMoi = dp[mask] + chiPhi[nguoiHienTai][viec];

            if (chiPhiMoi < dp[maskMoi]) {
                dp[maskMoi] = chiPhiMoi;
            }
        }
    }

    return (int)dp[soLuongMask - 1];
}

int main() {
    vector<vector<int>> chiPhi = {
        {9, 2, 7},
        {6, 4, 3},
        {5, 8, 1}
    };
    int n = 3;

    cout << giaiBaiToanPhanCong(chiPhi, n) << endl;

    return 0;
}
```

#### PYTHON
```python
def giai_bai_toan_phan_cong(chi_phi, n):
    so_luong_mask = 1 << n
    dp = [float('inf')] * so_luong_mask
    dp[0] = 0

    for mask in range(so_luong_mask):
        if dp[mask] == float('inf'):
            continue

        nguoi_hien_tai = bin(mask).count('1')
        if nguoi_hien_tai == n:
            continue

        for viec in range(n):
            viec_da_giao = (mask >> viec) & 1
            if viec_da_giao == 1:
                continue

            mask_moi = mask | (1 << viec)
            chi_phi_moi = dp[mask] + chi_phi[nguoi_hien_tai][viec]

            if chi_phi_moi < dp[mask_moi]:
                dp[mask_moi] = chi_phi_moi

    return dp[so_luong_mask - 1]


chi_phi = [
    [9, 2, 7],
    [6, 4, 3],
    [5, 8, 1],
]
n = 3

print(giai_bai_toan_phan_cong(chi_phi, n))
```

### Ví dụ 1 (Dễ) — Đếm số tập con có tổng chia hết cho k

**Phân tích:** Với n nhỏ (n ≤ 20), ta có thể dùng bitmask để duyệt qua **mọi** tập con của mảng, tính tổng từng tập con bằng bit, rồi kiểm tra chia hết. Đây không hẳn là "DP" theo đúng nghĩa (không có sự tái sử dụng kết quả bài toán con), nhưng minh hoạ cách dùng bitmask để duyệt tập con một cách có hệ thống — nền tảng cần nắm trước khi học DP phức tạp hơn.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int demTapConChiaHetChoK(vector<int> a, int k) {
    int n = (int)a.size();
    int soLuongMask = 1 << n;
    int soLuongTapConHopLe = 0;

    for (int mask = 0; mask < soLuongMask; mask++) {
        long long tong = 0;
        for (int i = 0; i < n; i++) {
            bool phanTuIDuocChon = (mask >> i) & 1;
            if (phanTuIDuocChon == true) {
                tong = tong + a[i];
            }
        }

        if (tong % k == 0) {
            soLuongTapConHopLe = soLuongTapConHopLe + 1;
        }
    }

    return soLuongTapConHopLe;
}

int main() {
    vector<int> a = {1, 2, 3, 4};
    int k = 3;

    cout << demTapConChiaHetChoK(a, k) << endl;

    return 0;
}
```

#### PYTHON
```python
def dem_tap_con_chia_het_cho_k(a, k):
    n = len(a)
    so_luong_mask = 1 << n
    so_luong_tap_con_hop_le = 0

    for mask in range(so_luong_mask):
        tong = 0
        for i in range(n):
            phan_tu_i_duoc_chon = (mask >> i) & 1
            if phan_tu_i_duoc_chon == 1:
                tong = tong + a[i]

        if tong % k == 0:
            so_luong_tap_con_hop_le = so_luong_tap_con_hop_le + 1

    return so_luong_tap_con_hop_le


a = [1, 2, 3, 4]
k = 3

print(dem_tap_con_chia_het_cho_k(a, k))
```

### Ví dụ 2 (Trung bình) — Chia nhóm tối thiểu theo ràng buộc trọng lượng (Elevator Rides dạng đơn giản)

Bài toán: có n người với trọng lượng cho trước, thang máy có sức chứa tối đa `sucChua`. Hãy tìm số lượt thang máy tối thiểu để chở hết tất cả mọi người.

**Phân tích:** Định nghĩa `dp[mask]` = số lượt thang máy tối thiểu để chở hết những người trong tập `mask`. Với mỗi `mask`, ta thử **mọi tập con** `submask` của `mask` làm "chuyến đi cuối cùng" (phải thoả tổng trọng lượng ≤ sucChua), rồi `dp[mask] = min(dp[mask - submask] + 1)`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timSoLuotThangMayToiThieu(vector<int> trongLuong, int sucChua) {
    int n = (int)trongLuong.size();
    int soLuongMask = 1 << n;

    vector<int> tongTrongLuongCuaMask(soLuongMask, 0);
    for (int mask = 1; mask < soLuongMask; mask++) {
        int bitThapNhat = mask & (-mask); // tách ra bit 1 thấp nhất
        int viTriBit = __builtin_ctz(bitThapNhat); // vị trí của bit đó
        tongTrongLuongCuaMask[mask] = tongTrongLuongCuaMask[mask ^ bitThapNhat] + trongLuong[viTriBit];
    }

    vector<int> dp(soLuongMask, INT_MAX);
    dp[0] = 0;

    for (int mask = 1; mask < soLuongMask; mask++) {
        // Duyệt qua mọi tập con "submask" của "mask" (kỹ thuật duyệt submask chuẩn)
        for (int submask = mask; submask > 0; submask = (submask - 1) & mask) {
            if (tongTrongLuongCuaMask[submask] <= sucChua) {
                int maskConLai = mask ^ submask;
                if (dp[maskConLai] != INT_MAX) {
                    int giaTriUngCu = dp[maskConLai] + 1;
                    if (giaTriUngCu < dp[mask]) {
                        dp[mask] = giaTriUngCu;
                    }
                }
            }
        }
    }

    return dp[soLuongMask - 1];
}

int main() {
    vector<int> trongLuong = {3, 5, 2, 6};
    int sucChua = 7;

    cout << timSoLuotThangMayToiThieu(trongLuong, sucChua) << endl;

    return 0;
}
```

#### PYTHON
```python
def tim_so_luot_thang_may_toi_thieu(trong_luong, suc_chua):
    n = len(trong_luong)
    so_luong_mask = 1 << n

    tong_trong_luong_cua_mask = [0] * so_luong_mask
    for mask in range(1, so_luong_mask):
        bit_thap_nhat = mask & (-mask)
        vi_tri_bit = bit_thap_nhat.bit_length() - 1
        tong_trong_luong_cua_mask[mask] = tong_trong_luong_cua_mask[mask ^ bit_thap_nhat] + trong_luong[vi_tri_bit]

    dp = [float('inf')] * so_luong_mask
    dp[0] = 0

    for mask in range(1, so_luong_mask):
        submask = mask
        while submask > 0:
            if tong_trong_luong_cua_mask[submask] <= suc_chua:
                mask_con_lai = mask ^ submask
                if dp[mask_con_lai] != float('inf'):
                    gia_tri_ung_cu = dp[mask_con_lai] + 1
                    if gia_tri_ung_cu < dp[mask]:
                        dp[mask] = gia_tri_ung_cu
            submask = (submask - 1) & mask

    return dp[so_luong_mask - 1]


trong_luong = [3, 5, 2, 6]
suc_chua = 7

print(tim_so_luot_thang_may_toi_thieu(trong_luong, suc_chua))
```

> **Lưu ý về độ phức tạp duyệt submask:** vòng lặp `for submask = mask; submask > 0; submask = (submask-1) & mask` là kỹ thuật chuẩn để duyệt qua **mọi tập con** của `mask` — tổng số lần lặp qua toàn bộ chương trình (cho tất cả các mask) là O(3^n), không phải O(4^n) như thoạt nhìn, vì mỗi phần tử trong n phần tử chỉ có đúng 3 trạng thái có thể: "không thuộc mask", "thuộc mask nhưng không thuộc submask", "thuộc cả mask và submask".

### Ví dụ 3 (Khó) — Bài toán người du lịch (Traveling Salesman Problem — TSP)

Bài toán: có n thành phố, biết khoảng cách giữa mọi cặp thành phố. Người du lịch xuất phát từ thành phố 0, phải đi qua **tất cả** các thành phố còn lại đúng 1 lần, rồi quay lại thành phố 0. Hãy tìm tổng quãng đường ngắn nhất.

**Phân tích:** Đây là bài toán Bitmask DP kinh điển nhất. Khác với Ví dụ trước (chỉ cần biết **tập hợp** đã xử lý), TSP cần biết thêm **đỉnh hiện tại đang đứng** — vì khoảng cách di chuyển tiếp theo phụ thuộc vào vị trí hiện tại. Định nghĩa `dp[mask][u]` = quãng đường ngắn nhất để thăm đúng tập thành phố `mask` (bắt buộc đã thăm thành phố 0), và hiện đang đứng ở thành phố `u`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long giaiBaiToanTSP(vector<vector<long long>> khoangCach, int n) {
    int soLuongMask = 1 << n;
    vector<vector<long long>> dp(soLuongMask, vector<long long>(n, LLONG_MAX));

    dp[1][0] = 0; // mask = 1 (chỉ có bit 0 bật) nghĩa là chỉ mới thăm thành phố 0, đang đứng ở đó

    for (int mask = 1; mask < soLuongMask; mask++) {
        for (int u = 0; u < n; u++) {
            if (dp[mask][u] == LLONG_MAX) {
                continue;
            }
            bool uThuocMask = (mask >> u) & 1;
            if (uThuocMask == false) {
                continue; // trạng thái không hợp lệ: đang đứng ở u nhưng u chưa thuộc tập đã thăm
            }

            for (int v = 0; v < n; v++) {
                bool vDaTham = (mask >> v) & 1;
                if (vDaTham == true) {
                    continue; // v đã thăm rồi, không thăm lại
                }

                int maskMoi = mask | (1 << v);
                long long chiPhiMoi = dp[mask][u] + khoangCach[u][v];

                if (chiPhiMoi < dp[maskMoi][v]) {
                    dp[maskMoi][v] = chiPhiMoi;
                }
            }
        }
    }

    int maskDayDu = soLuongMask - 1;
    long long ketQuaTotNhat = LLONG_MAX;

    for (int u = 0; u < n; u++) {
        if (dp[maskDayDu][u] == LLONG_MAX) {
            continue;
        }
        long long chiPhiQuayVe = dp[maskDayDu][u] + khoangCach[u][0];
        if (chiPhiQuayVe < ketQuaTotNhat) {
            ketQuaTotNhat = chiPhiQuayVe;
        }
    }

    return ketQuaTotNhat;
}

int main() {
    vector<vector<long long>> khoangCach = {
        {0, 10, 15, 20},
        {10, 0, 35, 25},
        {15, 35, 0, 30},
        {20, 25, 30, 0}
    };
    int n = 4;

    cout << giaiBaiToanTSP(khoangCach, n) << endl; // 80

    return 0;
}
```

#### PYTHON
```python
def giai_bai_toan_tsp(khoang_cach, n):
    so_luong_mask = 1 << n
    dp = [[float('inf')] * n for _ in range(so_luong_mask)]

    dp[1][0] = 0

    for mask in range(1, so_luong_mask):
        for u in range(n):
            if dp[mask][u] == float('inf'):
                continue
            u_thuoc_mask = (mask >> u) & 1
            if u_thuoc_mask == 0:
                continue

            for v in range(n):
                v_da_tham = (mask >> v) & 1
                if v_da_tham == 1:
                    continue

                mask_moi = mask | (1 << v)
                chi_phi_moi = dp[mask][u] + khoang_cach[u][v]

                if chi_phi_moi < dp[mask_moi][v]:
                    dp[mask_moi][v] = chi_phi_moi

    mask_day_du = so_luong_mask - 1
    ket_qua_tot_nhat = float('inf')

    for u in range(n):
        if dp[mask_day_du][u] == float('inf'):
            continue
        chi_phi_quay_ve = dp[mask_day_du][u] + khoang_cach[u][0]
        if chi_phi_quay_ve < ket_qua_tot_nhat:
            ket_qua_tot_nhat = chi_phi_quay_ve

    return ket_qua_tot_nhat


khoang_cach = [
    [0, 10, 15, 20],
    [10, 0, 35, 25],
    [15, 35, 0, 30],
    [20, 25, 30, 0],
]
n = 4

print(giai_bai_toan_tsp(khoang_cach, n))  # 80
```

**Bài tập minh hoạ:** Cho n vật với trọng lượng và giá trị cho trước, và 2 cái túi có sức chứa `W1` và `W2` (không phải 1 túi như Chương 8). Mỗi vật chỉ được đặt vào **tối đa 1** trong 2 túi (hoặc không đặt vào túi nào). Hãy tìm tổng giá trị lớn nhất.

**Lời giải:** Vì n nhỏ, dùng bitmask xét mọi tập con **cho túi 1**, tính tổng trọng lượng và giá trị của tập đó; nếu hợp lệ (không vượt W1), với **phần còn lại** (bù của tập đó), tiếp tục xét mọi tập con của phần còn lại cho túi 2 — cách đơn giản hoá cho học sinh mới học là duyệt tất cả cặp (mask1, mask2) rời nhau, độ phức tạp O(3^n) bằng kỹ thuật duyệt submask đã học ở Ví dụ 2.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long giaiHaiCaiTui(vector<int> trongLuong, vector<int> giaTri, int W1, int W2, int n) {
    int soLuongMask = 1 << n;
    vector<int> tongTrongLuong(soLuongMask, 0);
    vector<int> tongGiaTri(soLuongMask, 0);

    for (int mask = 1; mask < soLuongMask; mask++) {
        int bitThapNhat = mask & (-mask);
        int viTriBit = __builtin_ctz(bitThapNhat);
        tongTrongLuong[mask] = tongTrongLuong[mask ^ bitThapNhat] + trongLuong[viTriBit];
        tongGiaTri[mask] = tongGiaTri[mask ^ bitThapNhat] + giaTri[viTriBit];
    }

    long long ketQuaTotNhat = 0;
    int maskDayDu = soLuongMask - 1;

    for (int mask1 = 0; mask1 < soLuongMask; mask1++) {
        if (tongTrongLuong[mask1] > W1) {
            continue;
        }

        int phanConLai = maskDayDu ^ mask1;

        // Duyệt mọi tập con của phanConLai cho túi 2
        for (int mask2 = phanConLai; ; mask2 = (mask2 - 1) & phanConLai) {
            if (tongTrongLuong[mask2] <= W2) {
                long long tongGiaTriHienTai = tongGiaTri[mask1] + tongGiaTri[mask2];
                if (tongGiaTriHienTai > ketQuaTotNhat) {
                    ketQuaTotNhat = tongGiaTriHienTai;
                }
            }
            if (mask2 == 0) {
                break;
            }
        }
    }

    return ketQuaTotNhat;
}

int main() {
    vector<int> trongLuong = {3, 4, 5};
    vector<int> giaTri = {30, 50, 60};
    int W1 = 5, W2 = 4;
    int n = 3;

    cout << giaiHaiCaiTui(trongLuong, giaTri, W1, W2, n) << endl;

    return 0;
}
```

#### PYTHON
```python
def giai_hai_cai_tui(trong_luong, gia_tri, W1, W2, n):
    so_luong_mask = 1 << n
    tong_trong_luong = [0] * so_luong_mask
    tong_gia_tri = [0] * so_luong_mask

    for mask in range(1, so_luong_mask):
        bit_thap_nhat = mask & (-mask)
        vi_tri_bit = bit_thap_nhat.bit_length() - 1
        tong_trong_luong[mask] = tong_trong_luong[mask ^ bit_thap_nhat] + trong_luong[vi_tri_bit]
        tong_gia_tri[mask] = tong_gia_tri[mask ^ bit_thap_nhat] + gia_tri[vi_tri_bit]

    ket_qua_tot_nhat = 0
    mask_day_du = so_luong_mask - 1

    for mask1 in range(so_luong_mask):
        if tong_trong_luong[mask1] > W1:
            continue

        phan_con_lai = mask_day_du ^ mask1

        mask2 = phan_con_lai
        while True:
            if tong_trong_luong[mask2] <= W2:
                tong_gia_tri_hien_tai = tong_gia_tri[mask1] + tong_gia_tri[mask2]
                if tong_gia_tri_hien_tai > ket_qua_tot_nhat:
                    ket_qua_tot_nhat = tong_gia_tri_hien_tai
            if mask2 == 0:
                break
            mask2 = (mask2 - 1) & phan_con_lai

    return ket_qua_tot_nhat


trong_luong = [3, 4, 5]
gia_tri = [30, 50, 60]
W1, W2 = 5, 4
n = 3

print(giai_hai_cai_tui(trong_luong, gia_tri, W1, W2, n))
```

---

## 9.4. DP theo trạng thái kết hợp đồ thị (mở rộng từ TSP)

**Nêu bài toán:** Có n nhóm học sinh, mỗi nhóm có một số lượng thành viên nhất định. Có một số cặp nhóm "xung khắc" (không được xếp chung 1 phòng thi). Hãy tìm cách chia các nhóm vào các phòng thi (mỗi phòng có sức chứa giới hạn), sao cho số lượng phòng thi sử dụng là **ít nhất**, và không có 2 nhóm xung khắc nào ở chung phòng.

**Phân tích vấn đề:** Đây là biến thể kết hợp giữa Bitmask DP (mục 9.3) và ràng buộc dạng đồ thị (các cặp xung khắc) — trạng thái DP không chỉ đơn thuần là "tập hợp đã xử lý" mà còn phải tôn trọng cấu trúc quan hệ giữa các phần tử trong tập đó.

**Giải pháp đơn thuần:** Thử tất cả các cách chia nhóm vào phòng bằng backtracking thuần tuý. Với n nhóm, độ phức tạp có thể lên tới O(n^n) trong trường hợp xấu — quá chậm.

**Khó khăn với giải pháp đơn thuần:** Không tận dụng được cấu trúc lặp lại của bài toán con.

**Cách tiếp cận mới:** Kết hợp 2 kỹ thuật đã học: trước tiên, dùng bitmask để liệt kê trước tất cả các "tập nhóm hợp lệ" có thể xếp chung 1 phòng (không có cặp xung khắc, tổng thành viên ≤ sức chứa) — đây là bước tiền xử lý dùng kỹ thuật của Ví dụ 1, mục 9.3. Sau đó, áp dụng DP tương tự bài toán "Elevator Rides" (Ví dụ 2, mục 9.3): `dp[mask]` = số phòng ít nhất để xếp hết các nhóm trong `mask`, xét mọi tập con hợp lệ làm "phòng cuối cùng".

### Minh hoạ lời giải chi tiết

Với 4 nhóm (0,1,2,3), thành viên lần lượt là (2,3,1,4), sức chứa phòng = 5, và cặp (0,1) xung khắc:

**Bước 1 — Tiền xử lý các tập hợp lệ:** với mỗi mask, kiểm tra: (a) tổng thành viên ≤ 5, (b) không chứa cả 2 nhóm 0 và 1 cùng lúc.

Ví dụ mask = `0011` (nhóm 0, 1): tổng thành viên = 2+3 = 5 (≤5, hợp lệ về sức chứa) NHƯNG chứa cả nhóm 0 và 1 (xung khắc) -> **không hợp lệ**.

mask = `0101` (nhóm 0, 2): tổng = 2+1 = 3 (hợp lệ), không có xung khắc -> **hợp lệ**.

**Bước 2 — DP giống Elevator Rides:** áp dụng đúng công thức đã học ở Ví dụ 2, mục 9.3, nhưng chỉ xét các `submask` đã được đánh dấu là "hợp lệ" ở bước 1.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int giaiBaiToanChiaPhong(vector<int> soThanhVien, vector<pair<int,int>> capXungKhac, int sucChua, int n) {
    int soLuongMask = 1 << n;

    vector<bool> laTapHopLe(soLuongMask, true);
    vector<int> tongThanhVien(soLuongMask, 0);

    for (int mask = 1; mask < soLuongMask; mask++) {
        int bitThapNhat = mask & (-mask);
        int viTriBit = __builtin_ctz(bitThapNhat);
        tongThanhVien[mask] = tongThanhVien[mask ^ bitThapNhat] + soThanhVien[viTriBit];

        if (tongThanhVien[mask] > sucChua) {
            laTapHopLe[mask] = false;
        }
    }

    // Đánh dấu các mask chứa cặp xung khắc là không hợp lệ
    for (int i = 0; i < (int)capXungKhac.size(); i++) {
        int nhomA = capXungKhac[i].first;
        int nhomB = capXungKhac[i].second;

        for (int mask = 0; mask < soLuongMask; mask++) {
            bool coA = (mask >> nhomA) & 1;
            bool coB = (mask >> nhomB) & 1;
            if (coA == true && coB == true) {
                laTapHopLe[mask] = false;
            }
        }
    }

    vector<int> dp(soLuongMask, INT_MAX);
    dp[0] = 0;

    for (int mask = 1; mask < soLuongMask; mask++) {
        for (int submask = mask; submask > 0; submask = (submask - 1) & mask) {
            if (laTapHopLe[submask] == true) {
                int maskConLai = mask ^ submask;
                if (dp[maskConLai] != INT_MAX) {
                    int giaTriUngCu = dp[maskConLai] + 1;
                    if (giaTriUngCu < dp[mask]) {
                        dp[mask] = giaTriUngCu;
                    }
                }
            }
        }
    }

    return dp[soLuongMask - 1];
}

int main() {
    vector<int> soThanhVien = {2, 3, 1, 4};
    vector<pair<int,int>> capXungKhac = {{0, 1}};
    int sucChua = 5;
    int n = 4;

    cout << giaiBaiToanChiaPhong(soThanhVien, capXungKhac, sucChua, n) << endl;

    return 0;
}
```

#### PYTHON
```python
def giai_bai_toan_chia_phong(so_thanh_vien, cap_xung_khac, suc_chua, n):
    so_luong_mask = 1 << n

    la_tap_hop_le = [True] * so_luong_mask
    tong_thanh_vien = [0] * so_luong_mask

    for mask in range(1, so_luong_mask):
        bit_thap_nhat = mask & (-mask)
        vi_tri_bit = bit_thap_nhat.bit_length() - 1
        tong_thanh_vien[mask] = tong_thanh_vien[mask ^ bit_thap_nhat] + so_thanh_vien[vi_tri_bit]

        if tong_thanh_vien[mask] > suc_chua:
            la_tap_hop_le[mask] = False

    for nhom_a, nhom_b in cap_xung_khac:
        for mask in range(so_luong_mask):
            co_a = (mask >> nhom_a) & 1
            co_b = (mask >> nhom_b) & 1
            if co_a == 1 and co_b == 1:
                la_tap_hop_le[mask] = False

    dp = [float('inf')] * so_luong_mask
    dp[0] = 0

    for mask in range(1, so_luong_mask):
        submask = mask
        while submask > 0:
            if la_tap_hop_le[submask] == True:
                mask_con_lai = mask ^ submask
                if dp[mask_con_lai] != float('inf'):
                    gia_tri_ung_cu = dp[mask_con_lai] + 1
                    if gia_tri_ung_cu < dp[mask]:
                        dp[mask] = gia_tri_ung_cu
            submask = (submask - 1) & mask

    return dp[so_luong_mask - 1]


so_thanh_vien = [2, 3, 1, 4]
cap_xung_khac = [(0, 1)]
suc_chua = 5
n = 4

print(giai_bai_toan_chia_phong(so_thanh_vien, cap_xung_khac, suc_chua, n))
```

### Ví dụ 1, 2, 3 và bài tập minh hoạ cho mục này

Vì mục 9.4 chủ yếu là sự **kết hợp** của các kỹ thuật đã học chi tiết ở mục 9.1-9.3 (Tree DP, Rerooting, Bitmask DP) với ràng buộc đồ thị, phần bài tập cuối chương (Bài 13-16) sẽ đóng vai trò là các "Ví dụ" mở rộng bổ sung cho mục này — bao gồm bài toán TSP có ràng buộc đường đi (không phải đồ thị đầy đủ), bài toán tô màu đồ thị nhỏ bằng bitmask, và bài toán ghép cặp tối ưu kết hợp cấu trúc cây.

---

## Tổng kết Chương 9

- **Tree DP:** trạng thái DP gắn với từng đỉnh của cây, luôn tính theo thứ tự duyệt **post-order** (con trước, cha sau) — nền tảng là DFS quen thuộc từ các chương trước, nhưng thêm bước "kết hợp kết quả của các con" khi quay lui.
- **Rerooting (đổi gốc):** kỹ thuật giúp tính kết quả Tree DP cho **tất cả** các đỉnh làm gốc chỉ trong 2 lượt DFS (O(n)), thay vì chạy lại DFS cho từng đỉnh (O(n²)) — chìa khoá là tìm công thức toán học biểu diễn "kết quả thay đổi như thế nào khi đổi gốc từ cha sang con".
- **Bitmask DP:** dùng khi n nhỏ (thường n ≤ 20-22) và trạng thái cần biểu diễn "tập hợp phần tử nào đã được xử lý" — biến bài toán từ O(n!) hoặc O(2^n × 2^n) xuống O(2^n × n) hoặc O(2^n × n²), tuỳ bài toán cụ thể.
- **Kỹ thuật duyệt submask** (`for submask = mask; submask > 0; submask = (submask-1) & mask`) là công cụ quan trọng khi bài toán cần xét mọi cách chia một tập hợp thành các tập con nhỏ hơn — tổng độ phức tạp qua toàn bộ chương trình là O(3^n), không phải O(4^n).
- **Kỹ năng cốt lõi cần luyện:** với bài toán trên cây, luôn tự hỏi "trạng thái DP tại một đỉnh phụ thuộc vào điều gì từ các con của nó?" trước khi viết code. Với bài toán cần bitmask, luôn kiểm tra giới hạn n trong đề — nếu n ≤ 20, đây gần như là tín hiệu chắc chắn cần dùng Bitmask DP.

---

## Bài tập cuối chương — Chương 9

### PHẦN A: ĐỀ BÀI (16 bài, không kèm lời giải)

**Bài 1 — Tree Diameter (CSES 1131) — Trung bình**
Giống hệt Ví dụ 2, mục 9.1 đã trình bày (dùng để luyện tập lại).

**Bài 2 — Tree Matching (CSES 1130) — Trung bình**
Cho một cây, tìm số cặp ghép tối đa (mỗi cạnh được chọn phải không chung đỉnh với cạnh khác được chọn) — biến thể của Tree DP với 2 trạng thái tương tự Independent Set.

**Bài 3 — Binary Tree Maximum Path Sum (LeetCode 124) — Khó**
Cho cây nhị phân có giá trị tại mỗi đỉnh (có thể âm), tìm tổng giá trị lớn nhất của một đường đi bất kỳ trong cây (đường đi không nhất thiết đi qua gốc).

**Bài 4 — House Robber III (LeetCode 337) — Trung bình**
Biến thể House Robber (Chương 8) trên cấu trúc cây nhị phân thay vì mảng — không được trộm 2 nhà cha-con liền kề.

**Bài 5 — Sum of Distances in Tree (LeetCode 834) — Khó**
Giống hệt code khung mẫu, mục 9.2 (Rerooting) đã trình bày (dùng để luyện tập lại).

**Bài 6 — Diameter of N-Ary Tree with Rerooting (dạng mở rộng) — Khó**
Cho một cây, với mỗi đỉnh, hãy tìm khoảng cách xa nhất từ đỉnh đó tới một đỉnh bất kỳ khác. Giống hệt Ví dụ 3, mục 9.2 đã trình bày (dùng để luyện tập lại).

**Bài 7 — Subsets (LeetCode 78, xem lại với góc nhìn Bitmask) — Dễ**
Dùng bitmask (thay vì backtracking như Chương 7) để liệt kê tất cả các tập con của một tập hợp cho trước.

**Bài 8 — Partition to K Equal Sum Subsets (LeetCode 698) — Trung bình-Khó**
Cho mảng số nguyên và số k, kiểm tra xem có thể chia mảng thành k tập con có tổng bằng nhau hay không. (Gợi ý: dùng Bitmask DP tương tự Ví dụ 2, mục 9.3.)

**Bài 9 — Shortest Path Visiting All Nodes (LeetCode 847) — Khó**
Cho một đồ thị vô hướng liên thông, tìm độ dài đường đi ngắn nhất sao cho đi qua **tất cả** các đỉnh (được phép đi qua 1 đỉnh nhiều lần, không bắt buộc quay về điểm xuất phát). (Gợi ý: đây là biến thể TSP dùng BFS kết hợp Bitmask thay vì DP thuần.)

**Bài 10 — Traveling Salesman Problem cơ bản (CSES 1690 - Hamiltonian Flights) — Khó**
Giống hệt Ví dụ 3, mục 9.3 đã trình bày, nhưng không bắt buộc quay lại điểm xuất phát — chỉ cần thăm đủ tất cả các đỉnh theo đúng hướng các cạnh cho phép (đồ thị có hướng, không phải đồ thị đầy đủ như bài toán gốc).

**Bài 11 — Minimum Cost to Connect Two Groups of Points (LeetCode 1595) — Khó**
Cho 2 nhóm điểm, chi phí kết nối giữa mọi cặp điểm (1 điểm nhóm A, 1 điểm nhóm B) cho trước. Mỗi điểm ở cả 2 nhóm đều phải được kết nối với ít nhất 1 điểm ở nhóm còn lại. Tìm tổng chi phí nhỏ nhất. (Gợi ý: Bitmask DP theo "tập điểm nhóm B đã được kết nối".)

**Bài 12 — Can I Win (LeetCode 464) — Trung bình-Khó**
Trò chơi 2 người, lần lượt chọn số từ 1 đến n (mỗi số chỉ được chọn 1 lần), ai đạt tổng ≥ target trước thì thắng. Kiểm tra người đi trước có chiến thắng nếu chơi tối ưu hay không. (Gợi ý: Bitmask DP theo "tập số đã được chọn".)

**Bài 13 — Bài toán chia phòng thi có ràng buộc xung khắc (tự thiết kế, mở rộng) — Khó**
Giống hệt code khung mẫu, mục 9.4 đã trình bày (dùng để luyện tập lại, hoặc thử với bộ dữ liệu lớn hơn).

**Bài 14 — Tô màu đồ thị nhỏ bằng Bitmask (Graph Coloring, tự thiết kế) — Khó**
Cho đồ thị n đỉnh (n ≤ 15) và số màu k, đếm số cách tô màu hợp lệ (2 đỉnh kề nhau không cùng màu) bằng Bitmask DP — với mỗi bước, `dp[mask]` = số cách tô cho tập đỉnh `mask` là một "lớp màu" độc lập hợp lệ (không có cạnh nối 2 đỉnh trong cùng mask).

**Bài 15 — Ghép cặp tối ưu trên cây (Tree DP kết hợp trạng thái ghép cặp, tự thiết kế) — Khó**
Cho một cây có trọng số trên cạnh, tìm cách ghép các đỉnh thành từng cặp (mỗi cặp phải nối trực tiếp bằng 1 cạnh, không phải mọi đỉnh đều cần được ghép) sao cho tổng trọng số các cặp được ghép là lớn nhất. (Đây là bài toán Maximum Weight Matching trên cây, dùng Tree DP với 2 trạng thái tương tự Independent Set.)

**Bài 16 — Bài toán tổng hợp: TSP với ràng buộc thời gian mở cửa (tự thiết kế, dạng đề thi HSG/ICPC) — Khó**
Mở rộng bài toán TSP cơ bản: mỗi thành phố `i` chỉ có thể ghé thăm trong khung giờ `[mo[i], dong[i]]`. Hãy tìm hành trình thăm hết các thành phố với tổng thời gian di chuyển nhỏ nhất, tuân thủ ràng buộc khung giờ (nếu tới sớm hơn `mo[i]`, phải chờ đến đúng giờ mới được bắt đầu công việc tại đó).

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Tree Diameter</summary>

Giống hệt Ví dụ 2, mục 9.1, hàm `dfsTinhChieuCao`/`dfs_tinh_chieu_cao`.
</details>

<details>
<summary>Lời giải Bài 2 — Tree Matching</summary>

**Ý tưởng:** `dp[u][0]` = số cặp ghép tối đa trong cây con gốc `u`, khi `u` **không** được ghép với con nào của nó. `dp[u][1]` = số cặp ghép tối đa khi `u` **có thể** ghép với 1 trong các con (nhưng không bắt buộc).

#### C++
```cpp
vector<vector<int>> danhSachKe;
vector<array<int,2>> dp;

void dfsTreeMatching(int u, int cha) {
    dp[u][0] = 0;
    dp[u][1] = 0;
    int loiIchGhepTotNhat = 0; // lợi ích tăng thêm nếu ghép u với 1 con cụ thể

    for (int con : danhSachKe[u]) {
        if (con == cha) continue;
        dfsTreeMatching(con, u);

        // Mặc định: không ghép u với con này, lấy giá trị tốt nhất của con (có/không ghép trong nhánh con)
        int giaTriTotNhatCuaCon = max(dp[con][0], dp[con][1]);
        dp[u][0] += giaTriTotNhatCuaCon;
        dp[u][1] += giaTriTotNhatCuaCon;

        // Thử ghép u với con này: được thêm 1 cặp, nhưng con đó không được ghép với cháu của nó nữa
        int loiIchNeuGhepConNay = 1 + dp[con][0] - giaTriTotNhatCuaCon;
        if (loiIchNeuGhepConNay > loiIchGhepTotNhat) {
            loiIchGhepTotNhat = loiIchNeuGhepConNay;
        }
    }

    dp[u][1] = dp[u][1] + loiIchGhepTotNhat;
}
```

#### PYTHON
```python
danh_sach_ke = []
dp = []


def dfs_tree_matching(u, cha):
    dp[u][0] = 0
    dp[u][1] = 0
    loi_ich_ghep_tot_nhat = 0

    for con in danh_sach_ke[u]:
        if con == cha:
            continue
        dfs_tree_matching(con, u)

        gia_tri_tot_nhat_cua_con = max(dp[con][0], dp[con][1])
        dp[u][0] += gia_tri_tot_nhat_cua_con
        dp[u][1] += gia_tri_tot_nhat_cua_con

        loi_ich_neu_ghep_con_nay = 1 + dp[con][0] - gia_tri_tot_nhat_cua_con
        if loi_ich_neu_ghep_con_nay > loi_ich_ghep_tot_nhat:
            loi_ich_ghep_tot_nhat = loi_ich_neu_ghep_con_nay

    dp[u][1] = dp[u][1] + loi_ich_ghep_tot_nhat
```
</details>

<details>
<summary>Lời giải Bài 3 — Binary Tree Maximum Path Sum</summary>

**Ý tưởng:** tương tự Tree Diameter (Bài 1), nhưng thay vì cộng chiều cao, ta cộng giá trị đỉnh, và chỉ lấy nhánh có giá trị dương (bỏ qua nhánh âm vì không có lợi).

#### C++
```cpp
long long ketQuaTotNhat = LLONG_MIN;

long long dfsTinhDuongDiTotNhat(TreeNode* node) {
    if (node == nullptr) return 0;

    long long benTrai = max(0LL, dfsTinhDuongDiTotNhat(node->left));
    long long benPhai = max(0LL, dfsTinhDuongDiTotNhat(node->right));

    long long duongDiQuaNode = (long long)node->val + benTrai + benPhai;
    if (duongDiQuaNode > ketQuaTotNhat) {
        ketQuaTotNhat = duongDiQuaNode;
    }

    return (long long)node->val + max(benTrai, benPhai);
}
```

#### PYTHON
```python
ket_qua_tot_nhat = [float('-inf')]


def dfs_tinh_duong_di_tot_nhat(node):
    if node is None:
        return 0

    ben_trai = max(0, dfs_tinh_duong_di_tot_nhat(node.left))
    ben_phai = max(0, dfs_tinh_duong_di_tot_nhat(node.right))

    duong_di_qua_node = node.val + ben_trai + ben_phai
    if duong_di_qua_node > ket_qua_tot_nhat[0]:
        ket_qua_tot_nhat[0] = duong_di_qua_node

    return node.val + max(ben_trai, ben_phai)
```
</details>

<details>
<summary>Lời giải Bài 4 — House Robber III</summary>

Giống hệt cấu trúc Independent Set (mục 9.1), áp dụng trên cây nhị phân: `dp[node][0]` (không trộm), `dp[node][1]` (có trộm).

#### C++
```cpp
pair<long long,long long> dfsRobTree(TreeNode* node) {
    if (node == nullptr) return {0, 0};

    auto trai = dfsRobTree(node->left);
    auto phai = dfsRobTree(node->right);

    long long khongTrom = max(trai.first, trai.second) + max(phai.first, phai.second);
    long long coTrom = (long long)node->val + trai.first + phai.first;

    return {khongTrom, coTrom};
}
```

#### PYTHON
```python
def dfs_rob_tree(node):
    if node is None:
        return (0, 0)

    trai = dfs_rob_tree(node.left)
    phai = dfs_rob_tree(node.right)

    khong_trom = max(trai[0], trai[1]) + max(phai[0], phai[1])
    co_trom = node.val + trai[0] + phai[0]

    return (khong_trom, co_trom)
```
</details>

<details>
<summary>Lời giải Bài 5 — Sum of Distances in Tree</summary>

Giống hệt code khung mẫu, mục 9.2, hàm `dfsBuoc1`/`dfsBuoc2`.
</details>

<details>
<summary>Lời giải Bài 6 — Diameter with Rerooting</summary>

Giống hệt Ví dụ 3, mục 9.2, hàm `dfsBuoc1`/`dfsBuoc2` (phiên bản có trọng số cạnh).
</details>

<details>
<summary>Lời giải Bài 7 — Subsets bằng Bitmask</summary>

#### C++
```cpp
vector<vector<int>> subsetsBangBitmask(vector<int> a) {
    int n = (int)a.size();
    int soLuongMask = 1 << n;
    vector<vector<int>> ketQua;

    for (int mask = 0; mask < soLuongMask; mask++) {
        vector<int> tapConHienTai;
        for (int i = 0; i < n; i++) {
            if ((mask >> i) & 1) {
                tapConHienTai.push_back(a[i]);
            }
        }
        ketQua.push_back(tapConHienTai);
    }

    return ketQua;
}
```

#### PYTHON
```python
def subsets_bang_bitmask(a):
    n = len(a)
    so_luong_mask = 1 << n
    ket_qua = []

    for mask in range(so_luong_mask):
        tap_con_hien_tai = []
        for i in range(n):
            if (mask >> i) & 1:
                tap_con_hien_tai.append(a[i])
        ket_qua.append(tap_con_hien_tai)

    return ket_qua
```
</details>

<details>
<summary>Lời giải Bài 8 — Partition to K Equal Sum Subsets</summary>

**Ý tưởng:** Tổng phải chia hết cho k, mỗi phần bằng `tong/k`. Dùng `dp[mask]` lưu "tổng hiện tại của nhóm đang xây dựng" (dạng phần dư so với target) cho tập `mask` đã dùng.

#### C++
```cpp
bool canPartitionKSubsets(vector<int> a, int k) {
    int n = (int)a.size();
    long long tongTatCa = 0;
    for (int x : a) tongTatCa += x;

    if (tongTatCa % k != 0) return false;
    long long target = tongTatCa / k;

    int soLuongMask = 1 << n;
    vector<int> dp(soLuongMask, -1); // dp[mask] = tổng hiện tại của "nhóm đang xây" modulo target

    dp[0] = 0;
    for (int mask = 0; mask < soLuongMask; mask++) {
        if (dp[mask] == -1) continue;

        for (int i = 0; i < n; i++) {
            if ((mask >> i) & 1) continue; // đã dùng phần tử i
            if (dp[mask] + a[i] > target) continue; // vượt quá target cho nhóm hiện tại

            int maskMoi = mask | (1 << i);
            if (dp[maskMoi] == -1) {
                dp[maskMoi] = (dp[mask] + a[i]) % target;
            }
        }
    }

    return dp[soLuongMask - 1] == 0;
}
```

#### PYTHON
```python
def can_partition_k_subsets(a, k):
    n = len(a)
    tong_tat_ca = sum(a)

    if tong_tat_ca % k != 0:
        return False
    target = tong_tat_ca // k

    so_luong_mask = 1 << n
    dp = [-1] * so_luong_mask
    dp[0] = 0

    for mask in range(so_luong_mask):
        if dp[mask] == -1:
            continue

        for i in range(n):
            if (mask >> i) & 1:
                continue
            if dp[mask] + a[i] > target:
                continue

            mask_moi = mask | (1 << i)
            if dp[mask_moi] == -1:
                dp[mask_moi] = (dp[mask] + a[i]) % target

    return dp[so_luong_mask - 1] == 0
```
</details>

<details>
<summary>Lời giải Bài 9 — Shortest Path Visiting All Nodes</summary>

**Ý tưởng:** dùng BFS trên trạng thái (đỉnh hiện tại, tập đỉnh đã thăm) — mỗi trạng thái là 1 nút trong đồ thị trạng thái mở rộng, khoảng cách BFS chính là số bước ít nhất.

#### C++
```cpp
int shortestPathAllNodes(vector<vector<int>> adj, int n) {
    int soLuongMask = 1 << n;
    vector<vector<int>> khoangCach(soLuongMask, vector<int>(n, -1));
    queue<pair<int,int>> hangDoi; // (mask, dinh)

    for (int i = 0; i < n; i++) {
        int maskBanDau = 1 << i;
        khoangCach[maskBanDau][i] = 0;
        hangDoi.push({maskBanDau, i});
    }

    int maskDayDu = soLuongMask - 1;

    while (!hangDoi.empty()) {
        auto [mask, u] = hangDoi.front(); hangDoi.pop();

        if (mask == maskDayDu) {
            return khoangCach[mask][u];
        }

        for (int v : adj[u]) {
            int maskMoi = mask | (1 << v);
            if (khoangCach[maskMoi][v] == -1) {
                khoangCach[maskMoi][v] = khoangCach[mask][u] + 1;
                hangDoi.push({maskMoi, v});
            }
        }
    }

    return -1;
}
```

#### PYTHON
```python
from collections import deque


def shortest_path_all_nodes(adj, n):
    so_luong_mask = 1 << n
    khoang_cach = [[-1] * n for _ in range(so_luong_mask)]
    hang_doi = deque()

    for i in range(n):
        mask_ban_dau = 1 << i
        khoang_cach[mask_ban_dau][i] = 0
        hang_doi.append((mask_ban_dau, i))

    mask_day_du = so_luong_mask - 1

    while len(hang_doi) > 0:
        mask, u = hang_doi.popleft()

        if mask == mask_day_du:
            return khoang_cach[mask][u]

        for v in adj[u]:
            mask_moi = mask | (1 << v)
            if khoang_cach[mask_moi][v] == -1:
                khoang_cach[mask_moi][v] = khoang_cach[mask][u] + 1
                hang_doi.append((mask_moi, v))

    return -1
```
</details>

<details>
<summary>Lời giải Bài 10 — Hamiltonian Flights</summary>

Giống hệt Ví dụ 3, mục 9.3 (TSP), nhưng: (a) chỉ cộng `khoangCach[u][v]` nếu tồn tại cạnh từ u đến v (đồ thị không đầy đủ), (b) không cần cộng thêm quãng đường quay về điểm xuất phát ở cuối.

#### C++
```cpp
long long hamiltonianFlights(vector<vector<long long>> chiPhiCanh, int n) {
    // chiPhiCanh[u][v] = -1 nếu không có cạnh trực tiếp từ u đến v
    int soLuongMask = 1 << n;
    vector<vector<long long>> dp(soLuongMask, vector<long long>(n, LLONG_MAX));
    dp[1][0] = 0;

    for (int mask = 1; mask < soLuongMask; mask++) {
        for (int u = 0; u < n; u++) {
            if (dp[mask][u] == LLONG_MAX) continue;
            if (!((mask >> u) & 1)) continue;

            for (int v = 0; v < n; v++) {
                if ((mask >> v) & 1) continue;
                if (chiPhiCanh[u][v] == -1) continue;

                int maskMoi = mask | (1 << v);
                long long ungCu = dp[mask][u] + chiPhiCanh[u][v];
                if (ungCu < dp[maskMoi][v]) {
                    dp[maskMoi][v] = ungCu;
                }
            }
        }
    }

    long long ketQua = LLONG_MAX;
    int maskDayDu = soLuongMask - 1;
    for (int u = 0; u < n; u++) {
        if (dp[maskDayDu][u] < ketQua) {
            ketQua = dp[maskDayDu][u];
        }
    }

    return (ketQua == LLONG_MAX) ? -1 : ketQua;
}
```

#### PYTHON
```python
def hamiltonian_flights(chi_phi_canh, n):
    so_luong_mask = 1 << n
    dp = [[float('inf')] * n for _ in range(so_luong_mask)]
    dp[1][0] = 0

    for mask in range(1, so_luong_mask):
        for u in range(n):
            if dp[mask][u] == float('inf'):
                continue
            if not ((mask >> u) & 1):
                continue

            for v in range(n):
                if (mask >> v) & 1:
                    continue
                if chi_phi_canh[u][v] == -1:
                    continue

                mask_moi = mask | (1 << v)
                ung_cu = dp[mask][u] + chi_phi_canh[u][v]
                if ung_cu < dp[mask_moi][v]:
                    dp[mask_moi][v] = ung_cu

    mask_day_du = so_luong_mask - 1
    ket_qua = min(dp[mask_day_du])

    return -1 if ket_qua == float('inf') else ket_qua
```
</details>

<details>
<summary>Lời giải Bài 11 — Minimum Cost to Connect Two Groups</summary>

**Ý tưởng:** `dp[i][mask]` = chi phí nhỏ nhất khi đã xét `i` điểm đầu của nhóm A, và tập điểm nhóm B đã được kết nối là `mask`. Với mỗi điểm A thứ `i`, thử kết nối với từng điểm B, đồng thời cuối cùng phải đảm bảo mọi điểm B chưa được kết nối bởi điểm A nào phải được kết nối bù bởi 1 điểm A bất kỳ (chọn chi phí nhỏ nhất).

#### C++
```cpp
int connectTwoGroups(vector<vector<int>> chiPhi) {
    int n = (int)chiPhi.size();     // số điểm nhóm A
    int m = (int)chiPhi[0].size();  // số điểm nhóm B
    int soLuongMaskB = 1 << m;

    vector<int> chiPhiNhoNhatChoDiemB(m, INT_MAX);
    for (int j = 0; j < m; j++) {
        for (int i = 0; i < n; i++) {
            chiPhiNhoNhatChoDiemB[j] = min(chiPhiNhoNhatChoDiemB[j], chiPhi[i][j]);
        }
    }

    vector<vector<int>> dp(n + 1, vector<int>(soLuongMaskB, INT_MAX));
    dp[0][0] = 0;

    for (int i = 0; i < n; i++) {
        for (int mask = 0; mask < soLuongMaskB; mask++) {
            if (dp[i][mask] == INT_MAX) continue;

            for (int j = 0; j < m; j++) {
                int maskMoi = mask | (1 << j);
                int ungCu = dp[i][mask] + chiPhi[i][j];
                if (ungCu < dp[i + 1][maskMoi]) {
                    dp[i + 1][maskMoi] = ungCu;
                }
            }
        }
    }

    int ketQua = INT_MAX;
    int maskDayDu = soLuongMaskB - 1;
    for (int mask = 0; mask < soLuongMaskB; mask++) {
        if (dp[n][mask] == INT_MAX) continue;

        int chiPhiBu = 0;
        for (int j = 0; j < m; j++) {
            if (!((mask >> j) & 1)) {
                chiPhiBu += chiPhiNhoNhatChoDiemB[j];
            }
        }

        ketQua = min(ketQua, dp[n][mask] + chiPhiBu);
    }

    return ketQua;
}
```

#### PYTHON
```python
def connect_two_groups(chi_phi):
    n = len(chi_phi)
    m = len(chi_phi[0])
    so_luong_mask_b = 1 << m

    chi_phi_nho_nhat_cho_diem_b = [min(chi_phi[i][j] for i in range(n)) for j in range(m)]

    dp = [[float('inf')] * so_luong_mask_b for _ in range(n + 1)]
    dp[0][0] = 0

    for i in range(n):
        for mask in range(so_luong_mask_b):
            if dp[i][mask] == float('inf'):
                continue

            for j in range(m):
                mask_moi = mask | (1 << j)
                ung_cu = dp[i][mask] + chi_phi[i][j]
                if ung_cu < dp[i + 1][mask_moi]:
                    dp[i + 1][mask_moi] = ung_cu

    ket_qua = float('inf')
    mask_day_du = so_luong_mask_b - 1
    for mask in range(so_luong_mask_b):
        if dp[n][mask] == float('inf'):
            continue

        chi_phi_bu = 0
        for j in range(m):
            if not ((mask >> j) & 1):
                chi_phi_bu += chi_phi_nho_nhat_cho_diem_b[j]

        ket_qua = min(ket_qua, dp[n][mask] + chi_phi_bu)

    return ket_qua
```
</details>

<details>
<summary>Lời giải Bài 12 — Can I Win</summary>

**Ý tưởng:** `dp[mask]` = true nếu người **đang đến lượt** có thể thắng, với `mask` là tập số đã dùng. Duyệt thử mọi số chưa dùng, nếu chọn số đó khiến đạt target hoặc khiến đối thủ ở trạng thái thua, thì người hiện tại thắng.

#### C++
```cpp
unordered_map<int,bool> ghiNho;
int nGlobal, targetGlobal;

bool dfsCanIWin(int mask, int tongHienTai) {
    if (ghiNho.count(mask)) return ghiNho[mask];

    for (int i = 1; i <= nGlobal; i++) {
        if ((mask >> i) & 1) continue;

        if (tongHienTai + i >= targetGlobal) {
            ghiNho[mask] = true;
            return true;
        }

        if (dfsCanIWin(mask | (1 << i), tongHienTai + i) == false) {
            ghiNho[mask] = true;
            return true;
        }
    }

    ghiNho[mask] = false;
    return false;
}

bool canIWin(int maxChoiceableInteger, int desiredTotal) {
    nGlobal = maxChoiceableInteger;
    targetGlobal = desiredTotal;

    if (desiredTotal <= 0) return true;

    long long tongTatCa = (long long)maxChoiceableInteger * (maxChoiceableInteger + 1) / 2;
    if (tongTatCa < desiredTotal) return false;

    ghiNho.clear();
    return dfsCanIWin(0, 0);
}
```

#### PYTHON
```python
ghi_nho = {}
n_global = 0
target_global = 0


def dfs_can_i_win(mask, tong_hien_tai):
    if mask in ghi_nho:
        return ghi_nho[mask]

    for i in range(1, n_global + 1):
        if (mask >> i) & 1:
            continue

        if tong_hien_tai + i >= target_global:
            ghi_nho[mask] = True
            return True

        if dfs_can_i_win(mask | (1 << i), tong_hien_tai + i) == False:
            ghi_nho[mask] = True
            return True

    ghi_nho[mask] = False
    return False


def can_i_win(max_choiceable_integer, desired_total):
    global n_global, target_global
    n_global = max_choiceable_integer
    target_global = desired_total

    if desired_total <= 0:
        return True

    tong_tat_ca = max_choiceable_integer * (max_choiceable_integer + 1) // 2
    if tong_tat_ca < desired_total:
        return False

    ghi_nho.clear()
    return dfs_can_i_win(0, 0)
```
</details>

<details>
<summary>Lời giải Bài 13 — Chia phòng thi có ràng buộc xung khắc</summary>

Giống hệt code khung mẫu, mục 9.4, hàm `giaiBaiToanChiaPhong`/`giai_bai_toan_chia_phong`.
</details>

<details>
<summary>Lời giải Bài 14 — Tô màu đồ thị bằng Bitmask</summary>

**Ý tưởng:** Bước 1, xác định các "tập độc lập" hợp lệ (mask không chứa cạnh nối 2 đỉnh trong đó) — đây là các ứng viên cho "1 lớp màu". Bước 2, `dp[mask][soMauDaDung]` = có thể tô hết tập `mask` bằng đúng `soMauDaDung` lớp màu hay không — dùng kỹ thuật tương tự bài chia phòng (Bài 13).

#### C++
```cpp
bool laTapDocLap(int mask, vector<vector<int>> &keMask) {
    // keMask[i] = bitmask các đỉnh kề với đỉnh i
    for (int i = 0; i < (int)keMask.size(); i++) {
        if (!((mask >> i) & 1)) continue;
        if ((mask & keMask[i]) != 0) return false; // có đỉnh kề i cũng nằm trong mask
    }
    return true;
}

long long demSoCachToMau(vector<vector<int>> keMask, int k, int n) {
    const long long MOD = 1000000007;
    int soLuongMask = 1 << n;

    vector<bool> docLap(soLuongMask, false);
    for (int mask = 0; mask < soLuongMask; mask++) {
        docLap[mask] = laTapDocLap(mask, keMask);
    }

    // dp[soMau][mask] = số cách tô đúng "mask" bằng đúng "soMau" lớp màu (có phân biệt thứ tự màu)
    vector<vector<long long>> dp(k + 1, vector<long long>(soLuongMask, 0));
    dp[0][0] = 1;

    for (int soMau = 1; soMau <= k; soMau++) {
        for (int mask = 0; mask < soLuongMask; mask++) {
            for (int submask = mask; ; submask = (submask - 1) & mask) {
                if (docLap[submask] == true) {
                    dp[soMau][mask] = (dp[soMau][mask] + dp[soMau - 1][mask ^ submask]) % MOD;
                }
                if (submask == 0) break;
            }
        }
    }

    return dp[k][soLuongMask - 1];
}
```

#### PYTHON
```python
def la_tap_doc_lap(mask, ke_mask):
    n = len(ke_mask)
    for i in range(n):
        if not ((mask >> i) & 1):
            continue
        if (mask & ke_mask[i]) != 0:
            return False
    return True


def dem_so_cach_to_mau(ke_mask, k, n):
    MOD = 1000000007
    so_luong_mask = 1 << n

    doc_lap = [la_tap_doc_lap(mask, ke_mask) for mask in range(so_luong_mask)]

    dp = [[0] * so_luong_mask for _ in range(k + 1)]
    dp[0][0] = 1

    for so_mau in range(1, k + 1):
        for mask in range(so_luong_mask):
            submask = mask
            while True:
                if doc_lap[submask] == True:
                    dp[so_mau][mask] = (dp[so_mau][mask] + dp[so_mau - 1][mask ^ submask]) % MOD
                if submask == 0:
                    break
                submask = (submask - 1) & mask

    return dp[k][so_luong_mask - 1]
```
</details>

<details>
<summary>Lời giải Bài 15 — Ghép cặp tối ưu trên cây</summary>

**Ý tưởng:** `dp[u][0]` = trọng số lớn nhất trong cây con gốc `u`, khi `u` **chưa được ghép**. `dp[u][1]` = trọng số lớn nhất khi `u` **đã được ghép** (với 1 trong các con của nó). Với mỗi con, thử ghép `u` với con đó (cộng trọng số cạnh, con đó không được ghép với cháu), hoặc không ghép (lấy giá trị tốt nhất của con dù ghép hay không).

#### C++
```cpp
vector<vector<pair<int,int>>> danhSachKe; // (đỉnh kề, trọng số)
vector<array<long long,2>> dp;

void dfsGhepCapToiUu(int u, int cha) {
    dp[u][0] = 0;
    dp[u][1] = 0;
    long long loiIchGhepTotNhat = 0;

    for (auto &canh : danhSachKe[u]) {
        int con = canh.first;
        int trongSoCanh = canh.second;
        if (con == cha) continue;

        dfsGhepCapToiUu(con, u);

        long long giaTriTotNhatCuaCon = max(dp[con][0], dp[con][1]);
        dp[u][0] += giaTriTotNhatCuaCon;
        dp[u][1] += giaTriTotNhatCuaCon;

        long long loiIchNeuGhepConNay = (long long)trongSoCanh + dp[con][0] - giaTriTotNhatCuaCon;
        if (loiIchNeuGhepConNay > loiIchGhepTotNhat) {
            loiIchGhepTotNhat = loiIchNeuGhepConNay;
        }
    }

    dp[u][1] = dp[u][1] + loiIchGhepTotNhat;
}
```

#### PYTHON
```python
danh_sach_ke = []
dp = []


def dfs_ghep_cap_toi_uu(u, cha):
    dp[u][0] = 0
    dp[u][1] = 0
    loi_ich_ghep_tot_nhat = 0

    for con, trong_so_canh in danh_sach_ke[u]:
        if con == cha:
            continue

        dfs_ghep_cap_toi_uu(con, u)

        gia_tri_tot_nhat_cua_con = max(dp[con][0], dp[con][1])
        dp[u][0] += gia_tri_tot_nhat_cua_con
        dp[u][1] += gia_tri_tot_nhat_cua_con

        loi_ich_neu_ghep_con_nay = trong_so_canh + dp[con][0] - gia_tri_tot_nhat_cua_con
        if loi_ich_neu_ghep_con_nay > loi_ich_ghep_tot_nhat:
            loi_ich_ghep_tot_nhat = loi_ich_neu_ghep_con_nay

    dp[u][1] = dp[u][1] + loi_ich_ghep_tot_nhat
```
</details>

<details>
<summary>Lời giải Bài 16 — TSP với ràng buộc thời gian mở cửa</summary>

**Ý tưởng:** Mở rộng trực tiếp từ Ví dụ 3, mục 9.3 — trạng thái `dp[mask][u]` giờ đây lưu **thời điểm hoàn thành sớm nhất** khi đứng tại `u` với tập đã thăm là `mask` (thay vì chỉ lưu tổng quãng đường). Khi chuyển sang đỉnh `v`, thời gian đến nơi là `dp[mask][u] + thoiGianDiChuyen[u][v]`; nếu đến sớm hơn `mo[v]`, phải chờ tới `mo[v]` mới coi là "hoàn thành" tại đó — dùng `max(thoiGianDenNoi, mo[v])`. Nếu đến muộn hơn `dong[v]`, trạng thái này không hợp lệ (bỏ qua).

#### C++
```cpp
long long tspVoiKhungGio(vector<vector<long long>> thoiGianDiChuyen, vector<int> moCua, vector<int> dongCua, int n) {
    int soLuongMask = 1 << n;
    vector<vector<long long>> dp(soLuongMask, vector<long long>(n, LLONG_MAX));
    dp[1][0] = max(0, moCua[0]); // xuất phát từ 0, có thể phải chờ tới giờ mở cửa

    for (int mask = 1; mask < soLuongMask; mask++) {
        for (int u = 0; u < n; u++) {
            if (dp[mask][u] == LLONG_MAX) continue;
            if (!((mask >> u) & 1)) continue;

            for (int v = 0; v < n; v++) {
                if ((mask >> v) & 1) continue;

                long long thoiGianDenNoi = dp[mask][u] + thoiGianDiChuyen[u][v];
                if (thoiGianDenNoi > dongCua[v]) {
                    continue; // đến quá muộn, không hợp lệ
                }

                long long thoiGianHoanThanh = max(thoiGianDenNoi, (long long)moCua[v]);

                int maskMoi = mask | (1 << v);
                if (thoiGianHoanThanh < dp[maskMoi][v]) {
                    dp[maskMoi][v] = thoiGianHoanThanh;
                }
            }
        }
    }

    long long ketQua = LLONG_MAX;
    int maskDayDu = soLuongMask - 1;
    for (int u = 0; u < n; u++) {
        if (dp[maskDayDu][u] < ketQua) {
            ketQua = dp[maskDayDu][u];
        }
    }

    return (ketQua == LLONG_MAX) ? -1 : ketQua;
}
```

#### PYTHON
```python
def tsp_voi_khung_gio(thoi_gian_di_chuyen, mo_cua, dong_cua, n):
    so_luong_mask = 1 << n
    dp = [[float('inf')] * n for _ in range(so_luong_mask)]
    dp[1][0] = max(0, mo_cua[0])

    for mask in range(1, so_luong_mask):
        for u in range(n):
            if dp[mask][u] == float('inf'):
                continue
            if not ((mask >> u) & 1):
                continue

            for v in range(n):
                if (mask >> v) & 1:
                    continue

                thoi_gian_den_noi = dp[mask][u] + thoi_gian_di_chuyen[u][v]
                if thoi_gian_den_noi > dong_cua[v]:
                    continue

                thoi_gian_hoan_thanh = max(thoi_gian_den_noi, mo_cua[v])

                mask_moi = mask | (1 << v)
                if thoi_gian_hoan_thanh < dp[mask_moi][v]:
                    dp[mask_moi][v] = thoi_gian_hoan_thanh

    mask_day_du = so_luong_mask - 1
    ket_qua = min(dp[mask_day_du])

    return -1 if ket_qua == float('inf') else ket_qua
```
</details>
