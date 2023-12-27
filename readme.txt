Sockscap64 - Makes Programs Support Socks Proxy

64 bit SocksCap, fully support Win XP/ Vista /Win7/ Win8 /Win 8.1/Win10 32 bit & 64 bit system.

SocksCap64 is a freeware developed by Taro that is an easy and a beautiful way to let the programs you want to work through a specific SOCKS proxy server, even if your applications don't have such an option. It allows you to use different proxies for different programs and make a launch list for the applications you wish to have a peculiar connection. SocksCap64 does not require modifications to the Winsock applications or the Winsock stacks.

With SocksCap64 you can work with any Internet client through a network that is separated from the Internet by a firewall (only one open port is required).


Here are some of its benefits:

Makes programs to work through a specific SOCKS proxy server;
Hides your activity and your IP address;
Manages proxies for each application;
Easy, simple, but very useful application;
Support Win XP/Win Vista/Win7/Win8/Win8.1/Win 10 32bit & 64bit system.
Support Socks4/4a/5/http/shadowsocks proxy protocols.
Support TCP/UDP network protocols.
Unlimited proxies.
Disable Temporarily.
100% free to download and 100% free to use.

SocksCap64 gets access through SOCKS4 and SOCKS5 proxies for almost every application. You don’t have to maintain any options in the program you want to work via proxy – SocksCap does everything itself.

keywords: bypass firewall, sockscap64, SocksCap 64 bit, SocksCap x64, Socks5 Proxy, Socks Tunnel

Taro labs is a laboratory founded by taro to develop free SOCKS tunnel software.

official website: http://www.sockscap64.com

如何將SocksCap64整合到你的軟件中去?(通過Socket控制SocksCap64軟件的一些行為?)

此舉主要為某些希望將SocksCap64(以下簡稱SC64)整合到自己軟件中去的開發者而設置.

SC64提供了一個本地的NS解析服務, 同時可以處理用戶發出一些控制行為, 此服務通常監聽在29810端口, 但某些情況下也會更改(例如此端口已提前被占用). 穩妥的做法是找到SC64的安裝目錄下的config\ns.ini文件, 裏邊記錄了當前SC64的NS服務正在工作的端口, 如下:
[basic]
ip=127.0.0.1
port=29810
searchport=1

port即是當前工作端口. 下邊將正確的開發流程加下說明.

1, 打開注冊表HKEY_CURRENT_USER\Software\Sockscap64
    Install_Dir 項指明了SC64當前的安裝目錄.

2, 讀取Install_Dir\config\ns.ini文件. 獲得SC64當前NS工作在哪個端口.

