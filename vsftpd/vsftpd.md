# Vsftpd配置教程

环境：debian 7.0

## 安装

安装

    sudo apt-get install vsftpd libpam-mysql mysql-server

查看是否打开21端口

    $ sudo netstat -npltu | grep 21
    tcp        0      0 0.0.0.0:21              0.0.0.0:*               LISTEN      15601/vsftpd    

## 系统账户

建立Vsftpd的虚拟宿主服务：
    
    useradd vsftpd -s /usr/sbin/nologin

虚拟用户并不是系统用户，也就是说这些FTP的用户在系统中是不存在的。他们的总体权限其实是集中寄托在一个在系统中的某一个用户身上的，所谓Vsftpd的虚拟宿主用户，就是这样一个支持着所有虚拟用户的宿主用户。由于他支撑了FTP的所有虚拟的用户，那么他本身的权限将会影响着这些虚拟的用户，因此，处于安全性的考虑，也要非分注意对该用户的权限的控制，该用户也绝对没有登陆系统的必要，这里也设定他为不能登陆系统的用户。

## 编辑Vsftpd配置文件

在编辑配置之前先备份

    sudo cp /etc/vsftpd.conf /etc/vsftpd.conf.bk
    sudo vi /etc/vsftpd.conf

编辑配置

    # Run standalone?  vsftpd can run either from an inetd or as a standalone
    # daemon started from an initscript.
    # 设定该Vsftpd服务工作在StandAlone模式下。
    listen=YES
    
    # Run standalone with IPv6?
    # Like the listen parameter, except vsftpd will listen on an IPv6 socket
    # instead of an IPv4 one. This parameter and the listen parameter are mutually
    # exclusive.
    #listen_ipv6=YES
    
    # Allow anonymous FTP?
    # 设定不允许匿访问
    anonymous_enable=NO
    
    # Uncomment this to allow local users to log in. 
    # 设定本地用户可以访问，否则虚拟用户无法访问。
    local_enable=YES
    
    # Uncomment this to enable any form of FTP write command.
    # 设定可以写操作
    write_enable=YES
    
    # Default umask for local users is 077. You may wish to change this to 022,
    # if your users expect that (022 is used by most other ftpd's)
    # 设定上传后文件的权限掩码。
    local_umask=022
    
    # Uncomment this to allow the anonymous FTP user to upload files. This only
    # has an effect if the above global write enable is activated. Also, you will
    # obviously need to create a directory writable by the FTP user.
    # 禁止匿名用户上传
    anon_upload_enable=NO
    
    # Uncomment this if you want the anonymous FTP user to be able to create
    # new directories.
    # 禁止匿名用户建立目录
    anon_mkdir_write_enable=NO
    
    # Activate directory messages - messages given to remote users when they
    # go into a certain directory.
    # 设定开启目录标语功能
    dirmessage_enable=YES
    
    # If enabled, vsftpd will display directory listings with the time
    # in  your  local  time  zone.  The default is to display GMT. The
    # times returned by the MDTM FTP command are also affected by this
    # option.
    use_localtime=YES
    
    # Activate logging of uploads/downloads.
    # 设定开启日志记录功能
    xferlog_enable=YES
    
    # Make sure PORT transfer connections originate from port 20 (ftp-data).
    # 设定端口20进行数据连接
    connect_from_port_20=YES
    
    # If you want, you can arrange for uploaded anonymous files to be owned by
    # a different user. Note! Using "root" for uploaded files is not
    # recommended!
    # 禁止上传文件更改宿主
    chown_uploads=NO
    #chown_username=whoever
    
    # You may override where the log file goes if you like. The default is shown
    # below.
    # 设定Vsftpd的服务日志保存路径。注意，该文件默认不存在。必须要手动touch出来，并且由于这里更改了Vsftpd的服务宿主用户为手动建立的Vsftpd。必须注意给与该用户对日志的写入权限，否则服务将启动失败。
    xferlog_file=/var/log/vsftpd.log
    
    # If you want, you can have your log file in standard ftpd xferlog format.
    # Note that the default log file location is /var/log/xferlog in this case.
    # 日志使用标准的记录格式。
    xferlog_std_format=YES
    
    # You may change the default value for timing out an idle session.
    # 设定空闲连接超时时间，这里使用默认。将具体数值留给每个具体用户具体指定，当然如果不指定的话，还是使用这里的默认值600，单位秒。
    #idle_session_timeout=600
    
    # You may change the default value for timing out a data connection.
    # 设定单次最大连续传输时间，这里使用默认。将具体数值留给每个具体用户具体指定，当然如果不指定的话，还是使用这里的默认值120，单位秒。
    #data_connection_timeout=120
    
    # It is recommended that you define on your system a unique user which the
    # ftp server can use as a totally isolated and unprivileged user.
    # 设定支撑Vsftpd服务的宿主用户为手动建立的Vsftpd用户。注意，一旦做出更改宿主用户后，必须注意一起与该服务相关的读写文件的读写赋权问题。比如日志文件就必须给与该用户写入权限等。
    nopriv_user=vsftpd
    
    # Enable this and the server will recognise asynchronous ABOR requests. Not
    # recommended for security (the code is non-trivial). Not enabling it,
    # however, may confuse older FTP clients.
    #设定支持异步传输功能。
    async_abor_enable=YES
    
    # By default the server will pretend to allow ASCII mode but in fact ignore
    # the request. Turn on the below options to have the server actually do ASCII
    # mangling on files when in ASCII mode.
    # Beware that on some FTP servers, ASCII support allows a denial of service
    # attack (DoS) via the command "SIZE /big/file" in ASCII mode. vsftpd
    # predicted this attack and has always been safe, reporting the size of the
    # raw file.
    # ASCII mangling is a horrible feature of the protocol.
    #设定支持ASCII模式的上传和下载功能。
    ascii_upload_enable=YES
    ascii_download_enable=YES
    
    # You may fully customise the login banner string:
    # 设定Vsftpd的登陆标语。
    ftpd_banner=Welcome to blah FTP service.
    
    # You may specify a file of disallowed anonymous e-mail addresses. Apparently
    # useful for combatting certain DoS attacks.
    #deny_email_enable=YES
    # (default follows)
    #banned_email_file=/etc/vsftpd.banned_emails
    #
    # You may restrict local users to their home directories.  See the FAQ for
    # the possible risks in this before using chroot_local_user or
    # chroot_list_enable below.
    #chroot_local_user=YES
    #
    # You may specify an explicit list of local users to chroot() to their home
    # directory. If chroot_local_user is YES, then this list becomes a list of
    # users to NOT chroot().
    # (Warning! chroot'ing can be very dangerous. If using chroot, make sure that
    # the user does not have write access to the top level directory within the
    # chroot)
    #chroot_local_user=YES
    #禁止用户登出自己的FTP主目录。
    chroot_list_enable=NO
    
    # (default follows)
    #chroot_list_file=/etc/vsftpd.chroot_list
    #
    # You may activate the "-R" option to the builtin ls. This is disabled by
    # default to avoid remote users being able to cause excessive I/O on large
    # sites. However, some broken FTP clients such as "ncftp" and "mirror" assume
    # the presence of the "-R" option, so there is a strong case for enabling it.
    # 禁止用户登陆FTP后使用"ls -R"的命令。该命令会对服务器性能造成巨大开销。如果该项被允许，那么挡多用户同时使用该命令时将会对该服务器造成威胁。
    ls_recurse_enable=NO
    
    # Customization
    #
    # Some of vsftpd's settings don't fit the filesystem layout by
    # default.
    #
    # This option should be the name of a directory which is empty.  Also, the
    # directory should not be writable by the ftp user. This directory is used
    # as a secure chroot() jail at times vsftpd does not require filesystem
    # access.
    secure_chroot_dir=/var/run/vsftpd/empty
    
    # This string is the name of the PAM service vsftpd will use.
    # 设定PAM服务下Vsftpd的验证配置文件名。因此，PAM验证将参考/etc/pam.d/下的vsftpd文件配置。
    pam_service_name=vsftpd
    
    # This option specifies the location of the RSA certificate to use for SSL
    # encrypted connections.
    rsa_cert_file=/etc/ssl/private/vsftpd.pem
    
    # 虚拟用户配置
    # 设定启用虚拟用户功能。
    guest_enable=YES
    
    # 指定虚拟用户的宿主用户。
    # guest_username=vsftpd
    
    # 设定虚拟用户的权限符合他们的宿主用户。
    virtual_use_local_privs=YES
    
    # 设定虚拟用户个人Vsftp的配置文件存放路径。也就是说，这个被指定的目录里，将存放每个Vsftp虚拟用户个性的配置文件，一个需要注意的地方就是这些配置文件名必须和虚拟用户名相同。
    user_config_dir=/etc/vsftpd/vconf

