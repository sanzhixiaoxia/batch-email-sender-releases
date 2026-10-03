# 邮件营销助手（Batch Email Sender）— 桌面版发布物

这个仓库**只放发布物**：Windows 安装包的 GitHub Release 资产与自动更新描述文件（`latest.yml`）。没有源码。

- 源码在私有仓库 [`sanzhixiaoxia/batch-email-sender`](https://github.com/sanzhixiaoxia/batch-email-sender)，本仓库的每次发布由那边的 `release-desktop` 分支打包工作流自动创建。
- 本仓库存在且公开的原因：桌面客户端的应用内更新走 `electron-updater` 的 GitHub provider，匿名读取 `releases/latest` 与 `latest.yml`。**私有仓库里这些请求一律 404**（实测 `latest.yml` 匿名访问 404），更新链路根本走不通；GitHub Release 资源不计入 Actions 制品额度，也不受仓库可见性影响的只有「私有」这一条。
- 每个版本在本仓库的 tag（如 `v2.0.202`）只是发布物锚点，**不指向源码提交**；源码侧的对应 tag 打在私有仓库的 bump 提交上，两边版本号一致。
- 安装包由自签名代码签名证书签署（`CN=BatchEmailSender Self-Signed Code Signing`，SHA-1 指纹 `D40D0F9F0262DDF1DEEB5A336F7708692F0EFDA8`）。自签名不在系统信任链里，首次运行会有 SmartScreen 提示属预期，消除方法见私有仓库 README 的「自动更新与代码签名」一节。

## 下载

去 [Releases](../../releases) 页取 `BatchEmailSender-Setup-<版本>.exe`。macOS 产物未签名未公证（自签无法通过 Apple 公证），Linux 产物当前不产出。

## 注意

发布物公开意味着二进制可被任何人下载与逆向，但不含源码、密钥、数据库或任何账号凭据。私有仓库的 Secrets 不在此列。
