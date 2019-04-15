## 一 Linux命令概要

#### 1.1 Linux命令格式

```
格式：	comman	[-options]	[parameter]				[]表示可选
格式：	命令 		-选项 		参数      
示例：	ls    		-l    		/usr
```

参数：
-l	即：long，
-a	即：all，
-t	即：time，

命令、选项、参数之间用空格隔开

#### 1.2 常用命令
```

ls			        显示当前目录下的文件与文件夹名
ls				    显示当前目录下的文件
ls -l				以行列表方式查看文件
ls /bin			    查看根目录下的bin文件夹下的东西（绝对路径）
ls Documents 	    查看Documents文件夹下的东西（相对路径）
ls -a				显示隐藏文件
ls -l -h			文件大小以合适的单位显示
ls *.txt			显示以txt为后缀的文件
ls *.*
ls *.t?t			显示所有以 .t任意字符t 结尾的文件,*表示任意个字符,？表示单个字符
ls *.t[xn]t		    显示txt，tnt后缀文件
ls *.t[a-f]t		显示tat 到 tft 中所有的后缀的文件
ls \*a			    文件名有*需要转义,可以将所有命令合并书写：ls -lah

cd			        进入文件夹
cd .		        进入当前路径
cd ..		        回到当前路径的上级目录
cd /		        回到根目录
cd -		        回到上次执行命令的目录
cd ~		        进入当前用户的家目录
注意：cd后面可跟绝对路径，也可以跟相对路径。如果省略目录，则默认	切换到当前用户的主目录。如果路径是从根路径开始的，则路径的	前面需要加上 “ / ”，如 “ /mnt ”，通常进入某个目录里的文件	夹，前面不用加 “ / ”。

pwd		            显示当前目录路径
clear		        清屏
tab			        自动补全
touch 		        创建文件
上下键		        历史命令
su 用户名	        选择用户	普通用户命令行前缀是 $ root用户前缀是#
mkdir a/b	        如果没有a文件夹，那么就无法创建b文件夹 mkdir -p a/b可以解决
rm	-r		        删除文件、文件夹，但是不能递归删除内部文件
rm	-rf		        删除文件夹

```

#### 1.3 帮助文档

Linux的命令包括两种：
- 内部命令：	Shell解析器的一部分，如 cd pwd help；使用 help + 命令，如 help cd 可以查看帮助文档
- 外部命令：	独立于Shell的程序，如 ls mkdir cp,使用 man + 命令，如 man ls 可以查看帮助文档,其实外部命令就是外部来操作了Linux底层的库、命令后调用的CPU。

#### 1.4 manual

man是linux提供的一个手册，包含了绝大部分的命令、函数使用说明。例：
```
man ls 
man 2 printf
```

man中各个section意义如下：
```
Standard commands（标准命令）
System calls（系统调用，如open,write）
Library functions（库函数，如printf,fopen）
Special devices（设备文件的说明，/dev下各种设备）
File formats（文件格式，如passwd）
Games and toys（游戏和娱乐）
Miscellaneous（协定等如：档案系统、网络协定、environ全局变量）
Administrative Commands（管理员命令，如ifconfig）
```

man是按照手册的章节号的顺序进行搜索的。man设置了如下的功能键：
```
功能键	功能
空格键	显示手册页的下一屏
Enter键	一次滚动手册页的一行
b	回滚一屏
f	前滚一屏
q	退出man命令
h	列出所有功能键
/word	搜索word字符串
```
