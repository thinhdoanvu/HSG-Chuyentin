# HỘI THI TIN HỌC TRẺ TOÀN QUỐC — Năm 2022
## ĐỀ THI VÒNG CHUNG KẾT QUỐC GIA — BẢNG B (THCS)

**Thời gian làm bài:** 150 phút, không kể thời gian phát đề
**Ngày thi:** 20/8/2022

## Tổng quan

| Bài | Tên bài | Điểm |
|---|---|---|
| Bài 1 | Số chính phương | 100 điểm |
| Bài 2 | Giá trị của dãy số | 100 điểm |
| Bài 3 | Tìm số | 100 điểm |

> Hãy lập trình giải các bài toán sau.

---

## Bài 1. Số chính phương (100 điểm)

Cho dãy số nguyên dương $A$ gồm $N$ phần tử $a_1, a_2, \dots, a_N$. Với phần tử $a_i$ của dãy số, số chính phương kết hợp của $a_i$ là số chính phương $s$ lớn nhất thoả mãn: khi thay $a_i$ thành $a_i \times s$ thì bội chung nhỏ nhất của dãy số $A$ không đổi.

**Yêu cầu:** Cho dãy số $A$ và vị trí $i$, tìm số chính phương kết hợp của phần tử $a_i$.

**Dữ liệu:** Vào từ thiết bị vào chuẩn có khuôn dạng:
- Dòng đầu tiên chứa hai số nguyên dương $N$ và $i$ ($i \le N \le 10^6$);
- Dòng thứ hai gồm $N$ số nguyên dương mô tả dãy số $A$, các số có giá trị không quá $10^7$.

**Kết quả:** Ghi ra thiết bị ra chuẩn một dòng gồm một số nguyên duy nhất là phần dư của kết quả tìm được khi chia cho $10^9 + 7$.

**Ràng buộc:**
- Có 50% số test ứng với 50% số điểm của bài thỏa mãn: $N \le 20$; $a_i \le 20$;
- 30% số test khác ứng với 30% số điểm của bài thỏa mãn: $N \le 10^3$; $a_i \le 10^7$;
- 20% số test còn lại ứng với 20% số điểm của bài không có ràng buộc gì thêm.

<details>
<summary><b>Ví dụ (bấm để xem)</b></summary>

| Dữ liệu vào | Kết quả ra |
|---|---|
| `5 2`<br>`8 4 6 18 5` | `9` |

**Giải thích:** Bội chung nhỏ nhất của dãy số $A$ là 360. Thay số 4 thành $4 \times 9 = 36$ thì dãy số là $8, 36, 6, 18, 5$ vẫn có bội chung nhỏ nhất là 360. Vậy kết quả là 9.

</details>

---

## Bài 2. Giá trị của dãy số (100 điểm)

Cho dãy số nguyên $A$ gồm $N$ phần tử $a_1, a_2, \dots, a_N$. Giá trị của dãy số $A$ được tính như sau:

$$v(A) = \sum_{i=1}^{N} |a_i - i|$$

Ví dụ dãy số $A$: 6, 2, 1, 3. Ta có $v(A) = |6-1| + |2-2| + |1-3| + |3-4| = 8$.

Cho *thao tác quay vòng* mảng $A$ được thực hiện như sau: chuyển số ở vị trí cuối cùng lên vị trí đầu tiên:

$$a_1, a_2, \dots, a_{N-1}, a_N \;\to\; a_N, a_1, a_2, \dots, a_{N-1}$$

Ví dụ dãy số $A$: 6, 2, 1, 3, sau một thao tác quay vòng thì dãy số sẽ như sau: 3, 6, 2, 1.

**Yêu cầu:** Cho dãy số $A$, hãy sử dụng thao tác quay vòng một số lần để giá trị của dãy số lớn nhất.

**Dữ liệu:** Vào từ thiết bị vào chuẩn có khuôn dạng:
- Dòng đầu tiên gồm một số nguyên dương $N$ ($N \le 10^5$);
- Dòng thứ hai gồm $N$ số nguyên $a_1, a_2, \dots, a_{N-1}, a_N$ ($|a_i| \le 10^9$; $1 \le i \le N$).

**Kết quả:** Ghi ra thiết bị ra chuẩn gồm một số nguyên là giá trị của dãy số lớn nhất tìm được.

**Ràng buộc:**
- Có 40% số test ứng với 40% số điểm của bài có $N \le 10^3$;
- 30% số test khác ứng với 30% số điểm của bài có $N \le 10^5$; $a_i \le a_{i+1}$ với $1 \le i < N$;
- 30% số test còn lại ứng với 30% số điểm của bài không có ràng buộc gì thêm.

<details>
<summary><b>Ví dụ (bấm để xem)</b></summary>

| Dữ liệu vào | Kết quả ra |
|---|---|
| `4`<br>`6 2 1 3` | `10` |

**Giải thích:**
- Ban đầu, dãy số 6, 2, 1, 3 có giá trị là 8;
- Quay vòng dãy số trên ta có dãy số: 3, 6, 2, 1 có giá trị là 10;
- Quay vòng dãy số trên ta có dãy số: 1, 3, 6, 2 có giá trị là 6;
- Quay vòng dãy số trên ta có dãy số: 2, 1, 3, 6 có giá trị là 4;

Vậy giá trị lớn nhất tìm được là 10.

</details>

---

## Bài 3. Tìm số (100 điểm)

Cho ba số nguyên dương $s, d, m$ ($s \le 10^6$; $d \le 9$; $m \le 9$). Hãy tìm số nguyên dương $N$ nhỏ nhất thỏa mãn các điều kiện:
- Tổng các chữ số của $N$ bằng $s$;
- Các chữ số của $N$ đều xuất hiện chẵn lần và là tập con của tập các chữ số từ 0 đến $d$ ($N$ không được bắt đầu bằng chữ số 0);
- Số $N$ chia hết cho $m$.

**Dữ liệu:** Vào từ thiết bị vào chuẩn gồm một dòng chứa ba số nguyên dương $s, d, m$.

**Kết quả:** Ghi ra thiết bị ra chuẩn một dòng chứa số nguyên dương $N$ tìm được hoặc ghi số $-1$ nếu không tồn tại số $N$ thỏa mãn.

**Ràng buộc:**
- Có 20% số test ứng với 20% số điểm có $m = 1$; $s \le 100$;
- Có 30% số test khác ứng với 30% số điểm có $s \le 100$;
- Có 30% số test khác ứng với 30% số điểm có $s \le 1000$;
- Có 20% số test còn lại ứng với 20% số điểm không có ràng buộc gì thêm.

<details>
<summary><b>Ví dụ (bấm để xem)</b></summary>

| Dữ liệu vào | Kết quả ra |
|---|---|
| `6 5 4` | `1212` |

</details>

---

*Hết*

- *Thí sinh không được sử dụng tài liệu.*
- *Cán bộ coi thi không giải thích gì thêm.*
