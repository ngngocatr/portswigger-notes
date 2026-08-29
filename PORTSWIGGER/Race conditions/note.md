## **Lab: Limit overrun race conditions**

__Tấn công bằng cách gửi nhiều request cực nhanh để vượt qua giới hạn__

![2025-10-31-14-34-10](../images/2025-10-31-14-34-10.png)
--- 

>>Flow mua hàng trong lab này có race condition, cho phép mua món với giá không đúng.\
>>Mục tiêu: mua thành công chiếc `Lightweight L33t Leather Jacket`.\
>>Login: `wiener : peter`.\
>>Gợi ý: để kích race nhanh hơn, dùng Trigger race conditions (custom action) trong Burp Suite Professional.

---


## **Lab: Bypassing rate limits via race conditions**
<img src="./images/2026-08-28-20-21-20.png" style="display: block; margin: 0 auto;">

>>Web có cơ chế rate limit để chống brute force; ta cần khai thác race condition để có thể bypass được cơ chế này

>>Với bài này ta cần sử dụng extension **Turbo Intruder** để có thể thực hiện brute force nhiều mật khẩu cùng một lúc thay vì phải thực hiện duplicate từng request rồi sửa lại mật khẩu cho từng request đó

Thực hiện bắt 1 request bằng Intercept rồi chuyển nó sang **Turbo Intruder**

<img src="./images/2026-08-28-20-26-55.png" style="display: block; margin: 0 auto;">

Ở đây ta có thể thực hiện chọn template phù hợp rồi chỉnh lại cho phù hợp với mục đích
<img src="./images/2026-08-28-20-27-31.png" style="display: block; margin: 0 auto;">

Đây là code Turbo để giải bài này

```python
def queueRequests(target, wordlists):
    engine = RequestEngine(
        endpoint=target.endpoint,
        concurrentConnections=1,
        engine=Engine.BURP2
    )

    passwords = '''
123123
abc123
football
monkey
letmein
shadow
master
666666
qwertyuiop
123321
mustang
123456
password
12345678
qwerty
123456789
12345
1234
111111
1234567
dragon
1234567890
michael
x654321
superman
1qaz2wsx
baseball
7777777
121212
000000
'''.strip().splitlines()

    for password in passwords:
        engine.queue(target.req, password, gate='race1')

    engine.openGate('race1')


def handleResponse(req, interesting):
    table.add(req)
```

Phần payload ta cần phải thực hiện đổi tham số password thành `%s` đại diện cho chỗ cần thay wordlist

```
csrf=nlbB12JXBLndHPVE7GQQeCkiTMJYTSWb&username=carlos&password=%s
```

Thực hiện chạy và quan sát kết quả

<img src="./images/2026-08-28-20-31-06.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-28-20-32-00.png" style="display: block; margin: 0 auto;">

Ta thấy có cả `200` (*Sai mật khẩu nhưng vẫn được xử lý --> bypass ratelimit* ), `302` (*Đăng nhập thành công*), và `400` (*Request đến sau và CSRF hết hạn*)

Vậy ta thu được mật khẩu là `baseball`

<img src="./images/2026-08-28-20-34-55.png" style="display: block; margin: 0 auto;">

--> Kết luận: web đã không xử lý đúng thứ tự của request, khiến nó phải xử lý đan sen nhiều request đồng thời do đó tạo ra sự bất đồng

## **Lab: Multi-endpoint race conditions**
<img src="./images/2026-08-29-15-37-06.png" style="display: block; margin: 0 auto;">

