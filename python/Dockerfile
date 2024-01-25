# 指定基础镜像
FROM python:3.8

# 设置工作目录
WORKDIR /app

# 复制当前目录下的所有文件到工作目录
COPY ./app/ /app

# 安装依赖
RUN pip3 install -r requirements.txt

# 暴露端口
EXPOSE 8000

# 启动命令
CMD [ "python", "app.py" ]