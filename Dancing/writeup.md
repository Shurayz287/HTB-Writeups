# Dancing

### The Scenario
**Nhiệm vụ:** Kiểm tra server.  
**Mục tiêu:** Máy chủ là "Dancing".   
**Cốt truyện:** Trong lúc quét dọn dải mạng nội bộ cũ kỹ của công ty, bạn phát hiện một địa chỉ IP lạ đang (active). Không có trong tài liệu bàn giao. Không ai biết nó là gì. Các sếp IT già thì bảo: "À, hình như là con máy Windows đời Tống dùng để share tài liệu gì đó hồi xưa, tưởng vứt rồi chứ?"  

### The Process

> *Task 1:* SMB stand for **Server Message Block**, which is a network communication protocol used by Windows!! Learn more [here](https://www.geeksforgeeks.org/computer-networks/network-file-system-nfs-vs-server-message-block-smb/) 

> *Task 2:* SMB service use port **445**

> *Task 3:* Scan port 455 by nmap, the name of service is **microsoft-ds** 

> *Task 4:* You should search **smbclient** to know more, i read [here] (https://hackviser.com/tactics/tools/smbclient), so the list option is `-L`

> *Task 5:* Okay ở đây chúng ta đã cần phải thử kết nối với server để xem xem server hiện tại có mấy shares ở đó, khi scan chúng ta đã thấy có 1 port về SMB và biết về smbclient, tiếp theo hãy thử kết nối với server SMB này bằng chính smbclient nhé!!!   

Đọc qua hướng dẫn dùng smbclient thì `-L` chính là list ra các shares đang có trong hệ thống! `smbclient -L <TARGET IP>`, ta dễ dàng thấy 4 share đang có:
```
Password for [WORKGROUP\kali]:

        Sharename       Type      Comment
        ---------       ----      -------
        ADMIN$          Disk      Remote Admin
        C$              Disk      Default share
        IPC$            IPC       Remote IPC
        WorkShares      Disk      
Reconnecting with SMB1 for workgroup listing.
```
Vì không biết password là gì nên cứ thử enter, để pass rỗng và kết quả khá khả quan!!

> *Task 6:* Okay tiếp theo là 1 câu mà chúng ta lại cần phải tìm hiểu về default của 1 hệ thống là gì, tìm 1 hồi không ra, đọc lại task thì thấy là ShareName có thể access, và chỉ có 4 cái, ShareName thì do user đặt nên khả năng cao không có default, nãy giờ tìm cũng không có. Okay test thử cho từng cái ShareName xem sao!!! Vẫn dùng smbclient thôi!

Test thử commnad vào lần lượt 4 Shares thì thấy có 2 Share chúng ta có thể vào là `IPC$` và `WorkShares` 

```sh
$ smbclient //<TARGET IP>/ADMIN$
Password for [WORKGROUP\kali]:
tree connect failed: NT_STATUS_ACCESS_DENIED
$ smbclient //<TARGET IP>/C$    
Password for [WORKGROUP\kali]:
tree connect failed: NT_STATUS_ACCESS_DENIED
$ smbclient //<TARGET IP>/IPC$
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> 
$ smbclient //<TARGET IP>/WorkShares
Password for [WORKGROUP\kali]:
Try "help" to get a list of possible commands.
smb: \> 
```

Okay tới đây chúng ta đã dễ dàng có đáp án cho Task 6 rồi!

> *Task 7:* Command có thể tải file về từ SMB shell cũng khá giống với đa số các server khác là `get`

Ở đây, dễ dàng các bạn đọc flag khi loanh quanh ở `WorkShares` với mấy lệnh cơ bản như cd, ls và get!! 

OKay tới đây mà dừng lại thì quá chán, tôi thử quét qua 1000 port và phát hiện ra ngoài 445 thì còn có port khác đang sống, tôi sẽ thử tìm hiểu thêm về chúng!!

```sh
Host is up (0.21s latency).
Not shown: 996 closed tcp ports (reset)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
5985/tcp open  http          Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
|_http-title: Not Found
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows
```

Hmmm okay, ngoài 445 thì còn tận 3 cổng để tham quan, ngoài ra cũng có thể thử vào các Share khác của SMB để xem có gì thú vị không!!! Sau 1 hồi vòng vo đủ kiểu thử thì tôi không nhận lại được gì, yeahh nhưng biết thêm được nhiều thứ!!!

### Summary 

Qua Machine này thì yeah tôi biết về SMB, port 445, học cách thử sai, cách phân tích bề mặt tấn công và thử nghiệm để xem có thật sự có lỗ hổng trên phần port đó hay không, hiểu biết thêm về các port dò được như sau:
- *Port 135 (msrpc, Microsoft Remote Procedure Call)*: đây là 1 cổng cho phép các user tìm đến server Windows tìm ra chính xác các dịch vụ ngầm đang có trên Windows đang ở cổng nào! Khi server có cấu hình cũ, tức là chúng ta có thể dùng Null Session để vào hỏi được danh sách các User hay Group, đây là 1 vector tấn công tìm năng ở port này!
- *Port 139 (netbios-ssn, NetBIOS Session Service)*: Giao thức cho phép các ứng dụng giao tiếp qua LAN bằng tên của máy tính, đặc biệt được sử dụng cho chia sẽ file. Khi LAN cũ, hacker có thẻ bắt các gói tin đang truyền đi để đánh cắp thông tin. Thường ở hiện tại thì nó đã bị vô hiệu hoá vì kém bảo mật!
- *Port 5985 (Win Remote Management by HTTP)*: Chính xác là SSH của windows, Sysadmin có thể dùng nó để mở PowerShell và điều khiển máy tính trong mạng, và để tấn công bằng vector này bắt buộc phải là 1 user của admin hoặc crack được password của admin! Khi chỉ là anonymous, thì khá khó để tấn công được! 

Chung quy lại thì không phải cứ có port là có hổng, phải tìm hiểu, thử và xem xét để biết thật sự đâu mới có vấn đề!!! Việc `WorkShares` để trống cho ai cũng vào được là 1 lọai rủi ro với công ty!!!
