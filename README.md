Fastdfs nginx server
=============
With some nginx modules in docker

Install
-------------
* [libfastcommon-1.0.35](https://github.com/happyfish100/libfastcommon)
* [fastdfs-master (20170712)](https://github.com/happyfish100/fastdfs)
* [nginx-1.12.2](http://nginx.org/)
* [fastdfs-nginx-module-master (20170710)](https://github.com/happyfish100/fastdfs-nginx-module)
* [echo-nginx-module-master (20170710)](https://github.com/openresty/echo-nginx-module)
* [nginx-eval-module-master (20170712)](https://github.com/vkholodkov/nginx-eval-module)
* [ngx_http_redis-0.3.8](https://www.nginx.com/resources/wiki/modules/redis)

Conf
-------------
* storage.conf
``````
## can not use 127.0.0.1
tracker_server=host-ip:22122
``````
* mod_fastdfs.conf
``````
tracker_server=127.0.0.1:22122
``````
* nginx.conf
``````
## if need check token from redis
upstream redisbackend {
  server    redis-server-ip:port;
  keepalive 1024;
}
``````
* other conf files if you needed

Build
-------------
``````
## you can use your image name to replace name 'fastdfs-nginx'
docker build -t fastdfs-nginx .
``````

Network
-------------
``````
## you can create your network for this server, like:
docker network create --driver bridge --subnet 192.168.1.0/20 network0
``````

Run
-------------
``````
## should use host network (test pass)
docker run -itd \
  --name fastdfs-nginx \
  --network=host \
  -p 80:80 \
  -p 22122:22122 \
  -p 23000:23000 \
  -v /etc/localtime:/etc/localtime:ro \
  -v /data/fdfs/logs/:/data/fdfs/logs/ \
  -v /data/fdfs/data/:/data/fdfs/data/ \
  -v /var/log/nginx/:/var/log/nginx/ \
  -v /u01/vs/demo_bbc/:/u01/vs/demo_bbc/ \
  -v /mnt/vdb/:/mnt/vdb/ \
  fastdfs-nginx \
  sh -c "/usr/bin/fdfs_trackerd /etc/fdfs/tracker.conf restart && /usr/bin/fdfs_storaged /etc/fdfs/storage.conf restart && /usr/sbin/nginx -g 'daemon off;'"
``````
