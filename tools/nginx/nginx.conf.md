worker_processes  auto;
worker_rlimit_nofile 65535;

events
{
    worker_connections  20480;
    multi_accept on;
    use epoll;
}

http
{
    add_header Access-Control-Allow-Origin *;
    add_header Access-Control-Allow-Headers X-Requested-With;
    add_header Access-Control-Allow-Methods GET,POST;

    log_format main '"$time_local","$remote_addr","$status","$host","$request","$http_user_agent","$bytes_sent","$request_time"';
    include       mime.types;
    default_type  application/octet-stream;
#    access_log logs/access.log main;
#    error_log logs/error.log;
    access_log off;
    error_log /dev/null;
    sendfile      on;
    keepalive_timeout     15;
    client_max_body_size  512m;

    #缓存设置说明
    #keys_zone：反向代理缓存的名称和内存占用。平均每个图片20KB/每个索引200字节=100倍，因此内存占用为缓存空间的1%。
    #inactive：连续此时间内没人访问，那么缓存文件将被删除。每次被访问都将重新计时。建议1小时。s秒，m分，h时，d天。
    #max_size：缓存占用磁盘的最大空间。如果缓存文件达到该数值，则会清除缓存最早的资源。服务器内存分区4GB，缓存建议3GB。
    proxy_cache_path /dev/shm  levels=1 keys_zone=cache_img:30m  inactive=1h max_size=3g use_temp_path=off;

    # 反向代理使用 $host 时，需要使用如下解析DNS
    resolver 119.29.29.29;

    server {
        listen 80;
        location / {

        set  $cache_name off;

        if ($host ~ ^.*gtimeg.com$){
        set $cache_name cache_img;
        }
            proxy_pass http://$host;
            proxy_cache $cache_name;
            proxy_cache_valid 200 1h;
            proxy_cache_valid any 10s;
            proxy_cache_key $uri;
        }

        # 图片使用缓存
        location ~* \.(jpg|jpeg|png|gif|webp|ico|bmp)$ {
            proxy_pass http://$host;
            proxy_cache cache_img;
            proxy_cache_valid 200 1h;
            proxy_cache_valid any 10s;
            proxy_cache_key $uri;
        }

        location ~* (.*?_pic.*|.*\d+/\d+/\d+.*|.*_ori.*|.*_img.*|.*tv.*){
            proxy_pass http://$host;
            proxy_cache cache_img;
            proxy_cache_valid 200 1h;
            proxy_cache_valid any 10s;
            proxy_cache_key $uri;
        }
    }

    server {
        listen 443;
        ssl on;
        ssl_certificate /usr/local/nginx/conf/inspur.crt;
        ssl_certificate_key /usr/local/nginx/conf/inspur_nopass.key;
        location / {
            proxy_pass https://$host;
            proxy_cache cache_img;
            proxy_cache_valid 200 1h;
            proxy_cache_valid any 10s;
            proxy_cache_key $uri;
        }

        # 图片使用缓存
        location ~* \.(jpg|jpeg|png|gif|webp|ico|bmp)$ {
            proxy_pass http://$host;
            proxy_cache cache_img;
            proxy_cache_valid 200 1h;
            proxy_cache_valid any 10s;
            proxy_cache_key $uri;
        }

        location ~* (.*?_pic.*|.*\d+/\d+/\d+.*|.*_ori.*|.*_img.*|.*tv.*){
            proxy_pass http://$host;
            proxy_cache cache_img;
            proxy_cache_valid 200 1h;
            proxy_cache_valid any 10s;
            proxy_cache_key $uri;
        }
    }
}
