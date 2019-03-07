# W(indows) N(ginx) M(ysql) P(hp) 服务器配置

windows服务器配置流程解析，全文默认路径均为：C:/wnmp/


## 服务器远程登录端口修改

### 修改注册表

1. Win+R 唤起运行窗口，键入 `regedit` 进入注册表

2. 找到 `[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal Server\Wds\rdpwd\Tds\tcp]` 下的 `PortNumber` 键值（默认为16进制，可修改成十进制）；此处修改为需求端口号（例如：2121）

3. 找到 `[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Tenninal Server\WinStations\RDP-Tcp]` 下的 `PortNumber` 键值，同上修改


### 设置防火墙

1. Win+R 唤起运行窗口，键入 `wf.msc` 进入防火墙设置

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

1. 在Nginx官网下载最新的稳定版（Stable version），本文最新版为：1.14.2；[下载地址](http://nginx.org/en/download.html)

2. 将压缩包解压到 C:/wnmp/ 下，命名为 nginx 

3. 开打 nginx/conf/ 下的 `nginx.conf` 文件，此文件为 nginx 的配置文件

    * 选添这三行，可以让 nginx 显示文件目录<br/>![](./static/n1.png 'n1')

    ```
      autoindex on;  
      autoindex_exact_size off; 
      autoindex_localtime on;
    ```

    * 此处将 `charset` 前的 # 去掉，并改成 `utf-8` 编码<br/>![](./static/n2.png 'n2')

    * 此处将 `root` 指向网站目录，例如：C:/wnmp/www ；如需开启php，可在 index 后添加 `index.php`<br/>![](./static/n3.png 'n3')

    * 添加这几行代码，是因为 nginx 运行多次后，会出现一个图片找不到的错误，可用此代码屏蔽<br/>![](./static/n4.png 'n4')

    ```
      location = /favicon.ico {
          log_not_found off;
          access_log off;
      }
    ```

    * 需要支持php的话，找到如图代码，将 # 删除，`root` 改为网站路径， `fastcgi_param` 修改如下图所示<br/>![](./static/n5.png 'n5')

    ```
      location ~ \.php$ {
          root           c:/wnmp/www;
          fastcgi_pass   127.0.0.1:9000;
          fastcgi_index  index.php;
          fastcgi_param  SCRIPT_FILENAME  $document_root$fastcgi_script_name;
          include        fastcgi_params;
      }
    ```

至此，Nginx 基础配置完毕



## Php配置

1. 在Php官网下载最新版php压缩包（VC15），本文最新版为：7.3.2 VC15 x64；[下载地址](https://windows.php.net/download/)

2. 将压缩包解压到 C:/wnmp/ 下，命名为 php 

3. 在php目录下，将 `php.ini-development` 文件复制一份并重命名为 `php.ini` ，打开此文件

    * 搜索 `extension_dir` ，找到：`extension_dir = "ext"`，先去前面的分号再改为 extension_dir = "你的php目录/ext"，例如：`extension_dir = "C:/wnmp/php/ext"`

    * 搜索 `enable_dl` ，找到：`enable_dl = Off` 改为 `enable_dl = On`

    * 搜索 `cgi.force_redirect` ，去掉 `cgi.force_redirect = 1` 前面的分号再改为 `cgi.force_redirect = 0`

    * 搜索 `date.timezone` ，找到：`;date.timezone =` ，去掉前面的分号再改为 `date.timezone = Asia/Shanghai`

    * 搜索 `fastcgi.impersonate` ，找到：`;fastcgi.impersonate = 1` ，去掉前面的分号

    * 搜索 `cgi.rfc2616_headers` ，找到：`;cgi.rfc2616_headers = 0` ，去掉前面的分号再改为 `cgi.rfc2616_headers = 1`

    * 之后找到：`;extension=pdo_mysql` 和 `;extension=mysqli` ，去掉前面的分号，用于支持MYSQL数据库，其余的 extension 根据需求去掉分号打开<br/>![](./static/p1.png 'p1')

至此，Php 的基本配置完毕，在 C:/wnmp/www/ 目录下新建 `index.php` 文件，内容为：
```php
<?php
    phpinfo();
?>
```



## Nginx 配置为系统服务

1. 首先感谢 [kohsuke](https://github.com/kohsuke) 大佬分享注册服务工具 winsw，请下载 `WinSW.NET4.exe`
 和 `sample-minimal.xml` 两个文件；[下载地址](https://github.com/kohsuke/winsw/releases)

2. 将 `WinSW.NET4.exe` 和 `sample-minimal.xml` 文件复制到 C:/wnmp/nginx/ 文件夹内，并重命名为 `nginx-server.exe` 和 `nginx-server.xml` （此为例子，只需保证两个文件名字相同即可）

3. 修改 `nginx-server.xml` 文件，内容如下：
```xml
<configuration>
  
    <!-- ID of the service. It should be unique accross the Windows system-->
    <id>Nginx</id>
    <!-- Display name of the service -->
    <name>Nginx Service</name>
    <!-- Service description -->
    <description>This service is a service cratead from a minimal configuration</description>

    <!-- Path to the executable, which should be started -->
    <executable>%BASE%\nginx.exe</executable>
    <logpath>%BASE%\logs</logpath>
    <startarguments>-p %BASE%\nginx.exe</startarguments>
    <stopexecutable>%BASE%\nginx.exe</stopexecutable>
    <stoparguments>-s stop</stoparguments>-->
 
</configuration>
```

4. 使用管理员身份启动cmd，进入到当前目录下（C:/wnmp/nginx/），输入 `nginx-server.exe install` 并回车，服务就可正确安装，Win+R 输入 `services.msc` 回车，在服务中即可看到 nginx 服务，右键点击启动

至此，Nginx 系统服务安装完毕



## Php 配置为系统服务

默认情况下，当 `php-cgi` 处理php达到500次，就自动关闭，既不能解析php，所以需要一个守护进程来管理 `php-cgi`

1. 首先感谢 [Xiaoxia](https://github.com/78) 大佬分享 php-cgi 守护进程工具 `xxfpm.exe`， [下载地址](https://github.com/78/xxfpm)

2. 在压缩包里找到 `pthreadGC2.dll` ，将这个文件复制到 C:/Windows/SysWOW64 目录（如果系统是32位的，将dll文件复制到 C:\Windows\System32 下）

3. 将 `xxfpm.exe` 和之前下载的 `WinSW.NET4.exe`、`sample-minimal.xml` 复制到 C：/wnmp/nginx/ 目录下，并将后两个文件重命名为 `php-server.exe` 和 `php-server.xml` （此为例子，只需保证两个文件名字相同即可）

4. 修改 `php-server.xml` 文件，内容如下：（代码中的php路径请按需修改）
```xml
<configuration>
  
    <id>PHP-CGI</id>
    <name>PHP-CGI</name>
    <description>PHP-CGI</description>
    <executable>%BASE%\xxfpm.exe</executable>
    <startargument>C:/wnmp/php/php-cgi.exe -c C:/wnmp/php/php.ini</startargument>

    <!-- 启动进程数量，可修改，建议大于 1 -->
    <startargument>-n</startargument>
    <startargument>5</startargument>

    <!-- 监听IP，默认一般都用 127.0.0.1 -->
    <startargument>-i</startargument>
    <startargument>127.0.0.1</startargument>

    <!-- 监听端口，默认一般都用 9000 -->
    <startargument>-p</startargument>
    <startargument>9000</startargument>

    <!-- 停止参数 -->
    <stopexecutable>taskkill</stopexecutable>
    <stopargument>/F</stopargument>
    <stopargument>/IM</stopargument>
    <stopargument>xxfpm.exe</stopargument>

    
    <logpath>%BASE%\logs</logpath>

</configuration>
```

5. 使用管理员身份启动cmd，进入到当前目录下（C:/wnmp/nginx/），输入 `php-server.exe install` 并回车，服务就可正确安装，Win+R 输入 `services.msc` 回车，在服务中即可看到 php 服务，右键点击启动

6. 如报错缺少 `pthreadgc2.dll` ，请执行以下方案

    * Win+R 输入 `regsvr32 pthreadgc2.dll` 回车，如还报错，请用下面的办法

    * 新建一个文件 `registe.bat` 和 `pthreadgc2.dll` 放置在同一目录，文件内容如下：
    ```bat
    @echo 开始注册
    copy pthreadgc2.dll %windir%\SysWOW64\
    regsvr32 %windir%\SysWOW64\pthreadgc2.dll /s
    @echo pthreadgc2.dll注册成功
    @pause
    ```

    * 运行 `registe.bat`，完成 `pthreadgc2.dll` 注册（如果32位的系统，请将system32替换为system32）。完成后可将此文件删除

至此，Php服务安装完毕，可在任务管理器中看到开启数量的 `CGI/FastCGI` 进程