# 前置准备

## 安装LNMP环境
由于个人习惯，选择使用的<a href="https://lnmp.org/">LNMP一键脚本</a>搭建的该环境，执行该脚本是*建议使用screen*，防止在安装过程中SSH断联导致安装失败。
```bash
wget http://soft.vpser.net/lnmp/lnmp1.8.tar.gz -cO lnmp1.8.tar.gz &amp;&amp; tar zxf lnmp1.8.tar.gz &amp;&amp; cd lnmp1.8 &amp;&amp; sudo bash install.sh
```

## 安装 Asp.Net Core SDK 3.1 运行环境

由于博主使用的Ubuntu 18.04 LTS 支持 .Net，因此可以轻易使用包管理器安装.Net，其它 OS 请参考[在 Linux 发行版上安装 .NET | Microsoft Docs](https://docs.microsoft.com/zh-cn/dotnet/core/install/linux)


```bash
wget https://packages.microsoft.com/config/ubuntu/18.04/packages-microsoft-prod.deb -O packages-microsoft-prod.deb
```

```
sudo dpkg -i packages-microsoft-prod.deb && rm packages-microsoft-prod.deb
```

```bash
sudo apt-get update; \
  sudo apt-get install -y apt-transport-https && \
  sudo apt-get update && \
  sudo apt-get install -y dotnet-sdk-3.1
```

![image](https://cdn.jsdelivr.net/gh/Gladtbam/Image_Oneself/E5RenewX/dotnet_test.png)


## 创建 SSL 证书

博主使用 [OHTTPS](https://www.ohttps.com?invitationCode=xlpm40g1mq05n9gz) 获取免费 SSL 证书，**其HTTPS证书均由[Let's Encrypt](https://letsencrypt.org/)颁发**，其证书格式为PEM(常见的文件后缀为.pem、.crt、.cer、.key)，<font color="red">需要将其转换成PFX格式</font>

### PEM转换PFX

```
openssl pkcs12 -export -out cert.pfx -inkey cert.key -in fullchain.cer
```

其中cert.key 为***私钥文件***，fullchain.cer为***证书和中间证书***

## OAuth应用注册

### 注册 Github OAuth

1. [前往此处](https://github.com/settings/profile)
2. 单击Developer Settings
3. OAuth Apps -> New Oauth App
4. 填写Application Name
5. Homepage URL 填写https://e5.gladtbam.top
6. Authorization callback URL 填写https://e5.gladtbam.top/signin-github（xxx改你自己)
7. 单击Generate a new client secret
8. 记下<font color=orange>***client id***</font>和<font color=orange>***secret***</font> 稍后在Config.xml相应位置填入

### Microsoft 应用注册


[单击此处前往](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/ApplicationsListBlade)Azure控制台

1. 选择“New registeration” Name随意
2. 选择Accounts in any organizational directory (Any Azure AD directory - Multitenant) and personal Microsoft accounts (e.g. Skype, Xbox)
3. Redirect URI 输入https://e5.gladtbam/signin-microsoft
4. 进入Certificates & secrets -> New client Secrets -> 名称随意 -> end-date
5. 记录<font color=orange>***值***</font>和<font color=orange>***client id***</font>
6. 稍后填入Config.xml中

# 开始部署

## 使用 lnmp 新建一个 vhost  
 - 如下所示

```bash
gladtbam@VM-8-5-ubuntu:~$ sudo lnmp vhost add
[sudo] password for gladtbam:
+-------------------------------------------+
|    Manager for LNMP, Written by Licess    |
+-------------------------------------------+
|              https://lnmp.org             |
+-------------------------------------------+
Please enter domain(example: www.lnmp.org): e5.gladtbam.top    //输入域名
 Your domain: e5.gladtbam.top
Enter more domain name(example: lnmp.org *.lnmp.org):
Please enter the directory for the domain: e5.gladtbam.top
Default directory: /home/wwwroot/e5.gladtbam.top:
Virtual Host Directory: /home/wwwroot/e5.gladtbam.top
Allow access log? (y/n) y
Enter access log filename(Default:e5.gladtbam.top.log):
You access log filename: e5.gladtbam.top.log
Please enter Administrator Email Address: xxxxxxxxxxxx@qq.com
Server Administrator Email:xxxxxxxxxxxx@qq.com
Create database and MySQL user with same name (y/n) y
Enter current root password of Database (Password will not shown):
OK, MySQL root password correct.
Enter database name: name
Your will create a database and MySQL user with same name: custom_mysql_name
Please enter password for mysql user e5renewx: password
Your password: password
Add SSL Certificate (y/n) y
1: Use your own SSL Certificate and Key
2: Use Let's Encrypt to create SSL Certificate and Key
3: Use BuyPass to create SSL Certificate and Key
4: Use ZeroSSL to create SSL Certificate and Key
Enter 1, 2, 3 or 4: 1   //选择1 输入在 OHTTPS 申请的 SSL 证书(手动传到服务器)
Please enter full path to SSL Certificate file: /usr/local/nginx/conf/ssl/gladtbam.top/gladtbam.top.cer    //证书和中间证书 fullchain.cer
Please enter full path to SSL Certificate Key file: /usr/local/nginx/conf/ssl/gladtbam.top/gladtbam.top.key    //私钥 cert.key

Press any key to start create virtul host...

Create Virtul Host directory......
set permissions of Virtual Host directory......
Test Nginx configure file......
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful

Reload Nginx......
Test Apache configure file...
test apache configure... Syntax OK
 done
Restart Apache...
graceful apache...  done
Add database Sucessfully.
Test Nginx configure file......
nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
Reload Nginx......
================================================
Virtualhost infomation:
Your domain: e5.gladtbam.top
Home Directory: /home/wwwroot/e5.gladtbam.top
Enable log: yes
Database username: name
Database userpassword: password
Database Name: name
Create ftp account: no
Enable SSL: yes
  =>Certificate file
================================================
```

## 部署 MIcrosoft365 E5 Renew X 程序  

### 1. 下载
- [下载链接](https://sundayrx.lanzoui.com/aW09Lsss75g)
- [发布地址](https://blog.csdn.net/qq_33212020/article/details/119747634)

### 2. 使用 scp 上传到 VPS  
![image](https://cdn.jsdelivr.net/gh/Gladtbam/Image_Oneself/E5RenewX/scp_upload.png)

### 3. 解压上传的程序到上面创建的目录下  
比如我的目录在`/home/wwwroot/e5.gladtbam.top`  
![image](https://cdn.jsdelivr.net/gh/Gladtbam/Image_Oneself/E5RenewX/unzip.png)

### 4. 配置Config.xml  
```
chown www:www -R /home/wwwroot/e5.gladtbam.top/
cd /home/wwwroot/e5.gladtbam.top/Deploy/
vim Config.xml
```

**将pfx证书文件放在当前目录**

### 5. 配置 Nginx
```bash
cd /usr/local/nginx/conf/vhost/
vim e5.gladtbam.top.conf
```
**配置参考 [Skyler的部署帮助文档](https://docs-1.leeskyler.top)**
- [点击此处下载博主配置文件](https://cdn.jsdelivr.net/gh/Gladtbam/Image_Oneself/E5RenewX/nginx.conf)

重启服务
```bash
lnmp restart
```

### 6. 启动程序
```
dotnet Microsoft365_E5_Renew_X.dll
```
![image](https://cdn.jsdelivr.net/gh/Gladtbam/Image_Oneself/E5RenewX/renewx_done.png)

#### 可能出现的报错
```
System.InvalidOperationException: Unable to configure HTTPS endpoint. No server certificate was specified, and the default developer certificate could not be found or is out of date.
To generate a developer certificate run 'dotnet dev-certs https'. To trust the certificate (Windows and macOS only) run 'dotnet dev-certs https --trust'.
For more information on configuring HTTPS see https://go.microsoft.com/fwlink/?linkid=848054.
```

原因是没有pfx证书
##### 解决办法
1. 没有pfx证书，按提示执行`dotnet dev-certs https --trust` ,生成自签名证书
2. 有pfx证书，但没有放在指定目录或Config.xml文件没有填写密码,或者通过pem证书转换pfx时没有添加密码

### 7. 制作守护程序

```
/etc/systemd/system
vim e5renewx.service
```

复制以下内容到e5renewx.service(修改对应的内容)
```bash
[Unit]
Description="Microsoft E5 Renew API Web dotnet Service"
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
GuessMainPID=true
Environment=DOTNET_CLI_HOME=/tmp
WorkingDirectory=/home/wwwroot/e5.gladtbam.top
StandardOutput=journal
StandardError=journal
ExecStart=/usr/bin/dotnet /home/wwwroot/e5.gladtbam.top/Microsoft365_E5_Renew_X.dll
Restart=always
RestartSec=1

[Install]
WantedBy=multi-user.target
```

```
#重新加载配置文件
systemctl daemon-reload
#开机自启动
systemctl enable e5renewx.service
#启动服务
systemctl start e5renewx.service
```

# 引用文档
- [MS365 E5 Renew 系列帮助文档](https://docs-1.leeskyler.top/) By leeskyler
- [E5 调用API续订服务：Microsoft 365 E5 Renew X](https://blog.csdn.net/qq_33212020/article/details/119747634) By SundayRX
