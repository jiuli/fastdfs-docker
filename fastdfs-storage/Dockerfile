#base on docker centos 
FROM centos:7

#AUTHOR
MAINTAINER jiuli <jiuli@jiuli.com>

#安装wget
RUN yum install -y wget

#更换yum源 
RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
	&& wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
	&& yum clean all \
	&& yum makecache 

#安装工具集
RUN yum install -y zlib zlib-devel pcre pcre-devel gcc gcc-c++ openssl openssl-devel libevent libevent-devel perl unzip net-tools git

#配置环境变量
ENV CENTOS_VERSION=7 \
	USER_ROOT=/home/jiuli \
	FASTDFS_PATH=/home/jiuli/fastdfs \
  	FASTDFS_STORAGE_PATH=/home/jiuli/fastdfs/storage \
 	FASTDFS_STORAGE_DATA_PATH=/home/jiuli/fastdfs/storage/data \
  	LIB_FAST_COMMON_VERSION=1.0.36 \
  	FASTDFS_VERSION=5.11 \
  	NGINX_VERSION=1.11.7

#创建必要的目录
RUN mkdir -p ${FASTDFS_PATH}/download \
 && mkdir -p ${FASTDFS_STORAGE_PATH} \
 && mkdir -p ${FASTDFS_PATH}/client \
 && mkdir -p ${FASTDFS_PATH}/mod 

#fastdfs-nginx-module解决storage之间同步延迟，找不到文件的问题，fastdfs-nginx-module可重定向文件连接到源服务器找到文件。
#下载
RUN wget "https://github.com/happyfish100/libfastcommon/archive/V${LIB_FAST_COMMON_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/libfastcommon
RUN wget "https://github.com/happyfish100/fastdfs/archive/V${FASTDFS_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/fastdfs
RUN git clone https://github.com/happyfish100/fastdfs-nginx-module.git ${FASTDFS_PATH}/download/nginx_module
# 指定fastdfs-nginx-module版本
RUN cd ${FASTDFS_PATH}/download/nginx_module && git checkout 5a8110f57ec55ff580260cf1fc6aa152a5a4c574
RUN wget "http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/nginx 
RUN tar zxvf ${FASTDFS_PATH}/download/libfastcommon/V${LIB_FAST_COMMON_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/libfastcommon \
 && tar zxvf ${FASTDFS_PATH}/download/fastdfs/V${FASTDFS_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/fastdfs \
 && tar zxvf ${FASTDFS_PATH}/download/nginx/nginx-${NGINX_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/nginx 

#安装libfastcommon
WORKDIR ${FASTDFS_PATH}/download/libfastcommon/libfastcommon-${LIB_FAST_COMMON_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install"]

#安装fastdfs
WORKDIR ${FASTDFS_PATH}/download/fastdfs/fastdfs-${FASTDFS_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install && cp conf/* /etc/fdfs/"] 

#安装nginx
WORKDIR ${FASTDFS_PATH}/download/nginx/nginx-${NGINX_VERSION}
RUN ./configure --add-module=${FASTDFS_PATH}/download/nginx_module/src \
  && make \
  && make install \
  && cp ${FASTDFS_PATH}/download/nginx_module/src/mod_fastdfs.conf /etc/fdfs/

#添加初始化配置
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
COPY fdfs_config/* /etc/fdfs/
COPY nginx_config/nginx.conf /usr/local/nginx/conf/nginx.conf
COPY ["start","stop","/home/jiuli/"]
RUN chmod +x /home/jiuli/start \
  && chmod +x /home/jiuli/stop \
  && ln -s /home/jiuli/start /usr/local/bin/start_fdfs \
  && ln -s /home/jiuli/stop /usr/local/bin/stop_fdfs

CMD ["bash","start_fdfs"]
