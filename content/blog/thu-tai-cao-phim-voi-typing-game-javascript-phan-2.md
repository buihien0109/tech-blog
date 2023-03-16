---
title: "Thử tài cào phím với Typing Game - Javascript (phần 2)"
description: "Chào mọi người, ở bài viết trước mình đã hướng dẫn các bạn render các từ trong một đoạn văn bản ra ngoài giao diện, đồng thời chúng ta cũng có thể chuyển đổi ngôn ngữ từ tiếng việt sang tiếng anh hoặc ngược lại nếu muốn"
dateString: 25/12/2022
draft: false
tags: ["FrontEnd", "Javascript", "Web", "Game"]
weight: 104
cover:
    image: "/blog/typing-game/typing-game-2.png"
---

Chào mọi người, ở [bài viết trước](https://techmaster.vn/posts/36501/thu-tai-cao-phim-voi-typing-game-javascript-phan-1) mình đã hướng dẫn các bạn render các từ trong một đoạn văn bản ra ngoài giao diện, đồng thời chúng ta cũng có thể chuyển đổi ngôn ngữ từ tiếng việt sang tiếng anh hoặc ngược lại nếu muốn

Trong khuôn khổ bài viết bài này, chúng ta cũng nhau đi thực hiện một số công việc tiếp theo trong Typing Game bao gồm :

- Đếm ngược thời gian
- Xử lý khi gõ từ

> Nếu các bạn thích chủ đề làm Game và Ứng dụng bằng Javascript thì hãy comment ở bên dưới bài viết cho mình biết nhé 😆

Bây giờ thì cùng bắt đầu thôi nào 😄

## Đếm ngược thời gian

```lang-javascript
const timeEl = document.querySelector('#time');

let time;
let interval;
let isPlaying;

function init() {
    time = 60;
    index = 0;
    isPlaying = false;

    // Chọn ngôn ngữ

    // Đổi vị trí các từ trong mảng

    // Render từ

    // Hiển thị thời gian
    timeEl.innerText = convertTime(time);

    // Highlight từ đầu tiên
}
```

Ở đoạn code trên mình có khai báo 3 biến : **time**, **interval**, **isPlaying**

- **time** : Thời gian chơi game (khi bắt đầu game thì time sẽ bắt đầu đếm ngược)
- **interval** : Lưu trữ giá trị của hàm **setInterval()** (phục vụ cho mục đích đếm ngược thời gian), về sau khi thời gian = 0, lúc này mình sẽ dùng hàm **clearInterval()** để dừng quá trình đếm ngược thời gian dựa vào biến interval
- **isPlaying** : Đây là biến để kiểm tra xem người chơi đã bắt đầu gõ phím chưa để chúng ta bắt đầu quá trình đếm ngược thời gian

Trong function **init()** mình sẽ khởi tạo giá trị **time = 60** (thời gian chơi game là 60s), **isPlaying = false** (người chơi chưa gõ phím để bắt đầu). Đồng thời cũng hiển thị thời gian lên trên giao diện (mới chỉ hiển thị thôi nhé, chứ chưa đếm ngược đâu)

```lang-javascript
// Hiển thị thời gian
timeEl.innerText = convertTime(time);
```

Vì mình muốn thời gian hiển thị lên giao diện theo chuẩn mà mình mong muốn **mm:ss** nên mình đã định nghĩa thêm function **convertTime()** để làm điều này

```lang-javascript
function convertTime(num) {
    let minute = `0${Math.floor(num / 60)}`.slice(-2);
    let second = `0${num % 60}`.slice(-2);
    return `${minute}:${second}`;
}
```

Function **convertTime()** chỉ là convert từ **second -> minute:second** thôi chứ không có gì cả. À ở trên có 1 trích nhỏ nhỏ là mình sử dụng **slice(-2)** để có thể thêm số 0 vào trước nếu trường hợp second và minute < 10

Để quá trình đếm ngược thời gian được diễn ra, người chơi cần focus vào trong ô input và bắt đầu nhập từ

```lang-javascript
inputWordEl.addEventListener('keyup', function (e) {
    // Xử lý đếm ngược thời gian
    if (!isPlaying) {
        interval = setInterval(countdownTime, 1000);
        isPlaying = true;
    }

    // Code xử lý khi người chơi nhập từ
});
```

Đến lúc này biến **isPlaying** mới phát huy sức mạnh của nó. Ban đầu trong function **init()** mình đã khởi tạo giá trị **isPlaying = false**. Khi người chơi bắt đầu nhập từ, nó sẽ kiểm tra biến **isPlaying** trong câu lệnh **if**, nếu thỏa mãn điều kiện nó sẽ thực hiện function **countdownTime** mỗi 1s (1000ms) 1 lần thông qua hàm **setInterval()**

Sau khi quá trình đếm ngược thời gian diễn ra, mình sẽ đặt giá trị **isPlaying = true** để đảm bảo nó không thực hiện câu lệnh trong **if** lần nào nữa

Vậy trong function **countdownTime** mình sẽ làm gì

```lang-javascript
const inputWordEl = document.querySelector('#input-word');

function countdownTime() {
    time--;
    timeEl.innerText = convertTime(time);

    if (time == 0) {
        clearInterval(interval);
        inputWordEl.disabled = true;
        inputWordEl.value = '';
    }
}
```

Đơn giản là mỗi lần **countdownTime** được thực hiên, mình sẽ giảm **time** đi 1 đơn vị, sau đó cập nhật **time** mới lên trên giao diện

Khi **time = 0**, mình sử dụng hàm **clearInterval** để dừng quá trình đếm ngược lại. Đồng thời **disabled** và **clear** hết nội dung trong ô input để không cho người chơi thực hiện nhập từ nữa (hết giờ là nghỉ chơi rồi phải không nào 😅)

* * *

## Xử lý nhập từ khi chơi

Phần tiếp theo của bài viết này, mình sẽ đi xử lý khi người chơi nhập từ

Để xử lý phần này, chúng ta cần biết cách thức chơi của trò này như thế nào

Khi người chơi nhập từ vào ô input, chúng ta sẽ kiểm tra tại thời điểm nhập, dữ liệu trong ô input có khớp với từ đang được highlight hay không?

- Nếu khớp thì không làm gì cả
- Nếu không khớp thì từ đang được highlight sẽ có **background màu đỏ** _(để thông báo ông nhập sai rồi, sửa lại đi)_

Trường hợp người chơi bấm phím space (dấu cách) để next sang từ tiếp theo chúng ta lại phải kiểm tra lần nữa, dữ liệu trong ô input có khớp với từ trước khi next hay không?

- Nếu khớp thì từ đó sẽ có **chữ màu xanh**
- Nếu không khớp thì từ đó sẽ có **chữ màu đỏ**

Bây giờ đi đến phần code xử lý nào

```lang-javascript
inputWordEl.addEventListener('keyup', function (e) {
    // Xử lý đếm ngược thời gian
    ...

    // Kiểm tra từ tại thời điểm gõ
    checkCurrentWord(e.target.value.trim(), words[index]);

    if (e.keyCode == 32) {
        // Kiểm tra từ tại thời điểm next
        compareWord(e.target.value.trim(), words[index]);

        // Chuyển sang từ tiếp theo
        index++;
        highlightWord(index);

        e.target.value = '';
    }
});
```

Ở trên mình lắng nghe sự kiện keyup của ô input. Mỗi khi người chơi gõ phím chúng ta sẽ gọi function **checkCurrentWord()** truyền vào 2 đối số :

- **e.target.value.trim()** : Nội dung trong ô input sau khi đã loại bỏ khoảng trắng ở đầu và cuối
- **words[index]** : Từ tại vị trí index trong mảng từ

Mục đích của function này là kiểm tra từ tại thời điểm nhập để xem từ đang được highlight có được bắt đầu với giá trị trong ô input hay không

Trường hợp người chơi nhấn phím **space (e.keyCode == 32)** tức là người chơi muốn chuyển qua từ kế tiếp. Lúc này mình sẽ gọi function **compareWord()** truyền vào 2 đối số tương tự như function **checkCurrentWord()** để kiểm tra xem dữ liệu trong ô input có khớp với từ trước khi next hay không

Để chuyển qua từ tiếp theo chúng ta sẽ tăng giá trị index lên 1 đơn vị ( **index++**), đồng thời gọi function **highlightWord()** để highlight từ chuẩn bị gõ tiếp theo

```lang-javascript
e.target.value = ''
```

Cuối cùng là clear giá trị trong ô input để cho lần gõ tiếp theo

* * *

Bây giờ chúng ta sẽ đi vào chi tiết 2 function : **checkCurrentWord()** và **compareWord()**

```lang-javascript
function checkCurrentWord(inputValue, word) {
    let spans = document.querySelectorAll('#words span');

    // Từ không được bắt đầu bằng value trong ô input
    if (!word.startsWith(inputValue)) {
        spans[index].classList.add('highlight-wrong');
    } else {
        spans[index].classList.remove('highlight-wrong');
    }
}
```

function **checkCurrentWord()** có tác dụng kiểm tra từ tại thời điểm gõ, mỗi khi người nhập một ký tự mới, mình sẽ so sánh nội dung của của từ đang được gõ có được bắt đầu bằng nội dung của ô input hay không? Để kiểm tra điều này, mình sử dụng phương thức **startsWith()** trong ES6

Nếu từ không được bắt đầu bằng value trong ô input, thêm class **'highlight-wrong'** để css background-color thành màu đỏ cho người chơi dễ nhận biết là mình đang gõ sai và tiến hành sửa lại

Ngược lại, nếu từ được bắt đầu bằng value trong ô input, chúng ta sẽ remove class **'highlight-wrong'** (nếu có)

```lang-javascript
function compareWord(inputValue, word) {
    let spans = document.querySelectorAll('#words span');
    Array.from(spans).map((span) => span.classList.remove('highlight-wrong'));

    // Từ không được bắt đầu bằng value trong ô input
    if (!word.startsWith(inputValue)) {
        spans[index].classList.add('wrong');
    }

    // Từ được bắt đầu bằng value trong ô input nhưng độ dài khác nhau
    if (word.startsWith(inputValue) && inputValue.length != word.length) {
        spans[index].classList.add('wrong');
    }

    // Từ và value trong ô input giống nhau
    if (inputValue == word) {
        spans[index].classList.add('correct');
    }
}
```

Tiếp đến là function **compareWord()** để kiểm tra xem dữ liệu trong ô input có khớp với từ trước khi next hay không? Ở đây mình sẽ tiến hành kiểm tra 3 điều kiện để có thể highlight từ

- Từ không được bắt đầu bằng value trong ô input --> thêm class **'wrong'** vào thẻ span chứa từ đó
- Từ được bắt đầu bằng value trong ô input nhưng độ dài khác nhau -> thêm class **'wrong'** vào thẻ span chứa từ đó
- Từ và value trong ô input giống nhau --> thêm class **'correct'** vào thẻ span chứa từ đó

Tất cả các class trên mình đã đều định nghĩa trong file **style.css**, các bạn có thể mở file css ra và xem thử

Và đây là kết quả mình đã chơi thử

![chơi thử game typing](https://media.techmaster.vn/api/static/9479/c2n5ofc51co7q82829gg)

Cũng không đến nỗi tệ phải không nào 😄😄

* * *

## Chơi lại game

Khi một lượt chơi kết thúc, người chơi có nhu cầu chơi lại game (nhấn vào nút button bên cạnh ô thời gian), lúc này chúng ta sẽ xử lý phần này như sau

```lang-javascript
const btnReload = document.querySelector('.btn-reload');
btnReload.addEventListener('click', function () {
    init();
});
```

Đơn giản khi người chơi muốn chơi lại game, chúng ta chỉ việc gọi function **init()** để khởi tạo lại những thuộc tính ban đầu cho game. Nhưng có điều, lúc trước mình có **disabled** ô input khi **time = 0** để không cho người chơi nhập từ khi thời gian kết thúc. Lúc này trong function **init()** chúng ta cần set lại thuộc tính **disabled = false** để người chơi có thể tiếp tục nhập từ khi lượt chơi mới bắt đầu

```lang-javascript
function init() {
    time = 60;
    index = 0;
    isPlaying = false;

    if (interval) {
        clearInterval(interval);
    }

    inputWordEl.disabled = false;
    inputWordEl.value = '';

    // Chọn ngôn ngữ
    // Đổi vị trí các từ trong mảng
    // Render từ
    // Hiển thị thời gian
    // Highlight từ đầu tiên
}
```

À, ở đây mình có bổ sung thêm một trường hợp nữa

```lang-javascript
    if (interval) {
        clearInterval(interval);
    }
```

Lúc trước khi **time = 0**, mình mới dừng thời gian đếm ngược lại thông qua hàm **clearInterval()**, nhưng trong khi đang chơi game, người chơi có thể chơi lại game khi thời gian chưa kết thúc, để cho thời gian đếm ngược được chuẩn xác, trong function **init()** mình có kiểm tra xem biến **interval** có tồn tại hay không? Nếu nó tồn tại thì mình sẽ **clearInterval** đi

* * *

Ở phần tiếp theo của **Typing Game**, mình sẽ hướng dẫn các bạn các chức năng còn lại bao gồm :

- Cập nhật kết quả
- Chức năng Bảng xếp hạng người chơi

Source code của phần này mình để ở đây nhé : [https://github.com/buihien0109/typing-game-javascript/tree/master/typing-part-2](https://github.com/buihien0109/typing-game-javascript/tree/master/typing-part-2)

Các bạn có thể tham khảo thêm khóa học này nhé:

- Javascript căn bản - Tổng hợp 12 game huyền thoại - [tại đây](https://techmaster.vn/khoa-hoc/ev5/javascript-can-ban-tong-hop-12-game-huyen-thoai).
- Khóa học Web Frontend (HTML, CSS, JavaScript) + ReactJs - [tại đây](https://techmaster.vn/lo-trinh/front-end)