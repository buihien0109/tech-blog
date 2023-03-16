---
title: "Làm dự án Shopping Cart với Javascript (phần 2)"
description: "Tiếp tục với bài hướng dẫn Làm dự án Shopping Cart. Ở bài viết Làm dự án Shopping Cart với Javascript (phần 1) chúng ta đã làm được các chức năng cơ bản như"
dateString: 23/12/2022
draft: false
tags: ["FrontEnd", "Javascript", "Web"]
weight: 102
cover:
    image: "/blog/shopping-cart/shopping-cart-2.png"
---

Tiếp tục với bài hướng dẫn Làm dự án Shopping Cart. Ở bài viết [Làm dự án Shopping Cart với Javascript (phần 1)](https://techmaster.vn/posts/36493/lam-du-an-shopping-cart-voi-javascript-phan-1) chúng ta đã làm được các chức năng cơ bản như :

- Render danh sách sản phẩm từ 1 data có sẵn
- Cập nhật số lượng sản phẩm hiện có trong giỏ hàng
- Xóa sản phẩm khỏi giỏ hàng

Trong bài viết này mình sẽ tiếp tục làm các chức năng còn lại bao gồm:

- Thay đổi số lượng sản phẩm
- Cập nhật tổng tiền
- Áp dụng mã giảm giá

Bắt đầu thôi nào 😊😊

## Thay đổi số lượng sản phẩm

Khi người dùng muốn thay đổi số lượng của một sản phẩm, họ có thể nhập một số nguyên dương bất kỳ vào ô số lượng, lúc này chúng ta cần tính toán lại tổng số lượng sản phẩm có trong giỏ hàng và cập nhật lên trên giao diện

Để làm điều này, lúc render danh sách sản phẩm ra ngoài giao diện, chúng ta sẽ gán sự kiện **onchange** cho ô input, để mỗi khi số lượng sản phẩm thay đổi, nó sẽ kích hoạt sự kiện này

```javascript
function renderUI(arr) {
   // ...

    for (let i = 0; i < arr.length; i++) {
        const p = arr[i];
        productsEle.innerHTML += `
        // ...

        <div class="quantity">
            <input
                type="number"
                class="quantity"
                step="1"
                value="${p.count}"
                onchange="changeTotalProduct(${p.id}, event)"
            >
        </div>
        `;
    }
}
```

Khi số lượng sản phẩm thay đổi mình sẽ gọi function **changeTotalProduct()** để cập nhật lại số lượng thông qua id, và tham số **event** trong hàm xử lý sự kiện sẽ giúp chúng ta có thể lấy được giá trị số lượng trong ô input

```javascript
// Thay đổi số lượng sản phẩm
function changeTotalProduct(id, e) {
    for (let i = 0; i < products.length; i++) {
        if (products[i].id == id) {
            products[i].count = Number(e.target.value);
        }
    }
    renderUI(products);
}
```

Trong function **changeTotalProduct()** mình định nghĩa ở trên, chúng ta sẽ duyệt qua mảng products mockup ban đầu để tìm ra sản phẩm có id = id truyền vào. Khi tìm được sản phẩm phù hợp, lúc này chúng ta sẽ cập nhật lại giá trị **count** = giá trị trong ô input.

Ở đây để lấy giá trị của ô input thông qua biến event trong hàm xử lý sự kiện, chúng sẽ sử dụng cú pháp **e.target.value** và hàm **Number()** sẽ giúp convert giá trị đó thành kiểu **number**

Sau khi cập nhật xong giá trị count cho sản phẩm, chúng ta sẽ gọi lại function **renderUI()** để tiến hành cập nhật bên trên giao diện

## Cập nhật tổng tiền

Trong phần cập nhật tổng tiền này, chúng ta sẽ cập nhật 3 giá trị:

- Subtotal : Số tiền ban đầu
- VAT : Thuế giá trị gia tăng
- Total : Số tiền cuối cùng mà người mua phải trả

Công thức tính mình sẽ áp dụng như sau:

- Subtotal = Số lượng * Giá tiền từng sản phẩm
- VAT = Subtotal * 5%
- Total = Subtotal + VAT

> Ở phần này mình chưa áp dụng mã giảm giá nhé. Nếu có mã giảm giá thì công thức sẽ khác, phần mã giảm giá mình sẽ xử lý sau

Bây giờ tiến hành tính tiền cho khách hàng của mình thôi nào. Cái gì liên quan đến tiền nong đều phải cẩn thận, hihi 😅😅

```javascript
// Truy cập vào các thành phần
let subTotalEl = document.querySelector('.subtotal span');
let vatEl = document.querySelector('.vat span');
let totalEle = document.querySelector('.total span');

// Cập nhật tổng tiền
function updateTotalMoney(arr) {
    // Tính tổng tiền cart
    let totalMoney = 0;

    for (let i = 0; i < arr.length; i++) {
        const p = arr[i];
        totalMoney += p.count * p.price;
    }

    // Cập nhật tiền lên trên giao diện
    subTotalEl.innerText = convertMoney(totalMoney);
    vatEl.innerText = convertMoney(totalMoney * 0.05);
    totalEle.innerText = convertMoney(totalMoney * 1.05);
}
```

Đầu tiên thì mình sẽ truy cập vào các thành phần, nơi mà chứa các loại tiền hiển thị bên trên giao diện, để tiếp theo khi chúng ta tính toán được các loại tiền sẽ cập nhật nội dung cho các thành phần đó

Để cập nhật tổng tiền cho giỏ hàng, mình đã định nghĩa function **updateTotalMoney()** và truyền vào **arr** (chính là danh sách sản phẩm mình muốn tính tiền)

Ở trong function **updateTotalMoney()** chúng ta sẽ tiến hành xử lý như thế nào?

Ban đầu chúng ta định nghĩa biến **totalMoney = 0** (đây chính là giá trị **Subtotal**). Tiếp đến sử dụng **vòng lặp for** qua mảng arr. Với mỗi vòng lặp (tức là mỗi sản phẩm) chúng ta sẽ lấy **count (số lượng sản phẩm) * price (giá mỗi sản phẩm)** được kết quả bao nhiêu thì cộng dồn với gái trị **totalMoney**

Cuối cùng, chúng ta sẽ cập nhật các giá trị tương ứng lên trên giao diện dựa vào công thức đã được tính toán ban đầu

OK, thế là xong phần định nghĩa function **updateTotalMoney**, tiếp theo là gọi ra, câu hỏi bây giờ là gọi ra ở đâu và gọi ra khi nào?

Thì khi trang web vừa load xong, thì ngoài việc hiển thị các sản phẩm, cập nhật số lượng sản phẩm có trong giỏ hàng thì đồng thời chúng ta cũng tiến hành cập nhật tổng tiền cho nó. Tức là trong function **renderUI()** chúng ta sẽ gọi function **updateTotalMoney()** vừa định nghĩa ở trên

```javascript
function renderUI(arr) {
    ...

    // Cập nhật số lượng sản phẩm trong cart
    ...

    // Cập nhật tổng tiền
    updateTotalMoney(arr);

    // Kiểm tra mảng rỗng
    ...

    // Vòng lặp for
    ...
}
```

Bây giờ chúng ta cùng xem kết quả trên giao diện nào 😆

![cập nhật tổng tiền cho giỏ hàng](https://media.techmaster.vn/api/static/9479/c2kv2pk51codum1dseng)

* * *

Chức năng cuối cùng dự án này, chính là **áp dụng mã giảm giá** vào giỏ hàng

Mua hàng mà có mã giảm giá thì ai mà không thích chứ 😆😆

## Áp dụng mã giảm giá

Để áp dụng được mã giảm giá, chúng ta cần có danh sách các mã giảm giá có hiệu lực

```javascript
// Danh sách promotion code (Mã giảm giá)
let promotionCode = {
    A: 10,
    B: 20,
    C: 30,
    D: 40,
};
```

Mã giảm giá mình sẽ lưu trong object **promotionCode** : Với **key "A"** giảm **10%**, **key "B"** giảm **20%**, ...

```javascript
// Kiểm tra mã giảm giá
let inputPromotion = document.querySelector('#promo-code');

function checkPromotion() {
    let value = inputPromotion.value;
    if (promotionCode[value]) {
        return promotionCode[value];
    }
    return 0;
}
```

Ở trên mình định nghĩa function **checkPromotion()**, mục đích của function này là kiểm tra xem giá trị mã giảm giá trong ô input có chính xác hay không? Nếu chính xác thì sẽ trả về % được giảm, nếu không thì trả về 0 (không được giảm giá)

Tiếp đến khi chúng ta cập nhật tổng tiền cho đơn hàng, cần bổ sung thêm trường hợp có mã giảm giá. Bây giờ phải điều chính lại công thức 1 tí rồi

- Subtotal = Số lượng * Giá tiền từng sản phẩm
- VAT = Subtotal * 5%
- Discount = Subtotal * % được giảm
- Total = Subtotal + VAT - Discount

Ok, bây giờ áp dụng vào function **updateTotalMoney()** thôi nào

```javascript
let discount = document.querySelector('.discount');
let discountEle = document.querySelector('.discount span');

// Cập nhật tổng tiền
function updateTotalMoney(arr) {
    // Tính tổng tiền cart
    let totalMoney = 0;
    let discountMoney = 0;

    for (let i = 0; i < arr.length; i++) {
        const p = arr[i];
        totalMoney += p.count * p.price;
    }

    // Có mã giảm giá hay không?
    // Mã giảm giá có hợp lệ hay không?
    let data = checkPromotion();

    if (data) {
        discountMoney = (totalMoney * data) / 100;
        discount.classList.remove('hide');
    } else {
        discount.classList.add('hide');
    }

    // Cập nhật tiền lên trên giao diện
    subTotalEl.innerText = convertMoney(totalMoney);
    vatEl.innerText = convertMoney(totalMoney * 0.05);
    discountEle.innerText = convertMoney(discountMoney);
    totalEle.innerText = convertMoney(totalMoney * 1.05 - discountMoney);
}
```

Sau khi cập nhật lại function **updateTotalMoney()** mình có bổ sung thêm biến **discountMoney = 0** (tổng tiền giảm giá)

Tiếp đến kiểm tra xem mã giảm giá có hiệu lực hay không bằng cách gọi function **checkPromotion()**. Vì function này trả về giá trị % được giảm (nếu có), nên mình có lưu vào biến **data** để có thể sử dụng về sau

Tiếp theo, nếu **data** có giá trị (tức là mã giảm giá có hiệu lực). Lúc này mình sẽ tính toán số tiền được giảm dựa theo công thức ở trên. Đồng thời sẽ hiển thị **component discount** lên để người dùng nhìn thấy (mặc định ban đầu là ẩn). Trường hợp mã giảm giá không chính xác, thì chỉ việc ẩn **component discount** đi thôi

Cuối cùng là lắng nghe sự kiện khi người dùng click vào button để áp dụng mã giảm giá

```javascript
let btnPromotion = document.querySelector('.promotion button');

btnPromotion.addEventListener('click', function () {
    updateTotalMoney(products);
});
```

Ở đây khi nút " **Áp dụng mã giảm giá được kích hoạt**", đơn giản là chúng ta gọi lại function **updateTotalMoney()** để nó tính toán lại toàn bộ các giá trị tiền trong trường hợp mã giảm giá có đúng hoặc không

Rồi cùng xem kết nào

![áp dụng mã giảm giá](https://media.techmaster.vn/api/static/9479/c2kvh5k51codum1dseo0)

Ở hình ảnh trên mình đã áp dụng thành công mã giảm giá "A" và được giảm 10% rồi đó 😍

* * *

Source code của phần này mình để ở đây nhé : [https://github.com/buihien0109/shopping-cart-javascript/tree/master/shopping-cart-part-2](https://github.com/buihien0109/shopping-cart-javascript/tree/master/shopping-cart-part-2)

> Các bạn có thể tham khảo thêm khóa học này nhé : [https://techmaster.vn/khoa-hoc/ev5/javascript-can-ban-tong-hop-12-game-huyen-thoai](https://techmaster.vn/khoa-hoc/ev5/javascript-can-ban-tong-hop-12-game-huyen-thoai)
>
> Khóa học Web Frontend - [tại đây](https://techmaster.vn/lo-trinh/front-end)