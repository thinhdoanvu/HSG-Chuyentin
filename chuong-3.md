# Chương 3: Cấu trúc dữ liệu cơ bản (STL/Built-in)

> Chương này trình bày chi tiết từng cấu trúc dữ liệu: cách khởi tạo, các thao tác thêm/xoá/truy cập, khi nào nên dùng cấu trúc nào, và bài tập minh hoạ đi kèm. Code được viết theo phong cách đơn giản, rõ ràng, mỗi câu lệnh trên một dòng riêng, phù hợp cho học sinh mới bắt đầu.

---

## 3.1. Pair (Cặp giá trị)

**Vấn đề:** Rất nhiều bài toán cần lưu **2 giá trị đi liền với nhau** — ví dụ: một điểm số đi kèm tên học sinh, một toạ độ (x, y), hoặc một cạnh trong đồ thị gồm (đỉnh đến, trọng số). Nếu dùng 2 mảng riêng biệt để lưu 2 giá trị này, ta sẽ phải luôn giữ đồng bộ 2 mảng đó — dễ gây lỗi khi sắp xếp hoặc xoá phần tử. `pair` cho phép gộp 2 giá trị này thành **một đơn vị duy nhất**.

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    // Cách 1: dùng make_pair
    pair<int, string> hocSinh1 = make_pair(90, "An");

    // Cách 2: dùng dấu ngoặc nhọn (khởi tạo trực tiếp)
    pair<int, string> hocSinh2 = {85, "Binh"};

    // Cách 3: khai báo rồi gán sau
    pair<int, int> toaDo;
    toaDo.first = 5;
    toaDo.second = 10;

    cout << hocSinh1.first << " " << hocSinh1.second << endl;
    cout << hocSinh2.first << " " << hocSinh2.second << endl;
    cout << toaDo.first << " " << toaDo.second << endl;

    return 0;
}
```
```python
# Python không có kiểu pair riêng, ta dùng tuple (không sửa được sau khi tạo)
# hoặc dùng list (sửa được) để thay thế

# Cách 1: dùng tuple
hoc_sinh_1 = (90, "An")

# Cách 2: dùng list, có thể sửa từng phần tử sau khi tạo
hoc_sinh_2 = [85, "Binh"]

# Truy cập phần tử bằng chỉ số 0 và 1 (không có .first / .second như C++)
diem_1 = hoc_sinh_1[0]
ten_1 = hoc_sinh_1[1]

print(diem_1, ten_1)
print(hoc_sinh_2[0], hoc_sinh_2[1])

# Nếu muốn đặt tên rõ ràng như .first/.second, có thể dùng namedtuple
from collections import namedtuple

HocSinh = namedtuple("HocSinh", ["diem", "ten"])
hoc_sinh_3 = HocSinh(diem=95, ten="Chi")
print(hoc_sinh_3.diem, hoc_sinh_3.ten)
```

### Truy cập và thay đổi giá trị

```cpp
pair<int, int> p = {3, 7};

// Truy cập
int giaTriDau = p.first;
int giaTriSau = p.second;

// Thay đổi giá trị
p.first = 10;
p.second = 20;

cout << p.first << " " << p.second << endl;
```
```python
p = [3, 7]  # dùng list vì cần thay đổi được giá trị

gia_tri_dau = p[0]
gia_tri_sau = p[1]

p[0] = 10
p[1] = 20

print(p[0], p[1])
```

### So sánh 2 pair (rất hữu ích khi sắp xếp)

```cpp
pair<int, int> a = {1, 5};
pair<int, int> b = {1, 3};

// pair so sánh theo QUY TẮC TỪ ĐIỂN: so sánh .first trước, nếu bằng nhau mới so sánh .second
if (a > b) {
    cout << "a lon hon b" << endl;
} else {
    cout << "a khong lon hon b" << endl;
}
// Kết quả: "a lon hon b" vì .first bằng nhau (đều là 1), nhưng .second của a (5) > .second của b (3)
```
```python
a = (1, 5)
b = (1, 3)

# Python cũng so sánh tuple theo quy tắc từ điển giống C++
if a > b:
    print("a lon hon b")
else:
    print("a khong lon hon b")
```

### Dùng Pair để sắp xếp theo nhiều tiêu chí

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<pair<int, string>> danhSachHocSinh;
    danhSachHocSinh.push_back(make_pair(80, "Binh"));
    danhSachHocSinh.push_back(make_pair(90, "An"));
    danhSachHocSinh.push_back(make_pair(80, "An"));

    // sort() mặc định sẽ tự động so sánh theo .first trước, rồi đến .second
    // -> kết quả: sắp xếp theo điểm tăng dần, nếu bằng điểm thì theo tên tăng dần
    sort(danhSachHocSinh.begin(), danhSachHocSinh.end());

    for (int i = 0; i < (int)danhSachHocSinh.size(); i++) {
        cout << danhSachHocSinh[i].second << " - " << danhSachHocSinh[i].first << endl;
    }

    return 0;
}
```
```python
danh_sach_hoc_sinh = [(80, "Binh"), (90, "An"), (80, "An")]

# sort() mặc định cũng tự động so sánh phần tử đầu trước, rồi đến phần tử sau
danh_sach_hoc_sinh.sort()

for hoc_sinh in danh_sach_hoc_sinh:
    diem = hoc_sinh[0]
    ten = hoc_sinh[1]
    print(ten, "-", diem)
```

### Khi nào nên dùng Pair?
- Cần lưu 2 giá trị liên kết chặt chẽ với nhau mà không cần tạo hẳn 1 struct/class riêng.
- Cần sắp xếp theo nhiều tiêu chí — pair tự động so sánh theo `.first` trước rồi đến `.second`, rất tiện khi kết hợp với `sort()`.
- Biểu diễn cạnh trong đồ thị: `pair<int,int>` cho (đỉnh, trọng số), hoặc toạ độ (x, y).
- Nếu cần lưu **hơn 2 giá trị**, dùng `tuple` (C++) hoặc tuple/namedtuple (Python), hoặc tạo hẳn một `struct`.

### Bài tập minh hoạ
Cho danh sách các trận đấu, mỗi trận gồm (điểm đội A, điểm đội B). Sắp xếp danh sách theo hiệu số (điểm A trừ điểm B) giảm dần.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<pair<int, int>> danhSachTran;
    danhSachTran.push_back(make_pair(3, 1));
    danhSachTran.push_back(make_pair(2, 2));
    danhSachTran.push_back(make_pair(5, 0));

    vector<pair<int, int>> danhSachCoHieuSo;
    for (int i = 0; i < (int)danhSachTran.size(); i++) {
        int diemA = danhSachTran[i].first;
        int diemB = danhSachTran[i].second;
        int hieuSo = diemA - diemB;
        danhSachCoHieuSo.push_back(make_pair(hieuSo, i));
    }

    sort(danhSachCoHieuSo.begin(), danhSachCoHieuSo.end(), greater<pair<int,int>>());

    for (int i = 0; i < (int)danhSachCoHieuSo.size(); i++) {
        int hieuSo = danhSachCoHieuSo[i].first;
        int chiSoTranDau = danhSachCoHieuSo[i].second;
        cout << "Tran " << chiSoTranDau << " co hieu so: " << hieuSo << endl;
    }

    return 0;
}
```
```python
danh_sach_tran = [(3, 1), (2, 2), (5, 0)]

danh_sach_co_hieu_so = []
for chi_so in range(len(danh_sach_tran)):
    diem_a = danh_sach_tran[chi_so][0]
    diem_b = danh_sach_tran[chi_so][1]
    hieu_so = diem_a - diem_b
    danh_sach_co_hieu_so.append((hieu_so, chi_so))

danh_sach_co_hieu_so.sort(reverse=True)

for cap in danh_sach_co_hieu_so:
    hieu_so = cap[0]
    chi_so_tran_dau = cap[1]
    print("Tran", chi_so_tran_dau, "co hieu so:", hieu_so)
```

---

## 3.2. Vector (Mảng động)

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a;                             // vector rỗng
    vector<int> b(5);                           // 5 phần tử, giá trị mặc định là 0
    vector<int> c(5, 100);                       // 5 phần tử, tất cả đều mang giá trị 100

    vector<int> d;
    d.push_back(1);
    d.push_back(2);
    d.push_back(3);

    // Ma trận 2 chiều: 3 dòng, 4 cột, tất cả giá trị ban đầu là 0
    vector<vector<int>> mangHaiChieu(3, vector<int>(4, 0));

    return 0;
}
```
```python
a = []                                  # danh sách rỗng
b = [0] * 5                              # 5 phần tử, giá trị 0
c = [100] * 5                             # 5 phần tử, tất cả đều là 100

d = []
d.append(1)
d.append(2)
d.append(3)

# Ma trận 2 chiều: 3 dòng, 4 cột
mang_hai_chieu = []
for i in range(3):
    dong_moi = [0] * 4
    mang_hai_chieu.append(dong_moi)
# LƯU Ý QUAN TRỌNG: không được viết mang_hai_chieu = [[0]*4]*3
# vì cách viết đó khiến cả 3 dòng CÙNG tham chiếu đến 1 danh sách duy nhất,
# khi sửa 1 dòng thì cả 3 dòng đều bị sửa theo.
```

### Thêm phần tử

```cpp
vector<int> a;
a.push_back(10);                       // thêm vào CUỐI - nhanh, chi phí trung bình O(1)

a.insert(a.begin(), 5);                 // thêm vào ĐẦU - chậm, chi phí O(n)
a.insert(a.begin() + 2, 7);              // thêm vào vị trí thứ 2 - chậm, chi phí O(n)
```
```python
a = []
a.append(10)                            # thêm vào cuối - nhanh, chi phí trung bình O(1)

a.insert(0, 5)                           # thêm vào đầu - chậm, chi phí O(n)
a.insert(2, 7)                           # thêm vào vị trí thứ 2 - chậm, chi phí O(n)
```

### Xoá phần tử

