# 指定基础镜像
FROM python:3.12-alpine

# 设置工作目录
WORKDIR /app
COPY ./requirements.txt /app/
# 安装依赖
RUN pip3 install -r requirements.txt