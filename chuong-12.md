# Chương 12: Đường đi ngắn nhất

---

## 12.1. Thuật toán Dijkstra

**Nêu bài toán:** Cho một đồ thị có trọng số trên cạnh (tất cả trọng số đều **không âm**), và một đỉnh xuất phát. Hãy tìm khoảng cách ngắn nhất từ đỉnh xuất phát tới **tất cả** các đỉnh còn lại.

**Phân tích vấn đề:** BFS (Chương 11) chỉ giải quyết được trường hợp đồ thị **không trọng số** (hoặc mọi cạnh có trọng số bằng nhau) — vì BFS ngầm giả định "đi qua nhiều cạnh hơn luôn xa hơn". Khi các cạnh có trọng số khác nhau, một đường đi qua **ít cạnh hơn** có thể lại **dài hơn** một đường đi qua nhiều cạnh nhưng mỗi cạnh nhẹ hơn. Cần một thuật toán khác biết "ưu tiên" mở rộng theo khoảng cách thực sự, không phải theo số cạnh.

**Giải pháp đơn thuần:** Với mỗi đỉnh, thử tất cả các đường đi có thể có từ đỉnh xuất phát, tính tổng trọng số, lấy giá trị nhỏ nhất. Số lượng đường đi có thể tăng theo cấp số nhân với số cạnh — không khả thi.

**Khó khăn với giải pháp đơn thuần:** Đồ thị có thể có rất nhiều đường đi khác nhau giữa 2 đỉnh; liệt kê hết là bất khả thi với đồ thị lớn.

**Cách tiếp cận mới — Dijkstra:** Duy trì một mảng `khoangCach[]`, khởi tạo `khoangCach[nguon] = 0` và các đỉnh khác là "vô cực". Dùng một **hàng đợi ưu tiên (priority queue / min-heap)** luôn lấy ra đỉnh có khoảng cách tạm thời **nhỏ nhất** chưa được xử lý. Khi xử lý một đỉnh `u`, ta thử "nới lỏng" (relax) khoảng cách tới các đỉnh kề `v`: nếu `khoangCach[u] + trongSo(u,v) < khoangCach[v]`, cập nhật `khoangCach[v]` và đưa `v` vào hàng đợi. Vì luôn xử lý đỉnh gần nhất trước, khi một đỉnh được lấy ra khỏi hàng đợi lần đầu, khoảng cách của nó đã chắc chắn là tối ưu (không thể tìm được đường ngắn hơn sau này) — điều kiện này **chỉ đúng khi mọi trọng số đều không âm**.

### Minh hoạ lời giải chi tiết

Đồ thị: cạnh (0-1, trọng số 4), (0-2, trọng số 1), (2-1, trọng số 2), (1-3, trọng số 1), (2-3, trọng số 5). Tìm khoảng cách ngắn nhất từ đỉnh 0.

| Bước | Đỉnh xử lý | khoangCach[0,1,2,3] trước | Cập nhật | khoangCach sau |
|---|---|---|---|---|
| Khởi tạo | - | [0, ∞, ∞, ∞] | - | [0, ∞, ∞, ∞] |
| 1 | 0 (khoảng cách 0) | [0, ∞, ∞, ∞] | qua (0,1): 0+4=4; qua (0,2): 0+1=1 | [0, 4, 1, ∞] |
| 2 | 2 (khoảng cách 1, nhỏ nhất trong hàng đợi) | [0, 4, 1, ∞] | qua (2,1): 1+2=3 < 4 -> cập nhật; qua (2,3): 1+5=6 | [0, 3, 1, 6] |
| 3 | 1 (khoảng cách 3) | [0, 3, 1, 6] | qua (1,3): 3+1=4 < 6 -> cập nhật | [0, 3, 1, 4] |
| 4 | 3 (khoảng cách 4) | [0, 3, 1, 4] | không còn cạnh nào cần xét | [0, 3, 1, 4] |

Kết quả: khoảng cách ngắn nhất từ đỉnh 0 tới các đỉnh 0,1,2,3 lần lượt là 0, 3, 1, 4.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<long long> dijkstra(vector<vector<pair<int,int>>> danhSachKe, int n, int dinhNguon) {
    // danhSachKe[u] chứa các cặp (đỉnh kề v, trọng số cạnh)
    const long long VO_CUC = LLONG_MAX / 2;
    vector<long long> khoangCach(n, VO_CUC);
    khoangCach[dinhNguon] = 0;

    // priority_queue mặc định là max-heap, dùng greater<> để biến thành min-heap
    priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<pair<long long,int>>> hangDoiUuTien;
    hangDoiUuTien.push(make_pair(0LL, dinhNguon));

    while (!hangDoiUuTien.empty()) {
        long long khoangCachHienTai = hangDoiUuTien.top().first;
        int dinhHienTai = hangDoiUuTien.top().second;
        hangDoiUuTien.pop();

        if (khoangCachHienTai > khoangCach[dinhHienTai]) {
            continue; // thông tin cũ đã lỗi thời (bị vượt qua bởi 1 lần cập nhật tốt hơn), bỏ qua
        }

        for (int i = 0; i < (int)danhSachKe[dinhHienTai].size(); i++) {
            int dinhKe = danhSachKe[dinhHienTai][i].first;
            int trongSoCanh = danhSachKe[dinhHienTai][i].second;

            long long khoangCachMoi = khoangCach[dinhHienTai] + trongSoCanh;
            if (khoangCachMoi < khoangCach[dinhKe]) {
                khoangCach[dinhKe] = khoangCachMoi;
                hangDoiUuTien.push(make_pair(khoangCachMoi, dinhKe));
            }
        }
    }

    return khoangCach;
}

