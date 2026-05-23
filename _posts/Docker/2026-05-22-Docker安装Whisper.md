---
layout: post
categories: Docker
title: Docker安装Whisper
meta: Docker安装Whisper
---
* content
{:toc}

## 正文

Whisper 是一个基于 ggml-org 项目的免费的离线音频转录模型。Github 地址：https://github.com/ggml-org/whisper.cpp

Whisper.cpp 是纯离线、无 Python 依赖、低资源、嵌入式、高并发、隐私优先、免费开源的软件，适配所有 Linux 系统（Ubuntu/CentOS/Debian/ 服务器）。


安装中对系统环境有要求，最好在docker中安装。

安装 docker：
```
# 切换到root身份
su - root

# 执行安装脚本
curl -fsSL https://get.docker.com | bash -s docker --mirror Aliyun

# 启动并设置开机自启
systemctl start docker
systemctl enable docker

# 验证安装成功
docker --version
```

在 /root 管理员根目录下创建一个Dockerfile文件。

Dockerfile.whisper 文件内容：

```
FROM quay.io/centos/centos:stream9-minimal

# 替换为阿里云镜像源（加速安装）
RUN sed -e 's|mirror.centos.org|mirrors.aliyun.com|g' \
        -e 's|mirror.stream.centos.org|mirrors.aliyun.com|g' \
        -i /etc/yum.repos.d/centos* && \
    microdnf clean all

# 安装依赖（包含ffmpeg）
RUN microdnf install -y git make gcc gcc-c++ cmake && \
    microdnf clean all

WORKDIR /app

# 下载源码（gitclone.com 加速，解决阿里云下载失败）
RUN git clone https://gitee.com/mirrors_mccormicka/whisper.cpp.git

WORKDIR /app/whisper.cpp

# 编译
RUN cmake -B build
RUN cmake --build build -j --config Release

# 让容器保持后台运行（PHP 可以随时 exec 调用）
CMD ["tail", "-f", "/dev/null"]
```

构建镜像：
```
> docker build -t whisper-slim -f Dockerfile.whisper .
```

启动容器（挂载目录）：
```
docker run -d \
  --name whisper-srv \
  -v /www/wwwroot:/www/wwwroot \
  whisper-slim
```

进入容器，用国内镜像下载模型：
```
docker exec -it whisper-srv bash

cd /app/whisper.cpp

# 下载原始模型
curl -L https://hf-mirror.com/ggerganov/whisper.cpp/resolve/main/ggml-base.bin -o models/ggml-base.bin

# 量化为 q5_0（更小更快）
./build/bin/quantize models/ggml-base.bin models/ggml-base-q5_0.bin q5_0

# 下载完成后退出
exit
```

注意，whisper-cli 目前仅支持 16 位 WAV 文件，因此请确保在运行工具之前转换输入。例如，使用 ffmpeg 如此操作：
```
ffmpeg -i /www/wwwroot/upload/input.mp3 -ar 16000 -ac 1 -c:a pcm_s16le /www/wwwroot/upload/output.wav
```

用到了 ffmpeg ，宿主机安装 ffmpeg：
```
# 1. 安装 epel 源
yum install -y epel-release

# 2. RPM Fusion 源
yum install -y https://mirrors.aliyun.com/rpmfusion/free/el/rpmfusion-free-release-7.noarch.rpm

# 3. 安装 ffmpeg（完整版）
yum install -y ffmpeg ffmpeg-devel

# 4. 验证是否安装成功
ffmpeg -version
```



在 PHP 代码中执行：
```
$cmd = "docker exec whisper-srv /app/whisper.cpp/build/bin/whisper-cli -m /app/whisper.cpp/models/ggml-base-q5_0.bin -l zh -nt -f /www/wwwroot/upload/output.wav";

$output = shell_exec($cmd);
```

$cmd 部分参数解说：
```
docker exec whisper-srv        # 进入容器执行
/app/whisper.cpp/build/bin/whisper-cli   # 识别程序路径
-m /app/whisper.cpp/models/ggml-base-q5_0.bin   # 模型（必须指定）
-l zh                          # 语言=中文
-f 音频文件路径                 # 要识别的音频（必须是挂载目录里的）
-nt                            # 输出纯文本（方便PHP接收）
```





<br/><br/><br/><br/><br/>
## 参考资料

<https://zhuanlan.zhihu.com/p/1934266877867198244>

<https://zhuanlan.zhihu.com/p/1966238230786598191>


