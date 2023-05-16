# MTProto 代理自动安装程序
在 Centos 或 Ubuntu 上安装 MTProtoProxy 的非常小的脚本

## 为什么这个安装程序？
* 生成随机秘密
*所有代理服务器的脚本
* 自动配置防火墙
* 创建一个服务在后台运行并启动
* 在官方代理、Python 代理和 Erlang 代理之间进行选择
*易于设置
*安装后撤销和添加秘密
* 支持 Centos 7/8 或 Ubuntu 18 或更高版本以及 Debian 10 或更高版本
* 自动配置NTP
* API 支持 [[参考](https://github.com/HirbodBehnam/MTProtoProxyInstaller/wiki/API-For-Python-Script)]
## 官方还是 Python 代理？
在以下情况下使用 python：
1. 你的服务器的 CPU 有一个核心，或者你想在一个核心上运行代理。
2.你的服务器是低端的。
3. 你服务的是一小群人。 （如家庭或小公司）
4.您想限制用户连接。
5. 您的服务器上还运行着另一个应用程序或服务。 （Openvpn、shadowsocks、nginx 或...）

否则，使用官方代理。
#### 表现？
Python 代理表示它可以通过 1 个 CPU 内核（~2.5 GHz）和 1024MB RAM 提供大约 4000 个并发连接。

官方代理可以为每个核心提供大约 10000 到 16000 个连接。
## Python 脚本
### 兼容性说明
**当前的 Python 脚本不再支持 Centos。**
您可以从 [此处](https://raw.githubusercontent.com/HirbodBehnam/MTProtoProxyInstaller/4dfad402915ee612332a171c919dcd90132de643/MTProtoProxyInstall.sh) 获取支持它的最新版本。
#### 安装
在你的服务器上运行
```狂欢
curl -o MTProtoProxyInstall.sh -L https://git.io/fjo34 && bash MTProtoProxyInstall.sh
```
等到安装完成，您应该会得到链接。 （使用 `systemctl status mtprotoproxy -l` 也会显示上述链接）

要更新、卸载、更改端口、撤销机密或...代理，请再次运行此脚本。
#### 管理代理
#### 服务
使用 `systemctl start mtprotoproxy` 启动，使用 `systemctl stop mtprotoproxy` 停止，使用 `systemctl status mtprotoproxy -l` 查看脚本日志。 对于热重载，请参见下文。
##### 配置
要手动配置，请代理编辑/opt/mtprotoproxy 中的config.py 以更改配置； 然后使用 `systemctl restart mtprotoproxy` 或使用热重载重启服务器。
##### 配额限制器
代理的 Python 版本能够通过用户使用的流量来限制用户。 您可以通过在安装后重新运行脚本来更改配额。 但请记住，如果您重新启动代理，所有的使用都将重置。 （他们再次从 0 开始计数。）

因此，如果你想要用户管理，你可以使用这个[程序](https://github.com/HirbodBehnam/PortForwarder)
##### 热重载：
热重载重新加载配置文件而不重新启动服务。 如果您设置了一些配额限制，它会很有用。

在您的终端上复制并执行以下每一行：
```狂欢
pid=$(systemctl show --property MainPID mtprotoproxy)
arrPID=(${pid//=/})
pid=${arrPID[1]}
杀死-USR2“$pid”
```
### API
此脚本为您提供安装后 API 支持以控制代理。 [更多信息](https://github.com/HirbodBehnam/MTProtoProxyInstaller/wiki/API-For-Python-Script)
## 官方脚本
#### 安装
在你的服务器上运行
```狂欢
curl -o MTProtoProxyOfficialInstall.sh -L https://git.io/fjo3u && bash MTProtoProxyOfficialInstall.sh
```
并等待安装完成，您将在安装后看到链接。
####＃ 工人
每个工作人员可以在现代 CPU 上处理超过 10000 个连接。 连接将在工作人员之间分配。 不要产生超过 CPU 线程数的工作线程。
#### 自动安装（无钥匙）
您可以使用参数运行脚本以启用“无密钥安装程序”。

例如：
```狂欢
curl -o MTProtoProxyOfficialInstall.sh -L https://git.io/fjo3u && bash MTProtoProxyOfficialInstall.sh --端口 443 --secret 00000000000000000000000000000000
```
参考：
```
-p | --port : int -> 代理将监听的端口。 不包含此参数以选择随机端口
-s | --secret : string -> 添加一个秘密到秘密列表。 Secret 必须是 32 个字符，并且是十六进制格式； 使用多个此参数来添加更多秘密（请参见下面的示例）
-t | --tag : string -> 设置代理的广告标签。 不要传递此参数以禁用标记。
--workers : int -> 代理产生的工人数量。 默认是你的 CPU 线程数 - 1。
--disable-updater : bool -> 传递此参数以禁用代理更新程序。
--tls : string -> 代理必须模仿的主机。 默认值为 www.cloudflare.com。 要禁用假 tls，请使用：'--tls ""'
--custom-args : string -> 如果你愿意，你可以设置一些直接放入服务文件的其他参数。
--no-bbr : bool -> 如果操作系统是 Ubuntu 18.04 或更高版本，则传递此参数以不启用 BBR。 （对其他操作系统没有任何影响）。
--no-nat: bool -> 传递此参数以禁用 NAT 检查
```

例子：
```狂欢
curl -o MTProtoProxyOfficialInstall.sh -L https://git.io/fjo3u && bash MTProtoProxyOfficialInstall.sh --port 3033  --secret 0123456789abcdef0123456789abcdef --tag 9cd18205e07716f93152af6d44366e13 --tls "www.cloudflare-cn.com"
```
#### 管理代理
####＃＃ 服务
使用 `systemctl start MTProxy` 启动，使用 `systemctl stop MTProxy` 停止，使用 `systemctl status MTProxy -l` 查看脚本日志。
##### 配置
服务文件保存在“/etc/systemd/system/MTProxy.service”中。 您可以手动编辑它。 在 `/opt/MTProxy/objs/bin` 中还有一个由脚本创建的名为 `mtconfig.conf` 的文件。 它用于通过脚本加载代理配置。 *你不能删除这个文件*，但是，你可以编辑它。 此外，如果您启用了自动更新程序，您将拥有另外两个名为“updater.sh”和“updater.log”的文件
## Golang 版本安装程序 (MTG)
这个代理服务器是用 golang 写的。 TBH，我没有使用过它，我不知道它是否好。 另请注意，go 版本旨在易于设置，并且不会为您提供很多配置代理的选项。
#### 安装
```狂欢
curl -o MTGInstal.sh -L https://git.io/mtg_installer && bash MTGInstal.sh
```
#### 服务
名为“mtg”的服务名称已为您创建。 所有配置都在该服务中。
## Erlang 版本安装程序
感谢 [Erlang Proxy](https://github.com/seriyps/mtproto_proxy) 的@seriyps 创建者，您现在可以使用脚本安装 Erlang 代理。

**注意：**此脚本适用于 Ubuntu 18/19、Debian 9/10 和 Centos 7。
```狂欢
curl -L -o mtp_install.sh https://git.io/fj5ru && bash mtp_install.sh
```
您也可以只提供端口/秘密/广告标签/协议作为命令行参数：
```狂欢
curl -L -o mtp_install.sh https://git.io/fj5ru && bash mtp_install.sh -p 443 -s d0d6e111bada5511fcce9584deadbeef -t dcbe8f1493fa4cd9ab300891c0b5b326 -a dd -a tls
```
## 其他信息
### 防火墙
安装程序将尝试在公共区域配置代理。 但是，您可以手动输入这些规则，以防出现任何错误或其他情况。 只需重新运行脚本并选择“生成防火墙规则”，脚本就会生成并应用防火墙规则。
### 随机填充
由于一些 ISP 通过数据包大小检测 MTProxy，如果启用这种模式，则会将随机填充添加到数据包中。
它只为请求它的客户启用。
将 dd 前缀添加到 secret (cafe...babe => ddcafe...babe) 以在客户端启用此模式。

### 假 TLS
假 TLS 是一种使代理流量看起来像 TLS（类似于网站流量）的方法。 为了让您的客户使用它，您必须与他们共享特定链接。 该脚本将在最后打印它。 假 TLS 链接以“ee”开头。
### 配额管理
我在 golang([link](https://github.com/HirbodBehnam/PortForwarder)) 中编写了一个小程序来转发具有配额管理的流量。 我还在 [此处](https://github.com/HirbodBehnam/MTProtoProxyInstaller/wiki/Quota-Management-For-Server) 编写了一个指南，以便使用 MTProto 对其进行配置。 现在它还支持限制每个端口的_connections_ 数量。 [波斯语指南](http://rizy.ir/limitUsers)
### 如何在 Windows 上安装？
我写了一个小指南来在 Windows 上安装它。 请阅读 [wiki](https://github.com/HirbodBehnam/MTProtoProxyInstaller/wiki) 了解更多信息。
#### 服务器
您可以使用任何 VPS 或专用服务器。 如果你想要一台便宜的低端服务器，我个人推荐在[Virmach](https://virmach.com/)购买一台； 他们也接受加密货币！

#### 购买服务器、安装脚本、常见问题解答和 QoS 的波斯语指南
***我自己不使用 MTPROTO 代理。*** 您也可以使用 [shadowsocks with Cloak](https://github.com/HirbodBehnam/Shadowsocks-Cloak-Installer)(**强烈推荐**，我使用 我自己）或[wireguard]（https://github.com/l-n-s/wireguard-install）或[openvpn]（https://github.com/angristan/openvpn-install）。

（如果你来自伊朗，你可能需要用VPN打开这个链接）

http://rizy.ir/4EbW
#### 购买服务器和安装脚本的英文指南
https://www.reddit.com/r/Telegram/comments/95m5vi/how_to_deploy_mtproto_proxy_server_on_centos/
### 代理项目
[Python 代理](https://github.com/alexbers/mtprotoproxy)

[官方C代理](https://github.com/TelegramMessenger/MTProxy)

[Golang 代理又名 MTG](https://github.com/9seconds/mtg)

[二郎代理](https://github.com/seriyps/mtproto_proxy)
### 捐款
您可以通过“1XDgEkpnkJ7hC8Kwv5adfaDC1Z3FrkwsK”的比特币、“0xbb527a28B76235E1C125206B7CcFF944459b4894”的以太坊、ZCash 向我捐款
在`t1ZKYrYZCjxDYvo6mQaLZi3gNe2a6MydUo3`和比特币黄金在`GcNgxfyR3nnAsD3Nhuckvq14sXYuDFkK9P`