3, 創建Socket向第2步中取得到的端口發送控制指令. 相關控制指令在如下說明.
    與ns通信的數據包格式為(ns的回複信息也是這樣的格式): cmd (1 bytes) + length of packet body(4 bytes) + packet body
    length of packet body指packet body長度.
    packet body是采用“\r\n”区隔的多行文本，采用Utf8編碼, 以0x00结束( 发送指令时务必将0x00结束符发送过来)。 
    ns的指令回複中的packet body也是"\r\n"分隔的多行文本, 僅2行, 第一行是錯誤碼, 0表示成功, 1表示一般性錯誤,其它值為具體的錯誤編碼.

    a) 显示/隐藏SC64主窗口
       cmd: 9
       line 1: on: 顯示主窗體. off: 隱藏主窗體.

    b) 设置当前代理
       cmd: 10
       Line 1	代理服务器地址，如 1.2.3.4
       Line 2	代理服务器端口，如1080
       Line 3	账号，此行如果为空则表示不需要账号
       Line 4	密码，此行如果为空则表示不需要密码
       Line 5	3是HTTP,4表示是socks4/4a,5表示是socks5，6是Shadowsocks
       Line 6	加密方式,只用在ss代理中, 可选的值是: aes-256-cfb, aes-192-cfb,aes-128-cfb,rc4-md5,rc4,salsa20,chacha20

     c) 启用/禁用代理功能 ( 即: SC64中的臨時禁用SC64的功能)
       cmd: 11
       line 1: on：启用代理功能；off：禁用代理功能

     d) 启动应用程序      
       cmd: 12
       Line 1	程序命令行
       Line 2	工作目录
       Line 3	启动参数

     e)	活动探测( SC64當前是否仍在工作中, 是否卡死無響應)
       cmd: 13
       無packet body, length of packet body給0就行.

     f)	设置代理规则
	cmd:14
       第一行	代理指令
       set:  设置代理规则
       clear: 清除所有代理规则　（　此指令只需一行　）

       第二行	目标Hosts规则. 规则可以是:
       一个IP地址, 如: 127.0.0.1
       一个IP段,如: 192.168.1.1-192.168.1.100
       如果为*号则表示匹配所有.
       可以是多个IP或者多个IP段, 以;(分号)隔开. 例如:
       192.168.1.1;10.0.0.1;139.12.10.1-139.12.10.100

       第三行	目标PORTS规则, 规则可以是:
       一个端口,如: 1080
       一个端口段, 如: 8000-8010
       如果为*号则表示匹配所有.

       第四行	规则行为
       proxy:  通过代理请求网络
       direct: 直连
       block: 阻止访问网络
	   
	   注意:  ip 与 port 是 AND 的关系. 例如:  同时指定了 IP为: 192.168.1.1 端口为: 80 则表示匹配到192.168.1.1:80的访问才执行规则.

       注意: 必须先设置好规则, 再启动目标程序. 目标程序启动后中途改变规则不会实现生效. 

       因此, 如果需要设置为仅允许某些IP通过代理, 其它IP均不通过代理请求的话,可以发送指令如下 ( 例子中仅允许133.155.1.10-133.155.1.15 通过代理请求网络 , 其它所有请求均直连 ):
       clear
       set\r\n*\r\n*\r\ndirect
       set\r\n133.155.1.10-133.155.1.15\r\n*\r\nproxy
	 g) 退出程序
	   無packet body. 也不會有回複. SC64收到此指令直接退出.

命令行参数说明, SocksCap64 2.6版本开始支持命令行方式启动参数, 相关说明如下:
-i 指定代理IP, 如: -i192.168.1.1
-p 指定代理端口, 如: -p1080
-t 指定代理类型, 如: -t5
type: ( 不支持通过命令行使用shadowsocks代理 )
3: http
4: socks 4/4a
5: socks 5
( 注意: 你不能在命令行中同时为不同程序指定不同代理, 并且同时启动它们, 例如你为Google Chrome设置了代理192.168.1.2并启动了它, 然后你为IE通过命令行中设置了代理: 192.168.1.3并运行它那么IE的代理将会复盖之前Google Chrome的代理)
-r 指定要运行的程序文件的完整路径,如果含有空格必须将文件以引用包围,
如: -r”C:\Program Files (x86)\Google\Chrome\Application\chrome.exe”
-d 指定要运行的程序文件的工作目录, 如未指定则直接取需要运行的程序文件所在的目录, 如果含有空格必须将文件以引用包围,
如: -d”C:\Program Files (x86)\Google\Chrome\Application\”
-a 指定要传给运行的程序的参数, 未指定则表示无.
如: -att
-n 指定程序域名解析的方式,如未指定默认为DNS_REMOTE(2)
如: -n2
local: 0
local then remote: 1
remote: 2
-w 指定窗口显示方式 hide/show. -whide 隐藏窗口启动, -wshow 显示窗口启动. 如果参数中指定了一个程序文件(-r参数), 但又没有指定窗口显示方式的话,默认是隐藏窗口的(-whide)
如: -whide 启动sc64时隐藏主窗口.
-e -esilent 是否以静默方式启动, 安静方式启动后, 不会弹出一些提示, 例如: 提示导入浏览器等. 例如: -esilent 以安静方式启动.
--quit 退出之前启动的进程. 如果启动时加了这个参数, 会查找之前启动的进程向它发送退出消息, 之后自己退出, 如果是首次启动的话, 找不到之前的进程,那自己直接退出.
--hideicon 隐藏托盘图标

