---
title: "Sử dụng Javascript Form Validation"
description: "Khi bạn ghé thăm một trang web nào đó, bạn có thể thường xuyên bắt gặp các form (biểu mẫu) ví dụ như các form đăng ký, đăng nhập tài khoản, các form lấy ý kiến khách hàng, ..."
dateString: 03/01/2023
draft: false
tags: ["FrontEnd", "Javascript", "Web"]
weight: 107
cover:
    image: "/blog/validation/validation.png"
---

Khi bạn ghé thăm một trang web nào đó, bạn có thể thường xuyên bắt gặp các form (biểu mẫu) ví dụ như các form đăng ký, đăng nhập tài khoản, các form lấy ý kiến khách hàng, ... Các thông tin mà người dùng nhập vào form cần phải được xác thực (validate) để đảm bảo sự hợp lý của dữ liệu.

Một vài ví dụ về validate form chúng ta có thể thường gặp bao gồm:

- Kiểm tra đảm bảo dữ liệu không được để trống.
- Kiểm tra định dạng email, số điện thoại
- Yêu cầu khi đặt mật khẩu (độ dài, các ký tự phải có, ...)
- Zip code, quốc gia, ngày tháng, ...
- ...

Vậy trên thực tế người ta sử dụng validate form khi nào?

- Dữ liệu của form sẽ được gửi tới server, và việc validate sẽ được thực hiện tại phía máy chủ.
- Dữ liệu của form sẽ được xác thực tại client trước khi gửi tới server bằng cách sử dụng Javascript, điều này giúp server không phải làm việc quá nhiều, và tăng hiệu năng cho ứng dụng.
- Sử dụng kết hợp cả 2 phương thức trên để validate.

OK, bây giờ chúng ta cùng đi vào một ví dụ cụ thể

![giao diện đăng ký form](https://media.techmaster.vn/api/static/9479/c2ve4tc51cofhqf7v720)

Ở trên mình có giao diện một form đăng ký cơ bản với các trường dữ liệu : **Họ tên**, **Số điện thoại**, **Email** và một nút **Đăng ký**

> Giao diện phần này các bạn có thể tham khảo tại đây : [https://github.com/buihien0109/js-validate-basic](https://github.com/buihien0109/js-validate-basic)

Đối với form bên trên, chúng ta có thể liệt kê ra một số trường hợp cần validate như:

- Họ tên không được để trống
- Số điện thoại không được để trống
- Email không được để trống
- Số điện thoại không đúng định dạng
- Email không đúng định dạng

Nếu có trường hợp nào trong các trường hợp trên xảy ra, chúng ta sẽ thông báo 1 message text ngay bên dưới ô input để người dùng dễ nhận biết và chỉnh sửa lại nội dung cho phù hợp

Còn trường hợp tất cả các ô dữ liệu đều thỏa mãn điều kiện validate, lúc này chúng ta sẽ thông báo ra message với nội dung **"Đăng ký thành công"**

Bắt đầu thôi nào 😄 =))

```javascript
const btnRegister = document.getElementById('btn-register');

btnRegister.addEventListener('click', function () {
    let isValid = checkValidate();

    if (isValid) {
        alert('Gửi đăng ký thành công');
    }
});
```

Đầu tiên chúng ta sẽ truy cập vào nút " **Đăng ký**" và tiến hành lắng nghe sự kiện khi người dùng click vào nút này

Bên trong hàm xử lý sự kiện, chúng ta tiến hành gọi function **checkValidate()**, function này sẽ trả về giá trị **true/false** tùy thuộc vào dữ liệu trong các ô input có thỏa mãn tất cả các điều kiện validate hay không?

Trường hợp **isValid == true**, lúc này chúng ta sẽ hiển thị message **"Gửi đăng ký thành công"**

Bây giờ chúng ta sẽ tiến hành khai báo function **checkValidate()**

```javascript
// Truy cập vào các ô input
const usernameEle = document.getElementById('username');
const emailEle = document.getElementById('email');
const phoneEle = document.getElementById('phone');

// Validate dữ liệu trong các ô input và highlight
function checkValidate() {
    let usernameValue = usernameEle.value;
    let emailValue = emailEle.value;
    let phoneValue = phoneEle.value;

    let isCheck = true;

    // Kiểm tra trường username
    if (usernameValue == '') {
        setError(usernameEle, 'Tên không được để trống');
        isCheck = false;
    } else {
        setSuccess(usernameEle);
    }

    // Kiểm tra trường email
    if (emailValue == '') {
        setError(emailEle, 'Email không được để trống');
        isCheck = false;
    } else if (!isEmail(emailValue)) {
        setError(emailEle, 'Email không đúng định dạng');
        isCheck = false;
    } else {
        setSuccess(emailEle);
    }

    // Kiểm tra trường phone
    if (phoneValue == '') {
        setError(phoneEle, 'Số điện thoại không được để trống');
        isCheck = false;
    } else if (!isPhone(phoneValue)) {
        setError(phoneEle, 'Số điện thoại không đúng định dạng');
        isCheck = false;
    } else {
        setSuccess(phoneEle);
    }

    return isCheck;
}
```

