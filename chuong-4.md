# Chương 4: Sắp xếp & Tìm kiếm nhị phân

---

## 4.1. Sắp xếp với Comparator tuỳ chỉnh

**Vấn đề:** Hàm `sort()` mặc định chỉ sắp xếp tăng dần theo phép so sánh `<` tự nhiên. Nhưng đề bài thường yêu cầu sắp xếp theo **nhiều tiêu chí** (ví dụ: điểm giảm dần, nếu bằng điểm thì tên tăng dần theo bảng chữ cái) hoặc theo tiêu chí **không tự nhiên**.

### Cách viết Comparator
#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

bool compareHocSinh(const pair<int, string> &hocSinhA, const pair<int, string> &hocSinhB) {
    int diemA = hocSinhA.first;
    string tenA = hocSinhA.second;
    int diemB = hocSinhB.first;
    string tenB = hocSinhB.second;

    if (diemA != diemB) {
        // Điểm cao hơn thì đứng trước
        return diemA > diemB;
    }
    // Nếu điểm bằng nhau, tên nào đứng trước theo bảng chữ cái thì đứng trước
    return tenA < tenB;
}

int main() {
    vector<pair<int, string>> danhSachHocSinh;
    danhSachHocSinh.push_back(make_pair(80, "Binh"));
    danhSachHocSinh.push_back(make_pair(90, "An"));
    danhSachHocSinh.push_back(make_pair(80, "An"));

    sort(danhSachHocSinh.begin(), danhSachHocSinh.end(), compareHocSinh);

    for (int i = 0; i < (int)danhSachHocSinh.size(); i++) {
        int diem = danhSachHocSinh[i].first;
        string ten = danhSachHocSinh[i].second;
        cout << ten << " - " << diem << " diem" << endl;
    }

    return 0;
}
```
#### PYTHON
```python
def lay_khoa_sap_xep(hoc_sinh):
    # hoc_sinh là 1 tuple (diem, ten)
    diem = hoc_sinh[0]
    ten = hoc_sinh[1]
    # Trả về (-diem, ten) để điểm giảm dần (đảo dấu), tên tăng dần
    return (-diem, ten)


danh_sach_hoc_sinh = [(80, "Binh"), (90, "An"), (80, "An")]

danh_sach_hoc_sinh.sort(key=lay_khoa_sap_xep)

for hoc_sinh in danh_sach_hoc_sinh:
    diem = hoc_sinh[0]
    ten = hoc_sinh[1]
    print(ten, "-", diem, "diem")
```

> **Quy tắc bắt buộc của comparator trong C++:** hàm phải trả về `true` nếu phần tử thứ nhất **thực sự đứng trước** phần tử thứ hai. Nếu 2 phần tử bằng nhau, hàm phải trả về `false`. Viết sai quy tắc này có thể khiến chương trình chạy sai hoặc bị lỗi khi mảng lớn.

### Ví dụ 1 (Dễ) — Sắp xếp giảm dần đơn giản

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> danhSachSo;
    danhSachSo.push_back(5);
    danhSachSo.push_back(2);
    danhSachSo.push_back(8);
    danhSachSo.push_back(1);
    danhSachSo.push_back(9);

    sort(danhSachSo.begin(), danhSachSo.end(), greater<int>());

    for (int i = 0; i < (int)danhSachSo.size(); i++) {
        cout << danhSachSo[i] << " ";
    }
    cout << endl;

    return 0;
}
```
#### PYTHON
```python
danh_sach_so = [5, 2, 8, 1, 9]

danh_sach_so.sort(reverse=True)

for so in danh_sach_so:
    print(so, end=" ")
print()
```

### Ví dụ 2 (Trung bình) — Sắp xếp theo tỉ lệ giá trị/khối lượng

Bài toán: sắp xếp các vật theo tỉ lệ giá trị/khối lượng giảm dần (sẽ dùng ở Chương 25 — Bài toán Fractional Knapsack). Nếu chia trực tiếp để so sánh, có thể gặp sai số dấu phẩy động. Cách an toàn hơn là **so sánh chéo bằng phép nhân** thay vì chia.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

struct Vat {
    long long giaTri;
    long long khoiLuong;
};

bool compareTiLe(const Vat &vatA, const Vat &vatB) {
    // Ta muốn so sánh: giaTri_A / khoiLuong_A > giaTri_B / khoiLuong_B
    // Để tránh chia (tránh sai số), ta nhân chéo:
    long long benTrai = vatA.giaTri * vatB.khoiLuong;
    long long benPhai = vatB.giaTri * vatA.khoiLuong;
    return benTrai > benPhai;
}

int main() {
    vector<Vat> danhSachVat;
    danhSachVat.push_back({60, 10});
    danhSachVat.push_back({100, 20});
    danhSachVat.push_back({120, 30});

    sort(danhSachVat.begin(), danhSachVat.end(), compareTiLe);

    for (int i = 0; i < (int)danhSachVat.size(); i++) {
        cout << "Gia tri: " << danhSachVat[i].giaTri;
        cout << ", Khoi luong: " << danhSachVat[i].khoiLuong << endl;
    }

    return 0;
}
```

#### PYTHON
```python
from functools import cmp_to_key


def so_sanh_ti_le(vat_a, vat_b):
    gia_tri_a = vat_a[0]
    khoi_luong_a = vat_a[1]
    gia_tri_b = vat_b[0]
    khoi_luong_b = vat_b[1]

    # So sánh gia_tri_a / khoi_luong_a > gia_tri_b / khoi_luong_b bằng nhân chéo
    ben_trai = gia_tri_a * khoi_luong_b
    ben_phai = gia_tri_b * khoi_luong_a

    if ben_trai > ben_phai:
        return -1  # vat_a đứng trước vat_b
    elif ben_trai < ben_phai:
        return 1   # vat_b đứng trước vat_a
    else:
        return 0   # bằng nhau


danh_sach_vat = [(60, 10), (100, 20), (120, 30)]

danh_sach_vat.sort(key=cmp_to_key(so_sanh_ti_le))

for vat in danh_sach_vat:
    gia_tri = vat[0]
    khoi_luong = vat[1]
    print("Gia tri:", gia_tri, ", Khoi luong:", khoi_luong)
```

### Ví dụ 3 (Khó) — Sắp xếp để ghép chuỗi tạo số lớn nhất

Bài toán: cho danh sách các số (dạng chuỗi ký tự), ghép chúng lại theo một thứ tự nào đó để tạo thành số lớn nhất có thể được. Ví dụ: `["3", "30", "34", "5", "9"]` ghép thành `"9534330"`.

**Phân tích:** Ta không thể so sánh 2 số này bằng giá trị số học bình thường. Ví dụ `"30"` lớn hơn `"3"` về mặt số học, nhưng nếu ghép `"3"` trước `"30"` ta được `"330"`, còn ghép `"30"` trước `"3"` ta được `"303"`. Vì `"330"` lớn hơn `"303"`, nên `"3"` phải đứng trước `"30"`. Vậy quy tắc so sánh đúng là: so sánh chuỗi `a + b` với chuỗi `b + a`.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

bool compareGhepSo(const string &soA, const string &soB) {
    string ghepAB = soA + soB;
    string ghepBA = soB + soA;
    // Nếu ghép A trước B tạo ra số lớn hơn, thì A đứng trước
    return ghepAB > ghepBA;
}

string taoSoLonNhat(vector<string> danhSachSo) {
    sort(danhSachSo.begin(), danhSachSo.end(), compareGhepSo);

    string ketQua = "";
    for (int i = 0; i < (int)danhSachSo.size(); i++) {
        ketQua = ketQua + danhSachSo[i];
    }

    // Xử lý trường hợp toàn số 0, ví dụ "000" phải trả về "0"
    if (ketQua[0] == '0') {
        return "0";
    }
    return ketQua;
}

int main() {
    vector<string> danhSachSo;
    danhSachSo.push_back("3");
    danhSachSo.push_back("30");
    danhSachSo.push_back("34");
    danhSachSo.push_back("5");
    danhSachSo.push_back("9");

    cout << taoSoLonNhat(danhSachSo) << endl;

    return 0;
}
```
#### PYTHON
```python
from functools import cmp_to_key


def so_sanh_ghep_so(so_a, so_b):
    ghep_ab = so_a + so_b
    ghep_ba = so_b + so_a

    if ghep_ab > ghep_ba:
        return -1  # so_a nên đứng trước
    elif ghep_ab < ghep_ba:
        return 1   # so_b nên đứng trước
    else:
        return 0


def tao_so_lon_nhat(danh_sach_so):
    danh_sach_so_moi = list(danh_sach_so)
    danh_sach_so_moi.sort(key=cmp_to_key(so_sanh_ghep_so))

    ket_qua = ""
    for so in danh_sach_so_moi:
        ket_qua = ket_qua + so

    if ket_qua[0] == '0':
        return "0"
    return ket_qua


danh_sach_so = ["3", "30", "34", "5", "9"]
print(tao_so_lon_nhat(danh_sach_so))
```

**Bài tập minh hoạ:** Cho danh sách các cuộc họp, mỗi cuộc họp có giờ bắt đầu và giờ kết thúc. Hãy tìm số phòng họp tối thiểu cần có để tổ chức tất cả các cuộc họp, sao cho không có 2 cuộc họp nào bị trùng giờ trong cùng 1 phòng.

**Lời giải:** Ta tách mỗi cuộc họp thành 2 sự kiện: sự kiện "bắt đầu" (cộng thêm 1 phòng đang dùng) và sự kiện "kết thúc" (bớt đi 1 phòng đang dùng). Sau đó sắp xếp tất cả sự kiện theo thời gian, rồi quét qua để tìm số phòng đang dùng lớn nhất tại một thời điểm bất kỳ.

