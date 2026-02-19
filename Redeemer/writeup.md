# Redeemer


### The Scenario

**Cốt truyện:** Bug Bounty Hunter and the random server!!

### Process

> *Task 1:* 1 câu hỏi đánh đố, vừa vào đã phải scan rồi!!! Thử thôi!!

Sau khi scan 100 và 1000 port khả năng cao nhất mà không nhận được gì, tôi nghĩ có lẽ cần scan dần tất cả 65535 port!! Tôi quyết định chia nhỏ ra theo 6 lần mỗi lần 10000 cổng để đỡ đợi lâu, bắt đầu với `nmap 10.129.136.187 -p 0-10000` thì được kết quả:
```
Host is up (0.21s latency).
Not shown: 10000 closed tcp ports (reset)
PORT     STATE SERVICE
6379/tcp open  redis

Nmap done: 1 IP address (1 host up) scanned in 127.53 seconds
```

Hmmm okay 1 cổng tcp có service là redis, tạm thời qua task 1, tìm hiểu kỹ hơn về redis xem sao!!

> *Task 2:* Okay thấy **redis** rồi đó

> *Task 3:* Câu này search đỡ thôi, và kết quả là **In-memory Database** ýe 

> *Task 4:* Okay giờ chúng ta cần tìm tool để interact với server redis này!! Searching time!! Okay keyword nên là `Redis interactive tool CLI` và tôi thấy nó là `redis-cli`, chịu khó đi, tốn thời gian lắm!!!

> *Task 5:* Okay giờ vào xem help menu của cái này là gì nào, test `-h` là ra ngay!! À thì ra là -h ko phải là option help mà ra menu error, thôi vẫn tìm được kết quả cho host là `-h` rồi!!

> *Task 6:* Tôi thử connect với server ngay, vừa vào server là cứ gõ help trước xem sao!!! Nhưng không phải help rồi!! Okay tìm thêm trên mạng xem sao!!! Đọc 1 hồi ở [đây](https://redis.io/docs/latest/develop/tools/cli/) thì thấy có info server là xem được thông tin server, có vẻ lệnh info là lệnh đúng!

> *Task 7:* Scan bằng `-sV` hoặc gõ `info server`, đọc thấy redis server là ra ngay!

> *Task 8:* Dùng AI hỏi là chill ngay, nhưng tôi ko thích :)), okay tôi ngồi đọc và serearch về cái này, tôi tin search chắc chắn ra và tôi đọc được cách dùng sơ sơ thêm ở [here] (https://stackoverflow.com/questions/12802726/how-to-list-all-redis-databases), mục tiêu của tôi là list ra xem có cái database nào, chỉ vậy thôi!!! Còn việc select thì tìm thêm sẽ ra lệnh `SELECT`

> *Task 9:* OKay đọc cái ở task 8 xong tôi dùng `info keyspace` phát ra ngay database 0 có 4 keys!! list key ra thì phải `select 0` trước, và sau đó dùng `KEYS *`

> *Task 10:* Như task 9, lỡ nói nhiều quá rồi!

Okay tới đây tìm flag thì chỉ đơn giản là tìm lệnh để lấy được thông tin bằng key flag, và đó là `get` yeahhh!!

### Extend

Thử tìm hiểu quậy thêm cái server này xem sao!!! Loanh quanh 1 hồi thì okay, tôi không tìm được gì cả nên tôi search thử về lỗ hổng của nó thì khá thú vị!!! `RCE` đây là 1 lỗ hổng siêu căng và siêu thú vị để thử!!! You can read **CVE-2025-49844**. Okay letgo now

Tôi dạo 1 vòng và tìm hiểu thì thấy là có 2 nguyên do chính, đầu tiên là lỗ hổng ở redis 1 phần là do việc các distro Debain/Ubuntu có tính năng Dynamic Libary cho Lua, chính vì vậy khi gọi eval trong redis theo code lua hoàn toàn có thể giúp thực thi được code lua, tuy nhiên khi dùng code [này](https://github.com/raminfp/redis_exploit) để thử exploit thì tôi nhận được kết quả là lỗ hổng thật sự của nó là Use-After-Free, đây là 1 kỹ thuật Pwn không đơn giản và tôi không thực hiện được ở hiện tại nên tôi quyết định bỏ qua và sẽ cố tìm hiểu lại sau khi thuật Pwn của bản thân tốt hơn!! Tôi tìm thềm vè các bản vá thì đọc được là cần chặn quyền dùng hàm eval của user là xong, coi như chữa cháy được lỗ hổng này!

### Summary

Qua machine này, toi hiểu thêm về 1 database server, hiểu các lệnh cơ bản, ngoài ra tôi còn học được các tìm CVE và các lỗ hổng từ version, tìm kiến code Exploit và các PoC để hiểu thêm về thực tế khi đối diện hoặc khai thác từ các lỗ hổng mới!! Và hơn hết hiểu và biết thêm về kỹ thuật Use-After-Free siêu hay!!

