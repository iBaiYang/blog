---
layout: post
categories: Docker
title: Docker安装opus-mt-zh-en
meta: Docker安装opus-mt-zh-en
---
* content
{:toc}

## 引言

opus-mt-zh-en 是一个用于中文到英文的机器翻译模型。 
地址：<https://huggingface.co> 的国内镜像 <https://hf-mirror.com/> 下查看 Helsinki-NLP/opus-mt-zh-en 模型：
<https://hf-mirror.com/Helsinki-NLP/opus-mt-zh-en>，还有源码：<https://hf-mirror.com/Helsinki-NLP/opus-mt-zh-en/tree/main>


下面先把 Docker 用法讲一下，再讲opus-mt-zh-en。

## Docker 常用命令

补充几个命令：
```
# 查看所有镜像
docker images

# 删除指定镜像
docker rmi <IMAGE_ID>

# 强制删除（正在被容器使用也删）
docker rmi -f <IMAGE_ID>

# 删除多个
docker rmi <ID1> <ID2> <ID3>

# 查看运行中容器大小
docker ps -s

# 停止容器
docker stop opus-zh-en-slim

# 删除容器
docker rm -f opus-zh-en-slim

# 删除镜像
docker rmi -f opus-zh-en-slim

# 清理悬空镜像
docker image prune -f

# 查看磁盘占用
docker system df

# 明细：逐个容器、镜像、卷体积
docker system df -v

# 清理无用镜像+构建缓存（谨慎！）
docker system prune

# 全局清理无用资源（容器/网络/卷/缓存）（-f 不确认，直接删）
docker system prune -f

# 清理所有未使用的镜像、容器、网络、缓存（-a 连同未使用的镜像一起删（不只是悬空镜像））
docker system prune -a

# 删除停止的容器
docker container prune

# 查看构建缓存占用
docker builder du

# 清理构建缓存（能省 几个 GB）
docker builder prune -a -f
```


## Docker 安装opus-mt-zh-en

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
docker run -d --name opus-zh-en-slim opus-zh-en-slim
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

## HuggingFace 模型安装