```cpp
#include <bits/stdc++.h>
using namespace std;

int timSoPhongHopToiThieu(vector<pair<int, int>> danhSachCuocHop) {
    vector<pair<int, int>> danhSachSuKien;

    for (int i = 0; i < (int)danhSachCuocHop.size(); i++) {
        int gioBatDau = danhSachCuocHop[i].first;
        int gioKetThuc = danhSachCuocHop[i].second;
        // Sự kiện bắt đầu, đánh dấu bằng số 1
        danhSachSuKien.push_back(make_pair(gioBatDau, 1));
        // Sự kiện kết thúc, đánh dấu bằng số -1
        danhSachSuKien.push_back(make_pair(gioKetThuc, -1));
    }

    // Sắp xếp theo thời gian; nếu trùng thời gian, sự kiện kết thúc (-1) đứng trước
    // vì cặp (thoiGian, -1) tự nhiên nhỏ hơn cặp (thoiGian, 1) khi so sánh pair
    sort(danhSachSuKien.begin(), danhSachSuKien.end());

    int soPhongDangDung = 0;
    int soPhongToiDa = 0;

    for (int i = 0; i < (int)danhSachSuKien.size(); i++) {
        int loaiSuKien = danhSachSuKien[i].second;
        soPhongDangDung = soPhongDangDung + loaiSuKien;
        if (soPhongDangDung > soPhongToiDa) {
            soPhongToiDa = soPhongDangDung;
        }
    }

    return soPhongToiDa;
}

int main() {
    vector<pair<int, int>> danhSachCuocHop;
    danhSachCuocHop.push_back(make_pair(0, 30));
    danhSachCuocHop.push_back(make_pair(5, 10));
    danhSachCuocHop.push_back(make_pair(15, 20));

    cout << timSoPhongHopToiThieu(danhSachCuocHop) << endl;

    return 0;
}
```
```python
def tim_so_phong_hop_toi_thieu(danh_sach_cuoc_hop):
    danh_sach_su_kien = []

    for cuoc_hop in danh_sach_cuoc_hop:
        gio_bat_dau = cuoc_hop[0]
        gio_ket_thuc = cuoc_hop[1]
        danh_sach_su_kien.append((gio_bat_dau, 1))
        danh_sach_su_kien.append((gio_ket_thuc, -1))

    # Sắp xếp theo thời gian; nếu trùng thời gian, sự kiện kết thúc (-1) đứng trước
    danh_sach_su_kien.sort()

    so_phong_dang_dung = 0
    so_phong_toi_da = 0

    for su_kien in danh_sach_su_kien:
        loai_su_kien = su_kien[1]
        so_phong_dang_dung = so_phong_dang_dung + loai_su_kien
        if so_phong_dang_dung > so_phong_toi_da:
            so_phong_toi_da = so_phong_dang_dung

    return so_phong_toi_da


danh_sach_cuoc_hop = [(0, 30), (5, 10), (15, 20)]
print(tim_so_phong_hop_toi_thieu(danh_sach_cuoc_hop))
```

---

## 4.2. Tìm kiếm nhị phân cổ điển

**Nêu bài toán:** Cho một mảng đã được sắp xếp `a` gồm n phần tử. Hãy tìm vị trí của giá trị `x` trong mảng đó (hoặc xác nhận rằng `x` không tồn tại trong mảng).

**Phân tích:** Nếu mảng chưa được sắp xếp, ta buộc phải duyệt qua từng phần tử, tốn O(n) thời gian. Nhưng vì mảng **đã có thứ tự**, tại mỗi bước ta có thể loại bỏ **một nửa** không gian tìm kiếm chỉ bằng cách so sánh với phần tử nằm ở giữa.

**Giải pháp đơn thuần:** Duyệt tuyến tính qua từng phần tử của mảng, so sánh với `x`. Cách này luôn cho kết quả đúng nhưng không tận dụng được việc mảng đã có thứ tự.

**Khó khăn với giải pháp đơn thuần:** Khi n lên tới hàng triệu, hoặc khi ta cần thực hiện rất nhiều truy vấn tìm kiếm liên tiếp, việc duyệt tuyến tính O(n) cho mỗi truy vấn sẽ quá chậm.

**Cách tiếp cận mới:** Sử dụng tìm kiếm nhị phân. Mỗi bước ta thu hẹp không gian tìm kiếm còn lại một nửa, nên tổng độ phức tạp chỉ còn O(log n).

### Cài đặt tìm kiếm nhị phân cơ bản

```cpp
#include <bits/stdc++.h>
using namespace std;

int timKiemNhiPhan(vector<int> a, int target) {
    int chiSoDau = 0;
    int chiSoCuoi = (int)a.size() - 1;

    while (chiSoDau <= chiSoCuoi) {
        int chiSoGiua = chiSoDau + (chiSoCuoi - chiSoDau) / 2;

        if (a[chiSoGiua] == target) {
            return chiSoGiua;
        } else if (a[chiSoGiua] < target) {
            // Giá trị cần tìm lớn hơn giá trị ở giữa
            // -> loại bỏ nửa bên trái, chỉ xét nửa bên phải
            chiSoDau = chiSoGiua + 1;
        } else {
            // Giá trị cần tìm nhỏ hơn giá trị ở giữa
            // -> loại bỏ nửa bên phải, chỉ xét nửa bên trái
            chiSoCuoi = chiSoGiua - 1;
        }
    }

    // Không tìm thấy
    return -1;
}

int main() {
    vector<int> a = {1, 3, 5, 7, 9, 11, 13};
    int target = 7;

    int viTri = timKiemNhiPhan(a, target);
    cout << "Vi tri cua " << target << " la: " << viTri << endl;

    return 0;
}
```
```python
def tim_kiem_nhi_phan(a, target):
    chi_so_dau = 0
    chi_so_cuoi = len(a) - 1

    while chi_so_dau <= chi_so_cuoi:
        chi_so_giua = chi_so_dau + (chi_so_cuoi - chi_so_dau) // 2

        if a[chi_so_giua] == target:
            return chi_so_giua
        elif a[chi_so_giua] < target:
            # Giá trị cần tìm lớn hơn giá trị ở giữa
            chi_so_dau = chi_so_giua + 1
        else:
            # Giá trị cần tìm nhỏ hơn giá trị ở giữa
            chi_so_cuoi = chi_so_giua - 1

    return -1


a = [1, 3, 5, 7, 9, 11, 13]
target = 7

vi_tri = tim_kiem_nhi_phan(a, target)
print("Vi tri cua", target, "la:", vi_tri)
```

> **Lưu ý về công thức tính điểm giữa:** ta viết `chiSoGiua = chiSoDau + (chiSoCuoi - chiSoDau) / 2` thay vì `chiSoGiua = (chiSoDau + chiSoCuoi) / 2`. Lý do là khi `chiSoDau` và `chiSoCuoi` là những số rất lớn, phép cộng `chiSoDau + chiSoCuoi` trong C++ có thể bị **tràn số** (vượt quá giới hạn của kiểu `int`). Cách viết đầu tiên tránh được lỗi này.

### Sử dụng công cụ có sẵn: `lower_bound` và `upper_bound` (C++), `bisect` (Python)

```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    vector<int> a = {1, 3, 5, 5, 5, 7, 9};
    int target = 5;

    // lower_bound trả về vị trí ĐẦU TIÊN có giá trị >= target
    vector<int>::iterator conTroDau = lower_bound(a.begin(), a.end(), target);

    // upper_bound trả về vị trí ĐẦU TIÊN có giá trị > target
    vector<int>::iterator conTroCuoi = upper_bound(a.begin(), a.end(), target);

    int viTriDau = conTroDau - a.begin();
    int viTriCuoi = conTroCuoi - a.begin();

    cout << "Vi tri dau tien >= target: " << viTriDau << endl;
    cout << "Vi tri dau tien > target: " << viTriCuoi << endl;

    return 0;
}
```
```python
import bisect

a = [1, 3, 5, 5, 5, 7, 9]
target = 5

# bisect_left trả về vị trí ĐẦU TIÊN có giá trị >= target
vi_tri_dau = bisect.bisect_left(a, target)

# bisect_right trả về vị trí ĐẦU TIÊN có giá trị > target
vi_tri_cuoi = bisect.bisect_right(a, target)

print("Vi tri dau tien >= target:", vi_tri_dau)
print("Vi tri dau tien > target:", vi_tri_cuoi)
```

### Ví dụ 1 (Dễ) — Tìm vị trí xuất hiện đầu tiên và cuối cùng của 1 giá trị

```cpp
#include <bits/stdc++.h>
using namespace std;

pair<int, int> timViTriDauVaCuoi(vector<int> a, int target) {
    int viTriDau = lower_bound(a.begin(), a.end(), target) - a.begin();

    // Nếu target không tồn tại trong mảng
    if (viTriDau >= (int)a.size() || a[viTriDau] != target) {
        return make_pair(-1, -1);
    }

    int viTriSauCung = upper_bound(a.begin(), a.end(), target) - a.begin() - 1;

    return make_pair(viTriDau, viTriSauCung);
}

int main() {
    vector<int> a = {1, 2, 2, 2, 3, 4, 5};
    int target = 2;

    pair<int, int> ketQua = timViTriDauVaCuoi(a, target);
    cout << "Vi tri dau: " << ketQua.first << endl;
    cout << "Vi tri cuoi: " << ketQua.second << endl;

    return 0;
}
```
```python
import bisect


def tim_vi_tri_dau_va_cuoi(a, target):
    vi_tri_dau = bisect.bisect_left(a, target)

    if vi_tri_dau >= len(a) or a[vi_tri_dau] != target:
        return (-1, -1)

    vi_tri_cuoi = bisect.bisect_right(a, target) - 1

    return (vi_tri_dau, vi_tri_cuoi)


a = [1, 2, 2, 2, 3, 4, 5]
target = 2

vi_tri_dau, vi_tri_cuoi = tim_vi_tri_dau_va_cuoi(a, target)
print("Vi tri dau:", vi_tri_dau)
print("Vi tri cuoi:", vi_tri_cuoi)
```

### Ví dụ 2 (Trung bình) — Tìm kiếm trên mảng đã sắp xếp nhưng bị xoay

