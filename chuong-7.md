# Chương 7: Đệ quy & Quay lui (Backtracking)

---

## 7.1. Tư duy đệ quy và cây trạng thái

**Nêu bài toán:** Cho một số nguyên n, hãy tính giai thừa của n (n! = 1 × 2 × 3 × ... × n).

**Phân tích vấn đề:** Bài toán "tính n!" có thể được định nghĩa dựa trên chính nó ở kích thước nhỏ hơn: `n! = n × (n-1)!`, với điều kiện dừng `0! = 1`. Đây chính là bản chất của **đệ quy** — một hàm gọi lại chính nó với dữ liệu đầu vào nhỏ hơn, cho tới khi đạt tới trường hợp cơ sở (base case) đủ đơn giản để trả lời trực tiếp.

**Giải pháp đơn thuần:** Dùng vòng lặp `for` nhân dồn từ 1 đến n. Cách này đúng nhưng không tổng quát hoá được cho các bài toán có cấu trúc phức tạp hơn (như duyệt cây, sinh tổ hợp).

**Khó khăn với giải pháp đơn thuần:** Nhiều bài toán có cấu trúc "chia nhỏ thành bài toán con giống hệt nhưng kích thước bé hơn" rất khó diễn đạt bằng vòng lặp đơn thuần — ví dụ duyệt qua tất cả các cách chọn tập con của một tập hợp, hoặc duyệt một cấu trúc cây.

**Cách tiếp cận mới:** Viết hàm đệ quy gồm 2 phần bắt buộc: **điều kiện dừng** (base case) — khi nào hàm ngừng gọi lại chính nó và trả về kết quả trực tiếp; và **bước đệ quy** (recursive case) — cách hàm gọi lại chính nó với dữ liệu nhỏ hơn, kèm theo cách kết hợp kết quả của lời gọi con để tạo ra kết quả của bài toán lớn hơn.

### Minh hoạ lời giải chi tiết

Với `tinhGiaiThua(4)`, ta có thể vẽ "cây gọi hàm" như sau:

```
tinhGiaiThua(4)
  = 4 * tinhGiaiThua(3)
        = 3 * tinhGiaiThua(2)
              = 2 * tinhGiaiThua(1)
                    = 1 * tinhGiaiThua(0)
                          = 1  (điều kiện dừng)
                    = 1 * 1 = 1
              = 2 * 1 = 2
        = 3 * 2 = 6
  = 4 * 6 = 24
```

Ta thấy quá trình gọi hàm đi sâu dần xuống cho tới khi gặp điều kiện dừng (`tinhGiaiThua(0) = 1`), sau đó các kết quả được "trả về" và nhân dần lên theo chiều ngược lại.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long tinhGiaiThua(int n) {
    if (n == 0) {
        return 1; // điều kiện dừng
    }
    return (long long)n * tinhGiaiThua(n - 1); // bước đệ quy
}

int main() {
    cout << tinhGiaiThua(4) << endl;
    return 0;
}
```

#### PYTHON
```python
def tinh_giai_thua(n):
    if n == 0:
        return 1  # điều kiện dừng
    return n * tinh_giai_thua(n - 1)  # bước đệ quy


print(tinh_giai_thua(4))
```

### Ví dụ 1 (Dễ) — Tính tổng các chữ số của một số nguyên bằng đệ quy

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int tinhTongChuSo(int n) {
    if (n == 0) {
        return 0;
    }
    int chuSoCuoi = n % 10;
    return chuSoCuoi + tinhTongChuSo(n / 10);
}

int main() {
    cout << tinhTongChuSo(12345) << endl; // 1+2+3+4+5 = 15
    return 0;
}
```

#### PYTHON
```python
def tinh_tong_chu_so(n):
    if n == 0:
        return 0
    chu_so_cuoi = n % 10
    return chu_so_cuoi + tinh_tong_chu_so(n // 10)


print(tinh_tong_chu_so(12345))  # 1+2+3+4+5 = 15
```

### Ví dụ 2 (Trung bình) — Duyệt cây thư mục bằng đệ quy (mô phỏng)

Bài toán: mô phỏng một cấu trúc thư mục lồng nhau (mỗi thư mục có thể chứa file hoặc thư mục con khác), hãy đếm tổng số file trong toàn bộ cây thư mục.

**Phân tích:** Cấu trúc thư mục là một ví dụ tiêu biểu của dữ liệu có tính đệ quy tự nhiên — một thư mục chứa danh sách các "mục", mỗi mục có thể là file (trường hợp cơ sở) hoặc lại là một thư mục khác (bước đệ quy).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

struct MucTin {
    bool laFile;
    vector<MucTin*> cacMucCon; // chỉ có ý nghĩa nếu laFile = false
};

int demSoFile(MucTin* muc) {
    if (muc->laFile == true) {
        return 1; // điều kiện dừng: đây là 1 file
    }

    int tongSoFile = 0;
    for (int i = 0; i < (int)muc->cacMucCon.size(); i++) {
        tongSoFile = tongSoFile + demSoFile(muc->cacMucCon[i]);
    }
    return tongSoFile;
}

int main() {
    MucTin file1; file1.laFile = true;
    MucTin file2; file2.laFile = true;
    MucTin file3; file3.laFile = true;

    MucTin thuMucCon; thuMucCon.laFile = false;
    thuMucCon.cacMucCon.push_back(&file2);
    thuMucCon.cacMucCon.push_back(&file3);

    MucTin thuMucGoc; thuMucGoc.laFile = false;
    thuMucGoc.cacMucCon.push_back(&file1);
    thuMucGoc.cacMucCon.push_back(&thuMucCon);

    cout << demSoFile(&thuMucGoc) << endl; // 3 file

    return 0;
}
```

#### PYTHON
```python
class MucTin:
    def __init__(self, la_file):
        self.la_file = la_file
        self.cac_muc_con = []


def dem_so_file(muc):
    if muc.la_file == True:
        return 1

    tong_so_file = 0
    for muc_con in muc.cac_muc_con:
        tong_so_file = tong_so_file + dem_so_file(muc_con)
    return tong_so_file


file1 = MucTin(True)
file2 = MucTin(True)
file3 = MucTin(True)

thu_muc_con = MucTin(False)
thu_muc_con.cac_muc_con.append(file2)
thu_muc_con.cac_muc_con.append(file3)

thu_muc_goc = MucTin(False)
thu_muc_goc.cac_muc_con.append(file1)
thu_muc_goc.cac_muc_con.append(thu_muc_con)

print(dem_so_file(thu_muc_goc))  # 3 file
```

### Ví dụ 3 (Khó) — Đệ quy không tối ưu và cách nhận biết vấn đề (dãy Fibonacci)

Bài toán: tính số Fibonacci thứ n, với `fib(0) = 0`, `fib(1) = 1`, `fib(n) = fib(n-1) + fib(n-2)`.

**Phân tích:** Nếu cài đặt đệ quy trực tiếp theo đúng công thức, mỗi lời gọi `fib(n)` sinh ra 2 lời gọi con `fib(n-1)` và `fib(n-2)` — nhưng nhiều lời gọi con này bị **tính lại nhiều lần** (ví dụ `fib(n-2)` được tính cả trong nhánh `fib(n-1)` lẫn trực tiếp). Đây là dấu hiệu quan trọng cần nhận biết: khi cây đệ quy có các nhánh lặp lại cùng một bài toán con nhiều lần, độ phức tạp sẽ tăng theo cấp số nhân — cần một kỹ thuật khác (Quy hoạch động, sẽ học ở Chương 8) để "ghi nhớ" các kết quả đã tính, tránh tính lại.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long fibonacciDonGian(int n) {
    if (n <= 1) {
        return n;
    }
    return fibonacciDonGian(n - 1) + fibonacciDonGian(n - 2);
}

int main() {
    cout << fibonacciDonGian(10) << endl; // chạy nhanh
    // cout << fibonacciDonGian(45) << endl; // sẽ RẤT CHẬM vì tính lại quá nhiều lần
    return 0;
}
```

#### PYTHON
```python
def fibonacci_don_gian(n):
    if n <= 1:
        return n
    return fibonacci_don_gian(n - 1) + fibonacci_don_gian(n - 2)


print(fibonacci_don_gian(10))  # chạy nhanh
# print(fibonacci_don_gian(35))  # sẽ RẤT CHẬM vì tính lại quá nhiều lần
```

**Bài tập minh hoạ:** Viết hàm đệ quy tính luỹ thừa `x^n` (với n là số nguyên không âm) mà không dùng vòng lặp.

**Lời giải:** `x^n = x × x^(n-1)`, với điều kiện dừng `x^0 = 1`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long tinhLuyThua(long long x, int n) {
    if (n == 0) {
        return 1;
    }
    return x * tinhLuyThua(x, n - 1);
}

int main() {
    cout << tinhLuyThua(2, 10) << endl; // 1024
    return 0;
}
```

#### PYTHON
```python
def tinh_luy_thua(x, n):
    if n == 0:
        return 1
    return x * tinh_luy_thua(x, n - 1)


print(tinh_luy_thua(2, 10))  # 1024
```

---

## 7.2. Sinh hoán vị, tổ hợp, tập con (Backtracking cơ bản)

**Nêu bài toán:** Cho một tập hợp gồm n phần tử phân biệt, hãy liệt kê **tất cả** các hoán vị (thứ tự sắp xếp) có thể có của tập hợp đó.

**Phân tích vấn đề:** Với n phần tử, số lượng hoán vị là n! — đây không phải là bài toán "tìm 1 giá trị tối ưu" mà là bài toán "liệt kê đầy đủ tất cả các khả năng". Ta cần một kỹ thuật có thể **thử tất cả các lựa chọn** tại mỗi bước, rồi **quay lui** để thử các lựa chọn khác.

**Giải pháp đơn thuần:** Với n rất nhỏ, có thể liệt kê thủ công. Nhưng với n bất kỳ, ta cần một quy trình tự động.

**Khó khăn với giải pháp đơn thuần:** Không có công thức đóng nào để liệt kê trực tiếp — cần một quy trình có hệ thống để duyệt qua từng khả năng mà không bỏ sót và không lặp lại.

**Cách tiếp cận mới — Backtracking (Quay lui):** Xây dựng lời giải từng bước một. Tại mỗi bước, thử tất cả các lựa chọn khả dĩ cho vị trí hiện tại; với mỗi lựa chọn, đệ quy để xây dựng tiếp các bước còn lại; sau khi đệ quy trở về (đã thử hết các khả năng của lựa chọn đó), ta **hoàn tác** lựa chọn (quay lui) để thử lựa chọn khác.

### Minh hoạ lời giải chi tiết

Với tập `{1, 2, 3}`, cây quay lui sinh hoán vị trông như sau (mỗi nhánh là một lựa chọn tại một bước):

```
Bắt đầu: đường_đi = []
├── Chọn 1: đường_đi = [1]
│   ├── Chọn 2: đường_đi = [1, 2]
│   │   └── Chọn 3: đường_đi = [1, 2, 3] -> GHI NHẬN kết quả
│   └── Chọn 3: đường_đi = [1, 3]
│       └── Chọn 2: đường_đi = [1, 3, 2] -> GHI NHẬN kết quả
├── Chọn 2: đường_đi = [2]
│   ├── Chọn 1: đường_đi = [2, 1]
│   │   └── Chọn 3: đường_đi = [2, 1, 3] -> GHI NHẬN kết quả
│   └── Chọn 3: đường_đi = [2, 3]
│       └── Chọn 1: đường_đi = [2, 3, 1] -> GHI NHẬN kết quả
└── Chọn 3: đường_đi = [3]
    ├── Chọn 1: đường_đi = [3, 1]
    │   └── Chọn 2: đường_đi = [3, 1, 2] -> GHI NHẬN kết quả
    └── Chọn 2: đường_đi = [3, 2]
        └── Chọn 1: đường_đi = [3, 2, 1] -> GHI NHẬN kết quả
