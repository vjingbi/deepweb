ChaosVPN被认为是进入MarianaWeb的方式之一

ChaosVPN：UbuntuHowto
ChaosVPN是一个VPN连接黑客和黑客空间-它不提供匿名上网！
为此，请查看tor或其他类似服务。
它还不会帮助您访问诸如.rdos，.lll，.clos之类的域或任何其他应该在“暗网”上可用的奇怪内容。
内容
•	1个 针对UBUNTU用户的快速指南
o	1.1 安装软件
o	1.2 安装必要的帮助程序
	1.2.1 安装tinc
o	1.3 安装我们的ChaosVPN程序
	1.3.1 最简单的方法：使用启动板PPA
	1.3.2 替代：从我们的git仓库编译自己
	1.3.2.1 方法1：创建git快照debian软件包
	1.3.2.2 方法2：创建debian软件包并安装它
	1.3.2.3 方法3：只需编译并安装原始二进制文件
o	1.4 创建配置目录
o	1.5 将新节点添加到中央配置
	1.5.1 设计您将要使用的网络缺口和唯一的IP范围
	1.5.2 主机名
	1.5.3 生成密钥
	1.5.3.1使用tinc 1.1+ 生成密钥
	1.5.3.2 生成具有TINC 1.0.xx键
	1.5.4向 我们发送您的信息
o	1.6 自定义配置文件
o	1.7 启用启动ChaosVPN
o	/ etc / ppp / ip-up中的1.8 脚本可以自动启动，也可以通过cron不时重新启动

UBUNTU用户的快速指南
安装软件
安装必要的帮助程序
需要使用chaosvpn客户端：
＃apt-get install tinc iproute
如果不使用预先创建的debian软件包，则需要编译chaosvpn-client：
＃apt-get install build-essential git bison flex libssl-dev zlib1g-dev debhelper devscripts
（有关可下载的预先创建的软件包，请参见下文）
安装tinc
＃apt-get install tinc
您需要来自Debian squeeze / unstable的软件包，或者来自http://debian.sdinet.de/squeeze/sdinet/tinc/或http://backports.debian.org/的backport。
该版本至少应为tinc版本1.0.13，但可能与1.0.10或更高版本一起使用。
或访问http://tinc-vpn.org/，下载并自行构建-至少./configure，指定参数--sysconfdir = / etc，然后检查脚本中的二进制文件
如果tinc安装出现以下错误：
> ./MAKEDEV：不知道如何使设备“调谐”
然后手动创建设备：
＃mkdir -p / dev / net
＃mknod / dev / net / tun c 10200
＃chown root：根/ dev / net / tun
＃chmod 600 / dev / net / tun
安装我们的ChaosVPN程序
最简单的方法：使用启动板PPA
LTS版本12.04（精确）有amd64和i386二进制程序包，还有一个源程序包
将以下行添加到您的/etc/apt/sources.list中：
对于Ubuntu Precise：
deb http://ppa.launchpad.net/matt-nycresistor/chaosvpn/ubuntu精确主
 deb-src http://ppa.launchpad.net/matt-nycresistor/chaosvpn/ubuntu精确主 

使存储库密钥已知：
 apt-get更新
 须藤添加apt存储库ppa：matt-nycresistor / chaosvpn 
对任何内容的警告回答“ y”。
再次运行apt-get update：
 apt-get更新
最后安装ChaosVPN软件：
 apt-get install chaosvpn
安装完成，在下面的某些页面继续下一步。
PPS：该存储库可用于i386（Intel + AMD x86 32位）和amd64（Intel + AMD x86 64位）-其他体系结构的用户必须自己编译chaosvpn客户端。
替代方法：从我们的git存储库进行编译
始终需要编译：
＃git clone git：//github.com/ryd/chaosvpn.git
＃cd chaosvpn
方法1：创建一个git快照debian包
＃dch -i 
 增加版本并设置ubuntu特定信息。
＃制作deb
 也许会引发关于缺少构建依赖项的错误，请安装并重试。
＃sudo dpkg -i ../chaosvpn_2.0*.deb
 安装生成的软件包文件，将上面的文件名替换为真实名称。
 也可以将生成的.deb软件包复制到同一台计算机的另一台计算机上
 架构并在那里安装-无需完整的编译环境
 在路由器/防火墙上。
 
方法2：创建debian软件包并安装
＃dch -i
 增加版本并设置ubuntu特定信息。
＃debuild -us -uc
 应该给你包在父目录
＃sudo dpkg -i ../chaosvpn_2.0*.deb
 安装生成的软件包文件，将上面的文件名替换为真实名称。
方法3：只需编译并安装原始二进制文件
＃使
＃sudo make install
创建配置目录
＃mkdir -p / etc / tinc / chaos
将新节点添加到中央配置
设计您将要使用的网络昵称和唯一的IP范围
此网络昵称或有时称为节点名是运行vpn软件的网络端点/网关
的名称，不一定是用户的名称，每个用户甚至可能有多个网关。
在以下<nodename>所在的位置使用。
请仅在其中使用字符az，0-9和_。请注意，仅支持小写字母。

