# W(indows) N(ginx) M(ysql) P(hp) 服务器配置

windows服务器配置流程解析，全文默认路径均为：C:/wnmp/


## 服务器远程登录端口修改

### 修改注册表

1. Win+R 唤起运行窗口，键入 regedit 进入注册表

2. 找到 [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd\Tds\tcp] 下的 PortNumber 键值（默认为16进制，可修改成十进制）；此处修改为需求端口号（例如：2121）

3. 找到 [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Tenninal Server\WinStations\RDP-Tcp] 下的 PortNumber 键值，同上修改


### 设置防火墙

1. Win+R 唤起运行窗口，键入 wf.msc 进入防火墙设置

2. 防火墙设置
![](./static/1.png '1')

3. 入站规则设置<br/>
![](./static/2.png '2')

4. 入站规则设置<br/>
![](./static/3.png '3')

5. 入站规则设置<br/>
![](./static/4.png '4')

6. 入站规则设置<br/>
![](./static/5.png '5')

7. 入站规则设置<br/>
![](./static/6.png '6')


如果在第三方租的服务器，请在服务器的安全策略规则中新增该端口



## 服务器必须软件

* 请检查服务器是否安装 .Net Framework 4.0，如没有请安装

* 请检查服务器是否安装 Visual C++ Redistributable for Visual Studio 2015，如没有请安装；32位和64位都安装，避免报错



## Nginx配置

1. 在Nginx官网下载最新的稳定版（Stable version），本文最新版为：1.14.2；[下载页](http://nginx.org/en/download.html)

2. 将压缩包解压到 C:/wnmp/ 下，命名为 nginx 

3. 开打 nginx/conf/ 下的 nginx.conf 文件，此文件为 nginx 的配置文件

  * 选添这三行，可以让 nginx 显示文件目录<br/>

  ![](./static/n1.png 'n1')<br/>

  ```
  autoindex on;  
  autoindex_exact_size off; 
  autoindex_localtime on;
  ```