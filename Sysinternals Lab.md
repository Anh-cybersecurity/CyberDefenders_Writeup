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
| **File/artifact cung cấp** | SysInternal.E01 |
| **Proof of Completion** | https://cyberdefenders.org/blueteam-ctf-challenges/achievements/TuananhCyberSecurity/sysinternals/ |

#CyberDefenders #CyberSecurity #BlueYard #BlueTeam #InfoSec #SOC #SOCAnalyst #DFIR #CCD #CyberDefender

## Bối cảnh & Mục tiêu
Một người dùng tưởng rằng mình đang tải xuống bộ công cụ SysInternals và đã thử mở nó, nhưng các công cụ này không khởi chạy được và trở nên không thể truy cập. Kể từ đó, người dùng nhận thấy hệ thống của mình dần trở nên chậm chạp và kém phản hồi.

## Công cụ sử dụng
- Autopsy/FTK Imager
- VirusTotal
- Amcache Parser

## Quá trình phân tích
- Khi giải nén, tôi thu được một file có đuôi .E01 như sau:
<img width="1919" height="195" alt="image" src="https://github.com/user-attachments/assets/22c00dd3-8e3f-4218-8172-9d3d4de284d0" />

- Tôi import file này vào trong Autopsy để bắt đầu quá trình điều tra
<img width="2559" height="1599" alt="image" src="https://github.com/user-attachments/assets/a2917f5e-a450-48ac-a550-5c0037b92e24" />

- Sau khi đã import xong tôi sẽ bắt đầu thực hiện việc phân tích và điều tra

---

### Question 1. What was the malicious executable file name that the user downloaded?
#### Phân tích:
- Truy cập vào mục ~/Downloads/ folder của User, ở Public tôi thấy trong thư mục Downloads có chứa một executable là **SysInternals.exe**
<img width="1545" height="570" alt="image" src="https://github.com/user-attachments/assets/2f51e1bb-5c1c-4d9f-9044-fe3cc1478dd2" />

- Vậy file malicious là "SysInternals.exe"
#### Đáp án:
<img width="892" height="174" alt="image" src="https://github.com/user-attachments/assets/3e140d14-ca00-4178-a507-b002c07b8584" />

---

### Question 2. When was the last time the malicious executable file was modified?
#### Phân tích: 
- Ở mục đó, ta tiếp tục nhìn mục properties thì thấy **Modified Time** là **2022-11-16 04:18:51 ICT**
<img width="504" height="308" alt="image" src="https://github.com/user-attachments/assets/22cc5177-5b3c-40b5-a6a6-762a9b47a559" />

- Nếu ta convert thời gian đó sang UTC format thì sẽ thu được là **2022-11-15 21:18:51 UTC**
- Vậy nên thời gian cần tìm là theo format "YYYY-MM-DD HH:MM" là **2022-11-15 21:18**
#### Đáp án:
<img width="896" height="169" alt="image" src="https://github.com/user-attachments/assets/6c6ce991-3c0a-4088-8ae2-2eab9382c33c" />

---

### Question 3. What is the SHA1 hash value of the malware?
#### Phân tích:
- Theo như research một chút, thì ta biết được location nó được lưu là: **C:\Windows\AppCompat\Programs\Amcache.hve**
<img width="1918" height="478" alt="image" src="https://github.com/user-attachments/assets/903a3402-4a32-4db4-a8f5-d8a0ed0b0a79" />
<img width="1919" height="417" alt="image" src="https://github.com/user-attachments/assets/d9283817-6c0f-474e-b9e4-fc5d5f313ebe" />

- Ta export file đó về Export sau đó ta sử dụng tools AmcacheParser của Eric Zimmerman

```
AmcacheParser.exe --csv "D:\Documents\CyberDefenders\100-SysInternals\sysinternal\Outputs" -f "D:\Documents\CyberDefenders\100-SysInternals\sysinternal\Export\Amcache.hve"
```

- Ta thu được các file output.csv sau:
<img width="1693" height="763" alt="image" src="https://github.com/user-attachments/assets/c062c0dd-d574-47f9-88d7-5a186189528a" />