int main() {
    int n = 4;
    vector<vector<pair<int,int>>> danhSachKe(n);

    auto themCanh = [&](int u, int v, int trongSo) {
        danhSachKe[u].push_back(make_pair(v, trongSo));
        danhSachKe[v].push_back(make_pair(u, trongSo));
    };

    themCanh(0, 1, 4);
    themCanh(0, 2, 1);
    themCanh(2, 1, 2);
    themCanh(1, 3, 1);
    themCanh(2, 3, 5);

    vector<long long> ketQua = dijkstra(danhSachKe, n, 0);
    for (int i = 0; i < n; i++) {
        cout << "Dinh " << i << ": " << ketQua[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
import heapq


def dijkstra(danh_sach_ke, n, dinh_nguon):
    VO_CUC = float('inf')
    khoang_cach = [VO_CUC] * n
    khoang_cach[dinh_nguon] = 0

    hang_doi_uu_tien = []
    heapq.heappush(hang_doi_uu_tien, (0, dinh_nguon))

    while len(hang_doi_uu_tien) > 0:
        khoang_cach_hien_tai, dinh_hien_tai = heapq.heappop(hang_doi_uu_tien)

        if khoang_cach_hien_tai > khoang_cach[dinh_hien_tai]:
            continue

        for dinh_ke, trong_so_canh in danh_sach_ke[dinh_hien_tai]:
            khoang_cach_moi = khoang_cach[dinh_hien_tai] + trong_so_canh
            if khoang_cach_moi < khoang_cach[dinh_ke]:
                khoang_cach[dinh_ke] = khoang_cach_moi
                heapq.heappush(hang_doi_uu_tien, (khoang_cach_moi, dinh_ke))

    return khoang_cach


n = 4
danh_sach_ke = [[] for _ in range(n)]


def them_canh(u, v, trong_so):
    danh_sach_ke[u].append((v, trong_so))
    danh_sach_ke[v].append((u, trong_so))


them_canh(0, 1, 4)
them_canh(0, 2, 1)
them_canh(2, 1, 2)
them_canh(1, 3, 1)
them_canh(2, 3, 5)

ket_qua = dijkstra(danh_sach_ke, n, 0)
for i in range(n):
    print("Dinh", i, ":", ket_qua[i])
```

> **Lưu ý quan trọng về dòng kiểm tra `if (khoangCachHienTai > khoangCach[dinhHienTai])`:** vì cùng 1 đỉnh có thể được đưa vào hàng đợi **nhiều lần** với các giá trị khoảng cách khác nhau (mỗi lần tìm được đường tốt hơn), khi lấy ra 1 phần tử, ta cần kiểm tra xem giá trị đó có còn là giá trị **tốt nhất hiện tại** hay không — nếu không, đây là thông tin "cũ", bỏ qua để tránh xử lý lại một đỉnh nhiều lần không cần thiết.

### Ví dụ 1 (Dễ) — Dijkstra trên đồ thị có hướng, truy vết đường đi cụ thể

**Phân tích:** Ngoài việc tính khoảng cách, ta thường cần biết **đường đi cụ thể** — lưu thêm mảng `dinhTruoc[]` ghi lại "đỉnh nào dẫn tới đỉnh này với khoảng cách tốt nhất", rồi truy vết ngược từ đích về nguồn.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

pair<vector<long long>, vector<int>> dijkstraCoTruyVet(vector<vector<pair<int,int>>> danhSachKe, int n, int dinhNguon) {
    const long long VO_CUC = LLONG_MAX / 2;
    vector<long long> khoangCach(n, VO_CUC);
    vector<int> dinhTruoc(n, -1);
    khoangCach[dinhNguon] = 0;

    priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<pair<long long,int>>> hangDoi;
    hangDoi.push(make_pair(0LL, dinhNguon));

    while (!hangDoi.empty()) {
        long long d = hangDoi.top().first;
        int u = hangDoi.top().second;
        hangDoi.pop();

        if (d > khoangCach[u]) continue;

        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            int v = danhSachKe[u][i].first;
            int trongSo = danhSachKe[u][i].second;

            if (khoangCach[u] + trongSo < khoangCach[v]) {
                khoangCach[v] = khoangCach[u] + trongSo;
                dinhTruoc[v] = u;
                hangDoi.push(make_pair(khoangCach[v], v));
            }
        }
    }

    return make_pair(khoangCach, dinhTruoc);
}

vector<int> truyVetDuongDi(vector<int> dinhTruoc, int dich) {
    vector<int> duongDi;
    int dinhHienTai = dich;

    while (dinhHienTai != -1) {
        duongDi.push_back(dinhHienTai);
        dinhHienTai = dinhTruoc[dinhHienTai];
    }

    reverse(duongDi.begin(), duongDi.end());
    return duongDi;
}

int main() {
    int n = 4;
    vector<vector<pair<int,int>>> danhSachKe(n);
    danhSachKe[0].push_back({1, 4});
    danhSachKe[0].push_back({2, 1});
    danhSachKe[2].push_back({1, 2});
    danhSachKe[1].push_back({3, 1});
    danhSachKe[2].push_back({3, 5});

    auto ketQua = dijkstraCoTruyVet(danhSachKe, n, 0);
    vector<int> duongDi = truyVetDuongDi(ketQua.second, 3);

    cout << "Duong di ngan nhat toi dinh 3: ";
    for (int i = 0; i < (int)duongDi.size(); i++) {
        cout << duongDi[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
import heapq


def dijkstra_co_truy_vet(danh_sach_ke, n, dinh_nguon):
    VO_CUC = float('inf')
    khoang_cach = [VO_CUC] * n
    dinh_truoc = [-1] * n
    khoang_cach[dinh_nguon] = 0

    hang_doi = []
    heapq.heappush(hang_doi, (0, dinh_nguon))

    while len(hang_doi) > 0:
        d, u = heapq.heappop(hang_doi)

        if d > khoang_cach[u]:
            continue

        for v, trong_so in danh_sach_ke[u]:
            if khoang_cach[u] + trong_so < khoang_cach[v]:
                khoang_cach[v] = khoang_cach[u] + trong_so
                dinh_truoc[v] = u
                heapq.heappush(hang_doi, (khoang_cach[v], v))

    return khoang_cach, dinh_truoc


def truy_vet_duong_di(dinh_truoc, dich):
    duong_di = []
    dinh_hien_tai = dich

    while dinh_hien_tai != -1:
        duong_di.append(dinh_hien_tai)
        dinh_hien_tai = dinh_truoc[dinh_hien_tai]

    duong_di.reverse()
    return duong_di


n = 4
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0].append((1, 4))
danh_sach_ke[0].append((2, 1))
danh_sach_ke[2].append((1, 2))
danh_sach_ke[1].append((3, 1))
danh_sach_ke[2].append((3, 5))

khoang_cach, dinh_truoc = dijkstra_co_truy_vet(danh_sach_ke, n, 0)
duong_di = truy_vet_duong_di(dinh_truoc, 3)

print("Duong di ngan nhat toi dinh 3:", duong_di)
```

### Ví dụ 2 (Trung bình) — Dijkstra với nhiều nguồn (Multi-source Dijkstra)

Bài toán: cho đồ thị và một tập nhiều đỉnh nguồn (không chỉ 1 đỉnh), tìm khoảng cách ngắn nhất từ **nguồn gần nhất** tới mỗi đỉnh.

**Phân tích:** Giống ý tưởng Multi-source BFS (Chương 11) — đưa **tất cả** các đỉnh nguồn vào hàng đợi ưu tiên ngay từ đầu, với khoảng cách khởi tạo đều là 0.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<long long> dijkstraDaNguon(vector<vector<pair<int,int>>> danhSachKe, int n, vector<int> cacNguon) {
    const long long VO_CUC = LLONG_MAX / 2;
    vector<long long> khoangCach(n, VO_CUC);

    priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<pair<long long,int>>> hangDoi;

    for (int i = 0; i < (int)cacNguon.size(); i++) {
        khoangCach[cacNguon[i]] = 0;
        hangDoi.push(make_pair(0LL, cacNguon[i]));
    }

    while (!hangDoi.empty()) {
        long long d = hangDoi.top().first;
        int u = hangDoi.top().second;
        hangDoi.pop();

        if (d > khoangCach[u]) continue;

        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            int v = danhSachKe[u][i].first;
            int trongSo = danhSachKe[u][i].second;

            if (khoangCach[u] + trongSo < khoangCach[v]) {
                khoangCach[v] = khoangCach[u] + trongSo;
                hangDoi.push(make_pair(khoangCach[v], v));
            }
        }
    }

    return khoangCach;
}

int main() {
    int n = 5;
    vector<vector<pair<int,int>>> danhSachKe(n);
    danhSachKe[0].push_back({1,2}); danhSachKe[1].push_back({0,2});
    danhSachKe[1].push_back({2,3}); danhSachKe[2].push_back({1,3});
    danhSachKe[3].push_back({4,1}); danhSachKe[4].push_back({3,1});
    danhSachKe[2].push_back({3,4}); danhSachKe[3].push_back({2,4});

    vector<int> cacNguon = {0, 4};
    vector<long long> ketQua = dijkstraDaNguon(danhSachKe, n, cacNguon);

    for (int i = 0; i < n; i++) {
        cout << "Dinh " << i << ": " << ketQua[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
import heapq


def dijkstra_da_nguon(danh_sach_ke, n, cac_nguon):
    VO_CUC = float('inf')
    khoang_cach = [VO_CUC] * n

    hang_doi = []
    for nguon in cac_nguon:
        khoang_cach[nguon] = 0
        heapq.heappush(hang_doi, (0, nguon))

    while len(hang_doi) > 0:
        d, u = heapq.heappop(hang_doi)

        if d > khoang_cach[u]:
            continue

        for v, trong_so in danh_sach_ke[u]:
            if khoang_cach[u] + trong_so < khoang_cach[v]:
                khoang_cach[v] = khoang_cach[u] + trong_so
                heapq.heappush(hang_doi, (khoang_cach[v], v))

    return khoang_cach


n = 5
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0].append((1, 2)); danh_sach_ke[1].append((0, 2))
danh_sach_ke[1].append((2, 3)); danh_sach_ke[2].append((1, 3))
danh_sach_ke[3].append((4, 1)); danh_sach_ke[4].append((3, 1))
danh_sach_ke[2].append((3, 4)); danh_sach_ke[3].append((2, 4))

cac_nguon = [0, 4]
ket_qua = dijkstra_da_nguon(danh_sach_ke, n, cac_nguon)

for i in range(n):
    print("Dinh", i, ":", ket_qua[i])
```

### Ví dụ 3 (Khó) — Dijkstra với trạng thái mở rộng (K đường đi ngắn nhất khác nhau)

Bài toán: tìm độ dài của đường đi ngắn nhất **thứ K** (không phải thứ 1) từ đỉnh nguồn tới đỉnh đích, cho phép đường đi lặp lại đỉnh.

**Phân tích:** Đây là biến thể mở rộng trạng thái — thay vì chỉ lưu `khoangCach[dinh]` (khoảng cách ngắn nhất duy nhất), ta cho phép mỗi đỉnh được xử lý **tối đa K lần** (lưu K giá trị khoảng cách nhỏ nhất khác nhau đã tìm được đi qua đỉnh đó), dùng cùng cơ chế hàng đợi ưu tiên.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timDuongDiNganThuK(vector<vector<pair<int,int>>> danhSachKe, int n, int nguon, int dich, int K) {
    vector<int> demSoLanXuLy(n, 0);
    priority_queue<pair<long long,int>, vector<pair<long long,int>>, greater<pair<long long,int>>> hangDoi;
    hangDoi.push(make_pair(0LL, nguon));

    while (!hangDoi.empty()) {
        long long d = hangDoi.top().first;
        int u = hangDoi.top().second;
        hangDoi.pop();

        if (demSoLanXuLy[u] >= K) {
            continue; // đỉnh này đã được xử lý đủ K lần, không cần xử lý thêm
        }
        demSoLanXuLy[u] = demSoLanXuLy[u] + 1;

        if (u == dich && demSoLanXuLy[u] == K) {
            return d; // đây chính là đường đi ngắn thứ K tới đích
        }

        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            int v = danhSachKe[u][i].first;
            int trongSo = danhSachKe[u][i].second;
            hangDoi.push(make_pair(d + trongSo, v));
        }
    }

    return -1; // không tồn tại đường đi ngắn thứ K
}

int main() {
    int n = 4;
    vector<vector<pair<int,int>>> danhSachKe(n);
    danhSachKe[0].push_back({1, 1});
    danhSachKe[0].push_back({2, 2});
    danhSachKe[1].push_back({3, 3});
    danhSachKe[2].push_back({3, 1});

    int K = 2;
    cout << timDuongDiNganThuK(danhSachKe, n, 0, 3, K) << endl;

    return 0;
}
```

#### PYTHON
```python
import heapq


def tim_duong_di_ngan_thu_k(danh_sach_ke, n, nguon, dich, K):
    dem_so_lan_xu_ly = [0] * n
    hang_doi = []
    heapq.heappush(hang_doi, (0, nguon))

    while len(hang_doi) > 0:
        d, u = heapq.heappop(hang_doi)

        if dem_so_lan_xu_ly[u] >= K:
            continue
        dem_so_lan_xu_ly[u] += 1

        if u == dich and dem_so_lan_xu_ly[u] == K:
            return d

        for v, trong_so in danh_sach_ke[u]:
            heapq.heappush(hang_doi, (d + trong_so, v))

    return -1


n = 4
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0].append((1, 1))
danh_sach_ke[0].append((2, 2))
danh_sach_ke[1].append((3, 3))
danh_sach_ke[2].append((3, 1))

K = 2
print(tim_duong_di_ngan_thu_k(danh_sach_ke, n, 0, 3, K))
```

**Bài tập minh hoạ:** Cho đồ thị có trọng số không âm, và một đỉnh đích cho trước, hãy tìm đỉnh nguồn (trong số tất cả các đỉnh) sao cho khoảng cách ngắn nhất từ nguồn đó tới đích là **lớn nhất có thể** (tìm đỉnh "xa đích nhất" theo nghĩa ngắn nhất).

**Lời giải:** Đảo ngược hướng tất cả các cạnh (nếu đồ thị có hướng), chạy Dijkstra 1 lần từ đích trên đồ thị đã đảo ngược — khi đó `khoangCach[u]` chính là khoảng cách ngắn nhất từ `u` tới đích ban đầu. Tìm giá trị lớn nhất trong mảng này.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 4;
    vector<vector<pair<int,int>>> danhSachKe(n); // giả sử đồ thị vô hướng nên không cần đảo cạnh

    danhSachKe[0].push_back({1, 4}); danhSachKe[1].push_back({0, 4});
    danhSachKe[0].push_back({2, 1}); danhSachKe[2].push_back({0, 1});
    danhSachKe[2].push_back({1, 2}); danhSachKe[1].push_back({2, 2});
    danhSachKe[1].push_back({3, 1}); danhSachKe[3].push_back({1, 1});

    int dinhDich = 3;
    // dijkstra() đã định nghĩa ở Code khung mẫu
    vector<long long> khoangCach = dijkstra(danhSachKe, n, dinhDich);

    long long khoangCachLonNhat = 0;
    int dinhXaNhat = dinhDich;
    for (int i = 0; i < n; i++) {
        if (khoangCach[i] > khoangCachLonNhat) {
            khoangCachLonNhat = khoangCach[i];
            dinhXaNhat = i;
        }
    }

    cout << "Dinh xa dich nhat: " << dinhXaNhat << ", khoang cach: " << khoangCachLonNhat << endl;

    return 0;
}
```

#### PYTHON
```python
n = 4
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0].append((1, 4)); danh_sach_ke[1].append((0, 4))
danh_sach_ke[0].append((2, 1)); danh_sach_ke[2].append((0, 1))
danh_sach_ke[2].append((1, 2)); danh_sach_ke[1].append((2, 2))
danh_sach_ke[1].append((3, 1)); danh_sach_ke[3].append((1, 1))

dinh_dich = 3
khoang_cach = dijkstra(danh_sach_ke, n, dinh_dich)  # hàm dijkstra đã định nghĩa ở Code khung mẫu

khoang_cach_lon_nhat = 0
dinh_xa_nhat = dinh_dich
for i in range(n):
    if khoang_cach[i] > khoang_cach_lon_nhat:
        khoang_cach_lon_nhat = khoang_cach[i]
        dinh_xa_nhat = i

print("Dinh xa dich nhat:", dinh_xa_nhat, ", khoang cach:", khoang_cach_lon_nhat)
```

---

## 12.2. Thuật toán Bellman-Ford

**Nêu bài toán:** Cho đồ thị có hướng, trong đó **một số cạnh có thể có trọng số âm** (ví dụ đại diện cho lợi nhuận thay vì chi phí). Hãy tìm khoảng cách ngắn nhất từ đỉnh nguồn tới tất cả các đỉnh khác, hoặc phát hiện nếu tồn tại **chu trình âm** (một chu trình mà tổng trọng số các cạnh là âm — khi đó "khoảng cách ngắn nhất" không được định nghĩa, vì có thể đi vòng quanh chu trình vô hạn lần để giảm khoảng cách vô hạn).

**Phân tích vấn đề:** Dijkstra (mục 12.1) dựa vào tính chất "đỉnh được xử lý đầu tiên (khoảng cách nhỏ nhất trong hàng đợi) chắc chắn đã tối ưu" — tính chất này **bị phá vỡ** khi có trọng số âm, vì một con đường "vòng qua" 1 cạnh âm có thể làm giảm khoảng cách sau khi đỉnh đã được "chốt" là tối ưu.

**Giải pháp đơn thuần:** Nếu bỏ qua vấn đề trọng số âm và dùng Dijkstra như bình thường, thuật toán sẽ cho kết quả **sai** trong nhiều trường hợp — đây không phải một lựa chọn hợp lệ.

**Cách tiếp cận mới — Bellman-Ford:** Lặp lại quá trình "nới lỏng" (relax) **tất cả** các cạnh, đúng `n-1` lần (với n là số đỉnh). Về lý thuyết, đường đi ngắn nhất giữa 2 đỉnh bất kỳ trong đồ thị không có chu trình âm chỉ cần đi qua tối đa `n-1` cạnh — nên sau `n-1` lần lặp toàn bộ các cạnh, khoảng cách chắc chắn đã hội tụ về giá trị tối ưu. Nếu sau `n-1` lần lặp mà vẫn còn cạnh có thể "nới lỏng" thêm được, điều đó chứng tỏ tồn tại **chu trình âm**.

### Minh hoạ lời giải chi tiết

Đồ thị có hướng: (0→1, trọng số 4), (0→2, trọng số 5), (1→2, trọng số -3), (2→3, trọng số 4). Tìm khoảng cách ngắn nhất từ đỉnh 0, với n = 4 đỉnh (cần 3 lần lặp).

| Lần lặp | Xét cạnh 0→1 | Xét cạnh 0→2 | Xét cạnh 1→2 | Xét cạnh 2→3 |
|---|---|---|---|---|
| Khởi tạo | khoangCach = [0, ∞, ∞, ∞] | | | |
| Lần 1 | 0+4=4 < ∞ -> KC[1]=4 | 0+5=5 < ∞ -> KC[2]=5 | 4+(-3)=1 < 5 -> KC[2]=1 | 1+4=5 < ∞ -> KC[3]=5 |
| Lần 2 | không đổi | không đổi | không đổi | không đổi |
| Lần 3 | không đổi | không đổi | không đổi | không đổi |

Kết quả: `khoangCach = [0, 4, 1, 5]`. Vì cạnh 1→2 có trọng số âm (-3), khoảng cách tới đỉnh 2 giảm từ 5 (đi trực tiếp 0→2) xuống 1 (đi 0→1→2), minh chứng cho việc Dijkstra (xử lý đỉnh gần nhất trước) có thể "chốt" sai nếu áp dụng nhầm trên đồ thị có trọng số âm.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

pair<vector<long long>, bool> bellmanFord(vector<tuple<int,int,int>> danhSachCanh, int n, int dinhNguon) {
    // danhSachCanh: mỗi phần tử là (u, v, trongSo) - cạnh có hướng từ u tới v
    const long long VO_CUC = LLONG_MAX / 2;
    vector<long long> khoangCach(n, VO_CUC);
    khoangCach[dinhNguon] = 0;

    for (int lanLap = 0; lanLap < n - 1; lanLap++) {
        for (int i = 0; i < (int)danhSachCanh.size(); i++) {
            int u = get<0>(danhSachCanh[i]);
            int v = get<1>(danhSachCanh[i]);
            int trongSo = get<2>(danhSachCanh[i]);

            if (khoangCach[u] != VO_CUC && khoangCach[u] + trongSo < khoangCach[v]) {
                khoangCach[v] = khoangCach[u] + trongSo;
            }
        }
    }

    // Lần lặp thứ n để kiểm tra chu trình âm
    bool coChuTrinhAm = false;
    for (int i = 0; i < (int)danhSachCanh.size(); i++) {
        int u = get<0>(danhSachCanh[i]);
        int v = get<1>(danhSachCanh[i]);
        int trongSo = get<2>(danhSachCanh[i]);

        if (khoangCach[u] != VO_CUC && khoangCach[u] + trongSo < khoangCach[v]) {
            coChuTrinhAm = true;
            break;
        }
    }

    return make_pair(khoangCach, coChuTrinhAm);
}

int main() {
    int n = 4;
    vector<tuple<int,int,int>> danhSachCanh = {
        {0, 1, 4}, {0, 2, 5}, {1, 2, -3}, {2, 3, 4}
    };

    auto ketQua = bellmanFord(danhSachCanh, n, 0);
    vector<long long> khoangCach = ketQua.first;
    bool coChuTrinhAm = ketQua.second;

    if (coChuTrinhAm == true) {
        cout << "Do thi co chu trinh am" << endl;
    } else {
        for (int i = 0; i < n; i++) {
            cout << "Dinh " << i << ": " << khoangCach[i] << endl;
        }
    }

    return 0;
}
```

#### PYTHON
```python
def bellman_ford(danh_sach_canh, n, dinh_nguon):
    VO_CUC = float('inf')
    khoang_cach = [VO_CUC] * n
    khoang_cach[dinh_nguon] = 0

    for lan_lap in range(n - 1):
        for u, v, trong_so in danh_sach_canh:
            if khoang_cach[u] != VO_CUC and khoang_cach[u] + trong_so < khoang_cach[v]:
                khoang_cach[v] = khoang_cach[u] + trong_so

    co_chu_trinh_am = False
    for u, v, trong_so in danh_sach_canh:
        if khoang_cach[u] != VO_CUC and khoang_cach[u] + trong_so < khoang_cach[v]:
            co_chu_trinh_am = True
            break

    return khoang_cach, co_chu_trinh_am


n = 4
danh_sach_canh = [(0, 1, 4), (0, 2, 5), (1, 2, -3), (2, 3, 4)]

khoang_cach, co_chu_trinh_am = bellman_ford(danh_sach_canh, n, 0)

if co_chu_trinh_am == True:
    print("Do thi co chu trinh am")
else:
    for i in range(n):
        print("Dinh", i, ":", khoang_cach[i])
```

### Ví dụ 1 (Dễ) — So sánh thời gian chạy Dijkstra và Bellman-Ford

**Phân tích (không code, chỉ phân tích):**
- Dijkstra: độ phức tạp O((n+m) log n) nhờ dùng priority queue, nhưng **chỉ đúng khi không có trọng số âm**.
- Bellman-Ford: độ phức tạp O(n × m) — chậm hơn Dijkstra đáng kể, nhưng xử lý được trọng số âm và có thể phát hiện chu trình âm.
- Nguyên tắc chọn: nếu đề bài đảm bảo trọng số không âm, **luôn ưu tiên Dijkstra** vì nhanh hơn. Chỉ dùng Bellman-Ford khi có khả năng trọng số âm hoặc cần phát hiện chu trình âm.

### Ví dụ 2 (Trung bình) — Tìm và in ra một chu trình âm cụ thể

Bài toán: không chỉ phát hiện có chu trình âm hay không, mà còn phải **in ra** các đỉnh tạo thành chu trình âm đó.

**Phân tích:** Nếu sau `n-1` lần lặp, cạnh `(u,v)` vẫn còn "nới lỏng" được, đỉnh `v` (hoặc một đỉnh nào đó trên đường truy vết từ `v`) chắc chắn nằm trên (hoặc bị ảnh hưởng bởi) chu trình âm. Ta truy vết bằng mảng `dinhTruoc[]`, đi ngược **thêm n bước nữa** từ `v` để đảm bảo chắc chắn đã đi vào bên trong chu trình (không chỉ là điểm "vào" chu trình), rồi từ điểm đó truy vết tiếp cho tới khi quay lại đúng điểm đó lần thứ 2.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<int> timChuTrinhAm(vector<tuple<int,int,int>> danhSachCanh, int n) {
    const long long VO_CUC = LLONG_MAX / 2;
    vector<long long> khoangCach(n, 0); // khởi tạo 0 cho mọi đỉnh để tìm chu trình âm bất kỳ trong đồ thị
    vector<int> dinhTruoc(n, -1);
    int dinhBiAnhHuong = -1;

    for (int lanLap = 0; lanLap < n; lanLap++) {
        dinhBiAnhHuong = -1;
        for (int i = 0; i < (int)danhSachCanh.size(); i++) {
            int u = get<0>(danhSachCanh[i]);
            int v = get<1>(danhSachCanh[i]);
            int trongSo = get<2>(danhSachCanh[i]);

            if (khoangCach[u] + trongSo < khoangCach[v]) {
                khoangCach[v] = khoangCach[u] + trongSo;
                dinhTruoc[v] = u;
                dinhBiAnhHuong = v;
            }
        }
    }

    if (dinhBiAnhHuong == -1) {
        return {}; // không có chu trình âm
    }

    // Đi ngược thêm n bước để chắc chắn đang đứng trong chu trình
    int dinhTrongChuTrinh = dinhBiAnhHuong;
    for (int i = 0; i < n; i++) {
        dinhTrongChuTrinh = dinhTruoc[dinhTrongChuTrinh];
    }

    vector<int> chuTrinh;
    int dinhHienTai = dinhTrongChuTrinh;
    do {
        chuTrinh.push_back(dinhHienTai);
        dinhHienTai = dinhTruoc[dinhHienTai];
    } while (dinhHienTai != dinhTrongChuTrinh);

    chuTrinh.push_back(dinhTrongChuTrinh);
    reverse(chuTrinh.begin(), chuTrinh.end());

    return chuTrinh;
}

int main() {
    int n = 3;
    vector<tuple<int,int,int>> danhSachCanh = {
        {0, 1, 1}, {1, 2, -3}, {2, 0, 1}
    };

    vector<int> ketQua = timChuTrinhAm(danhSachCanh, n);
    if (ketQua.empty()) {
        cout << "Khong co chu trinh am" << endl;
    } else {
        cout << "Chu trinh am: ";
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
def tim_chu_trinh_am(danh_sach_canh, n):
    khoang_cach = [0] * n
    dinh_truoc = [-1] * n
    dinh_bi_anh_huong = -1

    for lan_lap in range(n):
        dinh_bi_anh_huong = -1
        for u, v, trong_so in danh_sach_canh:
            if khoang_cach[u] + trong_so < khoang_cach[v]:
                khoang_cach[v] = khoang_cach[u] + trong_so
                dinh_truoc[v] = u
                dinh_bi_anh_huong = v

    if dinh_bi_anh_huong == -1:
        return []

    dinh_trong_chu_trinh = dinh_bi_anh_huong
    for i in range(n):
        dinh_trong_chu_trinh = dinh_truoc[dinh_trong_chu_trinh]

    chu_trinh = []
    dinh_hien_tai = dinh_trong_chu_trinh
    while True:
        chu_trinh.append(dinh_hien_tai)
        dinh_hien_tai = dinh_truoc[dinh_hien_tai]
        if dinh_hien_tai == dinh_trong_chu_trinh:
            break

    chu_trinh.append(dinh_trong_chu_trinh)
    chu_trinh.reverse()

    return chu_trinh


n = 3
danh_sach_canh = [(0, 1, 1), (1, 2, -3), (2, 0, 1)]

ket_qua = tim_chu_trinh_am(danh_sach_canh, n)
if len(ket_qua) == 0:
    print("Khong co chu trinh am")
else:
    print("Chu trinh am:", ket_qua)
```

### Ví dụ 3 (Khó) — Bellman-Ford tối ưu bằng hàng đợi (SPFA — Shortest Path Faster Algorithm)

**Phân tích:** Bellman-Ford chuẩn duyệt **tất cả** m cạnh trong mỗi lần lặp, kể cả những cạnh mà đỉnh nguồn của nó chưa hề bị thay đổi khoảng cách kể từ lần lặp trước. SPFA cải tiến bằng cách chỉ "nới lỏng" các cạnh xuất phát từ những đỉnh **vừa được cập nhật** — dùng một hàng đợi giống BFS. Trong trường hợp trung bình, SPFA nhanh hơn nhiều so với O(n×m), nhưng trong trường hợp xấu nhất (có thể bị thiết kế đề cố tình), độ phức tạp vẫn có thể suy biến về O(n×m) — nên **không được coi là thay thế hoàn toàn** cho Bellman-Ford chuẩn trong các bài thi có dữ liệu đối kháng.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

pair<vector<long long>, bool> spfa(vector<vector<pair<int,int>>> danhSachKe, int n, int dinhNguon) {
    const long long VO_CUC = LLONG_MAX / 2;
    vector<long long> khoangCach(n, VO_CUC);
    vector<int> demSoLanVaoHangDoi(n, 0);
    vector<bool> dangTrongHangDoi(n, false);

    khoangCach[dinhNguon] = 0;
    queue<int> hangDoi;
    hangDoi.push(dinhNguon);
    dangTrongHangDoi[dinhNguon] = true;

    while (!hangDoi.empty()) {
        int u = hangDoi.front();
        hangDoi.pop();
        dangTrongHangDoi[u] = false;

        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            int v = danhSachKe[u][i].first;
            int trongSo = danhSachKe[u][i].second;

            if (khoangCach[u] + trongSo < khoangCach[v]) {
                khoangCach[v] = khoangCach[u] + trongSo;

                if (dangTrongHangDoi[v] == false) {
                    hangDoi.push(v);
                    dangTrongHangDoi[v] = true;
                    demSoLanVaoHangDoi[v] = demSoLanVaoHangDoi[v] + 1;

                    if (demSoLanVaoHangDoi[v] > n) {
                        return make_pair(khoangCach, true); // 1 đỉnh vào hàng đợi quá n lần -> có chu trình âm
                    }
                }
            }
        }
    }

    return make_pair(khoangCach, false);
}

int main() {
    int n = 4;
    vector<vector<pair<int,int>>> danhSachKe(n);
    danhSachKe[0].push_back({1, 4});
    danhSachKe[0].push_back({2, 5});
    danhSachKe[1].push_back({2, -3});
    danhSachKe[2].push_back({3, 4});

    auto ketQua = spfa(danhSachKe, n, 0);
    if (ketQua.second == true) {
        cout << "Co chu trinh am" << endl;
    } else {
        for (int i = 0; i < n; i++) {
            cout << "Dinh " << i << ": " << ketQua.first[i] << endl;
        }
    }

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def spfa(danh_sach_ke, n, dinh_nguon):
    VO_CUC = float('inf')
    khoang_cach = [VO_CUC] * n
    dem_so_lan_vao_hang_doi = [0] * n
    dang_trong_hang_doi = [False] * n

    khoang_cach[dinh_nguon] = 0
    hang_doi = deque()
    hang_doi.append(dinh_nguon)
    dang_trong_hang_doi[dinh_nguon] = True

    while len(hang_doi) > 0:
        u = hang_doi.popleft()
        dang_trong_hang_doi[u] = False

        for v, trong_so in danh_sach_ke[u]:
            if khoang_cach[u] + trong_so < khoang_cach[v]:
                khoang_cach[v] = khoang_cach[u] + trong_so

                if dang_trong_hang_doi[v] == False:
                    hang_doi.append(v)
                    dang_trong_hang_doi[v] = True
                    dem_so_lan_vao_hang_doi[v] += 1

                    if dem_so_lan_vao_hang_doi[v] > n:
                        return khoang_cach, True

    return khoang_cach, False


n = 4
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0].append((1, 4))
danh_sach_ke[0].append((2, 5))
danh_sach_ke[1].append((2, -3))
danh_sach_ke[2].append((3, 4))

khoang_cach, co_chu_trinh_am = spfa(danh_sach_ke, n, 0)
if co_chu_trinh_am == True:
    print("Co chu trinh am")
else:
    for i in range(n):
        print("Dinh", i, ":", khoang_cach[i])
```

**Bài tập minh hoạ:** Cho đồ thị có trọng số âm (không chứa chu trình âm), và một số tiền ban đầu, kiểm tra xem có tồn tại một chuỗi giao dịch tiền tệ (mỗi cạnh là tỷ giá quy đổi, biểu diễn dưới dạng log để cộng dồn thay vì nhân) giúp tăng vô hạn số tiền hay không (đây chính là ứng dụng thực tế của việc phát hiện chu trình âm — gọi là "arbitrage detection" trong tài chính).

**Lời giải:** Áp dụng trực tiếp `bellmanFord`/`bellman_ford`, kiểm tra `coChuTrinhAm`/`co_chu_trinh_am` — nếu true, tồn tại cơ hội giao dịch vòng tròn sinh lợi vô hạn (trên lý thuyết).

---

## 12.3. Thuật toán Floyd-Warshall

**Nêu bài toán:** Cho một đồ thị có trọng số (có thể âm nhưng không có chu trình âm), hãy tìm khoảng cách ngắn nhất giữa **mọi cặp đỉnh** (không chỉ từ 1 đỉnh nguồn cố định).

**Phân tích vấn đề:** Nếu dùng Dijkstra hoặc Bellman-Ford cho từng đỉnh làm nguồn riêng biệt, độ phức tạp sẽ là `n × O(Dijkstra)` hoặc `n × O(Bellman-Ford)`. Với n nhỏ (ví dụ n ≤ 400-500), có một cách tiếp cận trực tiếp hơn để tính đồng thời mọi cặp.

**Giải pháp đơn thuần:** Chạy Bellman-Ford (hoặc Dijkstra nếu không có trọng số âm) n lần, mỗi lần lấy 1 đỉnh làm nguồn. Độ phức tạp O(n² × m) với Bellman-Ford, hoặc O(n × (n+m) log n) với Dijkstra.

**Khó khăn với giải pháp đơn thuần:** Với n vừa phải (vài trăm), cách tiếp cận trên vẫn khả thi nhưng không tối ưu; với n rất nhỏ, tồn tại một thuật toán đơn giản hơn về mặt cài đặt.

**Cách tiếp cận mới — Floyd-Warshall:** Dùng quy hoạch động (Chương 8) với ý tưởng: `khoangCach[i][j]` = độ dài đường đi ngắn nhất từ `i` tới `j`, **chỉ được phép đi qua các đỉnh trung gian có chỉ số từ 1 tới k** (k tăng dần từ 0 tới n). Với mỗi giá trị k, ta cập nhật: `khoangCach[i][j] = min(khoangCach[i][j], khoangCach[i][k] + khoangCach[k][j])` — nghĩa là kiểm tra xem đi "vòng qua" đỉnh k có ngắn hơn không. Độ phức tạp là O(n³), cực kỳ đơn giản để cài đặt (chỉ cần 3 vòng lặp lồng nhau).

### Minh hoạ lời giải chi tiết

**Điểm mấu chốt cần ghi nhớ:** thứ tự 3 vòng lặp **bắt buộc** phải là `k` (đỉnh trung gian) ở **ngoài cùng**, rồi mới tới `i` và `j` — nếu đặt sai thứ tự, thuật toán sẽ cho kết quả sai vì phá vỡ tính chất "chỉ đi qua đỉnh trung gian có chỉ số ≤ k đã được xử lý xong ở các vòng lặp k trước đó".

Với đồ thị 3 đỉnh: khoảng cách trực tiếp là `KC[0][1]=3`, `KC[1][2]=4`, `KC[0][2]=10` (chưa qua trung gian), các cặp không có cạnh trực tiếp coi là ∞.

- Với k=0: kiểm tra `KC[1][2]` qua đỉnh 0 — nhưng không có cạnh 1→0 hay 0→2 hợp lệ nào cải thiện (giả sử không có), không đổi.
- Với k=1: kiểm tra `KC[0][2]` qua đỉnh 1: `KC[0][1] + KC[1][2] = 3 + 4 = 7 < 10` -> cập nhật `KC[0][2] = 7`.
- Với k=2: không còn cải thiện nào khác.

Kết quả: `KC[0][2]` giảm từ 10 xuống 7, nhờ phát hiện đường đi gián tiếp qua đỉnh 1 ngắn hơn đường trực tiếp.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<vector<long long>> floydWarshall(vector<vector<long long>> khoangCachBanDau, int n) {
    vector<vector<long long>> khoangCach = khoangCachBanDau;

    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (khoangCach[i][k] + khoangCach[k][j] < khoangCach[i][j]) {
                    khoangCach[i][j] = khoangCach[i][k] + khoangCach[k][j];
                }
            }
        }
    }

    return khoangCach;
}

int main() {
    int n = 4;
    const long long VO_CUC = LLONG_MAX / 4; // chia 4 để tránh tràn số khi cộng 2 giá trị vô cực

    vector<vector<long long>> khoangCach(n, vector<long long>(n, VO_CUC));
    for (int i = 0; i < n; i++) {
        khoangCach[i][i] = 0; // khoảng cách từ 1 đỉnh tới chính nó luôn là 0
    }

    // Nhập các cạnh trực tiếp
    khoangCach[0][1] = 5;
    khoangCach[1][2] = 3;
    khoangCach[0][3] = 10;
    khoangCach[2][3] = 1;

    vector<vector<long long>> ketQua = floydWarshall(khoangCach, n);

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (ketQua[i][j] >= VO_CUC) {
                cout << "INF ";
            } else {
                cout << ketQua[i][j] << " ";
            }
        }
        cout << endl;
    }

    return 0;
}
```

#### PYTHON
```python
def floyd_warshall(khoang_cach_ban_dau, n):
    khoang_cach = [dong[:] for dong in khoang_cach_ban_dau]  # tạo bản sao độc lập

    for k in range(n):
        for i in range(n):
            for j in range(n):
                if khoang_cach[i][k] + khoang_cach[k][j] < khoang_cach[i][j]:
                    khoang_cach[i][j] = khoang_cach[i][k] + khoang_cach[k][j]

    return khoang_cach


