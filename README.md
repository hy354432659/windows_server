# W(indows) N(ginx) M(ysql) P(hp) 服务器配置

windows服务器配置流程解析


## 服务器远程登录端口修改

### 修改注册表

  1. Win+R 唤起运行窗口，键入 regedit 进入注册表

  2. 找到 [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd\Tds\tcp] 下的 PortNumber 键值（默认为16进制，可修改成十进制）；此处修改为需求端口号（例如：55678）

  3. 找到 [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Tenninal Server\WinStations\RDP-Tcp] 下的 PortNumber 键值，同上修改