Bài toán: mảng đã sắp xếp bị "xoay" tại một điểm không xác định trước. Ví dụ mảng gốc `[0,1,2,4,5,6,7]` bị xoay thành `[4,5,6,7,0,1,2]`. Hãy tìm target trong mảng này với độ phức tạp O(log n).

**Phân tích:** Mảng này không còn đơn điệu toàn bộ, nhưng nếu ta xét đoạn từ `chiSoDau` đến `chiSoGiua` và đoạn từ `chiSoGiua` đến `chiSoCuoi`, luôn có **ít nhất một trong hai đoạn** vẫn giữ nguyên thứ tự tăng dần. Ta kiểm tra đoạn nào còn đơn điệu để quyết định thu hẹp về bên nào.

```cpp
#include <bits/stdc++.h>
using namespace std;

int timKiemTrenMangBiXoay(vector<int> a, int target) {
    int chiSoDau = 0;
    int chiSoCuoi = (int)a.size() - 1;

    while (chiSoDau <= chiSoCuoi) {
        int chiSoGiua = chiSoDau + (chiSoCuoi - chiSoDau) / 2;

        if (a[chiSoGiua] == target) {
            return chiSoGiua;
        }

        // Kiểm tra xem nửa bên trái (từ chiSoDau đến chiSoGiua) có đang tăng dần không
        bool nuaTraiTangDan = (a[chiSoDau] <= a[chiSoGiua]);

        if (nuaTraiTangDan == true) {
            // Nửa trái đang tăng dần, kiểm tra target có nằm trong nửa trái không
            if (a[chiSoDau] <= target && target < a[chiSoGiua]) {
                chiSoCuoi = chiSoGiua - 1;
            } else {
                chiSoDau = chiSoGiua + 1;
            }
        } else {
            // Nửa phải đang tăng dần, kiểm tra target có nằm trong nửa phải không
            if (a[chiSoGiua] < target && target <= a[chiSoCuoi]) {
                chiSoDau = chiSoGiua + 1;
            } else {
                chiSoCuoi = chiSoGiua - 1;
            }
        }
    }

    return -1;
}

int main() {
    vector<int> a = {4, 5, 6, 7, 0, 1, 2};
    int target = 0;

    cout << timKiemTrenMangBiXoay(a, target) << endl;

    return 0;
}
```
```python
def tim_kiem_tren_mang_bi_xoay(a, target):
    chi_so_dau = 0
    chi_so_cuoi = len(a) - 1

    while chi_so_dau <= chi_so_cuoi:
        chi_so_giua = chi_so_dau + (chi_so_cuoi - chi_so_dau) // 2

        if a[chi_so_giua] == target:
            return chi_so_giua

        nua_trai_tang_dan = a[chi_so_dau] <= a[chi_so_giua]

        if nua_trai_tang_dan == True:
            if a[chi_so_dau] <= target and target < a[chi_so_giua]:
                chi_so_cuoi = chi_so_giua - 1
            else:
                chi_so_dau = chi_so_giua + 1
        else:
            if a[chi_so_giua] < target and target <= a[chi_so_cuoi]:
                chi_so_dau = chi_so_giua + 1
            else:
                chi_so_cuoi = chi_so_giua - 1

    return -1


a = [4, 5, 6, 7, 0, 1, 2]
target = 0

print(tim_kiem_tren_mang_bi_xoay(a, target))
```

### Ví dụ 3 (Khó) — Tìm kiếm nhị phân trên một hàm không tường minh

Bài toán: có n giếng nước và m trạm bơm cần xây dựng, sao cho khoảng cách từ mỗi giếng nước tới trạm bơm gần nhất không vượt quá bán kính r. Hãy tìm bán kính r **nhỏ nhất có thể** để mọi giếng đều được phủ sóng.

**Phân tích:** Ở đây không có một "mảng" cụ thể để tìm kiếm. Nhưng nếu bán kính r là khả thi (mọi giếng đều được phủ), thì chắc chắn mọi bán kính lớn hơn r cũng khả thi. Đây chính là tính chất **đơn điệu**, cho phép ta áp dụng tìm kiếm nhị phân trên giá trị r, với một hàm `check(r)` để kiểm tra tính khả thi. Kỹ thuật này sẽ được trình bày chi tiết ở mục 4.3.

**Bài tập minh hoạ:** Cho một mảng số nguyên, tìm một phần tử được gọi là "đỉnh núi" — tức là phần tử đó lớn hơn cả 2 phần tử đứng liền kề bên trái và bên phải nó (coi 2 đầu mảng là âm vô cùng).

**Lời giải:** Dù mảng không sắp xếp toàn bộ, ta vẫn có thể tìm đỉnh núi bằng tìm kiếm nhị phân. Nếu `a[chiSoGiua] < a[chiSoGiua + 1]`, nghĩa là dãy số đang đi lên tại vị trí đó, nên đỉnh núi chắc chắn nằm ở bên phải của `chiSoGiua`.

```cpp
#include <bits/stdc++.h>
using namespace std;

int timDinhNui(vector<int> a) {
    int chiSoDau = 0;
    int chiSoCuoi = (int)a.size() - 1;

    while (chiSoDau < chiSoCuoi) {
        int chiSoGiua = chiSoDau + (chiSoCuoi - chiSoDau) / 2;

        if (a[chiSoGiua] < a[chiSoGiua + 1]) {
            // Dãy số đang đi lên, đỉnh núi nằm bên phải
            chiSoDau = chiSoGiua + 1;
        } else {
            // Dãy số đang đi xuống (hoặc đứng yên), đỉnh núi nằm ở chiSoGiua hoặc bên trái
            chiSoCuoi = chiSoGiua;
        }
    }

    return chiSoDau;
}

int main() {
    vector<int> a = {1, 3, 5, 8, 4, 2};

    cout << "Vi tri dinh nui: " << timDinhNui(a) << endl;

    return 0;
}
```
```python
def tim_dinh_nui(a):
    chi_so_dau = 0
    chi_so_cuoi = len(a) - 1

    while chi_so_dau < chi_so_cuoi:
        chi_so_giua = chi_so_dau + (chi_so_cuoi - chi_so_dau) // 2

        if a[chi_so_giua] < a[chi_so_giua + 1]:
            chi_so_dau = chi_so_giua + 1
        else:
            chi_so_cuoi = chi_so_giua

    return chi_so_dau


a = [1, 3, 5, 8, 4, 2]
print("Vi tri dinh nui:", tim_dinh_nui(a))
```

---

## 4.3. Tìm kiếm nhị phân trên đáp án (Binary Search on Answer)

**Nêu bài toán:** Có n cuốn sách với số trang cho trước, cần chia số sách này cho k người, mỗi người nhận một đoạn sách **liên tiếp nhau**. Hãy tìm cách chia sao cho người nhận nhiều trang nhất phải nhận **ít trang nhất có thể được** (tối thiểu hoá giá trị lớn nhất).

**Phân tích:** Đây không phải bài toán tìm kiếm trên một mảng có sẵn, mà là bài toán **tối ưu hoá**. Ta nhận thấy: nếu với một ngưỡng số trang X, ta có thể chia sách sao cho không ai phải nhận vượt quá X trang (mà vẫn dùng không quá k người), thì với **mọi ngưỡng X' lớn hơn X**, ta chắc chắn cũng chia được. Đây chính là tính chất đơn điệu cho phép áp dụng tìm kiếm nhị phân — nhưng lần này ta tìm trên **không gian giá trị đáp án**, không phải trên mảng.

**Giải pháp đơn thuần:** Thử lần lượt từng giá trị X từ nhỏ đến lớn, với mỗi X kiểm tra xem có chia được không. Cách này đúng nhưng rất chậm nếu tổng số trang lớn.

**Khó khăn với giải pháp đơn thuần:** Nếu tổng số trang lên tới hàng tỷ, việc thử từng giá trị một là không khả thi trong giới hạn thời gian cho phép.

**Cách tiếp cận mới:** Áp dụng tìm kiếm nhị phân trên giá trị đáp án X. Với mỗi giá trị X được thử, ta dùng một hàm kiểm tra `check(X)` (thường chạy trong O(n)) để xác định X có khả thi hay không, rồi thu hẹp khoảng tìm kiếm dựa vào kết quả đó.

### Dấu hiệu nhận biết bài toán dạng này
- Đề bài hỏi "giá trị nhỏ nhất/lớn nhất sao cho..." (tối thiểu hoá giá trị lớn nhất, hoặc tối đa hoá giá trị nhỏ nhất).
- Tồn tại một hàm kiểm tra `check(x)` chạy nhanh.
- Tính chất đơn điệu: nếu x là khả thi thì mọi giá trị "tốt hơn x" cũng khả thi.

### Ví dụ 1 (Dễ) — Bài toán Koko ăn chuối

Bài toán: có n đống chuối, đống thứ i có số lượng `soChuoi[i]` quả. Koko ăn với tốc độ k quả mỗi giờ (mỗi giờ chỉ được ăn từ 1 đống; nếu đống đó ít hơn k quả thì Koko ăn hết đống đó rồi nghỉ phần thời gian còn lại của giờ đó). Có h giờ để ăn hết tất cả. Hãy tìm tốc độ k **nhỏ nhất** để Koko ăn hết trong h giờ.

