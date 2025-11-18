---
title: Để là chuyên gia phát triển hệ nhúng
sidebar: suy_ngam_sidebar
summary: "Đây là câu trả lời của riêng tôi cho câu hỏi: làm thể nào để trở thành một chuyên gia phát triển hệ nhúng"
permalink: de_la_chuyen_gia_phat_trien_he_nhung.html
folder: suy_ngam
---

## Thế nào là một chuyên gia phát triển hệ nhúng?

Tôi nghĩ rằng, muốn là chuyên gia ở lĩnh vực nào, mình phải có hiểu biết về lĩnh vực đó. Muốn biết thì phải học, muốn hiểu thì phải làm. Ngày nay, với sự trợ giúp của trí tuệ nhân tạo, `học` trở nên dễ dàng hơn bao giờ hết, và `làm` trở nên nhanh chóng hơn bao giờ hết. Quan trọng là chúng ta có muốn hay không?

Để trở thành một chuyên gia phát triển hệ nhúng, bước đầu tiên là cần có hiểu biết về hệ nhúng. Hệ nhúng gồm 2 phần: phần cứng và phần mềm. Kiến thức và kỹ năng của cả 2 mảng này đều quan trọng, ngay cả khi tôi chỉ là Embedded Software Engineer.

Thêm vào đó, vạn vật trong thế giới kết nối với nhau. Học và làm về mạng các hệ nhúng là điều không thể tránh khỏi.

Và cuối cùng, ngoài khả năng làm việc độc lập, tôi nghĩ rằng mình cũng cần phải có khả năng làm việc cùng đồng nghiệp. Vì suy cho cùng, ít có cái gì lớn mà bạn lại có thể làm được một mình, phải có đồng đội.

## Học và làm việc với máy móc

### Hệ nhúng.

<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Phần cứng của hệ thống nhúng</title>
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
    mindmap
    root)Phần cứng của hệ thống nhúng(
      [kiến thức]
        ((cần học))
          (vi xử lý)
            (ARM)
            (RISC-V)
          (bộ nhớ)
            (RAM)
            (eMMC)
            (QSPI)
          (vi điều khiển)
            (GPIO)
            (ADC)
            (Timer)
            (Watchdog)
            (Interrupt)
            (PWM)
          (bus)
            (UART)
            (I2C)
            (SPI)
        ((nên học))
          (Lý thuyết mạch)
          (Điện tử căn bản)
          (HDL)
      [kỹ năng]
        ((cần làm))
          (Đo lường)
            (Multimeter)
            (Oscilloscope)
          (Đọc tài liệu)
            (Schematic)
            (Datasheet)
            (ICD)
        ((nên làm))
          (PCB)
          (IP core/FPGA)
  </pre>
</body>
</html>


<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Phần mềm của hệ thống nhúng</title>
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
    mindmap
    root)Phần mềm của hệ thống nhúng(
      [kiến thức]
        ((cần học))
          (Phân tích<br>thiết kế)
            (Object-oriented)
            (Design pattern)
          (Lập trình)
            (Ngôn ngữ)
              (C)
              (C++)
            (Cấu trúc dữ liệu<br>và giải thuật)
            (Hệ thống<br>biên dịch)
              (Make)
              (CMake)
            (Nguyên tắc SOLID)
          (Kiểm thử)
            (TDD)
            (SIL/HIL)
        ((nên học))
          (Quy trình<br>phát triển)
            (Agile)
            (V-Model)
            (CI/CD)
          (Lập trình)
            (Ngôn ngữ lập trình)
              (Rust)
              (Python)
            (Cơ sở dữ liệu)
            (Hệ thống<br>biên dịch)
              (Buildroot)
              (Yocto)
          (Trình khởi động)
            (U-boot)
          (Hệ điều hành)
            (Linux)
            (RTOS)
          (Rootfs)
            (systemd)
            (SysVinit)
          (Security)
            (Secure boot)
            (Firmware encryption)
      [kỹ năng]
        ((cần làm))
          (Lập trình)
            (Clean code)
            (Git)
            (Lập trình<br>kernel space)
              (devicetree)
              (kernel driver)
            (Lập trình<br>user space)
              (C/C++ app)
              (shell script)
              (service unit file)
              (Google unit test)
            (Biên dịch)
              (makefile)
              (CMakeLists)
              (Kbuild)
              (Yocto recipe)
            (Gỡ lỗi)
              (strace)
              (perf)
              (GDB)
              (dmesg)
              (lsof)
          (UML)
          (Github Actions)
        ((nên làm))
          (lập trình<br>vi điều khiển)
  </pre>
</body>
</html>

### Mạng các hệ nhúng


<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Mạng các hệ thống nhúng</title>
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
    mindmap
    root)Mạng các hệ thống nhúng(
      [kiến thức]
        ((cần học))
          (Mạng thông tin)
            (Mạng máy tính)
            (Mạng di động)
          (Giao thức truyền thông)
            (L1/L2)
              (Ethernet)
              (USB)
              (CAN)
              (Bluetooth)
              (Wifi)
              (Zigbee)
            (Transportation)
              (UDP)
              (TCP)
            (Application)
              (MQTT)
              (RPC)
              (HTTP)
        ((nên học))
          (Lý thuyết thông tin)
          (Lý thuyết trường)
          (Lý thuyết anten)
          (Lý thuyết điều khiển)
      [Thực hành]
        ((cần làm))
          (server/client apps)
          (Gỡ lỗi)
            (tcpdump)
            (Wireshark)
            (socat)
            (iperf)
        ((nên làm))
          (WebAPI)
          (cấu hình router)
            (iptables)
            (port forwarding)
  </pre>
</body>
</html>


## Học và làm việc với con người.

<html lang="en">
<head>
  <meta charset="UTF-8">
  <title>Hệ thống nhúng</title>
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
    mindmap
    root)Làm việc với con người(
      [Tư duy]
        (Với bản thân)
          (học, học nữa, học mãi)
          (kỷ luật)
          (đơn giản<br>nhất có thể)
          (nói đúng<br>làm đúng)
          (hành động<br>có cơ sở)
          (hành động<br>có mục đích)
          (sạch<br>đẹp<br>gọn)
          (dám nghĩ<br>dám thử<br>dám chịu)
          (khó không lùi<br>bại không nản)
        (Với đồng nghiệp)
          (cùng tiến<br>cùng thắng)
          (cầu thị)
          (khiêm tốn)
          (lịch sự)
          (giữ chữ tín)
      [Kỹ năng]
        (giao tiếp)
          (ngoại ngữ)
          (giải thích)
          (thuyết phục)
          (từ chối)
          (thương lượng)
          (phản biện)
        (phân tích<br>vấn đề)
          (FTA)
          (5-Why)
  </pre>
</body>
</html>