n = 4
VO_CUC = float('inf')

khoang_cach = [[VO_CUC] * n for _ in range(n)]
for i in range(n):
    khoang_cach[i][i] = 0

khoang_cach[0][1] = 5
khoang_cach[1][2] = 3
khoang_cach[0][3] = 10
khoang_cach[2][3] = 1

ket_qua = floyd_warshall(khoang_cach, n)

for i in range(n):
    for j in range(n):
        if ket_qua[i][j] == VO_CUC:
            print("INF", end=" ")
        else:
            print(ket_qua[i][j], end=" ")
    print()
```

> **Lưu ý về giá trị "vô cực" trong C++:** khi dùng `LLONG_MAX` trực tiếp làm giá trị vô cực, phép cộng `khoangCach[i][k] + khoangCach[k][j]` (2 giá trị vô cực cộng lại) có thể gây **tràn số** (overflow), dẫn tới kết quả sai (một số âm rất lớn). Luôn chia giá trị vô cực cho ít nhất 2-4 (như `LLONG_MAX / 4`) để có khoảng đệm an toàn.

### Ví dụ 1 (Dễ) — Tìm đường kính đồ thị (Graph Diameter) bằng Floyd-Warshall

Bài toán: cho đồ thị, "đường kính" là khoảng cách ngắn nhất **lớn nhất** trong số mọi cặp đỉnh có thể tới được nhau. Hãy tính đường kính này.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timDuongKinhDoThi(vector<vector<long long>> khoangCachSauFloyd, int n, long long VO_CUC) {
    long long duongKinh = 0;

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (i != j && khoangCachSauFloyd[i][j] < VO_CUC) {
                if (khoangCachSauFloyd[i][j] > duongKinh) {
                    duongKinh = khoangCachSauFloyd[i][j];
                }
            }
        }
    }

    return duongKinh;
}

int main() {
    int n = 4;
    const long long VO_CUC = LLONG_MAX / 4;

    vector<vector<long long>> khoangCach(n, vector<long long>(n, VO_CUC));
    for (int i = 0; i < n; i++) khoangCach[i][i] = 0;

    khoangCach[0][1] = 1;
    khoangCach[1][2] = 1;
    khoangCach[2][3] = 1;

    vector<vector<long long>> ketQua = floydWarshall(khoangCach, n);
    cout << timDuongKinhDoThi(ketQua, n, VO_CUC) << endl; // 3 (từ đỉnh 0 tới đỉnh 3)

    return 0;
}
```