```

Sau khi thử xong một nhánh (ví dụ `[1, 2, 3]`), ta **quay lui** — bỏ phần tử 3 ra khỏi đường đi (trở về `[1, 2]`), rồi bỏ tiếp phần tử 2 (trở về `[1]`), để thử nhánh khác (`[1, 3]`).

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

void sinhHoanVi(vector<int> &a, vector<int> &duongDi, vector<bool> &daDung, vector<vector<int>> &tatCaKetQua) {
    if ((int)duongDi.size() == (int)a.size()) {
        tatCaKetQua.push_back(duongDi); // đã chọn đủ n phần tử -> ghi nhận kết quả
        return;
    }

    for (int i = 0; i < (int)a.size(); i++) {
        if (daDung[i] == true) {
            continue; // phần tử này đã được dùng trong đường đi hiện tại, bỏ qua
        }

        // Bước "chọn"
        duongDi.push_back(a[i]);
        daDung[i] = true;

        // Bước "đệ quy" để xây dựng tiếp
        sinhHoanVi(a, duongDi, daDung, tatCaKetQua);

        // Bước "quay lui" - hoàn tác lựa chọn vừa thực hiện
        duongDi.pop_back();
        daDung[i] = false;
    }
}

int main() {
    vector<int> a = {1, 2, 3};
    vector<int> duongDi;
    vector<bool> daDung(a.size(), false);
    vector<vector<int>> tatCaKetQua;

    sinhHoanVi(a, duongDi, daDung, tatCaKetQua);

    for (int i = 0; i < (int)tatCaKetQua.size(); i++) {
        for (int j = 0; j < (int)tatCaKetQua[i].size(); j++) {
            cout << tatCaKetQua[i][j] << " ";
        }
        cout << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def sinh_hoan_vi(a, duong_di, da_dung, tat_ca_ket_qua):
    if len(duong_di) == len(a):
        tat_ca_ket_qua.append(list(duong_di))  # ghi nhận bản sao của đường đi hiện tại
        return

    for i in range(len(a)):
        if da_dung[i] == True:
            continue

        duong_di.append(a[i])
        da_dung[i] = True

        sinh_hoan_vi(a, duong_di, da_dung, tat_ca_ket_qua)

        duong_di.pop()
        da_dung[i] = False


a = [1, 2, 3]
duong_di = []
da_dung = [False] * len(a)
tat_ca_ket_qua = []

sinh_hoan_vi(a, duong_di, da_dung, tat_ca_ket_qua)

for ket_qua in tat_ca_ket_qua:
    print(ket_qua)
```

### Ví dụ 1 (Dễ) — Sinh tất cả tập con của một tập hợp

Bài toán: cho tập hợp n phần tử, liệt kê tất cả các tập con có thể có (kể cả tập rỗng và chính tập hợp đó).

**Phân tích:** Với mỗi phần tử, ta có đúng 2 lựa chọn: **chọn** nó vào tập con hiện tại, hoặc **không chọn**. Cây quay lui ở đây có 2 nhánh tại mỗi bước, thay vì n nhánh như bài toán hoán vị.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

void sinhTapCon(vector<int> &a, int chiSoHienTai, vector<int> &duongDi, vector<vector<int>> &tatCaKetQua) {
    if (chiSoHienTai == (int)a.size()) {
        tatCaKetQua.push_back(duongDi); // đã xét hết tất cả phần tử -> ghi nhận
        return;
    }

    // Lựa chọn 1: KHÔNG chọn phần tử này
    sinhTapCon(a, chiSoHienTai + 1, duongDi, tatCaKetQua);

    // Lựa chọn 2: CÓ chọn phần tử này
    duongDi.push_back(a[chiSoHienTai]);
    sinhTapCon(a, chiSoHienTai + 1, duongDi, tatCaKetQua);
    duongDi.pop_back(); // quay lui

    return;
}

