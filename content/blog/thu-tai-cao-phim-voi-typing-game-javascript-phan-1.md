---
title: "Thử tài cào phím với Typing Game - Javascript (phần 1)"
description: "Để phục vụ cho nhu cầu cào phím của anh em, thì bài viết này chúng ta sẽ cùng nhau làm thử một ứng dụng hay nói mĩ miều hơn là một game để luyện tập gõ phím 😁. Mình sẽ gọi nôm na là Typing Game cho nó Tây chút chứ không có gì đâu."
dateString: 24/12/2022
draft: false
tags: ["FrontEnd", "Javascript", "Web", "Game"]
weight: 103
cover:
    image: "/blog/typing-game/typing-game-1.png"
---

> Phận làm trai, gõ phím bình thiên hạ
>
> Chí anh hùng, click chuột định giang sơn

Để phục vụ cho nhu cầu cào phím của anh em, thì bài viết này chúng ta sẽ cùng nhau làm thử một ứng dụng hay nói mĩ miều hơn là một game để luyện tập gõ phím 😁. Mình sẽ gọi nôm na là Typing Game cho nó Tây chút chứ không có gì đâu.

Bây giờ thì cùng bắt đầu thôi nào 😅

* * *

Đây là giao diện game ban đầu của chúng ta

![giao diện bắt đầu game typing](https://media.techmaster.vn/api/static/9479/c2lig1c51codum1dseug)

> Phần sources code đầu game mình để ở đây nhé. Các bạn có thể tham khảo ( [https://github.com/buihien0109/typing-game-javascript/tree/master/typing-start](https://github.com/buihien0109/typing-game-javascript/tree/master/typing-start))

Khi bắt đầu game thì mặc định mình sẽ để ngôn ngữ là tiếng việt, chúng ta có thể chuyển qua lại giữa tiếng anh và tiếng việt nếu thích

OK, bắt tay vào code thôi 😋

```lang-javascript
// Khai báo biến
let string_vietnamese =
    'Tôi trả tiền làm tượng nhưng cũng không hài lòng Anh Tú Thứ năm Chủ khu du lịch An sapa Sa Pa Lào Cai nghĩ cộng đồng mạng nên có cái nhìn chính xác hơn về sự cố liên quan đến tượng Nữ thần Tự do vừa qua Chia sẻ với Zing ông Nguyễn Ngọc Đông chủ khu An sapa thừa nhận rất mệt mỏi trong những ngày qua khi trở thành nạn nhân của cộng đồng mạng';

let string_english =
    'This shows us that the global nature needs our lives in the planet It involved all of us even if in many ways different and unequivocal And in this way it teaches us even more on what we have to do to create a just planet fair and safe from an environmental point of view In brief the Covid pandemic has taught us this interdependence this sharing together';

let language = {
    1: string_vietnamese,
    2: string_english,
};
```

Bởi vì chúng ta có thể chọn qua lại giữa 2 ngôn ngữ tiếng anh và tiếng việt. Nên trong code phần khai báo biến mình có định nghĩa ra 2 biến

- **string_vietnamese** : Chứa nội dung tiếng việt
- **string_english** : Chứa nội dung tiếng anh

và object **language** giúp chúng ta có thể lấy ra nội dung tương ứng khi đổi ngôn ngữ nhé. Ở đây các key "1", "2" của object **language** chính là value của ô select (các bạn có thể quan sát trong mã HTML nhé). Dựa vào value của ô select chúng ta có thể lấy ra ngôn ngữ tương ứng ở trong object **language**

![giá trị select](https://media.techmaster.vn/api/static/9479/c2likms51codum1dsev0)

Tiếp theo mình sẽ thực hiện việc hiển thị các từ lên trên giao diện tùy thuộc vào ngôn ngữ là gì

## Hiển thị các từ

```lang-javascript
const languageEl = document.querySelector('#chose-language');

let words;
let index;

function init() {
    index = 0;

    // Chọn ngôn ngữ
    let languageValue = languageEl.value;
    words = language[languageValue].toLowerCase().split(' ');

    // Đổi vị trí các từ trong mảng
    words = randomWords(words);

    // Render từ
    renderWords(words);

    // Highlight từ đầu tiên
    highlightWord(index);
}
```

Ở bên trên mình định nghĩa **function init()** tác dụng của function này là khởi tạo ra các thuộc tính ban đầu cho game. Vì ban đầu mục đích của mình chỉ là hiển thị các từ lên trên giao diện, nên phần thời gian và điểm số khi bắt đầu game mình sẽ xử lý sau

Trong **function init()** này, mình chia ra thực hiện một số công việc nhỏ:

- Chọn ngôn ngữ
- Đổi vị trí các từ trong mảng
- Render từ ra ngoài giao diện
- Highlight từ đầu tiên

Bây giờ chúng ta sẽ đi cụ thể vào từng công việc nhé

### Chọn ngôn ngữ

Để biết người chơi đang chọn ngôn ngữ nào thì chúng ta sẽ lấy ra **value** tương ứng trong ô select. Nhưng để lấy được **value** ra thì chúng ta phải truy cập vào ô select trước nhé

```lang-javascript
let languageValue = languageEl.value;
```

Sau khi lấy được value rồi, công việc tiếp theo của chúng ta là dựa vào value đó và lấy ra nội dung đoạn văn tương ứng trong **object language** và tách nội dung đó thành mảng các từ sử dụng **split()**

```lang-javascript
words = language[languageValue].toLowerCase().split(' ');
```

Để thêm phần tự tin mình đã làm đúng thì mọi người có thể **console.log(words)** kết quả ra kiểm tra nhé.

Mình nên kiểm tra kết quả ở từng phần một trước khi chuyển sang phần tiếp theo, để chẳng may có lỗi sai còn biết fix ở chỗ nào, chứ làm một lèo, sau kiểm tra lại, lại không biết sai ở đâu 😅 😅

Nếu bước đầu tiên thấy ổn rồi, mình sẽ chuyển qua bước tiếp theo nhé

### Đổi vị trí các từ trong mảng

```lang-javascript
function randomWords(arr) {
    return arr.sort(function () {
        return Math.random() - 0.5;
    });
}
```

Có nhiều cách để chúng ta có đảo vị trí của các phần tử trong mảng (tùy các bạn lựa chọn). Ở trong function **randomWords()** mình sẽ tận dụng các phương thức có sẵn trong Javascript như **sort()**, **Math.random()** để có thể đảo vị trí các phần tử trong mảng

Nếu bạn nào quên cách sử dụng có thể xem lại ở đây nhé

- [Sử dụng sort() method](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort?retiredLocale=vi)
- [Sử dụng Math.random()](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Math/random)

### Render từ ra ngoài giao diện

```lang-javascript
const wordsEl = document.querySelector('#words');

function renderWords(arr) {
    wordsEl.innerHTML = '';
    for (let i = 0; i < arr.length; i++) {
        const w = arr[i];
        wordsEl.innerHTML += `
            <span>${w}</span>
        `;
    }
}
```

Mục đích của function **renderWords()** giúp chúng ta có thể hiển thị các từ lên trên giao diện

Tham số **arr** chính là một mảng các từ

Trước khi chúng ta render các từ ra ngoài giao diện, chúng ta sẽ clear hết nội dung trong phần tử **wordsEl** (đây chính là nơi chúng ta sẽ render vào)

Sau đó sử dụng vòng lặp for để duyệt qua các phần tử của mảng arr (mỗi phần tử của mảng là chính là 1 từ)

Với mỗi vòng lặp, mình sẽ tạo ra một cấu trúc HTML của từ (mỗi từ sẽ được bọc trong 1 thẻ **span**). Sau đó mình chỉ cần chèn các giá trị tương ứng của từ vào trong thẻ **span** thôi, ở đây để chèn giá trị mình sử dụng cú pháp của template string trong **ES6** (${}) nhưng các bạn nên nhớ phải đặt trong dấu **backtick** nhé (``) chứ không phải dấu nháy kép đâu.

### Highlight từ đầu tiên

```lang-javascript
function highlightWord(index) {
    let spans = document.querySelectorAll('#words span');
    Array.from(spans).map((span) => span.classList.remove('highlight'));
    spans[index].classList.add('highlight');
}
```

Để highlight từ đầu tiên khi bắt đầu game, mình sẽ truyền **index** vào, **index** ở đây chính là vị trí của từ đó ở trong mảng **words**. Về sau khi muốn highlight các từ khác mình chỉ cần truyền **index** phù hợp vào là được

Muốn từ đó được highlight lên, mình chỉ cần thêm class **"highlight"** vào thẻ **span** có chỉ số tương ứng với chỉ số **index** ở trong **spans** nhé

Vì trong một thời điểm chỉ có 1 từ được highlight lên, vì vậy trước khi highlight từ đó mình sẽ remove class **"highlight"** ở tất cả các thẻ span, sau đó mới thêm class **"highlight"** vào thẻ span có chỉ số là index, điều này đảm bảo chỉ có 1 từ được highlight

Sau đó định nghĩa xong các công việc nhỏ rồi, tiếp đến chúng ta sẽ gọi function init() để xem kết quả trông như thế nào 😄

```lang-javascript
window.onload = init;
```

Ở đây mình lắng nghe sự kiện **onload** để đảm bảo HTML, CSS load xong thì mới thực hiện gọi **function init**

Và đây là thành quả sau ban đầu quá trình miệt mài viết code của chúng ta 😄

![Kết quả ban đầu typing game](https://media.techmaster.vn/api/static/9479/c2ll2s451codum1dsf00)

Với kết quả ban đầu, mình đã render thành công các từ ra ngoài giao diện và đã highlight được từ đầu tiên (mặc định là hiển thị tiếng Việt nhé)

Vậy câu hỏi tiếp theo, bây giờ mình muốn hiển thị các từ tiếng Anh ra thì làm như thế nào, vì bên trên mình mới hiển thị các từ tiếng Việt

OK, bây giờ mình sẽ tiếp tục xử lý để chúng ta có thể hiển thị được tiếng Anh

```lang-javascript
languageEl.addEventListener('change', function () {
    init();
});
```

Game là dễ phải không nào 😁😁 !!! Chỉ cần phần select chọn ngôn ngữ được thay đổi giá trị, thì lúc này **function init()** sẽ được gọi lại và render ra ngôn ngữ tương ứng với giá trị mình chúng ta đã chọn trong ô select

![đổi ngôn ngữ typing game](https://media.techmaster.vn/api/static/9479/c2ll5ls51codum1dsf0g)

* * *

Ở phần tiếp theo của bài viết này, mình sẽ hướng dẫn các bạn các chức năng bao gồm :

- Đếm ngược thời gian
- Xử lý khi gõ từ
- Cập nhật kết quả

Source code của phần này mình để ở đây nhé : [https://github.com/buihien0109/typing-game-javascript/tree/master/typing-part-1](https://github.com/buihien0109/typing-game-javascript/tree/master/typing-part-1)

Các bạn có thể tham khảo thêm khóa học này nhé:

- Javascript căn bản - Tổng hợp 12 game huyền thoại - [tại đây](https://techmaster.vn/khoa-hoc/ev5/javascript-can-ban-tong-hop-12-game-huyen-thoai).
- Khóa học Web Frontend (HTML, CSS, JavaScript) + ReactJs - [tại đây](https://techmaster.vn/lo-trinh/front-end)