#### PYTHON
```python
def tim_duong_kinh_do_thi(khoang_cach_sau_floyd, n, VO_CUC):
    duong_kinh = 0

    for i in range(n):
        for j in range(n):
            if i != j and khoang_cach_sau_floyd[i][j] < VO_CUC:
                if khoang_cach_sau_floyd[i][j] > duong_kinh:
                    duong_kinh = khoang_cach_sau_floyd[i][j]

    return duong_kinh


n = 4
VO_CUC = float('inf')

khoang_cach = [[VO_CUC] * n for _ in range(n)]
for i in range(n):
    khoang_cach[i][i] = 0

khoang_cach[0][1] = 1
khoang_cach[1][2] = 1
khoang_cach[2][3] = 1

ket_qua = floyd_warshall(khoang_cach, n)
print(tim_duong_kinh_do_thi(ket_qua, n, VO_CUC))  # 3
```

### Ví dụ 2 (Trung bình) — Phát hiện chu trình âm bằng Floyd-Warshall

**Phân tích:** Sau khi chạy Floyd-Warshall, nếu tồn tại một đỉnh `i` sao cho `khoangCach[i][i] < 0`, điều đó chứng tỏ có một chu trình âm đi qua đỉnh `i` (đường đi từ `i` quay lại chính `i` có tổng trọng số âm).

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

