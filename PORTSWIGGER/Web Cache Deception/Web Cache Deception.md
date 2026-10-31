## Lab 1: Exploiting path mapping for web cache deception
__Khai thác ánh xạ đường dẫn để lừa đảo bộ đệm web__

Lab yêu cầu tìm _API key_ của `carlos`
![2026-01-12-10-11-41](../../images/2026-01-12-10-11-41.png)

Đăng nhập bằng tài khoản `wiener`\
Có thể thấy được thông tin của tài khoản bao gồm cả `API key`
![2026-01-12-10-46-03](../../images/2026-01-12-10-46-03.png)

Khi thêm vào URL một đường dẫn với tên `test_01.css`, ta thấy web vẫn trả về thông tin một cách bình thường\
Và trong response có thấy Header `X-Cache: miss` tức là request này được server xử lí chứ chưa phải lấy từ _Cache_ và `Cache-Control: max-age=30` tức là thông tin trong Cache chỉ được lưu trong vòng 30 giây
![2026-01-12-10-48-39](../../images/2026-01-12-10-48-39.png)
![2026-01-12-11-00-52](../../images/2026-01-12-11-00-52.png)

Khi gửi lại request đó trong vòng 30 giây, Header `X-Cache` trở thành `hit` --> tức là dữ liệu được lấy từ Cache
![2026-01-12-11-00-22](../../images/2026-01-12-11-00-22.png)

Khi đó dữ liệu đã được lưu vào Cache --> Ta có thể đọc được thông tin người dùng kể cả khi không có được tài khoản của họ bởi nó đã được lưu trữ bởi file `test_01.css` ở trên Cache\
Ở đây ta thử truy cập đường dẫn `/my-account/test_01.css` bằng trình duyệt ẩn danh, ta đã thấy được nội dung thông tin cá nhân của người dùng
![2026-01-12-11-02-30](../../images/2026-01-12-11-02-30.png)

--> Nếu muốn lấy được thông tin của `Carlos` thì cần người dùng phải truy cập vào `/my-account/test_01.css` --> Thông tin người dùng được lưu vào Cache --> Sau đó ta chỉ cần đọc Cache đó để lấy thông tin của `Carlos`

Sang `Server Exploit` (Do ta kiểm soát), viết một site chuyển hướng đến đường dẫn ``/my-account/test_01.css``, sau đó gửi cho nạn nhân
```HTML
<script>document.location="https://0a9000b603d9cd9d805b032700e400a3.web-security-academy.net/my-account/test_01.css"</script>
```
![2026-01-12-11-08-02](../../images/2026-01-12-11-08-02.png)

Sau khi gửi cho nạn nhân, ta truy cập vào đường dẫn `/my-account/test_01.css`\
Ta thấy được nội dung thông tin cá nhân của `carlos`
![2026-01-12-11-09-53](../../images/2026-01-12-11-09-53.png)
![2026-01-12-11-11-34](../../images/2026-01-12-11-11-34.png)

## Lab 2: Exploiting path delimiters for web cache deception
__Khai thác ký tự phân cách đường dẫn để đánh lừa bộ đệm web__

Lab yêu cầu lấy được _API key_ của `carlos`
![2026-01-12-11-40-16](../../images/2026-01-12-11-40-16.png)

Đăng nhập bằng tài khoản của `wiener` 
![2026-01-12-11-42-26](../../images/2026-01-12-11-42-26.png)

Khi thử với request `/my-account/test.js`, web trả về `Not Found`
![2026-01-12-11-43-21](../../images/2026-01-12-11-43-21.png)