```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraCoTheAnHet(vector<int> soChuoi, int tocDo, int soGio) {
    long long tongSoGioCanDung = 0;

    for (int i = 0; i < (int)soChuoi.size(); i++) {
        // Số giờ cần để ăn hết đống thứ i là ceil(soChuoi[i] / tocDo)
        long long soGioChoDongNay = (soChuoi[i] + tocDo - 1) / tocDo;
        tongSoGioCanDung = tongSoGioCanDung + soGioChoDongNay;
    }

    return tongSoGioCanDung <= soGio;
}

int timTocDoAnNhoNhat(vector<int> soChuoi, int soGio) {
    int tocDoNhoNhat = 1;
    int tocDoLonNhat = 0;
    for (int i = 0; i < (int)soChuoi.size(); i++) {
        if (soChuoi[i] > tocDoLonNhat) {
            tocDoLonNhat = soChuoi[i];
        }
    }

    int ketQua = tocDoLonNhat;

    while (tocDoNhoNhat <= tocDoLonNhat) {
        int tocDoGiua = tocDoNhoNhat + (tocDoLonNhat - tocDoNhoNhat) / 2;

        if (kiemTraCoTheAnHet(soChuoi, tocDoGiua, soGio) == true) {
            // tocDoGiua khả thi, thử tìm tốc độ nhỏ hơn
            ketQua = tocDoGiua;
            tocDoLonNhat = tocDoGiua - 1;
        } else {
            // tocDoGiua không đủ nhanh, cần tăng tốc độ
            tocDoNhoNhat = tocDoGiua + 1;
        }
    }

    return ketQua;
}

int main() {
    vector<int> soChuoi = {3, 6, 7, 11};
    int soGio = 8;

    cout << timTocDoAnNhoNhat(soChuoi, soGio) << endl;

    return 0;
}
```
```python
def kiem_tra_co_the_an_het(so_chuoi, toc_do, so_gio):
    tong_so_gio_can_dung = 0

    for so_luong in so_chuoi:
        # Số giờ cần để ăn hết đống này là ceil(so_luong / toc_do)
        so_gio_cho_dong_nay = (so_luong + toc_do - 1) // toc_do
        tong_so_gio_can_dung = tong_so_gio_can_dung + so_gio_cho_dong_nay

    return tong_so_gio_can_dung <= so_gio


def tim_toc_do_an_nho_nhat(so_chuoi, so_gio):
    toc_do_nho_nhat = 1
    toc_do_lon_nhat = max(so_chuoi)
    ket_qua = toc_do_lon_nhat

    while toc_do_nho_nhat <= toc_do_lon_nhat:
        toc_do_giua = toc_do_nho_nhat + (toc_do_lon_nhat - toc_do_nho_nhat) // 2

        if kiem_tra_co_the_an_het(so_chuoi, toc_do_giua, so_gio) == True:
            ket_qua = toc_do_giua
            toc_do_lon_nhat = toc_do_giua - 1
        else:
            toc_do_nho_nhat = toc_do_giua + 1

    return ket_qua


so_chuoi = [3, 6, 7, 11]
so_gio = 8

print(tim_toc_do_an_nho_nhat(so_chuoi, so_gio))
```

### Ví dụ 2 (Trung bình) — Chia sách cho k người

```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraCoTheChiaDuoc(vector<int> soTrang, int soNguoi, long long soTrangToiDaMoiNguoi) {
    int soNguoiDaDung = 1;
    long long soTrangHienTai = 0;

    for (int i = 0; i < (int)soTrang.size(); i++) {
        // Nếu 1 cuốn sách đã vượt quá ngưỡng, không thể chia được
        if (soTrang[i] > soTrangToiDaMoiNguoi) {
            return false;
        }

        if (soTrangHienTai + soTrang[i] > soTrangToiDaMoiNguoi) {
            // Người hiện tại đã đầy, chuyển sang người tiếp theo
            soNguoiDaDung = soNguoiDaDung + 1;
            soTrangHienTai = soTrang[i];
        } else {
            soTrangHienTai = soTrangHienTai + soTrang[i];
        }
    }

    return soNguoiDaDung <= soNguoi;
}

long long timSoTrangLonNhatNhoNhat(vector<int> soTrang, int soNguoi) {
    long long canDuoi = 0;
    long long tongTatCa = 0;

    for (int i = 0; i < (int)soTrang.size(); i++) {
        if (soTrang[i] > canDuoi) {
            canDuoi = soTrang[i];
        }
        tongTatCa = tongTatCa + soTrang[i];
    }

    long long canTren = tongTatCa;
    long long ketQua = tongTatCa;

    while (canDuoi <= canTren) {
        long long giuaKhoang = canDuoi + (canTren - canDuoi) / 2;

        if (kiemTraCoTheChiaDuoc(soTrang, soNguoi, giuaKhoang) == true) {
            ketQua = giuaKhoang;
            canTren = giuaKhoang - 1;
        } else {
            canDuoi = giuaKhoang + 1;
        }
    }

    return ketQua;
}

int main() {
    vector<int> soTrang = {100, 200, 300, 400, 500};
    int soNguoi = 3;

    cout << timSoTrangLonNhatNhoNhat(soTrang, soNguoi) << endl;

    return 0;
}
```
```python
def kiem_tra_co_the_chia_duoc(so_trang, so_nguoi, so_trang_toi_da_moi_nguoi):
    so_nguoi_da_dung = 1
    so_trang_hien_tai = 0

    for trang in so_trang:
        if trang > so_trang_toi_da_moi_nguoi:
            return False

        if so_trang_hien_tai + trang > so_trang_toi_da_moi_nguoi:
            so_nguoi_da_dung = so_nguoi_da_dung + 1
            so_trang_hien_tai = trang
        else:
            so_trang_hien_tai = so_trang_hien_tai + trang

    return so_nguoi_da_dung <= so_nguoi


def tim_so_trang_lon_nhat_nho_nhat(so_trang, so_nguoi):
    can_duoi = max(so_trang)
    can_tren = sum(so_trang)
    ket_qua = can_tren

    while can_duoi <= can_tren:
        giua_khoang = can_duoi + (can_tren - can_duoi) // 2

        if kiem_tra_co_the_chia_duoc(so_trang, so_nguoi, giua_khoang) == True:
            ket_qua = giua_khoang
            can_tren = giua_khoang - 1
        else:
            can_duoi = giua_khoang + 1

    return ket_qua


so_trang = [100, 200, 300, 400, 500]
so_nguoi = 3

print(tim_so_trang_lon_nhat_nho_nhat(so_trang, so_nguoi))
```

### Ví dụ 3 (Khó) — Tìm kiếm nhị phân trên số thực

Khi đáp án là một số thực (không phải số nguyên), ta không thể dùng điều kiện dừng `canDuoi <= canTren` như trước, vì số thực không có khái niệm "phần tử liền kề" rõ ràng. Thay vào đó, ta lặp một **số lần cố định** để đạt độ chính xác mong muốn.

```cpp
#include <bits/stdc++.h>
using namespace std;

bool check(double banKinh) {
    // Hàm kiểm tra giả định — trong bài toán thực tế sẽ kiểm tra
    // xem bán kính này có đủ để phủ hết các giếng nước hay không
    return banKinh >= 5.0;
}

int main() {
    double canDuoi = 0.0;
    double canTren = 1000000000.0;

    int soLanLap = 0;
    while (soLanLap < 100) {
        double giuaKhoang = (canDuoi + canTren) / 2.0;

        if (check(giuaKhoang) == true) {
            canTren = giuaKhoang;
        } else {
            canDuoi = giuaKhoang;
        }

        soLanLap = soLanLap + 1;
    }

    cout << fixed << setprecision(6) << canDuoi << endl;

    return 0;
}
```
```python
def check(ban_kinh):
    # Hàm kiểm tra giả định
    return ban_kinh >= 5.0


can_duoi = 0.0
can_tren = 1000000000.0

so_lan_lap = 0
while so_lan_lap < 100:
    giua_khoang = (can_duoi + can_tren) / 2.0

    if check(giua_khoang) == True:
        can_tren = giua_khoang
    else:
        can_duoi = giua_khoang

    so_lan_lap = so_lan_lap + 1

print(round(can_duoi, 6))
```

**Bài tập minh hoạ:** Cho một mảng gồm n số nguyên dương và một số nguyên m, hãy chia mảng thành **đúng m đoạn con liên tiếp**, sao cho tổng lớn nhất trong các đoạn đó là **nhỏ nhất có thể được**.

**Lời giải:** Đây thực chất chính là bài toán "chia sách cho k người" ở Ví dụ 2, chỉ khác tên gọi. Cách giải hoàn toàn giống hàm `timSoTrangLonNhatNhoNhat`/`tim_so_trang_lon_nhat_nho_nhat` ở trên, chỉ cần thay mảng số trang bằng mảng số đề cho, và thay số người bằng số đoạn m.

---

## 4.4. Tìm kiếm tam phân (Ternary Search)

**Nêu bài toán:** Cho một hàm số `f(x)` có dạng "đơn mốt" (unimodal), nghĩa là hàm tăng dần rồi giảm dần (hoặc ngược lại, giảm dần rồi tăng dần), chỉ có duy nhất 1 đỉnh hoặc 1 đáy. Hãy tìm giá trị x để `f(x)` đạt giá trị lớn nhất (hoặc nhỏ nhất).

**Phân tích:** Tìm kiếm nhị phân dựa vào việc so sánh bằng hoặc khác với một giá trị mục tiêu cụ thể, nên không áp dụng trực tiếp được cho bài toán tìm cực trị của hàm số. Ta cần một kỹ thuật khác.

**Giải pháp đơn thuần:** Duyệt qua tất cả các giá trị x có thể có, tính `f(x)` với từng giá trị rồi so sánh để tìm cực trị. Cách này tốn O(n) và không dùng được khi x là số thực (có vô số giá trị).

**Cách tiếp cận mới:** Sử dụng tìm kiếm tam phân. Ở mỗi bước, ta chọn 2 điểm chia đoạn `[canDuoi, canTren]` thành 3 phần bằng nhau, gọi là `diem1` và `diem2`. So sánh `f(diem1)` với `f(diem2)` để xác định cực trị nằm ở 2/3 đoạn nào, rồi loại bỏ 1/3 đoạn còn lại.

### Ví dụ 1 (Dễ) — Tìm giá trị nhỏ nhất của hàm bậc 2

