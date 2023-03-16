---
title: "Làm dự án Shopping Cart với Javascript (phần 1)"
description: "Chào các bạn, hôm nay mình sẽ hướng dẫn các bạn tạo một trang shopping cart đơn giản bằng JS, HTML và CSS. Mình không sử dụng bất kì thư viện và framework nào để code giao diện cũng như các chức năng của shopping cart."
dateString: 22/12/2022
draft: false
tags: ["FrontEnd", "Javascript", "Web"]
weight: 101
cover:
    image: "/blog/shopping-cart/shopping-cart-1.png"
---

Chào các bạn, hôm nay mình sẽ hướng dẫn các bạn tạo một trang shopping cart đơn giản bằng JS, HTML và CSS. Mình không sử dụng bất kì thư viện và framework nào để code giao diện cũng như các chức năng của shopping cart.

Trong bài viết này mình sẽ hướng dẫn các bạn một số chức năng trong giỏ hàng như sau :

- Render danh sách sản phẩm từ 1 data có sẵn
- Cập nhật số lượng sản phẩm hiện có trong giỏ hàng
- Xóa sản phẩm khỏi giỏ hàng
- Thay đổi số lượng sản phẩm
- Cập nhật tổng tiền
- Áp dụng mã giảm giá

Đây là giao diện giỏ hàng ban đầu của chúng ta