建立Vsftpd的日志文件，并更该属主为Vsftpd的服务宿主用户：
    
    touch /var/log/vsftpd.log
    chown vsftpd.vsftpd /var/log/vsftpd.log

建立虚拟用户配置文件存放路径

    mkdir /etc/vsftpd/vconf/

虚拟用户配置, 按需求把virtuser改成具体用户

    vi /etc/vsftpd/vconf/virtuser

    #指定虚拟用户的具体主路径。
    local_root=/home/vsftp/virtuser
    
    #设定不允许匿名用户访问。
    anonymous_enable=NO
    
    #设定允许写操作。
    write_enable=YES
    
    #设定上传文件权限掩码。
    local_umask=022
    
    #设定不允许匿名用户上传。
    anon_upload_enable=NO
    
    #设定不允许匿名用户建立目录。
    anon_mkdir_write_enable=NO
    
    #设定空闲连接超时时间。
    idle_session_timeout=600
    
    #设定单次连续传输最大时间。
    data_connection_timeout=120
    
    #设定并发客户端访问个数。
    max_clients=10
    
    #设定单个客户端的最大线程数，这个配置主要来照顾Flashget、迅雷等多线程下载软件。
    max_per_ip=5
    
    #设定该用户的最大传输速率，单位b/s。
    local_max_rate=50000
    