```cpp
vector<int> a = {1, 2, 3, 4, 5};

a.pop_back();                           // xoá phần tử cuối cùng - nhanh, O(1)
a.erase(a.begin());                      // xoá phần tử đầu tiên - chậm, O(n)
a.erase(a.begin() + 1);                   // xoá phần tử ở vị trí thứ 1 - chậm, O(n)
```
```python
a = [1, 2, 3, 4, 5]

a.pop()                                  # xoá phần tử cuối cùng - nhanh, O(1)
a.pop(0)                                  # xoá phần tử đầu tiên - chậm, O(n)
del a[1]                                  # xoá phần tử ở vị trí thứ 1 - chậm, O(n)
```

### Truy cập và duyệt qua các phần tử

```cpp
vector<int> a = {10, 20, 30};

int giaTri = a[1];                        // truy cập nhanh, KHÔNG kiểm tra biên
int giaTriAnToan = a.at(1);                // truy cập có kiểm tra biên, dùng khi cần debug

for (int i = 0; i < (int)a.size(); i++) {
    cout << a[i] << " ";
}
cout << endl;

// Duyệt và sửa từng phần tử: mỗi phần tử nhân đôi lên
for (int i = 0; i < (int)a.size(); i++) {
    a[i] = a[i] * 2;
}
```
```python
a = [10, 20, 30]

gia_tri = a[1]                            # Python tự động kiểm tra biên, báo lỗi nếu sai chỉ số

for i in range(len(a)):
    print(a[i], end=" ")
print()

# Duyệt và sửa từng phần tử: mỗi phần tử nhân đôi lên
for i in range(len(a)):
    a[i] = a[i] * 2
```

### Khi nào nên dùng Vector?
- Dùng làm lựa chọn mặc định cho hầu hết các bài toán cần lưu một dãy phần tử có thứ tự.
- Cần truy cập nhanh theo chỉ số — Vector cho tốc độ nhanh nhất trong việc này.
- **Không nên dùng** khi cần thêm hoặc xoá phần tử thường xuyên ở **đầu** mảng với số lượng phần tử lớn — khi đó nên dùng Deque (mục 3.4).

### Bài tập minh hoạ
Cho một vector chứa n số nguyên, thực hiện q truy vấn: mỗi truy vấn hoặc "thêm số x vào cuối", hoặc "xoá phần tử cuối cùng", hoặc "in ra phần tử ở vị trí k".

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a;
    int soLuongTruyVan;
    cin >> soLuongTruyVan;

    for (int i = 0; i < soLuongTruyVan; i++) {
        string loaiTruyVan;
        cin >> loaiTruyVan;

        if (loaiTruyVan == "THEM") {
            int x;
            cin >> x;
            a.push_back(x);
        } else if (loaiTruyVan == "XOA") {
            a.pop_back();
        } else {
            int k;
            cin >> k;
            cout << a[k] << endl;
        }
    }

    return 0;
}
```
```python
a = []

so_luong_truy_van = int(input())

for i in range(so_luong_truy_van):
    du_lieu_dong = input().split()
    loai_truy_van = du_lieu_dong[0]

    if loai_truy_van == "THEM":
        x = int(du_lieu_dong[1])
        a.append(x)
    elif loai_truy_van == "XOA":
        a.pop()
    else:
        k = int(du_lieu_dong[1])
        print(a[k])
```

---

## 3.3. Stack (Ngăn xếp)

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    stack<int> nganXep;  // ngăn xếp rỗng

    return 0;
}
```
```python
ngan_xep = []  # Python không có kiểu Stack riêng, dùng list là đủ vì append/pop ở cuối đều nhanh
```

### Thêm, xoá, truy cập

```cpp
stack<int> nganXep;

nganXep.push(10);                     // thêm phần tử lên đỉnh - O(1)
nganXep.push(20);

int dinh = nganXep.top();              // xem giá trị đỉnh, KHÔNG xoá
nganXep.pop();                          // xoá phần tử đỉnh, KHÔNG trả về giá trị

bool rong = nganXep.empty();
int kichThuoc = nganXep.size();
```
```python
ngan_xep = []

ngan_xep.append(10)                    # thêm phần tử lên đỉnh - O(1)
ngan_xep.append(20)

dinh = ngan_xep[-1]                     # xem giá trị đỉnh, KHÔNG xoá
gia_tri_bi_xoa = ngan_xep.pop()          # xoá phần tử đỉnh, Python TRẢ VỀ giá trị bị xoá (khác C++)

rong = (len(ngan_xep) == 0)
kich_thuoc = len(ngan_xep)
```

> **Lưu ý quan trọng:** luôn kiểm tra ngăn xếp không rỗng trước khi gọi `top()`/`pop()`. Nếu gọi trên ngăn xếp rỗng, chương trình C++ sẽ có lỗi nghiêm trọng khó phát hiện, còn Python sẽ báo lỗi ngay lập tức.

### Khi nào nên dùng Stack?
- Cần xử lý theo nguyên tắc "vào sau ra trước" (phần tử thêm vào sau cùng sẽ được lấy ra đầu tiên).
- Kiểm tra dấu ngoặc hợp lệ, chức năng undo/redo, duyệt đồ thị theo kiểu DFS mà không dùng đệ quy.
- Tính giá trị của biểu thức toán học ở dạng hậu tố (postfix).

### Bài tập minh hoạ
Kiểm tra một chuỗi chỉ chứa các ký tự ngoặc tròn, ngoặc vuông, ngoặc nhọn có hợp lệ hay không (mỗi ngoặc mở phải có ngoặc đóng tương ứng, đúng thứ tự lồng nhau).

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraNgoacHopLe(string chuoi) {
    stack<char> nganXep;

    for (int i = 0; i < (int)chuoi.size(); i++) {
        char kyTu = chuoi[i];

        if (kyTu == '(' || kyTu == '[' || kyTu == '{') {
            nganXep.push(kyTu);
        } else {
            if (nganXep.empty()) {
                return false;
            }

            char dinhNganXep = nganXep.top();
            nganXep.pop();

            if (kyTu == ')' && dinhNganXep != '(') {
                return false;
            }
            if (kyTu == ']' && dinhNganXep != '[') {
                return false;
            }
            if (kyTu == '}' && dinhNganXep != '{') {
                return false;
            }
        }
    }

    return nganXep.empty();
}

int main() {
    string chuoi = "{[()]}";
    if (kiemTraNgoacHopLe(chuoi) == true) {
        cout << "Hop le" << endl;
    } else {
        cout << "Khong hop le" << endl;
    }
    return 0;
}
```
```python
def kiem_tra_ngoac_hop_le(chuoi):
    ngan_xep = []
    cap_ngoac_tuong_ung = {')': '(', ']': '[', '}': '{'}

    for ky_tu in chuoi:
        if ky_tu == '(' or ky_tu == '[' or ky_tu == '{':
            ngan_xep.append(ky_tu)
        else:
            if len(ngan_xep) == 0:
                return False

            dinh_ngan_xep = ngan_xep.pop()

            if dinh_ngan_xep != cap_ngoac_tuong_ung[ky_tu]:
                return False

    return len(ngan_xep) == 0


chuoi = "{[()]}"
if kiem_tra_ngoac_hop_le(chuoi) == True:
    print("Hop le")
else:
    print("Khong hop le")
```

---

## 3.4. Queue (Hàng đợi)

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    queue<int> hangDoi;  // hàng đợi rỗng

    return 0;
}
```
```python
from collections import deque

hang_doi = deque()
# LƯU Ý QUAN TRỌNG: KHÔNG dùng list thường làm hàng đợi trong Python,
# vì thao tác list.pop(0) tốn O(n), phải luôn dùng collections.deque
```

### Thêm, xoá, truy cập

```cpp
queue<int> hangDoi;

hangDoi.push(10);                    // thêm vào CUỐI hàng đợi - O(1)
hangDoi.push(20);

int dauHangDoi = hangDoi.front();      // xem phần tử đầu tiên, KHÔNG xoá
int cuoiHangDoi = hangDoi.back();       // xem phần tử cuối cùng, KHÔNG xoá

hangDoi.pop();                          // xoá phần tử đầu tiên, KHÔNG trả về giá trị
```
```python
from collections import deque

hang_doi = deque()

hang_doi.append(10)                   # thêm vào cuối hàng đợi - O(1)
hang_doi.append(20)

dau_hang_doi = hang_doi[0]
cuoi_hang_doi = hang_doi[-1]

gia_tri_bi_xoa = hang_doi.popleft()    # xoá phần tử đầu tiên, TRẢ VỀ giá trị bị xoá
```

### Khi nào nên dùng Queue?
- Cần xử lý theo nguyên tắc "vào trước ra trước".
- **Bắt buộc phải dùng** khi duyệt đồ thị theo thuật toán BFS (duyệt theo từng lớp, từng mức).
- Mô phỏng hàng chờ trong thực tế: ai đến trước được xử lý trước.

### Bài tập minh hoạ
Đếm số bước di chuyển ngắn nhất của quân mã (trong cờ vua) từ ô (0, 0) đến ô (x, y) trên một bàn cờ vô hạn.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;

int demSoBuocDiChuyenQuanMa(int xDich, int yDich) {
    vector<int> dichChuyenX = {1, 1, -1, -1, 2, 2, -2, -2};
    vector<int> dichChuyenY = {2, -2, 2, -2, 1, -1, 1, -1};

    map<pair<int,int>, int> khoangCach;
    queue<pair<int,int>> hangDoi;

    khoangCach[make_pair(0, 0)] = 0;
    hangDoi.push(make_pair(0, 0));

    while (!hangDoi.empty()) {
        pair<int,int> viTriHienTai = hangDoi.front();
        hangDoi.pop();

        int xHienTai = viTriHienTai.first;
        int yHienTai = viTriHienTai.second;

        if (xHienTai == xDich && yHienTai == yDich) {
            return khoangCach[viTriHienTai];
        }

        for (int huong = 0; huong < 8; huong++) {
            int xMoi = xHienTai + dichChuyenX[huong];
            int yMoi = yHienTai + dichChuyenY[huong];
            pair<int,int> viTriMoi = make_pair(xMoi, yMoi);

            if (khoangCach.count(viTriMoi) == 0) {
                khoangCach[viTriMoi] = khoangCach[viTriHienTai] + 1;
                hangDoi.push(viTriMoi);
            }
        }
    }

    return -1;
}

