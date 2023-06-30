介绍：

利用 Nginx 支持 SNI 分流特性，对 VLESS+Vision+TLS、Trojan+TCP+TLS、HTTP/3 server 进行 SNI 分流（四层转发），实现除 Xray 的 mKCP 应用外各应用共用 443 端口。其中 Caddy 为 VLESS+Vision+TLS 与 Trojan+TCP+TLS 提供 WEB 应用（回落应用），为 Xray 的 WebSocket、H2C、gRPC 提供反向代理，为 forwardproxy 插件提供正向代理，其应用如下：

1、E=VLESS+Vision+TLS（回落/分流配置，TLS 由自己启用及处理。）

2、F=Trojan+TCP+TLS（回落/分流配置，TLS 由自己启用及处理。）

3、B=VMess+WebSocket+TLS（TLS 由 Caddy 提供及处理，不需配置。）

4、D=VLESS+H2C+TLS（TLS 由 Caddy 提供及处理，不需配置。）

5、G=Shadowsocks+gRPC+TLS（TLS 由 Caddy 提供及处理，不需配置。）

6、A=VLESS+mKCP+seed

7、N=NaiveProxy（基于 Caddy 的改进版 forwardproxy 插件实现，TLS 由 Caddy 提供及处理。）

注意：

1、Nginx 支持 SNI 分流需要 Nginx 包含 stream_core_module 和 stream_ssl_preread_module 模块。

2、Xray 版本不小于 v1.7.2 才完美支持 VLESS 协议的 XTLS Vision 应用。

3、Xray 的监听地址不支持 Shadowsocks 协议使用 UDS 监听。

4、Caddy 支持 H2C server 与 HTTP/1.1 server 共用一个端口或一个进程。

5、Caddy 版本不小于 v2.6.0 才支持 H2C/gRPC proxy 的 UDS 转发。

6、使用本人 Releases 中编译好的 Caddy 文件，可同时支持 H2C server、H2C/gRPC proxy、NaiveProxy 等应用。

7、本示例 NaiveProxy 除了支持 HTTP/2 代理应用，还同时支持 HTTP/3 代理应用，即 QUIC 协议传输。若 NaiveProxy 使用 HTTP/3 代理应用，建议[增加 UDP 接收缓冲区大小](https://github.com/lucas-clemente/quic-go/wiki/UDP-Receive-Buffer-Size)。

8、本示例所需 TLS 证书由 Caddy（内置 ACME 客户端） 提供，实现 TLS 证书自动申请及更新。

9、配置1：使用 Local Loopback 连接，且启用了 PROXY protocol。配置2：使用 UDS 连接（对应 HTTP/3 server、Shadowsocks+gRPC+TLS 除外），且启用了 PROXY protocol。

10、本示例 F 兼容原版 Trojan 的服务端应用，即可使用 Trojan 或 Trojan-Go 客户端连接。

11、若仅实现科学上网、且不需要 NaiveProxy 支持 HTTP/3 代理应用，推荐采用 [Xray(E+F+B+D+G+A)+Caddy(N)](https://github.com/lxhao61/integrated-examples/tree/main/Xray(E%2BF%2BB%2BD%2BG%2BA)%2BCaddy(N)) 示例。