bool kiemTraChuTrinhAmBangFloyd(vector<vector<long long>> khoangCachSauFloyd, int n) {
    for (int i = 0; i < n; i++) {
        if (khoangCachSauFloyd[i][i] < 0) {
            return true;
        }
    }
    return false;
}

int main() {
    int n = 3;
    const long long VO_CUC = LLONG_MAX / 4;

    vector<vector<long long>> khoangCach(n, vector<long long>(n, VO_CUC));
    for (int i = 0; i < n; i++) khoangCach[i][i] = 0;

    khoangCach[0][1] = 1;
    khoangCach[1][2] = -3;
    khoangCach[2][0] = 1;

    vector<vector<long long>> ketQua = floydWarshall(khoangCach, n);
    cout << (kiemTraChuTrinhAmBangFloyd(ketQua, n) ? "Co chu trinh am" : "Khong co chu trinh am") << endl;

    return 0;
}
```

#### PYTHON
```python
def kiem_tra_chu_trinh_am_bang_floyd(khoang_cach_sau_floyd, n):
    for i in range(n):
        if khoang_cach_sau_floyd[i][i] < 0:
            return True
    return False


n = 3
VO_CUC = float('inf')

khoang_cach = [[VO_CUC] * n for _ in range(n)]
for i in range(n):
    khoang_cach[i][i] = 0

khoang_cach[0][1] = 1
khoang_cach[1][2] = -3
khoang_cach[2][0] = 1

ket_qua = floyd_warshall(khoang_cach, n)
print("Co chu trinh am" if kiem_tra_chu_trinh_am_bang_floyd(ket_qua, n) else "Khong co chu trinh am")
```

### Ví dụ 3 (Khó) — Tìm đường đi ngắn nhất với truy vết trên Floyd-Warshall

Bài toán: ngoài tính khoảng cách giữa mọi cặp đỉnh, hãy truy vết ra **đường đi cụ thể** giữa 1 cặp đỉnh bất kỳ.

**Phân tích:** Duy trì thêm một ma trận `dinhTiepTheo[i][j]` = "từ i muốn đi ngắn nhất tới j, bước tiếp theo nên đi tới đỉnh nào?" — cập nhật ma trận này song song với việc cập nhật `khoangCach[i][j]` trong vòng lặp Floyd-Warshall.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

pair<vector<vector<long long>>, vector<vector<int>>> floydWarshallCoTruyVet(vector<vector<long long>> khoangCachBanDau, int n) {
    vector<vector<long long>> khoangCach = khoangCachBanDau;
    vector<vector<int>> dinhTiepTheo(n, vector<int>(n, -1));

    for (int i = 0; i < n; i++) {
        for (int j = 0; j < n; j++) {
            if (i != j && khoangCach[i][j] < LLONG_MAX / 4) {
                dinhTiepTheo[i][j] = j; // ban đầu: nếu có cạnh trực tiếp, bước tiếp theo là chính j
            }
        }
    }

    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (khoangCach[i][k] + khoangCach[k][j] < khoangCach[i][j]) {
                    khoangCach[i][j] = khoangCach[i][k] + khoangCach[k][j];
                    dinhTiepTheo[i][j] = dinhTiepTheo[i][k]; // đi qua k, nên bước đầu tiên giống như đi tới k
                }
            }
        }
    }

    return make_pair(khoangCach, dinhTiepTheo);
}

vector<int> truyVetDuongDiFloyd(vector<vector<int>> dinhTiepTheo, int u, int v) {
    if (dinhTiepTheo[u][v] == -1) {
        return {}; // không tồn tại đường đi
    }

    vector<int> duongDi;
    duongDi.push_back(u);

    while (u != v) {
        u = dinhTiepTheo[u][v];
        duongDi.push_back(u);
    }

    return duongDi;
}

int main() {
    int n = 4;
    const long long VO_CUC = LLONG_MAX / 4;

    vector<vector<long long>> khoangCach(n, vector<long long>(n, VO_CUC));
    for (int i = 0; i < n; i++) khoangCach[i][i] = 0;

    khoangCach[0][1] = 1;
    khoangCach[1][2] = 1;
    khoangCach[2][3] = 1;

    auto ketQua = floydWarshallCoTruyVet(khoangCach, n);
    vector<int> duongDi = truyVetDuongDiFloyd(ketQua.second, 0, 3);

    cout << "Duong di tu 0 den 3: ";
    for (int i = 0; i < (int)duongDi.size(); i++) {
        cout << duongDi[i] << " ";
    }
    cout << endl;

    return 0;
}
```

#### PYTHON
```python
def floyd_warshall_co_truy_vet(khoang_cach_ban_dau, n):
    khoang_cach = [dong[:] for dong in khoang_cach_ban_dau]
    dinh_tiep_theo = [[-1] * n for _ in range(n)]

    VO_CUC = float('inf')
    for i in range(n):
        for j in range(n):
            if i != j and khoang_cach[i][j] < VO_CUC:
                dinh_tiep_theo[i][j] = j

    for k in range(n):
        for i in range(n):
            for j in range(n):
                if khoang_cach[i][k] + khoang_cach[k][j] < khoang_cach[i][j]:
                    khoang_cach[i][j] = khoang_cach[i][k] + khoang_cach[k][j]
                    dinh_tiep_theo[i][j] = dinh_tiep_theo[i][k]

    return khoang_cach, dinh_tiep_theo


def truy_vet_duong_di_floyd(dinh_tiep_theo, u, v):
    if dinh_tiep_theo[u][v] == -1:
        return []

    duong_di = [u]

    while u != v:
        u = dinh_tiep_theo[u][v]
        duong_di.append(u)

    return duong_di


n = 4
VO_CUC = float('inf')

khoang_cach = [[VO_CUC] * n for _ in range(n)]
for i in range(n):
    khoang_cach[i][i] = 0

khoang_cach[0][1] = 1
khoang_cach[1][2] = 1
khoang_cach[2][3] = 1

khoang_cach_ket_qua, dinh_tiep_theo = floyd_warshall_co_truy_vet(khoang_cach, n)
duong_di = truy_vet_duong_di_floyd(dinh_tiep_theo, 0, 3)

print("Duong di tu 0 den 3:", duong_di)
```

**Bài tập minh hoạ:** Cho đồ thị n đỉnh (n ≤ 400), hãy tìm 3 đỉnh `a, b, c` sao cho `khoangCach[a][b] + khoangCach[b][c] + khoangCach[c][a]` (một "chu trình tam giác" đi qua cả 3 đỉnh) là **nhỏ nhất có thể**.

**Lời giải:** Sau khi chạy Floyd-Warshall 1 lần (O(n³)), thử tất cả các bộ ba `(a,b,c)` — độ phức tạp thêm O(n³) nữa, nhưng với n ≤ 400, tổng độ phức tạp khoảng 400³ × 2 ≈ 1.28×10^8, vẫn chấp nhận được.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

long long timChuTrinhTamGiacNhoNhat(vector<vector<long long>> khoangCachSauFloyd, int n, long long VO_CUC) {
    long long ketQuaTotNhat = VO_CUC;

    for (int a = 0; a < n; a++) {
        for (int b = 0; b < n; b++) {
            if (a == b || khoangCachSauFloyd[a][b] >= VO_CUC) continue;

            for (int c = 0; c < n; c++) {
                if (c == a || c == b) continue;
                if (khoangCachSauFloyd[b][c] >= VO_CUC || khoangCachSauFloyd[c][a] >= VO_CUC) continue;

                long long tongChiPhi = khoangCachSauFloyd[a][b] + khoangCachSauFloyd[b][c] + khoangCachSauFloyd[c][a];
                if (tongChiPhi < ketQuaTotNhat) {
                    ketQuaTotNhat = tongChiPhi;
                }
            }
        }
    }

    return ketQuaTotNhat;
}
```

#### PYTHON
```python
def tim_chu_trinh_tam_giac_nho_nhat(khoang_cach_sau_floyd, n, VO_CUC):
    ket_qua_tot_nhat = VO_CUC

    for a in range(n):
        for b in range(n):
            if a == b or khoang_cach_sau_floyd[a][b] >= VO_CUC:
                continue

            for c in range(n):
                if c == a or c == b:
                    continue
                if khoang_cach_sau_floyd[b][c] >= VO_CUC or khoang_cach_sau_floyd[c][a] >= VO_CUC:
                    continue

                tong_chi_phi = khoang_cach_sau_floyd[a][b] + khoang_cach_sau_floyd[b][c] + khoang_cach_sau_floyd[c][a]
                if tong_chi_phi < ket_qua_tot_nhat:
                    ket_qua_tot_nhat = tong_chi_phi

    return ket_qua_tot_nhat
```

---

## 12.4. Thuật toán 0-1 BFS

**Nêu bài toán:** Cho một đồ thị mà **mọi cạnh chỉ có trọng số 0 hoặc 1**, hãy tìm khoảng cách ngắn nhất từ đỉnh nguồn tới tất cả các đỉnh khác.

**Phân tích vấn đề:** Dijkstra (mục 12.1) giải quyết được bài toán này với độ phức tạp O((n+m) log n) nhờ priority queue. Nhưng vì trọng số chỉ có 2 giá trị (0 hoặc 1) — một trường hợp đặc biệt — ta có thể làm nhanh hơn, đạt O(n+m) mà **không cần** priority queue (vốn có thêm hệ số log).

**Giải pháp đơn thuần:** Dùng Dijkstra như bình thường với priority queue — vẫn đúng, nhưng có hệ số log không cần thiết.

**Cách tiếp cận mới — 0-1 BFS:** Dùng một **deque** (Chương 3, 6) thay cho priority queue. Khi "nới lỏng" một cạnh có trọng số 0, đẩy đỉnh đích vào **đầu** deque (vì nó có cùng khoảng cách với đỉnh hiện tại, nên "gần" như đỉnh hiện tại, xử lý ngay). Khi nới lỏng một cạnh có trọng số 1, đẩy đỉnh đích vào **cuối** deque (xử lý sau, tương tự BFS thường). Tính chất deque đảm bảo tại mọi thời điểm, các đỉnh trong deque được sắp xếp theo khoảng cách **không giảm** từ đầu tới cuối — giống hệt tính chất mà priority queue duy trì, nhưng đạt được với chi phí O(1) mỗi thao tác thay vì O(log n).

### Minh hoạ lời giải chi tiết

Đồ thị: 0→1 (trọng số 0), 0→2 (trọng số 1), 1→2 (trọng số 0), 2→3 (trọng số 1). Tìm khoảng cách từ đỉnh 0.

| Bước | Deque trước | Đỉnh xử lý | Hành động | Deque sau |
|---|---|---|---|---|
| Khởi tạo | [] | - | Đưa 0 vào deque, KC[0]=0 | [0] |
| 1 | [0] | 0 | Cạnh 0→1 (w=0): đẩy đầu, KC[1]=0. Cạnh 0→2 (w=1): đẩy cuối, KC[2]=1 | [1, 2] |
| 2 | [1, 2] | 1 | Cạnh 1→2 (w=0): 0+0=0 < KC[2]=1 -> cập nhật KC[2]=0, đẩy đầu | [2, 2] (2 lần, 1 lần cũ lỗi thời) |
| 3 | [2, 2] | 2 (KC=0) | Cạnh 2→3 (w=1): 0+1=1 < ∞ -> KC[3]=1, đẩy cuối | [2, 3] |
| 4 | [2, 3] | 2 (bản cũ, KC ghi nhận đã là 0, không đổi gì thêm) | Bỏ qua vì đã xử lý | [3] |
| 5 | [3] | 3 | không còn cạnh nào | [] |

Kết quả: `KC = [0, 0, 0, 1]`.

### Code khung mẫu

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

vector<long long> zeroOneBFS(vector<vector<pair<int,int>>> danhSachKe, int n, int dinhNguon) {
    // danhSachKe[u] chứa (đỉnh kề v, trọng số 0 hoặc 1)
    const long long VO_CUC = LLONG_MAX / 2;
    vector<long long> khoangCach(n, VO_CUC);
    khoangCach[dinhNguon] = 0;

    deque<int> hangDoi;
    hangDoi.push_back(dinhNguon);

    while (!hangDoi.empty()) {
        int u = hangDoi.front();
        hangDoi.pop_front();

        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            int v = danhSachKe[u][i].first;
            int trongSo = danhSachKe[u][i].second;

            long long khoangCachMoi = khoangCach[u] + trongSo;
            if (khoangCachMoi < khoangCach[v]) {
                khoangCach[v] = khoangCachMoi;

                if (trongSo == 0) {
                    hangDoi.push_front(v); // trọng số 0 -> đẩy vào ĐẦU, xử lý ngay
                } else {
                    hangDoi.push_back(v); // trọng số 1 -> đẩy vào CUỐI, xử lý sau
                }
            }
        }
    }

    return khoangCach;
}

int main() {
    int n = 4;
    vector<vector<pair<int,int>>> danhSachKe(n);
    danhSachKe[0].push_back({1, 0});
    danhSachKe[0].push_back({2, 1});
    danhSachKe[1].push_back({2, 0});
    danhSachKe[2].push_back({3, 1});

    vector<long long> ketQua = zeroOneBFS(danhSachKe, n, 0);
    for (int i = 0; i < n; i++) {
        cout << "Dinh " << i << ": " << ketQua[i] << endl;
    }

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def zero_one_bfs(danh_sach_ke, n, dinh_nguon):
    VO_CUC = float('inf')
    khoang_cach = [VO_CUC] * n
    khoang_cach[dinh_nguon] = 0

    hang_doi = deque()
    hang_doi.append(dinh_nguon)

    while len(hang_doi) > 0:
        u = hang_doi.popleft()

        for v, trong_so in danh_sach_ke[u]:
            khoang_cach_moi = khoang_cach[u] + trong_so

            if khoang_cach_moi < khoang_cach[v]:
                khoang_cach[v] = khoang_cach_moi

                if trong_so == 0:
                    hang_doi.appendleft(v)
                else:
                    hang_doi.append(v)

    return khoang_cach


n = 4
danh_sach_ke = [[] for _ in range(n)]
danh_sach_ke[0].append((1, 0))
danh_sach_ke[0].append((2, 1))
danh_sach_ke[1].append((2, 0))
danh_sach_ke[2].append((3, 1))

ket_qua = zero_one_bfs(danh_sach_ke, n, 0)
for i in range(n):
    print("Dinh", i, ":", ket_qua[i])
```

