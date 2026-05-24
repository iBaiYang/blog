---
layout: post
categories: Docker
title: Docker安装opus-mt-zh-en
meta: Docker安装opus-mt-zh-en
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

# 安装必装依赖
RUN pip3 install --no-cache-dir transformers sentencepiece sacremoses torch

WORKDIR /app

# 下载模型（国内加速，加长超时时间）
RUN python3 -c "import os; os.environ['HF_ENDPOINT']='https://hf-mirror.com'; os.environ['TRANSFORMERS_TIMEOUT']='300'; from transformers import AutoTokenizer, AutoModelForSeq2SeqLM; tokenizer = AutoTokenizer.from_pretrained('Helsinki-NLP/opus-mt-zh-en'); model = AutoModelForSeq2SeqLM.from_pretrained('Helsinki-NLP/opus-mt-zh-en'); tokenizer.save_pretrained('/app/model'); model.save_pretrained('/app/model')"

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

修改执行权限：
chmod +x /app/translate.py

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

### 减少占用空间

上面容器占用的空间有8G左右，有些大，加上quantization='int8'（这次没实际操作去验证）：
```
# 下载模型（hf-mirror 加速）
RUN python3 -c "import os; os.environ['HF_ENDPOINT']='https://hf-mirror.com'; os.environ['TRANSFORMERS_TIMEOUT']='300'; from transformers import AutoTokenizer, AutoModelForSeq2SeqLM; tokenizer = AutoTokenizer.from_pretrained('Helsinki-NLP/opus-mt-zh-en'); model = AutoModelForSeq2SeqLM.from_pretrained('Helsinki-NLP/opus-mt-zh-en'); tokenizer.save_pretrained('/app/model'); model.save_pretrained('/app/model'),quantization='int8'"
```

还有一种如下Dockerfile来减少占用空间（未验证）：
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

# 安装轻量引擎（不需要 torch，torch会占用1.5G左右！）
RUN pip3 install --no-cache-dir ctranslate2==4.4.2 sentencepiece==0.2.0

WORKDIR /app

# 直接下载已转换好的模型（不使用 transformers）
RUN mkdir -p /app/model && \
    cd /app/model && \
    curl -L -O https://hf-mirror.com/Helsinki-NLP/opus-mt-zh-en/resolve/main/source.spm && \
    curl -L -O https://hf-mirror.com/Helsinki-NLP/opus-mt-zh-en/resolve/main/target.spm


# 在线转换模型（不需要 torch！）
RUN python3 -c "
import os
os.environ['HF_ENDPOINT'] = 'https://hf-mirror.com'
import ctranslate2
ctranslate2.converters.OpusMTConverter().convert(
    model_name='Helsinki-NLP/opus-mt-zh-en',
    output_dir='/app/model',
    quantization='int8'
)"

CMD ["tail", "-f", "/dev/null"]
```

翻译脚本：
```
echo '#!/usr/bin/env python3
import ctranslate2
import sentencepiece as spm
import sys
if __name__ == "__main__":
    text = sys.argv[1]
    sp = spm.SentencePieceProcessor(model_file="/app/model/source.spm")
    translator = ctranslate2.Translator("/app/model", device="cpu")
    tokens = sp.encode(text, out_type=str)
    res = translator.translate_batch([tokens])
    output = sp.decode(res[0].hypotheses[0])
    print(output)
' > /app/translate.py
```

修改执行权限：
```
chmod +x /app/translate.py
```

### 其他

补充几个命令：
```
# 停止容器
docker stop opus-zh-en-slim

# 删除容器
docker rm -f opus-zh-en-slim

# 删除镜像
docker rmi -f opus-zh-en-slim

# 清理悬空镜像
docker image prune -f

# 全局清理无用资源（容器/网络/卷/缓存）
docker system prune -f
```







<br/><br/><br/><br/><br/>
## 参考资料



