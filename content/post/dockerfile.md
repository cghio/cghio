+++
title = "Dockerfile snippets"
tags = [ "docker" ]
+++

检测位置：

```sh
python2.7 -c 'from urllib import urlopen; from json import loads; \
print(loads(urlopen("http://ip-api.com/json").read().decode("utf-8" \
).strip())["countryCode"])' > /tmp/country
```

或：

```sh
python3 -c 'from urllib.request import urlopen; from json import loads; \
print(loads(urlopen("http://ip-api.com/json").read().decode("utf-8" \
).strip())["countryCode"])' > /tmp/country
```

Ubuntu 更改源：

```sh
sed -i 's/archive.ubuntu.com/mirrors.aliyun.com/g' /etc/apt/sources.list
```

Debian 更改源：

```sh
(echo "deb http://mirrors.aliyun.com/debian jessie main" && \
echo "deb http://mirrors.aliyun.com/debian jessie-updates main" && \
echo "deb http://mirrors.aliyun.com/debian-security/ jessie/updates main") \
> /etc/apt/sources.list
```

Node.js 源：

```sh
(echo "registry = https://registry.npm.taobao.org" && \
echo "disturl = https://npm.taobao.org/dist" && \
echo "phantomjs_cdnurl = https://npm.taobao.org/dist/phantomjs") \
> ~/.npmrc
```

Bundler 源：

```sh
gem sources -r https://rubygems.org/
gem sources -a https://ruby.taobao.org/
bundle config mirror.https://rubygems.org https://ruby.taobao.org
```

更改 locale ：

```Dockerfile
RUN apt-get update && apt-get install -y --no-install-recommends locales locales-all

RUN export LANGUAGE=en_US.UTF-8 && \
    export LANG=en_US.UTF-8 && \
    export LC_ALL=en_US.UTF-8 && \
    locale-gen en_US.UTF-8 && \
    DEBIAN_FRONTEND=noninteractive dpkg-reconfigure locales

ENV LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
```

更改时区：

```sh
echo Asia/Hong_Kong > /etc/timezone && dpkg-reconfigure -f noninteractive tzdata
```
