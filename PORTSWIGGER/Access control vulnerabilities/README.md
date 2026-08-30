## **Lab: Unprotected admin functionality**
<img src="./images/2026-08-30-16-33-17.png" style="display: block; margin: 0 auto;">

>>Web có admin panel không được bảo vệ

Ta thực hiện truy cập `robots.txt` và nhận được 

<img src="./images/2026-08-30-16-34-10.png" style="display: block; margin: 0 auto;">

Truy cập `/administrator-panel` và nhận được admin panel mà không cần bất kì bước xác thực nào

<img src="./images/2026-08-30-16-35-13.png" style="display: block; margin: 0 auto;">

## **Lab: Unprotected admin functionality with unpredictable URL**
<img src="./images/2026-08-30-16-35-52.png" style="display: block; margin: 0 auto;">

>>Lab có admin panel không có cơ chế xác thực và có đường dẫn không đoán được nhưng nó bị lộ ở đâu đó trên trang web

Ta thực hiện xem source code của web thì thấy có một đoạn mã JS tiết lộ cơ chế điều hướng đến admin panel và đường dẫn của nó

<img src="./images/2026-08-30-16-40-50.png" style="display: block; margin: 0 auto;">

Thực hiện truy cập đường dẫn `/admin-dtrf7r` và nhận được admin panel

<img src="./images/2026-08-30-16-41-52.png" style="display: block; margin: 0 auto;">

## **Lab: User role controlled by request parameter**
<img src="./images/2026-08-30-16-43-37.png" style="display: block; margin: 0 auto;">

>>Lab nói rằng admin panel ở đường dẫn `/admin` và nó sử dụng một loại cookie để xác thực

<img src="./images/2026-08-30-16-46-04.png" style="display: block; margin: 0 auto;">

Thực hiện đăng nhập và quan sát ta thấy có một cookie là `Admin` có giá trị là `false`, ta đổi thành `true` rồi truy cập `/admin`

<img src="./images/2026-08-30-16-47-24.png" style="display: block; margin: 0 auto;">

## **Lab: User role can be modified in user profile**
<img src="./images/2026-08-30-16-48-13.png" style="display: block; margin: 0 auto;">

>>Web có thể truy cập admin panel khi `roleid` có giá trị là `2` 

Khi đổi mật khẩu ta quan sát request ở Burp, ta thấy có trường `roleid`

<img src="./images/2026-08-30-16-56-29.png" style="display: block; margin: 0 auto;">

Vì vậy ta thực hiện thêm một tham số ở trong data `roleid`: `2`

<img src="./images/2026-08-30-16-57-34.png" style="display: block; margin: 0 auto;">

Kết quả web đã thực hiện thay đổi `roleid` thành 2, thực hiện truy cập đường dẫn `/admin` và ta nhận được **admin panel**

<img src="./images/2026-08-30-16-58-38.png" style="display: block; margin: 0 auto;">

## **Lab: User ID controlled by request parameter**
<img src="./images/2026-08-30-17-00-12.png" style="display: block; margin: 0 auto;">

>>Lab nói rằng có tồn tại lỗ hổng leo thang đặc quyền ngang

Đăng nhập và quan sát profile của cá nhận có một tham số `id`

<img src="./images/2026-08-30-17-01-15.png" style="display: block; margin: 0 auto;">

Thực hiện thay đổi tham số này thành `carlos`, ta nhận được profile của carlos, chứng tỏ rằng web không thực hiện kiểm tra session của người người dùng có đúng là của người đó không dẫn đến việc leo thang đặc quyền theo chiều ngang

## **Lab: User ID controlled by request parameter, with unpredictable user IDs**
<img src="./images/2026-08-30-17-04-24.png" style="display: block; margin: 0 auto;">

>>Lab yêu cầu tìm GUID của người dùng rồi leo thang đặc quyền chiều ngang

Thực hiện đăng nhập và quan sát ta thấy lần này id của người đùng là một chuỗi không có ý nghĩa

<img src="./images/2026-08-30-17-06-18.png" style="display: block; margin: 0 auto;">

Ta thực hiện truy cập một bài viết của `carlos`, ta thấy rằng id của người dùng bị lộ tại đây

<img src="./images/2026-08-30-17-13-15.png" style="display: block; margin: 0 auto;">

Ta thực hiện quay lại `/my-account` và thực hiện thay đổi id thành id của `carlos`

<img src="./images/2026-08-30-17-15-05.png" style="display: block; margin: 0 auto;">

## **Lab: User ID controlled by request parameter with data leakage in redirect**
<img src="./images/2026-08-30-17-18-26.png" style="display: block; margin: 0 auto;">

>>Lab nói rằng thông tin nhạy cảm bị lộ trong thân của `redirect response`

Thực hiện thay đổi tham số `id` thành `carlos` và ta đã nhận được profile của người dùng `carlos` 

<img src="./images/2026-08-30-17-23-24.png" style="display: block; margin: 0 auto;">

## **Lab: User ID controlled by request parameter with password disclosure**

Thực hiện đăng nhập người dùng `wiener`, sau đó đổi tham số id thành `adminitrator` và ta nhận được profile kèm mật khẩu của `administrator`

<img src="./images/2026-08-30-17-27-08.png" style="display: block; margin: 0 auto;">

