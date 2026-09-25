# [BENIGN] – TryHackMe Writeup

*Writeup được hoàn thành bởi Tuananh – 25.09.2026*

[![Platform](https://img.shields.io/badge/TryHackMe-red)]() [![Category](https://img.shields.io/badge/Category-Security%20Operation-blue)]() [![Difficulty](https://img.shields.io/badge/Difficulty-Medium-orange)]()

## 📌 Thông tin Challenge

|                            |                                                   |
| -------------------------- | ------------------------------------------------- |
| **Tên**                    | Benign                                            |
| **Nền tảng**               | TryHackMe                                         |
| **Danh mục**               | Security Operation                                |
| **Độ khó**                 | Medium                                            |
| **Link challenge**         | [Link](https://tryhackme.com/room/benign)         |
| **File/artifact cung cấp** | Splunk Server                                     |
| Proof of Completion | https://tryhackme.com/room/benign?utm_campaign=social_share&utm_medium=social&utm_content=room&utm_source=copy&sharerId=69674d0f961178b92bcde478 |

## 🎯 Bối cảnh

Một trong các hệ thống IDS của khách hàng đã phát hiện hoạt động thực thi tiến trình có dấu hiệu đáng ngờ, cho thấy một máy trạm thuộc bộ phận Nhân sự (HR) đã bị xâm nhập. Việc thực thi một số công cụ liên quan đến thu thập thông tin mạng và tác vụ được lập lịch (scheduled tasks) đã xác nhận nghi vấn này. Do hạn chế về nguồn lực, chúng tôi chỉ có thể trích xuất các bản ghi thực thi tiến trình có Event ID: 4688 và nạp chúng vào Splunk (sử dụng chỉ mục `win_eventlogs`) để phục vụ quá trình điều tra chuyên sâu.

Thông tin về Mạng

Hệ thống mạng được chia thành ba phân đoạn logic. Thông tin này sẽ hỗ trợ cho quá trình điều tra.

Bộ phận CNTT (IT): James, Moin, Katrina

Bộ phận Nhân sự (HR): Haroon, Chris, Diana

Bộ phận Tiếp thị (Marketing): Bell, Amelia, Deepak
## 🧰 Công cụ sử dụng

- Splunk

## 🔍 Quá trình phân tích

- Tôi bắt đầu bằng việc activate và mở lab, thì họ có dẫn tôi đến splunk server
<img width="1919" height="1078" alt="image" src="https://github.com/user-attachments/assets/a05e1824-026c-407d-abb8-fb8ac497cce4" />

- Tôi sẽ thực hiện list ra các sourcetype để tôi cần để tâm đến trong cuộc điều tra này
<img width="1918" height="304" alt="image" src="https://github.com/user-attachments/assets/1fc68323-19b2-493a-bb95-43c5e6bb85f5" />

- Tôi thấy dã có tổng cộng 13959 events được thu thập ở trên nền tảng Splunk liên quan đến window events logs

### Question 1: How many logs are ingested from the month of March, 2022?

#### Phân tích:
- Đầu tiên tôi giới hạn thời gian về tháng 3/2022
<img width="646" height="148" alt="image" src="https://github.com/user-attachments/assets/8cd18b4e-19f5-480f-9ced-39de2c9d9265" />

- Sau khi filter, thì hiện ra tổng cộng có 13959 events vào khoảng thời điểm tháng 3
<img width="1917" height="170" alt="image" src="https://github.com/user-attachments/assets/c278aa5d-59f9-4dd4-ba49-54b896d2e66e" />

#### Đáp án:
<img width="1251" height="106" alt="image" src="https://github.com/user-attachments/assets/7b3365b5-940e-45c0-b23f-351dfc428b7f" />

---

### Question 2: Imposter Alert: There seems to be an imposter account observed in the logs, what is the name of that user?

#### Phân tích:
- Tôi thực hiện list ra các tài khoản đã được thu thập trên nền tảng Splunk, thực hiện câu lệnh truy vấn như sau:
```
index=*
| stats count by UserName
```
<img width="1919" height="646" alt="image" src="https://github.com/user-attachments/assets/bd8fca4d-66dd-448e-ae35-f50846c55319" />

- Ở đây tôi nhận thấy được rằng có một tài khoản rất giống với Amelia thuộc bộ phận Marketing là **Amel1a**, nên tôi nghĩ đây là một tài khoản mạo danh

#### Đáp án:
<img width="1264" height="108" alt="image" src="https://github.com/user-attachments/assets/e3d260a1-215f-4d93-b243-cde51d2d49f0" />

---

### Question 3: Which user from the HR department was observed to be running scheduled tasks?

#### Phân tích
- Vì đây là Scheduled Task, nên hoạt động này sẽ được ghi lại với eventID là 4688 nên tôi sẽ kiểm tra eventID đó với câu lệnh truy vấn như sau:
  ```
  index=* source="win_event_logs.json" schtasks
  ```
<img width="1913" height="956" alt="image" src="https://github.com/user-attachments/assets/47bf7455-6a44-4496-96ac-3e260a272851" />

- Có 87 events liên quan, sau đó tôi lọc theo nhân viên thuộc phòng HR (Bộ phận Nhân sự (HR): Haroon, Chris, Diana)
<img width="601" height="331" alt="image" src="https://github.com/user-attachments/assets/1072dd8b-a499-4680-aff6-17045da24ec3" />

- Tôi thấy có mỗi **Chris** là người thực hiện 
  
#### Đáp án:
<img width="1258" height="100" alt="image" src="https://github.com/user-attachments/assets/a826ac89-bd5c-487c-826e-c639e8a7a4d1" />

--- 

### Question 4: Which user from the HR department executed a system process (LOLBIN) to download a payload from a file-sharing host.

#### Phân tích:
- Dựa vào nguồn tài liệu trên https://lolbas-project.github.io/
- Tôi kiểm tra bằng các lọc ra phòng HR ra trước
```
index=* source="win_event_logs.json" HostName="*HR*"
```
<img width="1916" height="955" alt="image" src="https://github.com/user-attachments/assets/cb72a778-7bb8-40dd-9ab2-02705a9cc6e4" />

- Sau đó tôi kiểm tra tiếp đến các giá trị đặc biệt của field CommandLine
<img width="596" height="492" alt="image" src="https://github.com/user-attachments/assets/7729e462-05a8-47a4-a1dc-60d561039291" />
<img width="1919" height="880" alt="image" src="https://github.com/user-attachments/assets/4aa6cef6-407b-4e0c-9a47-44dfd458322f" />

- Trong các câu lệnh được lọc ra thì có câu lệnh đầu tiên với tần suất xuất hiện ít, và có sử dụng certutil.exe để download payload
- Kiểm tra kỹ event đó và phát hiện được user thực hiện là Haroon
<img width="1919" height="953" alt="image" src="https://github.com/user-attachments/assets/eab290e2-dcdb-4da4-a30e-ef22b9378ed6" />

#### Đáp án:
<img width="1261" height="106" alt="image" src="https://github.com/user-attachments/assets/03a2d90d-7299-4c91-8e60-661087a088cd" />

---

### Question 5: To bypass the security controls, which system process (lolbin) was used to download a payload from the internet?

#### Phân tích:
- Dựa vào câu hỏi trên, ta biết được haroon đã sử dụng **certutil.exe** để tải payload trên mạng về

#### Đáp án:
<img width="1280" height="107" alt="image" src="https://github.com/user-attachments/assets/2f41b7ad-6542-4bb3-8bdc-7f7c05f8b9eb" />

---

### Question 6: What was the date that this binary was executed by the infected host? format (YYYY-MM-DD)

#### Phân tích:
- Dựa vào câu hỏi số 4, ta cũng biết được ngày mà binary được thực thi là vào 2022-03-04

#### Đáp án:
<img width="1273" height="125" alt="image" src="https://github.com/user-attachments/assets/4eaf6b5d-26fa-47cd-8227-07164c2e5467" />

---

### Question 7: Which third-party site was accessed to download the malicious payload?

#### Phân tích:
- Dựa vào câu hỏi số 4, ta biết được bên thứ 3 để tải về là: controlc[.]com

#### Đáp án:
<img width="1271" height="113" alt="image" src="https://github.com/user-attachments/assets/f82e5dba-5b0d-4b33-bdd9-990cb2478910" />

---

### Question 8: What is the name of the file that was saved on the host machine from the C2 server during the post-exploitation phase?

#### Phân tích: 
- Dựa vào câu số 4, ta biết được file mà được save sau post-exploitation là benign[.]exe

#### Đáp án:
<img width="1280" height="94" alt="image" src="https://github.com/user-attachments/assets/236e9eca-7762-4b7b-8688-dfd666b0bfc8" />

### Question 9: The suspicious file downloaded from the C2 server contained malicious content with the pattern THM{..........}; what is that pattern?

#### Phân tích:
- Ta thực hiện lên website đó để kiểm tra xem là có malicious content (hxxps[://]controlc[.]com/e4d11035)
<img width="1917" height="1075" alt="image" src="https://github.com/user-attachments/assets/5b7a983f-2fdc-4669-841a-b48e7e07fd16" />

- Ta đã thu thập được flag với nội dung là: **THM{KJ&*H^B0}**

#### Đáp án:
<img width="1253" height="113" alt="image" src="https://github.com/user-attachments/assets/c1d05c5b-18db-41df-8b02-4c1a82c8dc91" />

---

### Question 10: What is the URL that the infected host connected to?

#### Phân tích:
- Dựa vào câu hỏi trên, ta biết được website đầy đủ là (hxxps[://]controlc[.]com/e4d11035)

#### Đáp án:
<img width="1267" height="113" alt="image" src="https://github.com/user-attachments/assets/640ed358-6bc7-429e-9fae-84bec4d11126" />


## 🚩 Indicators of Compromise (IOC)

| Loại | Giá trị | Ghi chú |
| --- | --- | --- |
| **User bị lợi dụng** | `haroon` | Tài khoản HR thực thi LOLBIN để tải payload |
| **User bất thường (imposter)** | `Amel1a` | Không nằm trong danh sách 9 user hợp lệ (IT/HR/Marketing) — dấu hiệu tài khoản giả mạo/anomaly |
| **LOLBIN sử dụng** | `certutil.exe` | Lợi dụng binary hợp lệ của Windows để tải file, né tránh AV/whitelist |
| **File tải về** | `benign.exe` | Payload lưu trên host sau khi tải |
| **Nguồn tải (third-party)** | `controlc.com` | Site chia sẻ file/paste hợp lệ bị lợi dụng làm nơi host payload |
| **URL cụ thể** | `https://controlc.com/e4d11035` | Nơi host `benign.exe` |
| **Thời điểm thực thi** | `2022-03-04` | Ngày binary được chạy trên host nhiễm |
| **Event ID nguồn log** | `4688` (Process Creation) | Toàn bộ log điều tra lấy từ event này |
| **Pattern nội dung file** | `THM{KJ&*H^B0}` | Chuỗi phát hiện trong nội dung file tải về, dùng để xác nhận payload |

## MITRE ATT&CK

| Tactic | Technique | ID | Ghi chú |
| --- | --- | --- | --- |
| **Execution (TA0002)** | System Binary Proxy Execution | T1218 | Lạm dụng `certutil.exe` (LOLBIN) để thực thi tác vụ tải file |
| **Command and Control (TA0011)** | Ingress Tool Transfer | T1105 | `certutil.exe -urlcache -split -f` tải `benign.exe` từ controlc.com |
| **Command and Control (TA0011)** | Web Service | T1102 | Dùng site chia sẻ file hợp pháp (controlc.com) làm điểm host payload, né domain reputation filtering |
| **Persistence (TA0003)** | Scheduled Task | T1053.005 | User `chris.fort` (HR) bị ghi nhận chạy scheduled task bất thường |
| **Discovery (TA0007)** | System Network Configuration Discovery | T1016 | Theo mô tả scenario: "tools related to network information gathering" được thực thi (chưa xác nhận command cụ thể) |
| **Defense Evasion (TA0005)** | Masquerading | T1036 | Tài khoản `Amel1a` gần giống `Amelia` (Marketing) — nghi vấn giả mạo username |

## Bài học rút ra

- Splunk command `top`/`rare` trên field `UserName` hoặc `CommandLine` rất hiệu quả để phát hiện outlier trong tập user/command ít biến động — cách nhanh nhất để bắt "imposter account" mà không cần biết trước danh sách user hợp lệ.
- Lạm dụng LOLBIN (`certutil.exe`) để tải payload là kỹ thuật phổ biến vì binary này được ký hợp lệ và thường không bị AV chặn — cần theo dõi command-line argument (`-urlcache`, `-split`, `-f`) chứ không chỉ theo tên process.
- Kẻ tấn công dùng site chia sẻ file hợp pháp (controlc.com) thay vì domain lạ để tránh bị chặn theo reputation — nhắc nhở rằng whitelist domain không đủ, cần kiểm tra hành vi (process cha/con, argument).

## 🔗 Tài liệu tham khảo

- [TryHackMe — Benign](https://tryhackme.com/room/benign)
- <https://attack.mitre.org/>
- [LOLBAS — Certutil.exe](https://lolbas-project.github.io/lolbas/Binaries/Certutil/)
