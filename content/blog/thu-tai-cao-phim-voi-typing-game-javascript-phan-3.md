---
title: "Thử tài cào phím với Typing Game - Javascript (phần 3)"
description: "Trong nội dung bài viết hướng dẫn Typing Game (phần 3), mình sẽ tiếp tục làm các chức năng còn lại của Typing game bao gồm ..."
dateString: 26/12/2022
draft: false
tags: ["FrontEnd", "Javascript", "Web", "Game"]
weight: 105
cover:
    image: "/blog/typing-game/typing-game-3.png"
---

Chào mọi người, ở bài viết [Typing Game (phần 2)](https://techmaster.vn/posts/36506/thu-tai-cao-phim-voi-typing-game-javascript-phan-2) mình đã hướng dẫn các bạn các chức năng : Đếm ngược thời gian, xử lý khi người chơi gõ từ, chơi lại game

> Không biết trong quá trình thực hiện các bạn có gặp lỗi lầm gì không, hãy comment cho mình biết nhé

Trong nội dung bài viết hướng dẫn Typing Game (phần 3), mình sẽ tiếp tục làm các chức năng còn lại của Typing game bao gồm :

-   Cập nhật kết quả ngươi chơi
-   Demo chức năng bảng xếp hạng người chơi

**Ok, Let's go !!** 😁

## Cập nhật kết quả ngươi chơi

Khi nào chúng ta cập nhật kết quả cho người?

Tất nhiên là khi hết thời gian rồi phải không nào (time = 0). Vậy khi cập nhật kết quả cho người chơi, chúng ta cần hiển thị các thông tin gì

![thông tin người chơi](https://media.techmaster.vn/api/static/9479/c2n6v9c51co7q82829l0)

Từ ảnh trên, chúng ta có thể dễ dàng nhận thấy, các thông tin cần cập nhật bao gồm :

-   Chỉ số WPM : Số từ gõ mỗi phút
-   Số ký tự gõ đúng
-   Số ký tự gõ sai
-   Tổng số ký tự đã gõ
-   Độ chính xác khi gõ
-   Số từ gõ đúng
-   Số từ gõ sai

Rất nhiều thông tin phải không nào, chúng ta phải tự mình tính toán thôi

```lang-javascript
function countdownTime() {
   ...

    if (time == 0) {
        ...

        // Cập nhật kết quả người chơi
        updateInfoPlayer();
    }
}
```

Trong function **countdownTime()**, khi **time = 0**, mình sẽ gọi function **updateInfoPlayer()** để tiến hành cập nhật kết quả cho người chơi

```lang-javascript
// Thông tin người chơi
const wordsCorrectEl = document.querySelector('.words-correct');
const wordsWrongEl = document.querySelector('.words-wrong');
const wpmCountEl = document.querySelector('.wps-count');
const characterCorrectCountEl = document.querySelector('.character-correct-count');
const characterWrongCountEl = document.querySelector('.character-wrong-count');
const characterCountEl = document.querySelector('.character-count');
const percentCorrectEl = document.querySelector('.percent-correct');

function updateInfoPlayer() {
    let spans = document.querySelectorAll('#words span');

    let totalCorrectWords = 0; // Tổng số từ gõ đúng
    let totalWrongWords = 0; // Tổng số từ gõ sai
    let totalCorrectCharacters = 0; // Tổng số ký tự gõ đúng
    let totalWrongCharacters = 0; // Tổng số ký tự gõ sai
    let totalCharacters = 0; // Tổng số ký tự đã gõ

    for (let i = 0; i < spans.length; i++) {
        // Đếm số từ và ký tự đúng
        if (spans[i].classList.contains('correct')) {
            totalCorrectWords++;
            totalCorrectCharacters += spans[i].innerText.length;
        }
        // Đếm số từ và ký tự sai
        if (spans[i].classList.contains('wrong')) {
            totalWrongWords++;
            totalWrongCharacters += spans[i].innerText.length;
        }
    }
    // Cập nhật số từ đúng - sai
    wordsCorrectEl.innerText = totalCorrectWords;
    wordsWrongEl.innerText = totalWrongWords;

    // Tính toán WPM
    totalCharacters = totalCorrectCharacters + totalWrongCharacters;
    wpm = Math.round(totalCharacters / 5 / (wpmTime / 60));
    wpmCountEl.innerText = `${wpm} WPM`;

    // Cập nhật số ký tự đúng - sai
    characterCorrectCountEl.innerText = totalCorrectCharacters;
    characterWrongCountEl.innerText = totalWrongCharacters;
    characterCountEl.innerText = totalCharacters;

    // Cập nhật phần trăm từ gõ chính xác
    percentCorrectEl.innerText = `${(
        (totalCorrectWords * 100) /
        (totalCorrectWords + totalWrongWords)
    ).toFixed(2)}%`;
}
```

Vì function trên mình cần tính toán rất nhiều thông tin của người chơi nên có hơi dài 1 tí

Để tính toán số từ và ký tự đúng, mình sẽ duyệt qua mảng **spans**, với mỗi vòng lặp mình sẽ kiểm tra xem phần tử span đó có chứa class **'correct'** hay không. Trường hợp phần tử span đó có chứa class **'correct'** (tức là từ này đã gõ đúng), mình sẽ tăng **totalCorrectWords (Tổng số từ gõ đúng)** lên 1 đơn vị, và **totalCorrectCharacters (Tổng số ký tự gõ đúng)** sẽ được cộng dồn với độ dài của nội dung trong thẻ span đó

Với từ và ký tự gõ sai mình cũng làm tương tự bằng việc kiểm tra với class **'wrong'**

![Công thức tính wpm](https://media.techmaster.vn/api/static/9479/c2n756s51co7q82829lg)

Trên mạng có nhiều công thức tính WPM với mức độ phức tạp khác nhau, trong bài viết này, để đơn giản hóa mình đã áp dụng công thức trên

Ở function **updateInfoPlayer()** có một đoạn mà các bạn cần chú ý

```lang-javascript
wpm = Math.round(totalCharacters / 5 / (wpmTime / 60));
```

Trong công thức trên mình có sử dụng **wpmTime** mà không phải là **time**, bởi vì khi mình cập nhật thông tin người chơi, lúc này **time = 0** rồi, nếu mình áp dụng vào công thức trên sẽ cho kết quả là '' **infinity**"

Nên lúc này mình sẽ tạo ra biến **wpmTime = time** lúc khởi tạo chúng ta khởi tạo giá trị **time** ban đầu trong function **init()**

```lang-javascript
let wpmTime;
let wpm
function init() {
    time = 10;
    wpmTime = time;
    wpm = 0;

    // ...
}
```

Các bạn có thể chơi thử để xem mức độ cào phím của mình đang ở mức nào nhé

-   Tốc độ đánh máy thấp: 0-60 WPM
-   Tốc độ đánh máy trung bình: 60-90 WPM
-   Tốc độ đánh máy cao: 90-130 WPM
-   Tốc độ đánh máy chuyên nghiệp: 130-170 WPM

> Tốc độ của mình ở mức trung bình thôi, đủ để toxic với đồng đội trong các pha combat tổng 5 vs 5 😁

## Bảng xếp hạng người chơi

Để tăng thêm phần gay cấn mà có làm thêm chức năng bảng xếp hạng người chơi

Giao diện phần bảng xếp hạng này chúng ta có thể quan sát như sau

![bảng xếp hạng người chơi](https://media.techmaster.vn/api/static/9479/c2n7us451co7q82829mg)

Giao diện trên là mình đã fix cứng code trong mã HTML để các bạn có cái nhìn trực quan cho phần bảng xếp hạng này. Ở đây mình đã ví dụ với 3 người chơi. Chúng ta có thể dễ dàng nhận thấy được, với mỗi người chơi chúng ta cần hiển thị các thông tin gì

-   Ảnh người chơi
-   Tên người chơi
-   Chỉ số wpm
-   Thời gian chơi (định dạng **hh:mm:ss - dd/mm/yyyy**)

Để linh động cho việc hiện thị, không fix cứng code html và chúng ta có thể thêm bản ghi vào bảng xếp hạng khi người chơi kết thúc lượt chơi. Vì vậy mình đã mockup một mảng **ranking** để phục vụ cho điều này, mỗi phần tử trong mảng **ranking** này sẽ đại diện cho thông tin của người chơi

```lang-javascript
// Mock up mảng rank
let ranking = [
    {
        avatar: 'https://images.unsplash.com/photo-1507525428034-b723cf961d3e?ixid=MnwxMjA3fDB8MHxzZWFyY2h8MXx8bmhhJTIwdHJhbmclMjBiZWFjaHxlbnwwfHwwfHw%3D&ixlib=rb-1.2.1&w=1000&q=80',
        username: 'Nguyen Van A',
        wpm: 10,
        time: formatDate(new Date(2021, 3, 27, 11, 33, 30)),
    },
    {
        avatar: 'https://ec.europa.eu/jrc/sites/jrcsh/files/styles/normal-responsive/public/fotolia-92027264european-day-forest-green-forest.jpg?itok=biCWJPQQ',
        username: 'Tran Van B',
        wpm: 500,
        time: formatDate(new Date(2021, 3, 26, 16, 33, 30)),
    },
    {
        avatar: 'https://images.unsplash.com/photo-1569389397653-c04fe624e663?ixid=MnwxMjA3fDF8MHxlZGl0b3JpYWwtZmVlZHwxfHx8ZW58MHx8fHw%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=60',
        username: 'Phan Thi C',
        wpm: 151,
        time: formatDate(new Date(2021, 3, 27, 02, 33, 30)),
    },
];
```

Trong mảng ranking, mình đã mockup thông tin của 3 user, mỗi user bao gồm các thông tin như : **avatar, username, wpm, time**

Riêng đối với thuộc tính **time**, mình có định nghĩa thêm function **formatDate()** để format time theo chuẩn **hh:mm:ss - dd/mm/yyyy**

```lang-javascript
function formatDate(date) {
    let year = date.getFullYear();
    let month = `0${date.getMonth() + 1}`.slice(-2);
    let day = `0${date.getDate()}`.slice(-2);

    let hour = `0${date.getHours()}`.slice(-2);
    let minute = `0${date.getMinutes()}`.slice(-2);
    let second = `0${date.getSeconds()}`.slice(-2);

    return `${hour}:${minute}:${second} - ${day}/${month}/${year}`;
}
```

Function **formatDate()** cũng tương tự như **covertTime()**, để đảm bảo thời gian hiển thị đúng theo định dạng mà mình mong muốn ( **hh:mm:ss - dd/mm/yyyy**)

## Render bảng xếp hạng

```lang-javascript
const tableEl = document.querySelector('tbody');

// render ranking
function renderRanking(arr) {
    // Sắp xếp thứ tự người chơi theo wpm giảm dần
    let arrSort = arr.sort(function (a, b) {
        return b.wpm - a.wpm;
    });

    // Lấy ra danh sách TOP 10
    let arrTop10 = arrSort.slice(0, 10)

    // Render lên giao diện
    tableEl.innerHTML = '';
    for (let i = 0; i < arrTop10.length; i++) {
        const u = arrTop10[i]
        tableEl.innerHTML += `
            <tr>
                <td>${i + 1}</td>
                <td>
                    <img src=${u.avatar} alt="${u.username}">
                </td>
                <td>${u.username}</td>
                <td class="font-weight-bold">${u.wpm}</td>
                <td class="font-italic">${u.time}</td>
            </tr>
        `;
    }
}
```

Để render mảng ranking chúng ta vừa mockup ra ngoài giao diện, mình đã định nghĩa function **renderRanking()** với tham số truyền vào là mảng **arr**

Trong function **renderRanking()** này mình tiến hành chia làm 3 bước:

-   Sắp xếp thứ tự người chơi theo wpm giảm dần
-   Lấy ra danh sách TOP 10
-   Render lên giao diện

Với bước 1 và bước 2 không có gì đặc biệt, riêng đối với bước 3 mình tiến hành xử lý như sau:

Đầu tiên clear hết dữ liệu của table đang fix cứng trong mã html để render dữ liệu từ mảng đã mockup từ trước

```lang-javascript
tableEl.innerHTML = '';
```

Sau đó sử dụng vòng lặp **for** để duyệt qua các phần tử của mảng **arrTop10** (mỗi phần tử của mảng là object chứa thông tin của người chơi)

Với mỗi vòng lặp, mình sẽ tạo ra một cấu trúc HTML của 1 bản ghi ranking (các bạn có thể copy trong file html ban đầu nhé, toàn bộ thẻ **tr** trong **tbody** của table đấy). Sau đó mình chỉ cần chèn các giá trị tương ứng vào thôi, ở đây để chèn giá trị mình sử dụng cú pháp của **template string** trong ES6 (${}) nhưng các bạn nên nhớ phải đặt trong dấu **backtick** nhé (``) chứ không phải dấu nháy kép đâu.

Sau khi định nghĩa xong function **renderRanking()** mình sẽ tiến hành gọi nó trong function **init()**

```lang-javascript
function init() {
    // ...

    // Render bảng xếp hạng người chơi
    renderRanking(ranking);
}
```

Kết quả sau khi mockup và hiển thị bảng xếp hạng

![Kết quả sau khi mockup và hiển thị bảng xếp hạng](https://media.techmaster.vn/api/static/9479/c2ni9hc51co7q82829o0)

Thật tuyệt phải không nào 😁

## Thêm người chơi vào bảng xếp hạng

```lang-javascript
function countdownTime() {
    // ...
    if (time == 0) {
        // ...

        addPlayerToRanking();
    }
}
```

Khi **time = 0**, mình sẽ gọi function **addPlayerToRanking()** để thêm bản ghi của người chơi vào bảng xếp hạng

```lang-javascript
function addPlayerToRanking() {
    // Tạo 1 bản ghi người chơi
    let player = {
        avatar: 'https://images.unsplash.com/photo-1564564321837-a57b7070ac4f?ixid=MnwxMjA3fDB8MHxzZWFyY2h8Nnx8bWFufGVufDB8fDB8fA%3D%3D&ixlib=rb-1.2.1&auto=format&fit=crop&w=500&q=60',
        username: 'Bùi Hiên',
        wpm: wpm,
        time: formatDate(new Date()),
    }

    // Thêm bản ghi vào mảng ranking
    ranking.push(player)

    // Render lại bảng xếp hạng sau khi thêm bản ghi mới
    renderRanking(ranking)
}
```

Vì hiện tại trong game chưa có cơ chế đăng nhập ở biết người chơi hiện tại là ai? Nên ở đây mình có tạo ra biến **player** chưa thông tin người chơi vừa kết thúc lượt chơi của mình

Trong **player** có 2 thông tin mà mình đăng fix cứng là **avatar** và **username**, còn **wpm** là kết quả của người chơi trong lần chơi đó, và **time** là thời gian sau khi lượt chơi kết thúc

Tiếp theo chúng ta sẽ thêm bản ghi **player** vào mảng **ranking**

Và cuối cùng là gọi **renderRanking(ranking)** để render lại bảng xếp hạng sau khi thêm bản ghi mới

Kết quả sau 1 số lượt chơi của mình như sau

![kết quả sau một số lượt chơi game](https://media.techmaster.vn/api/static/9479/c2ni8gk51co7q82829ng)

---

Source code của phần này mình để ở đây nhé : [https://github.com/buihien0109/typing-game-javascript/tree/master/typing-part-3](https://github.com/buihien0109/typing-game-javascript/tree/master/typing-part-3)

Các bạn có thể tham khảo thêm khóa học này nhé:

-   Javascript căn bản - Tổng hợp 12 game huyền thoại - [tại đây](https://techmaster.vn/khoa-hoc/ev5/javascript-can-ban-tong-hop-12-game-huyen-thoai).
-   Khóa học Web Frontend (HTML, CSS, JavaScript) + ReactJs - [tại đây](https://techmaster.vn/lo-trinh/front-end)