Trong function **checkValidate()**, trước hết chúng ta sẽ lấy ra dữ liệu của các ô input và lưu vào các biến **usernameValue**, **emailValue**, **phoneValue**

Tiếp theo chúng ta có biến **isCheck**, biến này dùng để xác định xem dữ liệu ở tất cả các ô input có hợp lệ hay không, chỉ cần 1 trường hợp không hợp lệ **isCheck = false** (mặc định là **true**)

Trong function này chúng ta sẽ tiến hành kiểm tra dữ liệu của 3 ô input:

- Kiểm tra trường username
- Kiểm tra trường phone
- Kiểm tra trường email

Ở trong mỗi trường lại có các điều kiện nhỏ hơn để chúng ta tiến hành kiểm tra. Vì cơ bản quá trình kiểm tra của 3 trường này khá giống nhau, vì vậy mình sẽ giải thích qua về quá trình validate của trường **Email**

```javascript
if (emailValue == '') {
    setError(emailEle, 'Email không được để trống');
    isCheck = false;
} else if (!isEmail(emailValue)) {
    setError(emailEle, 'Email không đúng định dạng');
    isCheck = false;
} else {
    setSuccess(emailEle);
}
```

Với trường email có 2 điều kiện validate :

- Email không được để trống
- Email không đúng định dạng

Với trường hợp xảy ra lỗi, lúc này chúng ta sẽ gọi function **setError()** đồng thời truyền vào **emailEle** (để highlight) và **message** (thông báo lỗi trên giao diện), đồng thời giá trị **isCheck = false**

Trong trường hợp dữ liệu thảo mãn, chúng ta sẽ gọi function **setSuccess()** và truyền vào **emailEle** (để highlight)

```javascript
function setError(ele, message) {
    let parentEle = ele.parentNode;
    parentEle.classList.add('error');
    parentEle.querySelector('small').innerText = message;
}
```

Function **setError()** có tác dụng highlight ô input và hiển thị message error trong trường hợp validate error

```javascript
function setSuccess(ele) {
    ele.parentNode.classList.add('success');
}
```

Function **setError()** có tác dụng highlight ô input trong trường hợp validate success

2 class **"success"** và **"error"** mình đều định nghĩa trong file style.css

Hình ảnh minh họa cho function **setError()**

![Demo trường hợp có lỗi](https://media.techmaster.vn/api/static/9479/c2veihs51cofhqf7v730)

```javascript
function isEmail(email) {
    return /^(([^<>()\[\]\\.,;:\s@"]+(\.[^<>()\[\]\\.,;:\s@"]+)*)|(".+"))@((\[[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}\.[0-9]{1,3}])|(([a-zA-Z\-0-9]+\.)+[a-zA-Z]{2,}))$/.test(email);
}

function isPhone(number) {
    return /(84|0[3|5|7|8|9])+([0-9]{8})\b/.test(number);
}
```

2 function **isEmail()**, **isPhone()** có tác dụng kiểm tra xem đầu vào có phải là email hoặc số điện thoại hay không. Ở đây mình có sử dụng Regex để kiểm tra, phần Regex này các bạn có thể tìm kiếm trên mạng hoặc có thể tự viết 1 đoạn Regex

Trước khi đi đến phần kiểm tra thành quả, mình có bổ sung thêm 1 đoạn code khi người dùng bấm vào nút **"Đăng ký"**

```javascript
const inputEles = document.querySelectorAll('.input-row');

btnRegister.addEventListener('click', function () {
    Array.from(inputEles).map((ele) =>
        ele.classList.remove('success', 'error')
    );

    ...
});
```

Mình bổ sung thêm đoạn code trên để đảm bảo trước khi tiến hành validate thì trong các ô input không xuất hiện class " **success**", " **error**"

😄 Dưới đây là một vài kết quả, mình đạt được sau khi thử nghiệm, các bạn có thể quan sát

**1. Trường hợp các ô dữ liệu để trống**

![Trường hợp các ô dữ liệu để trống](https://media.techmaster.vn/api/static/9479/c2veku451cofhqf7v73g)

**2. Trường hợp không đúng định dạng dữ liệu**

![Trường hợp không đúng định dạng dữ liệu](https://media.techmaster.vn/api/static/9479/c2vekv451cofhqf7v740)

**3. Trường hợp validate thành công**

![Trường hợp validate thành công](https://media.techmaster.vn/api/static/9479/c2vekvs51cofhqf7v74g)

* * *

Sources code tham khảo của bài viết này các bạn có thể tìm kiếm tại đây : [https://github.com/buihien0109/js-validate-basic](https://github.com/buihien0109/js-validate-basic)