- Vì đang điều tra file .exe nên ta sẽ thực hiện vào `20260918235302_Amcache_UnassociatedFileEntries.csv`
<img width="1919" height="1079" alt="image" src="https://github.com/user-attachments/assets/81c4d856-335c-4330-ad59-307cc40ade73" />

- Mã SHA1 hash thu được là: **`fa1002b02fc5551e075ec44bb4ff9cc13d563dcf`**
#### Đáp án:
<img width="897" height="171" alt="image" src="https://github.com/user-attachments/assets/62a99872-c0ce-4fa6-919c-b6e41019bfd8" />

---

### Question 4. Based on the Alibaba vendor, what is the malware's family?
#### Phân tích: 
- Ta thu được mã hash SHA1 của file .exe malicious đó, nên giờ tôi sẽ thực hiện việc submit nó lên trên VirusTotal để kiểm tra về thông tin của malware này
<img width="1918" height="957" alt="image" src="https://github.com/user-attachments/assets/03e72971-c2ca-434c-af0e-2a420f2a5b91" />

- Theo như Alibaba Vendor, thì Malware này thuộc family là: **Rozena**
<img width="1799" height="506" alt="image" src="https://github.com/user-attachments/assets/88c200ae-3d9a-46eb-b7bc-1ffd8864c6ab" />

#### Đáp án: 
<img width="888" height="165" alt="image" src="https://github.com/user-attachments/assets/41e4a363-1a96-4f93-8fb8-85b874f0d3a5" />

---

### Question 5. What is the first mapped domain's Fully Qualified Domain Name (FQDN)?
#### Phân tích:
- Ta kiểm tra trong mục Relations tab
<img width="1796" height="267" alt="image" src="https://github.com/user-attachments/assets/1c2f7bd4-d5a0-4604-a092-0f71679d465b" />

