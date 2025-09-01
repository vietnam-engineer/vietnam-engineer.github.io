---
title: Device Tree Source (DTS)
sidebar: ldd_sidebar
summary: "Bài viết này sẽ giải thích DTS là gì, và DTS có cấu trúc như thế nào."
permalink: device_tree_source.html
folder: devicetree
---

## DTS là gì?

- DTS là viết tắt của Device Tree Source. Đó là một tệp tin mã nguồn của Device Tree.
- Một tệp tin DTS có đuôi mở rộng là `.dts` hoặc `.dtsi`.
    - Tệp tin `.dtsi` thường được dùng để mô tả Device Tree ở mức SoC (System on Chip).
    - Tệp tin `.dts` thường được dùng để mô tả Device Tree ở mức bảng mạch (board). Tương tự như C/C++, tệp tin này thường tham chiếu đến các tệp tin `.dtsi` thông qua từ khóa `include.`
- Một tệp tin DTS biểu diễn một cấu trúc cây, gồm các node. Mỗi node gồm các thuộc tính và các node con.
- Một tệp tin DTS cũng chứa các comment dạng `/* */` hoặc dạng `//`.

Ví dụ:
```typescript
/* dts files without this tag will be treated by dtc as being in the obsolete version 0 */
/dts-v1/;

/ {
    node1 {
        a-string-property = "A string";
        a-string-list-property = "first string", "second string";
        // hex is implied in byte string. no '0x' prefix is required
        a-byte-string-property = [01 23 34 56];
        child-node1 {
            first-child-property;
            second-child-property = <1>;
            a-string-property = "Hello, world";
        };
        child-node2 {
        };
    };
    node2 {
        an-empty-property;
        a-cell-property = <1 2 3 4>; /* each number (cell) is a uint32 */
        child-node1 {
        };
    };
};
```

## Cấu trúc của một node trong Device Tree

Cấu trúc tổng quát của một node như sau:

```typescript
[label:] node-name[@unit-address] {
    [properties]
    [child nodes]
};
```

### Tên của node (node name)

Tên của mỗi node trong Device Tree, ngoại trừ node gốc `/`, tuân theo khuôn mẫu: `[label:] node-name[@unit-address]`
- `label`:
    - dùng để gán nhãn cho một node.
    - dài từ 1 đến 31 ký tự.
    - các ký tự hợp lệ: chữ số `0-9`, chữ in thường `a-z`, chữ in hoa `A-Z`, dấu gạch dưới `_`. Kí tự đầu tiên không được phép là chữ số.
    - để tham chiếu tới một node, ta có thể sử dụng kí tự `&` phía trước label của node đó.