现在存在的问题是服务器上下载模型文件的速度很慢或者无法下载，我们可以在本地电脑下载模型文件(https://hf-mirror.com/Helsinki-NLP/opus-mt-zh-en/tree/main下的所有文件)，然后上传到服务器上。

在root家目录下创建文件夹opus-mt-zh-en，并把下载的源码上传到这里：
```
mkdir -p /root/opus-mt-zh-en
```

### 使用 transformers 

不进行量化的写法。

requirements.txt文件：
```
transformers
torch
sentencepiece
sacremoses
```

transformers 源码中用 MarianTokenizer 做标点归一化用，MarianTokenizer初始化时会自动尝试导入 sacremoses。

translate.py文件：
```
#!/usr/bin/env python3
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM
import sys

if __name__ == "__main__":
    text = sys.argv[1]
    tokenizer = AutoTokenizer.from_pretrained("/app/opus-mt-zh-en")
    model = AutoModelForSeq2SeqLM.from_pretrained("/app/opus-mt-zh-en")
    inputs = tokenizer(text, return_tensors="pt", padding=True)
    outputs = model.generate(**inputs)
    print(tokenizer.decode(outputs[0], skip_special_tokens=True))
```

Dockerfile文件：
```
FROM quay.io/centos/centos:stream9-minimal

# 替换阿里云yum源
RUN sed -e 's|mirror.centos.org|mirrors.aliyun.com|g' \
        -e 's|mirror.stream.centos.org|mirrors.aliyun.com|g' \
        -i /etc/yum.repos.d/centos* && \
    microdnf clean all && microdnf makecache

# 仅装python基础，不装gcc、不装torch相关
RUN microdnf install -y python3 python3-pip \
    && microdnf clean all && rm -rf /var/cache/dnf

WORKDIR /app

# 安装运行&转换依赖：无torch、无transformers！
COPY requirements.txt .
RUN pip3 install --no-cache-dir -r requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

COPY opus-mt-zh-en ./opus-mt-zh-en

COPY translate.py .
RUN chmod +x /app/translate.py

CMD ["tail", "-f", "/dev/null"]
```

命令：
```
# 构建
docker build -t opus-zh-en .

# 启动
docker run -d --name translate opus-zh-en
```

构建后大概在8.5G左右。

执行翻译脚本：
```
[root@localhost ~]# docker exec translate python3 /app/translate.py '你好'
Hello.
[root@localhost ~]# 
```

可以正常使用。

### transformers 动态量化

执行时，内存超限，被kill了。

```
cat > quantize_dynamic_int8.py << 'EOF'
import torch
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM

model_dir = "/app/opus-mt-zh-en"
out_dir = "/app/opus-mt-zh-en-dynamic-int8"

# 加载原始 FP32 模型
tokenizer = AutoTokenizer.from_pretrained(model_dir)
model = AutoModelForSeq2SeqLM.from_pretrained(model_dir).eval()

# 动态量化：仅量化 Linear 层 → 权重 INT8，计算 FP32
qmodel = torch.ao.quantization.quantize_dynamic(
    model,
    {torch.nn.Linear},
    dtype=torch.qint8
)

# 保存量化后的模型
qmodel.save_pretrained(out_dir)
tokenizer.save_pretrained(out_dir)

print("动态 INT8 量化完成：", out_dir)
EOF
```

python3 quantize_dynamic_int8.py

```
cat > translate_int8.py << 'EOF'
#!/usr/bin/env python3
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM
import sys

if __name__ == "__main__":
    text = sys.argv[1]
    # 加载动态 INT8 量化模型
    tokenizer = AutoTokenizer.from_pretrained("/app/opus-mt-zh-en-dynamic-int8")
    model = AutoModelForSeq2SeqLM.from_pretrained("/app/opus-mt-zh-en-dynamic-int8")
    inputs = tokenizer(text, return_tensors="pt", padding=True)
    outputs = model.generate(**inputs)
    print(tokenizer.decode(outputs[0], skip_special_tokens=True))
EOF
```

python3 translate_int8.py "你好"


### transformers 静态量化

未尝试。

静态量化 = 权重 + 激活都提前算好量化参数（scale/zero_point），推理时不再动态计算 → 速度更快、理论上更省内存，但要做校准（需要数据）。

静态量化脚本 static_quantize_int8.py：
```
cat > static_quantize_int8.py << 'EOF'
import torch
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM
from torch.ao.quantization import get_default_qconfig, prepare, convert

# 路径
model_dir = "/app/opus-mt-zh-en"
out_dir = "/app/opus-mt-zh-en-static-int8"

# 加载模型
tokenizer = AutoTokenizer.from_pretrained(model_dir)
model = AutoModelForSeq2SeqLM.from_pretrained(model_dir).eval()

# 1. 设置量化配置（静态，x86 CPU 用 fbgemm）
qconfig = get_default_qconfig("fbgemm")
model.qconfig = qconfig

# 2. 融合层（可选但推荐，提速）
# OPUS-MT 是 marian，结构简单，这里只做最基础的
try:
    model = torch.quantization.fuse_modules(
        model,
        [
            ["encoder.layers.0.self_attn.q_proj", "encoder.layers.0.self_attn.k_proj"],
            # 可根据报错增删，不行就注释掉
        ]
    )
except Exception as e:
    print("融合跳过：", e)

# 3. 准备量化
model_prepared = prepare(model, inplace=False)

# 4. 校准（必须！静态量化核心，用 10–50 句中文即可）
calibration_texts = [
    "你好，今天天气很好",
    "我是一名学生",
    "机器学习很有趣",
    "人工智能改变世界",
    "自然语言处理",
    "我喜欢编程",
    "中国文化博大精深",
    "今天吃什么",
    "请帮我翻译这句话",
    "深度学习"
]
print("校准中...")
for text in calibration_texts:
    inputs = tokenizer(text, return_tensors="pt", padding=True)
    model_prepared(**inputs)

# 5. 转换为静态 INT8 模型
model_quantized = convert(model_prepared)

# 6. 保存
model_quantized.save_pretrained(out_dir)
tokenizer.save_pretrained(out_dir)

print("静态 INT8 量化完成：", out_dir)
EOF
```

执行量化：
```
python3 static_quantize_int8.py
```

改写 translate.py 加载静态量化模型：
```
cat > translate.py << 'EOF'
#!/usr/bin/env python3
from transformers import AutoTokenizer, AutoModelForSeq2SeqLM
import sys

if __name__ == "__main__":
    text = sys.argv[1]
    tokenizer = AutoTokenizer.from_pretrained("/app/opus-mt-zh-en-static-int8")
    model = AutoModelForSeq2SeqLM.from_pretrained("/app/opus-mt-zh-en-static-int8")
    inputs = tokenizer(text, return_tensors="pt", padding=True)
    outputs = model.generate(**inputs)
    print(tokenizer.decode(outputs[0], skip_special_tokens=True))
EOF
```

测试：`python3 translate.py "你好"`


### CTranslate2 量化

试下来未成功。

```
docker exec -it translate bash
cd /app
pip install ctranslate2 -i https://pypi.tuna.tsinghua.edu.cn/simple
ct2-opus-mt-converter --help
```

量化
```
ct2-transformers-converter \
  --model /app/opus-mt-zh-en \
  --output_dir /app/opus-mt-zh-en-ct2-int8float32 \
  --quantization int8_float32 \
  --force

# 或 

ct2-opus-mt-converter \
  --model_dir /app/opus-mt-zh-en \
  --output_dir /app/opus-mt-zh-en-ct2-int8float32 \
  --quantization int8_float32 \
  --force
```

脚本
```
cat > /app/ct2_translate.py << 'EOF'
#!/usr/bin/env python3
import sys
import ctranslate2
import sentencepiece as spm

# 量化模型路径
model_path = "/app/opus-mt-zh-en-ct2-int8float32"
spm_source = "/app/opus-mt-zh-en/source.spm"
spm_target = "/app/opus-mt-zh-en/target.spm"

# 加载分词器 & 模型
sp = spm.SentencePieceProcessor()
sp.load(spm_source)

sp_tgt = spm.SentencePieceProcessor()
sp_tgt.load(spm_target)

# 2核2G最优线程：inter=1, intra=2（不超物理核）
translator = ctranslate2.Translator(
    model_path,
    device="cpu",
    inter_threads=1,
    intra_threads=2
)

# 输入中文
text = sys.argv[1]

# 翻译
tokens = sp.encode(text, out_type=str)
results = translator.translate_batch([tokens])
output = sp_tgt.decode(results[0].hypotheses[0])

print(output)
EOF
```


查看：`python3 ct2_translate.py "你好"`






<br/><br/><br/><br/><br/>
## 参考资料



