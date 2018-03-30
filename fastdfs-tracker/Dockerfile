#base on docker centos 
FROM centos:7

#AUTHOR
MAINTAINER jiuli <554856005@qq.com>

#配置环境变量
ENV CENTOS_VERSION=7 \
  USER_ROOT=/home/jiuli \
  FASTDFS_PATH=/home/jiuli/fastdfs \
  FASTDFS_TRACKER_PATH=/home/jiuli/fastdfs/tracker \
  LIB_FAST_COMMON_VERSION=1.0.36 \
  FASTDFS_VERSION=5.11 

#创建必要的目录
RUN mkdir -p ${FASTDFS_PATH}/download \
  && mkdir -p ${FASTDFS_TRACKER_PATH} 
  
#安装wget
RUN yum install -y wget

#更换yum源 
RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
	&& wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
	&& yum clean all \
	&& yum makecache 

#安装工具集
RUN yum install -y zlib zlib-devel pcre pcre-devel gcc gcc-c++ openssl openssl-devel libevent libevent-devel perl unzip net-tools git



#下载
RUN wget "https://github.com/happyfish100/libfastcommon/archive/V${LIB_FAST_COMMON_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/libfastcommon
RUN wget "https://github.com/happyfish100/fastdfs/archive/V${FASTDFS_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/fastdfs
RUN tar zxvf ${FASTDFS_PATH}/download/libfastcommon/V${LIB_FAST_COMMON_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/libfastcommon \
  && tar zxvf ${FASTDFS_PATH}/download/fastdfs/V${FASTDFS_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/fastdfs 

#安装libfastcommon
WORKDIR ${FASTDFS_PATH}/download/libfastcommon/libfastcommon-${LIB_FAST_COMMON_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install"]

#安装fastdfs
WORKDIR ${FASTDFS_PATH}/download/fastdfs/fastdfs-${FASTDFS_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install && cp conf/* /etc/fdfs/"] 

#添加初始化配置
RUN mkdir -p /var/cache/nginx/proxy_cache/tmp 
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
COPY fdfs_config/* /etc/fdfs/
COPY ["start","stop","/home/jiuli/"]
RUN chmod +x /home/jiuli/start \
  && chmod +x /home/jiuli/stop \
  && ln -s /home/jiuli/start /usr/local/bin/start_fdfs \
  && ln -s /home/jiuli/stop /usr/local/bin/stop_fdfs

CMD ["/bin/bash","start_fdfs"] 
