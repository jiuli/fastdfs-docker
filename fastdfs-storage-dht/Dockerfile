#base on docker centos 
FROM centos:7

#AUTHOR
MAINTAINER jiuli <554856005@qq.com>
#閰嶇疆鐜鍙橀噺
ENV CENTOS_VERSION=7 \
  FASTDHT_CONFIG=/etc/fdht \
  FASTDFS_CONFIG=/etc/fdfs \
  USER_ROOT=/home/jiuli \
  FASTDFS_PATH=/home/jiuli/fastdfs \
  FASTDFS_STORAGE_PATH=/home/jiuli/fastdfs/storage \
  FASTDFS_STORAGE_DATA_PATH=/home/jiuli/fastdfs/storage/data \
  LIB_FAST_COMMON_VERSION=1.0.36 \
  FASTDFS_VERSION=5.11 \
  NGINX_VERSION=1.11.7 \
  FASTDFS_NGINX_VERSION=5a8110f57ec55ff580260cf1fc6aa152a5a4c574
	
#鍒涘缓蹇呰鐨勭洰褰�
RUN mkdir -p ${FASTDFS_PATH}/download \
  && mkdir -p ${FASTDFS_STORAGE_PATH} \
  && mkdir -p ${FASTDFS_PATH}/client \
  && mkdir -p ${FASTDFS_PATH}/mod \ 
  && mkdir -p ${FASTDHT_CONFIG} \
  && mkdir -p ${FASTDFS_CONFIG}
#瀹夎wget
RUN yum install -y wget

#鏇存崲yum婧� 
RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
  && wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
  && yum clean all \
  && yum makecache 

#瀹夎宸ュ叿闆�
RUN yum install -y zlib zlib-devel pcre pcre-devel gcc gcc-c++ openssl openssl-devel libevent libevent-devel perl unzip net-tools git



#涓嬭浇
RUN wget "https://github.com/happyfish100/libfastcommon/archive/V${LIB_FAST_COMMON_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/libfastcommon
RUN wget "https://github.com/happyfish100/fastdfs/archive/V${FASTDFS_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/fastdfs
RUN wget https://github.com/happyfish100/fastdfs-nginx-module/archive/${FASTDFS_NGINX_VERSION}.zip -P ${FASTDFS_PATH}/download/nginx_module
RUN wget "http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/nginx 
RUN tar zxvf ${FASTDFS_PATH}/download/libfastcommon/V${LIB_FAST_COMMON_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/libfastcommon \
  && tar zxvf ${FASTDFS_PATH}/download/fastdfs/V${FASTDFS_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/fastdfs \
  && unzip ${FASTDFS_PATH}/download/nginx_module/${FASTDFS_NGINX_VERSION}.zip -d ${FASTDFS_PATH}/download/nginx_module \
  && tar zxvf ${FASTDFS_PATH}/download/nginx/nginx-${NGINX_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/nginx 

#瀹夎libfastcommon
WORKDIR ${FASTDFS_PATH}/download/libfastcommon/libfastcommon-${LIB_FAST_COMMON_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install"]
RUN ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so \
#	&& ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so \
 	&&	ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so \
    && ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
    
#瀹夎fastdfs
WORKDIR ${FASTDFS_PATH}/download/fastdfs/fastdfs-${FASTDFS_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install"] 

RUN ln -s /usr/bin/fdfs_trackerd   /usr/local/bin \
	&& ln -s /usr/bin/fdfs_storaged   /usr/local/bin \
	&& ln -s /usr/bin/stop.sh         /usr/local/bin \
	&& ln -s /usr/bin/restart.sh      /usr/local/bin
	
#瀹夎nginx
WORKDIR ${FASTDFS_PATH}/download/nginx/nginx-${NGINX_VERSION}
RUN ./configure --add-module=${FASTDFS_PATH}/download/nginx_module/fastdfs-nginx-module-${FASTDFS_NGINX_VERSION}/src \
  && make \
  && make install \
  && cp ${FASTDFS_PATH}/download/nginx_module/fastdfs-nginx-module-${FASTDFS_NGINX_VERSION}/src/mod_fastdfs.conf /etc/fdfs/

#娣诲姞鍒濆鍖栭厤缃�
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
COPY fdht_config/* /etc/fdht/
COPY fdfs_config/* /etc/fdfs/
COPY nginx_config/nginx.conf /usr/local/nginx/conf/nginx.conf
COPY ["start","stop","/home/jiuli/"]
RUN /usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf
RUN chmod +x /home/jiuli/start \
  && chmod +x /home/jiuli/stop \
  && ln -s /home/jiuli/start /usr/local/bin/start_fdfs \
  && ln -s /home/jiuli/stop /usr/local/bin/stop_fdfs

CMD ["/bin/bash", "start_fdfs"] 
