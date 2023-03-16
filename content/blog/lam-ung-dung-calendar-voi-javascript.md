---
title: "Làm ứng dụng Calendar với Javascript"
description: "Chào các bạn, hôm nay mình sẽ hướng dẫn các bạn tạo một ứng dụng Calendar nho nhỏ bằng Javascript"
dateString: 29/12/2022
draft: false
tags: ["FrontEnd", "Javascript", "Web"]
weight: 107
cover:
    image: "/blog/calendar/calendar.png"
---

Chào các bạn, hôm nay mình sẽ hướng dẫn các bạn tạo một ứng dụng Calendar nho nhỏ bằng Javascript

Đây là giao diện ứng dụng ban đầu của chúng ta

![giao diện ban đầu của ứng dụng calendar](https://media.techmaster.vn/api/static/9479/c2up1ds51cofhqf7v6u0)

> Phần template của ứng dụng các bạn có thể tham khảo tại đây : [https://github.com/buihien0109/calendar-javascript/tree/master/calendar-start](https://github.com/buihien0109/calendar-javascript/tree/master/calendar-start)

Các bạn có thể quan sát thấy, ứng dụng của chúng ta bao gồm 1 số chức năng như sau :

- Hiển thị tháng và năm
- Hiển thị số ngày trong tháng
- Vị trí bắt đầu của ngày đầu tiên (ngày mùng 1 nằm ở thứ mấy trong tuần)
- Ngày hiện tại theo thời gian thực (được active với background)
- Chức năng next month, prev month

Bây giờ thì cùng bắt đầu triển thôi nào 😁

## Hiển thị tháng và năm

Công việc đầu tiên mình sẽ làm là hiển thị tháng và năm, cái nào đơn giản thì mình làm trước vậy ^^

Mặc định thì khi vào vừa trang thì mình sẽ hiển thị tháng và năm tại thời điển hiện tại

```javascript
let monthEle = document.querySelector('.month');
let yearEle = document.querySelector('.year');

let currentMonth = new Date().getMonth();
let currentYear = new Date().getFullYear();
```

Ở trên mình có truy cập vào nơi hiển thị tháng và năm trên giao diện và lưu vào 2 biến **monthEle** và **yearEle**. Để về sau, khi chúng ta có dữ liệu về tháng và năm thì dựa vào 2 biến này để hiển thị lên trên giao diện

**currentMonth**, **currentYear** là 2 biến lưu trữ thông tin về tháng và năm hiện tại. Có 1 lưu ý rằng **currentMonth** hiện tại đang có giá trị từ 0 -> 11 (sử dụng **getMonth()**) nhưng điều mình mong muốn là hiển thị tháng theo tên tiếng anh (ví dụ : January, February, ...)

```javascript
function displayInfo() {
    // Hiển thị tên tháng
    let currentMonthName = new Date(
        currentYear,
        currentMonth
    ).toLocaleString('en-us', { month: 'long' })

    monthEle.innerText = currentMonthName;

    // Hiển thị năm
    yearEle.innerText = currentYear;
}
```

Tiếp theo trong function **displayInfo()** mình sẽ hiển thị tháng và năm hiện tại ra ngoài giao diện

Đối với việc hiển thị năm thì không có gì để bàn rồi, nhưng riêng đối với tháng thì có hơi khác 1 chút, chúng ta cần phải convert tháng hiện tại từ number -> date string. Rất may là Javascript có sẵn phương thức **toLocaleString()** để giúp chúng ta có thể làm điều này

Ngoài ra, nếu các bạn không muốn sử dụng build-in method **toLocaleString()**, thì có thể sử dụng **switch - case** để thay thế

```javascript
window.onload = displayInfo;
```

Để hiển thị được thì cần gọi function **displayInfo()**. Ở đây mình lắng nghe sự kiện **onload()** để gọi function này

Và đây là kết quả ban đầu của chúng ta

![hiển thị tháng và năm - calendar](https://media.techmaster.vn/api/static/9479/c2upd7451cofhqf7v6ug)

## Hiển thị số ngày trong tháng

```javascript
// Lấy số ngày của 1 tháng
function getDaysInMonth() {
    return new Date(currentYear, currentMonth + 1, 0).getDate();
}

let dateEle = document.querySelector('.date-container');
// Hiển thị ngày trong tháng lên trên giao diện
function renderDate() {
    let daysInMonth = getDaysInMonth();

    dateEle.innerHTML = '';

    for (let i = 0; i < daysInMonth; i++) {
        dateEle.innerHTML += `
            <div class="day">${i + 1}</div>
        `;
    }
}
```

Function **getDaysInMonth()** giúp chúng ta có thể lấy được số ngày trong tháng **currentMonth**

Tiếp theo mình định nghĩa function **renderDate()**, function này có tác dụng hiển thị danh sách ngày lên trên giao diện

```javascript
let daysInMonth = getDaysInMonth();
```

Đầu tiên để biết tháng hiện tại có bao nhiêu ngày, chúng ta gọi function **getDaysInMonth()** vừa định nghĩa ở trên và lưu giá trị và biến **daysInMonth**

```javascript
dateEle.innerHTML = '';
```

Vì mình có fix cứng code HTML trong giao diện ban đầu để có bạn có thể quan sát xem số ngày trong tháng được hiển thị như thế nào. Nhưng bây giờ chúng ta cần phải hiển thị linh động số ngày theo tháng hiện tại ( **currentMonth**), nên trước khi render, chúng ta cần clear hết nội dung html fix cứng trước đó để chuẩn bị render nội dung mới

```javascript
for (let i = 0; i < daysInMonth; i++) {
    dateEle.innerHTML += `
        <div class="day">${i + 1}</div>
    `;
}
```

Cuối cùng chúng ta sẽ sử dụng vòng lặp để thực hiện render số ngày trong tháng ra ngoài giao diện (số ngày sẽ được chứa trong thẻ **"div"** có class **"day"**) nội dung bên trong sẽ dựa vào chỉ số của vòng lặp để hiển thị

## Hiển thị ngày bắt đầu của tháng

```javascript
// Lấy ngày bắt đầu của tháng
function getStartDayInMonth() {
    return new Date(currentYear, currentMonth, 1).getDay();
}
```

Function **getStartDayInMonth()** giúp chúng ta có thể biết ngày bắt đầu của tháng hiện tại nằm vào thứ mấy trong tuần

Phương thức **getDay()** trả về thứ trong tuần ( **0** - Chủ nhật, **1** - Thứ 2, **2** - Thứ 3 , ...)

```javascript
// Hiển thị ngày trong tháng lên trên giao diện
function renderDate() {
        let startDay = getStartDayInMonth();

    ...

    for (let i = 0; i < startDay; i++) {
        dateEle.innerHTML += `
            <div class="day"></div>
        `;
    }
}
```

Sau khi xác định được ngày đầu tiên trong tháng bắt đầu vào thứ mấy, lúc này chúng ta chỉ việc sử dụng vòng lặp để hiển thị nội dung ra ngoài giao diện. Đối với những thứ trong tuần trước ngày bắt đầu trong tháng chúng ta sẽ tạo ra template có nội dung **<div class="day"></div>** nhưng có điều là nội dung trong thẻ được để trống

Sau bước này chúng ta được kết quả như sau:

![hiển thị ngày bắt đầu trong tháng - calendar](https://media.techmaster.vn/api/static/9479/c2uqv6s51cofhqf7v6vg)

## Active ngày hiện tại

```javascript
function activeCurrentDay(day) {
    let day1 = new Date().toDateString();
    let day2 = new Date(currentYear, currentMonth, day).toDateString();
    return day1 == day2 ? 'active' : '';
}
```

Function **activeCurrentDay()** có tác dụng kiểm tra xem ngày hiện tại theo thời gian thực ( **day1**) có trùng với ngày được được khởi tạo bằng new Date() với các giá trị **currentYear**, **currentMonth** và tham số **day** hay không?

Nếu **day1 == day2** trùng nhau, lúc ngày chúng ta sẽ trả về chuỗi **'active'**, còn không thì trả về chuỗi rỗng **''**

```javascript
function renderDate() {
    ...

    for (let i = 0; i < daysInMonth; i++) {
        dateEle.innerHTML += `
            <div class="day ${activeCurrentDay(i + 1)}">${i + 1}</div>
        `;
    }
}
```

Tiếp theo chúng ta sẽ điều chỉnh 1 chút về việc hiển thị ngày trong function **renderDate()**. Trong vòng lặp for, mỗi khi render ngày ra ngoài giao diện, chúng ta sẽ kiểm tra thêm điều kiện **activeCurrentDay(i + 1)** tương ứng với ngày đó, để thực hiện việc active

Các bạn có thể quan sát kết quả ở hình ảnh dưới đây

![Active ngày hiện tại theo thời gian thực - calendar](https://media.techmaster.vn/api/static/9479/c2uqv8k51cofhqf7v700)

Cũng ổn phải không nào 😁

## Xử lý next month, previous month

```javascript
// Xử lý khi ấn vào nút next month
let btnNext = document.querySelector('.btn-next');
btnNext.addEventListener('click', function () {
    if (currentMonth == 11) {
        currentMonth = 0;
        currentYear++;
    } else {
        currentMonth++;
    }
    displayInfo();
});
```

Đơn giản là mỗi khi chúng ta click vào nút **next month**, lúc này chúng ta sẽ thực hiện tăng **currentMonth** lên 1 đơn vị, trường hợp **currentMonth == 11** (tức tháng hiện tại là tháng 12) lúc này tăng **currentYear** lên 1 và cho **currentMonth = 0** (cho tháng hiện tại quay về tháng 1)

Sau đó gọi function **displayInfo()** để cập nhật lại thông tin mới lên giao diện

Chúng ta sẽ xử lý tương tự như với **previous month**

```javascript
// Xử lý khi ấn vào nút previous month
let btnPrev = document.querySelector('.btn-prev');
btnPrev.addEventListener('click', function () {
    if (currentMonth == 0) {
        currentMonth = 11;
        currentYear--;
    } else {
        currentMonth--;
    }
    displayInfo();
});
```

Cuối cùng thì chúng ta đã hoàn thành một ứng dụng **Calendar** đơn giản sử dụng Javascript. Hi vọng các bạn thích bài viết này.

* * *

Phần sources code các bạn có thể tham khảo tại đây: [https://github.com/buihien0109/calendar-javascript/tree/master/calendar-final](https://github.com/buihien0109/calendar-javascript/tree/master/calendar-final)

Các bạn có thể tham khảo thêm khóa học này nhé:

- Javascript căn bản - Tổng hợp 12 game huyền thoại - [tại đây](https://techmaster.vn/khoa-hoc/ev5/javascript-can-ban-tong-hop-12-game-huyen-thoai).
- Khóa học Web Frontend (HTML, CSS, JavaScript) + ReactJs - [tại đây](https://techmaster.vn/lo-trinh/front-end)