---
title: Windows 下为各类 CLI 设置代理
date: 2020/9/26
updated: 2020/9/26
category: 
- proxy
tag: 
- proxy
---

该解决方案是 GitHub CLI 的 TLS handshake timeout issue 下，一位中国老哥给的。

根据[这位老哥给的solution](https://github.com/cli/cli/issues/514#issuecomment-666230820)，只需在终端的配置里写上如下函数：

```bash
proxy ()
{
    export http_proxy="http://127.0.0.1:port";
    export no_proxy="localhost,127.0.0.0/0,192.168.0.0/16,10.0.0.0/0,kubernetes.docker.internal";
    export NO_PROXY=$no_proxy;
    export HTTP_PROXY=$http_proxy https_proxy=$http_proxy HTTPS_PROXY=$http_proxy NO_PROXY=$no_proxy;
    echo "HTTP Proxy on";
    env | grep --color=auto -i proxy
}
noproxy ()
{
    unset http_proxy https_proxy HTTP_PROXY HTTPS_PROXY no_proxy NO_PROXY FTP_PROXY ftp_proxy ALL_PROXY all_proxy;
    env | grep --color=auto -i proxy;
    echo -e "Proxy environment variable removed."
}
```

每次启动的时候都加载这两个函数就可以了。

> no need to set proxy in git config, a proxy() and a noproxy() function in your bash_rc or bash_profile would be easier if you're a developer in china. most cli(eg. git,curl,wget,go,brew ...) respects http_proxy, https_proxy, HTTP_PROXY, HTTPS_PROXY.
