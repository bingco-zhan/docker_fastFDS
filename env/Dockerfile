
FROM centos:7
# 作者
MAINTAINER bingco


#===============================================
#   FastDFS 文件服务器容器搭建
#   该容器需联网下载相关软件
#===============================================

# 修改yum源
RUN yum install -y yum-utils
RUN yum-config-manager --add-repo http://mirrors.aliyun.com/repo/Centos-7.repo


# 安装必要依赖
RUN yum update -y
RUN yum install -y git gcc gcc-c++ make automake autoconf libtool pcre pcre-devel zlib zlib-devel openssl-devel wget vim




# 拉取安装包
WORKDIR /usr/local/src
RUN wget https://github.com/happyfish100/fastdfs/archive/V6.06.tar.gz -O fastdfs.tar.gz
RUN wget https://github.com/happyfish100/libfastcommon/archive/V1.0.43.tar.gz -O libfastcommon.tar.gz
RUN wget https://github.com/happyfish100/fastdfs-nginx-module/archive/V1.22.tar.gz -O fastdfs-nginx-module.tar.gz
RUN wget https://github.com/nginx/nginx/archive/release-1.17.9.tar.gz -O nginx.tar.gz
RUN tar -xvf fastdfs.tar.gz
RUN tar -xvf libfastcommon.tar.gz
RUN tar -xvf fastdfs-nginx-module.tar.gz
RUN tar -xvf nginx.tar.gz
RUN rm *.tar.gz




WORKDIR /usr/local/src/libfastcommon-1.0.43
RUN ./make.sh && ./make.sh install




WORKDIR /usr/local/src/fastdfs-6.06
RUN ./make.sh && ./make.sh install



WORKDIR /usr/local/src/nginx-release-1.17.9
RUN auto/configure --add-module=/usr/local/src/fastdfs-nginx-module-1.22/src --prefix=/usr/local/nginx
RUN make && make install



ADD client.conf /etc/fdfs/client.conf
ADD storage.conf /etc/fdfs/storage.conf
ADD tracker.conf /etc/fdfs/tracker.conf
ADD nginx.conf /usr/local/nginx/conf/nginx.conf
ADD mod_fastdfs.conf /etc/fdfs/mod_fastdfs.conf
RUN cp /usr/local/src/fastdfs-6.06/conf/http.conf /etc/fdfs/http.conf
RUN cp /usr/local/src/fastdfs-6.06/conf/mime.types /etc/fdfs/mime.types
RUN mkdir -p /data/tracker
RUN mkdir -p /data/storage



# 定义初始变量
ENV tracker_server 127.0.0.1
ENV group_name group1



WORKDIR /usr/local/bin
RUN echo "sed -i s/@tracker_server@/\${tracker_server}/g /etc/fdfs/mod_fastdfs.conf" > entrypoint.sh
RUN echo "sed -i s/@tracker_server@/\${tracker_server}/g /etc/fdfs/storage.conf" >> entrypoint.sh
RUN echo "sed -i s/@tracker_server@/\${tracker_server}/g /etc/fdfs/client.conf" >> entrypoint.sh
RUN echo "sed -i s/@group@/\${group_name}/g /etc/fdfs/mod_fastdfs.conf" >> entrypoint.sh
RUN echo "sed -i s/@group@/\${group_name}/g /etc/fdfs/storage.conf" >> entrypoint.sh
RUN echo "" >> entrypoint.sh
RUN echo "fdfs_trackerd /etc/fdfs/tracker.conf" >> entrypoint.sh
RUN echo "fdfs_storaged /etc/fdfs/storage.conf" >> entrypoint.sh
RUN echo "/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf -g 'daemon off;'" >> entrypoint.sh
RUN chmod u+x ./entrypoint.sh
CMD ["entrypoint.sh"]



EXPOSE 8088
EXPOSE 23000
EXPOSE 22122
ENTRYPOINT ["/bin/bash"]