int main() {
    cout << demSoBuocDiChuyenQuanMa(4, 4) << endl;
    return 0;
}
```
```python
from collections import deque


def dem_so_buoc_di_chuyen_quan_ma(x_dich, y_dich):
    dich_chuyen_x = [1, 1, -1, -1, 2, 2, -2, -2]
    dich_chuyen_y = [2, -2, 2, -2, 1, -1, 1, -1]

    khoang_cach = {}
    hang_doi = deque()

    khoang_cach[(0, 0)] = 0
    hang_doi.append((0, 0))

    while len(hang_doi) > 0:
        vi_tri_hien_tai = hang_doi.popleft()
        x_hien_tai = vi_tri_hien_tai[0]
        y_hien_tai = vi_tri_hien_tai[1]

        if x_hien_tai == x_dich and y_hien_tai == y_dich:
            return khoang_cach[vi_tri_hien_tai]

        for huong in range(8):
            x_moi = x_hien_tai + dich_chuyen_x[huong]
            y_moi = y_hien_tai + dich_chuyen_y[huong]
            vi_tri_moi = (x_moi, y_moi)

            if vi_tri_moi not in khoang_cach:
                khoang_cach[vi_tri_moi] = khoang_cach[vi_tri_hien_tai] + 1
                hang_doi.append(vi_tri_moi)

    return -1


print(dem_so_buoc_di_chuyen_quan_ma(4, 4))
```

---

## 3.5. Deque (Hàng đợi 2 đầu)

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    deque<int> hangDoiHaiDau;  // rỗng

    return 0;
}
```
```python
from collections import deque

hang_doi_hai_dau = deque()
```

### Thêm, xoá, truy cập (thao tác cả 2 đầu, đều nhanh O(1))

```cpp
deque<int> dq;

dq.push_back(10);                    // thêm vào cuối
dq.push_front(20);                    // thêm vào đầu

int dau = dq.front();
int cuoi = dq.back();

dq.pop_back();                        // xoá phần tử cuối
dq.pop_front();                        // xoá phần tử đầu

int phanTuOViTri2 = dq[2];             // deque hỗ trợ truy cập trực tiếp theo chỉ số, giống vector
```
```python
from collections import deque

dq = deque()

dq.append(10)                        # thêm vào cuối
dq.appendleft(20)                     # thêm vào đầu

dau = dq[0]
cuoi = dq[-1]

dq.pop()                              # xoá phần tử cuối
dq.popleft()                           # xoá phần tử đầu

phan_tu_o_vi_tri_2 = dq[2]             # deque của Python cũng hỗ trợ truy cập theo chỉ số
```

### Khi nào nên dùng Deque?
- Cần thêm hoặc xoá phần tử ở **cả 2 đầu** một cách nhanh chóng — Vector không làm được điều này hiệu quả ở đầu mảng.
- Là nền tảng cho kỹ thuật Sliding Window và Monotonic Queue sẽ học ở Chương 6.
- Có thể dùng thay cho Stack hoặc Queue nếu chưa chắc chắn sẽ cần thao tác ở đầu nào trong tương lai.

### Bài tập minh hoạ
Cho một dãy thao tác thêm số vào đầu hoặc cuối một dãy số, sau mỗi thao tác hãy in ra tổng của phần tử đầu tiên và phần tử cuối cùng hiện tại.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    deque<int> dq;
    int soLuongTruyVan;
    cin >> soLuongTruyVan;

    for (int i = 0; i < soLuongTruyVan; i++) {
        string loaiThemVao;
        int giaTri;
        cin >> loaiThemVao >> giaTri;

        if (loaiThemVao == "DAU") {
            dq.push_front(giaTri);
        } else {
            dq.push_back(giaTri);
        }

        int tongDauCuoi = dq.front() + dq.back();
        cout << tongDauCuoi << endl;
    }

    return 0;
}
```
```python
from collections import deque

dq = deque()

so_luong_truy_van = int(input())

for i in range(so_luong_truy_van):
    du_lieu_dong = input().split()
    loai_them_vao = du_lieu_dong[0]
    gia_tri = int(du_lieu_dong[1])

    if loai_them_vao == "DAU":
        dq.appendleft(gia_tri)
    else:
        dq.append(gia_tri)

    tong_dau_cuoi = dq[0] + dq[-1]
    print(tong_dau_cuoi)
```

---

## 3.6. Set (Tập hợp có thứ tự)

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    set<int> tapHop;                       // rỗng, tự động sắp xếp tăng dần
    set<int, greater<int>> tapHopGiamDan;   // tự động sắp xếp giảm dần

    set<int> tapHopKhoiTao;
    tapHopKhoiTao.insert(5);
    tapHopKhoiTao.insert(1);
    tapHopKhoiTao.insert(8);
    tapHopKhoiTao.insert(3);
    // Bên trong, tapHopKhoiTao đã tự động được sắp xếp thành: 1, 3, 5, 8

    return 0;
}
```
```python
# Python có sẵn kiểu set, nhưng KHÔNG tự động giữ thứ tự sắp xếp
# Nếu cần duy trì thứ tự và tìm cận trên/cận dưới, phải cài thư viện: pip install sortedcontainers
from sortedcontainers import SortedList

tap_hop = SortedList()
tap_hop.add(5)
tap_hop.add(1)
tap_hop.add(8)
tap_hop.add(3)
# Bên trong, tap_hop đã được sắp xếp thành: 1, 3, 5, 8
```

### Thêm, xoá, tìm kiếm

```cpp
set<int> s;
s.insert(10);                          // thêm phần tử - nếu đã tồn tại thì không thêm trùng
s.insert(20);
s.insert(30);

s.erase(20);                            // xoá theo giá trị

bool tonTai = (s.count(10) > 0);         // kiểm tra tồn tại, trả về 0 hoặc 1

// Tìm cận trên và cận dưới
auto conTroCanDuoi = s.lower_bound(15);   // trỏ tới phần tử ĐẦU TIÊN >= 15
auto conTroCanTren = s.upper_bound(15);    // trỏ tới phần tử ĐẦU TIÊN > 15

if (conTroCanDuoi != s.end()) {
    cout << "Can duoi: " << *conTroCanDuoi << endl;
}
```
```python
from sortedcontainers import SortedList

s = SortedList()
s.add(10)
s.add(20)
s.add(30)

s.remove(20)                            # xoá theo giá trị

ton_tai = (10 in s)                      # kiểm tra tồn tại, trả về True/False

# Tìm cận trên và cận dưới bằng phương thức bisect_left / bisect_right
vi_tri_can_duoi = s.bisect_left(15)       # vị trí phần tử ĐẦU TIÊN >= 15
if vi_tri_can_duoi < len(s):
    print("Can duoi:", s[vi_tri_can_duoi])
```

### Khi nào nên dùng Set (có thứ tự)?
- Cần duy trì một tập hợp luôn ở trạng thái đã sắp xếp trong khi liên tục thêm/xoá phần tử.
- Cần tìm "cận trên/cận dưới" của một giá trị — kiểu tập hợp không thứ tự (mục 3.8) không hỗ trợ việc này.
- Cần duyệt qua các phần tử theo đúng thứ tự tăng dần hoặc giảm dần.

### Bài tập minh hoạ
Xử lý nhiều truy vấn: thêm số x vào tập hợp, xoá số x khỏi tập hợp, hoặc hỏi "số nhỏ nhất trong tập hợp hiện tại mà lớn hơn x là số nào?".

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    set<int> s;
    int soLuongTruyVan;
    cin >> soLuongTruyVan;

    for (int i = 0; i < soLuongTruyVan; i++) {
        string loaiTruyVan;
        cin >> loaiTruyVan;

        if (loaiTruyVan == "THEM") {
            int x;
            cin >> x;
            s.insert(x);
        } else if (loaiTruyVan == "XOA") {
            int x;
            cin >> x;
            s.erase(x);
        } else {
            int x;
            cin >> x;
            auto conTro = s.upper_bound(x);
            if (conTro != s.end()) {
                cout << *conTro << endl;
            } else {
                cout << -1 << endl;
            }
        }
    }

    return 0;
}
```
```python
from sortedcontainers import SortedList

s = SortedList()

so_luong_truy_van = int(input())

for i in range(so_luong_truy_van):
    du_lieu_dong = input().split()
    loai_truy_van = du_lieu_dong[0]

    if loai_truy_van == "THEM":
        x = int(du_lieu_dong[1])
        s.add(x)
    elif loai_truy_van == "XOA":
        x = int(du_lieu_dong[1])
        s.remove(x)
    else:
        x = int(du_lieu_dong[1])
        vi_tri = s.bisect_right(x)
        if vi_tri < len(s):
            print(s[vi_tri])
        else:
            print(-1)
```

---

## 3.7. Map (Bảng ánh xạ có thứ tự)

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    map<string, int> banDo;   // rỗng, các key sẽ tự động được sắp xếp tăng dần

    map<string, int> banDoKhoiTao;
    banDoKhoiTao["An"] = 90;
    banDoKhoiTao["Binh"] = 85;

    return 0;
}
```
```python
# dict của Python giữ nguyên thứ tự CHÈN vào (từ Python 3.7 trở đi),
# KHÔNG tự động sắp xếp theo key như map của C++.
# Nếu cần key luôn được sắp xếp, dùng: pip install sortedcontainers
from sortedcontainers import SortedDict

ban_do = SortedDict()

ban_do_khoi_tao = SortedDict()
ban_do_khoi_tao["An"] = 90
ban_do_khoi_tao["Binh"] = 85
```

### Thêm, xoá, truy cập

