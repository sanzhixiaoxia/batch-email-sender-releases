# 邮件营销助手（Batch Email Sender）— 桌面版发布物

这个仓库**只放发布物**：Windows 安装包的 GitHub Release 资产与自动更新描述文件（`latest.yml`）。没有源码。

- 源码在私有仓库 [`sanzhixiaoxia/batch-email-sender`](https://github.com/sanzhixiaoxia/batch-email-sender)，本仓库的每次发布由那边的 `release-desktop` 分支打包工作流自动创建。
- 本仓库存在且公开的原因：桌面客户端的应用内更新走 `electron-updater` 的 GitHub provider，匿名读取 `releases/latest` 与 `latest.yml`。**私有仓库里这些请求一律 404**（实测 `latest.yml` 匿名访问 404），更新链路根本走不通；GitHub Release 资源不计入 Actions 制品额度，也不受仓库可见性影响的只有「私有」这一条。
- 每个版本在本仓库的 tag（如 `v2.0.202`）只是发布物锚点，**不指向源码提交**；源码侧的对应 tag 打在私有仓库的 bump 提交上，两边版本号一致。
- 安装包由自签名代码签名证书签署（`CN=BatchEmailSender Self-Signed Code Signing`，SHA-1 指纹 `D40D0F9F0262DDF1DEEB5A336F7708692F0EFDA8`）。自签名不在系统信任链里，首次运行会有 SmartScreen 提示属预期，消除方法见私有仓库 README 的「自动更新与代码签名」一节。

## ⚠️ 这个仓库不能改名、不能转私有、不能删

客户端安装包里的 `app-update.yml` 是**打包时写死**的一段配置，装到用户机器上就再也改不了。它现在指向：

```
owner: sanzhixiaoxia
repo: batch-email-sender-releases
```

所以只要本仓库改名、转成私有、或者被删掉，**所有已装机（v2.0.203 及之后）的应用内更新立刻失效**——用户那边表现为「检查更新失败」，而我们这边打包与发布照样全绿，收不到任何报错。同理，某版本的 Release 或其中的 `latest.yml` 资源被删，那个版本往后的更新也断。

## Feed Canary：线上更新链的自检

`.github/workflows/feed-canary.yml` 是本仓库唯一一个工作流，每天跑两次（03:17 / 15:17 UTC），**不带任何凭据**走一遍客户端真正会走的路径：

| 检查项 | 期望 | 坏掉的表现 |
| --- | --- | --- |
| `releases.atom` | 200 | 仓库转私有 / Release 全删 → 客户端查不到任何版本 |
| `releases/latest`（跟随后） | 200 且落到 `/tag/vX` | 定不到 tag，取不到 `latest.yml` |
| `download/<tag>/latest.yml` | 200 且 `version` 与 tag 一致 | `ERR_UPDATER_CHANNEL_FILE_NOT_FOUND` / 反复提示同一版本 |
| 安装包 HEAD + Range | 206 且 `Content-Range` 总长 = `latest.yml` 的 `size` | 客户端下载后 sha512/size 校验失败，更新装不上 |
| `.blockmap` | 200（缺则仅告警） | 差量更新退化为整包下载 |

任何一项硬失败 → run 变红，GitHub 会通知仓库 owner。公开仓库的 Actions 分钟数不计量，这个自检不占用私有源码仓库的额度。

探测脚本的**受控源不在本仓库**，而在私有源码仓库的 `.github/release-shelf/feed-canary.yml`；那边的 `scripts/check-shelf-watchdog.mjs` 会在每次发版时比对线上部署态与受控源的 git blob，逐字节不同就把发版拦红。要改这个自检，改源码仓库那份，然后跑 `GH_TOKEN=… node scripts/deploy-shelf-watchdog.mjs` 同步过来。

## 下载

去 [Releases](../../releases) 页取 `BatchEmailSender-Setup-<版本>.exe`。macOS 产物未签名未公证（自签无法通过 Apple 公证），Linux 产物当前不产出。

## 注意

发布物公开意味着二进制可被任何人下载与逆向，但不含源码、密钥、数据库或任何账号凭据。私有仓库的 Secrets 不在此列。
