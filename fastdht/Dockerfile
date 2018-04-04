#!/bin/base on docker centos 
FROM centos:7

#AUTHOR
MAINTAINER jiuli <554856005@qq.com>

#闁板秶鐤嗛悳顖氼暔閸欐﹢鍣�
ENV CENTOS_VERSION=7 \
  FASTDHT_CONFIG=/etc/fdht  \
  FASTDFS_PATH=/home/jiuli/fastdfs \
  BERKELEY_DB_VERSION=6.2.23 \
  LIB_FAST_COMMON_VERSION=1.0.36 \
  FASTDHT_VERSION=7fac37e9a8e2c9ee2d4ccc309f96dbd35f2e0403
#娑撳﹪娼伴悧鍫熸拱閸欒渹璐焔it commit閺冭泛锟芥瑧鏁撻幋鎰畱sha1 hash閸婄锟斤拷

#閸掓稑缂撹箛鍛邦洣閻ㄥ嫮娲拌ぐ锟�
RUN mkdir -p ${FASTDFS_PATH}/fastdht \
	mkdir -p ${FASTDHT_CONFIG}

#鐎瑰顥妛get
RUN yum install -y wget

#閺囧瓨宕瞴um濠э拷 
RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
	&& wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
	&& yum clean all \
	&& yum makecache 

#鐎瑰顥婂銉ュ徔闂嗭拷
RUN yum install -y zlib zlib-devel pcre pcre-devel gcc gcc-c++ openssl openssl-devel libevent libevent-devel perl unzip net-tools git



#娑撳娴�
RUN wget https://github.com/happyfish100/libfastcommon/archive/V${LIB_FAST_COMMON_VERSION}.zip -P ${FASTDFS_PATH}/download/libfastcommon
RUN wget https://github.com/happyfish100/fastdht/archive/${FASTDHT_VERSION}.zip -P ${FASTDFS_PATH}/download/fastdht 
RUN wget "http://download.oracle.com/berkeley-db/db-${BERKELEY_DB_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download 
RUN unzip ${FASTDFS_PATH}/download/libfastcommon/V${LIB_FAST_COMMON_VERSION}.zip -d ${FASTDFS_PATH}/download/libfastcommon \
 && unzip ${FASTDFS_PATH}/download/fastdht/${FASTDHT_VERSION}.zip -d ${FASTDFS_PATH}/download/fastdht \
 && tar zxvf ${FASTDFS_PATH}/download/db-${BERKELEY_DB_VERSION}.tar.gz -C ${FASTDFS_PATH}/download

#鐎瑰顥妉ibfastcommon
WORKDIR ${FASTDFS_PATH}/download/libfastcommon/libfastcommon-${LIB_FAST_COMMON_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install"]
RUN ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so \
#	&& ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so \
 	&&	ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so \
    && ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
    
#鐎瑰顥奲erkeley db
WORKDIR ${FASTDFS_PATH}/download/db-${BERKELEY_DB_VERSION}/build_unix
RUN ../dist/configure --prefix=/usr/local/db-${BERKELEY_DB_VERSION} \
 && make \
 && make install

#鐎瑰顥奻astdht
WORKDIR ${FASTDFS_PATH}/download/fastdht/fastdht-${FASTDHT_VERSION}
RUN sed -i "s?CFLAGS='-Wall -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE'?CFLAGS='-Wall -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE -I/usr/local/db-${BERKELEY_DB_VERSION}/include/ -L/usr/local/db-${BERKELEY_DB_VERSION}/lib/'?" make.sh 
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install"] 
RUN cp /usr/local/db-${BERKELEY_DB_VERSION}/lib/libdb-${BERKELEY_DB_VERSION:0:3}.so /usr/lib64

#濞ｈ濮為崚婵嗩潗閸栨牠鍘ょ純锟�
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
COPY fdht_config/* /etc/fdht/
COPY ["start","stop","/home/jiuli/"]
RUN chmod +x /home/jiuli/start \
  && chmod +x /home/jiuli/stop \
  && ln -s /home/jiuli/start /usr/local/bin/start_fdht \
  && ln -s /home/jiuli/stop /usr/local/bin/stop_fdht

CMD ["/bin/bash", "start_fdht"] 
