# 常用的系统命令

## 系统命令

```shell
shutdown -h now		#立刻进行关机
shutdown -r now		#重新启动
reboot #重启
su 用户 #切换用户
su - #启动root
sudo #以另一个用户身份执行命令
passwd #修改用户密码
logout #用户注销
ps #查看当前进程
ps -ef | grep test #查看test进程
kill PID #杀死PID进程
kill -9 PID #强行杀死进程
```

## 快捷命令

``` shell
chear #清屏
ctrl l #清屏
ctrl a #移动光标到行首
ctrl e #移动光标到行尾
ctrl f #光标后移一个字符,右箭头
ctrl b #光标前移一个字符,左箭头
ctrl d #删除光标位置的字符
ctrl t #光标位置的字符于前面的字符交换
ctrl k #剪切从光标位置到行尾的文本
ctrl u #剪切从光标位置到行首的文本
ctrl y #把剪切的文本粘贴到光标位置
ctrl r #搜索历史命令
ctrl p #移动到上一个历史命令,类似上箭头
ctrl n #移动到下一个历史命令,类似下箭头
history #显示历史命令
```

## 防火墙操作

```shell
#需要权限
systemctl start firewalld #启动
systemctl status firewalld #查看状态
systemctl disable firewalld #停止
systemctl stop firewalld #禁用
firewall-cmd --version #查看版本
firewall-cmd --state #显示状态
firewall-cmd --list-all #查看打开的端口
firewall-cmd --reload #更新防火墙规则
firewall-cmd --add-port=80/tcp --permanent #开放端口(永久生效)
firewall-cmd --query-port=80/tcp #查询端口是否打开
firewall-cmd --remove-port=80/tcp --permanent #删除端口
```

# 文件命令

## 文件操作

```shell
ls #展开当前目录下的可见文件
ls a #展开当前目录下的所有文件
ls l #展开当前目录下文件的详细信息
ll a #展开当前目录下所有文件的详细信息
pwd  #显示目前的目录
mkdir dir #创建dir目录
rmdir dir #删除dir空目录
touch file #创建文件
rm file	#带询问的删除
rm -f file #直接删除
rm -r dir #带询问的递归删除dir目录
rm -rf dir #直接删除dir目录
cp test1 test2 #复制test文件或目录到test2文件或目录
cp -i test1 test2 #发生覆盖时需要确认
cp -r dir1 dir2 #复制目录dir1中的内容到目录dir2
mv test1 test2 #移动文件或目录,与cp类似,或修改名称
tar -zcvf test.tar.gz /test/* #打包test目录文件
tar -zxvf test.tar.gz #解压文件于当前目录
tar -zxvf test.tar.gz -C /dir #解压文件于指定目录
```

## 查看文件

```shell
cat #由第一行开始显示文件内容
more #可向后翻页的显示文件内容
less #与more类似,但可以向前翻页
#空格键:向下翻一页
#enter:向下翻一行
#ctrl p:向上翻一行
#ctrl n:向下翻一行
#q:退出
```



## 权限操作

```shell
#需要一定的权限才可权限操作
chmod u=rwx,g=rw,o=r test #修改文件或目录的权限
chmod 764 test #数字代表权限r=4 w=2 x=1
chown 用户:文件 #修改文件或目录的所属用户
chown 用户:组:文件 #修改文件或目录的所属用户
```

# 软件安装

```bash
#更新gcc 9.3.1版本
sudo yum install centos-release-scl
sudo yum install devtoolset-9
scl enable devtoolset-9 bash
```

```bash
#安装boost
yum install boost
yum install boost-devel
yum install boost-doc
```

# 用户操作

```shell
useradd [选项] 用户名  #添加用户
选项含义如下：
-d 目录 指定用户主目录，如果此目录不存在，则同时使用-m选项，可以创建主目录。
-g 用户组 指定用户所属的用户组。
用户名 指定新账号的登录名

userdel 选项 用户名 #删除操作
常用的选项是-r，它的作用是把用户的主目录一起删除

passwd 选项 用户名 #修改用户密码
可使用的选项：
代码:
-l 锁定口令，即禁用账号。
-u 口令解锁。
-d 使账号无口令。

groupadd groupname #添加组
groupdel groupname #删除组
```



