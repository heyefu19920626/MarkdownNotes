# Nginx 


- [Nginx](#nginx)
  - [静态资源配置](#静态资源配置)
    - [root配置](#root配置)
    - [alias配置](#alias配置)
  - [localtion以及proxy_pass以/结尾的问题](#localtion以及proxy_pass以结尾的问题)
    - [location 结尾的/](#location-结尾的)
    - [proxy_pass 结尾的/](#proxy_pass-结尾的)

## 静态资源配置

1. root响应的路径：配置的路径+完整访问路径（完整的location配置路径+静态文件）
2. alias响应的路径：配置路径+静态文件（去除location中配置的路径）

需要的是
> /mnt/upload/files/a.png  

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