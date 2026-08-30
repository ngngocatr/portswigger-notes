## **Lab: File path traversal, simple case**
<img src="./images/2026-08-29-21-35-26.png" style="display: block; margin: 0 auto;">

>>Lab yêu cầu lấy nội dung của file `/etc/passwd`

Ta cần tìm một request hiển thị ảnh và thay bằng `/../../../../../../../../../../../etc/passwd`

<img src="./images/2026-08-29-21-38-54.png" style="display: block; margin: 0 auto;">

## **Lab: File path traversal, traversal sequences blocked with absolute path bypass**

<img src="./images/2026-08-29-21-40-00.png" style="display: block; margin: 0 auto;">

>>Lab nói rằng dấu web đã chặn dấu `../` nhưng vẫn chấp nhận đường dẫn tuyệt đối

Vậy nên ta chỉ cần chèn đường dẫn tuyệt đối của là `/etc/passwd`

<img src="./images/2026-08-29-21-46-46.png" style="display: block; margin: 0 auto;">

## **Lab: File path traversal, traversal sequences stripped non-recursively**

<img src="./images/2026-08-29-21-48-16.png" style="display: block; margin: 0 auto;">

>>Lab nói rằng web đã thay thế dấu `../` nhưng không thay thế đệ quy

Web có thể thực hiện filter thay thế chuỗi `../` thành chuỗi rỗng, nhưng có thể nó chỉ làm một lần\
Vậy nên ta có thể thực hiện bypass bằng chuỗi `....//` thay vì `../` để web filter 1 lần nó sẽ trở thành `../`

<img src="./images/2026-08-29-21-58-22.png" style="display: block; margin: 0 auto;">

## **Lab: File path traversal, traversal sequences stripped with superfluous URL-decode**
<img src="./images/2026-08-30-15-58-11.png" style="display: block; margin: 0 auto;">

>>Web đã chặn đường dẫn có chứa dấu `../`, nhưng trước khi đưa cho server xử lý thì nó thực hiện decode URL --> ta cần phải thực hiện URL encode để bypass filter

Ta sẽ sử dụng các payload có sẵn [tại đây](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/File%20Inclusion/Intruders/dot-slash-PathTraversal_and_LFI_pairing.txt) và đưa vào Intruder để thử

<img src="./images/2026-08-30-16-13-40.png" style="display: block; margin: 0 auto;">

Kết quả là ta nhận được kết quả ở một số payload URL encode như `%2f%2e%2e%252f%2e%2e%252f%2e%2e%252fetc/passwd`, `%2f%2e%2e%252f%2e%2e%252f%2e%2e%252f%2e%2e%252fetc/passwd`

## **Lab: File path traversal, validation of start of path**
<img src="./images/2026-08-30-16-15-24.png" style="display: block; margin: 0 auto;">

>>Lab thực hiện filter bằng cách đường dẫn phải bắt đầu bằng thư mục nào đó ở đầu

<img src="./images/2026-08-30-16-18-02.png" style="display: block; margin: 0 auto;">

Ta quan sát đường dẫn là đường dẫn tuyệt đối và là đường dẫn `/var/www/images/` ở đầu

Vậy nên ta thực hiện dùng payload `/var/www/images/../../../../etc/passwd`

<img src="./images/2026-08-30-16-19-52.png" style="display: block; margin: 0 auto;">

## **Lab: File path traversal, validation of file extension with null byte bypass**

<img src="./images/2026-08-30-16-21-25.png" style="display: block; margin: 0 auto;">

>>Để làm được lab này thì ban đầu cần đọc kĩ thuật [tại đây](https://portswigger.net/web-security/file-path-traversal#common-obstacles-to-exploiting-path-traversal-vulnerabilities:~:text=An%20application%20may%20require%20the%20user%2Dsupplied%20filename%20to%20end%20with%20an%20expected%20file%20extension%2C%20such%20as%20.png.%20In%20this%20case%2C%20it%20might%20be%20possible%20to%20use%20a%20null%20byte%20to%20effectively%20terminate%20the%20file%20path%20before%20the%20required%20extension.%20For%20example%3A%20filename%3D../../../etc/passwd%2500.png.)

Phía web có thể thực hiện filter bằng đuôi file `.jpg`, ví dụ: `/etc/passwd` trở thành `/etc/passwd.jpg`

Và một số framwork nhận ký tự `%00` (`NULL`) là kí tự đánh dấu kết thúc; ví dụ: `/etc/passwd%00.jpg` thì hệ thống sẽ chỉ lấy phần ở trước kí tự `%00` và phần sau sẽ bị bỏ\
Vì vậy ta sẽ dùng payload `../../../../etc/passwd%00.jpg` để bypass

<img src="./images/2026-08-30-16-27-42.png" style="display: block; margin: 0 auto;">

