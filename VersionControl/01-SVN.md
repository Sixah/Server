## 一 SVN安装

SVN属于C/S结构软件（客户端与服务器端）：
- 服务端软件：VisualSVN，	网址：http://www.visualsvn.com/
- 客户端软件：TortoiseSVN，网址：http://tortoisesvn.net/downloads（注意：安装时必须勾选 Add Subversion command）

## 二 SVN使用

- 首先在SVN服务器端创建一个公有目录WebApp做为项目目录；
- 在WebApp目录下创建Shop文件夹，做为版本仓库，但是此时还不具备仓库功能；
- 创建版本仓库：svnadmin create Shop 文件夹路径（Shop仓库）
- 安装成功后，Shop中就会出现相应配置文件，如果命令报错，重启即可。