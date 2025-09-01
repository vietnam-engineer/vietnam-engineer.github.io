---
title: Device Tree Binary (DTB)
sidebar: ldd_sidebar
summary: "Cấu trúc của một Device Tree Binary"
permalink: device_tree_binary.html
folder: devicetree
---

## DTB là gì?

- DTB là kết quả của quá trình biên dịch tệp tin DTS. Đó là một tệp tin nhị phân mã hóa dữ liệu Device Tree.
- DTB là viết tắt của Device Tree Binary hay Device Tree Blob. DTB còn có tên gọi khác là Flattened Device Tree, hay viết tắt là FDT.
- Trong quá trình khởi động, Bootloader sẽ truyền DTB cho hệ điều hành. Hệ điều hành sẽ sử dụng dữ liệu Device Tree để lựa chọn và khởi tạo các device driver.

## DTB có cấu trúc như thế nào?

DTB gồm 4 phần theo thứ tự sau:
- Header
- Memory Reservation Block
- Structure Block
- String Block

Giữa những phần trên có thể có phần Free Space để thỏa mãn yêu cầu về căn chỉnh địa chỉ của mỗi phần:
- Memory Reservation Block nằm ở địa chỉ chia hết cho 8.
- Structure Block nằm ở địa chỉ chia hết cho 4.

### Header

Header gồm các trường sau:

| tên trường | vị trí offset | mô tả |
|---|---|---|
| magic | 0x00 | có giá trị cố định bằng `0xd00dfeed`. |
| totalsize | 0x04 | tổng số byte của toàn bộ DTB. |
| off_dt_struct | 0x08 | vị trí của Structure Block, tính theo byte, kể từ vị trí bắt đầu Header. |
| off_dt_string | 0x0c | vị trí của String Block, tính theo byte, kể từ vị trí bắt đầu Header. |
| off_mem_rsvmap | 0x10 | vị trí của Memory Reservation Block, tính theo byte, kể từ vị trí bắt đầu Header. |
| version | 0x14 | phiên bản của cấu trúc Device Tree, mới nhất là 17. |
| last_comp_version | 0x18 | phiên bản thấp nhất của cấu trúc Device Tree mà phiên bản hiện tại có thể tương thích được. |
| boot_cpuid_phys | 0x1c | ID của CPU khởi động hệ thống. Giá trị của trường này cần phải giống với giá trị của thuộc tính `reg` của node `cpus` |
| size_dt_strings | 0x20 | tổng số byte của String Block |
| size_dt_struct | 0x24 | tổng số byte của Structure Block |

Chú ý:
- Tất cả các trường đều là các số nguyên 32 bit, và được lưu trữ theo định dạng big endian.
- Một số thông tin trong Header có thể được tìm thấy nhờ lệnh `file`. Ví dụ:
```bash
$ file example.dtb
example.dtb: Device Tree Blob version 17, size=90237, boot CPU=0, string block size=7105, DT structure block size=83076
```

### Memory Reservation Block

Đây là danh sách các vùng bộ nhớ mà hệ điều hành không được sử dụng.
- Thông tin về mỗi vùng bao gồm:
    - `base address`: Địa chỉ bắt đầu của vùng (tính từ vị trí bắt đầu Device Tree). Thông tin này được biểu diễn bằng một số nguyên 8 byte.
    - `size`: Kích thước của vùng (tính theo byte). Thông tin này được biểu diễn bằng một số nguyên 8 byte.
- Danh sách kết thúc với một cặp `base address` và `size` đều bằng 0.
- Các vùng này không được phép chồng lấn lên nhau.

### Structure Block

- Structure Block mô tả cấu trúc và nội dung của bản thân Device Tree.
- Structure Block gồm một chuỗi các piece. Mỗi piece bắt đầu bằng một token là một số nguyên 32 bit. Một số token có dữ liệu mở rộng đi theo sau. Tất cả các token đều nằm ở địa chỉ chia hết cho 4, do đó một số byte có giá trị bằng 0 (padding) được thêm vào phía sau dữ liệu mở rộng.
- Có 5 loại token:
    - FDT_DEVICE_NODE (0x00000001):
        - đánh dấu bắt đầu một node.
        - dữ liệu mở rộng theo sau token này là:
            - `node-name@unit-address` là một chuỗi kết thúc bằng ký tự null.
            - padding (nếu cần) để token tiếp theo bắt đầu từ vị trí chia hết cho 4.
        - theo sau phần dữ liệu mở rộng là một token bất kì, ngoại trừ FDT_END.
    - FDT_END_NODE (0x00000002):
        - đánh dấu kết thúc một node.
        - theo sau token này không có dữ liệu mở rộng.
        - theo sau token này là một token bất kì, ngoại trừ `FDT_PROP`.
    - FDT_PROP (0x00000003):
        - đánh dấu bắt đầu một thuộc tính.
        - dữ liệu mở rộng theo sau token này là:
            - các thông tin mô tả thuộc tính:
                - số lượng byte biểu diễn giá trị của thuộc tính. Thông tin này là một số nguyên 32 bit.
                - vị trí offset trong `String Block` lưu trữ tên của thuộc tính. Thông tin này là một số nguyên 32 bit.
            - giá trị của thuộc tính.
            - padding (nếu cần) để token tiếp theo bắt đầu từ vị trí chia hết cho 4.
    - FDT_NOP (0x00000004):
        - đánh dấu token này không được sử dụng bởi các chương trình phân tích Device Tree.
        - theo sau token này không có dữ liệu mở rộng.
        - khi muốn xóa một node hoặc một thuộc tính khỏi Device Tree, ta sử dụng token này để ghi đè node hoặc thuộc tính đó.
    - FDT_END (0x00000009):
        - đánh dấu kết thúc `Structure Block`. Chính vì thế, đây là token cuối cùng trong `Structure Block`. 
        - theo sau token này không có dữ liệu mở rộng.
- Structure Block được tổ chức theo kiểu cây tuyến tính, gồm các node lồng nhau. Mỗi node, bao gồm cả node gốc, sẽ được biểu diễn với định dạng như sau:
    - các token FDT_NOP (có thể có hoặc không).
    - token FDT_BEGIN_NODE. Theo sau token này là các dữ liệu mở rộng sau:
        - `node-name@unit-address` là một chuỗi kết thúc bằng ký tự null.
        - padding (nếu cần).
    - đối với mỗi thuộc tính của node:
        - các token FDT_NOP (có thể có hoặc không).
        - token FDT_PROP. Theo sau token này là các dữ liệu mở rộng sau:
            - các thông tin mô tả thuộc tính:
                - số lượng byte biểu diễn giá trị của thuộc tính. Thông tin này là một số nguyên 32 bit.
                - vị trí offset trong `String Block` lưu trữ tên của thuộc tính. Thông tin này là một số nguyên 32 bit.
            - giá trị của thuộc tính.
            - padding (nếu cần).
    - biểu diễn đệ quy của các node con theo định dạng này.
    - các token FDT_NOP (có thể có hoặc không).
    - token FDT_END_NODE.
- Toàn bộ Structure Block chính là biểu diễn của node gốc và token FDT_END.

### Strings Block

String Block chứa tên của các thuộc tính trong Device Tree. Mỗi cái tên là một chuỗi ký tự kết thúc bằng ký tự null. Mỗi cái tên này được tham chiếu từ Structure Block thông qua một địa chỉ offset trong String Block.
