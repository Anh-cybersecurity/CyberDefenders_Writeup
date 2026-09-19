# [UNIT42] – HackTheBox Writeup

*Writeup được hoàn thành bởi Tuananh – 19.09.2026*

![HackTheBox](https://img.shields.io/badge/HackTheBox-Blue%20Team%20Lab-blue) ![Category](https://img.shields.io/badge/Category-Log%20Analysis-green) ![Difficulty](https://img.shields.io/badge/Difficulty-Very%20Easy-green)

## 📌 Thông tin Challenge

|                            |                                                                |
| -------------------------- | -------------------------------------------------------------- |
| **Tên**                    | Unit 42                                                        |
| **Nền tảng**               | [HackTheBox](https://hackthebox.com)                           |
| **Danh mục**               |  Log Analysis                                                  |
| **Độ khó**                 |  Very Easy                                                     |
| **Link challenge**         | [Link](https://app.hackthebox.com/sherlocks/Unit42?tab=play_sherlock) |
| **File/artifact cung cấp** | .evtx                                                          |
| **Proof of Completion**    | https://labs.hackthebox.com/achievement/sherlock/3364356/632   |

## 🎯 Bối cảnh & Mục tiêu

Trong bài tập Sherlock này, bạn sẽ làm quen với các bản ghi Sysmon và những EventID hữu ích để nhận diện cũng như phân tích các hoạt động độc hại trên hệ thống Windows. Unit42 của Palo Alto gần đây đã thực hiện nghiên cứu về một chiến dịch tấn công liên quan đến UltraVNC, trong đó kẻ tấn công sử dụng phiên bản UltraVNC đã bị cài cắm mã độc (backdoor) để duy trì quyền truy cập vào hệ thống. Bài thực hành này được xây dựng dựa trên chiến dịch đó và sẽ hướng dẫn người tham gia tìm hiểu về giai đoạn xâm nhập ban đầu.

## 🧰 Công cụ sử dụng

- Window Event Viewer

## 🔍 Quá trình phân tích

- Tôi giải nén file .zip được cung cấp và mở ra được một file .evtx
<img width="1685" height="195" alt="image" src="https://github.com/user-attachments/assets/97eb4315-3d4d-465a-85f3-399e016b3e7f" />

- Mở file .evtx và thực hiện quá trình phân tích và điều tra
<img width="2559" height="1599" alt="image" src="https://github.com/user-attachments/assets/5b813659-fe59-4665-bdae-2e63bbb60e2b" />

- Thống kê các EventID có trong event viewer thu thập được
  - Event ID 1: Tạo/Thực thi tiến trình. Bao gồm đường dẫn tiến trình, đường dẫn tiến trình cha và các tham số dòng lệnh.
  - Event ID 2: Thay đổi thời gian tạo tệp. Bao gồm tệp thực hiện thay đổi, tệp chịu sự thay đổi, dấu thời gian đã bị sửa đổi và dấu thời gian ban đầu.
  - Event ID 3: Kết nối mạng. Bao gồm tiến trình thực hiện kết nối, địa chỉ IP đích và cổng (port).
  - Event ID 5: Kết thúc tiến trình. Bao gồm tên của tiến trình đã bị chấm dứt hoặc tự kết thúc.
  - Event ID 11: Tệp được tạo. Bao gồm tiến trình tạo tệp, tệp được tạo và đường dẫn đầy đủ của tệp đó.
  - Event ID 22: Truy vấn DNS. Bao gồm tiến trình truy vấn tên miền, tên miền đích và các địa chỉ IP được phân giải từ tên miền đó.
 
---

### Question 1: How many Event logs are there with Event ID 11?

#### Phân tích:
- Tôi filter chỉ eventID = 11
<img width="806" height="821" alt="image" src="https://github.com/user-attachments/assets/7a2ab7c6-fe15-4279-a335-83f2b4719f66" />

- Sau khi filter xong, hiện ra 56 events liên quan đến eventID 11
<img width="1722" height="354" alt="image" src="https://github.com/user-attachments/assets/119c0055-9a92-495c-8006-a4e1dbf1adc7" />

#### Đáp án:
<img width="1363" height="194" alt="image" src="https://github.com/user-attachments/assets/f5286f0d-a123-409b-a732-29e1dae330db" />

---

### Question 2: Whenever a process is created in memory, an event with Event ID 1 is recorded with details such as command line, hashes, process path, parent process path, etc. This information is very useful for an analyst because it allows us to see all programs executed on a system, which means we can spot any malicious processes being executed. What is the malicious process that infected the victim's system?

#### Phân tích:
- Filter chỉ EventID 1
<img width="809" height="825" alt="image" src="https://github.com/user-attachments/assets/ab43814d-78ca-4554-adf1-c475e09ce66e" />

- Sau khi filter xong, hiện ra 6 events liên quan đến event ID 1
<img width="1975" height="349" alt="image" src="https://github.com/user-attachments/assets/7f1bdd72-646f-4634-a2fb-0c1dd237fd43" />

- Tôi kiểm tra các event trong số được filter, thì phát hiện một dòng lệnh rất đáng nghi
<img width="1986" height="1071" alt="image" src="https://github.com/user-attachments/assets/3cb2ac4b-4a97-49ef-aad3-19c4f2462325" />

**- Image đáng nghi:** **C:\Windows\SysWOW64\msiexec[.]exe** vì theo như một chút research trên mạng thì tôi biết được msiexec.exe phải nằm trong thư mục **C:\Windows\System32\msiexec[.]exe**
<img width="1916" height="529" alt="image" src="https://github.com/user-attachments/assets/ba4fabf2-9b88-4f67-8f86-73f3aa74451b" />

- Các câu lệnh đó bao gồm:
```
"C:\Windows\system32\msiexec.exe" /i "C:\Users\CyberJunkie\AppData\Roaming\Photo and Fax Vn\Photo and vn 1.1.2\install\F97891C\main1.msi" AI_SETUPEXEPATH=C:\Users\CyberJunkie\Downloads\Preventivo24.02.14.exe.exe SETUPEXEDIR=C:\Users\CyberJunkie\Downloads\ EXE_CMD_LINE="/exenoupdates  /forcecleanup  /wintime 1707880560  " AI_EUIMSI=""
```
- Trong đó có một file rất đáng nghi là **Preventivo24.02.14[.]exe[.]exe** của User **CyberJunkie**
- Vậy nên Malicious process cần tìm là : **"C:\Users\CyberJunkie\Downloads\Preventivo24.02.14.exe.exe" **
#### Đáp án:
<img width="1364" height="240" alt="image" src="https://github.com/user-attachments/assets/63b9605d-2da1-4579-bb15-a26ead40cf5c" />

---

### Question 3. Which Cloud drive was used to distribute the malware?

#### Phân tích:
- Vì Cloud drive được sử dụng nên ta sẽ truy cập EventID 22
- Filter EventID 22
<img width="1970" height="304" alt="image" src="https://github.com/user-attachments/assets/85adbc0a-a0a2-46c3-bb47-f34ea1a6ba7a" />

- Tôi thấy được là người dùng đầu tiên truy cập vào FireFox > Dropbox.com
<img width="1907" height="258" alt="image" src="https://github.com/user-attachments/assets/9de0c69e-d746-46d5-a83f-e0ec9f7004e2" />
<img width="1885" height="255" alt="image" src="https://github.com/user-attachments/assets/7cfc67b8-650b-49e8-b85c-c7ba0cb95c23" />
<img width="1909" height="252" alt="image" src="https://github.com/user-attachments/assets/adca43ab-c906-4dc6-9505-790f38a72f65" />

- Từ đó nền tảng cloud drive được sử dụng để phân tán malware này là **Dropbox**
#### Đáp án:
<img width="1367" height="196" alt="image" src="https://github.com/user-attachments/assets/0df37653-6c6a-49d9-a161-37256fa29ce1" />

---

### Question 4. For many of the files it wrote to disk, the initial malicious file used a defense evasion technique called Time Stomping, where the file creation date is changed to make it appear older and blend in with other files. What was the timestamp changed to for the PDF file?

#### Phân tích:
- Biết được Malcious Actor đã sử dụng kỹ thuật Time Stomping (Thay đổi mác thời gian), nên tôi sẽ thực hiện tham khảo EventID 2 để detect việc file bị thay đổi mác thời gian
<img width="1977" height="728" alt="image" src="https://github.com/user-attachments/assets/12709c12-9217-45b2-ac43-bbcc140ee22a" />

- Locate được file .pdf, tôi thực hiện xem properties và phát hiện thời gian bị thay đổi là 2024-01-14 08:10:06
#### Đáp án:
<img width="1360" height="212" alt="image" src="https://github.com/user-attachments/assets/93c9a1a4-2da0-4617-aa75-27fd1391dad9" />

---

### Question 5. The malicious file dropped a few files on disk. Where was "once.cmd" created on disk? Please answer with the full path along with the filename.

#### Phân tích:
- Biết được EventID 11 thể hiện cho việc tạo tệp, nên tôi sẽ tra cứu EventID 11 xem có gì không
<img width="2555" height="501" alt="image" src="https://github.com/user-attachments/assets/000be094-4ba7-4372-ae2c-b136867e2d51" />

- Đi tìm once.cmd thì thấy được địa chỉ của nó là: **: C:\Users\CyberJunkie\AppData\Roaming\Photo and Fax Vn\Photo and vn 1.1.2\install\F97891C\WindowsVolume\Games\once.cmd**
<img width="1968" height="1113" alt="image" src="https://github.com/user-attachments/assets/f7b0be5e-a568-4457-9b2b-86601f98b9b2" />

#### Đáp án: 
<img width="1381" height="199" alt="image" src="https://github.com/user-attachments/assets/dfe34bd7-342d-4cb8-bb9a-3a393c508177" />

---

### Question 6. The malicious file attempted to reach a dummy domain, most likely to check the internet connection status. What domain name did it try to connect to?

#### Phân tích: 
- Tôi filter EventID 22 để kiểm tra các kết nối của nó
- Có thấy một event đề cập đến malicious executable file dẫn đến một domain không bình thường: www[.]example[.]com
<img width="1864" height="261" alt="image" src="https://github.com/user-attachments/assets/34161351-82ce-4238-90b5-60e88d945731" />

#### Đáp án:
<img width="1371" height="183" alt="image" src="https://github.com/user-attachments/assets/0b8caa68-26b6-4a49-be58-279b7c689878" />

---

### Question 7. Which IP address did the malicious process try to reach out to?

#### Phân tích:
- Tôi check EventID 3 vì đây là nơi để ghi chép lại log về việc có network connection
- Nó chỉ hiện ra một event duy nhất và có liên quan đến malicious executable file nên tôi sẽ điều tra kĩ event này
<img width="1866" height="460" alt="image" src="https://github.com/user-attachments/assets/5df36ca7-6618-42b6-b3d2-1c143529c3ee" />

- Tôi extract được Destination IP: 93[.]184[.]216[.]34 và DestinationPort là 80
- Vậy IP cần tìm là: 93[.]184[.]216[.]34
- Tuy không trong câu hỏi tuy nhiên tôi vẫn muốn tìm hiểu liệu đây có phải là một maclicious ip address không nên tôi đã đưa lên VirusTotal để kiểm tra
<img width="1917" height="632" alt="image" src="https://github.com/user-attachments/assets/cbab9459-4605-4c58-bfc0-207db76f0456" />

- ip này đã bị flaged
#### Đáp án:
<img width="1367" height="196" alt="image" src="https://github.com/user-attachments/assets/9943752f-566a-458b-ae67-26cece363464" />

---

### Question 8. The malicious process terminated itself after infecting the PC with a backdoored variant of UltraVNC. When did the process terminate itself?

#### Phân tích:
- Kiểm tra eventID 5 vì đây là nơi ghi lại quá trình Process Termination
<img width="1855" height="201" alt="image" src="https://github.com/user-attachments/assets/31b7386e-f325-4f63-b423-ce7b0397e30d" />

- Thời gian của Termination là: 2024-02-14 03:41:58
#### Đáp án:
<img width="1366" height="199" alt="image" src="https://github.com/user-attachments/assets/cfcad828-12cc-4023-8bb3-cf5388c00563" />

## 🚩 Indicators of Compromise (IOC)

| Loại | Giá trị | Ghi chú |
|---|---|---|
| **IP** | [x.x.x.x] | |
| **Domain** | [domain[.]com] | |
| **File name** | [tên file] | |
| **SHA1 / SHA256** | [hash] | |
| **URL** | [hxxp://...] | |
| **Service/Registry** | [tên service/key] | |

## MITRE ATT&CK

| Tactic | Technique | ID | Ghi chú |
|---|---|---|---|
| **[Tactic] (TAxxxx)** | [Technique name] | [Txxxx.xxx] | [Ghi chú ngắn gọn] |
| | | | |

## Bài học rút ra

- [Bài học kỹ thuật/nhận thức rút ra sau khi hoàn thành lab]
- [Kỹ năng/công cụ mới học được]
- [Điều cần lưu ý cho lần điều tra sau]

## 🔗 Tài liệu tham khảo

- [Link challenge gốc]
- [Link tài liệu bổ sung nếu có — MITRE ATT&CK, blog phân tích malware, v.v.]