Thực hiện đăng nhập vào người dùng `administrator` và xóa `carlos`

<img src="./images/2026-08-30-17-29-22.png" style="display: block; margin: 0 auto;">

## **Lab: Insecure direct object references**
<img src="./images/2026-08-30-17-30-22.png" style="display: block; margin: 0 auto;">

>>Lab nói rằng tin nhắn của người dùng được trữ trên file hệ thống và nó tồn tại lỗ hổng có thể đọc được trái phép 

Ta thực hiện đăng nhập và chat với vai trò của người dùng `wiener`, web có chức năng tải về tin nhắn bằng cách nhấn nút **View Transcript**\

<img src="./images/2026-08-30-17-35-50.png" style="display: block; margin: 0 auto;">

Tải thử 1 lần và ta quan sát được request trong Burp

<img src="./images/2026-08-30-17-37-52.png" style="display: block; margin: 0 auto;">

Web tải bằng đường dẫn `/download-transcript/2.txt`, ta thực hiện thay đổi file `2.txt` thành `1.txt`

<img src="./images/2026-08-30-17-39-05.png" style="display: block; margin: 0 auto;">

Và kết quả nhận được đoạn tin nhắn của người dùng carlos bao gồm mật khẩu của tài khoản này

## **Lab: URL-based access control can be circumvented**
<img src="./images/2026-08-30-17-42-07.png" style="display: block; margin: 0 auto;">

>>Lab nói rằng admin panel không được xác thực, nhưng nó chỉ chấp nhận truy cập đến từ nội bộ, backend của web có triển khai Header `X-Original-URL`
>>`X-Original-URL`: có chức năng ghi đề vào sau khi request đi qua được Firewall

Khi vừa mới vào ta đã nhận được admin panel nhưng khi truy cập thì nó bị chặn 

<img src="./images/2026-08-30-17-47-50.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-30-17-48-04.png" style="display: block; margin: 0 auto;">

Ta thực hiện ghi đè URL bằng cách sử dụng `X-Original-URL: /admin` và kết quả là đã bypass được firewall, bây giờ phải thực hiện xóa người dùng **carlos** bằng **Intercept** 

<img src="./images/2026-08-30-17-49-12.png" style="display: block; margin: 0 auto;">

Đến request này, ta cần thực hiện đổi từ `GET` thành `POST` để có thể tránh được lỗi `"Missing parameter 'username'"`

<img src="./images/2026-08-30-17-56-49.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-30-17-55-48.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-30-17-57-09.png" style="display: block; margin: 0 auto;">

## **Lab: Method-based access control can be circumvented**
<img src="./images/2026-08-30-19-42-36.png" style="display: block; margin: 0 auto;">

>>Lab yêu cầu dùng người dùng `wiener:perter` nâng quyền `admin`

Đăng nhập vào người dùng admin, thực hiện một thao tác nâng quyền của người dùng và quan sát request trong Burp

<img src="./images/2026-08-30-19-48-30.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-30-19-50-21.png" style="display: block; margin: 0 auto;">

Ta thực hiện đổi cookie `session` thành của `wiener`\
Ta thấy được kết quả là không có quyền thực hiện thay đổi, ta thực hiện chuyển method từ `POST` thành `GET` 

<img src="./images/2026-08-30-19-55-27.png" style="display: block; margin: 0 auto;">

Kết quả là request đã được xử lý, ta thực hiện thay `username` thành `wiener` và `upgrade`

<img src="./images/2026-08-30-19-56-47.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-30-19-57-04.png" style="display: block; margin: 0 auto;">

## **Lab: Multi-step process with no access control on one step**
<img src="./images/2026-08-30-20-09-31.png" style="display: block; margin: 0 auto;">

>>Lab yêu cầu thực hiện thay đổi quyền bằng tài khoản của `wiener`

Trong bước đổi role, web đã thêm một bước confirm đổi role

<img src="./images/2026-08-30-20-14-17.png" style="display: block; margin: 0 auto;">

Tổng là có 3 bước, ta thực hiện đổi `session` của `wiener` 

<img src="./images/2026-08-30-20-17-53.png" style="display: block; margin: 0 auto;">

Với request ban đầu, ta đã không có quyền, ta thực hiện chuyển sang request `confirm` và đã thành công 

<img src="./images/2026-08-30-20-19-21.png" style="display: block; margin: 0 auto;">

Thực hiện đổi `username` thành `wiener`

<img src="./images/2026-08-30-20-21-19.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-30-20-21-40.png" style="display: block; margin: 0 auto;">

## **Lab: Referer-based access control**

<img src="./images/2026-08-30-20-25-55.png" style="display: block; margin: 0 auto;">

Bài này có một header `Referer` để sử dụng làm filter, nếu thực hiện request này mà không có tiêu đề này hoặc giá trị của tiêu đề này thì sẽ bị **deny**

Thực hiện thay đổi `session` và `usename` và xóa đi header `Referer` 

<img src="./images/2026-08-30-20-30-41.png" style="display: block; margin: 0 auto;">

Ta thực hiện thêm lại tiêu đề này và kết quả là đã bypass được filter của web

<img src="./images/2026-08-30-20-31-23.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-30-20-31-38.png" style="display: block; margin: 0 auto;">





