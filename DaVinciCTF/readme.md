# _Disk Analysis_

![Screenshot 2025-05-25 212408](https://github.com/user-attachments/assets/23972689-6fe2-4c86-a76b-04a3115f49f0)

Theo thói quen check trong `console history` thì có ngay được flag

![Screenshot 2025-05-25 212846](https://github.com/user-attachments/assets/2b83703c-5b6e-40b8-9546-142bd9c4c14a)

Truy cập ảnh

![Screenshot 2025-05-25 212927](https://github.com/user-attachments/assets/32f519fb-1929-45e2-ae44-c794405dcfc1)

# _Noted_

![Screenshot 2025-05-25 214803](https://github.com/user-attachments/assets/7a694546-578a-4ee3-a264-05c973e55b4b)

Bài cho file .img, theo tiêu đề có ghi là `Windows 11 remembers everything, even when it's not saved.` và tên bài `Noted` nên mình nghĩ đến tính năng  `auto-save` của `Notepad`

Tiến hành check theo path `C:\Users\\AppData\Local\Packages\Microsoft.WindowsNotepad_8wekyb3d8bbwe\LocalState\TabState`

![Screenshot 2025-05-25 215525](https://github.com/user-attachments/assets/d3409c0c-ac9b-4550-99c9-01e92c6a2926)

Save all về và xem từng file là lấy được flag

![Screenshot 2025-05-25 215708](https://github.com/user-attachments/assets/cceb99ed-0f10-43c7-a190-302b54beeb07)

# _Volinux_

![Screenshot 2025-05-25 215856](https://github.com/user-attachments/assets/467760b5-ed3e-4e03-bbb9-7790b9eb5413)

Bài này cho 1 file memdump của linux và có giới thiệu đến 1 tool do chính họ đang phát triển là `volinux`, cốt lõi là nó dựa trên volatility3 nhưng dạng GUI

![Screenshot 2025-05-25 220057](https://github.com/user-attachments/assets/72f4c524-0ff8-454b-a057-26fdfd285b19)

Có được tool này cũng hay tại nó sẽ tự động nhận diện nên sẽ bỏ qua được bước build kernel, symbol... thủ công

![Screenshot 2025-05-25 220402](https://github.com/user-attachments/assets/f9e1bb57-b3b3-43b9-b9cb-d1e2a6dc25e3)

Làm theo hướng dẫn là mình sẽ truy cập được web, drop file vô rồi tiến hành phân tích

![Screenshot 2025-05-25 220931](https://github.com/user-attachments/assets/dfbae8f6-6025-49ed-95f0-421731e3c83e)

>Q1. What is the Linux kernel version of this memory dump?

![Screenshot 2025-05-25 221003](https://github.com/user-attachments/assets/01be901e-ab17-4b3f-83ed-8365a81b9b34)

Cái này tương ứng với `banners.Banners`

`Answer: 4.15.0-112-generic`

>Q2. What was the command used to run website on the machine?

Câu này mình sẽ đi chọn plugin `Bash history` tương ứng với `linux.bash`

![Screenshot 2025-05-25 221249](https://github.com/user-attachments/assets/e08f13c1-d503-4f0f-8b01-bd9ed7ca5d0e)

Có thể thấy được hành vi của attacker là dựng máy chủ PHP, cấp quyền,... sau đó khởi chạy server cục bộ bằng lệnh `php -S localhost:8080`

`Answer: php -S localhost:8080`

>Q4. What is the IP address of the machine?

Chọn plugin `IP address` tương ứng với `linux.ip.Addr`

![Screenshot 2025-05-25 222551](https://github.com/user-attachments/assets/80939141-7407-4d26-b147-551d082611af)

`Answer: 192.168.1.130`

>Q5. What is the MAC address of enp0s8 interface?

Vẫn trong hình trên ta có được ngay địa chỉ MAC

`Answer: 08:00:27:e5:4e:b8`

>Q6. When was the linux session started?

Dùng `Bootime info` tương ứng với `linux.boottime.Boottime`

![Screenshot 2025-05-25 222846](https://github.com/user-attachments/assets/5cec0f24-f0c0-4c42-8a4f-637a15be5a1a)

`Answer: 2025-04-21 16:20:46.752644 UTC`

>Q8. What's the name of parent process of php process?

![Screenshot 2025-05-25 223005](https://github.com/user-attachments/assets/bfdfbc12-3cc1-4e39-b633-b12854f509d2)

PID 1227 là tiến trình cha của `php`, lướt lên xíu để tìm tên

![Screenshot 2025-05-25 223050](https://github.com/user-attachments/assets/73cb09e4-7e86-4eca-b3d7-740c0582e862)

`Answer: upstart`

>Q7. An attacker has uploaded a file to the machine. What is the inode address of the file?

Dùng plugin `List File in Memory` tương đương với `linux.pagecache.Files`

![Screenshot 2025-05-25 223313](https://github.com/user-attachments/assets/6d00e9a7-8373-447b-a6df-e4bea185b215)

Vì mình đã check qua `bash history` thì thấy được một lệnh `php -S localhost:8080` đã được chạy. Khi chạy thì PHP sẽ tự động dùng thư mục hiện tại làm web root, cho phép truy cập hay upload các file .php

Nên mình sẽ tìm theo extension .php thì tìm thấy 1 file với tên `CVEugeneDELACROIX.pdf.php` trông rất khả nghi

`backend_1   | 0x9f351d64d000    /       8:1     392545  0x9f35145b2770  REG     3       0       -rw-r--r--      2025-04-21 16:25:03.071470 UTC 2025-04-21 16:24:52.346110 UTC  2025-04-21 16:24:52.434154 UTC  /var/www/unrestricted-file-upload-exercise-main/victim-service/uploads/CVEugeneDELACROIX.pdf.php`  

Thứ tự các mốc thời gian kịch bản của attacker như sau:

| Thời gian (UTC)             | Hành động                                                                      | Nguồn dữ liệu                |
|-----------------------------|----------------------------------------------------------------------------------|------------------------------|
| 2025-04-21 16:21:39         | Khởi chạy server PHP lần đầu với `php -S localhost:8080`                        | `linux.bash` (PID 2145)      |
| 2025-04-21 16:24:04         | Server PHP được khởi chạy lại (có thể là restart để cập nhật mã nguồn)         | `linux.bash` (PID 2145)      |
| 2025-04-21 16:24:52.346110  | **File `CVEugeneDELACROIX.pdf.php` được upload/lưu (ModificationTime)**        | `linux.pagecache.Files`      |
| 2025-04-21 16:25:03.071470  | **File `CVEugeneDELACROIX.pdf.php` được truy cập/thực thi (AccessTime)**       | `linux.pagecache.Files`      |

`Answer: 0x9f35145b2770`

>Q3. From which path the php command was executed?

Như phân tích ở trên, nếu chỉ nhìn vào `Bash history` thì chưa thể biết chính xác path mà attacker đang đứng. Do server được khởi chạy trước khi `upload` nên path mà attacker đang đứng sẽ lùi lại 1 thư mục so với vị trí của file `CVEugeneDELACROIX.pdf.php`

`Answer: /var/www/unrestricted-file-upload-exercise-main/victim-service`

![Screenshot 2025-05-25 230859](https://github.com/user-attachments/assets/1b025155-d14a-4aa6-b384-5da7f4babc51)

Nối chúng lại theo đúng format và tính md5

![Screenshot 2025-05-25 230957](https://github.com/user-attachments/assets/04f87fee-6fc7-4a6b-a341-9fa78c6f4b75)

`Flag: DVCTF{d0853c14865ee8562983c8faa9896120}`

# _The Breakage_

![image](https://github.com/user-attachments/assets/788fd636-1645-426e-b630-a9de398f1b96)

Bài cho 2 tệp .img của 2 điện thoại android, load vô FTK để extract `data` rồi phân tích

![Screenshot 2025-05-26 080423](https://github.com/user-attachments/assets/9103785a-1c8c-4f90-aeea-ae3123076cdc)

Bài mô tả cần `PIN` của phone1 và `Password` của phone2 để tạo thành flag `DVCTF{PIN:PASSWORD}`, hướng đi là crack bằng hashcat

Bước đầu là phải dựng `hash` của cả 2 máy, đọc blog [này](https://www.pentestpartners.com/security-blog/cracking-android-passwords-a-how-to/) là làm được

- Phone 1:

![Screenshot 2025-05-26 082038](https://github.com/user-attachments/assets/a6d0d5c5-0bda-49ce-9f6b-32dbeef18a04)

Do `salt` lấy ra là số âm nên có 1 số khác biệt, dùng script sau:

```python
salt = -5927124708069638479

# Chuyển số âm thành 8 byte (big endian, bù hai)
salt_bytes = salt.to_bytes(8, byteorder='big', signed=True)

# Chuyển bytes thành chuỗi hex lowercase
salt_hex = salt_bytes.hex()

print(salt_hex)
```

`md5 + salt`: FD55A53B6A21E46C41F82C2FDAE82620:adbe9f7b34117eb1

Tiến hành crack: `hashcat -m 10 FD55A53B6A21E46C41F82C2FDAE82620:adbe9f7b34117eb1 -a 3 ?d?d?d?d?d?d?d?d`

![Screenshot 2025-05-26 081048](https://github.com/user-attachments/assets/39b1b2cf-3fd2-4f7b-bb10-bfc6d23a5533)

=> PIN: `24681379`

- Phone 2: Tương tự Phone 1

`md5 + salt`: 1F2E0A7B9912B973B9FB1BB6AADCDE7C:9fb610735c471ce4

`hashcat -m 10 1F2E0A7B9912B973B9FB1BB6AADCDE7C:9fb610735c471ce4 /home/kali/Documents/rockyou.txt `

![Screenshot 2025-05-26 081211](https://github.com/user-attachments/assets/bf0cbb20-c08b-4a8d-8c5e-215bfe649dc8)

=> PASSWORD: `blueice309`

```FLAG: DVCTF{24681379:blueice309}```
