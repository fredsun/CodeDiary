
# 使用 ClickHouse 镜像作为基础镜像
FROM clickhouse/clickhouse-server

# 安装 FastAPI 的依赖
RUN apt-get update && \
    apt-get install -y python3-pip

# 设置 ClickHouse 数据目录
RUN mkdir -p /var/lib/clickhouse

# Install any needed packages specified in requirements.txt
RUN pip3 install  -r  webapi/requirements.txt -i https://pypi.tuna.tsinghua.edu.cn/simple

# 设置 ClickHouse 配置文件（具体配置根据你的需求调整）
COPY ./clickhouse-config.xml /etc/clickhouse-server/config.d/

# 复制数据文件到镜像中
<!-- COPY ./data.tsv /app/data.tsv -->

# Make port 80 available to the world outside this container
EXPOSE 80
# 启动 ClickHouse 服务并导入数据，然后运行 FastAPI
CMD service clickhouse-server start 
CMD ["python", "webapi/main.py"]
<!-- && \ -->
    <!-- clickhouse-client --query "INSERT INTO your_table FORMAT TSV" < /app/data.tsv && \ -->
    <!-- uvicorn app.main:app --host 0.0.0.0 --port 8000 --reload -->




# Define environment variable
ENV NAME World