- `node-name`:
    - là tên của node.
    - là một chuỗi dài từ 1 đến 31 ký tự.
    - các ký tự hợp lệ: chữ số `0-9`, chữ in thường `a-z`, chữ in hoa `A-Z`, dấu phẩy `,`, dấu chấm `.`, dấu gạch dưới `_`, dấu cộng `+`, dấu gạch ngang `-`.
    - bắt đầu bằng một chữ in thường hoặc chữ in hoa.
    - thường mô tả loại thiết bị (tham khảo [bảng 1](#bảng-1-các-tên-thường-dùng-cho-node-name)).
- `unit-address`:
    - là kiểu bus mà thiết bị nằm trên đó.
    - gồm 1 hoặc nhiều ký tự: chữ số `0-9`, chữ in thường `a-z`, chữ in hoa `A-Z`, dấu phẩy `,`, dấu chấm `.`, dấu gạch dưới `_`, dấu cộng `+`, dấu gạch ngang `-`.
    - phải trùng với trường địa chỉ đầu tiên được chỉ định bởi thuộc tính `reg` của node. Nếu node này không có thuộc tính `reg`, thì `@unit-address` không được phép xuất hiện và `node-name` phải khác với tên của tất cả các node khác cũng như các thuộc tính khác nằm cùng cấp trong Device Tree.

Ví dụ:
- `cpus`
- `ethernet@0,0`
- `gpio@48310000`
- `gic: interrupt-controller@f9010000`

<div class="panel-group" id="accordion">
  <div class="panel panel-default">
      <div class="panel-heading">
          <h4 class="panel-title">
              <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse00">Bảng 1. Các tên thường dùng cho node-name</a>
          </h4>
      </div>
      <div id="collapse00" class="panel-collapse collapse noCrossRef">
          <div class="panel-body">
            <table>
            <tbody>
                <tr>
                <td>adc</td>
                <td>clock-controller</td>
                <td>ethernet</td>
                </tr>
                <tr>
                <td>accelerometer</td>
                <td>co2-sensor</td>
                <td>ethernet-phy</td>
                </tr>
                <tr>
                <td>air-pollution-sensor</td>
                <td>compact-flash</td>
                <td>fdc</td>
                </tr>
                <tr>
                <td>atm</td>
                <td>cpu</td>
                <td>flash</td>
                </tr>
                <tr>
                <td>audio-codec</td>
                <td>cpus</td>
                <td>gnss</td>
                </tr>
                <tr>
                <td>audio-controller</td>
                <td>crypto</td>
                <td>gpio</td>
                </tr>
                <tr>
                <td>backlight</td>
                <td>disk</td>
                <td>gpu</td>
                </tr>
                <tr>
                <td>bluetooth</td>
                <td>display</td>
                <td>gyrometer</td>
                </tr>
                <tr>
                <td>bus</td>
                <td>dma-controller</td>
                <td>hdmi</td>
                </tr>
                <tr>
                <td>cache-controller</td>
                <td>dsi</td>
                <td>hwclock</td>
                </tr>
                <tr>
                <td>camera</td>
                <td>dsp</td>
                <td>i2c</td>
                </tr>
                <tr>
                <td>can</td>
                <td>eeprom</td>
                <td>i2c-mux</td>
                </tr>
                <tr>
                <td>charger</td>
                <td>efuse</td>
                <td>ide</td>
                </tr>
                <tr>
                <td>clock</td>
                <td>endpoint</td>
                <td>interrupt-controller</td>
                </tr>
                <tr>
                <td>iommu</td>
                <td>nvram</td>
                <td>serial</td>
                </tr>
                <tr>
                <td>isa</td>
                <td>oscillator</td>
                <td>sound</td>
                </tr>
                <tr>
                <td>keyboard</td>
                <td>parallel</td>
                <td>spi</td>
                </tr>
                <tr>
                <td>key</td>
                <td>pc-card</td>
                <td>spmi</td>
                </tr>
                <tr>
                <td>keys</td>
                <td>pci</td>
                <td>sram-controller</td>
                </tr>
                <tr>
                <td>lcd-controller</td>
                <td>pcie</td>
                <td>ssi-controller</td>
                </tr>
                <tr>
                <td>led</td>
                <td>phy</td>
                <td>syscon</td>
                </tr>
                <tr>
                <td>leds</td>
                <td>pinctrl</td>
                <td>temperature-sensor</td>
                </tr>
                <tr>
                <td>led-controllers</td>
                <td>pmic</td>
                <td>timer</td>
                </tr>
                <tr>
                <td>light-sensor</td>
                <td>pmu</td>
                <td>touchscreen</td>
                </tr>
                <tr>
                <td>lora</td>
                <td>port</td>
                <td>tpm</td>
                </tr>
                <tr>
                <td>magnetometer</td>
                <td>ports</td>
                <td>usb</td>
                </tr>
                <tr>
                <td>mailbox</td>
                <td>power-monitor</td>
                <td>usb-hub</td>
                </tr>
                <tr>
                <td>mdio</td>
                <td>pwm</td>
                <td>usb-phy</td>
                </tr>
                <tr>
                <td>memory</td>
                <td>regulator</td>
                <td>vibrator</td>
                </tr>
                <tr>
                <td>memory-controller</td>
                <td>reset-controller</td>
                <td>video-codec</td>
                </tr>
                <tr>
                <td>mmc</td>
                <td>rng</td>
                <td>vme</td>
                </tr>
                <tr>
                <td>mmc-slot</td>
                <td>rtc</td>
                <td>watchdog</td>
                </tr>
                <tr>
                <td>mouse</td>
                <td>sata</td>
                <td>wifi</td>
                </tr>
                <tr>
                <td>nand-controller</td>
                <td>scsi</td>
                <td>&nbsp;</td>
                </tr>
            </tbody>
            </table>
          </div>
      </div>
  </div>
  <!-- /.panel -->
</div>
<!-- /.panel-group -->

### Các thuộc tính của node (properties)

Mỗi node trong Device Tree chứa một số thuộc tính mô tả các đặc điểm của node đó. Mỗi thuộc tính tuân theo khuôn mẫu: `[label:] property-name [= value];`
- `label`:
    - dùng để gán nhãn cho một thuộc tính.
    - là một chuỗi dài từ 1 đến 31 ký tự.
    - các ký tự hợp lệ: chữ số `0-9`, chữ in thường `a-z`, chữ in hoa `A-Z`, dấu gạch dưới `_`. Kí tự đầu tiên không được phép là chữ số.
    - để tham chiếu tới một thuộc tính, ta có thể sử dụng kí tự `&` phía trước label của thuộc tính đó.
- `property-name`:
    - là tên của thuộc tính.
    - là một chuỗi dài từ 1 đến 31 ký tự.
    - các ký tự hợp lệ: chữ số `0-9`, chữ in thường `a-z`, chữ in hoa `A-Z`, dấu phẩy `,`, dấu chấm `.`, dấu gạch dưới `_`, dấu cộng `+`, dấu gạch ngang `-`, dấu hỏi `?`, dấu thăng `#`.
- `value`:
    - là giá trị của thuộc tính.
    - là một mảng gồm 0 hoặc nhiều byte, chứa thông tin về thuộc tính.
    - tham khảo [bảng 2](#bảng-2-các-kiểu-giá-trị-của-một-thuộc-tính) về các kiểu giá trị của thuộc tính. Ngoài ra, giá trị có thể là:
        - một biểu thức số học, bao gồm `+`, `-`, `*`, `/`, `%`
        - một biểu thức logic, bao gồm `&&`, `||`, `!`
        - một biểu thức bitwise, bao gồm `&`, `|`, `^`, `~`, `>>`, `<<`
        - một biểu thức quan hệ, bao gồm `<`, `>`, `<=`, `>=`, `==`, `!=`.

<div class="panel-group" id="accordion">
  <div class="panel panel-default">
      <div class="panel-heading">
          <h4 class="panel-title">
              <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse01">Bảng 2. Các kiểu giá trị của một thuộc tính</a>
          </h4>
      </div>
      <div id="collapse01" class="panel-collapse collapse noCrossRef">
          <div class="panel-body">
            <table>
            <thead>
                <tr>
                <th>Kiểu giá trị</th>
                <th>Mô tả</th>
                </tr>
            </thead>
            <tbody>
                <tr>
                <td><code class="language-plaintext highlighter-rouge">empty</code></td>
                <td>Giá trị là rỗng, tức là không chứa byte nào. Kiểu giá trị này được dùng để biểu diễn thông tin nhị phân. Khi thuộc tính xuất hiện, thì giá trị của thuộc tính là <code class="language-plaintext highlighter-rouge">true</code>. Khi thuộc tính vắng mặt, thì giá trị của thuộc tính là <code class="language-plaintext highlighter-rouge">false</code>.</td>
                </tr>
            </tbody>
            <tbody>
                <tr>
                <td><code class="language-plaintext highlighter-rouge">u32</code></td>
                <td>Giá trị gồm 4 byte được sắp xếp theo định dạng big-endian. Kiểu giá trị này được dùng để biểu diễn một số nguyên 32 bit.</td>
                </tr>
            </tbody>
            <tbody>
                <tr>
                <td><code class="language-plaintext highlighter-rouge">u64</code></td>
                <td>Giá trị là 2 số kiểu <code class="language-plaintext highlighter-rouge">u32</code>, mỗi số gọi là một <code class="language-plaintext highlighter-rouge">cell</code>. Kiểu giá trị này được dùng để biểu diễn một số nguyên 64 bit. <code class="language-plaintext highlighter-rouge">cell</code> thứ nhất và <code class="language-plaintext highlighter-rouge">cell</code> thứ hai lần lượt là MSB và LSB của số nguyên 64 bit.</td>
                </tr>
            </tbody>
            <tbody>
                <tr>
                <td><code class="language-plaintext highlighter-rouge">string</code></td>
                <td>Giá trị gồm nhiều byte. Mỗi byte, ngoại trừ byte cuối cùng, là một ký tự in ra được. Byte cuối cùng là ký tự <code class="language-plaintext highlighter-rouge">null</code>.</td>
                </tr>
            </tbody>
            <tbody>
                <tr>
                <td><code class="language-plaintext highlighter-rouge">stringlist</code></td>
                <td>Giá trị là một danh sách các chuỗi ký tự kiểu <code class="language-plaintext highlighter-rouge">string</code>.</td>
                </tr>
            </tbody>
            <tbody>
                <tr>
                <td><code class="language-plaintext highlighter-rouge">prop-encoded-array</code></td>
                <td>Giá trị gồm 0 hoặc nhiều byte. Kiểu giá trị này có định dạng phụ thuộc vào từng thuộc tính.</td>
                </tr>
            </tbody>            
            <tbody>
                <tr>
                <td><code class="language-plaintext highlighter-rouge">phandle</code></td>
                <td>Giá trị là 1 số kiểu <code class="language-plaintext highlighter-rouge">u32</code>. Kiểu giá trị này được dùng để biểu diễn một tham chiếu tới một node trong devicetree.</td>
                </tr>
            </tbody>
            </table>
          </div>
      </div>
  </div>
  <!-- /.panel -->
</div>
<!-- /.panel-group -->

Thuộc tính của một node có thể được định nghĩa bởi người dùng. Các thuộc tính đó nên được gắn một chuỗi định danh vào phía trước. Chuỗi định danh có thể là tên công ty hoặc tổ chức đã định nghĩa thuộc tính đó. Ví dụ: `fsl,channel-fifo-len` hoặc `ibm,ppc-interrupt-server#s`.

Tuy nhiên, cũng có những thuộc tính đã được chuẩn hóa. Ví dụ:
<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse02">Thuộc tính <code class="language-plaintext highlighter-rouge">compatible</code></a>
            </h4>
        </div>
        <div id="collapse02" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">stringlist</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Thuộc tính này giúp hệ điều hành chọn được driver phù hợp với thiết bị.</li>
                    <li>Giá trị của thuộc tính này là một danh sách các chuỗi ký tự. Các chuỗi nên được sắp xếp theo thứ tự tương thích với một thiết bị cụ thể cho đến một họ các thiết bị.</li>
                    <li>Mỗi chuỗi ký tự nên tuân thủ định dạng <code class="language-plaintext highlighter-rouge">"manufacturer,model"</code></li>
                    <li>Các chuỗi ký tự chỉ nên bao gồm các ký tự in thường, chữ số, dấu gạch ngang, và nên bắt đầu bằng một chữ cái. Dấu phẩy chỉ nên dùng ngay sau <code class="language-plaintext highlighter-rouge">manufacturer</code>. Không nên sử dụng dấu gạch dưới.</li>
                    </ul>
                </li>
                <li>Ví dụ:
                    <ul>
                    <code class="language-plaintext highlighter-rouge">compatible = "fsl,mpc8641", "ns16550";</code>
                    <p>Trong ví dụ trên, hệ điều hành sẽ tìm driver tương thích với "fsl,mpc8641" trước. Nếu không có, hệ điều hành sẽ tiếp tục tìm driver tương thích với "ns16550".</p>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse03">Thuộc tính <code class="language-plaintext highlighter-rouge">model</code></a>
            </h4>
        </div>
        <div id="collapse03" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">string</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Thuộc tính này dùng để xác định nhà sản xuất của thiết bị.</li>
                    <li>Giá trị của thuộc tính nên tuân thủ định dạng <code class="language-plaintext highlighter-rouge">"manufacturer,model"</code></li>
                    </ul>
                </li>
                <li>Ví dụ:
                    <ul>
                    <code class="language-plaintext highlighter-rouge">model = "fsl,MPC8349EMITX";</code>
                    <p>Trong ví dụ trên, nhà sản xuất thiết bị là Freescale, và model number của thiết bị là MPC8349EMITX.</p>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse04">Thuộc tính <code class="language-plaintext highlighter-rouge">phandle</code></a>
            </h4>
        </div>
        <div id="collapse04" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">u32</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Thuộc tính này dùng để định danh một node trong toàn Device Tree. Giá trị định danh này được sử dụng bởi node khác khi cần tham chiếu tới node chứa thuộc tính này.</li>
                    <li>Thuộc tính này hiếm khi được tìm thấy trong các tệp tin DTS. Trong quá trình biên dịch sang dạng tệp tin DTB, trình biên dịch sẽ tự động chèn thuộc tính này vào.</li>
                    </ul>
                </li>
                <li>Ví dụ:
                    <ul>
                    <div class="language-typescript highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
<span class="nx">pic</span><span class="p">@</span><span class="nd">10000000</span> <span class="p">{</span>
    <span class="nx">phandle</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
    <span class="nx">interrupt</span><span class="o">-</span><span class="nx">controller</span><span class="p">;</span>
    <span class="nx">reg</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mh">0x10000000</span> <span class="mh">0x100</span><span class="o">&gt;</span><span class="p">;</span>
<span class="p">};</span>
                    </code></pre></div></div>
                    <p>Node <code class="language-plaintext highlighter-rouge">pic</code> có một thuộc tính <code class="language-plaintext highlighter-rouge">phandle</code> với số định danh bằng 1. Một node khác có thể tham chiếu tới node <code class="language-plaintext highlighter-rouge">pic</code> thông qua số định danh này.</p>
                    <div class="language-typescript highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
<span class="nx">another</span><span class="o">-</span><span class="nx">device</span><span class="o">-</span><span class="nx">node</span> <span class="p">{</span>
    <span class="nx">interrupt</span><span class="o">-</span><span class="nx">parent</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
<span class="p">};</span>
                    </code></pre></div></div>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse05">Thuộc tính <code class="language-plaintext highlighter-rouge">status</code></a>
            </h4>
        </div>
        <div id="collapse05" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">string</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Thuộc tính này dùng để xác định trạng thái hoạt động của thiết bị.</li>
                    <li>Giá trị của thuộc tính có thể là:
                        <ul>
                        <li><code class="language-plaintext highlighter-rouge">"okay"</code>: Thiết bị hiện đang hoạt động.</li>
                        <li><code class="language-plaintext highlighter-rouge">"disabled"</code>: Thiết bị hiện đang không hoạt động, nhưng có thể hoạt động trong tương lai.</li>
                        <li><code class="language-plaintext highlighter-rouge">"reserved"</code>: Thiết bị hoạt động được, nhưng không nên sử dụng.</li>
                        <li><code class="language-plaintext highlighter-rouge">"fail"</code>: Thiết bị không hoạt động được. Có một lỗi nghiêm trọng được phát hiện trong thiết bị, và nhiều khả năng nó không thể hoạt động trở lại nếu không sửa chữa.</li>
                        <li><code class="language-plaintext highlighter-rouge">"fail-sss"</code>: Thiết bị không hoạt động được. Có một lỗi nghiêm trọng được phát hiện trong thiết bị, và nhiều khả năng nó không thể hoạt động trở lại nếu không sửa chữa. Phần "sss" của giá trị này dùng để chỉ điều kiện lỗi.</li>
                        </ul>
                    </li>
                    <li>Sự vắng mặt của thuộc tính này đồng nghĩa với <code class="language-plaintext highlighter-rouge">status="okay"</code></li>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse06">Thuộc tính <code class="language-plaintext highlighter-rouge">#address-cells</code> và <code class="language-plaintext highlighter-rouge">#size-cells</code></a>
            </h4>
        </div>
        <div id="collapse06" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">u32</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Thuộc tính này được sử dụng trong bất kì node nào có node con. Nó dùng để mô tả cách đánh địa chỉ cho các node con.</li>
                    <li>Thuộc tính <code class="language-plaintext highlighter-rouge">#address-cells</code> xác định số lượng các cell <code class="language-plaintext highlighter-rouge">u32</code> được dùng để biểu diễn trường địa chỉ trong thuộc tính <code class="language-plaintext highlighter-rouge">reg</code> của node con.</li>
                    <li>Thuộc tính <code class="language-plaintext highlighter-rouge">#size-cells</code> xác định số lượng các cell <code class="language-plaintext highlighter-rouge">u32</code> được dùng để biểu diễn trường kích thước dải địa chỉ trong thuộc tính <code class="language-plaintext highlighter-rouge">reg</code> của node con.</li>
                    <li>Tất cả các node nên có 2 thuộc tính này nếu chúng có node con. Nếu thiếu, hệ điều hành sẽ ngầm hiểu <code class="language-plaintext highlighter-rouge">#address-cells</code> = 2 và <code class="language-plaintext highlighter-rouge">#size-cells</code> = 1</li>
                    </ul>
                </li>
                <li>Ví dụ:
                    <ul>
                    <div class="language-typescript highlighter-rouge"><div class="highlight"><pre class="highlight"><code>
<span class="nx">soc</span> <span class="p">{</span>
    <span class="err">#</span><span class="nx">address</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
    <span class="err">#</span><span class="nx">size</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
    <span class="nx">serial</span><span class="p">@</span><span class="nd">4600</span> <span class="p">{</span>
        <span class="nx">compatible</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">ns16550</span><span class="dl">"</span><span class="p">;</span>
        <span class="nx">reg</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mh">0x4600</span> <span class="mh">0x100</span><span class="o">&gt;</span><span class="p">;</span>
        <span class="nx">clock</span><span class="o">-</span><span class="nx">frequency</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">0</span><span class="o">&gt;</span><span class="p">;</span>
        <span class="nx">interrupts</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mh">0xA</span> <span class="mh">0x8</span><span class="o">&gt;</span><span class="p">;</span>
        <span class="nx">interrupt</span><span class="o">-</span><span class="nx">parent</span> <span class="o">=</span> <span class="o">&lt;&amp;</span><span class="nx">ipic</span><span class="o">&gt;</span><span class="p">;</span>
    <span class="p">};</span>
<span class="p">};</span>
                    </code></pre></div></div>
                    <p>Trong ví dụ trên, các thuộc tính <code class="language-plaintext highlighter-rouge">#address-cells</code> và <code class="language-plaintext highlighter-rouge">#size-cells</code> của node <code class="language-plaintext highlighter-rouge">soc</code> đều bằng 1. Điều này có nghĩa là cần 1 cell để biểu diễn địa chỉ và 1 cell để biểu diễn kích thước dải địa chỉ của các node con. Thuộc tính <code class="language-plaintext highlighter-rouge">reg</code> của node con <code class="language-plaintext highlighter-rouge">serial</code> cần tuân thủ quy định này: trường địa chỉ của <code class="language-plaintext highlighter-rouge">reg</code> (0x4600) được biểu diễn bởi 1 cell, trường kích thước dải địa chỉ của <code class="language-plaintext highlighter-rouge">reg</code> (0x100) được biểu diễn bởi 1 cell.</p>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse07">Thuộc tính <code class="language-plaintext highlighter-rouge">reg</code></a>
            </h4>
        </div>
        <div id="collapse07" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">prop-encoded-array</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Thuộc tính này mô tả các dải địa chỉ của các thanh ghi của thiết bị trong không gian địa chỉ của node cha: bắt đầu ở những vị trí nào, kích thước bao nhiêu. Chú ý, không gian địa chỉ của node gốc chính là không gian địa chỉ của CPU.</li>
                    <li>Giá trị của thuộc tính này là một mảng các cặp <code class="language-plaintext highlighter-rouge">(address,size)</code>. Số lượng các cặp là tùy ý, phụ thuộc vào thiết bị.</li>
                    <li>Số lượng các cell u32 cần để biểu diễn địa chỉ và kích thước trong mỗi cặp thì phụ thuộc vào bus mà thiết bị đó cắm vào, và được chỉ định rõ bởi thuộc tính <code class="language-plaintext highlighter-rouge">#address-cells</code> cũng như <code class="language-plaintext highlighter-rouge">#size-cells</code> của node cha.</li>
                    </ul>
                </li>
                <li>Ví dụ:
                    <ul>
                    <p>Giả sử một GPIO controller bên trong một chip SoC có 2 khối thanh ghi, một khối 32 byte bắt đầu từ địa chỉ 0x101f3000 và một khối khác 256 byte bắt đầu từ địa chỉ 0x101f4000. Thuộc tính <code class="language-plaintext highlighter-rouge">reg</code> của node này sẽ là:</p>
<div class="language-typescript highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="o">/</span> <span class="p">{</span>
    <span class="err">#</span><span class="nx">address</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
    <span class="err">#</span><span class="nx">size</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
    <span class="nx">gpio</span><span class="p">@</span><span class="nd">101f3000</span> <span class="p">{</span>
        <span class="nx">compatible</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">arm,pl061</span><span class="dl">"</span><span class="p">;</span>
        <span class="nx">reg</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mh">0x101f3000</span> <span class="mh">0x20</span> <span class="mh">0x101f4000</span> <span class="mh">0x100</span><span class="o">&gt;</span><span class="p">;</span>
    <span class="p">};</span>
<span class="p">}</span>
</code></pre></div></div>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse08">Thuộc tính <code class="language-plaintext highlighter-rouge">ranges</code></a>
            </h4>
        </div>
        <div id="collapse08" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">empty</code> hoặc <code class="language-plaintext highlighter-rouge">prop-encoded-array</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Thuộc tính này cung cấp một cơ chế ánh xạ không gian địa chỉ của một node con vào không gian địa chỉ của node cha.</li>
                    <li>Giá trị của thuộc tính này có thể là một mảng các bộ ba <code class="language-plaintext highlighter-rouge">(child-bus-address, parent-bus-address, size)</code>. Số lượng các bộ ba là tùy ý.
                        <ul>
                        <li><code class="language-plaintext highlighter-rouge">child-bus-address</code> là một địa chỉ vật lý trong không gian địa chỉ của node con, tức là node chứa thuộc tính <code class="language-plaintext highlighter-rouge">ranges</code> này. Số lượng các cell u32 cần để biểu diễn <code class="language-plaintext highlighter-rouge">child-bus-address</code> được chỉ định rõ bởi thuộc tính <code class="language-plaintext highlighter-rouge">#address-cells</code> của node con.</li>
                        <li><code class="language-plaintext highlighter-rouge">parent-bus-address</code> là một địa chỉ vật lý trong không gian địa chỉ của node cha của node chứa thuộc tính <code class="language-plaintext highlighter-rouge">ranges</code> này. Số lượng các cell u32 cần để biểu diễn <code class="language-plaintext highlighter-rouge">parent-bus-address</code> được chỉ định rõ bởi thuộc tính <code class="language-plaintext highlighter-rouge">#address-cells</code> của node cha của node chứa thuộc tính <code class="language-plaintext highlighter-rouge">ranges</code>.</li>
                        <li><code class="language-plaintext highlighter-rouge">size</code> là kích thước của dải địa chỉ trong không gian địa chỉ của node con, tức là node chứa thuộc tính <code class="language-plaintext highlighter-rouge">ranges</code> này. Số lượng các cell u32 cần để biểu diễn <code class="language-plaintext highlighter-rouge">size</code> được chỉ định rõ bởi thuộc tính <code class="language-plaintext highlighter-rouge">#size-cells</code> của node con.</li>
                        </ul>
                    </li>
                    <li>Nếu giá trị của thuộc tính này là <code class="language-plaintext highlighter-rouge">empty</code> thì không gian địa chỉ con và cha là giống nhau.</li>
                    <li>Nếu thuộc tính này vắng mặt trong một node thì không tồn tại ánh xạ giữa không gian địa chỉ của node đó và node cha của node đó.</li>
                    </ul>
                </li>
                <li>Ví dụ:
                    <ul>
<div class="language-typescript highlighter-rouge"><div class="highlight"><pre class="highlight"><code><span class="o">/</span> <span class="p">{</span>
    <span class="nx">compatible</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">acme,coyotes-revenge</span><span class="dl">"</span><span class="p">;</span>
    <span class="err">#</span><span class="nx">address</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
    <span class="err">#</span><span class="nx">size</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
    <span class="p">...</span>
    <span class="nx">external</span><span class="o">-</span><span class="nx">bus</span> <span class="p">{</span>
        <span class="err">#</span><span class="nx">address</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">2</span><span class="o">&gt;</span><span class="p">;</span>
        <span class="err">#</span><span class="nx">size</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
        <span class="nx">ranges</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">0</span> <span class="mi">0</span>  <span class="mh">0x10100000</span>   <span class="mh">0x10000</span>     <span class="c1">// Chipselect 0 (Ethernet controller)</span>
                  <span class="mi">1</span> <span class="mi">0</span>  <span class="mh">0x10160000</span>   <span class="mh">0x10000</span>     <span class="c1">// Chipselect 1 (i2c controller)</span>
                  <span class="mi">2</span> <span class="mi">0</span>  <span class="mh">0x30000000</span>   <span class="mh">0x1000000</span><span class="o">&gt;</span><span class="p">;</span> <span class="c1">// Chipselect 2 (NOR Flash)</span>
        <span class="nx">ethernet</span><span class="p">@</span><span class="nd">0</span><span class="p">,</span><span class="mi">0</span> <span class="p">{</span>
            <span class="nx">compatible</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">smc,smc91c111</span><span class="dl">"</span><span class="p">;</span>
            <span class="nx">reg</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">0</span> <span class="mi">0</span> <span class="mh">0x10000</span><span class="o">&gt;</span><span class="p">;</span>
        <span class="p">};</span>
        <span class="nx">i2c</span><span class="p">@</span><span class="nd">1</span><span class="p">,</span><span class="mi">0</span> <span class="p">{</span>
            <span class="nx">compatible</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">acme,a1234-i2c-bus</span><span class="dl">"</span><span class="p">;</span>
            <span class="err">#</span><span class="nx">address</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span><span class="o">&gt;</span><span class="p">;</span>
            <span class="err">#</span><span class="nx">size</span><span class="o">-</span><span class="nx">cells</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">0</span><span class="o">&gt;</span><span class="p">;</span>
            <span class="nx">reg</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">1</span> <span class="mi">0</span> <span class="mh">0x10000</span><span class="o">&gt;</span><span class="p">;</span>
            <span class="nx">rtc</span><span class="p">@</span><span class="nd">58</span> <span class="p">{</span>
                <span class="nx">compatible</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">maxim,ds1338</span><span class="dl">"</span><span class="p">;</span>
                <span class="nx">reg</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">58</span><span class="o">&gt;</span><span class="p">;</span>
            <span class="p">};</span>
        <span class="p">};</span>
        <span class="nx">flash</span><span class="p">@</span><span class="nd">2</span><span class="p">,</span><span class="mi">0</span> <span class="p">{</span>
            <span class="nx">compatible</span> <span class="o">=</span> <span class="dl">"</span><span class="s2">samsung,k8f1315ebm</span><span class="dl">"</span><span class="p">,</span> <span class="dl">"</span><span class="s2">cfi-flash</span><span class="dl">"</span><span class="p">;</span>
            <span class="nx">reg</span> <span class="o">=</span> <span class="o">&lt;</span><span class="mi">2</span> <span class="mi">0</span> <span class="mh">0x1000000</span><span class="o">&gt;</span><span class="p">;</span>
        <span class="p">};</span>
    <span class="p">};</span>
<span class="p">};</span>
</code></pre></div></div>
                    <p>Các node con trực tiếp của node gốc, trong ví dụ này là node <code class="language-plaintext highlighter-rouge">external-bus</code>, sẽ nằm trong không gian địa chỉ của node gốc, cũng chính là không gian địa chỉ của CPU. Tuy nhiên, các node mà không trực tiếp là con của node gốc sẽ không sử dụng không gian địa chỉ của CPU. Để giúp xác định địa chỉ của các node con <code class="language-plaintext highlighter-rouge">ethernet</code>, <code class="language-plaintext highlighter-rouge">i2c</code> và <code class="language-plaintext highlighter-rouge">flash</code> trong không gian địa chỉ của CPU, node <code class="language-plaintext highlighter-rouge">external-bus</code> sử dụng thuộc tính <code class="language-plaintext highlighter-rouge">ranges</code> gồm 3 ánh xạ. Mỗi ánh xạ là một bộ gồm ba trường: địa chỉ của node con trong không gian địa chỉ của <code class="language-plaintext highlighter-rouge">external-bus</code> gồm 2 cell, địa chỉ của node con trong không gian địa chỉ của node cha của <code class="language-plaintext highlighter-rouge">external-bus</code> (ở đây là không gian địa chỉ của node gốc, cũng chính là không gian địa chỉ của CPU) gồm 1 cell, và kích thước dải địa chỉ của node con gồm 1 cell.</p>
                    <ul>
                    <li>Bộ ba thứ nhất: dải địa chỉ 0..0xffff của chip 0 sẽ được ánh xạ vào dải địa chỉ 0x10100000..0x1010ffff</li>
                    <li>Bộ ba thứ hai: dải địa chỉ 0..0xffff của chip 1 sẽ được ánh xạ vào dải địa chỉ 0x10160000..0x1016ffff</li>
                    <li>Bộ ba thứ ba: dải địa chỉ 0..0xffffff của chip 2 sẽ được ánh xạ vào dải địa chỉ 0x30000000..0x30ffffff</li>
                    </ul>
                    <p>Cũng chú ý rằng, node <code class="language-plaintext highlighter-rouge">i2c</code> không có thuộc tính <code class="language-plaintext highlighter-rouge">ranges</code>. Điều đó có nghĩa là, các thiết bị trên bus I2C không thể ánh xạ được vào không gian địa chỉ của <code class="language-plaintext highlighter-rouge">external-bus</code>, và do đó cũng không thể ánh xạ được vào không gian địa chỉ của CPU. Tức là, CPU không thể truy cập trực tiếp vào các thanh ghi của RTC, mà chỉ có thể truy cập một cách gián tiếp thông qua I2C controller.</p>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>


Ngoài các thuộc tính trên, còn có một số thuộc tính đặc biệt liên quan tới tín hiệu ngắt:

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse09">Thuộc tính <code class="language-plaintext highlighter-rouge">interrupt-controller</code></a>
            </h4>
        </div>
        <div id="collapse09" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">empty</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Thuộc tính này được dùng để khai báo một node đại diện cho thiết bị tiếp nhận các tín hiệu ngắt, tức là Interrupt Controller.</li>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse10">Thuộc tính <code class="language-plaintext highlighter-rouge">#interrupt-cells</code></a>
            </h4>
        </div>
        <div id="collapse10" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">u32</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Đây là một thuộc tính của node đại diện cho Interrupt Controller.</li>
                    <li>Thuộc tính này xác định số cell cần dùng để biểu diễn một interrupt specifier.</li>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse11">Thuộc tính <code class="language-plaintext highlighter-rouge">interrupt-parent</code></a>
            </h4>
        </div>
        <div id="collapse11" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">phandle</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Đây là một thuộc tính của node đại diện cho thiết bị tạo tín hiệu ngắt.</li>
                    <li>Thuộc tính này dùng để xác định một liên kết từ một thiết bị tạo ngắt đến một Interrupt Controller.</li>
                    <li>Giá trị của thuộc tính này là một tham chiếu tới node đại diện cho Interrupt Controller.</li>
                    <li>Nếu một node không chứa <code class="language-plaintext highlighter-rouge">interrupt-parent</code> thì sẽ kế thừa thuộc tính này từ node cha.</li>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>

<ul><li>
    <div class="panel-group" id="accordion">
    <div class="panel panel-default">
        <div class="panel-heading">
            <h4 class="panel-title">
                <a class="noCrossRef accordion-toggle" data-toggle="collapse" data-parent="#accordion" href="#collapse12">Thuộc tính <code class="language-plaintext highlighter-rouge">interrupt</code></a>
            </h4>
        </div>
        <div id="collapse12" class="panel-collapse collapse noCrossRef">
            <div class="panel-body">
                <ul>
                <li>Kiểu giá trị: <code class="language-plaintext highlighter-rouge">prop-encoded-array</code></li>
                <li>Cách sử dụng:
                    <ul>
                    <li>Đây là một thuộc tính của node đại diện cho thiết bị tạo tín hiệu ngắt.</li>
                    <li>Thuộc tính này dùng để định nghĩa một hoặc nhiều tín hiệu ngắt tạo ra bởi thiết bị. Mỗi tín hiệu ngắt được mô tả bằng một interrupt specifier.</li>
                    <li>Giá trị của thuộc tính này là một mảng các interrupt specifier. Số lượng các interrupt specifier là tùy ý. Số lượng cell biểu diễn một interrupt specifier được chỉ định bởi thuộc tính <code class="language-plaintext highlighter-rouge">#interrupt-cells</code> của node đại diện cho Interrupt Controller.</li>
                    </ul>
                </li>
                </ul>
            </div>
        </div>
    </div>
    <!-- /.panel -->
    </div>
    <!-- /.panel-group -->
</li></ul>


Để hiểu hơn về các thuộc tính liên quan tới tín hiệu ngắt, hãy cùng xem xét ví dụ sau:

```typescript
/dts-v1/;

/ {
    compatible = "acme,coyotes-revenge";
    #address-cells = <1>;
    #size-cells = <1>;
    interrupt-parent = <&intc>;

    cpus {
        #address-cells = <1>;
        #size-cells = <0>;
        cpu@0 {
            compatible = "arm,cortex-a9";
            reg = <0>;
        };
        cpu@1 {
            compatible = "arm,cortex-a9";
            reg = <1>;
        };
    };

    serial@101f0000 {
        compatible = "arm,pl011";
        reg = <0x101f0000 0x1000>;
        interrupts = <1 0>;
    };

    gpio@101f3000 {
        compatible = "arm,pl061";
        reg = <0x101f3000 0x1000
               0x101f4000 0x0010>;
        interrupts = <3 0>;
    };

    intc: interrupt-controller@10140000 {
        compatible = "arm,pl190";
        reg = <0x10140000 0x1000>;
        interrupt-controller;
        #interrupt-cells = <2>;
    };
};
```

Trong ví dụ trên,
- Device Tree có một node `interrupt-controller@10140000`.
    - Node này được gán nhãn `intc`. Nhãn này được dùng để gán giá trị cho thuộc tính `interrupt-parent` của node gốc.
    - Node này có 2 thuộc tính:
        - Thuộc tính `interrupt-controller` thể hiện node này đại diện cho Interrupt Controller.
        - Thuộc tính `#interrupt-cells` có giá trị bằng 2. Điều đó có nghĩa là cần 2 cell để biểu diễn một tín hiệu ngắt. Trong ngữ cảnh này, cell thứ nhất biểu diễn số hiệu ngắt, còn cell thứ hai biểu diễn cờ ngắt (ngắt cảm nhận theo sườn hay theo mức, sườn lên hay sườn xuống, mức cao hay mức thấp).
- Các node con của node gốc, ở đây là `serial@101f0000` và `gpio@101f3000`, sử dụng thuộc tính `interrupt` để mô tả các tín hiệu ngắt. Vì các node này kế thừa thuộc tính `interrupt-parent` của node gốc, nên tín hiệu ngắt từ `serial@101f0000` và `gpio@101f3000` được kết nối vào `interrupt-controller@10140000`.

## Ví dụ thực tế về DTS.

Trong mã nguồn của Linux kernel, Device Tree có thể được tìm thấy trong thư mục arch/<architecture>/boot/dts/
TBD

arch/arm64/boot/dts/

zynqmp.dtsi
zynqmp-zcu102-revA.dts
zynqmp-zcu102-revB.dts
zynqmp-zcu102-rev1.0.dts
zynqmp-zcu102-rev1.1.dts


## Tham khảo

- [Device Tree usage](https://elinux.org/Device_Tree_Usage)
- [Device Tree specification v0.4](https://github.com/devicetree-org/devicetree-specification/releases/tag/v0.4)