其次，请从IP范围中选择一个未使用的IPv4 范围，然后在该Wiki页面中写下您自己，以将您将来的范围标记为使用中。
请从正确的范围中进行选择，对于欧洲，请选择172.31。*。*；对于北美和其他地区，请选择10.100。*。*。
重复：请不要忘记将自己添加到IP范围列表中以将您的范围标记为已使用。
在以下<VPN中的<ipv4子网>所在的位置使用。
也可以使用IPv6网络，但是（目前）我们还没有一个中心范围，
您可以指定从（隧道）提供商那里收到的IPv6范围可以通过VPN
或专用IPv6 ULA 到达（RFC4193中描述的唯一本地地址）网络。
有关ULA和网络范围生成器的更多信息，请参见http://www.sixxs.net/tools/grh/ula/。
在以下<VPN中的<ipv6子网>所在的位置使用。
主机名
网关可以具有指向动态IP或静态主机名/固定IP的DynDNS（或类似名称）主机名。
即使是静态IP地址，也比原始IP地址更好地提供主机名，因此您可以自己更改它，不需要在需要时与我们联系。（也许像chaosvpn.yourdomain.example之类的东西）
在下面的<clienthost>位置使用。
产生金钥
使用Tinc 1.1+生成密钥
＃tinc --net = chaos init <节点名称>
将<nodename>替换为新节点应获得的名称。
** FIXME **需要某种方式，“ tinc init”将公钥放入单独的文件中，而不仅仅是放入生成的hosts文件中，而chaosvpn守护程序会覆盖该文件。
使用以下命令生成公共/私有RSA和ECDSA密钥对
＃tinc --net = chaos生成密钥2048
按Enter 4次，然后将/etc/tinc/chaos/ecdsa_key.priv、ecdsa_key.pub、rsa_key.priv和rsa_key.pub文件备份到外部设备上。
使用tinc 1.0.xx生成密钥
创建混乱配置文件夹
＃mkdir / etc / tinc / chaos /
与生成公共/私有密钥对
＃tincd --net = chaos --generate-keys = 2048
按Enter 2次，然后在外部设备上备份文件/etc/tinc/chaos/rsa_key.priv和rsa_key.pub。
给我们发您的信息
	通过电子邮件发送到chaosvpn-join@hamburg.ccc.de
我们需要以下信息-但请保持友善，并简短描述您/您的空间以及您加入chaosvpn的动机-或至少使我们发笑。:)
（请从电子邮件中删除所有以＃开头的行，它们只是描述）
[<节点名称>]

赞助者=
＃在ChaosVPN上命名一个人/昵称/节点名或组织/黑客空间
＃担保您获得访问权限。

gatewayhost = <客户端主机>
＃这应该是客户端主机的外部主机名或IP地址，而不是VPN地址。
＃如果无法通过互联网访问客户端，请将其保留，并在下面设置为hidden = 1。
＃如果可能，请提供主机名（偶数dyndns）而不是IP地址，以便于更改
＃从您的侧面而不接触中央配置。

网络= <VPN中的IPV4子网>
network6 = <VPN中的IPV6子网>
＃（必填，必须包含）
＃这可以是多个，IPv4或IPv6，带有IPv6的network6是可选的
＃
＃这些子网在我们的VPN中必须是唯一的，
＃只需使用仍然可用的网络块重新编号您的家庭网络（或使用NETMAP之类的东西）。
＃
＃请使用ChaosVPN：IPRanges上分配的网络列表，并在其中添加您自己。

所有者=
＃（必填，必须包含）
＃VPN网关的管理员，带有电子邮件地址-一种与负责人联系的方法
＃人，以防您的网络链接出现问题。

端口= 4712
＃ （可选的）
＃如果未指定tinc可以在tcp + udp端口655上运行
＃最好所有人都为此选择一个随机端口。
＃这个指定的端口或端口655应该接受来自Internet的TCP和UDP通信。

隐藏= 0
＃ （可选的）
＃“我不能接受入站隧道连接，只能连接出去。”
＃（例如，在NAT之后）
无声= 0
＃ （可选的）
＃“我无法连接，但您可以连接到我。”
＃隐藏= 1或静默= 1只能是一个。 

Ed25519PublicKey = <东西>
＃ （可选的）
＃仅限于tinc 1.1pre11 +，/ etc / tinc / chaos / ed25519_key.pub的内容

----- BEGIN RSA公钥-----
....
-----结束RSA公钥-----
＃（必填）
＃rsa-public-key-您的/etc/tinc/chaos/rsa_key.pub的内容

等待回复，请给我们一些时间，您的请求将被手动处理
重试，直到$成功或$拒绝-但不要向我们发送垃圾邮件。
自定义配置文件
FIXME将被扩展
/etc/tinc/chaosvpn.conf
顶部是变量。
更改
$ my_peerid从步骤4到网络昵称
$ my_vpn_ip到您网络范围内的IP地址，例如172.31.x.1
启用启动ChaosVPN
如果您通过我们的Debian软件包安装了ChaosVPN，则默认情况下不会启动它。
要启用此功能，请编辑文件/ etc / default / chaosvpn并将RUN =行更改为RUN =“ yes”
完成所有更改后（重新）启动chaosvpn客户端：
＃/etc/init.d/chaosvpn开始
如果一切正确，那么现在应该正在运行tinc守护程序，并且“ route -n”的输出应该显示许多指向新“ chaos_vpn”网络接口的路由。
/ etc / ppp / ip-up中的脚本以自动启动，或通过cron时不时重启
如果您构建了一个debian软件包并安装了它，则cron和ip-up部件已经安装完毕，如果您使用make install手动安装了它，则必须自己进行安装。

（幸运的是，它的功能非常漂亮！;）

todo：吨;）进行测试，并针对其他Linux发行版甚至可能使用* bsd调整文档

