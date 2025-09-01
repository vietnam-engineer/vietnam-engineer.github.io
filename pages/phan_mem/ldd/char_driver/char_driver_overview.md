---
title: Giới thiệu về character driver
sidebar: ldd_sidebar
permalink: char_driver_overview.html
folder: driver
---

## Character driver là gì?

Character driver (hay char driver) là một loại driver dùng để quản lý các thiết bị mà dữ liệu được xử lý ở cấp độ ký tự (hay còn gọi là byte). Các thiết bị như UART, GPIO, ADC, I2C, SPI, hoặc các cảm biến đơn giản thường được điều khiển thông qua character driver.

Bạn có thể tìm thấy rất nhiều character driver trong Linux kernel source tree, ví dụ như [char mem driver](https://github.com/torvalds/linux/blob/master/drivers/char/mem.c).

## Char driver đóng vai trò gì?

Character driver đóng vai trò như một cầu nối giữa ứng dụng trên user-space và char device. Quá trình tương tác diễn ra như sau:

<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Driver types</title>
  <script type="module">
    import mermaid from "https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs";
    mermaid.initialize({
      startOnLoad: true,
      theme: "default",
      securityLevel: "loose",
      flowchart: { useMaxWidth: true },
      block: { useExperimentalRenderer: true }
    });
  </script>
</head>
<body>
  <pre class="mermaid">
  sequenceDiagram
    participant app as Ứng dụng
    participant fs as Filesystem
    participant drv as Character driver
    participant dev

    app ->> fs: gọi system call trên device file
    Note right of app: system call có thể là<br>open, read, write,...
    Note left of fs: device file được đặt<br>trong thư mục /dev
    fs ->> drv: kích hoạt một thao tác tương ứng
    drv ->> dev: truy cập thiết bị nếu cần thiết.
  </pre>
</body>
</html>

Như vậy, nhờ có char driver cùng với sự hỗ trợ của Filesystem, các ứng dụng trên user space có thể được phát triển độc lập mà không cần quan tâm đến chuẩn giao tiếp và hoạt động chi tiết của thiết bị phần cứng.
