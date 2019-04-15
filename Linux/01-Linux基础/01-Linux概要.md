## 一 Linux概要

## 二 Linux目录

```
/
根目录：一般根目录下只存放目录，在Linux下有且只有一个根目录。当你在终端里输入“/home”，你其实是在告诉电脑，先从/（根目录）开始，再进入到home目录。
/bin、/usr/bin
可执行二进制文件的目录，如常用的命令ls、tar、mv、cat等。
/usr
应用程序存放目录，/usr/bin 存放应用程序，/usr/share 存放共享数据		/usr/local:
存放软件升级包。
/usr/share/doc: 系统说明文件存放目录。
/usr/share/man: 程序说明文件存放目录。
/root
系统管理员root的家目录。
/etc
系统配置文件存放的目录，不建议在此目录下存放可执行文件，重要的配置文件有 /etc/inittab、/etc/fstab、/etc/init.d、/etc/X11、/etc/sysconfig、/etc/xinetd.d。
/home
系统默认的用户家目录，新增用户账号时，用户的家目录都存放在此目录下，~表示当前用户的家目录，~edu 表示用户 edu 的家目录。
/lib、/usr/lib、/usr/local/lib
系统使用的函数库的目录，程序在执行过程中，需要调用一些额外的参数时需要函数库的协助。
/sbin、/usr/sbin、/usr/local/sbin
放置可执行命令，如fdisk、shutdown、mount 等。与 /bin 不同的是，这几个目录是给系统管理员 root使用的命令，一般用户只能"查看"而不能设置和使用。
/tmp
一般用户或正在执行的程序临时存放文件的目录，任何人都可以访问，重要数据不可放置在此目录下。
/srv
服务启动之后需要访问的数据目录，如 www 服务需要访问的网页数据存放在 /srv/www 内。
/var
放置系统执行过程中经常变化的文件，如随时更改的日志文件 /var/log，	/var/log/message
所有的登录文件存放目录，/var/spool/mail：邮件存放的目录，/var/run:程序或服务启动后，其PID存放在该目录下。
```
## 三 Linux中的路径

绝对路径：从/目录开始描述的路径为绝对路径，如：
```
cd /home
ls /usr
```

相对路径:从当前位置开始描述的路径为相对路径，如：
```
cd ../../
ls abc/def
```

.和..:每个目录下都有.和..
```
. 表示当前目录
.. 表示上一级目录，即父目录
```
注意：根目录下的.和..都表示当前目录