Sử dụng [danh sách các kí tự phân cách](https://portswigger.net/web-security/web-cache-deception/wcd-lab-delimiter-list) của Burp để xem kí tự nào có thể dùng làm kí tự phân cách cho server\
Sau khi _Brute Force_, ta thấy có kí tự `;` và `?` trả về `200`
![2026-01-12-11-51-36](../../images/2026-01-12-11-51-36.png)
![2026-01-12-11-52-53](../../images/2026-01-12-11-52-53.png)
![2026-01-12-11-53-10](../../images/2026-01-12-11-53-10.png)

Với kí tự `;`, ta đổi đường dẫn thành `/my-account;test_01.js`, ta thấy `X-Cache: miss`, gửi thêm 1 lần nữa ta thấy `X-Cache: hit`\

![2026-01-12-11-54-32](../../images/2026-01-12-11-54-32.png)
![2026-01-12-11-56-02](../../images/2026-01-12-11-56-02.png)

Khi truy cập bằng đường dẫn đó bằng trình duyệt ẩn danh, ta có thể thấy được nội dung thông tin của người dùng --> Web gốc thì nhận `;` là một dấu phân cách, còn Web Cache không nên đã lưu file đó public 
![2026-01-12-11-56-50](../../images/2026-01-12-11-56-50.png)

--> Dùng URL này gửi cho `carlos`, khi nạn nhân ấn vào sẽ bị lưu Cache
![2026-01-12-12-01-35](../../images/2026-01-12-12-01-35.png)
![2026-01-12-12-02-07](../../images/2026-01-12-12-02-07.png)
![2026-01-12-12-02-34](../../images/2026-01-12-12-02-34.png)

## Lab 3: Exploiting origin server normalization for web cache deception
__Khai thác chuẩn hóa máy chủ gốc để lừa đảo bộ đệm web__

Web yêu cầu lấy thông tin _API key_ của người dùng `carlos`
![2026-01-14-14-24-58](../../images/2026-01-14-14-24-58.png)

Đăng nhập vào tài khoản `wiener`\
Lấy payload `/my-account`
![2026-01-14-14-27-04](../../images/2026-01-14-14-27-04.png)
![2026-01-14-14-28-11](../../images/2026-01-14-14-28-11.png)

Khi gửi một payload kèm thêm dấu `?` làm dấu phân cách `/my-account?test.js`, web vẫn trả về nội dung của người dùng, nhưng lại không lưu cache --> `server cache` cũng coi dấu `?` là dấu phân cách nên đã cắt đoạn `test.js` --> không phù hợp với quy tắc lưu trữ của `server cache` nữa
![2026-01-14-14-30-40](../../images/2026-01-14-14-30-40.png)

Nhưng khi mã hóa dấu `?` thì web lại trả về `404`\
--> Không thể bypass được vì không xử lí được dấu mã hóa
![2026-01-14-14-31-32](../../images/2026-01-14-14-31-32.png)

Khi dùng đường dẫn `/test_01234/../my-account`, web vẫn trả về nội dung như bình thường\
--> Chứng tỏ server gốc có xử lí `../`
![2026-01-14-16-02-00](../../images/2026-01-14-16-02-00.png)

Lấy bất kì một request có đường dẫn thư mục `/resources`, ta thấy web có trả về header `X-Cache: miss`\
![2026-01-14-16-11-12](../../images/2026-01-14-16-11-12.png)

Ta thử thêm `../my-account` vào sau, ta thấy web vẫn trả về nội dung như bình thường và có thêm cả header `X-Cache: miss`
![2026-01-14-16-26-17](../../images/2026-01-14-16-26-17.png)

Khi encode dấu `/` ta thấy web vẫn trả về `302`
![2026-01-14-16-28-05](../../images/2026-01-14-16-28-05.png)

Khi truy cập bằng `/resources/..%2fmy-account`, ta thấy web trả về thông tin của người dùng nhưng không trả thằng về `/my-account` mà vẫn ở URL đó --> Chứng tỏ server cache đã lưu thông tin của người dùng bằng khóa `/resources/..%2fmy-account`
![2026-01-14-16-29-09](../../images/2026-01-14-16-29-09.png)

Thử truy cập đường đẫn `/resources/..%2fmy-account` bằng trình duyệt ẩn danh, ta thấy thông tin của người dùng vẫn bị lộ do đã bị _server cache_ lưu public
![2026-01-14-16-32-31](../../images/2026-01-14-16-32-31.png)

Sang server ta kiểm soát, viết một HTML nhằm chuyển hướng nạn nhân đến ``/resources/..%2fmy-account``
```HTML
<script>document.location="https://0ad3005a042c32b189de9c47009e00c8.web-security-academy.net/resources/..%2fmy-account"</script>
```
![2026-01-14-16-34-07](../../images/2026-01-14-16-34-07.png)

Gửi cho nạn nhân, khi này server cache đã lưu thông tin của nạn nhân (trong vòng 30s)\
Ta truy cập đường dẫn `/resources/..%2fmy-account` và thấy được thông tin của nạn nhân
![2026-01-14-16-36-13](../../images/2026-01-14-16-36-13.png)

Header `Cache-Control: max-age=30` tức thông tin chỉ được lưu ở cache trong vòng 30s, nên trong 30s đó chỉ lưu được thông tin của người dùng đã ấn vào trước, không ghi đè được\
Nên cần phải thêm một phần `?different_text` để mỗi lần cache có thể lưu một thông tin khác nhau, không cần phải đợi 30s
![2026-01-14-16-41-32](../../images/2026-01-14-16-41-32.png)
![2026-01-14-16-42-02](../../images/2026-01-14-16-42-02.png)
![2026-01-14-16-42-26](../../images/2026-01-14-16-42-26.png)

## Lab 4: Exploiting cache server normalization for web cache deception
__Khai thác chuẩn hóa máy chủ cache để lừa đảo bộ đệm web__

Web yêu cầu lấy thông tin _API key_ của người dùng `carlos`
![2026-01-14-16-58-00](../../images/2026-01-14-16-58-00.png)

Thử sử dụng `../`, ta thấy server gốc không decode dẫn đến web trả về `404`
![2026-01-14-17-01-36](../../images/2026-01-14-17-01-36.png)

Ta thử tìm kiếm xem kí tự nào được server gốc chấp nhận làm kí tự phân đoạn\
![2026-01-14-17-12-58](../../images/2026-01-14-17-12-58.png)

Kết quả nhận được là những kí tự `# / ? %23 %2F %3F` là những kí tự phân đoạn
![2026-01-14-17-12-21](../../images/2026-01-14-17-12-21.png)

Sử dụng `discorver content` để xem được cấu trúc của thư mục
![2026-01-14-17-13-25](../../images/2026-01-14-17-13-25.png)

