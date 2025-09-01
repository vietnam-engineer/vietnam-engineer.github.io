---
title: Kiểm thử UART device driver
sidebar: ldd_sidebar
permalink: uart_device_driver_test.html
folder: driver
---

## Kiểm thử tích hợp

Để đảm bảo driver hoạt động tốt trước khi cho điều khiển thiết bị thật, ta có thể xây dựng hệ thống kiểm thử tích hợp như sau:

<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Write driver for UART device</title>
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
  flowchart LR
    usb <--USB--> bridge
    subgraph "Máy tính Windows"
      dev("vn29a80.py")
      driver("USB2COM
      driver")
      usb("USB
      Controller")
      dev <--UART-->driver<--USB-->usb
    end
    subgraph "Hệ thống nhúng"
      bridge[USB2UART]
      uart("UART
      controller")
      devdrv("vn29a80
      driver")
      gtest("Google test
      application")
      bridge <--UART--> uart <--bytes--> devdrv <--syscall--> gtest
    end
  </pre>
</body>
</html>

## Triển khai Google test application

Google Test framework cho phép chúng ta xây dựng các test application nhằm giúp giảm thiểu thời gian và công sức khi cần kiểm thử lặp lại nhiều lần.

Trong dự án này, một lớp có tên là [Vn29a80DrvTester](https://github.com/vietnam-engineer/linux_driver/blob/main/uart/test/vn29a80_driver_tester.hpp) được kế thừa từ lớp `::testing::Test` của Google test framework. Lớp `Vn29a80DrvTester` sẽ triển khai thêm các phương thức kiểm thử phù hợp với vn29a80 driver. Dựa vào đó, chúng ta sẽ xây dựng [bộ các test case](https://github.com/vietnam-engineer/linux_driver/blob/main/uart/test/vn29a80_driver_testcases.cpp) để kiểm tra khả năng hoạt động của vn29a80 driver.
