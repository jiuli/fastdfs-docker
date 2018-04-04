#base on docker centos 
FROM centos:7

#AUTHOR
MAINTAINER jiuli <554856005@qq.com>

#闂佹澘绉堕悿鍡涙偝椤栨凹鏆旈柛娆愶耿閸ｏ拷
ENV CENTOS_VERSION=7 \
	FASTDFS_CONFIG=/etc/fdfs \
  USER_ROOT=/home/jiuli \
  FASTDFS_PATH=/home/jiuli/fastdfs \
  FASTDFS_TRACKER_PATH=/home/jiuli/fastdfs/tracker \
  LIB_FAST_COMMON_VERSION=1.0.36 \
  FASTDFS_VERSION=5.11 

#闁告帗绋戠紓鎾圭疀閸涢偊娲ｉ柣銊ュ濞叉媽銇愰敓锟�
RUN mkdir -p ${FASTDFS_PATH}/download \
  && mkdir -p ${FASTDFS_TRACKER_PATH} \ 
  && mkdir -p ${FASTDFS_CONFIG}
#閻庣懓顦抽ˉ濡沢et
RUN yum install -y wget

#闁哄洤鐡ㄥ畷鐬磚m婵犙嶆嫹 
RUN mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup \
	&& wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo \
	&& yum clean all \
	&& yum makecache 

#閻庣懓顦抽ˉ濠傤啅閵夈儱寰旈梻鍡嫹
RUN yum install -y zlib zlib-devel pcre pcre-devel gcc gcc-c++ openssl openssl-devel libevent libevent-devel perl unzip net-tools git



#濞戞挸顑堝ù锟�
RUN wget "https://github.com/happyfish100/libfastcommon/archive/V${LIB_FAST_COMMON_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/libfastcommon
RUN wget "https://github.com/happyfish100/fastdfs/archive/V${FASTDFS_VERSION}.tar.gz" -P ${FASTDFS_PATH}/download/fastdfs
RUN tar zxvf ${FASTDFS_PATH}/download/libfastcommon/V${LIB_FAST_COMMON_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/libfastcommon \
  && tar zxvf ${FASTDFS_PATH}/download/fastdfs/V${FASTDFS_VERSION}.tar.gz -C ${FASTDFS_PATH}/download/fastdfs 

#閻庣懓顦抽ˉ濡塱bfastcommon
WORKDIR ${FASTDFS_PATH}/download/libfastcommon/libfastcommon-${LIB_FAST_COMMON_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install"]
RUN ln -s /usr/lib64/libfastcommon.so /usr/local/lib/libfastcommon.so \
#	&& ln -s /usr/lib64/libfastcommon.so /usr/lib/libfastcommon.so \
 	&&	ln -s /usr/lib64/libfdfsclient.so /usr/local/lib/libfdfsclient.so \
    && ln -s /usr/lib64/libfdfsclient.so /usr/lib/libfdfsclient.so
     
#閻庣懓顦抽ˉ濂籥stdfs
WORKDIR ${FASTDFS_PATH}/download/fastdfs/fastdfs-${FASTDFS_VERSION}
RUN ["/bin/bash", "-c", "./make.sh && ./make.sh install"] 

RUN ln -s /usr/bin/fdfs_trackerd   /usr/local/bin \
	&& ln -s /usr/bin/fdfs_storaged   /usr/local/bin \
	&& ln -s /usr/bin/stop.sh         /usr/local/bin \
	&& ln -s /usr/bin/restart.sh      /usr/local/bin
#婵烇綀顕ф慨鐐哄礆濠靛棭娼楅柛鏍ㄧ墵閸樸倗绱旈敓锟�
RUN mkdir -p /var/cache/nginx/proxy_cache/tmp 
RUN cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
COPY fdfs_config/* /etc/fdfs/
COPY ["start","stop","/home/jiuli/"]

RUN chmod +x /home/jiuli/start \
  && chmod +x /home/jiuli/stop \
 && ln -s /home/jiuli/start /usr/local/bin/start_fdfs \
 && ln -s /home/jiuli/stop /usr/local/bin/stop_fdfs
#  && service fdfs_trackerd start
   
CMD ["/bin/bash", "start_fdfs"] 
