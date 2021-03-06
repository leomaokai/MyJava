# FastDFS

> FastDFS 是一个开源的高性能分布式文件系统（DFS）。 它的主要功能包括：文件存储，文件同步和文件访问，以及高容量和负载平衡。主要解决了海量数据存储问题，特别适合以中小文件（建议范围：4KB < file_size <500MB）为载体的在线服务。

FastDFS 系统有三个角色：跟踪服务器(Tracker Server)、存储服务器(Storage Server)和客户端(Client)。

* **Tracker Server**：跟踪服务器，主要做调度工作，起到均衡的作用；负责管理所有的 storage server和 group，每个 storage 在启动后会连接 Tracker，告知自己所属 group 等信息，并保持周期性心跳。通过Trackerserver在文件上传时可以根据一些策略找到Storageserver提供文件上传服务。
* **Storage Server**：存储服务器，主要提供容量和备份服务；以 group 为单位，每个 group 内可以有多台 storage server，数据互为备份。Storage server没有实现自己的文件系统而是利用操作系统 的文件系统来管理文件。
* **Client**：客户端，上传下载数据的服务器，也就是我们自己的项目所部署在的服务器。

https://blog.csdn.net/prcyang/article/details/89946190