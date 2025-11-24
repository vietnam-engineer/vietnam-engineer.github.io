---
title: Gỡ lỗi phần mềm
sidebar: debug_sw_sidebar
permalink: debug_sw.html
folder: debug_sw
---

## Lỗi là gì?

Lỗi (problem/issue) là bất kỳ sự kiện nào khiến hệ thống không hoạt động đúng như mong đợi.
Trong kỹ thuật nói chung và lĩnh vực phần mềm nói riêng, lỗi luôn là điều khó tránh khỏi. Là một kỹ sư phần mềm, chúng ta không được phép "ngó lơ" hoặc "đá issue" khi xảy ra lỗi, mà cần phải hiểu lỗi xuất phát từ đâu và cần sửa như thế nào để không tái diễn.

Khi gặp lỗi, chúng ta thường có hai cách tiếp cận:
- Xử lý triệu chứng
    - Tập trung khắc phục hậu quả ngay lập tức để đưa hệ thống chạy lại nhanh nhất có thể.
    - Cách này mang tính "chữa cháy", hữu ích khi vấn đề cần giải quyết ngay.
    - Lỗi có thể lặp lại vì nguyên nhân sâu xa không được xử lý.
- Truy tìm nguyên nhân gốc rễ (Root Cause Analysis – RCA)
    - Tìm hiểu điều gì thực sự gây ra lỗi.
    - Không chỉ trả lời câu hỏi "chuyện gì xảy ra?", mà còn trả lời câu hỏi "tại sao nó xảy ra?".
    - Giúp đưa ra biện pháp khắc phục tận gốc.

Hai cách tiếp cận này đều quan trọng: xử lý triệu chứng giúp ổn định tạm thời, còn RCA giúp ngăn lỗi tái diễn. Bài viết này sẽ tập trung làm rõ hơn về RCA.

## RCA là gì?

RCA (Root Cause Analysis) là quy trình phân tích có hệ thống, nhằm tìm ra nguyên nhân gốc gây ra một sự cố, sau đó đề xuất giải pháp để ngăn ngừa sự cố đó xảy ra lần nữa. Thay vì chỉ nhìn vào bề mặt, RCA đi sâu vào ba nhóm nguyên nhân:
- Nguyên nhân vật lý
- Nguyên nhân con người
- Nguyên nhân tổ chức (quy trình, chính sách, đào tạo…)

Lợi ích của RCA
- Giảm lỗi tái diễn, giúp hệ thống ổn định hơn.
- Tăng năng suất do giảm thời gian xử lý sự cố.
- Tiết kiệm chi phí dài hạn.
- Cải thiện quy trình, nâng cao chất lượng.
- Giảm rủi ro và tăng độ an toàn.

## Các bước thực hiện RCA

- Xác định rõ vấn đề: Mô tả vấn đề một cách cụ thể, thống nhất về phạm vi và ảnh hưởng.
- Tạo nhóm RCA: Nhóm nên gồm một hoặc nhiều người hiểu hệ thống, quy trình.
- Thu thập dữ liệu: Thu thập lịch sử vận hành, lịch sử sự cố, báo cáo, tài liệu quy trình, phỏng vấn các nhân viên liên quan. Trong phần mềm, có nhiều công cụ để thu thập bằng chứng và dữ liệu giúp phân tích lỗi như:
    - log: ghi lại lịch sử hoạt động của phần mềm cũng như thông tin tại thời điểm xảy ra lỗi. Nó giúp xác định bối cảnh, dữ liệu đầu vào và luồng xử lý dẫn đến lỗi.
    - dmesg: ghi lại lịch sử hoạt động của Linux kernel, giúp xác định vấn đề ở mức hệ điều hành.
    - gdb: cung cấp call stack khi ứng dụng crash; giúp tìm vị trí chính xác trong code gây lỗi và luồng điều khiển dẫn đến crash.
    - valgrind: tìm lỗi quản lý bộ nhớ như leak, double free, use-after-free.
    - strace: theo dõi các system call (open, read, write, socket…), giúp phát hiện lỗi I/O, quyền truy cập, file missing, deadlock, hoặc timeout.
    - perf: phân tích hiệu năng, CPU hotspot, lock contention; giúp tìm nguyên nhân gốc rễ của các lỗi về hiệu suất (chậm, treo, timeouts).
    - lsof: liệt kê file descriptor, socket đang mở; giúp phát hiện leak FD, cạn tài nguyên, hoặc ứng dụng "treo" vì giữ lock hay socket.
    - ...
- Xác định các nguyên nhân khả dĩ: liệt kê càng nhiều khả năng càng tốt, tránh loại trừ quá sớm.
- Phân tích và xác định nguyên nhân gốc rễ: Đánh giá mối quan hệ, mức ảnh hưởng và khả năng xảy ra để tìm nguyên nhân cốt lõi.
- Đề xuất giải pháp: Giải pháp phải tác động trực tiếp vào nguyên nhân gốc, không chỉ sửa triệu chứng.
- Lập kế hoạch hành động và theo dõi: Đảm bảo giải pháp được triển khai hiệu quả và có biện pháp kiểm soát lâu dài.

## Một số phương pháp RCA phổ biến

- 5 Whys: Hỏi "Tại sao?" nhiều lần để đào sâu nguyên nhân.
- FTA: nhận diện nguyên nhân gốc rễ thông qua một sơ đồ các khả năng gây ra lỗi và mối quan hệ giữa chúng.

[Tham khảo]:https://www.ibm.com/think/topics/root-cause-analysis

