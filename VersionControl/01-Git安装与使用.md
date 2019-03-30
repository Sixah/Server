## 一 git 简介

## 二 git 安装

#### 2.1 win与mac安装

#### 2.2 centOS源码安装git

```
1 查看当前系统是否已经安装git
git --version              

2 如果没有安装git，那么此时需要预先安装git依赖包
yum -y update
yum -y install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker

3 下载git并解压源码
wget https://github.com/git/git/archive/v2.3.0.zip
unzip v2.3.0.zip                                # 如果提示unzip不存在，则 yum install -y unzip zip

3 编译安装到/usr/local/git目录下
cd git-2.3.0
make prefix=/usr/local/git all
make prefix=/usr/local/git install

注意：因为服务器时间不对编译的过程中报错如下图，使用ntpdate自动校正系统时间。报错“Writing perl.mak for Git make[2]: *** [perl.mak] Error 1”，请重启apache服务，service httpd restart。

4 定义git路径
git --version                                   # 查看版本的结果是1.8，不是2.3，因为默认使用了"/usr/bin"下的git
vim /etc/profile                                # 或者 /etc/bashrc
export PATH=/usr/local/git/bin:$PATH            # 加入该句
source /etc/profile
git --version                                   # 版本号为2.3，安装成功
```

## 三 git 使用

#### 3.1 基础使用

#### 3.2 使用问题

问题一：commit时，填错了-m内容，该如何撤销提交？ 
``` 
此时，可以重新add后再次commit，但是使用的命令为：`git commit -m "****" --amend`  
```

问题二：如何清空历史提交记录，成为一个干净的新仓库？  

即把旧项目提交到Git上，但是会有一些历史记录，这些历史记录中可能会有项目密码等敏感信息。如何删除这些历史记录，形成一个全新的仓库，并且保持代码不变呢？
```
git checkout --orphan latest_branch         # 1. Checkout
git add -A                                  # 2. Add all the files
git commit -am "commit message"             # 3. Commit the changes
git branch -D master                        # 4. Delete the branch
git branch -m master                        # 5.Rename the current branch to master
git push -f origin master                   # 6.Finally, force update your repository
```