> **Lưu ý quan trọng:** khác với Dijkstra (nơi ta kiểm tra "thông tin cũ lỗi thời" bằng cách so sánh khoảng cách khi lấy ra khỏi hàng đợi), code 0-1 BFS ở trên **không có bước kiểm tra này tường minh** — nhưng vẫn đúng vì mỗi khi tìm được khoảng cách tốt hơn, ta cập nhật `khoangCach[v]` ngay lập tức; các bản ghi "cũ" trong deque tuy vẫn tồn tại nhưng khi được xử lý, chúng vẫn dùng đúng giá trị `khoangCach[u]` **hiện tại** (đã cập nhật), không gây sai kết quả — chỉ hơi lãng phí 1 chút thời gian xử lý dư thừa.

### Ví dụ 1 (Dễ) — 0-1 BFS trên lưới ô vuông có "cổng dịch chuyển"

Bài toán: cho lưới ô vuông, di chuyển sang ô liền kề tốn 1 đơn vị thời gian; nhưng có một số ô là "cổng dịch chuyển" cùng loại, di chuyển giữa 2 ô cùng loại cổng dịch chuyển bất kỳ (không cần liền kề) tốn 0 đơn vị thời gian. Tìm thời gian ngắn nhất từ ô xuất phát tới ô đích.

