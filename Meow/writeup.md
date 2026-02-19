# Meow

### The Scenario
**Tên nhiệm vụ:** Kiểm tra an ninh thiết bị nội bộ (Internal Audit).
Mục tiêu: Máy chủ có tên mã "Meow".

**Cốt truyện:** Hãy tưởng tượng bạn là một chuyên gia bảo mật được thuê để kiểm tra mạng nội bộ của một công ty vận tải cũ. 

### Process

#### Task follow

> *Task 1:* VM stand for **Virtual Machine**, which is a software simulation a physical computer system but operating as independence computer with OS

> *Task 2:* Tool use to interact with OS by commands, this is **terminal** or shell or console

> *Task 3:* **openvpn** is `service` we use to form HTB's VPN

> *Task 4:* ICMP (Internet Control Message Protocol), a network protocol, **ping** is the tool we use to test the connection to server.

> *Task 5:* Most common tool for enumeration is **nmap**, which is utility for network discovery and security auditing

> *Task 6:* Search for commmon port and service, we will see **telnet**, which service use port 23 or tcp port

> *Task 7:* Câu chuyện đã khác, chúng ta bắt đầu cần recon!!!

Recon IP server với nmap, tập trung vào top 100 port phổ biến trước.

`nmap <TARGET IP> --top-ports 100` 

Kết quả trả về khá rõ ràng như sau:
```
Host is up (0.23s latency).
Not shown: 99 closed tcp ports (reset)
PORT   STATE SERVICE
23/tcp open  telnet
```
Phân tích kết quả cho ta thấy, cổng 23 đang mở, service nó cung cấp là **telnet**, tìm hiểu thêm về telnet, ta biết nó là 1 **Network Protocol** cho phép user login và control thiết bị từ xa. Mô hình chính là **Client-Server** nghĩa là ta làm 1 client connect tới server là thiết bị ở xa.

Tiếp theo, ta thử kết nối với telnet với default config, nếu cơ bản nhớ lại ngữ cảnh thì bạn có hỏi ai trong công ty họ cũng sẽ không biết config đâu, nên thử đại đi!

`telnet <TARGET IP>`

Đợi để kết nối thì sẽ cần nhập gì đó vào dòng `Meow login: ` giờ cần nhập gì vào? Một Username, vậy quay lại câu hỏi 7 sẽ thấy yêu cầu tìm hiểu về username có thể login vào telnet server với password trống, okay giờ username đó là gì? Scan thêm 1 chút với `nmap <TARGET IP> -p 23 -sV -sC` thì nhận được đó là Linux, tức tài khoản cao nhất, tài khoản admin **có thể là** là `root`, ta có thể thử xem sao! Một case khả thi khác là admin!!! Về password, thường là default (root, admin), phổ biến hơn là trống. 

Thực tế thường thấy hơn là 1 bộ wordlist được thu thập và chạy bruteforce từng cái để tìm ra cái nào đúng!

Vào server được với việc chỉ nhập root và Enter, khá ổn! Tiếp theo là task cuối, flag!

> *Flag:* Dùng lệnh đơn giản `ls` ta thấy được flag, và dễ dàng `cat flag.txt`

Dừng ở đây cơ bản là không vui, tôi muốn thử soi thêm!!!

### Extend

Okay tôi đọc thử file quan trọng đó là `/etc/passwd` xem sao, tôi có thể dễ dàng xác định có ai login vào hệ thống nữa không, có tài khoản nào nữa không? Tôi thử đọc và thấy:
```
root@Meow:~# cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd:/usr/sbin/nologin
systemd-timesync:x:102:104:systemd Time Synchronization,,,:/run/systemd:/usr/sbin/nologin
messagebus:x:103:106::/nonexistent:/usr/sbin/nologin
syslog:x:104:110::/home/syslog:/usr/sbin/nologin
_apt:x:105:65534::/nonexistent:/usr/sbin/nologin
tss:x:106:111:TPM software stack,,,:/var/lib/tpm:/bin/false
uuidd:x:107:112::/run/uuidd:/usr/sbin/nologin
tcpdump:x:108:113::/nonexistent:/usr/sbin/nologin
landscape:x:109:115::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:110:1::/var/cache/pollinate:/bin/false
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
lxd:x:998:100::/var/snap/lxd/common/lxd:/bin/false
telnetd:x:112:118::/nonexistent:/usr/sbin/nologin
test-user-0:x:1000:1000::/home/test-user-0:/bin/sh
usbmux:x:111:46:usbmux daemon,,,:/var/lib/usbmux:/usr/sbin/nologin
```