```cpp
map<string, int> mp;

mp["Chi"] = 95;                      // thêm mới, hoặc cập nhật nếu key đã tồn tại

mp["Diem"] = mp["Diem"] + 1;           // CẨN THẬN: mp["Diem"] sẽ tự tạo key này với giá trị 0
                                        // nếu key "Diem" chưa từng tồn tại trước đó

mp.erase("An");                        // xoá theo key

bool tonTai = (mp.count("An") > 0);     // kiểm tra tồn tại, KHÔNG tự tạo key mới

for (auto phanTu : mp) {
    string key = phanTu.first;
    int giaTri = phanTu.second;
    cout << key << ": " << giaTri << endl;
}
```
```python
mp = {}

mp["Chi"] = 95                        # thêm mới, hoặc cập nhật nếu key đã tồn tại

if "Diem" in mp:
    mp["Diem"] = mp["Diem"] + 1
else:
    mp["Diem"] = 1
# Cách viết trên AN TOÀN, tránh lỗi KeyError khi key chưa tồn tại

del mp["An"]                          # xoá theo key, sẽ báo lỗi nếu key không tồn tại

ton_tai = ("An" in mp)                 # kiểm tra tồn tại, KHÔNG gây lỗi và không tự tạo key mới

for key in mp:
    gia_tri = mp[key]
    print(key, ":", gia_tri)
```

> **Cạm bẫy kinh điển trong C++:** viết `mp["mot_key_nao_do"]` sẽ **tự động tạo** key đó với giá trị mặc định là 0, ngay cả khi bạn chỉ đang muốn **đọc** giá trị để kiểm tra. Nếu chỉ muốn kiểm tra tồn tại mà không muốn tạo key mới, hãy dùng `mp.count("key")`.

### Khi nào nên dùng Map (có thứ tự)?
- Tương tự Set nhưng cần lưu thêm một giá trị đi kèm mỗi key — ví dụ đếm tần suất xuất hiện, hoặc ánh xạ tên sang điểm số.
- Cần duyệt các phần tử theo đúng thứ tự của key, hoặc cần tìm cận trên/cận dưới theo key.

### Bài tập minh hoạ
Đếm số lần xuất hiện của từng từ trong một đoạn văn bản, in kết quả theo đúng thứ tự bảng chữ cái.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    map<string, int> tanSuat;
    string tu;

    while (cin >> tu) {
        tanSuat[tu] = tanSuat[tu] + 1;
    }

    for (auto phanTu : tanSuat) {
        string tuHienTai = phanTu.first;
        int soLan = phanTu.second;
        cout << tuHienTai << ": " << soLan << endl;
    }

    return 0;
}
```
```python
from sortedcontainers import SortedDict

tan_suat = SortedDict()

noi_dung = input().split()

for tu in noi_dung:
    if tu in tan_suat:
        tan_suat[tu] = tan_suat[tu] + 1
    else:
        tan_suat[tu] = 1

for tu in tan_suat:
    so_lan = tan_suat[tu]
    print(tu, ":", so_lan)
```

---

## 3.8. Unordered Set (Bảng băm — không có thứ tự)

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    unordered_set<int> tapHopBam;  // rỗng, thứ tự lưu trữ bên trong KHÔNG xác định

    return 0;
}
```
```python
tap_hop_bam = set()  # kiểu set mặc định của Python chính là bảng băm
```

### Thêm, xoá, tìm kiếm

```cpp
unordered_set<int> us;

us.insert(10);                       // thêm phần tử - nhanh, trung bình O(1)
us.erase(10);                         // xoá phần tử - nhanh, trung bình O(1)

bool tonTai = (us.count(10) > 0);      // kiểm tra tồn tại - nhanh, trung bình O(1)
```
```python
us = set()

us.add(10)                          # thêm phần tử - nhanh, trung bình O(1)
us.discard(10)                       # xoá phần tử - KHÔNG báo lỗi nếu chưa tồn tại
# us.remove(10)                      # cách khác để xoá - SẼ báo lỗi nếu chưa tồn tại

ton_tai = (10 in us)                 # kiểm tra tồn tại - nhanh, trung bình O(1)
```

### Khi nào nên dùng Unordered Set?
- Chỉ cần kiểm tra một giá trị có tồn tại hay không, hoặc loại bỏ các giá trị trùng lặp, mà **không quan tâm đến thứ tự** — nhanh hơn Set có thứ tự.
- **Lưu ý quan trọng:** trên một số trang luyện tập như Codeforces, có những đề bài cố tình tạo dữ liệu khiến `unordered_set<int>` trong C++ chạy chậm bất thường (gọi là "anti-hash test"). Nếu gặp tình huống này, có thể chuyển sang dùng `set` (có thứ tự) để an toàn hơn.

### Bài tập minh hoạ
Cho 2 mảng số nguyên A và B, tìm tất cả các phần tử xuất hiện trong mảng A nhưng **không** xuất hiện trong mảng B.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> timPhanTuChiCoOMangA(vector<int> mangA, vector<int> mangB) {
    unordered_set<int> tapHopB;
    for (int i = 0; i < (int)mangB.size(); i++) {
        tapHopB.insert(mangB[i]);
    }

    vector<int> ketQua;
    for (int i = 0; i < (int)mangA.size(); i++) {
        if (tapHopB.count(mangA[i]) == 0) {
            ketQua.push_back(mangA[i]);
        }
    }

    return ketQua;
}

int main() {
    vector<int> mangA = {1, 2, 3, 4, 5};
    vector<int> mangB = {3, 4, 5, 6, 7};

    vector<int> ketQua = timPhanTuChiCoOMangA(mangA, mangB);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;

    return 0;
}
```
```python
def tim_phan_tu_chi_co_o_mang_a(mang_a, mang_b):
    tap_hop_b = set()
    for gia_tri in mang_b:
        tap_hop_b.add(gia_tri)

    ket_qua = []
    for gia_tri in mang_a:
        if gia_tri not in tap_hop_b:
            ket_qua.append(gia_tri)

    return ket_qua


mang_a = [1, 2, 3, 4, 5]
mang_b = [3, 4, 5, 6, 7]

ket_qua = tim_phan_tu_chi_co_o_mang_a(mang_a, mang_b)
print(ket_qua)
```

---

## 3.9. Unordered Map (Bảng băm gán giá trị — không có thứ tự)

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    unordered_map<string, int> banDoBam;  // rỗng

    return 0;
}
```
```python
ban_do_bam = {}  # dict mặc định của Python chính là bảng băm

from collections import defaultdict
ban_do_bam_mac_dinh = defaultdict(int)  # tự động tạo giá trị mặc định 0 cho key chưa tồn tại
```

### Thêm, xoá, truy cập

```cpp
unordered_map<string, int> ump;

ump["An"] = 90;                       // thêm hoặc cập nhật - nhanh, trung bình O(1)
ump.erase("An");                       // xoá theo key - nhanh, trung bình O(1)

bool tonTai = (ump.count("An") > 0);    // kiểm tra tồn tại, KHÔNG tự tạo key mới
```
```python
ump = {}

ump["An"] = 90                       # thêm hoặc cập nhật
del ump["An"]                          # xoá theo key

ton_tai = ("An" in ump)                # kiểm tra tồn tại

from collections import defaultdict
dem_tan_suat = defaultdict(int)
dem_tan_suat["Chi"] = dem_tan_suat["Chi"] + 1  # với defaultdict, không cần kiểm tra tồn tại trước
```

### Khi nào nên dùng Unordered Map?
- Dùng để đếm tần suất, hoặc tra cứu nhanh khi **không** cần duyệt theo thứ tự của key — đây là trường hợp sử dụng phổ biến nhất trong lập trình thi đấu.
- Tương tự Unordered Set, cần cẩn thận với "anti-hash test" khi key là số nguyên trên Codeforces.

### Bài tập minh hoạ
Cho mảng n số nguyên và một số k, đếm số cặp (i, j) sao cho `a[i] - a[j] = k`.

**Lời giải:**
```cpp
#include <bits/stdc++.h>
using namespace std;

long long demSoCapCoHieuBangK(vector<int> a, int k) {
    unordered_map<int, int> tanSuat;
    long long soCap = 0;

    for (int i = 0; i < (int)a.size(); i++) {
        int giaTriCanTim = a[i] - k;

        if (tanSuat.count(giaTriCanTim) > 0) {
            soCap = soCap + tanSuat[giaTriCanTim];
        }

        tanSuat[a[i]] = tanSuat[a[i]] + 1;
    }

    return soCap;
}

int main() {
    vector<int> a = {1, 5, 3, 4, 2};
    int k = 2;

    cout << demSoCapCoHieuBangK(a, k) << endl;

    return 0;
}
```
```python
def dem_so_cap_co_hieu_bang_k(a, k):
    tan_suat = {}
    so_cap = 0

    for gia_tri in a:
        gia_tri_can_tim = gia_tri - k

        if gia_tri_can_tim in tan_suat:
            so_cap = so_cap + tan_suat[gia_tri_can_tim]

        if gia_tri in tan_suat:
            tan_suat[gia_tri] = tan_suat[gia_tri] + 1
        else:
            tan_suat[gia_tri] = 1

    return so_cap


a = [1, 5, 3, 4, 2]
k = 2

print(dem_so_cap_co_hieu_bang_k(a, k))
```

---

## 3.10. Priority Queue / Heap (Hàng đợi ưu tiên)

### Khởi tạo

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    priority_queue<int> hangDoiUuTien;                                        // MAX-HEAP (mặc định)
    priority_queue<int, vector<int>, greater<int>> hangDoiUuTienNhoNhat;         // MIN-HEAP

    return 0;
}
```
```python
import heapq

hang_doi_uu_tien = []  # module heapq của Python LUÔN xây dựng MIN-HEAP
```

### Thêm, xoá, truy cập

```cpp
priority_queue<int> pq;

pq.push(10);                          // thêm phần tử - O(log n)
pq.push(30);
pq.push(20);

int phanTuUuTienNhat = pq.top();        // xem phần tử ưu tiên cao nhất, KHÔNG xoá - O(1)
pq.pop();                                // xoá phần tử ưu tiên cao nhất - O(log n)
```
```python
import heapq

pq = []

