GO Simple Tunnel
======

### GO đường hầm bảo mật được triển khai bằng ngôn ngữ

[![GoDoc](https://godoc.org/github.com/ginuerzh/gost?status.svg)](https://godoc.org/github.com/ginuerzh/gost)
[![Go Report Card](https://goreportcard.com/badge/github.com/ginuerzh/gost)](https://goreportcard.com/report/github.com/ginuerzh/gost)
[![codecov](https://codecov.io/gh/ginuerzh/gost/branch/master/graphs/badge.svg)](https://codecov.io/gh/ginuerzh/gost/branch/master)
[![GitHub release](https://img.shields.io/github/release/ginuerzh/gost.svg)](https://github.com/ginuerzh/gost/releases/latest)
[![Docker](https://img.shields.io/docker/pulls/ginuerzh/gost.svg)](https://hub.docker.com/r/ginuerzh/gost/)
[![gost](https://snapcraft.io/gost/badge.svg)](https://snapcraft.io/gost)
 
[English README](README_en.md)

### ！！！[Đã có phiên bản V3, chào mừng các bạn đến trải nghiệm sớm](https://latest.gost.run)！！！

Đặc tính
------

* Nghe đa cổng
* Có thể đặt proxy chuyển tiếp, hỗ trợ chuyển tiếp nhiều cấp (chuỗi proxy)
* Hỗ trợ giao thức proxy HTTP/HTTPS/HTTP2/SOCKS4(A)/SOCKS5 tiêu chuẩn
* Hỗ trợ proxy web[Phòng thủ phát hiện](https://v2.gost.run/probe_resist/)
* [Hỗ trợ nhiều loại Backend](https://v2.gost.run/configuration/)
* [Proxy SOCKS5 hỗ trợ mã hóa thương lượng TLS](https://v2.gost.run/socks/)
* [Tunnel UDP over TCP](https://v2.gost.run/socks/)
* [Proxy trong suốt TCP / UDP](https://v2.gost.run/redirect/)
* [Chuyển tiếp cổng TCP / UDP cục bộ / từ xa](https://v2.gost.run/port-forwarding/)
* [Hỗ trợ giao thức Shadowsocks (TCP / UDP)](https://v2.gost.run/ss/)
* [Hỗ trợ cho proxy SNI](https://v2.gost.run/sni/)
* [Kiểm soát truy cập](https://v2.gost.run/permission/)
* [Cân bằng tải](https://v2.gost.run/load-balancing/)
* [Kiểm soát định tuyến](https://v2.gost.run/bypass/)
* DNS[Phân tích cú pháp](https://v2.gost.run/resolver/) Và [DNS](https://v2.gost.run/dns/)
* [TUN / TAP thiết bị](https://v2.gost.run/tuntap/)

Trang wiki: [v2.gost.run](https://v2.gost.run)

Nhóm thảo luận Telegram: <https://t.me/gogost>

Nhóm thảo luận trên Google: <https://groups.google.com/d/forum/go-gost>

Cài đặt
------

#### Tập tin nhị phân

<https://github.com/ginuerzh/gost/releases>

#### Biên dịch mã nguồn

```bash
git clone https://github.com/ginuerzh/gost.git
cd gost/cmd/gost
go build
```

#### Docker

```bash
docker pull ginuerzh/gost
```

#### Homebrew

```bash
brew install gost
```

#### Cửa hàng Ubuntu


```bash
sudo snap install core
sudo snap install gost
```

Bắt đầu nhanh chóng
------

#### Không đặt proxy chuyển tiếp

<img src="https://ginuerzh.github.io/images/gost_01.png" />

* Hoạt động như một proxy HTTP/SOCKS5 tiêu chuẩn

```bash
gost -L=:8080
```

* Đặt thông tin xác thực proxy

```bash
gost -L=admin:123456@localhost:8080
```

* Nghe đa cổng

```bash
gost -L=http2://:443 -L=socks5://:1080 -L=ss://aes-128-cfb:123456@:8338
```

#### Thiết lập proxy chuyển tiếp

<img src="https://ginuerzh.github.io/images/gost_02.png" />

```bash
gost -L=:8080 -F=192.168.1.1:8081
```

* Chuyển tiếp xác thực proxy

```bash
gost -L=:8080 -F=http://admin:123456@192.168.1.1:8081
```

#### Thiết lập proxy chuyển tiếp nhiều cấp (chuỗi proxy)

<img src="https://ginuerzh.github.io/images/gost_03.png" />

```bash
gost -L=:8080 -F=quic://192.168.1.1:6121 -F=socks5+wss://192.168.1.2:1080 -F=http2://192.168.1.3:443 ... -F=a.b.c.d:NNNN
```

gost chuyển tiếp yêu cầu tới a.b.c.d: NNNN thông qua chuỗi proxy theo thứ tự được đặt bởi -F và mỗi proxy chuyển tiếp có thể là bất kỳ proxy loại HTTP / HTTPS / HTTP2 / SOCKS4 / SOCKS5 / Shadowsocks nào.

#### 本地端口转发(TCP)

```bash
gost -L=tcp://:2222/192.168.1.1:22 [-F=...]
```

Chuyển tiếp dữ liệu trên cổng TCP cục bộ 2222 (thông qua chuỗi proxy) tới 192.168.1.1:22. Khi phần cuối của chuỗi proxy (tham số -F cuối cùng) là loại kênh chuyển tiếp SSH, gost sẽ trực tiếp sử dụng chức năng chuyển tiếp cổng cục bộ của SSH:

```bash
gost -L=tcp://:2222/192.168.1.1:22 -F forward+ssh://:2222
```

#### Chuyển tiếp cổng cục bộ (UDP)

```bash
gost -L=udp://:5353/192.168.1.1:53?ttl=60 [-F=...]
```

Chuyển tiếp dữ liệu trên cổng UDP cục bộ 5353 (thông qua chuỗi proxy) tới 192.168.1.1:53.
Mỗi kênh chuyển tiếp có một khoảng thời gian chờ. Khi vượt quá thời gian này và không có trao đổi dữ liệu trong khoảng thời gian này, kênh sẽ bị đóng. Thời gian chờ có thể được đặt bằng tham số `ttl`, giá trị mặc định là 60 giây.

**Lưu ý:** Khi chuyển tiếp dữ liệu UDP, nếu có chuỗi proxy, phần cuối của chuỗi proxy (tham số -F cuối cùng) phải là proxy loại gost SOCKS5 và gost sẽ sử dụng UDP qua TCP để chuyển tiếp.

#### Chuyển tiếp cổng từ xa (TCP)

```bash
gost -L=rtcp://:2222/192.168.1.1:22 [-F=... -F=socks5://172.24.10.1:1080]
```
Chuyển tiếp dữ liệu trên 172.24.10.1:2222 (thông qua chuỗi proxy) đến 192.168.1.1:22. Khi phần cuối của chuỗi proxy (tham số -F cuối cùng) là loại kênh chuyển tiếp SSH, gost sẽ trực tiếp sử dụng chức năng chuyển tiếp cổng từ xa SSH:

```bash
gost -L=rtcp://:2222/192.168.1.1:22 -F forward+ssh://:2222
```

#### Chuyển tiếp cổng từ xa (UDP)

```bash
gost -L=rudp://:5353/192.168.1.1:53?ttl=60 [-F=... -F=socks5://172.24.10.1:1080]
```
Chuyển tiếp dữ liệu trên 172.24.10.1:5353 (thông qua chuỗi proxy) tới 192.168.1.1:53.
Mỗi kênh chuyển tiếp có một khoảng thời gian chờ. Khi vượt quá thời gian này và không có trao đổi dữ liệu trong khoảng thời gian này, kênh sẽ bị đóng. Thời gian chờ có thể được đặt bằng tham số `ttl`, giá trị mặc định là 60 giây.

**Lưu ý:** Khi chuyển tiếp dữ liệu UDP, nếu có chuỗi proxy, phần cuối của chuỗi proxy (tham số -F cuối cùng) phải là proxy loại GOST SOCKS5 và gost sẽ sử dụng UDP-over-TCP để chuyển tiếp .

#### HTTP2

HTTP2 của gost hỗ trợ hai chế độ:
* Là một proxy HTTP2 tiêu chuẩn và tương thích ngược với proxy HTTPS.
* Vận chuyển các giao thức khác dưới dạng kênh.

##### Chế độ proxy
Người phục vụ:
```bash
gost -L=http2://:443
```
Khách hàng:
```bash
gost -L=:8080 -F=http2://server_ip:443
```

##### Chế độ kênh
Người phục vụ:
```bash
gost -L=h2://:443
```
Khách hàng:
```bash
gost -L=:8080 -F=h2://server_ip:443
```

#### QUIC
Sự hỗ trợ của gost dành cho QUIC dựa trên[quic-go](https://github.com/lucas-clemente/quic-go)thư viện.

Người phục vụ:
```bash
gost -L=quic://:6121
```

khách hàng:
```bash
gost -L=:8080 -F=quic://server_ip:6121
```

**Lưu ý:** Chế độ QUIC chỉ có thể được sử dụng làm nút đầu tiên của chuỗi proxy.

#### KCP
sự hỗ trợ của gost cho KCP dựa trên[kcp-go](https://github.com/xtaci/kcp-go) và [kcptun](https://github.com/xtaci/kcptun)库。

Người phục vụ:
```bash
gost -L=kcp://:8388
```

Khách hàng:
```bash
gost -L=:8080 -F=kcp://server_ip:8388
```

gost sẽ tự động tải tệp cấu hình kcp.json (nếu nó tồn tại) trong thư mục làm việc hiện tại hoặc bạn có thể chỉ định thủ công đường dẫn tệp cấu hình thông qua các tham số:
```bash
gost -L=kcp://:8388?c=/path/to/conf/file
```

**Lưu ý:** Chế độ KCP chỉ có thể được sử dụng làm nút đầu tiên của chuỗi proxy.

#### SSH
SSH của gost hỗ trợ hai chế độ:
* Là một kênh chuyển tiếp, được sử dụng với chuyển tiếp cổng TCP cục bộ / từ xa.
* Vận chuyển các giao thức khác dưới dạng kênh.

##### Chế độ chuyển tiếp
Người phục vụ:
```bash
gost -L=forward+ssh://:2222
```
Khách hàng:
```bash
gost -L=rtcp://:1222/:22 -F=forward+ssh://server_ip:2222
```

##### Chế độ kênh
Người phục vụ:
```bash
gost -L=ssh://:2222
```
Khách hàng:
```bash
gost -L=:8080 -F=ssh://server_ip:2222?ping=60
```

Thời gian gửi gói theo nhịp tim có thể được đặt thông qua tham số `ping`, tính bằng giây. Các gói nhịp tim không được gửi theo mặc định.


#### Proxy minh bạch
Proxy minh bạch dựa trên iptables.

```bash
gost -L=redirect://:12345 -F=http2://server_ip:443
```

#### obfs4
Chức năng này được cung cấp bởi[@isofew](https://github.com/isofew)Góp phần.

Người phục vụ:
```bash
gost -L=obfs4://:443
```

Khi máy chủ đang chạy, nó sẽ in ra địa chỉ kết nối trên bảng điều khiển để máy khách sử dụng:
```
obfs4://:443/?cert=4UbQjIfjJEQHPOs8vs5sagrSXx1gfrDCGdVh2hpIPSKH0nklv1e4f29r7jb91VIrq4q5Jw&iat-mode=0
```

Khách hàng:
```
gost -L=:8888 -F='obfs4://server_ip:443?cert=4UbQjIfjJEQHPOs8vs5sagrSXx1gfrDCGdVh2hpIPSKH0nklv1e4f29r7jb91VIrq4q5Jw&iat-mode=0'
```

Cơ chế mã hóa
------

#### HTTP

Đối với HTTP, TLS có thể được sử dụng để mã hóa toàn bộ quy trình giao tiếp, tức là proxy HTTPS:

Người phục vụ:

```bash
gost -L=https://:443
```
Khách hàng:

```bash
gost -L=:8080 -F=http+tls://server_ip:443
```

#### HTTP2

Chế độ proxy HTTP2 của gost chỉ hỗ trợ giao thức HTTP2 được mã hóa bằng TLS và không hỗ trợ truyền HTTP2 bản rõ.

Chế độ kênh HTTP2 của gost hỗ trợ cả chế độ được mã hóa (h2) và bản rõ (h2c).
#### SOCKS5

gost hỗ trợ các phương thức no-auth (0x00) và user / pass (0x02) của giao thức SOCKS5 tiêu chuẩn và mở rộng hai phương thức trên cơ sở này: tls (0x80) và tls-auth (0x82) để mã hóa dữ liệu.

Người phục vụ:

```bash
gost -L=socks5://:1080
```

Khách hàng:

```bash
gost -L=:8080 -F=socks5://server_ip:1080
```

Nếu cả hai đầu đều là gost (như trên) thì quá trình truyền dữ liệu sẽ được mã hóa (thương lượng để sử dụng phương thức tls hoặc tls-auth), nếu không, hãy sử dụng SOCKS5 tiêu chuẩn để giao tiếp (no-auth hoặc user / pass method).
#### Shadowsocks
Hỗ trợ của gost cho shadowsocks dựa trên[shadowsocks-go](https://github.com/shadowsocks/shadowsocks-go) thư viện.

Người phục vụ:

```bash
gost -L=ss://chacha20:123456@:8338
```
Khách hàng:

```bash
gost -L=:8080 -F=ss://chacha20:123456@server_ip:8338
```

##### Shadowsocks UDP relay

Hiện tại chỉ có máy chủ hỗ trợ UDP Relay.
Người phục vụ:

```bash
gost -L=ssu://chacha20:123456@:8338
```

#### TLS
gost có chứng chỉ TLS tích hợp sẵn, nếu bạn cần sử dụng chứng chỉ TLS khác, có hai cách:
* Đặt hai tệp cert.pem (khóa công khai) và key.pem (khóa riêng tư) trong thư mục đang chạy của gost. Gost sẽ tự động tải tệp cert.pem và key.pem trong thư mục đang chạy.
* Sử dụng tham số để chỉ định đường dẫn tệp chứng chỉ:
```bash
gost -L="http2://:443?cert=/path/to/my/cert/file&key=/path/to/my/key/file"
```

Đối với máy khách, chứng chỉ máy chủ và xác minh tên miền có thể được kích hoạt bằng tham số `secure`:
```bash
gost -L=:8080 -F="http2://server_domain_name:443?secure=true"
```

Đối với máy khách, bạn có thể chỉ định chứng chỉ CA cho[Ghim chứng chỉ](https://en.wikipedia.org/wiki/Transport_Layer_Security#Certificate_pinning)(Certificate Pinning):
```bash
gost -L=:8080 -F="http2://:443?ca=ca.pem"
```
Tính năng ghim chứng chỉ được cung cấp bởi[@sheerun](https://github.com/sheerun)Góp phần.
