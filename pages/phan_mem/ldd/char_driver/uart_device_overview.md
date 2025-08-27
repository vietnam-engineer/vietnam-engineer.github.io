---
title: UART device
sidebar: ldd_sidebar
permalink: uart_device_overview.html
folder: driver
---

## UART device

Bạn nào từng lập trình cho vi điều khiển chắc hẳn đã ít nhất một lần sử dụng module giao tiếp nối tiếp bất đồng bộ UART. Đây cũng là tên của một giao thức truyền thông giữa 2 thiết bị. Mặc dù đã ra đời từ cách đây rất lâu, nhưng UART vẫn có thể được tìm thấy trong hầu khắp các hệ thống nhúng ngày nay, do giao thức này thường được sử dụng bởi debug console.

Trong bài viết này, mình sẽ không đi vào chi tiết UART là gì, hoạt động ra sao vì đã có quá nhiều bài viết về nó. Các bạn có thể dễ dàng tra cứu những thông tin này trên Internet. Thay vào đó, mình sẽ trình bày về cấu hình của hệ thống sử dụng giao thức UART.

- Trong hệ thống đơn giản, UART controller của 2 thiết bị có thể kết nối trực tiếp với nhau: chân TX của controller này nối với chân RX của controller kia và ngược lại.

<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>only UART</title>
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
    uart1 <--UART--> uart2
    subgraph "Thiết bị 1"
      uart1("UART
      controller")
    end
    subgraph "Thiết bị 2"
      uart2("UART
      controller")
    end
  </pre>
</body>
</html>

- Tuy nhiên hệ thống nói trên không hoạt động tốt khi khoảng cách truyền nhận lớn hoặc môi trường dễ xảy ra nhiễu. Để khắc phục vấn đề này, các transceiver như [LTC2870](https://www.analog.com/media/en/technical-documentation/data-sheets/28701fb.pdf) được thêm vào hệ thống.

<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>UART with transceiver</title>
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
    trx1 <-- RSxxx --> trx2
    subgraph "Thiết bị 1"
      uart1("UART
      controller")
      trx1[Transceiver]
      uart1 <--UART--> trx1
    end
    subgraph "Thiết bị 2"
      trx2[Transceiver]
      uart2("UART
      controller")
      trx2 <--UART--> uart2
    end
  </pre>
</body>
</html>

- Khi một trong 2 thiết bị là máy tính cá nhân, một vấn đề khác lại nảy sinh. Vì máy tính ngày càng nhỏ đi, nên cổng giao tiếp nối tiếp DB9 (còn gọi là cổng COM) dần bị thay thể bởi cổng USB nhỏ gọn và đa năng hơn. Để vẫn có thể giao tiếp được với hệ thống nhúng, ta cần sử dụng một thiết bị đóng vai trò cầu nối, gọi là USB2COM. Trên máy tính, trình điều khiển cho USB2COM sẽ tạo ra cổng COM ảo (VCP), cho phép các Terminal Emulator như TeraTerm hay PuTTY có thể kết nối vào. Đây chính là cấu hình mà ta vẫn thường thấy trong môi trường phát triển phần mềm cho hệ thống nhúng sử dụng Linux.

<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>UART with USB2COM</title>
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
    usb <--USB--> USB2COM
    USB2COM <-- RSxxx --> trx
    subgraph "Máy tính cá nhân"
      term("Terminal
      Emulator")
      driver("USB2COM
      driver")
      usb("USB
      Controller")
      term <--UART-->driver<--USB-->usb
    end
    subgraph "Hệ thống nhúng"
      trx[Transceiver]
      uart("UART
      controller")
      trx <--UART--> uart
    end
  </pre>
</body>
</html>

- Vẫn chưa hết. Việc phải mua và dùng USB2COM hơi tốn kém và phiền phức. Để đơn giản hóa kết nối giữa máy tính và hệ thống nhúng, nhiều hệ thống nhúng ngày nay đã sử dụng bộ chuyển đổi USB2UART, ví dụ như [CP2108](https://www.silabs.com/documents/public/data-sheets/cp2108-datasheet.pdf).

<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>UART with USB2UART</title>
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
    subgraph "Máy tính cá nhân"
      term("Terminal
      Emulator")
      driver("USB2COM
      driver")
      usb("USB
      Controller")
      term <--UART-->driver<--USB-->usb
    end
    subgraph "Hệ thống nhúng"
      bridge[USB2UART]
      uart("UART
      controller")
      bridge <--UART--> uart
    end
  </pre>
</body>
</html>

## UART device giả tưởng

Trong các bài viết tiếp theo, mình sẽ trình bày cách thiết kế và lập trình driver cho một UART device giả tưởng có tên là [vn29a80](https://github.com/vietnam-engineer/linux_driver/blob/main/uart/doc/vn29a80_ICD.md). Thực chất, đó là [một phần mềm được viết bằng Python](https://github.com/vietnam-engineer/linux_driver/blob/main/uart/sim/vn29a80.py), giả lập một thiết bị phần cứng.

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
    subgraph "Máy tính cá nhân"
      dev("vn29a80")
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
      bridge <--UART--> uart <--bytes-->devdrv
    end
  </pre>
</body>
</html>