```cpp
#include <bits/stdc++.h>
using namespace std;

double tinhHamSo(double x) {
    // Hàm parabol có giá trị nhỏ nhất tại x = 3
    return (x - 3) * (x - 3) + 5;
}

double timGiaTriNhoNhat(double canDuoi, double canTren) {
    int soLanLap = 0;
    while (soLanLap < 100) {
        double diem1 = canDuoi + (canTren - canDuoi) / 3.0;
        double diem2 = canTren - (canTren - canDuoi) / 3.0;

        if (tinhHamSo(diem1) > tinhHamSo(diem2)) {
            // Giá trị nhỏ nhất không nằm bên trái diem1
            canDuoi = diem1;
        } else {
            // Giá trị nhỏ nhất không nằm bên phải diem2
            canTren = diem2;
        }

        soLanLap = soLanLap + 1;
    }

    return (canDuoi + canTren) / 2.0;
}

int main() {
    double ketQua = timGiaTriNhoNhat(-100.0, 100.0);
    cout << fixed << setprecision(6) << ketQua << endl;

    return 0;
}
```
```python
def tinh_ham_so(x):
    # Hàm parabol có giá trị nhỏ nhất tại x = 3
    return (x - 3) * (x - 3) + 5


def tim_gia_tri_nho_nhat(can_duoi, can_tren):
    so_lan_lap = 0
    while so_lan_lap < 100:
        diem1 = can_duoi + (can_tren - can_duoi) / 3.0
        diem2 = can_tren - (can_tren - can_duoi) / 3.0

        if tinh_ham_so(diem1) > tinh_ham_so(diem2):
            can_duoi = diem1
        else:
            can_tren = diem2

        so_lan_lap = so_lan_lap + 1

    return (can_duoi + can_tren) / 2.0


ket_qua = tim_gia_tri_nho_nhat(-100.0, 100.0)
print(round(ket_qua, 6))
```

### Ví dụ 2 (Trung bình) — Tìm điểm gần nhất trên đoạn thẳng

Bài toán: cho một điểm P và một đoạn thẳng AB, tìm khoảng cách ngắn nhất từ điểm P đến một điểm bất kỳ nằm trên đoạn AB.

```cpp
#include <bits/stdc++.h>
using namespace std;

double tinhKhoangCach(double thamSoT, double Ax, double Ay, double Bx, double By, double Px, double Py) {
    // thamSoT nằm trong đoạn [0, 1], dùng để xác định 1 điểm trên đoạn AB
    double x = Ax + thamSoT * (Bx - Ax);
    double y = Ay + thamSoT * (By - Ay);

    double khoangCach = sqrt((x - Px) * (x - Px) + (y - Py) * (y - Py));
    return khoangCach;
}

int main() {
    double Ax = 0.0, Ay = 0.0;
    double Bx = 10.0, By = 0.0;
    double Px = 5.0, Py = 5.0;

    double canDuoi = 0.0;
    double canTren = 1.0;

    int soLanLap = 0;
    while (soLanLap < 100) {
        double diem1 = canDuoi + (canTren - canDuoi) / 3.0;
        double diem2 = canTren - (canTren - canDuoi) / 3.0;

        double khoangCachDiem1 = tinhKhoangCach(diem1, Ax, Ay, Bx, By, Px, Py);
        double khoangCachDiem2 = tinhKhoangCach(diem2, Ax, Ay, Bx, By, Px, Py);

        if (khoangCachDiem1 > khoangCachDiem2) {
            canDuoi = diem1;
        } else {
            canTren = diem2;
        }

        soLanLap = soLanLap + 1;
    }

    double thamSoTToiUu = (canDuoi + canTren) / 2.0;
    cout << fixed << setprecision(6);
    cout << tinhKhoangCach(thamSoTToiUu, Ax, Ay, Bx, By, Px, Py) << endl;

    return 0;
}
```
```python
import math


def tinh_khoang_cach(tham_so_t, Ax, Ay, Bx, By, Px, Py):
    x = Ax + tham_so_t * (Bx - Ax)
    y = Ay + tham_so_t * (By - Ay)

    khoang_cach = math.sqrt((x - Px) * (x - Px) + (y - Py) * (y - Py))
    return khoang_cach


Ax, Ay = 0.0, 0.0
Bx, By = 10.0, 0.0
Px, Py = 5.0, 5.0

can_duoi = 0.0
can_tren = 1.0

so_lan_lap = 0
while so_lan_lap < 100:
    diem1 = can_duoi + (can_tren - can_duoi) / 3.0
    diem2 = can_tren - (can_tren - can_duoi) / 3.0

    khoang_cach_diem1 = tinh_khoang_cach(diem1, Ax, Ay, Bx, By, Px, Py)
    khoang_cach_diem2 = tinh_khoang_cach(diem2, Ax, Ay, Bx, By, Px, Py)

    if khoang_cach_diem1 > khoang_cach_diem2:
        can_duoi = diem1
    else:
        can_tren = diem2

    so_lan_lap = so_lan_lap + 1

tham_so_t_toi_uu = (can_duoi + can_tren) / 2.0
print(round(tinh_khoang_cach(tham_so_t_toi_uu, Ax, Ay, Bx, By, Px, Py), 6))
```

### Ví dụ 3 (Khó) — Khi nào không nên dùng Ternary Search

Với mảng số nguyên có dạng "núi" (tăng rồi giảm), ta có thể dùng ternary search rời rạc, nhưng cách tìm đỉnh núi bằng tìm kiếm nhị phân (xem lại Bài tập minh hoạ ở mục 4.2, hàm `timDinhNui`/`tim_dinh_nui`) thường đơn giản hơn và không gặp vấn đề về độ chính xác số thực. Đây là điều quan trọng cần nhớ: **không phải mọi bài toán "tìm cực trị" đều cần dùng ternary search**.

**Bài tập minh hoạ:** Cho hàm chi phí có dạng `chiPhi(x) = a * x * x + b * x + c`, với `a` là số dương (đồ thị là parabol lõm lên trên). Hãy tìm giá trị x nguyên để `chiPhi(x)` là nhỏ nhất.

**Lời giải:** Vì hàm chỉ có 1 điểm cực tiểu, ta áp dụng tìm kiếm tam phân trên số nguyên. Khi làm việc với số nguyên, ta dừng vòng lặp khi khoảng tìm kiếm còn lại rất nhỏ (ví dụ chỉ còn 3 giá trị), rồi duyệt trực tiếp đoạn nhỏ đó.

```cpp
#include <bits/stdc++.h>
using namespace std;

long long tinhChiPhi(long long x, long long a, long long b, long long c) {
    return a * x * x + b * x + c;
}

long long timXNguyenNhoNhat(long long canDuoi, long long canTren, long long a, long long b, long long c) {
    while (canTren - canDuoi > 2) {
        long long diem1 = canDuoi + (canTren - canDuoi) / 3;
        long long diem2 = canTren - (canTren - canDuoi) / 3;

        if (tinhChiPhi(diem1, a, b, c) < tinhChiPhi(diem2, a, b, c)) {
            canTren = diem2;
        } else {
            canDuoi = diem1;
        }
    }

    // Đoạn còn lại rất nhỏ, duyệt trực tiếp để tìm giá trị tốt nhất
    long long giaTriTotNhat = canDuoi;
    for (long long x = canDuoi; x <= canTren; x++) {
        if (tinhChiPhi(x, a, b, c) < tinhChiPhi(giaTriTotNhat, a, b, c)) {
            giaTriTotNhat = x;
        }
    }

    return giaTriTotNhat;
}

int main() {
    long long a = 2, b = -20, c = 55;
    long long ketQua = timXNguyenNhoNhat(-1000, 1000, a, b, c);
    cout << ketQua << endl;

    return 0;
}
```
```python
def tinh_chi_phi(x, a, b, c):
    return a * x * x + b * x + c


def tim_x_nguyen_nho_nhat(can_duoi, can_tren, a, b, c):
    while can_tren - can_duoi > 2:
        diem1 = can_duoi + (can_tren - can_duoi) // 3
        diem2 = can_tren - (can_tren - can_duoi) // 3

        if tinh_chi_phi(diem1, a, b, c) < tinh_chi_phi(diem2, a, b, c):
            can_tren = diem2
        else:
            can_duoi = diem1

    gia_tri_tot_nhat = can_duoi
    for x in range(can_duoi, can_tren + 1):
        if tinh_chi_phi(x, a, b, c) < tinh_chi_phi(gia_tri_tot_nhat, a, b, c):
            gia_tri_tot_nhat = x

    return gia_tri_tot_nhat


a, b, c = 2, -20, 55
ket_qua = tim_x_nguyen_nho_nhat(-1000, 1000, a, b, c)
print(ket_qua)
```

---

## Tổng kết Chương 4

- **Sắp xếp với comparator:** luôn nhớ quy tắc — với 2 phần tử bằng nhau, hàm so sánh phải trả về `false` (trong C++) hoặc `0` (trong Python).
- **Tìm kiếm nhị phân cổ điển:** áp dụng khi mảng đã có thứ tự (hoặc có thể coi là có thứ tự) — không chỉ dùng để tìm 1 giá trị cụ thể, mà còn để tìm vị trí biên, tìm đỉnh núi, tìm điểm xoay.
- **Tìm kiếm nhị phân trên đáp án (Binary Search on Answer):** kỹ thuật quan trọng nhất chương này — nhận diện qua các cụm từ như "giá trị nhỏ nhất/lớn nhất sao cho...", cần có một hàm kiểm tra `check()` chạy nhanh và có tính đơn điệu.
- **Tìm kiếm tam phân:** dùng cho hàm có dạng đơn mốt (chỉ có 1 đỉnh hoặc 1 đáy) — cần cẩn thận vì nhiều bài toán tưởng chừng cần ternary search thực ra có thể giải bằng tìm kiếm nhị phân đơn giản hơn.
- **Kỹ năng cốt lõi cần luyện:** khi thấy đề bài có dạng "tối thiểu hoá giá trị lớn nhất" hoặc "tối đa hoá giá trị nhỏ nhất", hãy nghĩ ngay đến khả năng áp dụng tìm kiếm nhị phân trên đáp án.

---

## Bài tập cuối chương — Chương 4

### PHẦN A: ĐỀ BÀI (14 bài, không kèm lời giải)