在这里作为虚拟用户的配置文件模版只需要留一些和用户流量控制，访问方式控制的配置项目就可以了。这里的关键项是local_root这个配置，用来指定这个虚拟用户的FTP主路径。

### 检查权限

    chown -R vsftpd /home/vsftp/
    ll /home/vsftp

## MySQL支持

安装

    sudo apt-get install mysql-server libpam-mysql

连接MySQL

    mysql -u root -p

创建数据库

    create database vsftpd;

创建用户表

    use vsftpd;
    create table users(username varchar(30), password varchar(50));

插入虚拟用户

    insert into users (username, password)values ('foo',password('123456'));

授权

    GRANT ALL PRIVILEGES ON vsftpd.* TO vsftpd@localhost IDENTIFIED BY 'passwd123456';
    FLUSH PRIVILEGES;

退出

    quit;

编辑pam

    cp /etc/pam.d/vsftpd /etc/pam.d/vsftpd_orig
    cat /dev/null > /etc/pam.d/vsftpd
    vi /etc/pam.d/vsftpd

    auth required pam_mysql.so user=vsftpd passwd=passwd123456 host=localhost db=vsftpd table=users usercolumn=username passwdcolumn=password crypt=2
    account required pam_mysql.so user=vsftpd passwd=passwd123456 host=localhost db=vsftpd table=users usercolumn=username passwdcolumn=password crypt=2

上面涉及到的参数，只要对应前面数据库的设置就可以明白它们的含义。这里需要说明的是crypt参数。crypt表示口令字段中口令的加密方式：

* crypt=0，口令以明文方式(不加密)保存在数据库中；
* crypt=1，口令使用UNIX系统的DES加密方式加密后保存在数据库中；
* crypt= 2，口令经过MySQL的password()函数加密后保存。

## 运行

    sudo service vsftpd start

## 可能的错误

500 OOPS:错误

有可能是你的vsftpd.con配置文件中有不能被实别的命令，还有一种可能是命令的YES 或 NO 后面有空格。
