# 使用官方 Python 3.10.14 镜像作为基础镜像
FROM diffusers/diffusers-onnxruntime-cuda:latest
# 安装 vim
RUN sed -i -E 's#http://archive.ubuntu.com/ubuntu#http://mirrors.cloud.tencent.com/ubuntu#g' /etc/apt/sources.list
RUN apt-get update && apt-get install -y \
    vim \
    wget \
    htop \
    build-essential \
    procps \
    python3.10-dev \
    && rm -rf /var/lib/apt/lists/*

# 创建TikToken缓存目录
RUN mkdir /opt/tiktoken_cache

# 下载TikToken模型缓存
ARG TIKTOKEN_URL="https://openaipublic.blob.core.windows.net/encodings/cl100k_base.tiktoken"
RUN wget -O /opt/tiktoken_cache/$(echo -n $TIKTOKEN_URL | sha1sum | head -c 40) "$TIKTOKEN_URL"

# 设置环境变量指向TikToken缓存目录
ENV TIKTOKEN_CACHE_DIR=/opt/tiktoken_cache

RUN pip config set global.index-url https://mirrors.cloud.tencent.com/pypi/simple
# 复制 requirements.txt 文件到容器中
COPY requirements.txt /tmp/requirements.txt

# 安装 Python 依赖
RUN pip install --no-cache-dir -r /tmp/requirements.txt

# 复制 models 文件夹到 /root 目录
COPY models /root/models
COPY nltk_data /root/nltk_data

# 设置工作目录
WORKDIR /workspace

# 清理 APT 缓存
RUN apt-get clean && rm -rf /var/lib/apt/lists/*

RUN pip install httpx==0.27.0
# 设置默认命令
CMD ["/bin/bash"]
