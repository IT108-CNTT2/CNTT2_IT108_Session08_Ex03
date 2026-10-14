# Báo cáo xử lý thống kê sự cố qua Pipeline PowerShell

## Phần 1: Phân tích

### 1. Bản chất Pipeline trong PowerShell
- **Khác biệt cốt lõi:** Khác với CMD hay Bash truyền dòng chữ thuần túy (`text`) qua dấu `|` (phải dùng công cụ ngoài như `wc -l` để đếm), **PowerShell truyền nguyên một Đối tượng (Object)** có cấu trúc. 
- Nhờ vậy, mỗi kết quả lọc qua các bước đều mang theo các thuộc tính riêng (như nội dung dòng, số dòng, tên tệp). Ta chỉ cần bọc toàn bộ câu lệnh trong ngoặc đơn rồi chấm thuộc tính **`.Count`** là lấy ngay được con số tổng chính xác.

### 2. Sơ đồ luồng dữ liệu qua Pipeline
```text
[Đọc tệp] Get-Content server_log.log 
    │ (Truyền nội dung tệp dưới dạng object)
    ▼
[Lọc từ khóa] Select-String "TIMEOUT" 
    │ (Lọc ra các dòng chứa TIMEOUT, giữ lại các dòng nhầm lẫn như NOT TIMEOUT)
    ▼
[Loại trừ bẫy] Where-Object { $_ -notmatch "NOT TIMEOUT" } 
    │ (Loại bỏ hoàn toàn các dòng chứa NOT TIMEOUT)
    ▼
[Đếm kết quả] (.Count) ➔ Trả về con số tổng duy nhất
