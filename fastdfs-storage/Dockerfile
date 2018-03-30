#!/bin/base on docker centos 
FROM centos:7

#AUTHOR
MAINTAINER jiuli <554856005@qq.com>

#闁板秶鐤嗛悳顖氼暔閸欐﹢鍣�
ENV CENTOS_VERSION=7 \
	FASTDFS_CONFIG=/etc/fdfs \
	USER_ROOT=/home/jiuli \
	FASTDFS_PATH=/home/jiuli/fastdfs \
  	FASTDFS_STORAGE_PATH=/home/jiuli/fastdfs/storage \
 	FASTDFS_STORAGE_DATA_PATH=/home/jiuli/fastdfs/storage/data \
  	LIB_FAST_COMMON_VERSION=1.0.36 \
  	FASTDFS_VERSION=5.11 \
  	NGINX_VERSION=1.11.7

#閸掓稑缂撹箛鍛邦洣閻ㄥ嫮娲拌ぐ锟�
RUN mkdir -p ${FASTDFS_PATH}/download \
 && mkdir -p ${FASTDFS_STORAGE_PATH} \
 && mkdir -p ${FASTDFS_PATH}/client \
 && mkdir -p ${FASTDFS_PATH}/mod \
 && mkdir -p ${FASTDFS_CONFIG} 
 
#鐎瑰顥妛get
RUN yum install -y wget

#閺囧瓨宕瞴um濠э拷 
RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
	&& wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
	&& yum clean all \
	&& yum makecache 

#鐎瑰顥婂銉ュ徔闂嗭拷
RUN yum install -y zlib zlib-devel pcre pcre-devel gcc gcc-c++ openssl openssl-devel libevent libevent-devel perl unzip net-tools git



#fastdfs-nginx-module鐟欙絽鍠卻torage娑斿妫块崥灞绢劄瀵ゆ儼绻滈敍灞惧娑撳秴鍩岄弬鍥︽閻ㄥ嫰妫舵０姗堢礉fastdfs-nginx-module閸欘垶鍣哥�规艾鎮滈弬鍥︽鏉╃偞甯撮崚鐗堢爱閺堝秴濮熼崳銊﹀閸掔増鏋冩禒韬诧拷锟�
#娑撳娴�
RUN wget "https://github.com/happyfish100/libfastcommon/archive/V${LIB_FAST_COMMON_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/libfastcommon
RUN wget "https://github.com/happyfish100/fastdfs/archive/V${FASTDFS_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/fastdfs
RUN git clone https://github.com/happyfish100/fastdfs-nginx-module.git ${FASTDFS_PATH}/download/nginx_module
# 閹稿洤鐣緁astdfs-nginx-module閻楀牊婀�
RUN cd ${FASTDFS_PATH}/download/nginx_module && git checkout 5a8110f57ec55ff580260cf1fc6aa152a5a4c574
RUN wget "http://nginx.org/download/nginx-${NGINX_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/nginx 
RUN tar zxvf ${FASTDFS_PATH}/download/libfastcommon/V${LIB_FAST_COMMON_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/libfastcommon \
 && tar zxvf ${FASTDFS_PATH}/download/fastdfs/V${FASTDFS_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/fastdfs \
 && tar zxvf ${FASTDFS_PATH}/download/nginx/nginx-${NGINX_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/nginx 

#鐎瑰顥妉ibfastcommon
WORKDIR ${FASTDFS_PATH}/download/libfastcommon/libfastcommon-${LIB_FAST_COMMON_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install"]

#鐎瑰顥奻astdfs
WORKDIR ${FASTDFS_PATH}/download/fastdfs/fastdfs-${FASTDFS_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install && cp conf/* /etc/fdfs/"] 

#鐎瑰顥妌ginx
WORKDIR ${FASTDFS_PATH}/download/nginx/nginx-${NGINX_VERSION}
RUN ./configure --add-module=${FASTDFS_PATH}/download/nginx_module/src \
  && make \
  && make install \
  && cp ${FASTDFS_PATH}/download/nginx_module/src/mod_fastdfs.conf /etc/fdfs/

#濞ｈ濮為崚婵嗩潗閸栨牠鍘ょ純锟�
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
COPY fdfs_config/* /etc/fdfs/
COPY nginx_config/nginx.conf /usr/local/nginx/conf/nginx.conf
COPY ["start","stop","/home/jiuli/"]
RUN chmod +x /home/jiuli/start \
  && chmod +x /home/jiuli/stop \
  && ln -s /home/jiuli/start /usr/local/bin/start_fdfs \
  && ln -s /home/jiuli/stop /usr/local/bin/stop_fdfs

CMD ["/bin/bash","start_fdfs"]