>>Ta cần phải mua sản phẩm **Lightweight L33t Leather Jacket**
>>Trước khi thực làm nên đọc lại lý thuyết [tại đây
](https://portswigger.net/web-security/race-conditions#multi-endpoint-race-conditions)

Ban đầu ta sẽ thực hiện thêm sản phẩm vào giỏ hàng rồi thực hiện checkout

<img src="./images/2026-08-29-15-40-05.png" style="display: block; margin: 0 auto;">

Thực hiện quan sát request trong Burp

<img src="./images/2026-08-29-15-41-56.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-15-42-09.png" style="display: block; margin: 0 auto;">

Ta thấy 2 request này có khoảng thời gian xử lý khá là xa nhau `/cart` là `552ms` và `/cart/checkout` là `613ms`, có thể có 2 trường hợp:
- TH1: 2 request này có luồng xử lý nhau, dẫn đến việc thời gian xử lý của request phức tạp sẽ mất nhiều thời gian hơn
- TH2: request đầu tiên phải thực hiện thiết lập kết nối, nên nó sẽ mất nhiều thời gian hơn

Nếu rơi vào trường hợp 2, ta sẽ xử dụng được lý thuyết đã hướng dẫn trong bài trên: sử dụng một request "mồi" để thiết lập kết nối trước và tiếp tục thực hiện 2 request trên xem thời gian xử lý có tương đồng nhau không

Ta sẽ thiết lập một group gồm các request `/` (`9`) ở đầu, `/cart` (`8`), `/cart/checkout` (`6`) và gửi lần lượt bằng **single connection** - gửi 3 gói lần lượt nhau trong cùng một luồng

<img src="./images/2026-08-29-15-48-05.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-15-50-19.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-15-50-30.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-15-50-41.png" style="display: block; margin: 0 auto;">

Sau khi gửi, ta thấy được `/` mất `523ms`, `/cart` mất `225ms` và `/cart/checkout` mất `251ms` chứng tỏ rằng nó thuộc kịch bản 2

Ta tiến hành thay request add cart bằng sản phẩm **Lightweight L33t Leather Jacket** và gửi bằng **parallel**

## **Lab: Single-endpoint race conditions**
<img src="./images/2026-08-29-16-44-22.png" style="display: block; margin: 0 auto;">

>>Lab nói rằng người dùng `carlos` với email `carlos@ginandjuice.shop` được add vào hệ thống với quyền quản trị viên, nhưng người dùng này chưa hề thực hiện tạo tài khoản
>>Lab yêu cầu khai thác race condition và chiếm được email của `carlos` và xóa user `carlos`
>>Lưu ý: bài này cần thực hiện nhiều lần và cần một chút may mắn 

Luồng giả tưởng của chức năng đổi email:
```
session['reset-user'] = username
session['reset-token'] = token
send token tới session['reset-user']
```

Thứ tự lỗi khi gửi 2 request cùng một thời điểm là:

```
Request 1: reset cho attacker
session['reset-user'] = attacker

Request 2: reset cho victim
session['reset-user'] = victim
session['reset-token'] = TOKEN_VICTIM

Request 1 tiếp tục
session['reset-token'] = TOKEN_ATTACKER
send session['reset-token'] cho attacker
```

<img src="./images/2026-08-29-16-57-23.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-16-57-45.png" style="display: block; margin: 0 auto;">

Khi thực hiện nhập email vào để đổi thì ta nhận được mail đổi mật khẩu tương ứng với email ta vừa nhận được

<img src="./images/2026-08-29-16-59-21.png" style="display: block; margin: 0 auto;">

Thực hiện quan sát request trong Burp, ta thấy rằng có tham số `email` và `csrf` nhưng ta có thể thực hiện gửi nhiều lần bằng csrf này nên có thể bỏ qua tham số này

Ta thực hiện thay đổi tạo thêm một request nữa với email là `carlos@ginandjuice.shop` rồi nhóm nó vào một group và gửi với mod **parallel**

Luồng xử lý lí tưởng để xảy ra race condition:
```
attacker gửi request reset email: session['attacker'] = attacker

Vì gửi cùng 1 thời điểm nên request của email carlos cũng được gửi đồng thời ngay sau đó: session['carlos'] = carlos

Nhưng khi này có thể việc gen token của session['attacker'] mới xong cho nên nó trả luôn cho carlos token của attacker: session['token_attacker'] = carlos

Và khi này session['token_carlos'] = attacker, tức là attacker nhận dược token của carlos và claim được email này
```

<img src="./images/2026-08-29-17-05-36.png" style="display: block; margin: 0 auto;">

Thực hiện gửi group requets, ta có thể thấy được phần email có thể không được ngay vì bên trên chỉ là trường hợp lý tưởng, nên ta cần gửi nhiều lần và may mắn sẽ nhận được token của `carlos`

<img src="./images/2026-08-29-17-07-42.png" style="display: block; margin: 0 auto;">

Sau vài lần gửi request thì ta nhận được token của carlos, ta thực hiện xác nhận đổi email và nhận được quyền admin

<img src="./images/2026-08-29-17-08-55.png" style="display: block; margin: 0 auto;">

Thực hiện xóa carlos để solve

## **Lab: Exploiting time-sensitive vulnerabilities **
<img src="./images/2026-08-29-17-14-26.png" style="display: block; margin: 0 auto;">

>>Lab nói rằng cơ chế resetpassword có lỗ hổng về việc thực hiện tạo token bằng timestamp với độ chính xác cao thay vì dùng chuỗi ngẫu nhiên
>>Thực hiện tìm ra cách web gentoken và đổi mật khẩu chiếm quyền tài khoản `carlos` và xóa user này

Ban đầu ta thử gửi một request reset mật khẩu cho 2 request ở mode **parallel**, ta thấy được rằng thời gian xử lý 2 request cách xa nhau

<img src="./images/2026-08-29-21-09-38.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-21-10-01.png" style="display: block; margin: 0 auto;">

Và token cũng không giống nhau

<img src="./images/2026-08-29-21-10-38.png" style="display: block; margin: 0 auto;">

Điều này có thể do việc xử lý cùng một phiên sẽ được xử lý tuần tự chứ không xử lý song song nên có thể ra được token giống nhau\
Vì vậy ta thay đổi `phpsessionid` và `csrf`, kết quả là web đã xử lý request một cách đồng thời 

<img src="./images/2026-08-29-19-54-04.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-19-54-19.png" style="display: block; margin: 0 auto;">

Và token được trả về cũng giống hệt nhau

<img src="./images/2026-08-29-19-59-36.png" style="display: block; margin: 0 auto;">

Ta đổi phần username thành carlos để web xử lý trả về cùng token 

<img src="./images/2026-08-29-21-14-26.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-21-14-57.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-21-15-31.png" style="display: block; margin: 0 auto;">

<img src="./images/2026-08-29-21-16-00.png" style="display: block; margin: 0 auto;">

Thực tế đến đoạn này ta có thể vẫn bị lỗi `Invalid token` nhưng ta có thể thay đổi `phpsessionid` và `csrf` một lần nữa để có thể nhận được token chuẩn và đổi mật khẩu

Đổi mật khẩu, đăng nhập vào user `carlos` và xóa user này để solve 