Tôi thấy có 1 cái tên lạ so với các service mà hệ thống cần là `test-user-0` cái này chỉ có thể là mục đích test server lúc setup lên, tôi đoán là thế, trong passwd không có chứa hash của password rồi, tôi tìm hiểu thì hash được chứa trong `/etc/shadow` và chỉ có root mới đọc được. Chúng ta đang ở root, đọc thôi!!! Xem thằng `test-user-0` có gì vui để khám phá không?

```
root@Meow:~# cat /etc/shadow | grep test
test-user-0:U6aMy0wojraho:18795:0:99999:7:::
```

Rõ ràng trong phần password có 1 hash, chúng ta cần crack lại hash này, vì không biết là hash gì nên tôi dùng 1 công cụ có sẵn trong kali là `hash-identifier` cụ thể: `hash-identifier U6aMy0wojraho` thì được kết quả là `Possible Hashs: [+] DES(Unix)`, okay giờ tìm cách crack hash này xem sao nhé!! Dùng thử `john` hoặc `hashcat` đều được!!

Mình dùng `john` và qua vài bước như `john -h`, `john --help`, `john --list=formats`, `john --list=formats | grep des` thì thấy có `descrypt` tức ý tìm ngược lại từ hash của `DES`, okay bước. tiếp theo là password đã hash xong vào file và dùng john tìm, ở đây nên tìm hiểu thêm về *john the ripper* để dễ hiểu về cách dùng hơn!!

```sh
$ echo "test-user-0:U6aMy0wojraho" > hash
$ john --format=descrypt --wordlist=/usr/share/wordlists/rockyou.txt hash
$ john --show hash
test-user-0:

1 password hash cracked, 0 left
```

Okay ở đây theo kết quả mình thấy rõ là password khả năng cao là rỗng, vì sau khi crack thì không có gì phía sau username này cả, mình quyết định telnet vào server 1 lần nữa với username và password là `test-user-0:` và thành công, tới đây server cho mình terminal dùng `sh`, không quen nên mình chuyển lệnh `bash`, giờ mình loanh quanh thêm thì thấy là tài khoản này chỉ là user đầu tiên của hệ thống từ kết quả `1000` qua lệnh `id` tức nó là user đầu được tạo của hệ thống, xem thử ông này có quyền root gì không thì mình dùng `sudo -l` và thấy là `(ALL : ALL) ALL` tức run gì ở quyền admin cũng được, và tìm hiểu lý do thì đơn giản là để test nên config đơn giản để đảm bảo không bị `Permission Denied`, vì rõ ràng bạn có toàn quyền chạy mọi lệnh dù cho đó là sudo nên mình dùng thử `su` để chuyển qua root thử xem sao và được!!! 

Từ ý tưởng này mình có dựng nên 1 lab khá cơ bản với ý tưởng là mấy ông IT vừa setup xong, tạo tài khoản `developer` cho mấy ae Dev vào code và xem thử server hoạt động ổn chưa, pass là rỗng nhé!! Mọi người vào directory `lab` sau đó gõ 2 lệnh `docker build -t meow .` sau khi chạy xong thì gõ `docker run -d -p 2323:22 --name con-meow meow` và sau đó chỉ cần dùng ssh để kết nối tới server với port 2323 tại localhost và username là developer, cụ thể: `ssh developer@localhost -p 2323`. Hãy thử leo thang lên root từ quyền của developer nhé!!! Và đọc `/root/flag.txt`


### Summary

Tổng lại qua machine Meow này, chúng ta đã biết đến **Virtual Machine**, **terminal**, **openvpn**, **ping**, các service ứng với các port tương ứng đặc biệt port 23 cho **Telnet Protocol**, một giao thức cổ không mã hoá để điều kiển thiết bị bằng command-line từ xa. Biết thêm về Scan với **nmap**, hiểu thêm 1 cách thử kiểm tra dò quét xem server có gì *ngon* để vào húp, dùng hash-identifier để xác định hash, thêm kỹ năng phân tích và suy đoán!!! Và hiểu thêm 1 1 kỹ thuật leo thang đặc quyền khá hay ho nếu bạn đùa với lab của tôi!!
