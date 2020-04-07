# **Aliyun-website**
## 使用阿里云服务器搭建个人网站
# 准备工作：
## 一个阿里云ECS，一个网站域名
登录[阿里云主页](https://promotion.aliyun.com/ntms/act/campus2018.html)（此处为学生优惠界面），根据自身条件选择适合自己的服务器。在系统的选择上，
由于其他需求，我选择的是Ubuntu18，无其他需求的可以选择centos。
>centos与Ubuntu的区别：
>在Ubuntu18之前ubuntu的生命周期只有5年，而centos有7-10年，这就导致一个问题是，5年之后的Ubuntu会不安全，这就是建站大多数centos的原因。
>但现在的Ubuntu18也有了10年的生命周期，同时期centos也是10年，对于不需要长期支持的网站，两者都够用，此外Ubuntu对硬件兼容差，但包多。

登录[域名注册主页](https://wanwang.aliyun.com/?spm=5176.12825654.eofdhaal5.80.db6a2c4aTkITmJ&aly_as=sK6fZZIeV)选择一个属于你的域名。
## 打开你的ECS
购买成功后进入你的服务器管理控制台，先重置自己的密码，然后选择workbench远程控制，即可进入系统。但这种方法在日后比较麻烦，最好是使用其他设备在网页上使用。如果是自己的电脑，
建议安装[Xshell](https://www.netsarang.com/zh/xshell/)和[Xftp](https://www.netsarang.com/zh/xftp/)。Xshell提供终端模拟，Xftp提供文件传输。
非企业版用户是免费使用，提供自己的邮箱稍后会有下载链接。

安装Xshell后，点击文件，新建，名称自己拟定，主机填写你的ECS公网IP，其他保持默认。点击即可进入，登录名为root，密码为刚才重置的密码

启动好Xshell后，Xftp也相应链接，就可以在你的电脑和ECS上互传文件了。

## 可视化Ubuntu
安装Ubuntu可视化桌面
>sudo apt-get install ubuntu-desktop

安装后重启

使用VNC远程连接即可进入可视化界面，但该连接方法延迟非常高，建议Ubuntu安装teamviewer，使用该软件远程控制，延迟非常小。
进入系统执行
>sudo apt-get update

>sudo apt-get upgrade

因为在阿里云内网下载，速度飞快

# 安装JDK
## 下载jdk
在[oracle](https://www.oracle.com/java/technologies/javase-jdk8-downloads.html)上下载JDK，这里选择jdk-8u241-linux-x64.tar.gz

Oracle改变了老旧的网页，焕然一新的界面更加方便查找，但下载增加了注册Oracle账号的步骤。这里建议使用windows 下载好通过xftp传到ECS的Downloads目录下。
在ECS下新建文件夹 
>/usr/java/jdk
进入Downloads目录下，解压JDK到jdk文件夹下
>tar -xvf jdk-8u241-linux-x64.tar.gz -C /usr/java/jdk/
## 配置环境变量
使用vim打开/etc/profile 文件，在文件末尾加入
>export JAVA_HOME=/usr/java/jdk/jdk1.8.0_241

>export JRE_HOME=/usr/java/jdk/jdk1.8.0_241/jre

>export CLASSPATH=.:$JAVA_HOME/lib$:JRE_HOME/lib:$CLASSPATH

>export PATH=$JAVA_HOME/bin:$JRE_HOME/bin/$JAVA_HOME:$PATH

保存退出，执行

>source /etc/profile

查看安装是否成功

>java -verson

# 安装tomcat
## 下载安装包
进入[Tomcat官网](http://tomcat.apache.org/download-80.cgi?spm=a2c4e.10696291.0.0.246a19a4juIgWT&file=download-80.cgi)，下载core下的tar.gz
将文件传到ecs下的Downloads
在ECS下新建文件夹 
>/usr/java/tomcat

进入Downloads目录下，将tomcat解压到tomcat文件夹下

>tar -xvf apache-tomcat-8.5.53.tar.gz -C /usr/java/tomcat/

## 添加JDK路径
使用vim打开/usr/java/tomcat/apache-tomcat-8.5.53/bin/setclasspath.sh 文件，在文件末尾加入
>export JAVA_HOME=/usr/java/jdk/jdk1.8.0_241

>export JRE_HOME=/usr/java/jdk/jdk1.8.0_241/jre

## 打开tomcat
在/usr/java/tomcat/apache-tomcat-8.5.53/bin/目录下，执行

 >./startup.sh
 
 如果出现Tomcat started，说明启动成功。使用teamviewer打开ubuntu的浏览器，输入localhost:8080。验证是否成功
 
 ## 但现在只能在ubuntu下打开，很显然这不是最终目的
 # 其他设备的访问
 ## 访问公网IP
 在你的PC浏览器下输入你的服务器公网IP：8080 。发现无法连接，这是因为服务器的安全组并没有开放8080的端口，只需要开放即可。进入你的云服务器管理控制台，进入
 本实例安全组，点击配置规则，添加安全组规则，端口范围填8080/8080，授权对象0.0.0.0/0即可。
 
 在浏览器输入服务器公网IP：8080，即可进入tomcat的网页。
 
 ## 连接域名
 进入阿里云你的主页，进入域名控制台，它会出现你刚才购买的域名，点击域名后面的解析，第一次可以点击新手引导，选择网站解析，记录值填写你的服务器公网IP
 。之后就可以列表多了几条记录，这时候在打开你的域名：8080，就可以连接tomcat的网页。
 
 ## 修改端口
 进入网页后会发现如果不加：8080是无法进入的，这时候可以修改默认的端口号为80，就可以不用输入:8080，链接也不会出现：8080。
 
 服务器端进入/usr/java/tomcat/apache-tomcat-8.5.53/conf/目录下 vim打开server.xml文件，找到
>       <Connector port="8080" protocol="HTTP/1.1"
>              connectionTimeout="20000"            
>               redirectPort="8443" />
将8080改为80，保存退出
重启tomcat即可



 
 
 
 
 
 
 