**Bài 1 — Sort Array by Parity (LeetCode 905) — Dễ**
Cho một mảng số nguyên, hãy sắp xếp lại sao cho tất cả các số chẵn đứng trước, các số lẻ đứng sau (không yêu cầu giữ nguyên thứ tự trong từng nhóm).

**Bài 2 — Merge Intervals (LeetCode 56) — Trung bình**
Cho một danh sách các khoảng thời gian, hãy gộp tất cả các khoảng bị chồng lấn nhau thành các khoảng lớn nhất có thể.

**Bài 3 — Custom Sort String (LeetCode 791) — Trung bình**
Cho một chuỗi `order` định nghĩa thứ tự ưu tiên của các ký tự, hãy sắp xếp chuỗi `s` theo đúng thứ tự ưu tiên đó (những ký tự không xuất hiện trong `order` được đặt ở cuối, giữ nguyên thứ tự tương đối giữa chúng).

**Bài 4 — Binary Search (LeetCode 704) — Dễ**
Cài đặt thuật toán tìm kiếm nhị phân cơ bản trên một mảng đã được sắp xếp, trả về chỉ số của phần tử cần tìm hoặc -1 nếu không tìm thấy.

**Bài 5 — Find First and Last Position of Element in Sorted Array (LeetCode 34) — Trung bình**
Cho một mảng đã sắp xếp có thể có phần tử trùng lặp, hãy tìm vị trí xuất hiện đầu tiên và vị trí xuất hiện cuối cùng của một giá trị target cho trước.

**Bài 6 — Search in Rotated Sorted Array (LeetCode 33) — Trung bình**
Tìm một giá trị target trong một mảng đã sắp xếp nhưng bị xoay tại một điểm không xác định trước.

**Bài 7 — Factory Machines (CSES 1620) — Trung bình**
Có n máy sản xuất, máy thứ i cần một số phút nhất định để sản xuất ra 1 sản phẩm (các máy có thể chạy song song và mỗi máy có thể sản xuất nhiều sản phẩm liên tiếp nhau). Hãy tìm thời gian tối thiểu để tổng cộng tất cả các máy sản xuất được ít nhất k sản phẩm.

**Bài 8 — Array Division (CSES 1085) — Trung bình**
Cho một mảng n số nguyên, hãy chia mảng thành k đoạn con liên tiếp, sao cho tổng lớn nhất trong các đoạn con đó là nhỏ nhất có thể được (giống với Ví dụ 2 ở mục 4.3 nhưng dùng tên biến khác).

**Bài 9 — Aggressive Cows (dạng đề phổ biến trên nhiều trang luyện tập) — Trung bình-Khó**
Cho n vị trí chuồng bò nằm trên một đường thẳng, hãy đặt c con bò vào c chuồng khác nhau sao cho khoảng cách nhỏ nhất giữa 2 con bò bất kỳ là **lớn nhất có thể được**. Đây là bài toán "tối đa hoá giá trị nhỏ nhất" — ngược lại với các bài ở mục 4.3.

**Bài 10 — Median of Two Sorted Arrays (LeetCode 4) — Khó**
Cho 2 mảng đã sắp xếp có kích thước lần lượt là m và n, hãy tìm trung vị (median) của mảng gộp cả 2 mảng lại, mà không cần thực sự gộp chúng, với yêu cầu độ phức tạp là O(log(min(m,n))).

**Bài 11 — Peak Index in a Mountain Array (LeetCode 852) — Dễ-Trung bình**
Cho một mảng có dạng "núi" (tăng dần rồi giảm dần), hãy tìm chỉ số của đỉnh núi.

**Bài 12 — Capacity To Ship Packages Within D Days (LeetCode 1011) — Trung bình**
Có n kiện hàng cần được vận chuyển trong đúng D ngày, các kiện hàng phải được chở theo đúng thứ tự cho trước, mỗi ngày chở một số kiện liên tiếp mà không được vượt quá sức chứa của tàu. Hãy tìm sức chứa nhỏ nhất của tàu để hoàn thành việc vận chuyển đúng hạn.

**Bài 13 — Tìm cực trị của hàm đơn mốt (tự thiết kế) — Trung bình-Khó**
Cho hàm số `f(x) = |x - 5| + |x - 10| + (x - 7) * (x - 7) / 100`, xét trên đoạn từ 0 đến 20. Hãy dùng tìm kiếm tam phân để tìm giá trị x (số thực, sai số cho phép là 10 mũ âm 6) sao cho f(x) đạt giá trị nhỏ nhất.

**Bài 14 — Koko Eating Bananas biến thể có thêm ràng buộc (tự thiết kế) — Khó**
Biến thể của Ví dụ 1 (mục 4.3): Koko phải ăn hết chuối trong h giờ, nhưng có thêm một ràng buộc là mỗi giờ chỉ được ăn từ 1 trong 2 đống "ưa thích" được chỉ định trước (chỉ khi cả 2 đống ưa thích đã ăn hết mới được chuyển sang ăn đống khác). Hãy tìm tốc độ ăn nhỏ nhất k để Koko vẫn hoàn thành đúng hạn. Đây là bài rèn kỹ năng viết một hàm kiểm tra `check()` phức tạp hơn cho tìm kiếm nhị phân trên đáp án.

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Sort Array by Parity</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

bool compareChanLe(int soA, int soB) {
    int soDuA = soA % 2;
    int soDuB = soB % 2;
    // Số chẵn (số dư 0) đứng trước số lẻ (số dư 1)
    return soDuA < soDuB;
}

vector<int> sapXepChanTruocLeSau(vector<int> a) {
    sort(a.begin(), a.end(), compareChanLe);
    return a;
}

int main() {
    vector<int> a = {3, 1, 2, 4};
    vector<int> ketQua = sapXepChanTruocLeSau(a);
    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << ketQua[i] << " ";
    }
    cout << endl;
    return 0;
}
```
```python
def sap_xep_chan_truoc_le_sau(a):
    def lay_khoa(so):
        return so % 2  # Số chẵn (0) đứng trước số lẻ (1)

    a_moi = list(a)
    a_moi.sort(key=lay_khoa)
    return a_moi


a = [3, 1, 2, 4]
print(sap_xep_chan_truoc_le_sau(a))
```
</details>

<details>
<summary>Lời giải Bài 2 — Merge Intervals</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<int>> gopCacKhoang(vector<vector<int>> danhSachKhoang) {
    sort(danhSachKhoang.begin(), danhSachKhoang.end());

    vector<vector<int>> ketQua;

    for (int i = 0; i < (int)danhSachKhoang.size(); i++) {
        int batDau = danhSachKhoang[i][0];
        int ketThuc = danhSachKhoang[i][1];

        if (ketQua.size() > 0 && batDau <= ketQua.back()[1]) {
            // Chồng lấn với khoảng cuối cùng đã có, mở rộng khoảng đó
            if (ketThuc > ketQua.back()[1]) {
                ketQua.back()[1] = ketThuc;
            }
        } else {
            vector<int> khoangMoi;
            khoangMoi.push_back(batDau);
            khoangMoi.push_back(ketThuc);
            ketQua.push_back(khoangMoi);
        }
    }

    return ketQua;
}

int main() {
    vector<vector<int>> danhSachKhoang = {{1,3}, {2,6}, {8,10}, {15,18}};
    vector<vector<int>> ketQua = gopCacKhoang(danhSachKhoang);

    for (int i = 0; i < (int)ketQua.size(); i++) {
        cout << "[" << ketQua[i][0] << ", " << ketQua[i][1] << "] ";
    }
    cout << endl;

    return 0;
}
```
```python
def gop_cac_khoang(danh_sach_khoang):
    danh_sach_da_sap_xep = sorted(danh_sach_khoang)

    ket_qua = []

    for khoang in danh_sach_da_sap_xep:
        bat_dau = khoang[0]
        ket_thuc = khoang[1]

        if len(ket_qua) > 0 and bat_dau <= ket_qua[-1][1]:
            if ket_thuc > ket_qua[-1][1]:
                ket_qua[-1][1] = ket_thuc
        else:
            ket_qua.append([bat_dau, ket_thuc])

    return ket_qua


danh_sach_khoang = [[1, 3], [2, 6], [8, 10], [15, 18]]
print(gop_cac_khoang(danh_sach_khoang))
```
</details>

<details>
<summary>Lời giải Bài 3 — Custom Sort String</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

string sapXepTheoThuTu(string order, string s) {
    map<char, int> thuTuUuTien;
    for (int i = 0; i < (int)order.size(); i++) {
        thuTuUuTien[order[i]] = i;
    }

    vector<pair<int, char>> danhSachCoKhoa;
    for (int i = 0; i < (int)s.size(); i++) {
        char kyTu = s[i];
        int khoa;
        if (thuTuUuTien.count(kyTu) > 0) {
            khoa = thuTuUuTien[kyTu];
        } else {
            // Ký tự không có trong order thì đẩy về cuối
            khoa = 1000 + (int)kyTu;
        }
        danhSachCoKhoa.push_back(make_pair(khoa, kyTu));
    }

    stable_sort(danhSachCoKhoa.begin(), danhSachCoKhoa.end());

    string ketQua = "";
    for (int i = 0; i < (int)danhSachCoKhoa.size(); i++) {
        ketQua = ketQua + danhSachCoKhoa[i].second;
    }

    return ketQua;
}

int main() {
    string order = "cba";
    string s = "abcd";
    cout << sapXepTheoThuTu(order, s) << endl;
    return 0;
}
```
```python
def sap_xep_theo_thu_tu(order, s):
    thu_tu_uu_tien = {}
    for i in range(len(order)):
        thu_tu_uu_tien[order[i]] = i

    def lay_khoa(ky_tu):
        if ky_tu in thu_tu_uu_tien:
            return thu_tu_uu_tien[ky_tu]
        else:
            return len(order)  # ký tự lạ đẩy về cuối

    danh_sach_ky_tu = list(s)
    danh_sach_ky_tu.sort(key=lay_khoa)

    return "".join(danh_sach_ky_tu)


