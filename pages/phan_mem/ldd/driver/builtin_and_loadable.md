---
title: Giới thiệu về built-in và loadable driver
sidebar: ldd_sidebar
permalink: builtin_and_loadable.html
folder: driver
---

## Giới thiệu

Dựa vào khả năng tải nạp trong quá trình hoạt động, kernel-space driver được chia làm 2 loại: built-in driver và loadable driver.

| Yếu tố so sánh | built-in driver | loadable driver |
|---| ---|---|
| Điều kiện biên dịch | cần đặt trong kernel source tree | chỉ cần có linux-headers |
| Kết quả biên dịch | được biên dịch thành một phần của kernel image | được biên dịch thành một tệp tin độc lập với kernel image |
| Khả năng tải nạp | không thể dỡ bỏ trong quá trình hoạt động | có thể tải nạp khi cần và dỡ bỏ khi không cần |
| Thời gian hoạt động | hoạt động trong suốt thời gian chạy của hệ thống | bắt đầu hoạt động sau khi tải nạp và dừng lại sau khi dỡ bỏ |
| Chi phí vận hành | làm tăng kích thước của kernel image, tăng thời gian khởi động hệ thống | giúp cho kernel image trở nên nhỏ gọn, giảm thời gian khởi động hệ thống |
| Chi phí gỡ lỗi | tốn kém hơn| ít tốn kém hơn |
| Ứng dụng | điều khiển các thiết bị không bao giờ bị tháo ra khỏi hệ thống | điều khiển các thiết bị hay bị thay đổi trong hệ thống (cắm vào, rút ra, nâng cấp) |

## Built-in driver

Một số điều sau đây có thể hữu ích trong quá trình phát triển một built-in driver:
- Các built-in driver được liệt kê trong tệp tin `/lib/modules/$(uname -r)/modules.builtin`. Điều đó có nghĩa là, bạn có thể kiểm tra tệp tin này để chắc chắn rằng driver của mình đã được tích hợp vào kernel image.
- Nếu built-in driver chưa được tích hợp vào kernel image như mong đợi, hãy chắc chắn rằng các `CONFIG_XXX` cần cho việc biên dịch đã được thiết lập bằng `y`. Để kiểm tra, bạn có thể dùng lệnh `zcat /proc/config.gz | grep CONFIG_XXX`. Nếu lệnh này không cho kết quả gì, rất có thể bạn chưa sửa Kconfig và Makefile đúng cách.
- Nếu built-in driver đã được tích hợp vào kernel image như mong đợi, hãy chắc chắn rằng driver đó được khởi chạy thành công và hoạt động bình thường. Để kiểm tra, bạn có thể dùng lệnh `dmesg`.

## Loadable driver

