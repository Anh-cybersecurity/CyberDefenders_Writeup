# [PHISHSTRIKE] – CyberDefenders Writeup

![CyberDefenders](https://img.shields.io/badge/CyberDefenders-Blue%20Team%20Lab-blue)
![Category](https://img.shields.io/badge/Category-Threat%20Intel-lightgrey)
![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange)

## 📌 Thông tin Challenge

| | |
|---|---|
| **Tên** | PhishStrike Lab |
| **Nền tảng** | [CyberDefenders](https://cyberdefenders.org) |
| **Danh mục** | Threat Intel |
| **Độ khó** | Medium |
| **Link challenge** | [https://cyberdefenders.org/blueteam-ctf-challenges/...](https://cyberdefenders.org/blueteam-ctf-challenges/phishstrike/) |
| **File/artifact cung cấp** | .eml |

## 🎯 Bối cảnh & Mục tiêu

Với tư cách là chuyên gia phân tích an ninh mạng tại một cơ sở giáo dục, bạn nhận được cảnh báo về một email lừa đảo (phishing) nhắm vào đội ngũ giảng viên. Email này có vẻ như đến từ một nguồn tin cậy, thông báo về một giao dịch mua hàng trị giá 625.000 USD và cung cấp đường dẫn để tải xuống hóa đơn.
Nhiệm vụ của bạn là điều tra email này bằng cách sử dụng các công cụ tình báo về mối đe dọa (Threat Intel). Hãy phân tích phần tiêu đề email (header) và kiểm tra đường dẫn để phát hiện nội dung độc hại. Đồng thời, hãy xác định các Dấu hiệu bị xâm nhập (IOC) và ghi lại kết quả điều tra nhằm ngăn chặn nguy cơ gian lận cũng như nâng cao nhận thức của giảng viên về cách nhận diện email lừa đảo.

## 🧰 Công cụ sử dụng

- Email Header Analysis
- URLHaus
- URLScan.io
- VirusTotal
- MalwareBazaar
- VMRay

## 🔍 Quá trình phân tích

