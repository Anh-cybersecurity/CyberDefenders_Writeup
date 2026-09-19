## PowerShell Command History

### Nguồn dữ liệu chính: `ConsoleHost_history.txt`
Khi user gõ lệnh trong PowerShell console (dùng module `PSReadLine`), lịch sử được lưu tại:
```
C:\Users\<username>\AppData\Roaming\Microsoft\Windows\PowerShell\PSReadLine\ConsoleHost_history.txt
```

### Đặc điểm quan trọng
- File này là **plaintext**, không mã hoá → dễ đọc trực tiếp, nhưng cũng dễ bị attacker xoá/sửa nếu biết đường dẫn
- Chỉ ghi lại lệnh gõ trong **PowerShell console** tương tác (`powershell.exe`, `pwsh.exe`) — không áp dụng cho script chạy qua `.ps1` không tương tác
- Không lưu **output** của lệnh, chỉ lưu **input command**
- Giới hạn số dòng lưu trữ (mặc định ~4096 dòng, có thể bị ghi đè lệnh cũ)

### Nguồn bổ sung mạnh hơn (nên biết để điều tra sâu hơn)
| Nguồn | Event ID / Vị trí | Ưu điểm |
|---|---|---|
| **Script Block Logging** | Event ID `4104` — `Microsoft-Windows-PowerShell/Operational` | Ghi lại toàn bộ nội dung script/block thực thi, kể cả script ẩn/obfuscated (sau khi deobfuscate) |
| **Module Logging** | Event ID `4103` | Ghi lại pipeline execution details |
| **PowerShell Transcription** | File `.txt` tại vị trí cấu hình | Ghi lại toàn bộ session bao gồm cả output, nếu policy được bật |
| **Windows Event Log 400/403** | `Windows PowerShell.evtx` | Ghi lại engine start/stop, thấy version PowerShell được dùng |

### Vì sao artifact này hữu ích trong case Sysinternals Lab
Attacker dùng `Add-Content -Path $env:windir\...\hosts` để ghi IP giả — đây là thao tác **tương tác trực tiếp qua console** (không phải script chạy ẩn), nên gần như chắc chắn bị PSReadLine ghi lại trong `ConsoleHost_history.txt`, kể cả khi Script Block Logging chưa được bật trên máy nạn nhân.

---

## Tóm tắt: Khi nào dùng artifact nào?

| Câu hỏi điều tra | Artifact nên check trước |
|---|---|
| Tìm hash file (kể cả đã bị xóa) | Amcache.hve |
| Tìm đường dẫn/thời gian file xuất hiện lần đầu | Amcache.hve |
| Tìm lệnh user gõ thủ công (domain, IP, config thay đổi) | PowerShell `ConsoleHost_history.txt` |
| Tìm script bị obfuscate/chạy ẩn | Event ID 4104 (Script Block Logging) |
| Xác nhận file đã **thực thi** (không chỉ xuất hiện) | Prefetch, EVTX 4688, ShimCache — kết hợp với Amcache |