heapq.heappush(pq, 10)                # thêm phần tử - O(log n)
heapq.heappush(pq, 30)
heapq.heappush(pq, 20)

phan_tu_nho_nhat = pq[0]               # xem phần tử nhỏ nhất, KHÔNG xoá - O(1)
gia_tri_bi_xoa = heapq.heappop(pq)      # xoá và trả về phần tử nhỏ nhất - O(log n)

# Để mô phỏng MAX-HEAP trong Python, ta đảo dấu giá trị khi thêm và khi lấy ra
pq_max = []
heapq.heappush(pq_max, -10)
gia_tri_lon_nhat = -heapq.heappop(pq_max)
```

### Khi nào nên dùng Priority Queue?
- Cần liên tục lấy ra phần tử lớn nhất hoặc nhỏ nhất trong một tập dữ liệu đang thay đổi liên tục (thêm phần tử mới thường xuyên).
- Là công cụ bắt buộc cho thuật toán Dijkstra (Chương 12), các bài toán lập lịch tham lam (Chương 25).
- **Không nên dùng** khi cần xoá một phần tử bất kỳ (không phải phần tử ưu tiên cao nhất) — khi đó nên dùng `multiset`.

### Bài tập minh hoạ
Cho n công việc, mỗi công việc có thời gian xử lý riêng. Chỉ có 1 máy xử lý tuần tự từng công việc. Hãy sắp xếp thứ tự xử lý sao cho tổng thời gian chờ của tất cả công việc là nhỏ nhất.

**Lời giải:** Chiến lược tối ưu là luôn xử lý công việc có thời gian ngắn nhất trước.
```cpp
#include <bits/stdc++.h>
using namespace std;

long long tinhTongThoiGianChoNhoNhat(vector<int> thoiGianXuLy) {
    priority_queue<int, vector<int>, greater<int>> hangDoiUuTienNhoNhat;

    for (int i = 0; i < (int)thoiGianXuLy.size(); i++) {
        hangDoiUuTienNhoNhat.push(thoiGianXuLy[i]);
    }

    long long thoiGianHienTai = 0;
    long long tongThoiGianCho = 0;

    while (!hangDoiUuTienNhoNhat.empty()) {
        tongThoiGianCho = tongThoiGianCho + thoiGianHienTai;

        int congViecTiepTheo = hangDoiUuTienNhoNhat.top();
        hangDoiUuTienNhoNhat.pop();

        thoiGianHienTai = thoiGianHienTai + congViecTiepTheo;
    }

    return tongThoiGianCho;
}

int main() {
    vector<int> thoiGianXuLy = {5, 3, 8, 1};
    cout << tinhTongThoiGianChoNhoNhat(thoiGianXuLy) << endl;
    return 0;
}
```
```python
import heapq


def tinh_tong_thoi_gian_cho_nho_nhat(thoi_gian_xu_ly):
    hang_doi_uu_tien_nho_nhat = []

    for thoi_gian in thoi_gian_xu_ly:
        heapq.heappush(hang_doi_uu_tien_nho_nhat, thoi_gian)

    thoi_gian_hien_tai = 0
    tong_thoi_gian_cho = 0

    while len(hang_doi_uu_tien_nho_nhat) > 0:
        tong_thoi_gian_cho = tong_thoi_gian_cho + thoi_gian_hien_tai

        cong_viec_tiep_theo = heapq.heappop(hang_doi_uu_tien_nho_nhat)

        thoi_gian_hien_tai = thoi_gian_hien_tai + cong_viec_tiep_theo

    return tong_thoi_gian_cho


thoi_gian_xu_ly = [5, 3, 8, 1]
print(tinh_tong_thoi_gian_cho_nho_nhat(thoi_gian_xu_ly))
```

---

## Bảng so sánh tổng hợp — "Nên dùng cấu trúc nào?"

| Nhu cầu sử dụng | Cấu trúc phù hợp | Lý do |
|---|---|---|
| Lưu 2 giá trị đi kèm nhau | Pair | Gộp 2 giá trị thành 1 đơn vị, tự động so sánh theo quy tắc từ điển |
| Lưu dãy có thứ tự, cần truy cập nhanh theo chỉ số | Vector | Truy cập theo chỉ số rất nhanh |
| Cần thêm/xoá thường xuyên ở cả 2 đầu | Deque | Nhanh ở cả 2 đầu, Vector chỉ nhanh ở cuối |
| Xử lý theo kiểu "vào sau ra trước" | Stack | Đúng ngữ nghĩa, code rõ ràng |
| Xử lý theo kiểu "vào trước ra trước" | Queue | Bắt buộc dùng cho thuật toán BFS |
| Chỉ cần kiểm tra tồn tại, không cần thứ tự | Unordered Set / Unordered Map | Nhanh nhất, trung bình O(1) mỗi thao tác |
| Cần duyệt có thứ tự, hoặc tìm cận trên/cận dưới | Set / Map | Chậm hơn (O(log n)) nhưng hỗ trợ thêm nhiều thao tác |
| Cần liên tục lấy ra giá trị nhỏ nhất/lớn nhất | Priority Queue | Mỗi lần thêm/lấy ra chỉ tốn O(log n) |

---

## Tổng kết Chương 3

- **Pair** giúp gộp 2 giá trị liên quan thành 1 đơn vị, đặc biệt hữu ích khi cần sắp xếp theo nhiều tiêu chí.
- **Vector** là lựa chọn mặc định cho hầu hết bài toán cần lưu một dãy phần tử — nhưng nhớ rằng thêm/xoá ở đầu hoặc giữa mảng sẽ chậm.
- **Stack, Queue, Deque** cần được chọn đúng theo thứ tự xử lý mà bài toán yêu cầu.
- **Set, Map (có thứ tự)** cho khả năng duyệt có thứ tự và tìm cận trên/cận dưới, đổi lại tốc độ chậm hơn một chút.
- **Unordered Set, Unordered Map (bảng băm)** nhanh nhất khi chỉ cần tra cứu, nhưng mất khả năng duyệt theo thứ tự.
- **Priority Queue** là công cụ không thể thay thế khi cần liên tục lấy ra giá trị nhỏ nhất hoặc lớn nhất.
- **Kỹ năng quan trọng nhất của chương này:** trước khi bắt tay viết code, hãy tự hỏi bản thân "mình sẽ cần những thao tác gì? có cần giữ thứ tự không?" rồi tra vào bảng so sánh ở trên để chọn đúng cấu trúc dữ liệu — chọn sai cấu trúc dữ liệu là nguyên nhân phổ biến nhất khiến chương trình chạy quá chậm.

---

## Bài tập cuối chương — Chương 3

### PHẦN A: ĐỀ BÀI (15 bài, không kèm lời giải)

**Bài 1 — Valid Parentheses (LeetCode 20) — Dễ**
Cho một chuỗi chỉ chứa các ký tự ngoặc tròn, ngoặc vuông, ngoặc nhọn. Hãy kiểm tra chuỗi ngoặc đó có hợp lệ hay không.

**Bài 2 — Baseball Game (LeetCode 682) — Dễ**
Cho một danh sách các thao tác ghi điểm của một trận đấu bóng chày, mỗi thao tác có thể là một số nguyên (ghi điểm mới), chữ "C" (huỷ bỏ điểm ghi gần nhất), chữ "D" (ghi điểm gấp đôi điểm gần nhất), hoặc dấu "+" (ghi điểm bằng tổng của 2 điểm gần nhất). Hãy tính tổng điểm cuối cùng.

**Bài 3 — Implement Queue using Stacks (LeetCode 232) — Dễ-Trung bình**
Hãy cài đặt một hàng đợi (Queue) chỉ bằng cách sử dụng 2 ngăn xếp (Stack).

**Bài 4 — Min Stack (LeetCode 155) — Trung bình**
Thiết kế một ngăn xếp có thêm khả năng lấy được giá trị nhỏ nhất hiện có trong ngăn xếp, với yêu cầu mọi thao tác đều phải chạy nhanh với độ phức tạp O(1).

**Bài 5 — Design Circular Deque (LeetCode 641) — Trung bình**
Thiết kế một deque vòng có kích thước cố định k, hỗ trợ chèn và xoá ở cả 2 đầu, kiểm tra đầy hoặc rỗng.

**Bài 6 — Intersection of Two Arrays (LeetCode 349) — Dễ**
Cho 2 mảng số nguyên, trả về các phần tử xuất hiện trong cả 2 mảng.

**Bài 7 — Contains Duplicate II (LeetCode 219) — Trung bình**
Cho mảng số nguyên và số k, kiểm tra có tồn tại 2 chỉ số i, j sao cho giá trị tại 2 vị trí đó bằng nhau và khoảng cách giữa 2 chỉ số không vượt quá k.

**Bài 8 — Group Anagrams (LeetCode 49) — Trung bình**
Cho danh sách các chuỗi, hãy gom nhóm các chuỗi là biến thể đảo chữ cái của nhau (chứa cùng tập ký tự nhưng khác thứ tự) vào cùng một nhóm.

**Bài 9 — Kth Largest Element in an Array (LeetCode 215) — Trung bình**
Cho mảng số nguyên chưa sắp xếp, hãy tìm phần tử lớn thứ k trong mảng.

**Bài 10 — Top K Frequent Elements (LeetCode 347) — Trung bình-Khó**
Cho mảng số nguyên, hãy tìm k phần tử xuất hiện nhiều lần nhất.

**Bài 11 — Josephus Problem I (CSES 2162) — Trung bình-Khó**
Có n người đứng thành một vòng tròn, đánh số từ 1 đến n. Bắt đầu đếm từ người số 1, đếm tới người thứ 2 thì loại người đó ra khỏi vòng tròn, tiếp tục đếm 2 người kế tiếp trong số người còn lại và loại người thứ 2. Lặp lại cho tới khi chỉ còn 1 người. Hãy in ra thứ tự những người bị loại.

**Bài 12 — Concert Tickets (CSES 1091) — Khó**
Có h loại vé với các mức giá cho trước, và n khách hàng lần lượt đến mua vé, mỗi khách hàng sẵn sàng trả tối đa một số tiền nhất định. Với mỗi khách hàng, hãy tìm vé có giá cao nhất mà không vượt quá số tiền khách sẵn sàng trả (mỗi vé chỉ được bán 1 lần).

**Bài 13 — Traffic Lights (CSES 1163) — Khó**
Trên một đường thẳng có độ dài x, các đèn giao thông được thêm lần lượt vào các vị trí cho trước. Sau mỗi lần thêm một đèn, hãy in ra khoảng cách lớn nhất giữa 2 đèn liền kề nhau (hoặc giữa một đèn với đầu đường).

**Bài 14 — Sliding Window Median (LeetCode 480) — Khó**
Cho một mảng và kích thước cửa sổ k, hãy tìm trung vị (median) của mọi cửa sổ trượt có kích thước k trong mảng đó.

**Bài 15 — Design Twitter (LeetCode 355) — Khó**
Thiết kế một hệ thống mạng xã hội đơn giản kiểu Twitter: cho phép đăng bài (kèm theo mốc thời gian), theo dõi/bỏ theo dõi người dùng khác, và lấy ra 10 bài đăng gần nhất từ những người mình đang theo dõi (kể cả bài đăng của chính mình) theo thứ tự thời gian giảm dần.

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Valid Parentheses</summary>

Giống hệt bài giải mẫu ở mục 3.3, hàm `kiemTraNgoacHopLe`/`kiem_tra_ngoac_hop_le`.
</details>

<details>
<summary>Lời giải Bài 2 — Baseball Game</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

int tinhTongDiem(vector<string> danhSachThaoTac) {
    vector<int> ngăn xếp; // biến này không hợp lệ trong C++, sửa lại dưới đây
    return 0;
}
```