int main() {
    vector<int> a = {1, 2, 3};
    vector<int> duongDi;
    vector<vector<int>> tatCaKetQua;

    sinhTapCon(a, 0, duongDi, tatCaKetQua);

    for (int i = 0; i < (int)tatCaKetQua.size(); i++) {
        cout << "{ ";
        for (int j = 0; j < (int)tatCaKetQua[i].size(); j++) {
            cout << tatCaKetQua[i][j] << " ";
        }
        cout << "}" << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def sinh_tap_con(a, chi_so_hien_tai, duong_di, tat_ca_ket_qua):
    if chi_so_hien_tai == len(a):
        tat_ca_ket_qua.append(list(duong_di))
        return

    # Lựa chọn 1: KHÔNG chọn phần tử này
    sinh_tap_con(a, chi_so_hien_tai + 1, duong_di, tat_ca_ket_qua)

    # Lựa chọn 2: CÓ chọn phần tử này
    duong_di.append(a[chi_so_hien_tai])
    sinh_tap_con(a, chi_so_hien_tai + 1, duong_di, tat_ca_ket_qua)
    duong_di.pop()


a = [1, 2, 3]
duong_di = []
tat_ca_ket_qua = []

sinh_tap_con(a, 0, duong_di, tat_ca_ket_qua)

for ket_qua in tat_ca_ket_qua:
    print(ket_qua)
```

### Ví dụ 2 (Trung bình) — Sinh tổ hợp chập k của n phần tử

Bài toán: cho tập hợp n phần tử và số k, liệt kê tất cả các tổ hợp chập k (chọn ra đúng k phần tử, không quan tâm thứ tự, không trùng lặp phần tử).

**Phân tích:** Khác với hoán vị (thứ tự quan trọng), tổ hợp không quan tâm thứ tự — để tránh sinh ra 2 tổ hợp giống nhau nhưng khác thứ tự chọn (ví dụ chọn {1,2} và {2,1} là một), ta quy ước chỉ được chọn phần tử có chỉ số **lớn hơn** chỉ số của phần tử vừa chọn trước đó.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

void sinhToHop(vector<int> &a, int k, int chiSoBatDau, vector<int> &duongDi, vector<vector<int>> &tatCaKetQua) {
    if ((int)duongDi.size() == k) {
        tatCaKetQua.push_back(duongDi);
        return;
    }

    for (int i = chiSoBatDau; i < (int)a.size(); i++) {
        duongDi.push_back(a[i]);
        sinhToHop(a, k, i + 1, duongDi, tatCaKetQua); // chỉ số bắt đầu tiếp theo là i+1, không được lùi lại
        duongDi.pop_back();
    }
}

int main() {
    vector<int> a = {1, 2, 3, 4};
    int k = 2;
    vector<int> duongDi;
    vector<vector<int>> tatCaKetQua;

    sinhToHop(a, k, 0, duongDi, tatCaKetQua);

    for (int i = 0; i < (int)tatCaKetQua.size(); i++) {
        for (int j = 0; j < (int)tatCaKetQua[i].size(); j++) {
            cout << tatCaKetQua[i][j] << " ";
        }
        cout << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def sinh_to_hop(a, k, chi_so_bat_dau, duong_di, tat_ca_ket_qua):
    if len(duong_di) == k:
        tat_ca_ket_qua.append(list(duong_di))
        return

    for i in range(chi_so_bat_dau, len(a)):
        duong_di.append(a[i])
        sinh_to_hop(a, k, i + 1, duong_di, tat_ca_ket_qua)
        duong_di.pop()


a = [1, 2, 3, 4]
k = 2
duong_di = []
tat_ca_ket_qua = []

sinh_to_hop(a, k, 0, duong_di, tat_ca_ket_qua)

for ket_qua in tat_ca_ket_qua:
    print(ket_qua)
```

### Ví dụ 3 (Khó) — Sinh tổ hợp có tổng bằng target (Combination Sum)

Bài toán: cho tập hợp các số nguyên dương (có thể dùng lại mỗi số nhiều lần) và một số target, hãy liệt kê tất cả các tổ hợp (không phân biệt thứ tự) có tổng đúng bằng target.

**Phân tích:** Đây là biến thể của Ví dụ 2, nhưng cho phép **dùng lại cùng một phần tử nhiều lần** (nên khi đệ quy tiếp, chỉ số bắt đầu vẫn là `i`, không phải `i+1`), và cần thêm điều kiện dừng sớm khi tổng đã vượt quá target (đây chính là kỹ thuật **cắt tỉa**, sẽ học chi tiết ở mục 7.3).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

void sinhToHopCoTongBangTarget(vector<int> &a, int chiSoBatDau, int targetConLai,
                                 vector<int> &duongDi, vector<vector<int>> &tatCaKetQua) {
    if (targetConLai == 0) {
        tatCaKetQua.push_back(duongDi);
        return;
    }
    if (targetConLai < 0) {
        return; // cắt tỉa: tổng đã vượt quá target, không cần thử tiếp
    }

    for (int i = chiSoBatDau; i < (int)a.size(); i++) {
        duongDi.push_back(a[i]);
        // Vẫn dùng "i" (không phải i+1) vì được phép dùng lại phần tử này
        sinhToHopCoTongBangTarget(a, i, targetConLai - a[i], duongDi, tatCaKetQua);
        duongDi.pop_back();
    }
}

int main() {
    vector<int> a = {2, 3, 6, 7};
    int target = 7;
    vector<int> duongDi;
    vector<vector<int>> tatCaKetQua;

    sinhToHopCoTongBangTarget(a, 0, target, duongDi, tatCaKetQua);

    for (int i = 0; i < (int)tatCaKetQua.size(); i++) {
        for (int j = 0; j < (int)tatCaKetQua[i].size(); j++) {
            cout << tatCaKetQua[i][j] << " ";
        }
        cout << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def sinh_to_hop_co_tong_bang_target(a, chi_so_bat_dau, target_con_lai, duong_di, tat_ca_ket_qua):
    if target_con_lai == 0:
        tat_ca_ket_qua.append(list(duong_di))
        return
    if target_con_lai < 0:
        return  # cắt tỉa: tổng đã vượt quá target

    for i in range(chi_so_bat_dau, len(a)):
        duong_di.append(a[i])
        sinh_to_hop_co_tong_bang_target(a, i, target_con_lai - a[i], duong_di, tat_ca_ket_qua)
        duong_di.pop()


a = [2, 3, 6, 7]
target = 7
duong_di = []
tat_ca_ket_qua = []

sinh_to_hop_co_tong_bang_target(a, 0, target, duong_di, tat_ca_ket_qua)

for ket_qua in tat_ca_ket_qua:
    print(ket_qua)
```

**Bài tập minh hoạ:** Cho một chuỗi chữ số, liệt kê tất cả các cách chèn dấu cộng "+" vào giữa các chữ số để tổng các số tạo thành bằng một giá trị target cho trước.

**Lời giải:** Tại mỗi vị trí giữa 2 chữ số liên tiếp, ta có 2 lựa chọn: chèn dấu "+" hoặc không chèn — dùng backtracking để thử cả 2 lựa chọn.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

void timCachChenCong(string chuoiSo, int viTriHienTai, string soDangXayDung,
                       long long tongHienTai, long long target,
                       string bieuThucHienTai, vector<string> &tatCaKetQua) {
    if (viTriHienTai == (int)chuoiSo.size()) {
        long long tongCuoiCung = tongHienTai + stoll(soDangXayDung);
        if (tongCuoiCung == target) {
            tatCaKetQua.push_back(bieuThucHienTai + soDangXayDung);
        }
        return;
    }

    char chuSoHienTai = chuoiSo[viTriHienTai];

    // Lựa chọn 1: nối chữ số này vào số đang xây dựng, KHÔNG chèn dấu "+"
    timCachChenCong(chuoiSo, viTriHienTai + 1, soDangXayDung + chuSoHienTai,
                     tongHienTai, target, bieuThucHienTai, tatCaKetQua);

    // Lựa chọn 2: chèn dấu "+" trước chữ số này (chỉ khi đã có số đang xây dựng)
    if (soDangXayDung.size() > 0) {
        long long giaTriSoVuaXongXong = stoll(soDangXayDung);
        timCachChenCong(chuoiSo, viTriHienTai + 1, string(1, chuSoHienTai),
                         tongHienTai + giaTriSoVuaXongXong, target,
                         bieuThucHienTai + soDangXayDung + "+", tatCaKetQua);
    }
}

int main() {
    string chuoiSo = "123";
    long long target = 6;
    vector<string> tatCaKetQua;

    timCachChenCong(chuoiSo, 0, "", 0, target, "", tatCaKetQua);

    for (int i = 0; i < (int)tatCaKetQua.size(); i++) {
        cout << tatCaKetQua[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def tim_cach_chen_cong(chuoi_so, vi_tri_hien_tai, so_dang_xay_dung, tong_hien_tai, target, bieu_thuc_hien_tai, tat_ca_ket_qua):
    if vi_tri_hien_tai == len(chuoi_so):
        tong_cuoi_cung = tong_hien_tai + int(so_dang_xay_dung)
        if tong_cuoi_cung == target:
            tat_ca_ket_qua.append(bieu_thuc_hien_tai + so_dang_xay_dung)
        return

    chu_so_hien_tai = chuoi_so[vi_tri_hien_tai]

    # Lựa chọn 1: nối chữ số này vào số đang xây dựng
    tim_cach_chen_cong(chuoi_so, vi_tri_hien_tai + 1, so_dang_xay_dung + chu_so_hien_tai,
                        tong_hien_tai, target, bieu_thuc_hien_tai, tat_ca_ket_qua)

    # Lựa chọn 2: chèn dấu "+" trước chữ số này
    if len(so_dang_xay_dung) > 0:
        gia_tri_so_vua_xong = int(so_dang_xay_dung)
        tim_cach_chen_cong(chuoi_so, vi_tri_hien_tai + 1, chu_so_hien_tai,
                            tong_hien_tai + gia_tri_so_vua_xong, target,
                            bieu_thuc_hien_tai + so_dang_xay_dung + "+", tat_ca_ket_qua)


chuoi_so = "123"
target = 6
tat_ca_ket_qua = []

tim_cach_chen_cong(chuoi_so, 0, "", 0, target, "", tat_ca_ket_qua)

for ket_qua in tat_ca_ket_qua:
    print(ket_qua)
```

---

## 7.3. Kỹ thuật cắt tỉa (Pruning)

**Nêu bài toán:** Bài toán N-Queens: hãy đếm số cách đặt n quân hậu lên bàn cờ n×n sao cho không có 2 quân hậu nào tấn công lẫn nhau (không cùng hàng, không cùng cột, không cùng đường chéo).

**Phân tích vấn đề:** Nếu áp dụng backtracking thuần tuý (thử mọi cách đặt quân hậu vào từng ô, kiểm tra hợp lệ ở cuối), số lượng trạng thái cần duyệt sẽ là `n²` cách chọn ô cho mỗi quân hậu, dẫn tới độ phức tạp cực lớn — với n = 8 đã có thể lên tới hàng tỷ trạng thái nếu không cắt bớt sớm.

**Giải pháp đơn thuần:** Thử đặt từng quân hậu vào tất cả các ô có thể, sau khi đặt xong toàn bộ n quân hậu mới kiểm tra tính hợp lệ. Cách này lãng phí rất nhiều thời gian vào các nhánh chắc chắn sẽ thất bại.

**Khó khăn với giải pháp đơn thuần:** Ta đang xây dựng đầy đủ một lời giải sai rồi mới phát hiện ra nó sai — trong khi lẽ ra có thể phát hiện sớm ngay từ giữa chừng để **bỏ qua toàn bộ nhánh con** phía dưới.

**Cách tiếp cận mới — Cắt tỉa (Pruning):** Ngay khi phát hiện một lựa chọn chắc chắn dẫn đến kết quả không hợp lệ, ta **dừng ngay lập tức** nhánh đó, không cần đi tiếp xuống các bước sau. Việc này giúp loại bỏ một lượng lớn không gian tìm kiếm mà không cần duyệt qua.

### Minh hoạ lời giải chi tiết

Với bài toán N-Queens (n=4), thay vì thử đặt quân hậu ở mọi ô của mỗi hàng rồi mới kiểm tra, ta đặt lần lượt từng hàng một (mỗi hàng đúng 1 quân hậu, nên không cần kiểm tra "cùng hàng"), và **kiểm tra ngay** trước khi đặt xem cột và 2 đường chéo có bị chiếm chưa:

```
Hàng 0: thử cột 0 -> hợp lệ, đặt quân hậu
  Hàng 1: thử cột 0 -> CÙNG CỘT, cắt tỉa ngay, không đi tiếp
          thử cột 1 -> CÙNG ĐƯỜNG CHÉO, cắt tỉa ngay
          thử cột 2 -> hợp lệ, đặt quân hậu
             Hàng 2: thử cột 0 -> CÙNG CỘT, cắt tỉa
                     thử cột 1 -> CÙNG ĐƯỜNG CHÉO, cắt tỉa
                     thử cột 2 -> CÙNG CỘT, cắt tỉa
                     thử cột 3 -> CÙNG ĐƯỜNG CHÉO, cắt tỉa
                     -> Không còn lựa chọn nào ở hàng 2, quay lui về hàng 1
          thử cột 3 -> hợp lệ, đặt quân hậu
             ... (tiếp tục quá trình tương tự)
```

Nhờ kiểm tra ngay tại mỗi bước thay vì đợi đặt xong hết mới kiểm tra, ta tránh được rất nhiều nhánh con vô ích.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int n;
int soCachDatHopLe = 0;
vector<int> cotDaDung;      // cotDaDung[hang] = cột mà quân hậu ở hàng đó đang đứng
vector<bool> cotDaBiChiem;
vector<bool> duongCheoChinhBiChiem; // đường chéo có (hang - cot) không đổi
vector<bool> duongCheoPhuBiChiem;    // đường chéo có (hang + cot) không đổi

void datQuanHau(int hang) {
    if (hang == n) {
        soCachDatHopLe = soCachDatHopLe + 1;
        return;
    }

    for (int cot = 0; cot < n; cot++) {
        int chiSoDuongCheoChinh = hang - cot + n; // cộng thêm n để tránh chỉ số âm
        int chiSoDuongCheoPhu = hang + cot;

        // Kiểm tra và CẮT TỈA NGAY nếu vị trí này không hợp lệ
        if (cotDaBiChiem[cot] == true) {
            continue;
        }
        if (duongCheoChinhBiChiem[chiSoDuongCheoChinh] == true) {
            continue;
        }
        if (duongCheoPhuBiChiem[chiSoDuongCheoPhu] == true) {
            continue;
        }

        // Đặt quân hậu tại vị trí (hang, cot)
        cotDaBiChiem[cot] = true;
        duongCheoChinhBiChiem[chiSoDuongCheoChinh] = true;
        duongCheoPhuBiChiem[chiSoDuongCheoPhu] = true;

        datQuanHau(hang + 1);

        // Quay lui - bỏ quân hậu vừa đặt
        cotDaBiChiem[cot] = false;
        duongCheoChinhBiChiem[chiSoDuongCheoChinh] = false;
        duongCheoPhuBiChiem[chiSoDuongCheoPhu] = false;
    }
}

int main() {
    n = 8;
    cotDaBiChiem.assign(n, false);
    duongCheoChinhBiChiem.assign(2 * n, false);
    duongCheoPhuBiChiem.assign(2 * n, false);

    datQuanHau(0);

    cout << "So cach dat " << n << " quan hau: " << soCachDatHopLe << endl;

    return 0;
}
```

#### PYTHON
```python
def dat_quan_hau(hang, n, cot_da_bi_chiem, duong_cheo_chinh_bi_chiem, duong_cheo_phu_bi_chiem):
    if hang == n:
        return 1

    tong_so_cach = 0

    for cot in range(n):
        chi_so_duong_cheo_chinh = hang - cot + n
        chi_so_duong_cheo_phu = hang + cot

        if cot_da_bi_chiem[cot] == True:
            continue
        if duong_cheo_chinh_bi_chiem[chi_so_duong_cheo_chinh] == True:
            continue
        if duong_cheo_phu_bi_chiem[chi_so_duong_cheo_phu] == True:
            continue

        cot_da_bi_chiem[cot] = True
        duong_cheo_chinh_bi_chiem[chi_so_duong_cheo_chinh] = True
        duong_cheo_phu_bi_chiem[chi_so_duong_cheo_phu] = True

        tong_so_cach = tong_so_cach + dat_quan_hau(hang + 1, n, cot_da_bi_chiem,
                                                     duong_cheo_chinh_bi_chiem, duong_cheo_phu_bi_chiem)

        cot_da_bi_chiem[cot] = False
        duong_cheo_chinh_bi_chiem[chi_so_duong_cheo_chinh] = False
        duong_cheo_phu_bi_chiem[chi_so_duong_cheo_phu] = False

    return tong_so_cach


n = 8
cot_da_bi_chiem = [False] * n
duong_cheo_chinh_bi_chiem = [False] * (2 * n)
duong_cheo_phu_bi_chiem = [False] * (2 * n)

ket_qua = dat_quan_hau(0, n, cot_da_bi_chiem, duong_cheo_chinh_bi_chiem, duong_cheo_phu_bi_chiem)
print("So cach dat", n, "quan hau:", ket_qua)
```

### Ví dụ 1 (Dễ) — Cắt tỉa trong bài toán tổng con (Subset Sum) khi mảng đã sắp xếp

Bài toán: cho mảng số nguyên dương đã sắp xếp tăng dần và số target, kiểm tra xem có tồn tại tập con có tổng đúng bằng target hay không.

**Phân tích:** Vì mảng đã sắp xếp tăng dần, nếu tổng hiện tại cộng thêm phần tử nhỏ nhất còn lại đã vượt quá target, ta có thể cắt tỉa ngay lập tức toàn bộ nhánh đó.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

bool timTapConCoTongBangTarget(vector<int> &a, int chiSoHienTai, long long targetConLai) {
    if (targetConLai == 0) {
        return true;
    }
    if (chiSoHienTai == (int)a.size()) {
        return false;
    }
    if (a[chiSoHienTai] > targetConLai) {
        // CẮT TỈA: vì mảng đã sắp xếp tăng dần, mọi phần tử còn lại đều lớn hơn target còn lại
        // -> không cần thử tiếp, dừng nhánh này ngay
        return false;
    }

    // Lựa chọn 1: chọn phần tử này
    bool ketQuaKhiChon = timTapConCoTongBangTarget(a, chiSoHienTai + 1, targetConLai - a[chiSoHienTai]);
    if (ketQuaKhiChon == true) {
        return true;
    }

    // Lựa chọn 2: không chọn phần tử này
    bool ketQuaKhiKhongChon = timTapConCoTongBangTarget(a, chiSoHienTai + 1, targetConLai);
    return ketQuaKhiKhongChon;
}

int main() {
    vector<int> a = {2, 3, 5, 8, 13};
    long long target = 11;

    bool ketQua = timTapConCoTongBangTarget(a, 0, target);
    if (ketQua == true) {
        cout << "Co ton tai" << endl;
    } else {
        cout << "Khong ton tai" << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def tim_tap_con_co_tong_bang_target(a, chi_so_hien_tai, target_con_lai):
    if target_con_lai == 0:
        return True
    if chi_so_hien_tai == len(a):
        return False
    if a[chi_so_hien_tai] > target_con_lai:
        return False  # cắt tỉa

    ket_qua_khi_chon = tim_tap_con_co_tong_bang_target(a, chi_so_hien_tai + 1, target_con_lai - a[chi_so_hien_tai])
    if ket_qua_khi_chon == True:
        return True

    ket_qua_khi_khong_chon = tim_tap_con_co_tong_bang_target(a, chi_so_hien_tai + 1, target_con_lai)
    return ket_qua_khi_khong_chon


a = [2, 3, 5, 8, 13]
target = 11

ket_qua = tim_tap_con_co_tong_bang_target(a, 0, target)
if ket_qua == True:
    print("Co ton tai")
else:
    print("Khong ton tai")
```

### Ví dụ 2 (Trung bình) — Sudoku thu nhỏ với cắt tỉa theo ràng buộc

Bài toán: giải một bảng Sudoku thu nhỏ kích thước 4×4 (chia thành 4 khối 2×2), điền các số từ 1 đến 4 sao cho mỗi hàng, mỗi cột, mỗi khối 2×2 đều chứa đủ các số từ 1 đến 4 không trùng lặp.

**Phân tích:** Với mỗi ô trống, thay vì thử cả 4 giá trị rồi mới kiểm tra tính hợp lệ trên toàn bảng, ta chỉ thử các giá trị **chưa xuất hiện** trong hàng, cột, và khối chứa ô đó — đây chính là cắt tỉa dựa trên ràng buộc cục bộ.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraHopLe(vector<vector<int>> &bang, int hang, int cot, int giaTri) {
    for (int i = 0; i < 4; i++) {
        if (bang[hang][i] == giaTri) {
            return false;
        }
        if (bang[i][cot] == giaTri) {
            return false;
        }
    }

    int hangKhoi = (hang / 2) * 2;
    int cotKhoi = (cot / 2) * 2;
    for (int i = hangKhoi; i < hangKhoi + 2; i++) {
        for (int j = cotKhoi; j < cotKhoi + 2; j++) {
            if (bang[i][j] == giaTri) {
                return false;
            }
        }
    }

    return true;
}

bool giaiSudoku(vector<vector<int>> &bang, int viTri) {
    if (viTri == 16) {
        return true; // đã điền hết toàn bộ bảng
    }

    int hang = viTri / 4;
    int cot = viTri % 4;

    if (bang[hang][cot] != 0) {
        return giaiSudoku(bang, viTri + 1); // ô này đã có sẵn giá trị, bỏ qua
    }

    for (int giaTri = 1; giaTri <= 4; giaTri++) {
        if (kiemTraHopLe(bang, hang, cot, giaTri) == false) {
            continue; // cắt tỉa: giá trị này vi phạm ràng buộc
        }

        bang[hang][cot] = giaTri;
        if (giaiSudoku(bang, viTri + 1) == true) {
            return true;
        }
        bang[hang][cot] = 0; // quay lui
    }

    return false;
}

int main() {
    vector<vector<int>> bang = {
        {1, 0, 0, 4},
        {0, 0, 1, 0},
        {0, 1, 0, 0},
        {4, 0, 0, 1}
    };

    if (giaiSudoku(bang, 0) == true) {
        for (int i = 0; i < 4; i++) {
            for (int j = 0; j < 4; j++) {
                cout << bang[i][j] << " ";
            }
            cout << endl;
        }
    } else {
        cout << "Khong co loi giai" << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def kiem_tra_hop_le(bang, hang, cot, gia_tri):
    for i in range(4):
        if bang[hang][i] == gia_tri:
            return False
        if bang[i][cot] == gia_tri:
            return False

    hang_khoi = (hang // 2) * 2
    cot_khoi = (cot // 2) * 2
    for i in range(hang_khoi, hang_khoi + 2):
        for j in range(cot_khoi, cot_khoi + 2):
            if bang[i][j] == gia_tri:
                return False

    return True


def giai_sudoku(bang, vi_tri):
    if vi_tri == 16:
        return True

    hang = vi_tri // 4
    cot = vi_tri % 4

    if bang[hang][cot] != 0:
        return giai_sudoku(bang, vi_tri + 1)

    for gia_tri in range(1, 5):
        if kiem_tra_hop_le(bang, hang, cot, gia_tri) == False:
            continue

        bang[hang][cot] = gia_tri
        if giai_sudoku(bang, vi_tri + 1) == True:
            return True
        bang[hang][cot] = 0

    return False


bang = [
    [1, 0, 0, 4],
    [0, 0, 1, 0],
    [0, 1, 0, 0],
    [4, 0, 0, 1],
]

if giai_sudoku(bang, 0) == True:
    for hang in bang:
        print(hang)
else:
    print("Khong co loi giai")
```

### Ví dụ 3 (Khó) — Cắt tỉa theo cận trên/cận dưới (Branch Bound đơn giản) trong bài toán cái túi 0/1

Bài toán: cho n vật, vật thứ i có trọng lượng `w[i]` và giá trị `v[i]`, và một cái túi có sức chứa `W`. Hãy tìm cách chọn một số vật (mỗi vật chỉ được chọn 1 lần) sao cho tổng trọng lượng không vượt quá W và tổng giá trị là lớn nhất.

**Phân tích:** Nếu chỉ dùng backtracking thuần tuý (thử "chọn" hoặc "không chọn" mỗi vật), độ phức tạp là O(2^n). Ta có thể cắt tỉa mạnh hơn bằng cách tính một **cận trên** cho giá trị có thể đạt được từ trạng thái hiện tại (giả sử được lấy phần lẻ của vật tiếp theo — bài toán cái túi phân số); nếu cận trên này vẫn nhỏ hơn giá trị tốt nhất đã tìm được, ta cắt tỉa ngay nhánh đó vì chắc chắn không thể cải thiện kết quả.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int n;
vector<int> trongLuong, giaTri;
int sucChua;
long long giaTriTotNhatDaTim = 0;

double tinhCanTren(int chiSoHienTai, int trongLuongConLai, long long giaTriHienTai) {
    double canTren = giaTriHienTai;
    int trongLuongDangXet = trongLuongConLai;

    for (int i = chiSoHienTai; i < n && trongLuongDangXet > 0; i++) {
        if (trongLuong[i] <= trongLuongDangXet) {
            canTren = canTren + giaTri[i];
            trongLuongDangXet = trongLuongDangXet - trongLuong[i];
        } else {
            // Lấy 1 phần lẻ của vật này (giả định, không thực sự khả thi trong bài toán 0/1 gốc)
            canTren = canTren + (double)giaTri[i] * ((double)trongLuongDangXet / (double)trongLuong[i]);
            trongLuongDangXet = 0;
        }
    }

    return canTren;
}

void giaiCaiTui(int chiSoHienTai, int trongLuongDaDung, long long giaTriDaCo) {
    if (chiSoHienTai == n) {
        if (giaTriDaCo > giaTriTotNhatDaTim) {
            giaTriTotNhatDaTim = giaTriDaCo;
        }
        return;
    }

    double canTren = tinhCanTren(chiSoHienTai, sucChua - trongLuongDaDung, giaTriDaCo);
    if (canTren <= (double)giaTriTotNhatDaTim) {
        return; // CẮT TỈA: dù lấy hết phần lẻ tối ưu cũng không vượt qua kết quả tốt nhất hiện có
    }

    // Lựa chọn 1: chọn vật này (nếu còn đủ chỗ)
    if (trongLuongDaDung + trongLuong[chiSoHienTai] <= sucChua) {
        giaiCaiTui(chiSoHienTai + 1, trongLuongDaDung + trongLuong[chiSoHienTai], giaTriDaCo + giaTri[chiSoHienTai]);
    }

    // Lựa chọn 2: không chọn vật này
    giaiCaiTui(chiSoHienTai + 1, trongLuongDaDung, giaTriDaCo);
}

int main() {
    n = 4;
    trongLuong = {2, 3, 4, 5};
    giaTri = {3, 4, 5, 6};
    sucChua = 5;

    giaiCaiTui(0, 0, 0);

    cout << "Gia tri tot nhat: " << giaTriTotNhatDaTim << endl;

    return 0;
}
```

#### PYTHON
```python
def tinh_can_tren(chi_so_hien_tai, trong_luong_con_lai, gia_tri_hien_tai, n, trong_luong, gia_tri):
    can_tren = gia_tri_hien_tai
    trong_luong_dang_xet = trong_luong_con_lai

    i = chi_so_hien_tai
    while i < n and trong_luong_dang_xet > 0:
        if trong_luong[i] <= trong_luong_dang_xet:
            can_tren = can_tren + gia_tri[i]
            trong_luong_dang_xet = trong_luong_dang_xet - trong_luong[i]
        else:
            can_tren = can_tren + gia_tri[i] * (trong_luong_dang_xet / trong_luong[i])
            trong_luong_dang_xet = 0
        i = i + 1

    return can_tren


def giai_cai_tui(chi_so_hien_tai, trong_luong_da_dung, gia_tri_da_co, n, trong_luong, gia_tri, suc_chua, gia_tri_tot_nhat):
    if chi_so_hien_tai == n:
        if gia_tri_da_co > gia_tri_tot_nhat[0]:
            gia_tri_tot_nhat[0] = gia_tri_da_co
        return

    can_tren = tinh_can_tren(chi_so_hien_tai, suc_chua - trong_luong_da_dung, gia_tri_da_co, n, trong_luong, gia_tri)
    if can_tren <= gia_tri_tot_nhat[0]:
        return  # cắt tỉa

    if trong_luong_da_dung + trong_luong[chi_so_hien_tai] <= suc_chua:
        giai_cai_tui(chi_so_hien_tai + 1, trong_luong_da_dung + trong_luong[chi_so_hien_tai],
                     gia_tri_da_co + gia_tri[chi_so_hien_tai], n, trong_luong, gia_tri, suc_chua, gia_tri_tot_nhat)

    giai_cai_tui(chi_so_hien_tai + 1, trong_luong_da_dung, gia_tri_da_co, n, trong_luong, gia_tri, suc_chua, gia_tri_tot_nhat)


n = 4
trong_luong = [2, 3, 4, 5]
gia_tri = [3, 4, 5, 6]
suc_chua = 5
gia_tri_tot_nhat = [0]  # dùng list 1 phần tử để có thể sửa giá trị bên trong hàm đệ quy

giai_cai_tui(0, 0, 0, n, trong_luong, gia_tri, suc_chua, gia_tri_tot_nhat)

print("Gia tri tot nhat:", gia_tri_tot_nhat[0])
```

**Bài tập minh hoạ:** Cho một danh sách các từ, hãy đếm số cách sắp xếp lại các từ đó thành một hàng sao cho các từ liền kề nhau không được bắt đầu bằng cùng một chữ cái. (Bài rèn kỹ năng cắt tỉa theo ràng buộc cục bộ, tương tự Ví dụ 2.)

**Lời giải:**

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int demCachSapXep(vector<string> &tuVung, vector<bool> &daDung, vector<char> &duongDi) {
    if ((int)duongDi.size() == (int)tuVung.size()) {
        return 1;
    }

    int tongSoCach = 0;

    for (int i = 0; i < (int)tuVung.size(); i++) {
        if (daDung[i] == true) {
            continue;
        }

        char chuCaiDau = tuVung[i][0];

        // Cắt tỉa: nếu trùng chữ cái đầu với từ liền trước, bỏ qua ngay
        if (duongDi.size() > 0 && duongDi.back() == chuCaiDau) {
            continue;
        }

        daDung[i] = true;
        duongDi.push_back(chuCaiDau);

        tongSoCach = tongSoCach + demCachSapXep(tuVung, daDung, duongDi);

        daDung[i] = false;
        duongDi.pop_back();
    }

    return tongSoCach;
}

int main() {
    vector<string> tuVung = {"an", "banh", "com", "an"};
    vector<bool> daDung(tuVung.size(), false);
    vector<char> duongDi;

    cout << demCachSapXep(tuVung, daDung, duongDi) << endl;

    return 0;
}
```

#### PYTHON
```python
def dem_cach_sap_xep(tu_vung, da_dung, duong_di):
    if len(duong_di) == len(tu_vung):
        return 1

    tong_so_cach = 0

    for i in range(len(tu_vung)):
        if da_dung[i] == True:
            continue

        chu_cai_dau = tu_vung[i][0]

        if len(duong_di) > 0 and duong_di[-1] == chu_cai_dau:
            continue

        da_dung[i] = True
        duong_di.append(chu_cai_dau)

        tong_so_cach = tong_so_cach + dem_cach_sap_xep(tu_vung, da_dung, duong_di)

        da_dung[i] = False
        duong_di.pop()

    return tong_so_cach


tu_vung = ["an", "banh", "com", "an"]
da_dung = [False] * len(tu_vung)
duong_di = []

print(dem_cach_sap_xep(tu_vung, da_dung, duong_di))
```

---

## 7.4. Chia để trị cơ bản (Divide and Conquer)

**Nêu bài toán:** Cho một mảng n số nguyên, hãy sắp xếp mảng đó tăng dần.

**Phân tích vấn đề:** Đây là bài toán quen thuộc, nhưng ta xét theo góc nhìn "chia để trị": nếu ta có 2 nửa mảng **đã được sắp xếp riêng**, liệu có cách nào gộp chúng lại thành 1 mảng sắp xếp hoàn chỉnh nhanh hơn sắp xếp lại từ đầu không? Câu trả lời là có — đây chính là ý tưởng của thuật toán Merge Sort.

**Giải pháp đơn thuần:** Dùng thuật toán sắp xếp đơn giản như Bubble Sort hoặc Selection Sort, độ phức tạp O(n²).

**Khó khăn với giải pháp đơn thuần:** Với n lớn (ví dụ 10^6), O(n²) sẽ quá chậm.

**Cách tiếp cận mới — Chia để trị:** Chia bài toán lớn thành các bài toán con **giống hệt nhưng kích thước nhỏ hơn** (thường chia đôi), giải quyết từng bài toán con bằng đệ quy, rồi **kết hợp (merge)** kết quả của các bài toán con lại để có kết quả cho bài toán lớn. Khác với backtracking (thử và quay lui để liệt kê mọi khả năng), chia để trị chỉ đi theo **một hướng duy nhất** — chia rồi trị, không cần quay lui thử lại.

### Minh hoạ lời giải chi tiết

Với mảng `[38, 27, 43, 3, 9, 82, 10]`, quá trình Merge Sort như sau:

```
Chia:
[38, 27, 43, 3, 9, 82, 10]
      /              \
[38, 27, 43, 3]      [9, 82, 10]
   /       \            /     \
[38, 27]  [43, 3]    [9, 82]  [10]
  /  \      /  \       /  \
[38] [27] [43] [3]   [9] [82]

Trị (gộp dần từ dưới lên):
[27, 38]  [3, 43]    [9, 82]  [10]
   \       /            \      /
 [3, 27, 38, 43]      [9, 10, 82]
        \                /
   [3, 9, 10, 27, 38, 43, 82]
```

Ở mỗi bước "gộp" (merge), ta gộp 2 mảng con đã sắp xếp thành 1 mảng lớn hơn cũng đã sắp xếp — đây chính là kỹ thuật 2 con trỏ đã học ở Chương 6 (Ví dụ 2, mục 6.1).

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

void gopHaiNua(vector<int> &a, int trai, int giua, int phai) {
    vector<int> phanTraDaSapXep;
    for (int i = trai; i <= giua; i++) {
        phanTraDaSapXep.push_back(a[i]);
    }

    vector<int> phanPhaiDaSapXep;
    for (int i = giua + 1; i <= phai; i++) {
        phanPhaiDaSapXep.push_back(a[i]);
    }

    int conTroTrai = 0;
    int conTroPhai = 0;
    int viTriGhi = trai;

    while (conTroTrai < (int)phanTraDaSapXep.size() && conTroPhai < (int)phanPhaiDaSapXep.size()) {
        if (phanTraDaSapXep[conTroTrai] <= phanPhaiDaSapXep[conTroPhai]) {
            a[viTriGhi] = phanTraDaSapXep[conTroTrai];
            conTroTrai = conTroTrai + 1;
        } else {
            a[viTriGhi] = phanPhaiDaSapXep[conTroPhai];
            conTroPhai = conTroPhai + 1;
        }
        viTriGhi = viTriGhi + 1;
    }

    while (conTroTrai < (int)phanTraDaSapXep.size()) {
        a[viTriGhi] = phanTraDaSapXep[conTroTrai];
        conTroTrai = conTroTrai + 1;
        viTriGhi = viTriGhi + 1;
    }

    while (conTroPhai < (int)phanPhaiDaSapXep.size()) {
        a[viTriGhi] = phanPhaiDaSapXep[conTroPhai];
        conTroPhai = conTroPhai + 1;
        viTriGhi = viTriGhi + 1;
    }
}

void mergeSort(vector<int> &a, int trai, int phai) {
    if (trai >= phai) {
        return; // điều kiện dừng: đoạn chỉ có 0 hoặc 1 phần tử, đã "sắp xếp" sẵn
    }

    int giua = trai + (phai - trai) / 2;

    mergeSort(a, trai, giua);       // chia và trị nửa trái
    mergeSort(a, giua + 1, phai);    // chia và trị nửa phải
    gopHaiNua(a, trai, giua, phai);  // kết hợp 2 nửa đã sắp xếp
}

int main() {
    vector<int> a = {38, 27, 43, 3, 9, 82, 10};
    mergeSort(a, 0, (int)a.size() - 1);

    for (int i = 0; i < (int)a.size(); i++) {
        cout << a[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def gop_hai_nua(a, trai, giua, phai):
    phan_trai_da_sap_xep = a[trai:giua+1]
    phan_phai_da_sap_xep = a[giua+1:phai+1]

    con_tro_trai = 0
    con_tro_phai = 0
    vi_tri_ghi = trai

    while con_tro_trai < len(phan_trai_da_sap_xep) and con_tro_phai < len(phan_phai_da_sap_xep):
        if phan_trai_da_sap_xep[con_tro_trai] <= phan_phai_da_sap_xep[con_tro_phai]:
            a[vi_tri_ghi] = phan_trai_da_sap_xep[con_tro_trai]
            con_tro_trai = con_tro_trai + 1
        else:
            a[vi_tri_ghi] = phan_phai_da_sap_xep[con_tro_phai]
            con_tro_phai = con_tro_phai + 1
        vi_tri_ghi = vi_tri_ghi + 1

    while con_tro_trai < len(phan_trai_da_sap_xep):
        a[vi_tri_ghi] = phan_trai_da_sap_xep[con_tro_trai]
        con_tro_trai = con_tro_trai + 1
        vi_tri_ghi = vi_tri_ghi + 1

    while con_tro_phai < len(phan_phai_da_sap_xep):
        a[vi_tri_ghi] = phan_phai_da_sap_xep[con_tro_phai]
        con_tro_phai = con_tro_phai + 1
        vi_tri_ghi = vi_tri_ghi + 1


def merge_sort(a, trai, phai):
    if trai >= phai:
        return

    giua = trai + (phai - trai) // 2

    merge_sort(a, trai, giua)
    merge_sort(a, giua + 1, phai)
    gop_hai_nua(a, trai, giua, phai)


a = [38, 27, 43, 3, 9, 82, 10]
merge_sort(a, 0, len(a) - 1)
print(a)
```

### Ví dụ 1 (Dễ) — Tìm giá trị lớn nhất trong mảng bằng chia để trị

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int timGiaTriLonNhat(vector<int> &a, int trai, int phai) {
    if (trai == phai) {
        return a[trai]; // điều kiện dừng: chỉ còn 1 phần tử
    }

    int giua = trai + (phai - trai) / 2;

    int lonNhatNuaTrai = timGiaTriLonNhat(a, trai, giua);
    int lonNhatNuaPhai = timGiaTriLonNhat(a, giua + 1, phai);

    return max(lonNhatNuaTrai, lonNhatNuaPhai);
}

int main() {
    vector<int> a = {3, 7, 2, 9, 4, 1};
    cout << timGiaTriLonNhat(a, 0, (int)a.size() - 1) << endl;
    return 0;
}
```

#### PYTHON
```python
def tim_gia_tri_lon_nhat(a, trai, phai):
    if trai == phai:
        return a[trai]

    giua = trai + (phai - trai) // 2

    lon_nhat_nua_trai = tim_gia_tri_lon_nhat(a, trai, giua)
    lon_nhat_nua_phai = tim_gia_tri_lon_nhat(a, giua + 1, phai)

    return max(lon_nhat_nua_trai, lon_nhat_nua_phai)


a = [3, 7, 2, 9, 4, 1]
print(tim_gia_tri_lon_nhat(a, 0, len(a) - 1))
```

### Ví dụ 2 (Trung bình) — Đếm số nghịch thế bằng chia để trị

Bài toán: cho mảng n số nguyên, đếm số cặp (i, j) với `i < j` và `a[i] > a[j]` (gọi là "nghịch thế").

**Phân tích:** Đếm trực tiếp bằng 2 vòng lặp lồng nhau tốn O(n²). Nhưng nếu áp dụng chia để trị theo đúng cấu trúc Merge Sort: số nghịch thế của mảng gồm 3 phần — số nghịch thế trong nửa trái, số nghịch thế trong nửa phải, và số nghịch thế **giữa** 2 nửa (được đếm ngay trong quá trình gộp).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long demNghichTheKhiGop(vector<int> &a, int trai, int giua, int phai) {
    vector<int> phanTraDaSapXep;
    for (int i = trai; i <= giua; i++) {
        phanTraDaSapXep.push_back(a[i]);
    }

    vector<int> phanPhaiDaSapXep;
    for (int i = giua + 1; i <= phai; i++) {
        phanPhaiDaSapXep.push_back(a[i]);
    }

    int conTroTrai = 0;
    int conTroPhai = 0;
    int viTriGhi = trai;
    long long soNghichThe = 0;

    while (conTroTrai < (int)phanTraDaSapXep.size() && conTroPhai < (int)phanPhaiDaSapXep.size()) {
        if (phanTraDaSapXep[conTroTrai] <= phanPhaiDaSapXep[conTroPhai]) {
            a[viTriGhi] = phanTraDaSapXep[conTroTrai];
            conTroTrai = conTroTrai + 1;
        } else {
            // Mọi phần tử còn lại của nửa trái đều lớn hơn phần tử hiện tại của nửa phải
            // -> đều tạo thành nghịch thế với phần tử này
            soNghichThe = soNghichThe + (phanTraDaSapXep.size() - conTroTrai);
            a[viTriGhi] = phanPhaiDaSapXep[conTroPhai];
            conTroPhai = conTroPhai + 1;
        }
        viTriGhi = viTriGhi + 1;
    }

    while (conTroTrai < (int)phanTraDaSapXep.size()) {
        a[viTriGhi] = phanTraDaSapXep[conTroTrai];
        conTroTrai = conTroTrai + 1;
        viTriGhi = viTriGhi + 1;
    }

    while (conTroPhai < (int)phanPhaiDaSapXep.size()) {
        a[viTriGhi] = phanPhaiDaSapXep[conTroPhai];
        conTroPhai = conTroPhai + 1;
        viTriGhi = viTriGhi + 1;
    }

    return soNghichThe;
}

long long demNghichThe(vector<int> &a, int trai, int phai) {
    if (trai >= phai) {
        return 0;
    }

    int giua = trai + (phai - trai) / 2;

    long long soNghichTheTrai = demNghichThe(a, trai, giua);
    long long soNghichThePhai = demNghichThe(a, giua + 1, phai);
    long long soNghichTheGiua = demNghichTheKhiGop(a, trai, giua, phai);

    return soNghichTheTrai + soNghichThePhai + soNghichTheGiua;
}

int main() {
    vector<int> a = {5, 3, 2, 4, 1};
    cout << demNghichThe(a, 0, (int)a.size() - 1) << endl;
    return 0;
}
```

#### PYTHON
```python
def dem_nghich_the_khi_gop(a, trai, giua, phai):
    phan_trai_da_sap_xep = a[trai:giua+1]
    phan_phai_da_sap_xep = a[giua+1:phai+1]

    con_tro_trai = 0
    con_tro_phai = 0
    vi_tri_ghi = trai
    so_nghich_the = 0

    while con_tro_trai < len(phan_trai_da_sap_xep) and con_tro_phai < len(phan_phai_da_sap_xep):
        if phan_trai_da_sap_xep[con_tro_trai] <= phan_phai_da_sap_xep[con_tro_phai]:
            a[vi_tri_ghi] = phan_trai_da_sap_xep[con_tro_trai]
            con_tro_trai = con_tro_trai + 1
        else:
            so_nghich_the = so_nghich_the + (len(phan_trai_da_sap_xep) - con_tro_trai)
            a[vi_tri_ghi] = phan_phai_da_sap_xep[con_tro_phai]
            con_tro_phai = con_tro_phai + 1
        vi_tri_ghi = vi_tri_ghi + 1

    while con_tro_trai < len(phan_trai_da_sap_xep):
        a[vi_tri_ghi] = phan_trai_da_sap_xep[con_tro_trai]
        con_tro_trai = con_tro_trai + 1
        vi_tri_ghi = vi_tri_ghi + 1

    while con_tro_phai < len(phan_phai_da_sap_xep):
        a[vi_tri_ghi] = phan_phai_da_sap_xep[con_tro_phai]
        con_tro_phai = con_tro_phai + 1
        vi_tri_ghi = vi_tri_ghi + 1

    return so_nghich_the


def dem_nghich_the(a, trai, phai):
    if trai >= phai:
        return 0

    giua = trai + (phai - trai) // 2

    so_nghich_the_trai = dem_nghich_the(a, trai, giua)
    so_nghich_the_phai = dem_nghich_the(a, giua + 1, phai)
    so_nghich_the_giua = dem_nghich_the_khi_gop(a, trai, giua, phai)

    return so_nghich_the_trai + so_nghich_the_phai + so_nghich_the_giua


a = [5, 3, 2, 4, 1]
print(dem_nghich_the(a, 0, len(a) - 1))
```

### Ví dụ 3 (Khó) — Tìm cặp điểm gần nhất trên mặt phẳng (Closest Pair of Points)

Bài toán: cho n điểm trên mặt phẳng, tìm khoảng cách nhỏ nhất giữa 2 điểm bất kỳ trong số đó.

**Phân tích:** Nếu thử tất cả các cặp điểm, độ phức tạp là O(n²). Áp dụng chia để trị: chia các điểm (đã sắp xếp theo toạ độ x) thành 2 nửa, tìm khoảng cách nhỏ nhất trong mỗi nửa bằng đệ quy, rồi xét thêm các cặp điểm nằm **gần đường phân chia** ở giữa (vì cặp điểm gần nhất có thể nằm vắt qua ranh giới giữa 2 nửa).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

struct Diem {
    double x, y;
};

double tinhKhoangCach(Diem a, Diem b) {
    double hieuX = a.x - b.x;
    double hieuY = a.y - b.y;
    return sqrt(hieuX * hieuX + hieuY * hieuY);
}

double timKhoangCachNhoNhatBruteForce(vector<Diem> &diem, int trai, int phai) {
    double ketQua = DBL_MAX;
    for (int i = trai; i <= phai; i++) {
        for (int j = i + 1; j <= phai; j++) {
            double khoangCach = tinhKhoangCach(diem[i], diem[j]);
            if (khoangCach < ketQua) {
                ketQua = khoangCach;
            }
        }
    }
    return ketQua;
}

double timKhoangCachNhoNhat(vector<Diem> &diemTheoX, int trai, int phai) {
    // Nếu số điểm nhỏ, dùng brute force trực tiếp - điều kiện dừng của đệ quy
    if (phai - trai <= 3) {
        return timKhoangCachNhoNhatBruteForce(diemTheoX, trai, phai);
    }

    int giua = trai + (phai - trai) / 2;
    double xGiua = diemTheoX[giua].x;

    double ketQuaTrai = timKhoangCachNhoNhat(diemTheoX, trai, giua);
    double ketQuaPhai = timKhoangCachNhoNhat(diemTheoX, giua + 1, phai);

    double ketQuaTotNhat = min(ketQuaTrai, ketQuaPhai);

    // Thu thập các điểm nằm gần đường phân chia (trong khoảng ketQuaTotNhat) để kiểm tra thêm
    vector<Diem> cacDiemGanBienGioi;
    for (int i = trai; i <= phai; i++) {
        if (abs(diemTheoX[i].x - xGiua) < ketQuaTotNhat) {
            cacDiemGanBienGioi.push_back(diemTheoX[i]);
        }
    }

    sort(cacDiemGanBienGioi.begin(), cacDiemGanBienGioi.end(), [](Diem a, Diem b) {
        return a.y < b.y;
    });

    for (int i = 0; i < (int)cacDiemGanBienGioi.size(); i++) {
        for (int j = i + 1; j < (int)cacDiemGanBienGioi.size(); j++) {
            if (cacDiemGanBienGioi[j].y - cacDiemGanBienGioi[i].y >= ketQuaTotNhat) {
                break; // đã vượt quá khoảng cách tốt nhất theo trục y, không cần xét tiếp
            }
            double khoangCach = tinhKhoangCach(cacDiemGanBienGioi[i], cacDiemGanBienGioi[j]);
            if (khoangCach < ketQuaTotNhat) {
                ketQuaTotNhat = khoangCach;
            }
        }
    }

    return ketQuaTotNhat;
}

int main() {
    vector<Diem> diem = {{2,3}, {12,30}, {40,50}, {5,1}, {12,10}, {3,4}};

    sort(diem.begin(), diem.end(), [](Diem a, Diem b) {
        return a.x < b.x;
    });

    cout << fixed << setprecision(4) << timKhoangCachNhoNhat(diem, 0, (int)diem.size() - 1) << endl;

    return 0;
}
```

#### PYTHON
```python
import math


def tinh_khoang_cach(a, b):
    hieu_x = a[0] - b[0]
    hieu_y = a[1] - b[1]
    return math.sqrt(hieu_x * hieu_x + hieu_y * hieu_y)


def tim_khoang_cach_nho_nhat_brute_force(diem, trai, phai):
    ket_qua = float('inf')
    for i in range(trai, phai + 1):
        for j in range(i + 1, phai + 1):
            khoang_cach = tinh_khoang_cach(diem[i], diem[j])
            if khoang_cach < ket_qua:
                ket_qua = khoang_cach
    return ket_qua


def tim_khoang_cach_nho_nhat(diem_theo_x, trai, phai):
    if phai - trai <= 3:
        return tim_khoang_cach_nho_nhat_brute_force(diem_theo_x, trai, phai)

    giua = trai + (phai - trai) // 2
    x_giua = diem_theo_x[giua][0]

    ket_qua_trai = tim_khoang_cach_nho_nhat(diem_theo_x, trai, giua)
    ket_qua_phai = tim_khoang_cach_nho_nhat(diem_theo_x, giua + 1, phai)

    ket_qua_tot_nhat = min(ket_qua_trai, ket_qua_phai)

    cac_diem_gan_bien_gioi = []
    for i in range(trai, phai + 1):
        if abs(diem_theo_x[i][0] - x_giua) < ket_qua_tot_nhat:
            cac_diem_gan_bien_gioi.append(diem_theo_x[i])

    cac_diem_gan_bien_gioi.sort(key=lambda p: p[1])

    for i in range(len(cac_diem_gan_bien_gioi)):
        for j in range(i + 1, len(cac_diem_gan_bien_gioi)):
            if cac_diem_gan_bien_gioi[j][1] - cac_diem_gan_bien_gioi[i][1] >= ket_qua_tot_nhat:
                break
            khoang_cach = tinh_khoang_cach(cac_diem_gan_bien_gioi[i], cac_diem_gan_bien_gioi[j])
            if khoang_cach < ket_qua_tot_nhat:
                ket_qua_tot_nhat = khoang_cach

    return ket_qua_tot_nhat


diem = [(2,3), (12,30), (40,50), (5,1), (12,10), (3,4)]
diem.sort(key=lambda p: p[0])

print(round(tim_khoang_cach_nho_nhat(diem, 0, len(diem) - 1), 4))
```

**Bài tập minh hoạ:** Cho mảng n số nguyên, tìm đoạn con liên tiếp có tổng lớn nhất (Maximum Subarray Sum) bằng kỹ thuật chia để trị.

**Lời giải:** Đoạn con có tổng lớn nhất chỉ có thể nằm hoàn toàn ở nửa trái, hoàn toàn ở nửa phải, hoặc **vắt qua điểm giữa**. Trường hợp thứ 3 cần tính riêng bằng cách mở rộng dần từ điểm giữa ra 2 phía.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timTongLonNhatVatQuaGiua(vector<int> &a, int trai, int giua, int phai) {
    long long tongLonNhatBenTrai = LLONG_MIN;
    long long tongChayDong = 0;
    for (int i = giua; i >= trai; i--) {
        tongChayDong = tongChayDong + a[i];
        if (tongChayDong > tongLonNhatBenTrai) {
            tongLonNhatBenTrai = tongChayDong;
        }
    }

    long long tongLonNhatBenPhai = LLONG_MIN;
    tongChayDong = 0;
    for (int i = giua + 1; i <= phai; i++) {
        tongChayDong = tongChayDong + a[i];
        if (tongChayDong > tongLonNhatBenPhai) {
            tongLonNhatBenPhai = tongChayDong;
        }
    }

    return tongLonNhatBenTrai + tongLonNhatBenPhai;
}

long long timTongDoanConLonNhat(vector<int> &a, int trai, int phai) {
    if (trai == phai) {
        return a[trai];
    }

    int giua = trai + (phai - trai) / 2;

    long long ketQuaTrai = timTongDoanConLonNhat(a, trai, giua);
    long long ketQuaPhai = timTongDoanConLonNhat(a, giua + 1, phai);
    long long ketQuaVatQuaGiua = timTongLonNhatVatQuaGiua(a, trai, giua, phai);

    return max({ketQuaTrai, ketQuaPhai, ketQuaVatQuaGiua});
}

int main() {
    vector<int> a = {-2, 1, -3, 4, -1, 2, 1, -5, 4};
    cout << timTongDoanConLonNhat(a, 0, (int)a.size() - 1) << endl;
    return 0;
}
```

#### PYTHON
```python
def tim_tong_lon_nhat_vat_qua_giua(a, trai, giua, phai):
    tong_lon_nhat_ben_trai = float('-inf')
    tong_chay_dong = 0
    for i in range(giua, trai - 1, -1):
        tong_chay_dong = tong_chay_dong + a[i]
        if tong_chay_dong > tong_lon_nhat_ben_trai:
            tong_lon_nhat_ben_trai = tong_chay_dong

    tong_lon_nhat_ben_phai = float('-inf')
    tong_chay_dong = 0
    for i in range(giua + 1, phai + 1):
        tong_chay_dong = tong_chay_dong + a[i]
        if tong_chay_dong > tong_lon_nhat_ben_phai:
            tong_lon_nhat_ben_phai = tong_chay_dong

    return tong_lon_nhat_ben_trai + tong_lon_nhat_ben_phai


def tim_tong_doan_con_lon_nhat(a, trai, phai):
    if trai == phai:
        return a[trai]

    giua = trai + (phai - trai) // 2

    ket_qua_trai = tim_tong_doan_con_lon_nhat(a, trai, giua)
    ket_qua_phai = tim_tong_doan_con_lon_nhat(a, giua + 1, phai)
    ket_qua_vat_qua_giua = tim_tong_lon_nhat_vat_qua_giua(a, trai, giua, phai)

    return max(ket_qua_trai, ket_qua_phai, ket_qua_vat_qua_giua)


a = [-2, 1, -3, 4, -1, 2, 1, -5, 4]
print(tim_tong_doan_con_lon_nhat(a, 0, len(a) - 1))
```

---

## Tổng kết Chương 7

- **Đệ quy** là kỹ thuật viết hàm gọi lại chính nó, luôn cần **điều kiện dừng** rõ ràng để tránh gọi vô hạn — vẽ cây gọi hàm là cách trực quan nhất để hiểu và debug đệ quy.
- **Backtracking (Quay lui)** dùng để liệt kê đầy đủ các khả năng (hoán vị, tổ hợp, tập con), theo mô hình "chọn - đệ quy - hoàn tác (quay lui)".
- **Cắt tỉa (Pruning)** là kỹ thuật sống còn để backtracking chạy đủ nhanh trong thực tế — luôn tìm cách phát hiện sớm các nhánh chắc chắn thất bại để bỏ qua, thay vì xây dựng đầy đủ rồi mới kiểm tra.
- **Chia để trị (Divide and Conquer)** khác với backtracking ở chỗ nó chỉ đi theo một hướng duy nhất (chia rồi trị), không cần quay lui thử lại — phù hợp cho các bài toán có thể chia thành các bài toán con độc lập, giống hệt bài toán gốc nhưng kích thước nhỏ hơn.
- **Mối liên hệ quan trọng cần nhớ:** đệ quy không có "ghi nhớ" (như Ví dụ 3, mục 7.1 — Fibonacci) có thể dẫn tới việc tính lại cùng một bài toán con nhiều lần, gây độ phức tạp tăng theo cấp số nhân — đây chính là động lực dẫn tới kỹ thuật Quy hoạch động sẽ học ở Chương 8.

---

## Bài tập cuối chương — Chương 7

### PHẦN A: ĐỀ BÀI (16 bài, không kèm lời giải)

**Bài 1 — Reverse String (LeetCode 344) — Dễ**
Viết hàm đệ quy đảo ngược một chuỗi ký tự.

**Bài 2 — Power of Two (LeetCode 231) — Dễ**
Dùng đệ quy để kiểm tra xem một số nguyên có phải là luỹ thừa của 2 hay không.

**Bài 3 — Permutations (LeetCode 46) — Trung bình**
Cho một mảng số nguyên phân biệt, liệt kê tất cả các hoán vị có thể có.

**Bài 4 — Subsets (LeetCode 78) — Trung bình**
Cho một mảng số nguyên phân biệt, liệt kê tất cả các tập con có thể có.

**Bài 5 — Combinations (LeetCode 77) — Trung bình**
Cho 2 số nguyên n và k, liệt kê tất cả các tổ hợp chập k của các số từ 1 đến n.

**Bài 6 — Combination Sum (LeetCode 39) — Trung bình**
Giống hệt Ví dụ 3, mục 7.2 đã trình bày (dùng để luyện tập lại).

**Bài 7 — Letter Combinations of a Phone Number (LeetCode 17) — Trung bình**
Cho một chuỗi số điện thoại (từ 2 đến 9), mỗi số ứng với một tập chữ cái (giống bàn phím điện thoại cũ), hãy liệt kê tất cả các tổ hợp chữ cái có thể tạo ra.

**Bài 8 — Palindrome Partitioning (LeetCode 131) — Trung bình**
Cho một chuỗi, hãy liệt kê tất cả các cách chia chuỗi đó thành các đoạn con sao cho mỗi đoạn con đều là chuỗi đối xứng (palindrome).

**Bài 9 — N-Queens (LeetCode 51) — Khó**
Giống hệt bài toán chính, mục 7.3 đã trình bày, nhưng yêu cầu in ra tất cả các cách đặt hợp lệ (không chỉ đếm số lượng).

**Bài 10 — N-Queens II (LeetCode 52) — Khó**
Giống hệt bài toán chính, mục 7.3, chỉ cần đếm số lượng cách đặt hợp lệ (dùng để luyện tập lại).

**Bài 11 — Sudoku Solver (LeetCode 37) — Khó**
Giải một bảng Sudoku chuẩn 9×9 với các ô đã cho sẵn một số giá trị, sử dụng kỹ thuật cắt tỉa tương tự Ví dụ 2, mục 7.3.

**Bài 12 — Word Search (LeetCode 79) — Trung bình-Khó**
Cho một lưới ký tự và một từ cần tìm, kiểm tra xem có thể tạo thành từ đó bằng cách di chuyển qua các ô liền kề (trên/dưới/trái/phải), mỗi ô chỉ được dùng 1 lần, hay không.

**Bài 13 — Merge Sort (tự cài đặt lại, dùng để luyện tập) — Trung bình**
Cài đặt lại thuật toán Merge Sort từ đầu, không xem lại code mẫu, để tự kiểm tra mức độ hiểu bài.

**Bài 14 — Count of Smaller Numbers After Self (LeetCode 315) — Khó**
Cho mảng số nguyên, với mỗi phần tử, đếm số lượng phần tử nhỏ hơn nó nằm ở bên phải nó. (Gợi ý: kết hợp ý tưởng đếm nghịch thế ở Ví dụ 2, mục 7.4.)

**Bài 15 — Different Ways to Add Parentheses (LeetCode 241) — Khó**
Cho một biểu thức số học chỉ gồm số và các phép toán +, -, *, hãy tính tất cả các kết quả có thể có khi đặt ngoặc theo mọi cách khác nhau (dùng chia để trị: tại mỗi dấu phép toán, chia biểu thức thành 2 nửa, đệ quy tính kết quả từng nửa, rồi kết hợp lại).

**Bài 16 — Bài toán mê cung (Rat in a Maze, dạng phổ biến trong đề HSG) — Trung bình-Khó**
Cho một mê cung dạng lưới ô vuông, có các ô chặn và ô trống, xuất phát từ góc trên-trái, tìm tất cả các đường đi (chỉ được di chuyển xuống hoặc sang phải) tới góc dưới-phải.

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Reverse String</summary>

#### C++
```cpp
void daoNguocChuoi(vector<char> &s, int trai, int phai) {
    if (trai >= phai) {
        return;
    }
    swap(s[trai], s[phai]);
    daoNguocChuoi(s, trai + 1, phai - 1);
}
```

#### PYTHON
```python
def dao_nguoc_chuoi(s, trai, phai):
    if trai >= phai:
        return
    s[trai], s[phai] = s[phai], s[trai]
    dao_nguoc_chuoi(s, trai + 1, phai - 1)
```
</details>

<details>
<summary>Lời giải Bài 2 — Power of Two</summary>

#### C++
```cpp
bool isPowerOfTwo(int n) {
    if (n <= 0) {
        return false;
    }
    if (n == 1) {
        return true;
    }
    if (n % 2 != 0) {
        return false;
    }
    return isPowerOfTwo(n / 2);
}
```

#### PYTHON
```python
def is_power_of_two(n):
    if n <= 0:
        return False
    if n == 1:
        return True
    if n % 2 != 0:
        return False
    return is_power_of_two(n // 2)
```
</details>

<details>
<summary>Lời giải Bài 3 — Permutations</summary>

Giống hệt code khung mẫu, mục 7.2, hàm `sinhHoanVi`/`sinh_hoan_vi`.
</details>

<details>
<summary>Lời giải Bài 4 — Subsets</summary>

Giống hệt Ví dụ 1, mục 7.2, hàm `sinhTapCon`/`sinh_tap_con`.
</details>

<details>
<summary>Lời giải Bài 5 — Combinations</summary>

Giống hệt Ví dụ 2, mục 7.2, hàm `sinhToHop`/`sinh_to_hop`, với `a` là mảng số từ 1 đến n.
</details>

<details>
<summary>Lời giải Bài 6 — Combination Sum</summary>

Giống hệt Ví dụ 3, mục 7.2, hàm `sinhToHopCoTongBangTarget`/`sinh_to_hop_co_tong_bang_target`.
</details>

<details>
<summary>Lời giải Bài 7 — Letter Combinations of a Phone Number</summary>

#### C++
```cpp
void sinhToHopChuCai(string chuSo, int viTriHienTai, string duongDi,
                       vector<string> &banDoSo, vector<string> &tatCaKetQua) {
    if (viTriHienTai == (int)chuSo.size()) {
        if (duongDi.size() > 0) {
            tatCaKetQua.push_back(duongDi);
        }
        return;
    }

    int chiSoTrenBanDo = chuSo[viTriHienTai] - '0';
    string cacChuCaiTuongUng = banDoSo[chiSoTrenBanDo];

    for (int i = 0; i < (int)cacChuCaiTuongUng.size(); i++) {
        sinhToHopChuCai(chuSo, viTriHienTai + 1, duongDi + cacChuCaiTuongUng[i], banDoSo, tatCaKetQua);
    }
}

int main() {
    vector<string> banDoSo(10);
    banDoSo[2] = "abc"; banDoSo[3] = "def"; banDoSo[4] = "ghi";
    banDoSo[5] = "jkl"; banDoSo[6] = "mno"; banDoSo[7] = "pqrs";
    banDoSo[8] = "tuv"; banDoSo[9] = "wxyz";

    string chuSo = "23";
    vector<string> tatCaKetQua;
    sinhToHopChuCai(chuSo, 0, "", banDoSo, tatCaKetQua);

    for (int i = 0; i < (int)tatCaKetQua.size(); i++) {
        cout << tatCaKetQua[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def sinh_to_hop_chu_cai(chu_so, vi_tri_hien_tai, duong_di, ban_do_so, tat_ca_ket_qua):
    if vi_tri_hien_tai == len(chu_so):
        if len(duong_di) > 0:
            tat_ca_ket_qua.append(duong_di)
        return

    chi_so_tren_ban_do = int(chu_so[vi_tri_hien_tai])
    cac_chu_cai_tuong_ung = ban_do_so[chi_so_tren_ban_do]

    for chu_cai in cac_chu_cai_tuong_ung:
        sinh_to_hop_chu_cai(chu_so, vi_tri_hien_tai + 1, duong_di + chu_cai, ban_do_so, tat_ca_ket_qua)


ban_do_so = {
    2: "abc", 3: "def", 4: "ghi", 5: "jkl",
    6: "mno", 7: "pqrs", 8: "tuv", 9: "wxyz",
}

chu_so = "23"
tat_ca_ket_qua = []
sinh_to_hop_chu_cai(chu_so, 0, "", ban_do_so, tat_ca_ket_qua)

for ket_qua in tat_ca_ket_qua:
    print(ket_qua)
```
</details>

<details>
<summary>Lời giải Bài 8 — Palindrome Partitioning</summary>

#### C++
```cpp
bool kiemTraDoiXung(string &s, int trai, int phai) {
    while (trai < phai) {
        if (s[trai] != s[phai]) {
            return false;
        }
        trai = trai + 1;
        phai = phai - 1;
    }
    return true;
}

void sinhCachChia(string &s, int viTriBatDau, vector<string> &duongDi, vector<vector<string>> &tatCaKetQua) {
    if (viTriBatDau == (int)s.size()) {
        tatCaKetQua.push_back(duongDi);
        return;
    }

    for (int ketThuc = viTriBatDau; ketThuc < (int)s.size(); ketThuc++) {
        if (kiemTraDoiXung(s, viTriBatDau, ketThuc) == true) {
            string doanCon = s.substr(viTriBatDau, ketThuc - viTriBatDau + 1);
            duongDi.push_back(doanCon);
            sinhCachChia(s, ketThuc + 1, duongDi, tatCaKetQua);
            duongDi.pop_back();
        }
    }
}
```

#### PYTHON
```python
def kiem_tra_doi_xung(s, trai, phai):
    while trai < phai:
        if s[trai] != s[phai]:
            return False
        trai = trai + 1
        phai = phai - 1
    return True


def sinh_cach_chia(s, vi_tri_bat_dau, duong_di, tat_ca_ket_qua):
    if vi_tri_bat_dau == len(s):
        tat_ca_ket_qua.append(list(duong_di))
        return

    for ket_thuc in range(vi_tri_bat_dau, len(s)):
        if kiem_tra_doi_xung(s, vi_tri_bat_dau, ket_thuc) == True:
            doan_con = s[vi_tri_bat_dau:ket_thuc+1]
            duong_di.append(doan_con)
            sinh_cach_chia(s, ket_thuc + 1, duong_di, tat_ca_ket_qua)
            duong_di.pop()
```
</details>

<details>
<summary>Lời giải Bài 9 — N-Queens</summary>

Sửa lại hàm `datQuanHau`/`dat_quan_hau` ở mục 7.3 để ghi nhận toàn bộ bảng cờ mỗi khi đặt xong (thay vì chỉ đếm số lượng), bằng cách thêm một mảng lưu trạng thái bàn cờ hiện tại và copy nó vào danh sách kết quả khi `hang == n`.
</details>

<details>
<summary>Lời giải Bài 10 — N-Queens II</summary>

Giống hệt bài toán chính, mục 7.3, hàm `datQuanHau`/`dat_quan_hau` — chỉ cần trả về `soCachDatHopLe`.
</details>

<details>
<summary>Lời giải Bài 11 — Sudoku Solver</summary>

Mở rộng trực tiếp từ Ví dụ 2, mục 7.3 — chỉ cần đổi kích thước bảng từ 4×4 (khối 2×2) thành 9×9 (khối 3×3), và giá trị thử từ 1-4 thành 1-9. Cấu trúc hàm `kiemTraHopLe` và `giaiSudoku` giữ nguyên logic.
</details>

<details>
<summary>Lời giải Bài 12 — Word Search</summary>

#### C++
```cpp
bool timKiemTuDeQuy(vector<vector<char>> &luoi, string &tu, int hang, int cot, int viTriKyTu, vector<vector<bool>> &daTham) {
    if (viTriKyTu == (int)tu.size()) {
        return true;
    }

    int n = (int)luoi.size();
    int m = (int)luoi[0].size();

    if (hang < 0 || hang >= n || cot < 0 || cot >= m) {
        return false;
    }
    if (daTham[hang][cot] == true) {
        return false;
    }
    if (luoi[hang][cot] != tu[viTriKyTu]) {
        return false;
    }

    daTham[hang][cot] = true;

    int dHang[] = {0, 0, 1, -1};
    int dCot[] = {1, -1, 0, 0};

    for (int huong = 0; huong < 4; huong++) {
        int hangMoi = hang + dHang[huong];
        int cotMoi = cot + dCot[huong];
        if (timKiemTuDeQuy(luoi, tu, hangMoi, cotMoi, viTriKyTu + 1, daTham) == true) {
            daTham[hang][cot] = false; // quay lui trước khi trả về, để trạng thái nhất quán
            return true;
        }
    }

    daTham[hang][cot] = false; // quay lui
    return false;
}

bool exist(vector<vector<char>> luoi, string tu) {
    int n = (int)luoi.size();
    int m = (int)luoi[0].size();
    vector<vector<bool>> daTham(n, vector<bool>(m, false));

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (timKiemTuDeQuy(luoi, tu, i, j, 0, daTham) == true) {
                return true;
            }
        }
    }

    return false;
}
```

#### PYTHON
```python
def tim_kiem_tu_de_quy(luoi, tu, hang, cot, vi_tri_ky_tu, da_tham):
    if vi_tri_ky_tu == len(tu):
        return True

    n = len(luoi)
    m = len(luoi[0])

    if hang < 0 or hang >= n or cot < 0 or cot >= m:
        return False
    if da_tham[hang][cot] == True:
        return False
    if luoi[hang][cot] != tu[vi_tri_ky_tu]:
        return False

    da_tham[hang][cot] = True

    d_hang = [0, 0, 1, -1]
    d_cot = [1, -1, 0, 0]

    for huong in range(4):
        hang_moi = hang + d_hang[huong]
        cot_moi = cot + d_cot[huong]
        if tim_kiem_tu_de_quy(luoi, tu, hang_moi, cot_moi, vi_tri_ky_tu + 1, da_tham) == True:
            da_tham[hang][cot] = False
            return True

    da_tham[hang][cot] = False
    return False


def exist(luoi, tu):
    n = len(luoi)
    m = len(luoi[0])
    da_tham = [[False] * m for _ in range(n)]

    for i in range(n):
        for j in range(m):
            if tim_kiem_tu_de_quy(luoi, tu, i, j, 0, da_tham) == True:
                return True

    return False
```
</details>

<details>
<summary>Lời giải Bài 13 — Merge Sort tự cài đặt</summary>

Giống hệt code khung mẫu, mục 7.4 — khuyến khích học sinh tự viết lại hoàn toàn từ đầu, chỉ xem lại code mẫu để đối chiếu sau khi đã thử tự làm.
</details>

<details>
<summary>Lời giải Bài 14 — Count of Smaller Numbers After Self</summary>

**Ý tưởng:** Kết hợp Merge Sort với việc theo dõi chỉ số gốc của từng phần tử. Trong quá trình gộp, mỗi khi một phần tử từ nửa phải được đặt vào mảng kết quả **trước** các phần tử còn lại của nửa trái, ta biết các phần tử còn lại đó (bên trái) đều có ít nhất 1 phần tử nhỏ hơn nằm bên phải chúng.

#### C++
```cpp
void demSoNhoHonBenPhai(vector<pair<int,int>> &a, int trai, int phai, vector<int> &ketQua) {
    // a[i] = (giá trị, chỉ số gốc)
    if (trai >= phai) {
        return;
    }

    int giua = trai + (phai - trai) / 2;
    demSoNhoHonBenPhai(a, trai, giua, ketQua);
    demSoNhoHonBenPhai(a, giua + 1, phai, ketQua);

    vector<pair<int,int>> phanTraDaSapXep(a.begin() + trai, a.begin() + giua + 1);
    vector<pair<int,int>> phanPhaiDaSapXep(a.begin() + giua + 1, a.begin() + phai + 1);

    int conTroTrai = 0, conTroPhai = 0, viTriGhi = trai;

    while (conTroTrai < (int)phanTraDaSapXep.size() && conTroPhai < (int)phanPhaiDaSapXep.size()) {
        if (phanTraDaSapXep[conTroTrai].first <= phanPhaiDaSapXep[conTroPhai].first) {
            // Số lượng phần tử bên phải đã "đi qua" trước phần tử này chính là conTroPhai
            ketQua[phanTraDaSapXep[conTroTrai].second] += conTroPhai;
            a[viTriGhi] = phanTraDaSapXep[conTroTrai];
            conTroTrai = conTroTrai + 1;
        } else {
            a[viTriGhi] = phanPhaiDaSapXep[conTroPhai];
            conTroPhai = conTroPhai + 1;
        }
        viTriGhi = viTriGhi + 1;
    }

    while (conTroTrai < (int)phanTraDaSapXep.size()) {
        ketQua[phanTraDaSapXep[conTroTrai].second] += conTroPhai;
        a[viTriGhi] = phanTraDaSapXep[conTroTrai];
        conTroTrai = conTroTrai + 1;
        viTriGhi = viTriGhi + 1;
    }

    while (conTroPhai < (int)phanPhaiDaSapXep.size()) {
        a[viTriGhi] = phanPhaiDaSapXep[conTroPhai];
        conTroPhai = conTroPhai + 1;
        viTriGhi = viTriGhi + 1;
    }
}

vector<int> countSmaller(vector<int> nums) {
    int n = (int)nums.size();
    vector<pair<int,int>> a(n);
    for (int i = 0; i < n; i++) {
        a[i] = make_pair(nums[i], i);
    }

    vector<int> ketQua(n, 0);
    demSoNhoHonBenPhai(a, 0, n - 1, ketQua);

    return ketQua;
}
```

#### PYTHON
```python
def dem_so_nho_hon_ben_phai(a, trai, phai, ket_qua):
    if trai >= phai:
        return

    giua = trai + (phai - trai) // 2
    dem_so_nho_hon_ben_phai(a, trai, giua, ket_qua)
    dem_so_nho_hon_ben_phai(a, giua + 1, phai, ket_qua)

    phan_trai_da_sap_xep = a[trai:giua+1]
    phan_phai_da_sap_xep = a[giua+1:phai+1]

    con_tro_trai = 0
    con_tro_phai = 0
    vi_tri_ghi = trai

    while con_tro_trai < len(phan_trai_da_sap_xep) and con_tro_phai < len(phan_phai_da_sap_xep):
        if phan_trai_da_sap_xep[con_tro_trai][0] <= phan_phai_da_sap_xep[con_tro_phai][0]:
            chi_so_goc = phan_trai_da_sap_xep[con_tro_trai][1]
            ket_qua[chi_so_goc] = ket_qua[chi_so_goc] + con_tro_phai
            a[vi_tri_ghi] = phan_trai_da_sap_xep[con_tro_trai]
            con_tro_trai = con_tro_trai + 1
        else:
            a[vi_tri_ghi] = phan_phai_da_sap_xep[con_tro_phai]
            con_tro_phai = con_tro_phai + 1
        vi_tri_ghi = vi_tri_ghi + 1

    while con_tro_trai < len(phan_trai_da_sap_xep):
        chi_so_goc = phan_trai_da_sap_xep[con_tro_trai][1]
        ket_qua[chi_so_goc] = ket_qua[chi_so_goc] + con_tro_phai
        a[vi_tri_ghi] = phan_trai_da_sap_xep[con_tro_trai]
        con_tro_trai = con_tro_trai + 1
        vi_tri_ghi = vi_tri_ghi + 1

    while con_tro_phai < len(phan_phai_da_sap_xep):
        a[vi_tri_ghi] = phan_phai_da_sap_xep[con_tro_phai]
        con_tro_phai = con_tro_phai + 1
        vi_tri_ghi = vi_tri_ghi + 1


def count_smaller(nums):
    n = len(nums)
    a = [(nums[i], i) for i in range(n)]
    ket_qua = [0] * n

    dem_so_nho_hon_ben_phai(a, 0, n - 1, ket_qua)

    return ket_qua
```
</details>

<details>
<summary>Lời giải Bài 15 — Different Ways to Add Parentheses</summary>

#### C++
```cpp
vector<long long> tinhTatCaKetQua(string bieuThuc) {
    vector<long long> ketQua;

    bool laSoThuanTuy = true;
    for (int i = 0; i < (int)bieuThuc.size(); i++) {
        if (bieuThuc[i] == '+' || bieuThuc[i] == '-' || bieuThuc[i] == '*') {
            laSoThuanTuy = false;
            break;
        }
    }

    if (laSoThuanTuy == true) {
        ketQua.push_back(stoll(bieuThuc));
        return ketQua;
    }

    for (int i = 0; i < (int)bieuThuc.size(); i++) {
        char kyTu = bieuThuc[i];
        if (kyTu == '+' || kyTu == '-' || kyTu == '*') {
            string benTrai = bieuThuc.substr(0, i);
            string benPhai = bieuThuc.substr(i + 1);

            vector<long long> ketQuaTrai = tinhTatCaKetQua(benTrai);
            vector<long long> ketQuaPhai = tinhTatCaKetQua(benPhai);

            for (int j = 0; j < (int)ketQuaTrai.size(); j++) {
                for (int k = 0; k < (int)ketQuaPhai.size(); k++) {
                    long long giaTri;
                    if (kyTu == '+') {
                        giaTri = ketQuaTrai[j] + ketQuaPhai[k];
                    } else if (kyTu == '-') {
                        giaTri = ketQuaTrai[j] - ketQuaPhai[k];
                    } else {
                        giaTri = ketQuaTrai[j] * ketQuaPhai[k];
                    }
                    ketQua.push_back(giaTri);
                }
            }
        }
    }

    return ketQua;
}
```

#### PYTHON
```python
def tinh_tat_ca_ket_qua(bieu_thuc):
    la_so_thuan_tuy = True
    for ky_tu in bieu_thuc:
        if ky_tu == '+' or ky_tu == '-' or ky_tu == '*':
            la_so_thuan_tuy = False
            break

    if la_so_thuan_tuy == True:
        return [int(bieu_thuc)]

    ket_qua = []

    for i in range(len(bieu_thuc)):
        ky_tu = bieu_thuc[i]
        if ky_tu == '+' or ky_tu == '-' or ky_tu == '*':
            ben_trai = bieu_thuc[:i]
            ben_phai = bieu_thuc[i+1:]

            ket_qua_trai = tinh_tat_ca_ket_qua(ben_trai)
            ket_qua_phai = tinh_tat_ca_ket_qua(ben_phai)

            for gt in ket_qua_trai:
                for gp in ket_qua_phai:
                    if ky_tu == '+':
                        ket_qua.append(gt + gp)
                    elif ky_tu == '-':
                        ket_qua.append(gt - gp)
                    else:
                        ket_qua.append(gt * gp)

    return ket_qua
```
</details>

<details>
<summary>Lời giải Bài 16 — Bài toán mê cung</summary>

#### C++
```cpp
void timDuongDiTrongMeCung(vector<vector<int>> &meCung, int hang, int cot,
                              string duongDi, vector<string> &tatCaKetQua) {
    int n = (int)meCung.size();
    int m = (int)meCung[0].size();

    if (hang == n - 1 && cot == m - 1) {
        tatCaKetQua.push_back(duongDi);
        return;
    }

    // Di chuyển xuống dưới
    if (hang + 1 < n && meCung[hang + 1][cot] == 0) {
        timDuongDiTrongMeCung(meCung, hang + 1, cot, duongDi + "X", tatCaKetQua);
    }

    // Di chuyển sang phải
    if (cot + 1 < m && meCung[hang][cot + 1] == 0) {
        timDuongDiTrongMeCung(meCung, hang, cot + 1, duongDi + "P", tatCaKetQua);
    }
}

int main() {
    vector<vector<int>> meCung = {
        {0, 0, 0},
        {0, 1, 0},
        {0, 0, 0}
    };

    vector<string> tatCaKetQua;
    if (meCung[0][0] == 0) {
        timDuongDiTrongMeCung(meCung, 0, 0, "", tatCaKetQua);
    }

    for (int i = 0; i < (int)tatCaKetQua.size(); i++) {
        cout << tatCaKetQua[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def tim_duong_di_trong_me_cung(me_cung, hang, cot, duong_di, tat_ca_ket_qua):
    n = len(me_cung)
    m = len(me_cung[0])

    if hang == n - 1 and cot == m - 1:
        tat_ca_ket_qua.append(duong_di)
        return

    if hang + 1 < n and me_cung[hang + 1][cot] == 0:
        tim_duong_di_trong_me_cung(me_cung, hang + 1, cot, duong_di + "X", tat_ca_ket_qua)

    if cot + 1 < m and me_cung[hang][cot + 1] == 0:
        tim_duong_di_trong_me_cung(me_cung, hang, cot + 1, duong_di + "P", tat_ca_ket_qua)


me_cung = [
    [0, 0, 0],
    [0, 1, 0],
    [0, 0, 0],
]

tat_ca_ket_qua = []
if me_cung[0][0] == 0:
    tim_duong_di_trong_me_cung(me_cung, 0, 0, "", tat_ca_ket_qua)

for ket_qua in tat_ca_ket_qua:
    print(ket_qua)
```
</details>