**Phân tích:** Xây dựng đồ thị ẩn: cạnh trọng số 1 giữa các ô liền kề, cạnh trọng số 0 giữa mọi cặp ô có cùng loại cổng dịch chuyển — áp dụng trực tiếp 0-1 BFS.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int main() {
    int n = 3, m = 3;
    vector<string> luoi = {
        "A.#",
        "...",
        "#.A"
    };
    // 'A' là cổng dịch chuyển loại A, '.' là ô trống, '#' là chướng ngại vật

    auto chiSo = [&](int hang, int cot) { return hang * m + cot; };
    int soODinh = n * m;
    vector<vector<pair<int,int>>> danhSachKe(soODinh);

    map<char, vector<int>> nhomCongDichChuyen;

    int dHang[] = {0,0,1,-1}, dCot[] = {1,-1,0,0};
    for (int i = 0; i < n; i++) {
        for (int j = 0; j < m; j++) {
            if (luoi[i][j] == '#') continue;

            for (int huong = 0; huong < 4; huong++) {
                int iMoi = i + dHang[huong], jMoi = j + dCot[huong];
                if (iMoi>=0 && iMoi<n && jMoi>=0 && jMoi<m && luoi[iMoi][jMoi] != '#') {
                    danhSachKe[chiSo(i,j)].push_back({chiSo(iMoi,jMoi), 1});
                }
            }

            if (luoi[i][j] != '.') {
                nhomCongDichChuyen[luoi[i][j]].push_back(chiSo(i,j));
            }
        }
    }

    for (auto &nhom : nhomCongDichChuyen) {
        vector<int> &cacO = nhom.second;
        for (int i = 0; i < (int)cacO.size(); i++) {
            for (int j = 0; j < (int)cacO.size(); j++) {
                if (i != j) {
                    danhSachKe[cacO[i]].push_back({cacO[j], 0});
                }
            }
        }
    }

    vector<long long> ketQua = zeroOneBFS(danhSachKe, soODinh, chiSo(0,0));
    cout << ketQua[chiSo(2,2)] << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque, defaultdict

n, m = 3, 3
luoi = ["A.#", "...", "#.A"]


def chi_so(hang, cot):
    return hang * m + cot


so_o_dinh = n * m
danh_sach_ke = [[] for _ in range(so_o_dinh)]
nhom_cong_dich_chuyen = defaultdict(list)

d_hang = [0, 0, 1, -1]
d_cot = [1, -1, 0, 0]

for i in range(n):
    for j in range(m):
        if luoi[i][j] == '#':
            continue

        for huong in range(4):
            i_moi = i + d_hang[huong]
            j_moi = j + d_cot[huong]
            if 0 <= i_moi < n and 0 <= j_moi < m and luoi[i_moi][j_moi] != '#':
                danh_sach_ke[chi_so(i, j)].append((chi_so(i_moi, j_moi), 1))

        if luoi[i][j] != '.':
            nhom_cong_dich_chuyen[luoi[i][j]].append(chi_so(i, j))

for loai, cac_o in nhom_cong_dich_chuyen.items():
    for i in range(len(cac_o)):
        for j in range(len(cac_o)):
            if i != j:
                danh_sach_ke[cac_o[i]].append((cac_o[j], 0))

ket_qua = zero_one_bfs(danh_sach_ke, so_o_dinh, chi_so(0, 0))
print(ket_qua[chi_so(2, 2)])
```

### Ví dụ 2 (Trung bình) — Bài toán "số lần đổi hướng tối thiểu"

Bài toán: cho lưới ô vuông, mỗi ô có 1 mũi tên chỉ hướng bắt buộc di chuyển "mặc định". Nếu đi theo đúng hướng mũi tên, tốn 0 chi phí; nếu muốn đi hướng khác, tốn 1 chi phí (đại diện cho việc phải "sửa hướng"). Tìm chi phí nhỏ nhất để đi từ ô xuất phát tới ô đích.

**Phân tích:** Đây là ứng dụng kinh điển khác của 0-1 BFS — mỗi ô có 4 cạnh đi ra (4 hướng có thể), cạnh đi theo đúng hướng mũi tên có trọng số 0, còn lại có trọng số 1.

#### C++
```cpp
#include <bits/stdc++.h>
using namespace std;

int minCost(vector<vector<int>> luoi) {
    // luoi[i][j]: 1=phải, 2=trái, 3=xuống, 4=lên (quy ước hướng mặc định)
    int n = (int)luoi.size(), m = (int)luoi[0].size();
    int dHang[] = {0,0,1,-1}, dCot[] = {1,-1,0,0}; // ứng với hướng 1,2,3,4

    vector<vector<int>> khoangCach(n, vector<int>(m, INT_MAX));
    deque<pair<int,int>> hangDoi;

    khoangCach[0][0] = 0;
    hangDoi.push_back({0,0});

    while (!hangDoi.empty()) {
        auto [hang, cot] = hangDoi.front();
        hangDoi.pop_front();

        for (int huong = 0; huong < 4; huong++) {
            int hangMoi = hang + dHang[huong], cotMoi = cot + dCot[huong];
            if (hangMoi < 0 || hangMoi >= n || cotMoi < 0 || cotMoi >= m) continue;

            int chiPhi = (luoi[hang][cot] == huong + 1) ? 0 : 1;
            int khoangCachMoi = khoangCach[hang][cot] + chiPhi;

            if (khoangCachMoi < khoangCach[hangMoi][cotMoi]) {
                khoangCach[hangMoi][cotMoi] = khoangCachMoi;
                if (chiPhi == 0) hangDoi.push_front({hangMoi, cotMoi});
                else hangDoi.push_back({hangMoi, cotMoi});
            }
        }
    }

    return khoangCach[n-1][m-1];
}

int main() {
    vector<vector<int>> luoi = {
        {1, 1, 1, 1},
        {2, 2, 2, 2},
        {1, 1, 1, 1},
        {2, 2, 2, 2}
    };

    cout << minCost(luoi) << endl;

    return 0;
}
```

#### PYTHON
```python
from collections import deque


def min_cost(luoi):
    n, m = len(luoi), len(luoi[0])
    d_hang = [0, 0, 1, -1]
    d_cot = [1, -1, 0, 0]

    khoang_cach = [[float('inf')] * m for _ in range(n)]
    hang_doi = deque()

    khoang_cach[0][0] = 0
    hang_doi.append((0, 0))

    while len(hang_doi) > 0:
        hang, cot = hang_doi.popleft()

        for huong in range(4):
            hang_moi = hang + d_hang[huong]
            cot_moi = cot + d_cot[huong]
            if hang_moi < 0 or hang_moi >= n or cot_moi < 0 or cot_moi >= m:
                continue

            chi_phi = 0 if luoi[hang][cot] == huong + 1 else 1
            khoang_cach_moi = khoang_cach[hang][cot] + chi_phi

            if khoang_cach_moi < khoang_cach[hang_moi][cot_moi]:
                khoang_cach[hang_moi][cot_moi] = khoang_cach_moi
                if chi_phi == 0:
                    hang_doi.appendleft((hang_moi, cot_moi))
                else:
                    hang_doi.append((hang_moi, cot_moi))

    return khoang_cach[n-1][m-1]


luoi = [
    [1, 1, 1, 1],
    [2, 2, 2, 2],
    [1, 1, 1, 1],
    [2, 2, 2, 2],
]

print(min_cost(luoi))
```

### Ví dụ 3 (Khó) — Mở rộng 0-1 BFS thành "k-BFS" cho trọng số nhỏ tổng quát

**Phân tích:** Nếu trọng số cạnh không chỉ giới hạn ở 0 và 1, mà là các số nguyên nhỏ (ví dụ từ 0 đến k, với k nhỏ), ta có thể mở rộng ý tưởng deque bằng cách dùng **k+1 hàng đợi thường** (dạng "bucket queue"), mỗi hàng đợi ứng với 1 khoảng cách modulo (k+1), hoặc dùng cấu trúc "Dial's algorithm" — đây là kỹ thuật nâng cao hơn phạm vi minh hoạ chi tiết của chương này.

> **Ghi chú:** Dial's Algorithm sử dụng mảng các "bucket" (thùng chứa), mỗi bucket ứng với 1 giá trị khoảng cách cụ thể — khi trọng số cạnh bị chặn bởi 1 giá trị nhỏ W, độ phức tạp có thể đạt O(n + m + n×W), tốt hơn Dijkstra khi W nhỏ. Khuyến khích tìm hiểu thêm sau khi đã vững 0-1 BFS.

**Bài tập minh hoạ:** Cho một đồ thị mà cạnh chỉ có 2 loại trọng số là 0 và **C** (một hằng số cố định, không nhất thiết là 1), hãy điều chỉnh 0-1 BFS để áp dụng được cho trường hợp này.

**Lời giải:** Về bản chất thuật toán không đổi — chỉ cần thay giá trị cộng vào khoảng cách khi đi qua cạnh "nặng" từ 1 thành C; nguyên tắc "cạnh 0 đẩy vào đầu deque, cạnh có trọng số dương đẩy vào cuối deque" vẫn đúng, vì tính chất quan trọng chỉ là "có đúng 2 loại giá trị trọng số phân biệt", không nhất thiết phải là 0 và 1 cụ thể.

#### C++
```cpp
vector<long long> zeroOneBFSTongQuat(vector<vector<pair<int,int>>> danhSachKe, int n, int dinhNguon, int C) {
    const long long VO_CUC = LLONG_MAX / 2;
    vector<long long> khoangCach(n, VO_CUC);
    khoangCach[dinhNguon] = 0;

    deque<int> hangDoi;
    hangDoi.push_back(dinhNguon);

    while (!hangDoi.empty()) {
        int u = hangDoi.front();
        hangDoi.pop_front();

        for (int i = 0; i < (int)danhSachKe[u].size(); i++) {
            int v = danhSachKe[u][i].first;
            int trongSo = danhSachKe[u][i].second; // trọng số là 0 hoặc C

            long long khoangCachMoi = khoangCach[u] + trongSo;
            if (khoangCachMoi < khoangCach[v]) {
                khoangCach[v] = khoangCachMoi;
                if (trongSo == 0) hangDoi.push_front(v);
                else hangDoi.push_back(v);
            }
        }
    }

    return khoangCach;
}
```

#### PYTHON
```python
def zero_one_bfs_tong_quat(danh_sach_ke, n, dinh_nguon, C):
    VO_CUC = float('inf')
    khoang_cach = [VO_CUC] * n
    khoang_cach[dinh_nguon] = 0

    hang_doi = deque()
    hang_doi.append(dinh_nguon)

    while len(hang_doi) > 0:
        u = hang_doi.popleft()

        for v, trong_so in danh_sach_ke[u]:
            khoang_cach_moi = khoang_cach[u] + trong_so

            if khoang_cach_moi < khoang_cach[v]:
                khoang_cach[v] = khoang_cach_moi
                if trong_so == 0:
                    hang_doi.appendleft(v)
                else:
                    hang_doi.append(v)

    return khoang_cach
```

---

## Tổng kết Chương 12

- **Dijkstra:** áp dụng khi **mọi trọng số không âm**, dùng priority queue để luôn xử lý đỉnh gần nhất trước, độ phức tạp O((n+m) log n).
- **Bellman-Ford:** áp dụng khi có thể có **trọng số âm**, lặp lại việc nới lỏng toàn bộ cạnh đúng n-1 lần, độ phức tạp O(n×m) — chậm hơn Dijkstra nhưng xử lý được trọng số âm và phát hiện được chu trình âm. SPFA là biến thể tối ưu hoá bằng hàng đợi, nhanh hơn trong thực tế nhưng vẫn có thể suy biến về O(n×m) trong trường hợp xấu.
- **Floyd-Warshall:** áp dụng khi cần khoảng cách giữa **mọi cặp đỉnh** và n nhỏ (thường ≤ 400-500), độ phức tạp O(n³), cài đặt cực kỳ đơn giản (3 vòng lặp lồng nhau) — lưu ý thứ tự vòng lặp `k, i, j` là bắt buộc.
- **0-1 BFS:** trường hợp đặc biệt khi trọng số cạnh chỉ có 0 hoặc 1, dùng deque thay cho priority queue để đạt độ phức tạp O(n+m), không có hệ số log.
- **Kỹ năng cốt lõi cần luyện:** trước khi chọn thuật toán, luôn tự hỏi 3 câu: (1) cần khoảng cách từ 1 nguồn hay mọi cặp đỉnh? (2) trọng số có thể âm không? (3) trọng số chỉ có 2 giá trị đặc biệt (0/1) hay tổng quát? — 3 câu trả lời này sẽ dẫn thẳng tới đúng 1 trong 4 thuật toán của chương này.

---

## Bài tập cuối chương — Chương 12

### PHẦN A: ĐỀ BÀI (16 bài, không kèm lời giải)

**Bài 1 — Shortest Routes I (CSES 1671) — Dễ**
Giống hệt Code khung mẫu, mục 12.1 đã trình bày (dùng để luyện tập lại).

**Bài 2 — Network Delay Time (LeetCode 743) — Trung bình**
Cho n nút mạng, thời gian truyền tín hiệu giữa các cặp nút, và 1 nút nguồn phát tín hiệu. Tìm thời gian tối thiểu để tín hiệu lan tới tất cả các nút (chính là khoảng cách lớn nhất trong mảng khoảng cách Dijkstra từ nguồn).

**Bài 3 — Cheapest Flights Within K Stops (LeetCode 787) — Trung bình**
Tìm chi phí bay nhỏ nhất từ thành phố nguồn tới thành phố đích, với ràng buộc số điểm dừng tối đa là K. (Gợi ý: mở rộng trạng thái Dijkstra/Bellman-Ford để bao gồm cả "số bước đã đi", tương tự Ví dụ 3, mục 12.1.)

**Bài 4 — Path with Maximum Probability (LeetCode 1514) — Trung bình**
Tìm đường đi có xác suất thành công lớn nhất giữa 2 đỉnh, với mỗi cạnh có 1 xác suất thành công riêng (xác suất của đường đi là tích các xác suất từng cạnh). (Gợi ý: biến đổi bài toán "tìm max của tích" thành "tìm min/max của tổng" bằng logarit, hoặc điều chỉnh trực tiếp Dijkstra để dùng phép nhân và max-heap.)

**Bài 5 — High Score (CSES 1673) — Trung bình**
Giống hệt Code khung mẫu, mục 12.2 (Bellman-Ford, phát hiện chu trình dương ảnh hưởng tới đường đi tối ưu — lưu ý bài này là tìm đường đi DÀI nhất, cần đảo dấu trọng số hoặc đảo chiều bất đẳng thức).

**Bài 6 — Cycle Finding (CSES 1197) — Trung bình**
Giống hệt Ví dụ 2, mục 12.2 đã trình bày (dùng để luyện tập lại, tìm và in ra chu trình âm cụ thể).

**Bài 7 — Shortest Routes II (CSES 1672) — Trung bình**
Giống hệt Code khung mẫu, mục 12.3 đã trình bày (dùng để luyện tập lại, với nhiều truy vấn khoảng cách giữa các cặp đỉnh).

**Bài 8 — Find the City With the Smallest Number of Neighbors at a Threshold Distance (LeetCode 1334) — Trung bình**
Cho đồ thị, với mỗi thành phố, đếm số thành phố khác có khoảng cách ngắn nhất ≤ ngưỡng cho trước; tìm thành phố có số lượng này ít nhất (nếu bằng nhau, chọn thành phố có chỉ số lớn nhất). (Gợi ý: Floyd-Warshall để tính mọi cặp khoảng cách.)

**Bài 9 — 0-1 Matrix / Nearest 0 (LeetCode 542, đối chiếu với 0-1 BFS) — Trung bình**
Cho ma trận nhị phân, với mỗi ô 1, tìm khoảng cách (Manhattan) tới ô 0 gần nhất. (Gợi ý: đây thực chất giải được bằng Multi-source BFS thường, không cần 0-1 BFS — hãy phân tích vì sao 2 kỹ thuật cho cùng kết quả trong bài này.)

**Bài 10 — Jump Game III với 2 loại chi phí (tự thiết kế, minh hoạ 0-1 BFS) — Trung bình**
Cho mảng số nguyên, xuất phát từ vị trí 0, mỗi bước nhảy đúng 1 vị trí sang trái hoặc phải (chi phí 1), hoặc "nhảy đặc biệt" tới vị trí có cùng giá trị bất kỳ trong mảng (chi phí 0, nếu tồn tại). Tìm chi phí ít nhất để tới vị trí cuối mảng.

**Bài 11 — Swim in Rising Water (LeetCode 778) — Khó**
Cho lưới ô vuông với độ cao mỗi ô, nước dâng theo thời gian, tại thời điểm t chỉ có thể đi qua ô có độ cao ≤ t. Tìm thời gian nhỏ nhất để có thể đi từ góc trên-trái tới góc dưới-phải. (Gợi ý: biến thể Dijkstra với "khoảng cách" là độ cao lớn nhất phải vượt qua trên đường đi, thay vì tổng trọng số.)

**Bài 12 — Path With Minimum Effort (LeetCode 1631) — Khó**
Tương tự Bài 11, nhưng "effort" là chênh lệch độ cao lớn nhất giữa 2 ô liền kề trên đường đi (không phải độ cao tuyệt đối). Tìm đường đi có effort nhỏ nhất.

**Bài 13 — Bài toán tổng hợp: tìm đường đi ngắn nhất tránh k cạnh cấm (tự thiết kế) — Khó**
Cho đồ thị và số k, tìm đường đi ngắn nhất từ nguồn tới đích, nhưng được phép "bỏ qua" (coi trọng số = 0) tối đa k cạnh bất kỳ trên đường đi. (Gợi ý: mở rộng trạng thái Dijkstra thành (đỉnh, số lần đã dùng đặc quyền bỏ qua cạnh).)

**Bài 14 — Second Minimum Time to Reach Destination (LeetCode 2045) — Khó**
Tìm thời gian ngắn thứ 2 (không phải ngắn nhất) để đi từ đỉnh 1 tới đỉnh n trên đồ thị có thêm ràng buộc về đèn giao thông (mỗi cạnh có thời gian đi cố định, nhưng có thể phải chờ nếu đèn đỏ). (Gợi ý: liên hệ với Ví dụ 3, mục 12.1 — tìm đường đi ngắn thứ K.)

**Bài 15 — Ma trận trọng số hỗn hợp âm dương với truy vấn nhiều nguồn (tự thiết kế) — Khó**
Cho đồ thị có trọng số (có thể âm, không có chu trình âm) và q truy vấn, mỗi truy vấn hỏi khoảng cách ngắn nhất giữa 1 cặp đỉnh cho trước. Với n ≤ 500 và q lớn, hãy chọn thuật toán phù hợp và giải thích lý do lựa chọn.

**Bài 16 — Kết hợp Floyd-Warshall với truy vết đường đi và đếm số đường đi ngắn nhất (tự thiết kế, dạng đề thi HSG) — Khó**
Mở rộng Ví dụ 3, mục 12.3: ngoài truy vết đường đi, hãy đếm xem có **bao nhiêu** đường đi ngắn nhất khác nhau giữa mỗi cặp đỉnh (giả sử không có 2 đường đi trùng nhau hoàn toàn về tập cạnh được coi là đường đi khác nhau).

---

### PHẦN B: LỜI GIẢI

<details>
<summary>Lời giải Bài 1 — Shortest Routes I</summary>

Giống hệt hàm `dijkstra`/`dijkstra` ở Code khung mẫu, mục 12.1.
</details>

<details>
<summary>Lời giải Bài 2 — Network Delay Time</summary>

```cpp
int networkDelayTime(vector<vector<int>> &times, int n, int k) {
    vector<vector<pair<int,int>>> danhSachKe(n + 1);
    for (auto &t : times) {
        danhSachKe[t[0]].push_back({t[1], t[2]});
    }

    vector<long long> khoangCach = dijkstra(danhSachKe, n + 1, k); // hàm dijkstra đã định nghĩa

    long long ketQua = 0;
    for (int i = 1; i <= n; i++) {
        if (khoangCach[i] >= LLONG_MAX / 2) return -1;
        ketQua = max(ketQua, khoangCach[i]);
    }
    return (int)ketQua;
}
```
</details>

<details>
<summary>Lời giải Bài 3 — Cheapest Flights Within K Stops</summary>

**Ý tưởng:** dùng Bellman-Ford giới hạn số lần lặp đúng K+1 lần (thay vì n-1 lần), vì mỗi lần lặp tương ứng với việc cho phép đi thêm 1 cạnh.

```cpp
int findCheapestPrice(int n, vector<vector<int>> &flights, int src, int dst, int K) {
    vector<long long> khoangCach(n, LLONG_MAX / 2);
    khoangCach[src] = 0;

    for (int lanLap = 0; lanLap <= K; lanLap++) {
        vector<long long> khoangCachMoi = khoangCach;
        for (auto &f : flights) {
            int u = f[0], v = f[1], w = f[2];
            if (khoangCach[u] + w < khoangCachMoi[v]) {
                khoangCachMoi[v] = khoangCach[u] + w;
            }
        }
        khoangCach = khoangCachMoi;
    }

    return khoangCach[dst] >= LLONG_MAX / 2 ? -1 : (int)khoangCach[dst];
}
```
> Lưu ý: dùng `khoangCachMoi` riêng biệt (không cập nhật trực tiếp `khoangCach`) để đảm bảo mỗi lần lặp chỉ dùng thông tin từ lần lặp trước, đúng ý nghĩa "tối đa K+1 cạnh".
</details>

<details>
<summary>Lời giải Bài 4 — Path with Maximum Probability</summary>

```cpp
double maxProbability(int n, vector<vector<int>> &edges, vector<double> &succProb, int start, int end) {
    vector<vector<pair<int,double>>> danhSachKe(n);
    for (int i = 0; i < (int)edges.size(); i++) {
        int u = edges[i][0], v = edges[i][1];
        danhSachKe[u].push_back({v, succProb[i]});
        danhSachKe[v].push_back({u, succProb[i]});
    }

    vector<double> xacSuat(n, 0.0);
    xacSuat[start] = 1.0;

    priority_queue<pair<double,int>> hangDoi; // max-heap mặc định, phù hợp vì cần XÁC SUẤT LỚN NHẤT
    hangDoi.push({1.0, start});

    while (!hangDoi.empty()) {
        auto [p, u] = hangDoi.top(); hangDoi.pop();
        if (p < xacSuat[u]) continue;

        for (auto &[v, pCanh] : danhSachKe[u]) {
            if (xacSuat[u] * pCanh > xacSuat[v]) {
                xacSuat[v] = xacSuat[u] * pCanh;
                hangDoi.push({xacSuat[v], v});
            }
        }
    }

    return xacSuat[end];
}
```
</details>

<details>
<summary>Lời giải Bài 5 — High Score</summary>

**Ý tưởng:** đảo dấu trọng số các cạnh (tìm đường đi dài nhất = tìm đường đi ngắn nhất trên đồ thị có trọng số bị đảo dấu), dùng Bellman-Ford; nếu phát hiện chu trình dương "trên đường đi từ nguồn tới đích", trả về thông báo đặc biệt (điểm số vô hạn).
</details>

<details>
<summary>Lời giải Bài 6 — Cycle Finding</summary>

Giống hệt hàm `timChuTrinhAm`/`tim_chu_trinh_am` ở Ví dụ 2, mục 12.2.
</details>

<details>
<summary>Lời giải Bài 7 — Shortest Routes II</summary>

Giống hệt hàm `floydWarshall`/`floyd_warshall` ở Code khung mẫu, mục 12.3 — mỗi truy vấn trả lời trực tiếp bằng `ketQua[a][b]` sau khi đã chạy Floyd-Warshall 1 lần.
</details>

<details>
<summary>Lời giải Bài 8 — Find the City With Smallest Number of Neighbors</summary>

```cpp
int findTheCity(int n, vector<vector<int>> &edges, int distanceThreshold) {
    const long long VO_CUC = LLONG_MAX / 4;
    vector<vector<long long>> kc(n, vector<long long>(n, VO_CUC));
    for (int i = 0; i < n; i++) kc[i][i] = 0;
    for (auto &e : edges) {
        kc[e[0]][e[1]] = min(kc[e[0]][e[1]], (long long)e[2]);
        kc[e[1]][e[0]] = min(kc[e[1]][e[0]], (long long)e[2]);
    }

    kc = floydWarshall(kc, n);

    int thanhPhoTotNhat = -1, soLuongItNhat = n + 1;
    for (int i = 0; i < n; i++) {
        int demSoLuong = 0;
        for (int j = 0; j < n; j++) {
            if (i != j && kc[i][j] <= distanceThreshold) demSoLuong++;
        }
        if (demSoLuong <= soLuongItNhat) {
            soLuongItNhat = demSoLuong;
            thanhPhoTotNhat = i; // duyệt tăng dần i, tự động ưu tiên chỉ số lớn hơn khi bằng nhau
        }
    }
    return thanhPhoTotNhat;
}
```
</details>

<details>
<summary>Lời giải Bài 9 — 0-1 Matrix</summary>

**Phân tích:** vì mọi bước di chuyển (giữa 2 ô liền kề bất kỳ) đều có "trọng số" bằng nhau (1 bước = 1 khoảng cách Manhattan), đây thực chất là bài toán BFS thường (không cần 0-1 BFS, vì không có 2 loại trọng số khác nhau — mọi cạnh đều "nặng" như nhau). Multi-source BFS xuất phát từ tất cả các ô có giá trị 0 sẽ giải quyết bài toán trực tiếp, giống hệt Ví dụ 3, mục 11.2.
</details>

<details>
<summary>Lời giải Bài 10 — Jump Game III với 2 loại chi phí</summary>

```cpp
int minCostJump(vector<int> &a) {
    int n = (int)a.size();
    map<int, vector<int>> nhomGiaTri;
    for (int i = 0; i < n; i++) nhomGiaTri[a[i]].push_back(i);

    vector<vector<pair<int,int>>> danhSachKe(n);
    for (int i = 0; i < n; i++) {
        if (i - 1 >= 0) danhSachKe[i].push_back({i-1, 1});
        if (i + 1 < n) danhSachKe[i].push_back({i+1, 1});
        for (int j : nhomGiaTri[a[i]]) {
            if (j != i) danhSachKe[i].push_back({j, 0});
        }
    }

    vector<long long> kc = zeroOneBFS(danhSachKe, n, 0);
    return (int)kc[n-1];
}
```
</details>

<details>
<summary>Lời giải Bài 11 — Swim in Rising Water</summary>

**Ý tưởng:** dùng Dijkstra biến thể, "khoảng cách" từ nguồn tới 1 ô là **độ cao lớn nhất** phải vượt qua trên đường đi tốt nhất tới ô đó (không phải tổng trọng số).

```cpp
int swimInWater(vector<vector<int>> &luoi) {
    int n = (int)luoi.size();
    vector<vector<int>> thoiGian(n, vector<int>(n, INT_MAX));
    priority_queue<tuple<int,int,int>, vector<tuple<int,int,int>>, greater<>> pq;

    thoiGian[0][0] = luoi[0][0];
    pq.push({luoi[0][0], 0, 0});

    int dR[] = {0,0,1,-1}, dC[] = {1,-1,0,0};

    while (!pq.empty()) {
        auto [t, r, c] = pq.top(); pq.pop();
        if (t > thoiGian[r][c]) continue;
        if (r == n-1 && c == n-1) return t;

        for (int h = 0; h < 4; h++) {
            int nr = r + dR[h], nc = c + dC[h];
            if (nr < 0 || nr >= n || nc < 0 || nc >= n) continue;

            int tMoi = max(t, luoi[nr][nc]); // "chi phí" là độ cao lớn nhất, không phải tổng
            if (tMoi < thoiGian[nr][nc]) {
                thoiGian[nr][nc] = tMoi;
                pq.push({tMoi, nr, nc});
            }
        }
    }
    return -1;
}
```
</details>

<details>
<summary>Lời giải Bài 12 — Path With Minimum Effort</summary>

Giống hệt cấu trúc Bài 11, nhưng thay `tMoi = max(t, luoi[nr][nc])` bằng `tMoi = max(t, abs(luoi[nr][nc] - luoi[r][c]))` (chênh lệch độ cao giữa 2 ô liền kề, thay vì độ cao tuyệt đối).
</details>

<details>
<summary>Lời giải Bài 13 — Đường đi ngắn nhất với k cạnh miễn phí</summary>

Mở rộng trạng thái Dijkstra thành `(khoangCach, dinh, soLanDaDung)`:

```cpp
long long shortestPathWithKFree(vector<vector<pair<int,int>>> &danhSachKe, int n, int nguon, int dich, int k) {
    vector<vector<long long>> kc(n, vector<long long>(k+1, LLONG_MAX / 2));
    kc[nguon][0] = 0;

    priority_queue<tuple<long long,int,int>, vector<tuple<long long,int,int>>, greater<>> pq;
    pq.push({0, nguon, 0});

    while (!pq.empty()) {
        auto [d, u, soLanDung] = pq.top(); pq.pop();
        if (d > kc[u][soLanDung]) continue;

        for (auto &[v, w] : danhSachKe[u]) {
            // Không dùng đặc quyền
            if (d + w < kc[v][soLanDung]) {
                kc[v][soLanDung] = d + w;
                pq.push({d + w, v, soLanDung});
            }
            // Dùng đặc quyền (nếu còn)
            if (soLanDung < k && d < kc[v][soLanDung + 1]) {
                kc[v][soLanDung + 1] = d;
                pq.push({d, v, soLanDung + 1});
            }
        }
    }

    long long ketQua = LLONG_MAX / 2;
    for (int i = 0; i <= k; i++) ketQua = min(ketQua, kc[dich][i]);
    return ketQua;
}
```
</details>

<details>
<summary>Lời giải Bài 14 — Second Minimum Time</summary>

Áp dụng ý tưởng "K đường đi ngắn nhất" của Ví dụ 3, mục 12.1 với K=2, kết hợp thêm việc tính thời gian chờ đèn đỏ (đèn đỏ khi `(thoiGianHienTai / thoiGianChoMoiCanh) % 2 == 1`, cần làm tròn lên bội tiếp theo của `thoiGianChoMoiCanh`).
</details>

<details>
<summary>Lời giải Bài 15 — Chọn thuật toán cho n nhỏ, q lớn</summary>

**Phân tích lựa chọn:** với n ≤ 500 (nhỏ) và q lớn (nhiều truy vấn), thuật toán tối ưu là **Floyd-Warshall** — chạy 1 lần duy nhất O(n³) = 500³ ≈ 1.25×10^8 (chấp nhận được), sau đó mỗi truy vấn trả lời trong O(1) bằng cách tra bảng — tổng độ phức tạp O(n³ + q), tốt hơn nhiều so với chạy Bellman-Ford riêng cho mỗi truy vấn (O(q × n × m)).
</details>

<details>
<summary>Lời giải Bài 16 — Đếm số đường đi ngắn nhất bằng Floyd-Warshall</summary>

```cpp
pair<vector<vector<long long>>, vector<vector<long long>>> floydWarshallDemDuong(vector<vector<long long>> kcBanDau, int n) {
    vector<vector<long long>> kc = kcBanDau;
    vector<vector<long long>> demSoDuong(n, vector<long long>(n, 1)); // ban đầu mỗi cặp có cạnh trực tiếp coi là 1 đường

    for (int k = 0; k < n; k++) {
        for (int i = 0; i < n; i++) {
            for (int j = 0; j < n; j++) {
                if (kc[i][k] + kc[k][j] < kc[i][j]) {
                    kc[i][j] = kc[i][k] + kc[k][j];
                    demSoDuong[i][j] = demSoDuong[i][k] * demSoDuong[k][j];
                } else if (kc[i][k] + kc[k][j] == kc[i][j] && kc[i][j] < LLONG_MAX / 4) {
                    demSoDuong[i][j] += demSoDuong[i][k] * demSoDuong[k][j];
                }
            }
        }
    }

    return make_pair(kc, demSoDuong);
}
```
> **Ghi chú:** cách đếm này có thể đếm trùng lặp trong một số trường hợp đặc biệt (khi có nhiều đỉnh trung gian k khác nhau dẫn tới cùng 1 đường đi) — bài toán đếm số đường đi ngắn nhất chính xác 100% thường được giải tốt hơn bằng Dijkstra biến thể (đếm số cách đạt khoảng cách tối ưu ngay trong quá trình chạy), đây là hướng mở rộng dành cho học sinh muốn tìm hiểu sâu hơn.
</details>