Changelog:
========================================================================
2016-11-01 3.6
1, 修正通过命令行方式启动时会弹出之前启动进程SC主窗体的BUG.

2016-10-16 3.5
1, 重要安全更新,请务必更新到此版本!!!!!!
2, 加入Attach to process功能. ( 某些程序无法代理子进程,可以使用此功能来代理子进程 )

2016-09-28 3.3
1, ss新增支持Chacha20-ietf加密方式
2, 支持添加重复的EXE程序文件
2, 其它一些小BUG

2016-04-03 3.2
1, 应网友要求: 添加命令行参数--hideicon 隐藏托盘图标
2, 解决通过JSON格式添加SS节点导至程序出错的问题.

2016-03-18 3.1
1, 加入代理备注功能.
2, 支持通过未经BASE64的SS加接加入节点.
3, 优化UDP传输.
4, 优化代理测试,代理测试更快速.
5, 改进SS出错重连机制.
6, 改进SS代理 JSON格式, 兼容其它客户端版本.
7, 解决在最新的SS 服务器端LIBEV版本连接异常的问题.
8, 解决高DPI下界面图标错位的问题.
9, 解决SS节点密码中含有@字符无法通过SS,JSON,二维码加入的问题.
10, 修改可能存在无法提升新版本的问题.

2016.01-18 3.0
1, 修复在Http代理协议及Shadowsocks代理协议中的几个BUG.

2016.01-12 2.9
1, 修复一个可能会导至程序在启动崩溃的严重BUG.

2016-01-08 2.8
1, 支持Shadowsocks代理, HTTP代理.
2  增加-esilent, -quit两个参数 用于安静启动 以及退出以前启动的进程.
3, 当设置了系统代理时提醒用户.
4, 修正BUG: 修正多运行多个SocksCap64实例的时的检测功能的BUG.
5, 修正BUG: 显示界面的情况下,按win + d 显示桌面, 再点系统伴图标, 无法显示出主界面.
6, 修正BUG: PUTTY及MSTSC无法支持10.0.0.X段IP
7  修正BUG: 代理管理器中测试所有代理时'stop testing' 按扭也变得不可用了.
8  修正BUG: 启动微信PC客户端时出错.
9 修正BUG: 如果在主界面测试代理,接着把主界面X掉了,那个测试代理的窗口还在.
10 修正BUG: 代理密码为空的话, 验证失败.
11 修正BUG: 修正SOCKS4协议中的一点小BUG
12 修正BUG: 在XP下,NS有时会导至SC64出错
13 修正BUG: 些情况下UDP无法发送数据的BUG.

2015-06-27 2.6
========================================================================
1, 新功能: 可以显示当前通过代理的连接信息.
2, 新功能: 可以显示当前位于隧道中的程序.
3, 新功能: 为程序创建SC64快捷方式, 在桌面直接启动代理程序.
4, 新功能: 加入网络规则, 可以指定某些网站或者IP是否通过代理/直连/阻止.
5, 新功能: 创建SC64快捷方式. 之后可以通过SC64快捷方式直接启动程序进行代理而无需先启动SC64再启动程序.
6, 新功能: 安装向导多国语言支持.
7, 新功能: 界面操作方式调整. 主界面中可以直接测试代理及临时禁止SC64
8, 新特性: 修改DNS的解析方式, 使得整个程序运行更顺畅.
9, 新特性: 代理管理器中的代理密码显示为*(星号).
10, 修正BUG: 解决部份游戏和程序通过SocksCap64代理之后会产生界面卡顿问题.
12, 修正BUG: 帐号密码中含有特殊字符, 会使得客户端无法登录.
13, 修正BUG: 某些.NET程序无法被代理的问题.
14, 修正BUG: 退出SOCKSCAP64之后被代理的程序仍然可以连接网络.
15, 新功能: 代理管理器中可以删除当前活动代理.
16, 修正BUG: 代理管理器设置新的活动代理并立即删除它的话会出错.

