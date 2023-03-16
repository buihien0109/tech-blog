---
title: "Sự khác biệt giữa RUN, CMD và ENTRYPOINT trong Dockerfile"
description: "Trong Dockerfile chúng ta thường xuyên bắt gặp các instructions như CMD, RUN, ENTRYPOINT. Thoạt nhìn, chúng đều được sử dụng để chỉ định và thực thi các lệnh ..."
dateString: 27/12/2022
draft: false
tags: ["Devops", "Docker"]
weight: 106
cover:
    image: "/blog/docker/docker-run-cmd-endpoint.png"
---

## Tổng quan

Trong Dockerfile chúng ta thường xuyên bắt gặp các instructions như CMD, RUN, ENTRYPOINT. Thoạt nhìn, chúng đều được sử dụng để chỉ định và thực thi các lệnh. Nhưng sự khác biệt giữa chúng là gì? Trong bài viết này chúng ta sẽ cùng tìm hiểu về các instructions này

> Tham khảo [Lộ trình DevOps 43 buổi](https://devops.techmaster.vn/) - học trực tuyến có tương tác toàn quốc.

Chúng ta có thể hiểu ngắn gọn là:

- **RUN** thực thi các lệnh command line và một layer mới. Ví dụ: thường được sử dụng để cài đặt các gói phần mềm.
- **CMD** thực hiện lệnh mặc định khi chúng ta khởi tạo container từ image, lệnh mặc định này có thể được ghi đè từ dòng lệnh khi khởi tại container.
- **ENTRYPOINT** khá giống CMD đều dùng để chạy khi khởi tạo container, nhưng ENTRYPOINT không thể ghi đè từ dòng lệnh khi khi khởi tại container.

## Shell and Exec forms

Cả ba lệnh (RUN, CMD và ENTRYPOINT) có thể được chỉ định ở dạng shell form hoặc dạng exec form.

### Shell form

```bash
<instruction> <command>
```

Ví dụ:

```bash
RUN apt-get install ping
CMD echo "Xin chào các bạn"
ENTRYPOINT echo "Xin chào các bạn"
```

Khi instruction được thực thi trong shell form nó sẽ gọi xử lý **/bin/sh -c** bên dưới và xử lý shell xảy ra.

Ví dụ chúng ta có Dockerfile như sau

```bash
FROM ubuntu:latest
ENV name Nguyễn Văn A
ENTRYPOINT echo "Xin chào ${name}"
```

Bây giờ thử build image

```bash
docker build -t hello:latest .
```

Tạo một container từ image vừa build

```bash
docker run --name hello-example hello:latest
```

Kết quả chúng ta đạt được là

```bash
Xin chào Nguyễn Văn A
```

Biến ${name} được thay thế bằng giá trị set trong ENV

### Exec form

Exec form được sử dụng nhiều hơn với **CMD** và **ENTRYPOINT**

```bash
<instruction> ["executable", "param1", "param2", ...]
```

Ví dụ:

```bash
RUN ["apt-get", "install", "ping"]
CMD ["/bin/echo", "Xin chào các bạn"]
ENTRYPOINT ["/bin/echo", "Xin chào các bạn"]
```

Khi lệnh thực thi ở dạng exec form, nó gọi thực thi trực tiếp và xử lý shell không xảy ra.

Ví dụ: chúng ta có Dockerfile sau

```bash
FROM ubuntu:latest
ENV name Nguyễn Văn A
ENTRYPOINT ["/bin/echo", "Xin chào ${name}"]
```

Chúng ta thử build image mới và khởi tạo một container từ image vừa tạo này

Và đây là kết quả

```bash
Xin chào ${name}
```

Biến $name không được thay thế

**Vậy làm thế nào thể run bash**

Nếu muốn run _bash_ thì ta sử dụng exec form với _/bin/bash_ như một executable. Khi đó xử lý shell bình thường sẽ được thực thi. Chúng ta sẽ thực hiện như sau

Ví dụ

```bash
FROM ubuntu:latest
ENV name Nguyễn Văn A
ENTRYPOINT ["/bin/bash", "-c", "echo Xin chào ${name}"]
```

Ở đây chúng ta sử dụng executable **/bin/bash**

Kết quả đạt được như sau

```bash
Xin chào Nguyễn Văn A
```

Khi container chạy output sẽ in ra biến ${name}

#### **RUN**

RUN được thực thi trong quá trình build. Khi ta build một Docker image, Docker sẽ đọc các câu lệnh trong chỉ dẫn RUN và build tới một layer mới trong image sử dụng.

Thường thì ta sẽ thấy nhiều lệnh RUN trong dockerfile. RUN có 2 cấu trúc

```bash
- RUN < command > (shell form)
- RUN ["executable", "param1", "param2"]  (exec form) 
```

Ví dụ: Cài đặt nhiều package khi sử dụng câu lệnh RUN

```bash
RUN apt-get update && apt-get install -y \
  ping \
  nano \
  git \
```

Trong shell form, ta có thể sử dụng thêm ký tự \ để chia các câu lệnh dài thành các câu lệnh ngắn hơn trên một dòng

#### **CMD**

CMD cho phép ta set default command, có nghĩa là command này sẽ chỉ được chạy khi run container mà không chỉ định một command.

Nếu docker run với một command thì default command sẽ được ignore. Nếu dockerfile có nhiều hơn một lệnh CMD thì tất cả sẽ bị ignore ngoại trừ lệnh CMD cuối cùng.

CMD có 3 dạng form:

```bash
- CMD ["executable", "param1", "param2"]   (exec form)
- CMD ["param1", "param2"]  (đặt các tham số mặc định cho ENTRYPOINT ở dạng exec form)
- CMD command param1 param2   (shell form) 
```

Cách thứ 2 được sử dụng cùng với ENTRYPOINT trong exec form. Nó set default parameters được thêm vào ENTRYPOINT nếu container chạy mà không truyền đối số nào, ngược lại nó sẽ bị ignore. Ví dụ sử dụng đoạn mã dưới trong Dockerfile

```bash
FROM ubuntu:latest
CMD echo "Xin chào các bạn"
```

Chúng ta build image từ Dockerfile và khởi tạo container từ image vừa tạo **docker run -it <tên_image>** sẽ ra kết quả là:

```bash
Xin chào các bạn
```

nhưng khi chạy **docker run -it <tên_image> /bin/bash**, CMD sẽ bị ignored và bash sẽ được thay thế, output:

```bash
```

Kết quả không có gì được in ra cả.

#### **ENTRYPOINT**

Lệnh ENTRYPOINT cho phép ta cấu hình container sẽ chạy dưới dạng thực thi. Nó tương tự như CMD, vì nó cũng cho phép ta chỉ định một lệnh với các tham số. Sự khác biệt là lệnh ENTRYPOINT và các tham số không bị ignore khi Docker container chạy.

ENTRYPOINT có 2 dạng form:

```bash
- ENTRYPOINT ["executable", "param1", "param2"] (exec form)
- ENTRYPOINT command param1 param2 (shell form) 
```

**Exec form** của ENTRYPOINT cho phép ta đặt các lệnh và tham số và sau đó sử dụng một trong hai dạng : một là CMD để đặt các tham số bổ sung, hai là các đối số ENTRYPOINT (luôn được sử dụng), trong khi các đối số CMD có thể được ghi đè bằng các tham số dòng lệnh được cung cấp khi Docker container chạy. Ví dụ sử dụng đoạn mã dưới trong dockerfile

```bash
FROM ubuntu:latest
ENTRYPOINT ["/bin/echo", "Xin chào"]
CMD ["các bạn"]
```

Build image và chạy thử câu lệnh sau **docker run -it <tên_image>** sẽ cho kết quả là:

```bash
Xin chào các bạn
```

Bây giờ lại chạy với lệnh sau **docker run -it <tên_image> Nguyễn Văn A** sẽ cho kết quả là:

```bash
Xin chào Nguyễn Văn A
```

**Shell form** của ENTRYPOINT ignore tất CMD và các tham số dòng lệnh

#### **Tổng kết**

RUN được sử dụng để cài đặt các package, thực thi câu lệnh tạo ra layer mới của image.

ENTRYPOINT và CMD sử dụng khi ta cần một lệnh cần được thực thi. Ngoài ra, hãy sử dụng CMD kết hợp với ENTRYPOINT nếu ta cần cung cấp thêm các đối số mặc định có thể được ghi đè từ dòng lệnh khi docker container chạy.