# [SYSINTERNALS] – CyberDefenders Writeup

![CyberDefenders](https://img.shields.io/badge/CyberDefenders-Blue%20Team%20Lab-blue)
![Category](https://img.shields.io/badge/Category-Endpoint%20Forensics-lightgrey)
![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange)

## 📌 Thông tin Challenge

| | |
|---|---|
| **Tên** | Sysinternals Lab Lab |
| **Nền tảng** | [CyberDefenders](https://cyberdefenders.org) |
| **Danh mục** | Endpoint Forensics |
| **Độ khó** | Medium |
| **Link challenge** | [Link](https://cyberdefenders.org/blueteam-ctf-challenges/sysinternals/) |
| **File/artifact cung cấp** |  |
| **Proof of Completion** |  |

#CyberDefenders #CyberSecurity #BlueYard #BlueTeam #InfoSec #SOC #SOCAnalyst #DFIR #CCD #CyberDefender

## Bối cảnh & Mục tiêu
Một người dùng tưởng rằng mình đang tải xuống bộ công cụ SysInternals và đã thử mở nó, nhưng các công cụ này không khởi chạy được và trở nên không thể truy cập. Kể từ đó, người dùng nhận thấy hệ thống của mình dần trở nên chậm chạp và kém phản hồi.

## Công cụ sử dụng
- Autopsy
- VirusTotal
- Registry Explorer
- Event Log Explorer

## Quá trình phân tích
- Khi giải nén, tôi thu được một file có đuôi .E01 như sau:
<img width="1919" height="195" alt="image" src="https://github.com/user-attachments/assets/22c00dd3-8e3f-4218-8172-9d3d4de284d0" />

- Tôi import file này vào trong Autopsy để bắt đầu quá trình điều tra
<img width="2559" height="1599" alt="image" src="https://github.com/user-attachments/assets/a2917f5e-a450-48ac-a550-5c0037b92e24" />

