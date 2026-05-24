---
layout: post
categories: Docker
title: Docker安装OpusMtZhEn
meta: Docker安装OpusMtZhEn
---
* content
{:toc}

## 正文

切换到root账户，进入用户根目录，创建Dockerfile文件：
```
# 切换到root账户
su - root

# 进入用户根目录
cd /root

# 创建Dockerfile文件
touch Dockerfile.opus-zh-en
```

Dockerfile.opus-zh-en写入内容：
```
FROM quay.io/centos/centos:stream9-minimal

# 阿里云镜像源
RUN sed -e 's|mirror.centos.org|mirrors.aliyun.com|g' \
        -e 's|mirror.stream.centos.org|mirrors.aliyun.com|g' \
        -i /etc/yum.repos.d/centos* && \
    microdnf clean all

# 安装依赖
RUN microdnf install -y git make gcc gcc-c++ cmake python3 python3-pip && \
    microdnf clean all

# 阿里pip源
RUN pip3 config set global.index-url https://mirrors.aliyun.com/pypi/simple/

# 安装必装依赖（修复报错！）
RUN pip3 install --no-cache-dir transformers sentencepiece sacremoses torch

WORKDIR /app

# 下载模型（国内加速，加长超时时间）
RUN python3 -c "import os; os.environ['HF_ENDPOINT']='https://hf-mirror.com'; os.environ['TRANSFORMERS_TIMEOUT']='300'; from transformers import AutoTokenizer, AutoModelForSeq2SeqLM; tokenizer = AutoTokenizer.from_pretrained('Helsinki-NLP/opus-mt-zh-en'); model = AutoModelForSeq2SeqLM.from_pretrained('Helsinki-NLP/opus-mt-zh-en'); tokenizer.save_pretrained('/app/model'); model.save_pretrained('/app/model')"

RUN chmod +x /app/translate.py

CMD ["tail", "-f", "/dev/null"]
```

构建镜像和创建容器：
```
# 构建镜像
docker build -t opus-zh-en-slim -f Dockerfile.opus-zh-en .

# 创建容器
docker run -d --name opus-zh-en-slimopus-zh-en-slim
```

完善容器：
```
# 进入容器
docker exec -it opus-zh-en-slim /bin/bash

# 进入/app目录
cd /app

# 创建翻译脚本
echo '#!/usr/bin/env python3
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM
import sys
if __name__ == "__main__":
    text = sys.argv[1]
    tokenizer = AutoTokenizer.from_pretrained("/app/model")
    model = AutoModelForSeq2SeqLM.from_pretrained("/app/model")
    inputs = tokenizer(text, return_tensors="pt", padding=True)
    outputs = model.generate(**inputs)
    print(tokenizer.decode(outputs[0], skip_special_tokens=True))
' > /app/translate.py

# 退出容器
exit
```

PHP 调用：
```
$zhText = "你好";
// 翻译汉译英
$zhText = escapeshellarg($zhText);
$cmd = "docker exec opus-zh-en-slim python3 /app/translate.py {$zhText} 2>/dev/null";
$enText = trim(shell_exec($cmd));
```

清理 docker 残留缓存、悬空镜像、无用数据
```
# 清理悬空镜像
docker image prune -f

# 全局清理无用资源（容器/网络/卷/缓存）
docker system prune -f
``` 



<br/><br/><br/><br/><br/>
## 参考资料