Xin lỗi, xin trình bày lại phần code cho đúng cú pháp:

```cpp
#include <bits/stdc++.h>
using namespace std;

int tinhTongDiem(vector<string> danhSachThaoTac) {
    vector<int> danhSachDiem;

    for (int i = 0; i < (int)danhSachThaoTac.size(); i++) {
        string thaoTac = danhSachThaoTac[i];

        if (thaoTac == "C") {
            danhSachDiem.pop_back();
        } else if (thaoTac == "D") {
            int diemGanNhat = danhSachDiem.back();
            danhSachDiem.push_back(diemGanNhat * 2);
        } else if (thaoTac == "+") {
            int soLuong = (int)danhSachDiem.size();
            int diemThuNhat = danhSachDiem[soLuong - 1];
            int diemThuHai = danhSachDiem[soLuong - 2];
            danhSachDiem.push_back(diemThuNhat + diemThuHai);
        } else {
            int diemMoi = stoi(thaoTac);
            danhSachDiem.push_back(diemMoi);
        }
    }

    int tongDiem = 0;
    for (int i = 0; i < (int)danhSachDiem.size(); i++) {
        tongDiem = tongDiem + danhSachDiem[i];
    }

    return tongDiem;
}

int main() {
    vector<string> danhSachThaoTac = {"5", "2", "C", "D", "+"};
    cout << tinhTongDiem(danhSachThaoTac) << endl;
    return 0;
}
```
```python
def tinh_tong_diem(danh_sach_thao_tac):
    danh_sach_diem = []

    for thao_tac in danh_sach_thao_tac:
        if thao_tac == "C":
            danh_sach_diem.pop()
        elif thao_tac == "D":
            diem_gan_nhat = danh_sach_diem[-1]
            danh_sach_diem.append(diem_gan_nhat * 2)
        elif thao_tac == "+":
            diem_thu_nhat = danh_sach_diem[-1]
            diem_thu_hai = danh_sach_diem[-2]
            danh_sach_diem.append(diem_thu_nhat + diem_thu_hai)
        else:
            diem_moi = int(thao_tac)
            danh_sach_diem.append(diem_moi)

    tong_diem = 0
    for diem in danh_sach_diem:
        tong_diem = tong_diem + diem

    return tong_diem


danh_sach_thao_tac = ["5", "2", "C", "D", "+"]
print(tinh_tong_diem(danh_sach_thao_tac))
```
</details>

<details>
<summary>Lời giải Bài 3 — Implement Queue using Stacks</summary>

Ý tưởng: dùng 2 ngăn xếp, một để thêm phần tử vào (`nganXepVao`), một để lấy phần tử ra (`nganXepRa`). Khi `nganXepRa` rỗng, ta đổ toàn bộ phần tử từ `nganXepVao` sang `nganXepRa` (mỗi phần tử chỉ bị di chuyển đúng 1 lần trong suốt quá trình, nên tổng chi phí vẫn nhanh).

```cpp
#include <bits/stdc++.h>
using namespace std;

class HangDoiTuNganXep {
private:
    stack<int> nganXepVao;
    stack<int> nganXepRa;

    void chuyenNeuCanThiet() {
        if (nganXepRa.empty()) {
            while (!nganXepVao.empty()) {
                int giaTri = nganXepVao.top();
                nganXepVao.pop();
                nganXepRa.push(giaTri);
            }
        }
    }

public:
    void themVao(int giaTri) {
        nganXepVao.push(giaTri);
    }

    int layRaVaXoa() {
        chuyenNeuCanThiet();
        int giaTri = nganXepRa.top();
        nganXepRa.pop();
        return giaTri;
    }

    int xemPhanTuDau() {
        chuyenNeuCanThiet();
        return nganXepRa.top();
    }

    bool kiemTraRong() {
        return nganXepVao.empty() && nganXepRa.empty();
    }
};

int main() {
    HangDoiTuNganXep hangDoi;
    hangDoi.themVao(1);
    hangDoi.themVao(2);
    cout << hangDoi.layRaVaXoa() << endl; // in ra 1
    return 0;
}
```
```python
class HangDoiTuNganXep:
    def __init__(self):
        self.ngan_xep_vao = []
        self.ngan_xep_ra = []

    def _chuyen_neu_can_thiet(self):
        if len(self.ngan_xep_ra) == 0:
            while len(self.ngan_xep_vao) > 0:
                gia_tri = self.ngan_xep_vao.pop()
                self.ngan_xep_ra.append(gia_tri)

    def them_vao(self, gia_tri):
        self.ngan_xep_vao.append(gia_tri)

    def lay_ra_va_xoa(self):
        self._chuyen_neu_can_thiet()
        return self.ngan_xep_ra.pop()

    def xem_phan_tu_dau(self):
        self._chuyen_neu_can_thiet()
        return self.ngan_xep_ra[-1]

    def kiem_tra_rong(self):
        return len(self.ngan_xep_vao) == 0 and len(self.ngan_xep_ra) == 0


hang_doi = HangDoiTuNganXep()
hang_doi.them_vao(1)
hang_doi.them_vao(2)
print(hang_doi.lay_ra_va_xoa())  # in ra 1
```
</details>

<details>
<summary>Lời giải Bài 4 — Min Stack</summary>

Ý tưởng: dùng thêm 1 ngăn xếp phụ, lưu giá trị nhỏ nhất tính đến từng thời điểm.

```cpp
#include <bits/stdc++.h>
using namespace std;

class NganXepCoMin {
private:
    stack<int> nganXepChinh;
    stack<int> nganXepMin;

public:
    void push(int giaTri) {
        nganXepChinh.push(giaTri);

        if (nganXepMin.empty()) {
            nganXepMin.push(giaTri);
        } else {
            int minHienTai = nganXepMin.top();
            if (giaTri < minHienTai) {
                nganXepMin.push(giaTri);
            } else {
                nganXepMin.push(minHienTai);
            }
        }
    }

    void pop() {
        nganXepChinh.pop();
        nganXepMin.pop();
    }

    int top() {
        return nganXepChinh.top();
    }

    int layGiaTriNhoNhat() {
        return nganXepMin.top();
    }
};

int main() {
    NganXepCoMin ns;
    ns.push(5);
    ns.push(2);
    ns.push(8);
    cout << ns.layGiaTriNhoNhat() << endl; // in ra 2
    return 0;
}
```
```python
class NganXepCoMin:
    def __init__(self):
        self.ngan_xep_chinh = []
        self.ngan_xep_min = []

    def push(self, gia_tri):
        self.ngan_xep_chinh.append(gia_tri)

        if len(self.ngan_xep_min) == 0:
            self.ngan_xep_min.append(gia_tri)
        else:
            min_hien_tai = self.ngan_xep_min[-1]
            if gia_tri < min_hien_tai:
                self.ngan_xep_min.append(gia_tri)
            else:
                self.ngan_xep_min.append(min_hien_tai)

    def pop(self):
        self.ngan_xep_chinh.pop()
        self.ngan_xep_min.pop()

    def top(self):
        return self.ngan_xep_chinh[-1]

    def lay_gia_tri_nho_nhat(self):
        return self.ngan_xep_min[-1]


ns = NganXepCoMin()
ns.push(5)
ns.push(2)
ns.push(8)
print(ns.lay_gia_tri_nho_nhat())  # in ra 2
```
</details>

<details>
<summary>Lời giải Bài 5 — Design Circular Deque</summary>

Ý tưởng: dùng một mảng có kích thước cố định k, cùng với một biến `viTriDau` chạy vòng tròn (dùng phép chia lấy dư với k).