Một số điều sau đây có thể hữu ích trong quá trình phát triển một loadable driver:
- Để xem thông tin về một loadable driver, có thể dùng lệnh [modinfo](https://man7.org/linux/man-pages/man8/modinfo.8.html)
- Để tải nạp một loadable driver vào kernel space, có thể dùng lệnh [modprobe](https://man7.org/linux/man-pages/man8/modprobe.8.html) hoặc [insmod](https://man7.org/linux/man-pages/man8/insmod.8.html)
- Để dỡ bỏ một loadable driver khỏi kernel space, có thể dùng [modprobe -r](https://man7.org/linux/man-pages/man8/modprobe.8.html) hoặc [rmmod](https://man7.org/linux/man-pages/man8/rmmod.8.html)
- Để kiểm tra những loadable driver nào đã được tải nạp vào kernel space, có thể dùng [lsmod](https://man7.org/linux/man-pages/man8/lsmod.8.html)

## Cách tự động tải nạp loadable driver vào kernel space.

Nhiều người trong số chúng ta thường phát triển driver dưới dạng loadable vì ưu điểm của loadable driver là dễ dàng cập nhật khi cần vá lỗi. Đôi khi chúng ta muốn hệ thống tự động tải nạp loadable driver vào kernel space mà không cần phải ngồi gõ lệnh `modprobe`. Có nhiều cách để đạt được mong muốn này. Dưới đây trình bày 2 cách thức phố biến hiện nay (với giả định rằng, hệ thống của bạn sử dụng [systemd](https://man7.org/linux/man-pages/man1/systemd.1.html)).

### Sử dụng cơ chế chỉ định

#### Cơ chế hoạt động

[systemd-modules-load](https://man7.org/linux/man-pages/man8/systemd-modules-load.service.8.html) đóng vai trò chính trong cơ chế này. Dịch vụ này sẽ tải nạp các loadable module vào kernel space khi khởi động hệ thống.

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
    participant f as rootfs
    participant s as systemd-modules-load
    participant k as kernel
    
    s ->> k: đọc các tham số "modules_load" và "rd.modules_load" từ "/proc/cmdline"
    k -->> s: danh sách các modules
    loop từng module
      s ->> f: đọc /lib/modules/$(uname -r)/modules.dep<br>để xác định module này phụ thuộc vào các module nào.
      f -->> s: module
      s ->> k: gọi system call init_module() hoặc finit_module()<br> để tải nạp module vào kernel space
    end
    s ->> f: đọc các tệp tin có đuôi mở rộng ".conf"
    Note right of f: các tệp này được lưu trong<br>/etc/modules-load.d/<br>/lib/modules-load.d/<br>/usr/lib/modules-load.d/<br>...
    f -->> s: danh sách các modules
    loop từng module
      s ->> f: đọc /lib/modules/$(uname -r)/modules.dep<br>để xác định module này phụ thuộc vào các module nào.
      f -->> s: module
      s ->> k: gọi system call init_module() hoặc finit_module()<br> để tải nạp module vào kernel space
    end
  </pre>
</body>
</html>

Hình vẽ trên mô tả cơ chế tự động tải nạp một loadable module vào kernel space khi khởi động hệ thống. Quá trình này có thể được tóm tắt như sau:
1. Tải nạp các module được chỉ định trong [kernel command line](https://www.man7.org/linux/man-pages/man7/kernel-command-line.7.html) (nếu có).
    1. đọc các tham số `modules_load=` và `rd.modules_load=` từ `/proc/cmdline` để biết [những kernel module nào được chỉ định](https://github.com/systemd/systemd/blob/main/src/modules-load/modules-load.c#L34).
    2. đọc tệp tin [`/lib/modules/$(uname -r)/modules.dep`](https://man7.org/linux/man-pages/man5/modules.dep.5.html) để [xác định xem module này có phụ thuộc vào những module nào khác không](https://github.com/kmod-project/kmod/blob/master/libkmod/libkmod-module.c#L941).
    3. [tải nạp các module đó vào trong kernel space](https://github.com/systemd/systemd/blob/main/src/modules-load/modules-load.c#L167) dựa vào system call [init_module() hoặc finit_module()](https://man7.org/linux/man-pages/man2/init_module.2.html).
2. Tải nạp các module được chỉ định trong các tệp cấu hình (nếu có).
    1. đọc các tệp tin `*.conf` trong thư mục [modules-load.d](https://man7.org/linux/man-pages/man5/modules-load.d.5.html) để biết [những kernel module nào được chỉ định](https://github.com/systemd/systemd/blob/main/src/modules-load/modules-load.c#L173).
    2. đọc tệp tin `/lib/modules/$(uname -r)/modules.dep`để xác định xem module này có phụ thuộc vào những module nào khác không.
    3. [tải nạp các module đó vào trong kernel space](https://github.com/systemd/systemd/blob/main/src/modules-load/modules-load.c#L178) dựa vào system call [init_module()](https://github.com/torvalds/linux/blob/master/kernel/module/main.c#L3569) hoặc [finit_module()](https://github.com/torvalds/linux/blob/master/kernel/module/main.c#L3723).

#### Vận dụng vào thực tiễn

Để tự động tải nạp loadable driver của mình vào kernel space lúc khởi động hệ thống, chúng ta có thể làm như sau:
1. Lưu loadable driver của bạn vào trong `/lib/modules/$(uname -r)/extra` chẳng hạn.
2. Cập nhật `/lib/modules/$(uname -r)/modules.dep` bằng lệnh [depmod -a](https://man7.org/linux/man-pages/man8/depmod.8.html).
3. Chỉ định loadable driver mà chúng ta muốn tải nạp thông qua một trong hai cách sau:
    1. Truyền tên của loadable driver (không cần đuôi `.ko`) cho tham số `modules_load=` của kernel command line. Nếu cần truyền nhiều driver, hãy sử dụng dấu `,` để ngăn cách giữa tên của chúng.
    2. Tạo một tệp tin có đuôi mở rộng `.conf` trong thư mục `/etc/modules-load.d/`, sau đó hãy điền tên của load driver vào (không cần đuôi `.ko`). Nếu cần điền nhiều driver, hãy điền mỗi dòng một tên.
4. Khởi động lại hệ thống và kiểm tra xem driver đó đã được tải nạp vào kernel space hay chưa bằng cách sử dụng lệnh `lsmod` hoặc `journalctl -u systemd-modules-load`.

### Sử dụng cơ chế uevent + udev

#### Cơ chế hoạt động

[systemd-udevd](https://man7.org/linux/man-pages/man8/systemd-udevd.service.8.html) đóng vai trò chính trong cơ chế này. Dịch vụ này sẽ lắng nghe các sự kiện uevent gửi từ Linux kernel thông qua [netlink](https://man7.org/linux/man-pages/man7/netlink.7.html). Với mỗi sự kiện, `systemd-udevd` sẽ thực hiện các hành động phù hợp được quy định trong bộ quy tắc [udev](https://man7.org/linux/man-pages/man7/udev.7.html).

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
    participant k as kernel
    participant s as systemd-udevd
    participant f as rootfs
    
    k ->> k: tìm thấy một thiết bị<br>có mặt trong hệ thống
    k ->> s: uevent
    Note right of k: thông thường, uevent này sẽ<br>chứa một thông tin gọi là MODALIAS
    loop từng bộ quy tắc trong /lib/udev/rules.d/, /etc/udev/rules.d/,...
        s ->> f: đọc bộ quy tắc
        Note left of f: bộ quy tắc là tệp tin<br>có đuôi mở rộng ".rules"
        f -->> s: bộ quy tắc
        alt bộ quy tắc phù hợp với sự kiện uevent
            Note right of s: thông thường, quy tắc đó là<br>"80-drivers.rules"
            s ->> s: xác định hành động
            Note right of s: thông thường, hành động đó là<br>"modprobe ${MODALIAS}" hoặc<br>"kmod load ${MODALIAS}"
            s ->> k: tải nạp các loadable module thích hợp<br>vào trong kernel space
        end
    end
  </pre>
</body>
</html>

Hình vẽ trên mô tả cơ chế tự động tải nạp loadable driver vào kernel space khi Linux kernel tìm thấy một thiết bị trong hệ thống. Quá trình này có thể được tóm tắt như sau:
1. Linux kernel tìm thấy một thiết bị có mặt trong hệ thống (dựa vào quá trình enumeration đối với các thiết bị discoverable như PCI, USB; hoặc nhờ vào device tree hay ACPI đối với các thiết bị non-discoverable như I2C, UART).
2. Linux kernel sẽ [tạo một sự kiện uevent và gửi nó](https://github.com/torvalds/linux/blob/master/lib/kobject_uevent.c#L473) cho `systemd-udevd`. Một thông tin quan trọng của uevent trợ giúp quá trình tự động tải nạp driver là chuỗi kí tự định danh thiết bị, hay còn gọi là `MODALIAS`. Ví dụ:
```
ACTION=add
SUBSYSTEM=serial
MODALIAS=of:Nnode_name_in_devicetreeTdevice_typeCvendor0,model0
```
3. `systemd-udevd` sẽ tra cứu từng tệp tin có đuôi mở rộng `.rules` trong các thư mục `/lib/udev/rules.d` và `/etc/udev/rules.d` để xem có bộ quy tắc nào phù hợp với sự kiện này không. Thông thường bộ quy tắc [80-drivers.rules](https://github.com/systemd/systemd/blob/main/rules.d/80-drivers.rules) phù hợp với trường hợp loadable driver của chúng ta. Theo bộ quy tắc đó, lệnh `kmod load ${MODALIAS}` sẽ được thực hiện.
4. [kmod](https://man7.org/linux/man-pages/man8/kmod.8.html) sẽ tải nạp các loadable driver vào trong kernel space.
    1. `kmod` sẽ tra cứu `/lib/modules/$(uname -r)/modules.alias` để biết loadable driver nào tương ứng với `MODALIAS` trong uevent.
    2. `kmod` sẽ tra cứu `/lib/modules/$(uname -r)/modules.dep` để biết loadable driver này phụ thuộc vào những module nào khác.
    3. `kmod` sẽ tải nạp loadable driver đó cùng với các module mà nó phụ thuộc vào trong kernel space.

Bạn cũng có thể sử dụng lệnh `udevadm test` để mô phỏng và kiểm tra cách `systemd-udev` áp dụng các quy tắc lên một thiết bị cụ thể, mà không cần phải cắm/rút thiết bị thật hay khởi động lại hệ thống.

#### Vận dụng vào thực tiễn

Để tự động tải nạp loadable driver của mình vào kernel space, chúng ta có thể làm như sau:
1. Tạo bảng device_id_table trong driver của bạn.
    1. Tìm trong device tree thuộc tính `compatible` của node đại diện cho thiết bị mà driver của bạn điều khiển.
    2. Tạo bảng device_id_table dựa trên thuộc tính đó. Ví dụ:
    ```c
    static const struct of_device_id device_id_table[] = {
        { .compatible = "vendor,model", },
        { /* sentinel */ }
    };
    ```
2. Thêm `alias` vào trong driver của bạn.
    1. Bạn có thể sử dụng macro MODULE_DEVICE_TABLE để thêm `alias`. Ví dụ:
    ```c
    MODULE_DEVICE_TABLE(of, device_id_table);
    ```
    2. Sau khi biên dịch, hãy kiểm tra xem driver của bạn đã chứa thông tin `alias` hay chưa. Bạn có thể sử dụng lệnh `modinfo` để kiểm tra.
3. Lưu loadable driver của bạn vào trong `/lib/modules/$(uname -r)/extra` chẳng hạn.
4. Cập nhật `/lib/modules/$(uname -r)/modules.dep` và `/lib/modules/$(uname -r)/modules.alias` bằng lệnh `depmod -a`.
5. Khởi động lại hệ thống và kiểm tra xem driver đó đã được tải nạp vào kernel space hay chưa bằng cách sử dụng lệnh `lsmod`.