order = "cba"
s = "abcd"
print(sap_xep_theo_thu_tu(order, s))
```
</details>

<details>
<summary>Lời giải Bài 4 — Binary Search</summary>

Giống hệt hàm `timKiemNhiPhan`/`tim_kiem_nhi_phan` đã trình bày ở mục 4.2.
</details>

<details>
<summary>Lời giải Bài 5 — Find First and Last Position</summary>

Giống hệt hàm `timViTriDauVaCuoi`/`tim_vi_tri_dau_va_cuoi` ở Ví dụ 1, mục 4.2.
</details>

<details>
<summary>Lời giải Bài 6 — Search in Rotated Sorted Array</summary>

Giống hệt hàm `timKiemTrenMangBiXoay`/`tim_kiem_tren_mang_bi_xoay` ở Ví dụ 2, mục 4.2.
</details>

<details>
<summary>Lời giải Bài 7 — Factory Machines</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraDuSanPham(vector<long long> thoiGianMoiMay, long long tongThoiGian, long long soSanPhamCan) {
    long long tongSanPhamLamDuoc = 0;

    for (int i = 0; i < (int)thoiGianMoiMay.size(); i++) {
        long long soSanPhamCuaMayNay = tongThoiGian / thoiGianMoiMay[i];
        tongSanPhamLamDuoc = tongSanPhamLamDuoc + soSanPhamCuaMayNay;
    }

    return tongSanPhamLamDuoc >= soSanPhamCan;
}

long long timThoiGianNhoNhat(vector<long long> thoiGianMoiMay, long long soSanPhamCan) {
    long long canDuoi = 1;
    long long canTren = (long long)2e18;
    long long ketQua = canTren;

    while (canDuoi <= canTren) {
        long long giuaKhoang = canDuoi + (canTren - canDuoi) / 2;

        if (kiemTraDuSanPham(thoiGianMoiMay, giuaKhoang, soSanPhamCan) == true) {
            ketQua = giuaKhoang;
            canTren = giuaKhoang - 1;
        } else {
            canDuoi = giuaKhoang + 1;
        }
    }

    return ketQua;
}

int main() {
    vector<long long> thoiGianMoiMay = {2, 3, 7};
    long long soSanPhamCan = 10;
    cout << timThoiGianNhoNhat(thoiGianMoiMay, soSanPhamCan) << endl;
    return 0;
}
```
```python
def kiem_tra_du_san_pham(thoi_gian_moi_may, tong_thoi_gian, so_san_pham_can):
    tong_san_pham_lam_duoc = 0

    for thoi_gian in thoi_gian_moi_may:
        so_san_pham_cua_may_nay = tong_thoi_gian // thoi_gian
        tong_san_pham_lam_duoc = tong_san_pham_lam_duoc + so_san_pham_cua_may_nay

    return tong_san_pham_lam_duoc >= so_san_pham_can


def tim_thoi_gian_nho_nhat(thoi_gian_moi_may, so_san_pham_can):
    can_duoi = 1
    can_tren = int(2e18)
    ket_qua = can_tren

    while can_duoi <= can_tren:
        giua_khoang = can_duoi + (can_tren - can_duoi) // 2

        if kiem_tra_du_san_pham(thoi_gian_moi_may, giua_khoang, so_san_pham_can) == True:
            ket_qua = giua_khoang
            can_tren = giua_khoang - 1
        else:
            can_duoi = giua_khoang + 1

    return ket_qua


thoi_gian_moi_may = [2, 3, 7]
so_san_pham_can = 10
print(tim_thoi_gian_nho_nhat(thoi_gian_moi_may, so_san_pham_can))
```
</details>

<details>
<summary>Lời giải Bài 8 — Array Division</summary>

Giống hệt hàm `timSoTrangLonNhatNhoNhat`/`tim_so_trang_lon_nhat_nho_nhat` ở Ví dụ 2, mục 4.3, chỉ thay tên biến số trang bằng mảng đề cho.
</details>

<details>
<summary>Lời giải Bài 9 — Aggressive Cows</summary>

Đây là bài toán "tối đa hoá giá trị nhỏ nhất", nên chiều thu hẹp khoảng tìm kiếm bị đảo ngược so với các bài trước: nếu khoảng cách đang thử là khả thi, ta thử một khoảng cách **lớn hơn**.

```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraDatDuocBo(vector<int> viTriChuong, int soConBo, int khoangCachToiThieu) {
    int soConBoDaDat = 1;
    int viTriConBoTruoc = viTriChuong[0];

    for (int i = 1; i < (int)viTriChuong.size(); i++) {
        if (viTriChuong[i] - viTriConBoTruoc >= khoangCachToiThieu) {
            soConBoDaDat = soConBoDaDat + 1;
            viTriConBoTruoc = viTriChuong[i];
        }
    }

    return soConBoDaDat >= soConBo;
}

int timKhoangCachLonNhat(vector<int> viTriChuong, int soConBo) {
    sort(viTriChuong.begin(), viTriChuong.end());

    int canDuoi = 0;
    int canTren = viTriChuong.back() - viTriChuong.front();
    int ketQua = 0;

    while (canDuoi <= canTren) {
        int giuaKhoang = canDuoi + (canTren - canDuoi) / 2;

        if (kiemTraDatDuocBo(viTriChuong, soConBo, giuaKhoang) == true) {
            ketQua = giuaKhoang;
            canDuoi = giuaKhoang + 1;
        } else {
            canTren = giuaKhoang - 1;
        }
    }

    return ketQua;
}

int main() {
    vector<int> viTriChuong = {1, 2, 4, 8, 9};
    int soConBo = 3;
    cout << timKhoangCachLonNhat(viTriChuong, soConBo) << endl;
    return 0;
}
```
```python
def kiem_tra_dat_duoc_bo(vi_tri_chuong, so_con_bo, khoang_cach_toi_thieu):
    so_con_bo_da_dat = 1
    vi_tri_con_bo_truoc = vi_tri_chuong[0]

    for i in range(1, len(vi_tri_chuong)):
        if vi_tri_chuong[i] - vi_tri_con_bo_truoc >= khoang_cach_toi_thieu:
            so_con_bo_da_dat = so_con_bo_da_dat + 1
            vi_tri_con_bo_truoc = vi_tri_chuong[i]

    return so_con_bo_da_dat >= so_con_bo


def tim_khoang_cach_lon_nhat(vi_tri_chuong, so_con_bo):
    vi_tri_chuong_da_sap_xep = sorted(vi_tri_chuong)

    can_duoi = 0
    can_tren = vi_tri_chuong_da_sap_xep[-1] - vi_tri_chuong_da_sap_xep[0]
    ket_qua = 0

    while can_duoi <= can_tren:
        giua_khoang = can_duoi + (can_tren - can_duoi) // 2

        if kiem_tra_dat_duoc_bo(vi_tri_chuong_da_sap_xep, so_con_bo, giua_khoang) == True:
            ket_qua = giua_khoang
            can_duoi = giua_khoang + 1
        else:
            can_tren = giua_khoang - 1

    return ket_qua


vi_tri_chuong = [1, 2, 4, 8, 9]
so_con_bo = 3
print(tim_khoang_cach_lon_nhat(vi_tri_chuong, so_con_bo))
```
</details>

<details>
<summary>Lời giải Bài 10 — Median of Two Sorted Arrays</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

double timTrungVi(vector<int> a, vector<int> b) {
    if (a.size() > b.size()) {
        // Đảm bảo a luôn là mảng ngắn hơn để tìm kiếm nhị phân nhanh hơn
        vector<int> tam = a;
        a = b;
        b = tam;
    }

    int m = (int)a.size();
    int n = (int)b.size();

    int canDuoi = 0;
    int canTren = m;

    while (canDuoi <= canTren) {
        int i = canDuoi + (canTren - canDuoi) / 2; // số phần tử lấy từ a cho nửa trái
        int j = (m + n + 1) / 2 - i;                 // số phần tử lấy từ b cho nửa trái

        int aTrai = (i == 0) ? INT_MIN : a[i - 1];
        int aPhai = (i == m) ? INT_MAX : a[i];
        int bTrai = (j == 0) ? INT_MIN : b[j - 1];
        int bPhai = (j == n) ? INT_MAX : b[j];

        if (aTrai <= bPhai && bTrai <= aPhai) {
            if ((m + n) % 2 == 0) {
                int lonNhatBenTrai = max(aTrai, bTrai);
                int nhoNhatBenPhai = min(aPhai, bPhai);
                return (lonNhatBenTrai + nhoNhatBenPhai) / 2.0;
            } else {
                return max(aTrai, bTrai);
            }
        } else if (aTrai > bPhai) {
            canTren = i - 1;
        } else {
            canDuoi = i + 1;
        }
    }

    return -1.0; // Không xảy ra nếu dữ liệu đầu vào hợp lệ
}

int main() {
    vector<int> a = {1, 3};
    vector<int> b = {2};
    cout << fixed << setprecision(5) << timTrungVi(a, b) << endl;
    return 0;
}
```
```python
def tim_trung_vi(a, b):
    if len(a) > len(b):
        a, b = b, a

    m = len(a)
    n = len(b)

    can_duoi = 0
    can_tren = m

    while can_duoi <= can_tren:
        i = can_duoi + (can_tren - can_duoi) // 2
        j = (m + n + 1) // 2 - i

        if i == 0:
            a_trai = float('-inf')
        else:
            a_trai = a[i - 1]

        if i == m:
            a_phai = float('inf')
        else:
            a_phai = a[i]

        if j == 0:
            b_trai = float('-inf')
        else:
            b_trai = b[j - 1]

        if j == n:
            b_phai = float('inf')
        else:
            b_phai = b[j]

        if a_trai <= b_phai and b_trai <= a_phai:
            if (m + n) % 2 == 0:
                lon_nhat_ben_trai = max(a_trai, b_trai)
                nho_nhat_ben_phai = min(a_phai, b_phai)
                return (lon_nhat_ben_trai + nho_nhat_ben_phai) / 2.0
            else:
                return max(a_trai, b_trai)
        elif a_trai > b_phai:
            can_tren = i - 1
        else:
            can_duoi = i + 1

    return -1.0


