## Amcache.hve

### Nó là gì?
`Amcache.hve` là một registry hive (không phải phần của registry chính) nằm tại:
```
C:\Windows\AppCompat\Programs\Amcache.hve
```
Được Windows dùng để hỗ trợ tính năng **Application Compatibility** — theo dõi các ứng dụng/executable đã từng xuất hiện trên hệ thống, kể cả khi chúng chưa từng được chạy (chỉ cần được ghi vào đĩa và index bởi hệ thống).

### Vì sao hữu ích trong forensics?
Amcache lưu lại nhiều metadata quý giá cho mỗi file thực thi, ngay cả khi file đã bị xóa khỏi đĩa:
- **SHA1 hash** của file
- Đường dẫn đầy đủ tới file
- Thời gian file được tạo/compile (`PE Timestamp`)
- Kích thước file
- Publisher/Company name (nếu có)
- Volume ID / thông tin ổ đĩa chứa file

### Cấu trúc chính (khi parse bằng AmcacheParser)
| Mục | Mô tả |
|---|---|
| `Unassociated File Entries` | File .exe/.dll không được liên kết với "Program" cụ thể — malware thường nằm ở đây vì hiếm khi có installer đăng ký chính thức |
| `Inventory Application File` | File thuộc về một ứng dụng đã cài đặt |
| `Inventory Application` | Danh sách chương trình đã cài |
| `Program Entries` | Chương trình cài qua MSI/installer |

### Điểm mạnh & giới hạn
| Điểm mạnh | Giới hạn |
|---|---|
| Vẫn giữ hash/metadata dù file đã bị xóa | Không chứng minh **chắc chắn** file đã được thực thi — cần đối chiếu với Prefetch, EVTX 4688, ShimCache |
| Rất tốt để lấy SHA1 phục vụ tra cứu VirusTotal | Thời gian ghi vào Amcache có thể lệch so với thời gian thực thi thực tế |
| Khó bị xóa thủ công (hive bị lock khi hệ thống chạy) | Không lưu **command-line arguments** |

### Công cụ parse
```
AmcacheParser.exe --csv <output_dir> -f <path_to_Amcache.hve>
```