2015-05-03 2.1
========================================================================
1, 修正BUG: 代理管理器中切换新的活动代理后无法再切换到之前的活动代理上.
2, 修正BUG: 代理管理器中IP地址输入一个域名的话会导至SOCKSCAP64出错.
3, 新功能:  代理地址可以是一个域名.
4, UDP协议的相关BUG
5, 去掉主界面底部的广告.

2015-04-15 2.0
========================================================================
1, 新功能: 加入支持UDP代理协议.
2, 新功能: 加入程序选择器功能. ( 添加程序时从程序选择器中选择本机已安装的程序进行代理 )
3, 新功能: 某些需要高权限才能运行的程序在SocksCap64中运行时给出提示.
4, 新功能: 代理管理器中加入'通过UDP协议测试代理'的功能.
5, 修正BUG: 偶然的情况下能启动进程,无法出现程序的界面.
6, 修正BUG: 在SocksCap64中选中某程序,点'运行'按扭不能运行.
7, 修正BUG: 被代理的程序正在运行中突然退出SocksCap64偶尔会导至SocksCap64出错. 
8, 修正BUG: 某些系统下重装SocksCap64时提示写入文件失败, 其实进程还在后台未退出.
9, 其它细节修改.

2015-03-23 1.2.1
========================================================================
1, 修正BUG: SocksCap64_RunAsAdmin无法运行.

2015-03-22 1.2
========================================================================
1, 加入新功能: 运行过程中可以随时切换当前使用的代理并立即生效.
2, 加入功能: 可以手动切换程序界面语言
3, 代理管理器功能增强, 测试代理时可以查看测试日志.
4, 启用了新的本地文件加密方式.
5, 系统栏系统中加入临时禁止SocksCap64功能.
6, 修改网站数据库版本发布表格结构.
7, 修改进程创建的方式. 某些特殊目录及文件结构的程序可能导至创建进程时出现误差.
8, 修改DLL的注入方式. 新的注入方式更稳定, 更能适应各程序的创建方式.
9, 修正在Windows 8系统下可能导至某些程序无法正常工作的问题.
10, 针对Wow魔兽世界做一系列优化修改.
11, 解决Wow魔兽世界登录战网提示: 您正在尝试登录一下无效的服务器.( You're trying to connect to an invalid server. )
12, 修正bug: 启动程序后按Win + D快捷键显示桌面后, 双击系统栏图标无法恢复显示程序.
13, 修改其它一些bug.



2015-02-13 1.0
========================================================================
1, 包含了”SocksCap”的功能, 并且完美支持XP/Vista/Win7/Win8 (32bit & 64bit).
2, 快速配置, 轻松上手. 可以自动导入系统中已经安装的Web Browser. 安装好Sockscap64之后几乎不再需要额外的工作就可以开始使用了.
3, 注册网站帐号并在Sockscap64登录之后, 能与网站集成, 网站购买或者申请试用的的Socks5代理可以直接更新至Sockscap64客户端使用.
4, 超好使用的代理管理器. 可同时加入无限数量的代理进行管理并灵活切换使用.
5, 您可以在使用中临时禁止Sockscap64的功能从而使用真实身份访问网络. ( 例如: 您当前能过Sockscap64访问网站显示你的IP来自某国代理, 临时禁止Sockscap64之后再刷新网站看到的就是你的真实IP身份).
6, 支持本地解析域名, 先本地再远程SOCKS代理解析, 直接通过远程SOCKS代理解析三种域名解析方式.
7, 完全按照RFC文档书写的Socks 4/5协议支持, 访问速度极快. 当前版本仅支持TCP连接, 后期版本中会加入UDP支持. 当前版本仅支持Socks 4/5协议.