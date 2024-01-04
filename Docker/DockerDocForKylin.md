
# kylin 打包部分目录生成 docker.tar.gz 和 dockerfile, 
docker pull registry #拉取官方仓库
//docker run -di -p 5000:5000 registry  #在后台模式 detach 保持标准输入输出 input output 主机的5000端口映射到容器的5000端口，镜像名称叫registry
docker run -di --name=myRegistry -p 5000:5000 registry #新建仓库，且命名为myRegistry

## 私有库
给本地镜像打tag，注明服务器地址+端口+文件名, 和tag名, push后，直接发送到tag的服务器地址中
win下, 在docker软件界面打开Setting -> DockerEngine ，修改daemon.json,添加
```
{
 "insecure-registries": ["192.168.50.34:5000"]
}
```

重启 docker 
重启 仓库
docker start myRegistry
浏览器访问 http://192.168.50.34:5000/v2/_catalog  #填入本机ip访问返回 {"repositories":[]}
打tag格式 docker tag [OPTIONS] IMAGE[:TAG] [REGISTRYHOST/][USERNAME/]NAME[:TAG]
docker tag kylin-v10-docker:v1.0 192.168.50.34:5000/kylin:v1  # 给本地的镜像打tag 注意, name:tag之间没空格 , 且本地部分name和tag都需要完全注明, 本地images中生成 REPOSITORY为192.168.50.34:5000/kylin 的镜像
上传格式 docker push [OPTIONS] NAME[:TAG]
docker push 192.168.50.34:5000/kylin:v1  #将本地仓中, 最后得到 http://192.168.50.34:5000下返回{"repositories":["kylin"]}
删除img需要带tag
docker rmi 192.168.50.34:5000/kylin:v1

补充pip

制作fastapiDocker
docker build . -t fastapidemo:v1.0
测试
docker run -p 8000:8000 --name fastapi fastapidemo:v1.0 
docker exec -it fastapi bash

docker run --name testdocker -it --privileged=true kylin-v10-docker:v1.0 /sbin/init
进入/home/webapi/
source venv/bin/activate
uvicorn main:app --host 0.0.0.0 --port 8000 --reload

