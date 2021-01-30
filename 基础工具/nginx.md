安装完成一般常用命令

进入安装目录中，

命令： cd /usr/local/nginx/sbin

启动，关闭，重启，命令：

./nginx 启动

./nginx -s stop 关闭

./nginx -s reload 重启

[Nginx入门笔记](../进阶提高/Nginx/Nginx入门.md)

```
主要配置参数
/usr/local/nginx/conf/nginx.conf

(1)user  nginx;  #配置运行nginx的用户
(2)worker_processes  2; #初始的子进程数量
(3)worker_connections  1024; #配置单个进程处理的最大请求连接数
(4)server{  #配置虚拟主机
(5)listen	#配置虚拟主机监听端口
(6)server_name #配置服务器域名
(7)location  匹配规则 { }   #配置匹配特定的url
(8)root   #配置网站根目录
(9)index  #配置虚拟主机的默认首页
(10)error_page  404              /404.html; 
#解释：当出现404的时候，要重定向到网站根目录下的404.html页面
}

```

