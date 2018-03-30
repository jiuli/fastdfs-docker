FastDFS本身支持文件的排重处理机制，但需要FastDHT作为文件hash的索引存储。FastDHT是FastDFS同一个作者的开源key-value数据库。其排重原理为：

FastDFS的storage server每次上传均计算文件的hash值，然后从FastDHT服务器上进行查找比对，如果没有返回，则写入hash，并将文件保存；如果有返回，则建立一个新的文件链接（软链），不保存文件。

生成镜像：docker build  -t jiuli/fastdht . 