- URL đầu tiên được scanned là: hxxp[://]www[.]malware430[.]com/html/VMwareUpdate[.]exe
- Vậy FQDN đầu tiên bị scanned là www[.]malware430[.]com
#### Đáp án:
<img width="900" height="177" alt="image" src="https://github.com/user-attachments/assets/80f420f5-817f-42ff-843b-99a225d0db94" />

---

### Question 6. The mapped domain is linked to an IP address. What is that IP address?
#### Phân tích: 
- Câu này lúc làm tôi có stuck nên đã đọc hint đầu tiên của bài:
The IP address linked to the domain might be recorded in PowerShell command history or in the system's hosts file.
- Vậy nên tôi đi kiểm tra, Powershell của User
<img width="1619" height="689" alt="image" src="https://github.com/user-attachments/assets/fbfd7eba-603b-43bb-8538-9b6025883132" />

- Đọc dòng lệnh PS có thấy liên quan đến địa chỉ www[.]malware430[.]com

```
Add-Content -Path $env:windir\System32\drivers\etc\hosts -Value "`n192.168.15.10`twww.malware430.com" -Force
Add-Content -Path $env:windir\System32\drivers\etc\hosts -Value "`n192.168.15.10`twww.sysinternals.com" -Force
```

- Vậy nên IP cần tìm là: 192[.]168[.]15[.]10
#### Đáp án:
<img width="891" height="169" alt="image" src="https://github.com/user-attachments/assets/f1e09478-cb81-4800-bc9d-b0657ec873df" />

---

### Question 7. What is the name of the executable dropped by the first-stage executable?
#### Phân tích:
- Vào mục Behavior, Process Created Tree, mình thấy được cái relation như sau
<img width="996" height="296" alt="image" src="https://github.com/user-attachments/assets/52d5ebe5-6645-4bb0-9f18-f5891b05fe40" />

- Trong command shell này có một cái ta cần lưu ý:

```
"C:\Windows\System32\cmd.exe" /C c:\Windows\vmtoolsIO.exe -install && net start VMwareIOHelperService && sc config VMwareIOHelperService start= auto
```

- Lệnh này đã tải vmtoolsIO[.]exe và sau đó có chạy thêm VMwareIOHelperService
#### Đáp án:
<img width="896" height="174" alt="image" src="https://github.com/user-attachments/assets/e617e119-9578-4509-b168-ed908fa10577" />

---

### Question 8. What is the name of the service installed by 2nd stage executable?
#### Phân tích:
- Dựa vào phân tích của câu số 7
- Dịch vụ được chạy bởi 2nd stage executable là VMwareIOHelperService
#### Đáp án:
<img width="908" height="185" alt="image" src="https://github.com/user-attachments/assets/bd3642f4-d501-41d2-ba50-bf9ec1443c90" />

## 🚩 Indicators of Compromise (IOC)
**Malware Family:** Rozena

| Loại IOC | Giá trị | Ghi chú |
|---|---|---|
| File name (Stage 1) | `SysInternals.exe` | File giả danh SysInternals, được tải về `~/Downloads` |
| SHA1 (Stage 1) | `fa1002b02fc5551e075ec44bb4ff9cc13d563dcf` | Lấy từ Amcache.hve (AmcacheParser) |
| Modified Time | `2022-11-15 21:18 UTC` | Thời gian chỉnh sửa cuối của file .exe |
| Malware Family | Rozena | Theo vendor Alibaba trên VirusTotal |
| File name (Stage 2 / dropped) | `vmtoolsIO.exe` | Được drop bởi `SysInternals.exe`, giả danh VMware Tools |
| Service Name | `VMwareIOHelperService` | Service được cài đặt bởi Stage 2, dùng cho persistence |
| Domain (C2/mapped) | `www[.]malware430[.]com` | FQDN đầu tiên bị scan/liên kết với malware |
| Domain (giả mạo) | `www[.]sysinternals[.]com` | Được ghi thêm vào hosts file để đánh lừa |
| IP Address | `192[.]168[.]15[.]10` | Ánh xạ với cả 2 domain trên qua hosts file |
| File Path (Amcache) | `C:\Windows\AppCompat\Programs\Amcache.hve` | Nguồn tra cứu SHA1 |
| Command (Persistence) | `cmd.exe /C c:\Windows\vmtoolsIO.exe -install && net start VMwareIOHelperService && sc config VMwareIOHelperService start= auto` | Lệnh cài đặt & khởi động service độc hại |
| Hosts File Modification | `Add-Content -Path $env:windir\System32\drivers\etc\hosts -Value "192.168.15.10`t`twww.malware430.com" -Force` | PowerShell command ghi vào hosts file |

## MITRE ATT&CK
| Tactic | Technique | ID | Mô tả |
|---|---|---|---|
| Initial Access | Drive-by Compromise / User Execution: Malicious File | T1189 / T1204.002 | User tự tải và chạy `SysInternals.exe` giả mạo |
| Defense Evasion | Masquerading | T1036 | File/service giả danh SysInternals & VMware Tools (`vmtoolsIO.exe`, `VMwareIOHelperService`) |
| Defense Evasion | Impair Defenses: Modify Hosts File | T1564 / T1565 (tham khảo) | Ghi thêm entry vào hosts file để redirect/đánh lừa |
| Execution | Command and Scripting Interpreter: Windows Command Shell / PowerShell | T1059.003 / T1059.001 | Dùng `cmd.exe` và PowerShell để cài đặt, khởi chạy |
| Persistence | Create or Modify System Process: Windows Service | T1543.003 | Cài đặt service `VMwareIOHelperService` với `sc config ... start= auto` |
| Command and Control | Application Layer Protocol / DNS | T1071 | Kết nối tới `www[.]malware430[.]com` |
| Discovery | System Network Configuration Discovery (suy luận) | T1016 | Dựa trên hành vi ghi hosts file, có thể liên quan đến việc kiểm tra/định tuyến mạng |

## Bài học rút ra
- Học được cách sử dụng Autopsy trong quá trình điều tra một file disk image
- Học được về các kỹ thuật khai thác Amcache hve và Powershell history
