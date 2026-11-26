## 1. Server

| server_id | Host Mininet | IP |
|---|---|---|
| 1 | h3 | 10.0.0.3 |
| 2 | h4 | 10.0.0.4 |
| 3 | h5 | 10.0.0.5 |

IP ảo của dịch vụ: 10.0.0.100

## 2. Hàm chọn server

Mỗi giải thuật là một lớp có phương thức:

    select_server(state) -> int

- Trả về 1, 2 hoặc 3 (server_id).
- Nếu không còn server nào khỏe, trả về None.
- Giải thuật được giữ biến nội bộ (ví dụ Round Robin nhớ lượt trước).

## 3. state

Một dict chứa danh sách 3 server:

    state = {
      "servers": [
        {"id": 1, "active_conn": 4, "weight": 3, "healthy": True},
        {"id": 2, "active_conn": 2, "weight": 2, "healthy": True},
        {"id": 3, "active_conn": 0, "weight": 1, "healthy": False}
      ]
    }

- active_conn: số kết nối đang hoạt động (số nguyên >= 0).
- weight: trọng số (số nguyên > 0), chỉ WRR dùng.
- healthy: True nếu server nhận được yêu cầu, False nếu lỗi.
- Giải thuật phải bỏ qua server có healthy = False.

## 4. File kết quả CSV

Mỗi dòng là một yêu cầu HTTP. Dòng đầu là tên cột:

    timestamp,algo,scenario,run_id,server_id,status,rt_ms

| Cột | Ý nghĩa | Ví dụ |
|---|---|---|
| timestamp | Lúc gửi yêu cầu, giây Unix | 1764200000.123 |
| algo | rr, wrr hoặc lc | lc |
| scenario | K1 đến K5 | K3 |
| run_id | Lần chạy, 1 đến 5 | 2 |
| server_id | Server xử lý (1, 2, 3), để trống nếu lỗi | 2 |
| status | Mã HTTP (200, 503...) hoặc timeout | 200 |
| rt_ms | Thời gian phản hồi, mili giây | 143.7 |
Tên file: `results/{scenario}_{algo}_run{run_id}.csv`
Ví dụ: `results/K3_lc_run2.csv`