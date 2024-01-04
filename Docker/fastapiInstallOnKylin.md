pip install fastapi
pip install uvicorn 
pip install clickhouse_driver


# 针对webapi
切换到指定python.exe, 运行python.exe -m venv venv 新建venv虚拟环境文件夹
source venv/bin/activate


## 废弃方案
原启动指令
```
uvicorn main:app --reload
```
替换为main.py末尾添加 
```python
if __name__ == '__main__':
    uvicorn.run(app=app, host="0.0.0.0", port=8084)
```