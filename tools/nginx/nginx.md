# Nginx 


- [Nginx](#nginx)
  - [基本命令](#基本命令)
  - [静态资源配置](#静态资源配置)
  - [显示目录](#显示目录)
  - [证书转化](#证书转化)
    - [root配置](#root配置)
    - [alias配置](#alias配置)
    - [413 Request Entity Too Large](#413-request-entity-too-large)
  - [localtion以及proxy_pass以/结尾的问题](#localtion以及proxy_pass以结尾的问题)
    - [location 结尾的/](#location-结尾的)
    - [proxy_pass 结尾的/](#proxy_pass-结尾的)
  - [upstream](#upstream)
  - [log](#log)
    - [日志参数说明](#日志参数说明)

## 基本命令
1. 启动`start nginx`
2. 重新加载配置文件`nginx -s reload`
3. 关闭`nginx -s stop`或者`nginx -s quit`
4. 查看配置信息`nginx -V`
   1. 查看版本`nginx -v`
5. 配置语法检查`nginx -c ./conf/jason.conf -t`
   1. `nginx -t`检查配置文件
   2. `nginx -c`设置配置文件的路径
5. 重新打开日志文件`nginx -s reopen`
   1. `nginx -s`向主进程发送信号

## 静态资源配置

1. root响应的路径：配置的路径+完整访问路径（完整的location配置路径+静态文件）
2. alias响应的路径：配置路径+静态文件（去除location中配置的路径）

需要的是
> /mnt/upload/files/a.png  

## 显示目录

在location中添加如下  
```bash
        location /pdf {
            autoindex on; #开启目录浏览功能；
            autoindex_exact_size off; #关闭详细文件大小统计，让文件大小显示MB，GB单位，默认为b；
            autoindex_localtime on; #开启以服务器本地时区显示文件修改日期！
            charset utf-8,gbk; # 避免中文乱码
            alias /Users/tangan/codes/books;
        }
```

## 证书转化

将.crt和.key或者.pem和.key转化为jks  
使用openssl在linux下转化
1. 转化为pkcs12：`openssl pkcs12 -export -in server.crt -inkey server.key -out mycert.p12 -name abc -CAfile myCA.crt`
   1. CAfile可以不要
   2. -name为别名，也可不要
   3. 之后输入密码
   4. crt文件可以换为pem文件
   5. -out为输出的文件
2. keytool -importkeystore -v -srckeystore mycert.p12 -srcstoretype pkcs12 -srcstorepass a123456 -destkeystore Aserver.keystore -deststoretype jks -deststorepass b123456
   1. srcstorepass为pkcs12的密码
   2. destkeystore为jks文件
   3. deststorepass为jks密码


### root配置

```bash
location /images/ {  
     root /mnt/upload/files;  
}
```

1. root配置会在配置的目录后跟上URL，组成对应的文件路径，即想访问的地址是
> https://blog.yoodb.com/images/a.png  
2. nginx根据配置走的文件路径是
>/mnt/upload/files/images/a.png  

### alias配置

```bash
location /images/ {  
     alias /mnt/upload/files/;  
}
```

###  413 Request Entity Too Large

nginx服务器限制了上传文件的大小  

1. 在http{ }中设置：client_max_body_size 20m;
    1. http{} ——》控制所有nginx收到的请求报文大小
2. 在server{ }中设置：client_max_body_size 20m;
    2. server{}——》控制该server收到的请求报文大小
3. 在location{ }中设置：client_max_body_size 20m;
    3. location{}——》控制匹配了location 路由规则的请求报文大小

## localtion以及proxy_pass以/结尾的问题

### location 结尾的/

1. 在location中匹配的url最后有无/结尾，指的是模糊匹配与精确匹配的问题
   1. 没有"/"结尾时，location /abc/def 可以匹配/abc/defghi的请求，也可以匹配/abc/def/ghi ...... 
   2. 有"/"结尾时,location /abc/def/ 不能匹配/abc/defghi的请求，只能精确匹配 /abc/def/ghi这样的请求

### proxy_pass 结尾的/

1. 在proxy_pass中代理的url最后有无/结尾(不能作为判断依据)，指的是在proxy_pass 指定的url后要不要替换掉location里面匹配到的字符串
2. <font color="red">单纯从proxy_pass的问题上来说, 不能以有没有/结尾来判断, 而是以有没有uri来判断.</font>
3. <font color="red">只要在域名:端口 后面加上了任何以/开头的字符串, 就被视为有uri, 规则就会发生改变.</font>有uri就会把请求的uri拼到proxy_pass的url后面, 然后整个替换掉location里面匹配的字符串


访问http://localhost:8090/proxy/login.html:
```bash
listen 8090;
server localhost;

#情况1
location /proxy/ {
    proxy_pass http://myblog.com:8000/;
}
# proxy_pass的最终地址就是: http://myblog.com:8000/login.html  
# 因为proxy_pass 在端口号后面有以/开头的uri，代表绝对路径，所以会忽略匹配到的/proxy/, 直接将/proxy/ 整个从url里面删除.

#情况2
location /proxy/ {
    proxy_pass http://myblog.com:8000;
}
#proxy_pass 代理到 http://myblog.com:8000/proxy/login.html

#情况3
location /proxy/ {
    proxy_pass http://myblog.com:8000/disquz/;
}
#proxy_pass 代理到http://myblog.com:8000/disquz/login.html

#情况4
location /proxy/ {
    proxy_pass http://myblog.com:8000/disquz;
}
# proxy_pass 代理到http://myblog.com:8000/disquzlogin.html  
# 因为在端口号后面有/disquz 以/开头的uri, 所以会将/proxy/完全替换, 故/proxy/login.html 只剩下login.html 拼在url后面就会成为http://myblog.com:8000/disquzlogin.html

#情况5
location /proxy {
    proxy_pass http://myblog.com:8000/disquz/;
}
# proxy_pass 代理到http://myblog.com:8000/disquz//login.html  
# 因为匹配到了这个规则 所以把uri里面的/proxy去掉 剩下/login.html, 拼在url后面就是http://myblog.com:8000/disquz//login.html
```

## upstream


```bash
upstream test_8040{
    192.0.0.1:8040
}

upstream test_8050{
    192.0.0.2:8050
}

server{
    listen 8040;
    listen 8050;

location / {
    proxy http://test_$server_port
    # $server_port会被listen的port替换，然后test_$server_port会被upstream对应的替换
    # 访问http://localhost:8040转发到192.0.0.1:8040
    # 访问http://localhost:8050转发到192.0.0.2:8050
}
}

```

## log

1. 语法
    1. path 指定日志的存放位置。
    2. format 指定日志的格式。默认使用预定义的combined。
    3. buffer 用来指定日志写入时的缓存大小。默认是64k。
    4. gzip 日志写入前先进行压缩。压缩率可以指定，从1到9数值越大压缩比越高，同时压缩的速度也越慢。默认是1。
    5. flush 设置缓存的有效时间。如果超过flush指定的时间，缓存中的内容将被清空。
    6. if 条件判断。如果指定的条件计算为0或空字符串，那么该请求不会写入日志。
```bash
access_log path [format [buffer=size] [gzip[=level]] [flush=time] [if=condition]]; # 设置访问日志
access_log off; # 关闭访问日志

# 默认的日志格式
log_format combined '$remote_addr - $remote_user [$time_local] '
                    '"$request" $status $body_bytes_sent '
                    '"$http_referer" "$http_user_agent"';
```
2. 作用域
   1. 可以应用access_log指令的作用域分别有http，server，location，limit_except
3. 日志格式
   1. Nginx预定义了日志格式,如果没有明确指定日志格式默认使用该格式
   2. 日志格式语法为`log_format name [escape=default|json] string ...;`
      1. name 格式名称。在access_log指令中引用。
      2. escape 设置变量中的字符编码方式是json还是default，默认是default。
      3. string 要定义的日志格式内容。该参数可以有多个。参数中可以使用Nginx变量
   3. 需要开启日志，并指定日志格式名称

### 日志参数说明

```bash
$args                    #请求中的参数值
$query_string            #同 $args
$arg_NAME                #GET请求中NAME的值
$is_args                 #如果请求中有参数，值为"?"，否则为空字符串
$uri                     #请求中的当前URI(不带请求参数，参数位于$args)，可以不同于浏览器传递的$request_uri的值，它可以通过内部重定向，或者使用index指令进行修改，$uri不包含主机名，如"/foo/bar.html"。
$document_uri            #同 $uri
$document_root           #当前请求的文档根目录或别名
$host                    #优先级：HTTP请求行的主机名>"HOST"请求头字段>符合请求的服务器名.请求中的主机头字段，如果请求中的主机头不可用，则为服务器处理请求的服务器名称
$hostname                #主机名
$https                   #如果开启了SSL安全模式，值为"on"，否则为空字符串。
$binary_remote_addr      #客户端地址的二进制形式，固定长度为4个字节
$body_bytes_sent         #传输给客户端的字节数，响应头不计算在内；这个变量和Apache的mod_log_config模块中的"%B"参数保持兼容
$bytes_sent              #传输给客户端的字节数
$connection              #TCP连接的序列号
$connection_requests     #TCP连接当前的请求数量
$content_length          #"Content-Length" 请求头字段
$content_type            #"Content-Type" 请求头字段
$cookie_name             #cookie名称
$limit_rate              #用于设置响应的速度限制
$msec                    #当前的Unix时间戳
$nginx_version           #nginx版本
$pid                     #工作进程的PID
$pipe                    #如果请求来自管道通信，值为"p"，否则为"."
$proxy_protocol_addr     #获取代理访问服务器的客户端地址，如果是直接访问，该值为空字符串
$realpath_root           #当前请求的文档根目录或别名的真实路径，会将所有符号连接转换为真实路径
$remote_addr             #客户端地址
$remote_port             #客户端端口
$remote_user             #用于HTTP基础认证服务的用户名
$request                 #代表客户端的请求地址
$request_body            #客户端的请求主体：此变量可在location中使用，将请求主体通过proxy_pass，fastcgi_pass，uwsgi_pass和scgi_pass传递给下一级的代理服务器
$request_body_file       #将客户端请求主体保存在临时文件中。文件处理结束后，此文件需删除。如果需要之一开启此功能，需要设置client_body_in_file_only。如果将次文件传 递给后端的代理服务器，需要禁用request body，即设置proxy_pass_request_body off，fastcgi_pass_request_body off，uwsgi_pass_request_body off，or scgi_pass_request_body off
$request_completion      #如果请求成功，值为"OK"，如果请求未完成或者请求不是一个范围请求的最后一部分，则为空
$request_filename        #当前连接请求的文件路径，由root或alias指令与URI请求生成
$request_length          #请求的长度 (包括请求的地址，http请求头和请求主体)
$request_method          #HTTP请求方法，通常为"GET"或"POST"
$request_time            #处理客户端请求使用的时间,单位为秒，精度毫秒； 从读入客户端的第一个字节开始，直到把最后一个字符发送给客户端后进行日志写入为止。
$request_uri             #这个变量等于包含一些客户端请求参数的原始URI，它无法修改，请查看$uri更改或重写URI，不包含主机名，例如："/cnphp/test.php?arg=freemouse"
$scheme                  #请求使用的Web协议，"http" 或 "https"
$server_addr             #服务器端地址，需要注意的是：为了避免访问linux系统内核，应将ip地址提前设置在配置文件中
$server_name             #服务器名
$server_port             #服务器端口
$server_protocol         #服务器的HTTP版本，通常为 "HTTP/1.0" 或 "HTTP/1.1"
$status                  #HTTP响应代码
$time_iso8601            #服务器时间的ISO 8610格式
$time_local              #服务器时间（LOG Format 格式）
$cookie_NAME             #客户端请求Header头中的cookie变量，前缀"$cookie_"加上cookie名称的变量，该变量的值即为cookie名称的值
$http_NAME               #匹配任意请求头字段；变量名中的后半部分NAME可以替换成任意请求头字段，如在配置文件中需要获取http请求头："Accept-Language"，$http_accept_language即可
$http_cookie
$http_host               #请求地址，即浏览器中你输入的地址（IP或域名）
$http_referer            #url跳转来源,用来记录从那个页面链接访问过来的
$http_user_agent         #用户终端浏览器等信息
$http_x_forwarded_for
$sent_http_NAME          #可以设置任意http响应头字段；变量名中的后半部分NAME可以替换成任意响应头字段，如需要设置响应头Content-length，$sent_http_content_length即可
$sent_http_cache_control
$sent_http_connection
$sent_http_content_type
$sent_http_keep_alive
$sent_http_last_modified
$sent_http_location
$sent_http_transfer_encoding
```