---
title: nginx配置笔记
date: 2020-05-18 16:03:22
tags:
- Nginx
categories:
- Nginx
- 部署相关
---

# 1 location
| 符号              | 含义                                                                                      |
| ----------------- | ----------------------------------------------------------------------------------------- |
| location = /url   | `=` 精确匹配，uri必须完全一致才能匹配成功                                                   |
| location ^~ /Purl | `^~` Puri和请求url的开头相同就匹配成功，且不再去匹配正则，也属于普通匹配                    |
| location /Purl    | `普通匹配`，Purl和用户请求url的开头相同就匹配成功，如果有多个普通匹配就匹配成功则按最长的。 |
| location ~ reg    | `~` 区分大小写的正则匹配                                                                    |
| location ~* reg   | `~*` 不区分大小写的正则匹配                                                                 |

location的匹配顺序是： `= /url` > `^~ /Purl` > `/Purl` > `~ 和 ~*`

## 示例
```
实际使用中，个人觉得每个虚拟主机下（server节点下）至少有三个匹配规则定义，如下：
#直接匹配网站根，通过域名访问网站首页比较频繁，使用这个会加速处理，官网如是说。
#这里是直接转发给后端应用服务器了，也可以是一个静态首页
# 第一个必选规则
location = / {
    proxy_pass http://tomcat:8080/index
}
# 第二个必选规则是处理静态文件请求，这是nginx作为http服务器的强项
# 有两种配置模式，目录匹配或后缀匹配,任选其一或搭配使用
location ^~ /static/ {
    root /webroot/static/;
}
location ~* \.(gif|jpg|jpeg|png|css|js|ico)$ {
    root /webroot/res/;
}
#第三个规则就是通用规则，用来转发动态请求到后端应用服务器
#非静态文件请求就默认是动态请求，自己根据实际把握
#毕竟目前的一些框架的流行，带.php,.jsp后缀的情况很少了
location / {
    proxy_pass http://tomcat:8080/
}
```