```cpp
#include <bits/stdc++.h>
using namespace std;

class DequeVong {
private:
    vector<int> maChua;
    int viTriDau;
    int soLuongHienTai;
    int sucChua;

public:
    DequeVong(int k) {
        maChua.assign(k, 0);
        viTriDau = 0;
        soLuongHienTai = 0;
        sucChua = k;
    }

    bool themVaoDau(int giaTri) {
        if (soLuongHienTai == sucChua) {
            return false;
        }
        viTriDau = (viTriDau - 1 + sucChua) % sucChua;
        maChua[viTriDau] = giaTri;
        soLuongHienTai = soLuongHienTai + 1;
        return true;
    }

    bool themVaoCuoi(int giaTri) {
        if (soLuongHienTai == sucChua) {
            return false;
        }
        int viTriCuoi = (viTriDau + soLuongHienTai) % sucChua;
        maChua[viTriCuoi] = giaTri;
        soLuongHienTai = soLuongHienTai + 1;
        return true;
    }

    bool xoaDau() {
        if (soLuongHienTai == 0) {
            return false;
        }
        viTriDau = (viTriDau + 1) % sucChua;
        soLuongHienTai = soLuongHienTai - 1;
        return true;
    }

    bool xoaCuoi() {
        if (soLuongHienTai == 0) {
            return false;
        }
        soLuongHienTai = soLuongHienTai - 1;
        return true;
    }
};
```
```python
class DequeVong:
    def __init__(self, k):
        self.ma_chua = [0] * k
        self.vi_tri_dau = 0
        self.so_luong_hien_tai = 0
        self.suc_chua = k

    def them_vao_dau(self, gia_tri):
        if self.so_luong_hien_tai == self.suc_chua:
            return False
        self.vi_tri_dau = (self.vi_tri_dau - 1) % self.suc_chua
        self.ma_chua[self.vi_tri_dau] = gia_tri
        self.so_luong_hien_tai = self.so_luong_hien_tai + 1
        return True

    def them_vao_cuoi(self, gia_tri):
        if self.so_luong_hien_tai == self.suc_chua:
            return False
        vi_tri_cuoi = (self.vi_tri_dau + self.so_luong_hien_tai) % self.suc_chua
        self.ma_chua[vi_tri_cuoi] = gia_tri
        self.so_luong_hien_tai = self.so_luong_hien_tai + 1
        return True

    def xoa_dau(self):
        if self.so_luong_hien_tai == 0:
            return False
        self.vi_tri_dau = (self.vi_tri_dau + 1) % self.suc_chua
        self.so_luong_hien_tai = self.so_luong_hien_tai - 1
        return True

    def xoa_cuoi(self):
        if self.so_luong_hien_tai == 0:
            return False
        self.so_luong_hien_tai = self.so_luong_hien_tai - 1
        return True
```
</details>

<details>
<summary>Lời giải Bài 6 — Intersection of Two Arrays</summary>

Giống hệt bài giải mẫu ở mục 3.8, hàm `timPhanTuChiCoOMangA`/`tim_phan_tu_chi_co_o_mang_a`, chỉ khác điều kiện: thay vì lấy phần tử **không có** trong tập B, ta lấy phần tử **có** trong tập B.

```cpp
vector<int> timGiaoCuaHaiMang(vector<int> mangA, vector<int> mangB) {
    unordered_set<int> tapHopB;
    for (int i = 0; i < (int)mangB.size(); i++) {
        tapHopB.insert(mangB[i]);
    }

    unordered_set<int> ketQuaTapHop;
    for (int i = 0; i < (int)mangA.size(); i++) {
        if (tapHopB.count(mangA[i]) > 0) {
            ketQuaTapHop.insert(mangA[i]);
        }
    }

    vector<int> ketQua;
    for (int giaTri : ketQuaTapHop) {
        ketQua.push_back(giaTri);
    }
    return ketQua;
}
```
```python
def tim_giao_cua_hai_mang(mang_a, mang_b):
    tap_hop_b = set()
    for gia_tri in mang_b:
        tap_hop_b.add(gia_tri)

    ket_qua_tap_hop = set()
    for gia_tri in mang_a:
        if gia_tri in tap_hop_b:
            ket_qua_tap_hop.add(gia_tri)

    return list(ket_qua_tap_hop)
```
</details>

<details>
<summary>Lời giải Bài 7 — Contains Duplicate II</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraTrungLapGanNhau(vector<int> a, int k) {
    unordered_map<int, int> viTriGanNhat;

    for (int i = 0; i < (int)a.size(); i++) {
        if (viTriGanNhat.count(a[i]) > 0) {
            int viTriTruoc = viTriGanNhat[a[i]];
            if (i - viTriTruoc <= k) {
                return true;
            }
        }
        viTriGanNhat[a[i]] = i;
    }

    return false;
}
```
```python
def kiem_tra_trung_lap_gan_nhau(a, k):
    vi_tri_gan_nhat = {}

    for i in range(len(a)):
        if a[i] in vi_tri_gan_nhat:
            vi_tri_truoc = vi_tri_gan_nhat[a[i]]
            if i - vi_tri_truoc <= k:
                return True
        vi_tri_gan_nhat[a[i]] = i

    return False
```
</details>

<details>
<summary>Lời giải Bài 8 — Group Anagrams</summary>

Ý tưởng: sắp xếp các ký tự trong mỗi chuỗi để tạo thành "khoá" của nhóm, rồi gom nhóm theo khoá đó.

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<string>> gomNhomAnagram(vector<string> danhSachChuoi) {
    unordered_map<string, vector<string>> cacNhom;

    for (int i = 0; i < (int)danhSachChuoi.size(); i++) {
        string chuoiHienTai = danhSachChuoi[i];
        string khoa = chuoiHienTai;
        sort(khoa.begin(), khoa.end());

        cacNhom[khoa].push_back(chuoiHienTai);
    }

    vector<vector<string>> ketQua;
    for (auto nhom : cacNhom) {
        ketQua.push_back(nhom.second);
    }

    return ketQua;
}
```
```python
def gom_nhom_anagram(danh_sach_chuoi):
    cac_nhom = {}

    for chuoi_hien_tai in danh_sach_chuoi:
        danh_sach_ky_tu = list(chuoi_hien_tai)
        danh_sach_ky_tu.sort()
        khoa = "".join(danh_sach_ky_tu)

        if khoa in cac_nhom:
            cac_nhom[khoa].append(chuoi_hien_tai)
        else:
            cac_nhom[khoa] = [chuoi_hien_tai]

    ket_qua = []
    for khoa in cac_nhom:
        ket_qua.append(cac_nhom[khoa])

    return ket_qua
```
</details>

<details>
<summary>Lời giải Bài 9 — Kth Largest Element in an Array</summary>

Ý tưởng: dùng một min-heap chỉ giữ lại tối đa k phần tử lớn nhất đã gặp — khi heap vượt quá k phần tử, loại bỏ phần tử nhỏ nhất.

```cpp
#include <bits/stdc++.h>
using namespace std;

int timPhanTuLonThuK(vector<int> a, int k) {
    priority_queue<int, vector<int>, greater<int>> minHeap;

    for (int i = 0; i < (int)a.size(); i++) {
        minHeap.push(a[i]);
        if ((int)minHeap.size() > k) {
            minHeap.pop();
        }
    }

    return minHeap.top();
}
```
```python
import heapq


def tim_phan_tu_lon_thu_k(a, k):
    min_heap = []

    for gia_tri in a:
        heapq.heappush(min_heap, gia_tri)
        if len(min_heap) > k:
            heapq.heappop(min_heap)

    return min_heap[0]
```
</details>

<details>
<summary>Lời giải Bài 10 — Top K Frequent Elements</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> timKPhanTuXuatHienNhieuNhat(vector<int> a, int k) {
    unordered_map<int, int> tanSuat;
    for (int i = 0; i < (int)a.size(); i++) {
        tanSuat[a[i]] = tanSuat[a[i]] + 1;
    }

    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<pair<int,int>>> minHeap;

    for (auto phanTu : tanSuat) {
        int giaTri = phanTu.first;
        int soLan = phanTu.second;
        minHeap.push(make_pair(soLan, giaTri));

        if ((int)minHeap.size() > k) {
            minHeap.pop();
        }
    }

    vector<int> ketQua;
    while (!minHeap.empty()) {
        ketQua.push_back(minHeap.top().second);
        minHeap.pop();
    }

    return ketQua;
}
```
```python
import heapq
from collections import Counter


def tim_k_phan_tu_xuat_hien_nhieu_nhat(a, k):
    tan_suat = Counter(a)

    min_heap = []
    for gia_tri in tan_suat:
        so_lan = tan_suat[gia_tri]
        heapq.heappush(min_heap, (so_lan, gia_tri))

        if len(min_heap) > k:
            heapq.heappop(min_heap)

    ket_qua = []
    while len(min_heap) > 0:
        cap = heapq.heappop(min_heap)
        ket_qua.append(cap[1])

    return ket_qua
```
</details>

<details>
<summary>Lời giải Bài 11 — Josephus Problem I</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> giaiBaiToanJosephus(int n) {
    queue<int> hangDoi;
    for (int i = 1; i <= n; i++) {
        hangDoi.push(i);
    }

    vector<int> nguoiBiLoai;

    while (hangDoi.size() > 1) {
        int nguoiSongSot = hangDoi.front();
        hangDoi.pop();
        hangDoi.push(nguoiSongSot); // đưa người này về cuối hàng đợi

        int nguoiBiLoaiTiepTheo = hangDoi.front();
        hangDoi.pop();
        nguoiBiLoai.push_back(nguoiBiLoaiTiepTheo);
    }

    nguoiBiLoai.push_back(hangDoi.front()); // người cuối cùng còn lại

    return nguoiBiLoai;
}
```
```python
from collections import deque


def giai_bai_toan_josephus(n):
    hang_doi = deque()
    for i in range(1, n + 1):
        hang_doi.append(i)

    nguoi_bi_loai = []

    while len(hang_doi) > 1:
        nguoi_song_sot = hang_doi.popleft()
        hang_doi.append(nguoi_song_sot)

        nguoi_bi_loai_tiep_theo = hang_doi.popleft()
        nguoi_bi_loai.append(nguoi_bi_loai_tiep_theo)

    nguoi_bi_loai.append(hang_doi[0])

    return nguoi_bi_loai
```
</details>

<details>
<summary>Lời giải Bài 12 — Concert Tickets</summary>

