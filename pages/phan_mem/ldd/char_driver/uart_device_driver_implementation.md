---
title: Triển khai UART device driver
sidebar: ldd_sidebar
permalink: uart_device_driver_implementation.html
folder: driver
---

## Các bước triển khai

- [Xây dựng hạ tầng biên dịch](https://github.com/vietnam-engineer/linux_driver/commit/b8e69c28da029b9c6dbad2ec8bb20a44d41bdf47)

- Xây dựng các lớp của `uartdev_core.ko`, một module được dùng chung bởi nhiều UART device driver.
    - [Xây dựng lớp drv](https://github.com/vietnam-engineer/linux_driver/commit/a31021c509ce0cee904da82309751eb1987dbefe)
    - [Xây dựng lớp uif](https://github.com/vietnam-engineer/linux_driver/commit/d125d633af6bf85dee146e7541c4fd2a41ec3b96)
    - [Xây dựng lớp uart](https://github.com/vietnam-engineer/linux_driver/commit/05c9ba1f693aa1df004237090ae080cfb5be1d73)
    - Để các driver cho thiết bị cụ thể sử dụng được các phương thức của `uartdev_core.ko`, ta cần phải [công khai các API này](https://github.com/vietnam-engineer/linux_driver/commit/f6cb569a2ee3522313cd536729dcff93df9aec28)
- Xây dựng các lớp của `counter_vn29a80.ko`, một UART device driver cụ thể cho bộ đếm vn29a80.
    - [Xây dựng lớp vn29a80_drv](https://github.com/vietnam-engineer/linux_driver/commit/7e6069e487fa3313d3395cdbee6cd18dfe9970c9).
        - Phương thức vn29a80_drv_setup() sẽ được gọi khi kernel thấy vn29a80 xuất hiện.
        - Để hỗ trợ tự động tải nạp `counter_vn29a90.ko` và `uartdev_core.ko`, ta cần [sử dụng MODULE_DEVICE_TABLE](https://github.com/vietnam-engineer/linux_driver/commit/11862df8a28a756118490482ffe399cdc65bc774)
    - [Xây dựng lớp vn29a80_req](https://github.com/vietnam-engineer/linux_driver/commit/29383cbce0cba71cd536bb3b8f284a7d0d972b85)
    - [Xây dựng lớp vn29a80_res](https://github.com/vietnam-engineer/linux_driver/commit/dd90c31d7b06d95dd6e3673feebc0c5950761c4d)
        - Thông thường, ngoài các thông tin nhận được từ thiết bị, driver cũng có thể gắn thêm các thông tin quan trọng khác, như [timestamp](https://github.com/vietnam-engineer/linux_driver/commit/4b8deb1f79172d8a7738b137d08af447e23611f2).
        - Có 3 hệ thống thời gian hay gặp trong Linux: `CLOCK_REALTIME`, `CLOCK_MONOTONIC` và `CLOCK_MONOTONIC_RAW`. Vui lòng tham khảo ở [đây](https://man7.org/linux/man-pages/man3/clock_gettime.3.html) để biết thêm chi tiết.
    - [Xây dựng lớp vn29a80_uart](https://github.com/vietnam-engineer/linux_driver/commit/add78303193c447f04018a5b791e03ad2688d267)
    - [Xây dựng lớp vn29a80_uif](https://github.com/vietnam-engineer/linux_driver/commit/f9fd487b2f6bd0f43c30d46532eb92cafe8994fa)
        - [Xây dựng giao diện với device file](https://github.com/vietnam-engineer/linux_driver/commit/217368a9393ff062a46d6169500ec25abbc7150a)
        - [Xây dựng giao diện với sysfs files](https://github.com/vietnam-engineer/linux_driver/commit/c9c8fb1b120b22b3488a9793c10959e12da2d8f2)
    - Trong Linux kernel, có nhiều luồng thực thi. Các luồng thực thi này có thể đồng thời truy cập thiết bị, hoặc đồng thời truy cập dữ liệu của driver. Để tránh các kết quả không mong muốn, ta chỉ cho phép một luồng thực thi được phép thao tác trên thiết bị cũng như dữ liệu tại mỗi thời điểm.
        - [Bảo vệ thiết bị khỏi truy cập đa luồng](https://github.com/vietnam-engineer/linux_driver/blob/main/uart/src/vn29a80_uif.c#L29-L33)
        - [Bảo vệ dữ liệu khỏi truy cập đa luồng](https://github.com/vietnam-engineer/linux_driver/commit/9beead79b0abacb5c653741aafd8ca04b1cbc7a6)
