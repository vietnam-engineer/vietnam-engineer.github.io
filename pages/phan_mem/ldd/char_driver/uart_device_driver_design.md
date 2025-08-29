---
title: Thiết kế UART device driver
sidebar: ldd_sidebar
permalink: uart_device_driver_design.html
folder: driver
---

## Ưu điểm của nguyên lý hướng đối tượng trong phát triển phần mềm.

Thiết kế phần mềm theo nguyên lý hướng đối tượng có nhiều ưu điểm:
- Thứ nhất là dễ hiểu. Nguyên lý hướng đối tượng dùng ngôn ngữ gần gũi với đời sống để mô tả phần mềm, từ tổng quát đến cụ thể. Điều này giúp giảm thiểu sự khác biệt trong hiểu biết giữa các bên liên quan như quản lý, kiểm thử viên, lập trình viên về phần mềm định phát triển.
- Thứ hai là dễ bảo trì. Việc nâng cấp, sửa chữa một đối tượng sẽ không gây ảnh hưởng đến các đối tượng khác trong hệ thống, miễn là giao diện của đối tượng đó được giữ nguyên.
- Thứ ba là dễ mở rộng. Khi muốn thêm một chức năng mới, chỉ cần tạo một lớp mới hoặc mở rộng chức năng của lớp hiện có.
- Thứ tư là giảm công sức và chi phí phát triển phần mềm. Nhờ có sự kế thừa, những chức năng chung có thể được tái sử dụng mà không cần viết lại và kiểm thử lại.

Theo ý kiến cá nhân của mình, vì Linux device driver cũng là phần mềm nên hoàn toàn có thể áp dụng nguyên lý hướng đối tượng vào thiết kế và triển khai.

## Thiết kế vn29a80 driver theo nguyên lý hướng đối tượng.

Xin mời bạn đọc bản thiết kế của vn29a80 driver tại [đây](https://github.com/vietnam-engineer/linux_driver/blob/main/uart/doc/vn29a80_driver_design.md).
