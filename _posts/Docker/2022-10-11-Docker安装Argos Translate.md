---
layout: post
categories: Docker
title: Docker安装Argos Translate
meta: Docker安装Argos Translate
---
* content
{:toc}

## 正文

Argos Translate 使用 OpenNMT 进行翻译，可以作为 Python 库、命令行工具或 GUI 应用程序使用。Argos Translate 支持安装语言模型包，这些包是带有 ".argosmodel" 扩展名的 zip 存档，包含翻译所需的数据。LibreTranslate 是一个基于 Argos Translate 构建的 API 和 Web 应用程序。

Github 地址：https://github.com/argosopentech/argos-translate#installation



在 /root 管理员根目录下创建一个Dockerfile文件。

Dockerfile.argos 文件内容：

```
FROM quay.io/centos/centos:stream9-minimal

# 替换为阿里云镜像源（加速安装）
RUN sed -e 's|mirror.centos.org|mirrors.aliyun.com|g' \
        -e 's|mirror.stream.centos.org|mirrors.aliyun.com|g' \
        -i /etc/yum.repos.d/centos* && \
    microdnf clean all

RUN microdnf install -y python3.12 python3.12-pip && \
    microdnf clean all

WORKDIR /app

# 关键！pip 强制阿里云源（解决超时！）
RUN pip3.12 config set global.index-url https://mirrors.aliyun.com/pypi/simple/
RUN pip3.12 config set global.trusted-host mirrors.aliyun.com
RUN pip3.12 config set global.timeout 300

# 直接全局安装，不用虚拟环境
RUN pip3.12 install --no-cache-dir argostranslate -i https://mirrors.aliyun.com/pypi/simple/

# 预装中英译模型，也可以等容器启动后再执行
RUN argospm update
RUN argospm install translate-zh_en

# 容器保持运行
CMD ["tail", "-f", "/dev/null"]
```

构建镜像：
```
> docker build -t argos-slim -f Dockerfile.argos .
```

启动容器（挂载目录）：
```
docker run -d \
  --name argos-slim \
  argos-slim
```

进入容器：
```
docker exec -it argos-slim bash
```



在 PHP 代码中执行：
```
$cmd = "docker exec argos-slim argos-translate --from-lang zh --to-lang en '你好'";
$output = shell_exec($cmd);
```

argos-translate --from-lang zh --to-lang en "你好"




<br/><br/><br/><br/><br/>
## 参考资料



