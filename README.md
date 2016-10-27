# Nginx-rtmp
Nginx+rtmp 方式部署流媒体服务进行视频点播直播

# 安装服务流程

一，下载包：

1，	nginx-1.6.3.tar.gz

2，	nginx_mod_h264_streaming-2.2.7.tar.gz

3，	openssl-1.0.2d.tar.gz

4，	pcre-8.35.tar.gz

5，	zlib-1.2.8.tar.gz

6，	yamdi-1.9.tar.gz

7，	player.swf

8，	视频文件

二，安装

1，	确认是否已经安装，若没有先安装pcre-8.35.tar.gz 和 openssl-1.0.2d.tar.gz 和 zlib-1.2.8.tar.gz，否则不用安装。<tar方式，这些就不做详说>

2，	解压nginx_mod_h264_streaming-2.2.7.tar.gz

3，	安装nginx

4，	Tar解压

5，	然后输入命令:

./configure --prefix=/usr/local/nginx --add-module=../nginx_mod_h264_streaming-2.2.7 --with-openssl=/usr/bin/openssl --user=www --group=www --with-http_flv_module --with-http_stub_status_module
注意:若安装了pcre，先查找pcre的安装目录(find -name pcre) ,然后再4步骤最后添加--with-pcre=path，这里的path是安装文件解压后目录。同理其他的插件(如 openssl)。

6，	Make && make install

若：有异常如下
../nginx_mod_h264_streaming-2.2.7/src/ngx_http_streaming_module.c: 在函数‘ngx_streaming_handler’中:
../nginx_mod_h264_streaming-2.2.7/src/ngx_http_streaming_module.c:158: 错误：‘ngx_http_request_t’没有名为‘zero_in_uri’的成员
make[1]: *** [objs/addon/src/ngx_http_h264_streaming_module.o] 错误 1
修改: nginx_mod_h264_streaming-2.2.7/src文件下ngx_http_streaming_module.c，
注释掉
 if (r->zero_in_uri)   { 
    return NGX_DECLINED;  
		 }
     
7，	修改nginx.conf，详情参考压缩文件中的nginx.conf，直接用压缩文件中的nginx.conf覆盖也行。

  <注意端口、root的目录配置、MP4等视频文件访问过滤等>

8，	若64位 : ln -s /usr/local/lib/libpcre.so.1 /lib64
若32位 : ln -s /usr/local/lib/libpcre.so.1 /lib

9，	Sbin/nginx 启动

sbin/nginx -s  reload 重新启动 ，若出错pid，则输入命令

/usr/local/nginx/sbin/nginx -c /usr/local/nginx/conf/nginx.conf

若nginx: [emerg] getpwnam("www") failed，

添加用户:

/usr/sbin/groupadd -f www

/usr/sbin/useradd -g www www

10，	上传视频文件video.mp4和player.swf到/html文件夹:

	 如图目录
 
11，	客户端服务:

链接访问方式: http://IP:端口/player.swf?type=http&file= video.mp4

页面访问方式:修改index.html

 <script type="text/javascript" src="jwplayer.js"></script>

 <div id="container">Loading the player ...</div>
 
     <script type="text/javascript">
     
        jwplayer("container").setup({
        stretching: "fill",
	      flashplayer: "player.swf",
	      width: 400,
	      height:270,
	      levels: [{file: "video.mp4"}]
 });
</script>

	注意:访问端口是否在防火墙上启用


# 自动部署 

（上述流程已经在上传文件nginxinstall中处理完成，仅需P友解压nginxinstall.zip后执行nginxinstall.sh脚本即可完成安装。后续可以对nginx操作）

请注意：可以播放MP3/mp4的加密文件（以bak/dcf结尾）

1，存储（12.mp4.bak 12.mp4.dcf）用12.mp4访问 遇到dcf的访问bak文件

2，存储（12.mp3.bak 12.mp3.dcf）用12.mp3.bak访问 

请将nginx.conf中的vod 改成ondemand

# 性能测试

1、硬件配置：

服务器：8核cpu；16g内存；1000Mbps网卡；1000Mbps交换机

2、软件环境：

nginx，CentOS

3、测试内容：

nginx在大并发下的稳定表现

nginx在千兆环境下的能支持多大并发量的访问

4、视频数据

91M的mp4文件，码率：1667 KB/S

5、测试数据：

并发数量	进程cpu	空闲内存（G）	网络（Mbps）	视频播放情况

400	36%	15	766	5分钟内，5个视频中有1个视频出现2次卡顿

500	38%	14.9	877	播放5个视频，平均一分钟出现2个卡顿

600	45%	14.9	875	播放5个视频，20s内平均有3个卡顿

6、测试结论：

千兆网络下nginx最大支持400并发

# 性能测试工具

https://github.com/winlinvip/srs-bench 


 
