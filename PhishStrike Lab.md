# [PHISHSTRIKE] – CyberDefenders Writeup
*Writeup được hoàn thành bởi Tuananh – 13.09.2026*

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

- Giải nén ta thu được một file email .eml
<img width="860" height="74" alt="image" src="https://github.com/user-attachments/assets/5e603c7f-8a28-4473-8310-97e0d0c07570" />

- Đây là một kiểu tấn công gọi là Phishing Attack
- Mở email ra để xem có gì trong đó, lưu ý luôn phải thực hiện việc điều tra trong một môi trường ảo để tránh ảnh hưởng đấy máy host. Vậy nên tôi sẽ boot up Virtual Machine (Kali Linux - Host Only Connection)
<img width="880" height="493" alt="image" src="https://github.com/user-attachments/assets/f7442238-1d6a-41ec-8375-37348d03f7f9" />

- Inspect kỹ hơn email trên bằng Sublime Text
<img width="1401" height="786" alt="image" src="https://github.com/user-attachments/assets/f2048e1e-9989-4fab-b02e-4392ec4c4413" />
<img width="1490" height="786" alt="image" src="https://github.com/user-attachments/assets/78642623-3c50-4768-91c1-53c3dc72538d" />

- Khi hover chuột lên trên cái Hyperlink text, thì tôi có thấy một đường dẫn đến địa chỉ sau (đã được defang): **hxxp[://]107[.]175[.]247[.]199/loader/install[.]exe**
- Tôi sẽ thử submit cái địa chỉ IP lên trên VirusTotal để kiểm tra:
<img width="1308" height="785" alt="image" src="https://github.com/user-attachments/assets/1a2755c0-3736-4c6a-ac2c-77ab586f2640" />
<img width="1309" height="784" alt="image" src="https://github.com/user-attachments/assets/33769379-892b-403c-9d81-7aa5f496f8da" />

- Nhận thấy địa chỉ này bị flag là Malicious nên tôi có thể chắc chắn được rằng đây là một vụ tấn công lừa đảo Phishing
### Question 1: Identifying the sender's IP address with specific SPF and DKIM values helps trace the source of the phishing email. What is the sender's IP address that has an SPF value of softfail and a DKIM value of fail?
#### Phân tích:
- Mở trong sublime text, Find các value như là `spf=softfail`
<img width="1488" height="780" alt="image" src="https://github.com/user-attachments/assets/f7ac2b66-e885-48c6-8d0a-bc5821080bc6" />

và `dkim=fail`
<img width="1489" height="792" alt="image" src="https://github.com/user-attachments/assets/861af20b-b6d8-4d51-a0e1-2974e3923f2c" />

- Ta thấy đều liên quan đến IP address của sender là **18[.]208[.]22[.]104**
#### Đáp án: 
<img width="897" height="199" alt="image" src="https://github.com/user-attachments/assets/daf7e443-aee4-4e78-8c22-e899f4cb128d" />

---

### Question 2: Understanding the return path of an email is essential for tracing its origin. What is the return path specified in this email?

#### Phân tích:
- Mở sublime text, ta tiếp tục tìm đến Return-Path value
<img width="1493" height="795" alt="image" src="https://github.com/user-attachments/assets/c5622a46-eed0-488c-ab2b-067f40f75a2f" />

- Ta thấy nó có một return-path là đến địa chỉ: **erikajohana[.]lopez@uptc[.]edu[.]co**
#### Đáp án
<img width="896" height="200" alt="image" src="https://github.com/user-attachments/assets/f12085b8-fd78-4aa1-8b1a-9e306021df2b" />

---

### Question 3: Identifying the source of malware is critical for effective threat mitigation and response. What is the IP address of the server hosting the malicious file related to malware distribution?

#### Phân tích:
- Như cũng đã phân tích ở trên thì chúng ta phát hiện được rằng Hyperlink text có dẫn ta đến địa chỉ có IP mà đang host malicious file là `install.exe` đó là: **107[.]175[.]247[.]199**
#### Đáp án:
<img width="897" height="188" alt="image" src="https://github.com/user-attachments/assets/8b3a41c0-8e65-4d96-9ea3-0aa5c6ceab4f" />

---

### Question 4: Identifying malware that exploits system resources for cryptocurrency mining is critical for prioritizing threat mitigation efforts. The malicious URL can deliver several malware types. Which malware family is responsible for cryptocurrency mining?

#### Phân tích:
- Submit trên URLHaus để kiểm tra thì ta nhận được thông tin sau:
<img width="1749" height="402" alt="image" src="https://github.com/user-attachments/assets/2f17dc36-db70-46ae-9ea8-4dee1ce5f1b1" />

- Sử dụng việc tra cứu OSINT, thì ta biết được đây là một phần mềm độc hại liên quan đến đồng tiền ảo
<img width="1753" height="572" alt="image" src="https://github.com/user-attachments/assets/2a25b3e4-5087-4f68-9c06-7186c5759714" />

#### Đáp án:
<img width="892" height="212" alt="image" src="https://github.com/user-attachments/assets/bd42f5d0-8f46-4bf1-bf71-73e1644b1aa5" />

---

### Question 5: Identifying the specific URLs malware requests is key to disrupting its communication channels and reducing its impact. Based on the previous analysis of the cryptocurrency malware sample, what does this malware request the URL?

#### Phân tích:
- Ta đã biết được Malware là CoinMiner, ta trích xuất được mã SHA256 Hash từ URLHaus: `453fb1c4b3b48361fa8a67dcedf1eaec39449cb5a146a7770c63d1dc0d7562f0`
- Submit lên trên VT và xem report
<img width="1741" height="272" alt="image" src="https://github.com/user-attachments/assets/842badea-27ca-421d-944d-db5676f0fa6c" />

- Vào mục relations, có thấy một bản report về loại malware này trên JoeSandBox (https://www.joesandbox.com/analysis/728137/0/html)
<img width="1750" height="145" alt="image" src="https://github.com/user-attachments/assets/19ddd464-e175-4e94-8170-e0b172a3c829" />

- Đi đến HTTP packets Section để kiểm tra, Ta thấy có hành động request http_method=GET Quantjiitrbv.jpeg
<img width="1591" height="780" alt="image" src="https://github.com/user-attachments/assets/90bf00a9-32f2-466e-8fa6-6911603853f4" />

#### Đáp án:
<img width="897" height="217" alt="image" src="https://github.com/user-attachments/assets/ec68869d-a446-4ff1-a0fd-9765b6aa8a53" />

---

### Question 6: Understanding the registry entries added to the auto-run key by malware is crucial for identifying its persistence mechanisms. Based on the BitRAT malware sample analysis, what is the executable's name in the first value added to the registry auto-run key?

#### Phân tích:
- Dựa vào report trên URLHaus, dựa trên loại BitRAT malware, ta thu được mã SHA256 hash value: `bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539`
- Đưa lên VT để kiểm tra
<img width="1749" height="285" alt="image" src="https://github.com/user-attachments/assets/1b81875b-b136-4c9d-bef2-605eb742b7ec" />

- Check mục Community, ta có bản report trên VMRay (https://www.vmray.com/analyses/_vt/bf7628695c2d/report/overview.html)
<img width="902" height="783" alt="image" src="https://github.com/user-attachments/assets/1889d7a5-3f03-49cc-9986-66c4150adadf" />
<img width="1499" height="444" alt="image" src="https://github.com/user-attachments/assets/21aa0366-4653-4127-8205-16690287085d" />

#### Đáp án:
<img width="900" height="227" alt="image" src="https://github.com/user-attachments/assets/9bf02253-a9da-40fd-a93a-d4998fbe5ec5" />

---

### Question 7: Identifying the SHA-256 hash of files downloaded from a malicious URL is essential for tracking and analyzing malware activity. Based on the BitRAT analysis, what is the SHA-256 hash of the file previously downloaded and added to the autorun keys?

#### Phân tích:
- Dựa vào câu trên thì ta biết được là có mã hash là `bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539`

#### Đáp án:
<img width="880" height="212" alt="image" src="https://github.com/user-attachments/assets/078e9188-5ff9-41b8-ba2a-957e46c5d1a8" />

---

### Question 8: Analyzing the HTTP requests made by malware helps in identifying its communication patterns. What is the URL in the HTTP request used by the loader to retrieve the BitRAT malware?

#### Phân tích:
- Vào mục Behavior của VT
<img width="1311" height="792" alt="image" src="https://github.com/user-attachments/assets/25982ba9-f218-4fa7-91f5-9f44dae749f3" />

- Đi đến mục Network Comms (Network Communications), thì ta thấy có tag 2 HTTP
<img width="1371" height="785" alt="image" src="https://github.com/user-attachments/assets/7416cfbd-83f5-42de-83e3-bae499588e2f" />

- Ở đây có mỗi một HTTP request dùng bởi loader là: hxxp[://]107[.]175[.]247[.]199/loader/server[.]exe

#### Đáp án:
<img width="891" height="194" alt="image" src="https://github.com/user-attachments/assets/e7f5b1a6-dda7-4276-a2c4-5737b6b1a471" />

---

### Question 9: Introducing a delay in malware execution can help evade detection mechanisms. What is the delay (in seconds) caused by the PowerShell command according to the BitRAT analysis?

#### Phân tích:
- Vào mục Behavior trên VT của BitRat
<img width="1307" height="790" alt="image" src="https://github.com/user-attachments/assets/a00c9ba7-8b39-406a-a123-967bc7d814a0" />
<img width="1751" height="259" alt="image" src="https://github.com/user-attachments/assets/185e7e4b-a505-4e12-a64e-ef7111ab6782" />

- Nhâp vào View Matches > Find CommandLine
<img width="603" height="826" alt="image" src="https://github.com/user-attachments/assets/ae6b5a9a-952b-4794-b772-bd86c1993276" />

- Ta thấy có một đoạn cypher bằng Base64, nên ta đưa lên CyberChef để thực hiện decrypt nó
<img width="779" height="833" alt="image" src="https://github.com/user-attachments/assets/34d701b0-563c-48c0-889a-49d8185e5432" />

- Thông điệp thu được là: **`start - sleep Seconds 50`**
- Tìm hiểu thì đây là lệnh có chức năng delay một script hoặc hoạt động trong một khoảng thời gian nhất định
<img width="1792" height="631" alt="image" src="https://github.com/user-attachments/assets/f39bc66b-1606-4397-8a42-2d69d4058603" />

- Vậy thời gian delay là 50 giây
#### Đáp án:
<img width="898" height="198" alt="image" src="https://github.com/user-attachments/assets/a4a351fb-489a-4b8e-91e7-7bea7f45569c" />

---

### Question 10: Tracking the command and control (C2) domains used by malware is essential for detecting and blocking malicious activities. What is the C2 domain used by the BitRAT malware?

#### Phân tích:
- Đọc qua các report có trong mục Community ta thấy có một report của CarlosCabal
<img width="1600" height="204" alt="image" src="https://github.com/user-attachments/assets/b21da339-7f75-4be6-ad1e-aa1abd01e61a" />

- Mở ra và đọc thử report và thấy có mục liên quan đến C2 Communication
<img width="1369" height="382" alt="image" src="https://github.com/user-attachments/assets/05b5d9c2-788b-47a2-bf71-58c6dd6bb7e5" />

- ở Domain **`gh9st[.]mywire[.]org`**

#### Đáp án:
<img width="900" height="207" alt="image" src="https://github.com/user-attachments/assets/e49b43ba-e32c-449d-b6a9-3d1ee4ad5ca4" />

---

### Question 11: Understanding how malware exfiltrates data is essential for detecting and preventing data breaches. According to the AsyncRAT analysis, what is the Telegram Bot ID used by this malware?

#### Phân tích:
- Lên URLHaus để lấy mã SHA256 Hash từ mục có tag ASyncRAT là: **`5ca468704e7ccb8e1b37c0f7595c54df4e2f4035345b6e442e8bd4e11c58f791`**
- Lại lên report trong mục community
<img width="1539" height="829" alt="image" src="https://github.com/user-attachments/assets/f3b08ef8-b6a8-4f20-a17a-bd67daaebd4a" />

- Kiểm tra mục network có thấy telegram với id là **`bot5610920260`**

#### Đáp án:
<img width="898" height="200" alt="image" src="https://github.com/user-attachments/assets/e302e6ff-dda2-4d1d-98fb-b6ecdeb96263" />

---

## 🚩 Indicators of Compromise (IOC)

| Loại | Giá trị | Ghi chú |
|---|---|---|
| **IP của Sender** | 18[.]208[.]22[.]104 | |
| **Return-Path** | erikajohana[.]lopez@uptc[.]edu[.]co | |
| **Malicious IP** | 107[.]175[.]247[.]199 | |
| **Malicious URL** | hxxp[://]107[.]175[.]247[.]199/loader/install[.]exe |https://urlhaus.abuse.ch/url/2381718/ |
| **Malware** | CoinMiner and BitRAT | |
| **Hash Coin Miner** | 453fb1c4b3b48361fa8a67dcedf1eaec39449cb5a146a7770c63d1dc0d7562f0| |
| **Hash BitRat** | bf7628695c2df7a3020034a065397592a1f8850e59f9a448b555bc1c8c639539 | |
| **Tên miền C2** | gh9st[.]mywire[.]org | |
## MITRE ATT&CK

| Tactic | Technique | ID | Ghi chú |
|---|---|---|---|
| **Inital Access (TA0001)** | Phishing | T1566 |
| **Execution (TA0002)** | Command and Scripting Interpreter | T1059.001 | Powershell Scripting
| **Execution (TA0002)** | User Execution | T1204.002 | Malicious link |
| **Persistence (TA0003)** | Registry Run Keys / Startup Folder | T1547.001 | Jzwvix[.]exe được thêm vào Run Key |
| **Defense Evasion (TA0030)** | Obfuscated Files or Information | T1406 | Encode commandline ở dạng Base64 format |
| **Command and Control (TA0011)** | Application Layer Protocol | T1071.001 | HTTP web protocols ở domain gh9st[.]mywire[.]org |
| **Exfiltration (TA0010)** | Exfiltration Over Web Service | T1057.004 | Telegram Bot API |
| **Impact (TA0040)** | Financial Theft | T1657 | Business Email Compromise (BEC) |

---

## Bài học rút ra

- Sau lab này tôi trau dồi thêm về kĩ năng Email/Phishing Analysis
- Hiểu hơn về cách sử dụng các nền tảng như VirusTotal, URLHaus, và các nền tảng Sandboxing
- Hiểu về MITRE ATT&CK mapping

## 🔗 Tài liệu tham khảo

- https://cyberdefenders.org/blueteam-ctf-challenges/phishstrike/
- https://attack.mitre.org/
