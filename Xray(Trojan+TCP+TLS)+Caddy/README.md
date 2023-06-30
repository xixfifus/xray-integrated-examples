介绍：

本示例配置为 Trojan+TCP+TLS 应用。Xray 服务端前置（监听 443 端口）处理来自墙内的 HTTP/2或HTTPS 请求，如果是合法的 Xray 客户端请求，那么为该请求提供服务（科学上网）；否则将已解除 TLS 的流量请求回落（转发）给 Caddy，由 Caddy 为其提供 WEB 应用（回落应用）。

原理：

默认流程：Xray client <-------- TCP+TLS（HTTP/2或HTTPS） -------> Xray server  
回落流程：WEB client <--------------- HTTP/2或HTTPS ---------------> Xray server <-- H2C或HTTP/1.1 --> Caddy（WEB server）

注意：

1、Caddy 支持 H2C server 与 HTTP/1.1 server 共用一个端口或一个进程。

2、本示例所需 TLS 证书由 Caddy（内置 ACME 客户端） 提供，实现 TLS 证书自动申请及更新。

3、配置1：使用 Local Loopback 连接，且启用了 PROXY protocol。配置2：使用 UDS 连接，且启用了 PROXY protocol。

4、本示例兼容原版 Trojan 的服务端应用，即可使用 Trojan 或 Trojan-Go 客户端连接。