Ý tưởng: dùng `multiset` (cho phép giá trị trùng lặp) để lưu giá vé còn lại, mỗi truy vấn dùng `upper_bound` để tìm vé đắt nhất không vượt quá số tiền khách có.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int soLoaiVe, soKhachHang;
    cin >> soLoaiVe >> soKhachHang;

    multiset<int> danhSachVe;
    for (int i = 0; i < soLoaiVe; i++) {
        int giaVe;
        cin >> giaVe;
        danhSachVe.insert(giaVe);
    }

    for (int i = 0; i < soKhachHang; i++) {
        int soTienKhachCo;
        cin >> soTienKhachCo;

        auto conTro = danhSachVe.upper_bound(soTienKhachCo);

        if (conTro == danhSachVe.begin()) {
            cout << -1 << endl;
        } else {
            conTro--; // lùi lại 1 bước để có vé lớn nhất <= số tiền khách có
            cout << *conTro << endl;
            danhSachVe.erase(conTro);
        }
    }

    return 0;
}
```
```python
from sortedcontainers import SortedList

so_loai_ve, so_khach_hang = map(int, input().split())

danh_sach_ve = SortedList()
gia_cac_ve = list(map(int, input().split()))
for gia_ve in gia_cac_ve:
    danh_sach_ve.add(gia_ve)

for i in range(so_khach_hang):
    so_tien_khach_co = int(input())

    vi_tri = danh_sach_ve.bisect_right(so_tien_khach_co) - 1

    if vi_tri < 0:
        print(-1)
    else:
        print(danh_sach_ve[vi_tri])
        danh_sach_ve.pop(vi_tri)
```
</details>

<details>
<summary>Lời giải Bài 13 — Traffic Lights</summary>

Ý tưởng: dùng một `set` lưu vị trí các đèn đã đặt, kết hợp một `multiset` lưu độ dài các khoảng cách giữa 2 đèn liền kề. Khi thêm đèn mới, tìm 2 đèn lân cận, xoá khoảng cách cũ và thêm 2 khoảng cách mới.

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int doDaiDuongThang, soDenGiaoThong;
    cin >> doDaiDuongThang >> soDenGiaoThong;

    set<int> viTriCacDen;
    viTriCacDen.insert(0);
    viTriCacDen.insert(doDaiDuongThang);

    multiset<int> cacKhoangCach;
    cacKhoangCach.insert(doDaiDuongThang);

    for (int i = 0; i < soDenGiaoThong; i++) {
        int viTriDenMoi;
        cin >> viTriDenMoi;

        auto conTroBenPhai = viTriCacDen.lower_bound(viTriDenMoi);
        auto conTroBenTrai = conTroBenPhai;
        conTroBenTrai--;

        int viTriBenPhai = *conTroBenPhai;
        int viTriBenTrai = *conTroBenTrai;

        cacKhoangCach.erase(cacKhoangCach.find(viTriBenPhai - viTriBenTrai));
        cacKhoangCach.insert(viTriDenMoi - viTriBenTrai);
        cacKhoangCach.insert(viTriBenPhai - viTriDenMoi);

        viTriCacDen.insert(viTriDenMoi);

        int khoangCachLonNhat = *cacKhoangCach.rbegin();
        cout << khoangCachLonNhat << " ";
    }
    cout << endl;

    return 0;
}
```
> **Ghi chú:** bài này khá phức tạp, hiện tại Python không có kiểu `multiset` sẵn có gọn gàng như C++. Có thể mô phỏng bằng `SortedList` từ thư viện `sortedcontainers` kết hợp việc tự quản lý số lượng bản sao của từng khoảng cách, nhưng cài đặt sẽ dài hơn khá nhiều so với C++.
</details>

<details>
<summary>Lời giải Bài 14 — Sliding Window Median</summary>

Đây là bài toán khó, cần kết hợp `multiset` với việc duy trì một "con trỏ" luôn trỏ vào vị trí trung vị, mỗi khi cửa sổ trượt cần cập nhật lại con trỏ đó.

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<double> timTrungViCuaCuaSoTruot(vector<int> a, int k) {
    multiset<int> cuaSo(a.begin(), a.begin() + k);

    auto conTroGiua = cuaSo.begin();
    advance(conTroGiua, k / 2);

    vector<double> ketQua;

    int i = k;
    while (true) {
        if (k % 2 == 1) {
            ketQua.push_back((double)(*conTroGiua));
        } else {
            auto conTroTruoc = conTroGiua;
            conTroTruoc--;
            double trungBinh = ((double)(*conTroGiua) + (double)(*conTroTruoc)) / 2.0;
            ketQua.push_back(trungBinh);
        }

        if (i == (int)a.size()) {
            break;
        }

        int giaTriMoi = a[i];
        int giaTriCu = a[i - k];

        cuaSo.insert(giaTriMoi);
        if (giaTriMoi < *conTroGiua) {
            conTroGiua--;
        }

        if (giaTriCu <= *conTroGiua) {
            conTroGiua++;
        }
        cuaSo.erase(cuaSo.find(giaTriCu));

        i = i + 1;
    }

    return ketQua;
}
```
> **Ghi chú:** đây là bài nâng cao, độ khó vượt ngoài phạm vi cơ bản của chương này — nên giới thiệu như bài thử thách mở rộng cho học sinh khá giỏi, không bắt buộc mọi học sinh phải giải được ngay.
</details>

<details>
<summary>Lời giải Bài 15 — Design Twitter</summary>

Ý tưởng: mỗi người dùng có một danh sách bài đăng riêng (kèm mốc thời gian), và một danh sách những người mình đang theo dõi. Khi lấy tin tức, ta gộp bài đăng từ chính mình và tất cả người mình theo dõi, rồi lấy ra 10 bài mới nhất.

```cpp
#include <bits/stdc++.h>
using namespace std;

class Twitter {
private:
    int thoiGianHienTai;
    unordered_map<int, vector<pair<int,int>>> baiDangCuaMoiNguoi; // userId -> [(thoiGian, tweetId)]
    unordered_map<int, unordered_set<int>> danhSachDangTheoDoi;    // userId -> tập người đang theo dõi

public:
    Twitter() {
        thoiGianHienTai = 0;
    }

    void dangBai(int userId, int tweetId) {
        baiDangCuaMoiNguoi[userId].push_back(make_pair(thoiGianHienTai, tweetId));
        thoiGianHienTai = thoiGianHienTai + 1;
    }

    void theoDoi(int nguoiTheoDoi, int nguoiDuocTheoDoi) {
        danhSachDangTheoDoi[nguoiTheoDoi].insert(nguoiDuocTheoDoi);
    }

    void boTheoDoi(int nguoiTheoDoi, int nguoiDuocTheoDoi) {
        danhSachDangTheoDoi[nguoiTheoDoi].erase(nguoiDuocTheoDoi);
    }

    vector<int> layTinTuc(int userId) {
        vector<pair<int,int>> tatCaBaiDangLienQuan;

        for (int i = 0; i < (int)baiDangCuaMoiNguoi[userId].size(); i++) {
            tatCaBaiDangLienQuan.push_back(baiDangCuaMoiNguoi[userId][i]);
        }

        for (int nguoiDuocTheoDoi : danhSachDangTheoDoi[userId]) {
            for (int i = 0; i < (int)baiDangCuaMoiNguoi[nguoiDuocTheoDoi].size(); i++) {
                tatCaBaiDangLienQuan.push_back(baiDangCuaMoiNguoi[nguoiDuocTheoDoi][i]);
            }
        }

        // Sắp xếp theo thời gian giảm dần (bài mới nhất lên đầu)
        sort(tatCaBaiDangLienQuan.begin(), tatCaBaiDangLienQuan.end(), greater<pair<int,int>>());

        vector<int> ketQua;
        for (int i = 0; i < (int)tatCaBaiDangLienQuan.size() && i < 10; i++) {
            ketQua.push_back(tatCaBaiDangLienQuan[i].second);
        }

        return ketQua;
    }
};
```
```python
class Twitter:
    def __init__(self):
        self.thoi_gian_hien_tai = 0
        self.bai_dang_cua_moi_nguoi = {}   # userId -> danh sách (thoiGian, tweetId)
        self.danh_sach_dang_theo_doi = {}   # userId -> tập người đang theo dõi

    def dang_bai(self, user_id, tweet_id):
        if user_id not in self.bai_dang_cua_moi_nguoi:
            self.bai_dang_cua_moi_nguoi[user_id] = []
        self.bai_dang_cua_moi_nguoi[user_id].append((self.thoi_gian_hien_tai, tweet_id))
        self.thoi_gian_hien_tai = self.thoi_gian_hien_tai + 1

    def theo_doi(self, nguoi_theo_doi, nguoi_duoc_theo_doi):
        if nguoi_theo_doi not in self.danh_sach_dang_theo_doi:
            self.danh_sach_dang_theo_doi[nguoi_theo_doi] = set()
        self.danh_sach_dang_theo_doi[nguoi_theo_doi].add(nguoi_duoc_theo_doi)

    def bo_theo_doi(self, nguoi_theo_doi, nguoi_duoc_theo_doi):
        if nguoi_theo_doi in self.danh_sach_dang_theo_doi:
            self.danh_sach_dang_theo_doi[nguoi_theo_doi].discard(nguoi_duoc_theo_doi)

    def lay_tin_tuc(self, user_id):
        tat_ca_bai_dang_lien_quan = []

        if user_id in self.bai_dang_cua_moi_nguoi:
            for bai_dang in self.bai_dang_cua_moi_nguoi[user_id]:
                tat_ca_bai_dang_lien_quan.append(bai_dang)

        if user_id in self.danh_sach_dang_theo_doi:
            for nguoi_duoc_theo_doi in self.danh_sach_dang_theo_doi[user_id]:
                if nguoi_duoc_theo_doi in self.bai_dang_cua_moi_nguoi:
                    for bai_dang in self.bai_dang_cua_moi_nguoi[nguoi_duoc_theo_doi]:
                        tat_ca_bai_dang_lien_quan.append(bai_dang)

        tat_ca_bai_dang_lien_quan.sort(reverse=True)

        ket_qua = []
        so_luong_lay = min(10, len(tat_ca_bai_dang_lien_quan))
        for i in range(so_luong_lay):
            ket_qua.append(tat_ca_bai_dang_lien_quan[i][1])

        return ket_qua
```
</details>
