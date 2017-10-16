---
title: "利用 Caddy 和 Hugo 实现博客的自动化部署"
author: "米老朱"
tags: ["Blog", "Hugo", "Caddy", "GitHub"]
date: 2016-11-02
draft: false
---

Caddy 是一个 Go 语言实现的面向 HTTP2 和 HTTPS 的服务器，与 Nginx 和 Apache 相比，是一款激进的面向未来的浏览器；Hugo 是一款由 Go 语言实现的静态网站生成器，相对 Jekyll / Hyde / Hexo 这几个流行的静态站生成器，Hugo 的功能更加强大，扩展性更优。在开启 Caddy 的 git 插件后，借助 GitHub 的 webhook 特性便可实现博客自动化部署，Go Go Go！

<!--more-->

### 安装

```bash
# 下载 Caddy 并安装，Git 插件必选
wget http://url/of/caddy.tar.gz
tar xvf /path/to/caddy.tar.gz
cp /path/to/caddy /usr/local/bin
chown root:root /usr/local/bin/caddy
chmod 755 /usr/local/bin/caddy

# 获得 80 / 443 端口访问权限
setcap 'cap_net_bind_service=+ep' /usr/local/bin/caddy

# 将 Caddy 配置成 Systemd 自启服务
mkdir /etc/caddy
mkdir /etc/ssl/caddy
mkdir -p /var/www/laozhu.me
cp caddy.service /etc/systemd/system/
chown root:root /etc/systemd/system/caddy.service
chmod 744 /etc/systemd/system/caddy.service
touch /etc/caddy/Caddyfile

# 启动 Caddy 服务
systemctl daemon-reload
systemctl start caddy.service
systemctl enable caddy.service
service caddy status

# 下载 Hugo 并安装
wget http://url/of/hugo.deb
dpkg -i hugo.deb
hugo version
```

### 配置

![Deploy Workflow](/media/posts/deploy-blog-with-caddy-and-hugo/01.png)

上图为自动化部署的实现原理图，首先创建必须的文件夹，其中 laozhu.me 用于存放静态站的生成文件，repo 用于从 GitHub 拉取代码。

```bash
mkdir -p /var/www/{laozhu.me,repo}
```

将本地的 Hugo 博客推到 GitHub 代码仓库，并为他新建一个 webhook 及其密码，以下为本站 Caddyfile 的配置文件，仅供参考。

各位可自备梯子查看 YouTube 上的视频教程：[Caddy server Git add-on tutorial](https://www.youtube.com/watch?v=dmat1MUT0fc)

```caddy
laozhu.me {
  gzip
  minify
  root /var/www/laozhu.me
  log /var/log/caddy/access.log
  git {
    repo https://github.com/laozhu/my-blog
    path ../repo/laozhu.me
    hook /webhook webhook-secret-password
    then hugo --destination=/var/www/laozhu.me
    interval 3600
  }
  errors {
    log /var/log/caddy/error.log
    404 404.html # Not Found
  }
}
```

OK，现在重启你的 Caddy 服务器，当你写了一篇新的文章，并且 push 到 GitHub 的时候，Caddy 会被通知，并主动去 GitHub 拉取最新的博客内容，然后再调用 Hugo 命令行生成新的静态站文件到 `/var/www/laozhu.me`，如果你的 HTML 文件没有设置缓存，那通过浏览器就可以访问了。