a = [1, 3]
b = [2]
print(round(tim_trung_vi(a, b), 5))
```
</details>

<details>
<summary>Lời giải Bài 11 — Peak Index in a Mountain Array</summary>

Giống hệt hàm `timDinhNui`/`tim_dinh_nui` ở Bài tập minh hoạ, mục 4.2.
</details>

<details>
<summary>Lời giải Bài 12 — Capacity To Ship Packages Within D Days</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraChoDuTrongDNgay(vector<int> trongLuong, int sucChua, int soNgay) {
    int soNgayCanDung = 1;
    long long trongLuongHienTai = 0;

    for (int i = 0; i < (int)trongLuong.size(); i++) {
        if (trongLuongHienTai + trongLuong[i] > sucChua) {
            soNgayCanDung = soNgayCanDung + 1;
            trongLuongHienTai = 0;
        }
        trongLuongHienTai = trongLuongHienTai + trongLuong[i];
    }

    return soNgayCanDung <= soNgay;
}

int timSucChuaNhoNhat(vector<int> trongLuong, int soNgay) {
    int canDuoi = 0;
    long long tongTatCa = 0;

    for (int i = 0; i < (int)trongLuong.size(); i++) {
        if (trongLuong[i] > canDuoi) {
            canDuoi = trongLuong[i];
        }
        tongTatCa = tongTatCa + trongLuong[i];
    }

    int canTren = (int)tongTatCa;
    int ketQua = canTren;

    while (canDuoi <= canTren) {
        int giuaKhoang = canDuoi + (canTren - canDuoi) / 2;

        if (kiemTraChoDuTrongDNgay(trongLuong, giuaKhoang, soNgay) == true) {
            ketQua = giuaKhoang;
            canTren = giuaKhoang - 1;
        } else {
            canDuoi = giuaKhoang + 1;
        }
    }

    return ketQua;
}

int main() {
    vector<int> trongLuong = {1,2,3,4,5,6,7,8,9,10};
    int soNgay = 5;
    cout << timSucChuaNhoNhat(trongLuong, soNgay) << endl;
    return 0;
}
```
```python
def kiem_tra_cho_du_trong_d_ngay(trong_luong, suc_chua, so_ngay):
    so_ngay_can_dung = 1
    trong_luong_hien_tai = 0

    for tl in trong_luong:
        if trong_luong_hien_tai + tl > suc_chua:
            so_ngay_can_dung = so_ngay_can_dung + 1
            trong_luong_hien_tai = 0
        trong_luong_hien_tai = trong_luong_hien_tai + tl

    return so_ngay_can_dung <= so_ngay


def tim_suc_chua_nho_nhat(trong_luong, so_ngay):
    can_duoi = max(trong_luong)
    can_tren = sum(trong_luong)
    ket_qua = can_tren

    while can_duoi <= can_tren:
        giua_khoang = can_duoi + (can_tren - can_duoi) // 2

        if kiem_tra_cho_du_trong_d_ngay(trong_luong, giua_khoang, so_ngay) == True:
            ket_qua = giua_khoang
            can_tren = giua_khoang - 1
        else:
            can_duoi = giua_khoang + 1

    return ket_qua


trong_luong = [1,2,3,4,5,6,7,8,9,10]
so_ngay = 5
print(tim_suc_chua_nho_nhat(trong_luong, so_ngay))
```
</details>

<details>
<summary>Lời giải Bài 13 — Ternary Search hàm đơn mốt</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

double tinhHamF(double x) {
    double phanMotA = abs(x - 5.0);
    double phanMotB = abs(x - 10.0);
    double phanMotC = (x - 7.0) * (x - 7.0) / 100.0;
    return phanMotA + phanMotB + phanMotC;
}

double timGiaTriNhoNhatCuaF(double canDuoi, double canTren) {
    int soLanLap = 0;
    while (soLanLap < 200) {
        double diem1 = canDuoi + (canTren - canDuoi) / 3.0;
        double diem2 = canTren - (canTren - canDuoi) / 3.0;

        if (tinhHamF(diem1) > tinhHamF(diem2)) {
            canDuoi = diem1;
        } else {
            canTren = diem2;
        }

        soLanLap = soLanLap + 1;
    }

    return (canDuoi + canTren) / 2.0;
}

int main() {
    double ketQua = timGiaTriNhoNhatCuaF(0.0, 20.0);
    cout << fixed << setprecision(6) << ketQua << endl;
    return 0;
}
```
```python
def tinh_ham_f(x):
    phan_mot_a = abs(x - 5.0)
    phan_mot_b = abs(x - 10.0)
    phan_mot_c = (x - 7.0) * (x - 7.0) / 100.0
    return phan_mot_a + phan_mot_b + phan_mot_c


def tim_gia_tri_nho_nhat_cua_f(can_duoi, can_tren):
    so_lan_lap = 0
    while so_lan_lap < 200:
        diem1 = can_duoi + (can_tren - can_duoi) / 3.0
        diem2 = can_tren - (can_tren - can_duoi) / 3.0

        if tinh_ham_f(diem1) > tinh_ham_f(diem2):
            can_duoi = diem1
        else:
            can_tren = diem2

        so_lan_lap = so_lan_lap + 1

    return (can_duoi + can_tren) / 2.0


ket_qua = tim_gia_tri_nho_nhat_cua_f(0.0, 20.0)
print(round(ket_qua, 6))
```
</details>

<details>
<summary>Lời giải Bài 14 — Koko Eating Bananas biến thể</summary>

```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraCoTheAnHetBienThe(vector<int> soChuoi, int viTriDongUaThich1, int viTriDongUaThich2, int tocDo, int soGio) {
    long long tongSoGio = 0;

    long long soGioDong1 = (soChuoi[viTriDongUaThich1] + tocDo - 1) / tocDo;
    long long soGioDong2 = (soChuoi[viTriDongUaThich2] + tocDo - 1) / tocDo;
    tongSoGio = tongSoGio + soGioDong1 + soGioDong2;

    for (int i = 0; i < (int)soChuoi.size(); i++) {
        if (i == viTriDongUaThich1 || i == viTriDongUaThich2) {
            continue;
        }
        long long soGioDongNay = (soChuoi[i] + tocDo - 1) / tocDo;
        tongSoGio = tongSoGio + soGioDongNay;
    }

    return tongSoGio <= soGio;
}

int timTocDoNhoNhatBienThe(vector<int> soChuoi, int viTriDongUaThich1, int viTriDongUaThich2, int soGio) {
    int canDuoi = 1;
    int canTren = 0;
    for (int i = 0; i < (int)soChuoi.size(); i++) {
        if (soChuoi[i] > canTren) {
            canTren = soChuoi[i];
        }
    }

    int ketQua = canTren;

    while (canDuoi <= canTren) {
        int giuaKhoang = canDuoi + (canTren - canDuoi) / 2;

        bool khaThi = kiemTraCoTheAnHetBienThe(soChuoi, viTriDongUaThich1, viTriDongUaThich2, giuaKhoang, soGio);

        if (khaThi == true) {
            ketQua = giuaKhoang;
            canTren = giuaKhoang - 1;
        } else {
            canDuoi = giuaKhoang + 1;
        }
    }

    return ketQua;
}

int main() {
    vector<int> soChuoi = {5, 10, 15, 20};
    int viTriUaThich1 = 0;
    int viTriUaThich2 = 2;
    int soGio = 6;

    cout << timTocDoNhoNhatBienThe(soChuoi, viTriUaThich1, viTriUaThich2, soGio) << endl;

    return 0;
}
```
```python
def kiem_tra_co_the_an_het_bien_the(so_chuoi, vi_tri_dong_ua_thich_1, vi_tri_dong_ua_thich_2, toc_do, so_gio):
    tong_so_gio = 0

    so_gio_dong_1 = (so_chuoi[vi_tri_dong_ua_thich_1] + toc_do - 1) // toc_do
    so_gio_dong_2 = (so_chuoi[vi_tri_dong_ua_thich_2] + toc_do - 1) // toc_do
    tong_so_gio = tong_so_gio + so_gio_dong_1 + so_gio_dong_2

    for i in range(len(so_chuoi)):
        if i == vi_tri_dong_ua_thich_1 or i == vi_tri_dong_ua_thich_2:
            continue
        so_gio_dong_nay = (so_chuoi[i] + toc_do - 1) // toc_do
        tong_so_gio = tong_so_gio + so_gio_dong_nay

    return tong_so_gio <= so_gio


def tim_toc_do_nho_nhat_bien_the(so_chuoi, vi_tri_dong_ua_thich_1, vi_tri_dong_ua_thich_2, so_gio):
    can_duoi = 1
    can_tren = max(so_chuoi)
    ket_qua = can_tren

    while can_duoi <= can_tren:
        giua_khoang = can_duoi + (can_tren - can_duoi) // 2

        kha_thi = kiem_tra_co_the_an_het_bien_the(
            so_chuoi, vi_tri_dong_ua_thich_1, vi_tri_dong_ua_thich_2, giua_khoang, so_gio
        )

        if kha_thi == True:
            ket_qua = giua_khoang
            can_tren = giua_khoang - 1
        else:
            can_duoi = giua_khoang + 1

    return ket_qua


so_chuoi = [5, 10, 15, 20]
vi_tri_ua_thich_1 = 0
vi_tri_ua_thich_2 = 2
so_gio = 6

print(tim_toc_do_nho_nhat_bien_the(so_chuoi, vi_tri_ua_thich_1, vi_tri_ua_thich_2, so_gio))
```
> **Ghi chú:** phần khó của bài này không nằm ở khung tìm kiếm nhị phân (đã quen thuộc từ các bài trước), mà nằm ở việc viết đúng hàm kiểm tra `check` để phản ánh chính xác ràng buộc phức tạp hơn của đề bài.
</details>