![template-shopping-cart](https://media.techmaster.vn/api/static/9479/c2k745451codum1dsef0)

> Source code HTML, CSS mình để [ở đây,](https://github.com/buihien0109/shopping-cart-javascript/tree/master/shopping-cart-template) các bạn có thể tham khảo nhé =))

Để linh động số lượng sản phẩm sẽ hiển thị trong giỏ hàng thì mình sẽ mockup một danh sách số lượng sản phẩm, mỗi sản phẩm ở đây sẽ là một đối tượng bao gồm các thuộc tính như : id, tên sản phẩm, mô tả của sản phẩm, giá sản phẩm, hình ảnh và số lượng của từng sản phẩm ở trong giỏ hàng

```javascript
// Random id ngẫu nhiên trong khoảng 0 -> 100000
function randomId() {
    return Math.floor(Math.random() * 100000);
}

// Danh sách sản phẩm
let products = [
    {
        id: randomId(),
        name: 'Áo kiểu nữ cam đất phối cổ trắng dập ly',
        description:
            'Lorem ipsum dolor sit amet consectetur, adipisicing elit. Quae, velit.',
        price: 250000,
        image:
            'https://image.yes24.vn/Upload/ProductImage/anhduong201605/1947415_L.jpg?width=550&height=550',
        count: 1,
    },
    {
        id: randomId(),
        name: 'Áo trắng bèo lé đen tay loe dễ thương',
        description:
            'Lorem ipsum dolor sit amet consectetur, adipisicing elit. Quae, velit.',
        price: 350000,
        image:
            'https://image.yes24.vn/Upload/ProductImage/anhduong201605/1914666_L.jpg?width=550&height=550',
        count: 1,
    },
];
```

Bên trên mình đã mockup 1 mảng bảo gồm 2 sản phẩm và lưu chúng trong biến **products**

Ngoài ra mình có định nghĩa function **randomId()** để random ngẫu nhiên Id cho từng sản phẩm

Sau khi định nghĩa xong mảng các sản phẩm hiện có, bước tiếp theo mình sẽ hiển thị nó ra ngoài giao diện

## Render sản phẩm ra ngoài giao diện

Để render sản phẩm mình sẽ định nghĩa function **renderUI()**

```javascript
let productsEle = document.querySelector('.products');

function renderUI(arr) {
    // Code xử lý
}
```

Trong function **renderUI** mình sẽ truyền vào 1 mảng, dựa vào mảng này chúng ta sẽ render từng thành phần tương ứng, còn **productsEle** chính là nơi chúng ta sẽ render từng sản phẩm vào đó.

```javascript
function renderUI(arr) {
    productsEle.innerHTML = '';

    if (arr.length == 0) {
        productsEle.insertAdjacentHTML(
            'afterbegin',
            '<li>Không có sản phẩm nào trong giỏ hàng</li>'
        );
        document.querySelector('.option-container').style.display = 'none';
        return;
    }
}
```

Trước khi bắt đầu render, chúng ta sẽ clear hết tất cả nội dung ở trong phần tử **productsEle** bởi vì trong giao diện ban đầu, mình có fix cứng 2 sản phẩm bằng mã HTML để mọi người tiện theo dõi, nhưng bây giờ chúng ta đã có 1 danh sách sản phẩm vừa mockup, điều này không cần thiết nữa

Tiếp theo mình sẽ kiểm tra thêm trường hợp arr truyền vào có rỗng hay không, nếu mảng ban đầu là rỗng, tức là không có sản phẩm nào trong giỏ hàng, lúc này chúng ta sẽ hiển thị một message lên trên giao diện với nội dung **"Không có sản phẩm nào trong giỏ hàng"** để thông báo cho người dùng.

Đồng thời chúng ta cũng ẩn thành phần **".option-container"** đi, vì đây là nơi hiển thị mã code giảm giá và tổng tiền đơn hàng, khi không có sản phẩm nào trong giỏ hàng thì những thành phần này không cần thiết phải hiển thị lên

Trường hợp mảng arr có dữ liệu lúc này chúng ta sẽ tiếp tục xử lý như sau :

```javascript
function renderUI(arr) {
    // ...

    for (let i = 0; i < arr.length; i++) {
        const p = arr[i];
        productsEle.innerHTML += `
            <li class="row">
                <div class="col left">
                    <div class="thumbnail">
                        <a href="#">
                            <img src="${p.image}" alt="${p.name}">
                        </a>
                    </div>
                    <div class="detail">
                        <div class="name"><a href="#">${p.name}</a></div>
                        <div class="description">
                            ${p.description}
                        </div>
                        <div class="price">${convertMoney(p.price)}</div>
                    </div>
                </div>
                <div class="col right">
                    <div class="quantity">
                        <input type="number" class="quantity" step="1" value="${ p.count}" >
                    </div>
                    <div class="remove">
                        <span class="close">
                            <i class="fa fa-times" aria-hidden="true"></i>
                        </span>
                    </div>
                </div>
            </li>
        `;
    }
}
```

Ở đây mình sẽ sử dụng vòng lặp **for** để duyệt qua các phần tử của mảng (mỗi phần tử của mảng là object chứa dữ liệu của sản phẩm)

Với mỗi vòng lặp, mình sẽ tạo ra một cấu trúc HTML của 1 sản phẩm (các bạn có thể copy trong file html ban đầu nhé, toàn bộ thẻ **li** trong **ul.products** đấy). Sau đó mình chỉ cần chèn các giá trị tương ứng vào thôi, ở đây để chèn giá trị mình sử dụng cú pháp của **template string** trong ES6 (${}) nhưng các bạn nên nhớ phải đặt trong dấu **backtick** nhé (``) chứ không phải dấu nháy kép đâu.

À !!! còn một điều nữa, vì để hiển thị giá sản phẩm ra ngoài giao diện cho giống phong cách Việt Nam tí, mình có định nghĩa thêm function **convertMoney** để convert price sang định dạng VND

```javascript
// Convert number to money VND
function convertMoney(num) {
    return num.toLocaleString('it-IT', { style: 'currency', currency: 'VND' });
}
```

Để xem kết quả xem sản phẩm đã hiển thị lên chưa, bây giờ chúng ta sẽ gọi function **renderUI** và truyền vào **products** (danh sách sản phẩm đã mockup ban đầu)

```javascript
window.onload = renderUI(products);
```

Ở đây mình lắng nghe sự kiện **onload** để đảm bảo HTML, CSS load xong thì mới thực hiện gọi function

Và đây là thành quả ban đầu của chúng ta

![render sản phẩm](https://media.techmaster.vn/api/static/9479/c2k6s9k51codum1dsedg)

Nghe vẻ kết quả có vẻ nuột đấy 😋 😋

Bước tiếp theo chúng ta sẽ cập nhật toàn bộ số lượng sản phẩm hiện có trong giỏ hàng và hiển thị lên

## Cập nhật số lượng sản phẩm trong giỏ hàng

Để cập nhật số lượng mình sẽ định nghĩa function **updateTotalItem(arr)**

```javascript
// Cập nhật số lượng sản phẩm
function updateTotalItem(arr) {
    let total = 0;
    for (let i = 0; i < arr.length; i++) {
        const p = arr[i];
        total += p.count;
    }
    return total;
}
```

Ban đầu khi chưa biết số lượng sản phẩm hiện có là bao nhiều thì chúng ta định nghĩa biến **total = 0**. Tiếp theo sử dụng vòng lặp qua mảng arr. Với mỗi vòng lặp chúng ta sẽ cộng dồn giá trị của **total** với thuộc tính **count** của từng sản phẩm

Cuối cùng khi vòng lặp kết thúc chúng ta sẽ trả về giá trị của **total**

Sau khi định nghĩa trong function **updateTotalItem()** thì bây giờ chúng ta sẽ gọi ra thôi, để xem nó hiển thị như thế nào, nếu có lỗi thì còn biết đường mà fix =))

OK, thì ở đây mình sẽ gọi ở trong function **renderUI()**

```javascript
function renderUI(arr) {
    productsEle.innerHTML = '';

    // Cập nhật số lượng sản phẩm trong cart
    let countEle = document.querySelector('.count');
    countEle.innerText = `${updateTotalItem(arr)} items in the bag`;

    // Kiểm tra mảng rỗng
    ...

    // Vòng for
    ...
}
```

Vì nơi hiện thị số lượng sản phẩm nằm trong ".count" nên để set lại nội dung cho phần tử, ban đầu mình sẽ truy cập vào phần tử đó, sau đó set lại nội dung bằng cách gọi function **updateTotalItem(arr)** vừa mới định nghĩa ở bên trên

Quay lại giao diện và xem tổng số lượng sản phẩm đã được cập nhật hay chưa

![update-count-product](https://media.techmaster.vn/api/static/9479/c2k6uis51codum1dseeg)

wow, Ơ mây zing gút chóp 😆

Cuối cùng sẽ là chức năng xóa sản phẩm trong giỏ hàng

## Xóa sản phẩm trong giỏ hàng

Để xóa sản phẩm trong giỏ hàng, chúng ta sẽ gán sự kiện click cho từng sản phẩm lúc mà chúng ta thực hiện render

```javascript
function renderUI(arr) {
    // ...

    // Vòng for
    for (let i = 0; i < arr.length; i++) {
        const p = arr[i];
        productsEle.innerHTML += `
        // ...

        <div class="remove">
            <span class="close" onclick="removeItem(${p.id})">
                <i class="fa fa-times" aria-hidden="true"></i>
            </span>
        </div>
        `;
    }
}
```

Khi người dùng click vào nút xóa, chúng ta sẽ gọi function **removeItem()** và truyền vào **id** của sản phẩm mà chúng ta muốn xóa

Function **removeItem** mình sẽ định nghĩa như sau :

```javascript
// Remove item trong cart
function removeItem(id) {
    for (let i = 0; i < products.length; i++) {
        if (products[i].id == id) {
            products.splice(i, 1);
        }
    }
    renderUI(products);
}
```

Ở trên chúng ta sẽ duyệt qua tất cả sản phẩm trong products, với mỗi sản phẩm chúng ta sẽ kiểm tra xem id của sản phẩm đó có trùng với id truyền vào hay không. Nếu id trùng nhau, lúc này chúng ta sẽ xóa sản phẩm đó ra khỏi mảng products bằng method **splice()**

Ở trên chúng ta mới xóa sản phẩm ra khỏi mảng ban đầu thôi, chứ trên giao diện chưa có chuyện gì xảy ra đâu. Để sản phẩm đó biến mất trên giao diện, chúng ta cần gọi lại function **renderUI()** để nó render lại sản phẩm sau khi xóa lên trên giao diện =))

* * *

Ở [phần tiếp theo](https://techmaster.vn/posts/36494/lam-du-an-shopping-cart-voi-javascript-phan-2) của bài viết này, mình sẽ hướng dẫn các bạn các chức năng còn lại bao gồm :

- Thay đổi số lượng sản phẩm
- Cập nhật tổng tiền
- Áp dụng mã giảm giá

Source code của phần này mình để ở đây nhé : [https://github.com/buihien0109/shopping-cart-javascript/tree/master/shopping-cart-part-1](https://github.com/buihien0109/shopping-cart-javascript/tree/master/shopping-cart-part-1)

> Các bạn có thể tham khảo thêm khóa học này nhé : [https://techmaster.vn/khoa-hoc/ev5/javascript-can-ban-tong-hop-12-game-huyen-thoai](https://techmaster.vn/khoa-hoc/ev5/javascript-can-ban-tong-hop-12-game-huyen-thoai)