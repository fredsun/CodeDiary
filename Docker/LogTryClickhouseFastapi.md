1. 下载clickhouse镜像,yandex的2年没维护了
   docker pull clickhouse/clickhouse-server:22.8.14.53

2. 运行个基于镜像的容器测试
   docker run -d -p 8123:8123 -p 9000:9000 --name clickhouse clickhouse/clickhouse-server
   crul http://localhost:8123
   docker run -d -p 8123:8123 -p 9000:9000 --name=clickhouse-container clickhouse/clickhouse-server:22.8.14.53
    只为了提取文件
    
3. 进入容器
   docker exec -it clickhouse-container /bin/bash

4. 挂载思路
   1. docker cp 只保证本次执行，对内部文件的改动不会保存
   2. docker volumn是将启动时将外部文件挂载进内部文件夹，需要外部目录
   3. 考虑基于 clickhouse-server 新建 image, 在新建时放入数据文件，然后运行时启动clickhouse-server服务，导入数据，启动fastapi服务
   4. docker compose 是将clickhouse 和 fastapi的分离，但是clickhouse的数据文件需要挂载，fastapi的代码也需要挂载，这样就需要两个挂载点，而且需要两个dockerfile 
   5. docker 多阶段构建即多FROM无法完成，本意用于数据隔离，

# docker-compose ![https://blog.csdn.net/m0_68828261/article/details/129298850]
docker run --rm -d --name=temp-clickhouse-server clickhouse/clickhouse-server:22.8.14.53
docker cp temp-clickhouse-server:/etc/clickhouse-server/users.xml /home/clickhouse/conf/users.xml
docker cp temp-clickhouse-server:/etc/clickhouse-server/config.xml /home/clickhouse/conf/config.xml

关闭本地clickhouse-server服务后运行:
docker run -d --name=clickhouse-server \
-p 8123:8123 \
-p 9000:9000 \
--volume=/home/clickhouse/conf/config.xml:/etc/clickhouse-server/config.xml \
--volume=/home/clickhouse/conf/users.xml:/etc/clickhouse-server/users.xml \
--volume=/home/clickhouse/conf/users.d/default-password.xml:/etc/clickhouse-server/users.d/default-password.xml \
--volume=/home/clickhouse/data:/var/lib/clickhouse:rw \
--volume=/home/clickhouse/log:/var/log/clickhouse-server:rw \
clickhouse/clickhouse-server:22.8.14.53

docker exec -it clickhouse-server /bin/bash


挂载出log

docker run -d --name=fastapi \
-p 8123:8123 \
-p 9000:9000 \
--volume=/home/clickhousefastapi/log/clickhouse-server.log:/var/log/clickhouse-server/clickhouse-server.log \
--volume=/home/clickhousefastapi/log/clickhouse-server.err.log:/var/log/clickhouse-server/clickhouse-server.err.log \
fastapidemo:v1.0


## 直接打包运行成功clickhouse+docker+fastapi的kylin
E:\docker\kylin-docker>docker build . -t kylin:v1.0
[+] Building 863.5s (3/4)                                                                                docker:default
 => [internal] load .dockerignore                                                                                  0.1s
 => => transferring context: 2B                                                                                    0.0s
 => [internal] load build definition from Dockerfile                                                               0.1s
 => => transferring dockerfile: 541B                                                                               0.0s
 => [internal] load build context                                                                                696.1s
 => => transferring context: 27.69GB                                                                             696.0s
 => [1/1] ADD kylin-v10-docker.tar.gz /                                                                          167.3s
ERROR: failed to receive status: rpc error: code = Unavailable desc = error reading from server: EOF

c盘爆了

打包完成后，进入运行 
service clickhouse-server start 无法启动
tail -f /var/log/clickhouse-server/clickhouse-server.log  #查不出错
tail -f /var/log/clickhouse-server/stderr.log #查不出错
sudo --preserve-env -u 'clickhouse' /usr/bin/clickhouse-server --config-file /etc/clickhouse-server/config.xml --pid-file /var/run/clickhouse-server/clickhouse-server.pid
提示缺少proc文件
下次打包时加入


docker run --name testdocker -it --privileged=true kylin-v10-docker:v1.0 /sbin/init

/var/log/clickhouse-server#查看log vi clickhouse-server.err.log
shift + g 滑到最底
不断查看日志
vi /var/log/clickhouse-server/clickhouse-server.log
journalctl -xe


## 
  直接在 chroot . 下的原机器中启动service clickhouse-server start
  后提示缺少 /proc/self/statm
  mkdir /proc/self/statm 
  vi statm
```
2023.12.29 10:06:27.183767 [ 627072 ] {} <Information> Application: Will watch for the process with pid 627073
2023.12.29 10:06:27.183890 [ 627073 ] {} <Information> Application: Forked a child process to watch
2023.12.29 10:06:27.184208 [ 627073 ] {} <Information> SentryWriter: Sending crash reports is disabled
2023.12.29 10:06:27.184623 [ 627073 ] {} <Trace> Pipe: Pipe capacity is 1.00 MiB
2023.12.29 10:06:27.184949 [ 627073 ] {} <Information> Application: Starting ClickHouse 23.11.3.23 (revision: 54480, git hash: a14ab450b0ec41313233ceeeb38f3d7e8119878d, build id: 98093A380027728FEEE7A080672A837BCE5B63D2), PID 627073
2023.12.29 10:06:27.185206 [ 627073 ] {} <Information> Application: starting up
2023.12.29 10:06:27.185215 [ 627073 ] {} <Information> Application: OS name: Linux, version: 5.4.18-85-generic, architecture: x86_64
2023.12.29 10:06:27.188818 [ 627073 ] {} <Information> Application: Available RAM: 15.61 GiB; physical cores: 2; logical cores: 2.
2023.12.29 10:06:27.189834 [ 627073 ] {} <Trace> Context: Shutting down named sessions
2023.12.29 10:06:27.189851 [ 627073 ] {} <Trace> Context: Shutting down database catalog
2023.12.29 10:06:27.189866 [ 627073 ] {} <Trace> DatabaseCatalog: Shutting down system databases
2023.12.29 10:06:27.189873 [ 627073 ] {} <Trace> Context: Shutting down DDLWorker
2023.12.29 10:06:27.189876 [ 627073 ] {} <Trace> Context: Shutting down caches
2023.12.29 10:06:27.189949 [ 627073 ] {} <Error> Application: Code: 107. DB::ErrnoException: Cannot open file /proc/self/statm, errno: 2, strerror: No such file or directory. (FILE_DOESNT_EXIST), Stack trace (when copying this message, always include the lines below):

0. ? @ 0x000000000c500617 in ?
1. ? @ 0x000000000c501b34 in ?
2. ? @ 0x000000000c501a05 in ?
3. ? @ 0x000000000c65552c in ?
4. ? @ 0x000000000c63964e in ?
5. ? @ 0x00000000110d668e in ?
6. ? @ 0x000000000c58ed30 in ?
7. ? @ 0x0000000014c86586 in ?
8. ? @ 0x000000000c58a3d1 in ?
9. ? @ 0x0000000014c8f2d9 in ?
10. ? @ 0x000000000c58728a in ?
11. ? @ 0x00000000071c374e in ?
12. ? @ 0x00007f75f12ac0b3 in ?
13. ? @ 0x0000000005e9f36e in ?
 (version 23.11.3.23 (official build))
2023.12.29 10:06:27.189969 [ 627073 ] {} <Information> Application: shutting down
2023.12.29 10:06:27.189971 [ 627073 ] {} <Debug> Application: Uninitializing subsystem: Logging Subsystem
2023.12.29 10:06:27.190067 [ 627074 ] {} <Trace> BaseDaemon: Received signal -2
2023.12.29 10:06:27.190115 [ 627074 ] {} <Information> BaseDaemon: Stop SignalListener thread
2023.12.29 10:06:27.193517 [ 627072 ] {} <Information> Application: Child process exited normally with code 107.
```


  提示dns错误，修改dockerdns
  修改clickhouse listen_host为0.0.0.0
vi /etc/clickhouse-server/config.xml
切换到clickhouse用户启动server
 sudo -u clickhouse clickhouse-server --config-file /etc/clickhouse-server/config.xml 

 修改docker的DNS
  vi /etc/docker/daemon.json
  添加
  ```
  ,
   "dns" : [
    "114.114.114.114",
    "8.8.8.8"
  ]
  ```
```
2023.12.29 11:06:25.284241 [ 653919 ] {} <Information> Application: Will watch for the process with pid 653920
2023.12.29 11:06:25.284341 [ 653920 ] {} <Information> Application: Forked a child process to watch
2023.12.29 11:06:25.285308 [ 653920 ] {} <Information> SentryWriter: Sending crash reports is disabled
2023.12.29 11:06:25.285796 [ 653920 ] {} <Trace> Pipe: Pipe capacity is 1.00 MiB
2023.12.29 11:06:25.287594 [ 653920 ] {} <Information> Application: Starting ClickHouse 23.11.3.23 (revision: 54480, git hash: a14ab450b0ec41313233ceeeb38f3d7e8119878d, build id: 98093A380027728FEEE7A080672A837BCE5B63D2), PID 653920
2023.12.29 11:06:25.288238 [ 653920 ] {} <Information> Application: starting up
2023.12.29 11:06:25.288262 [ 653920 ] {} <Information> Application: OS name: Linux, version: 5.4.18-85-generic, architecture: x86_64
2023.12.29 11:06:25.332638 [ 653920 ] {} <Information> Application: Available RAM: 15.61 GiB; physical cores: 2; logical cores: 2.
2023.12.29 11:06:25.339350 [ 653920 ] {} <Trace> AsynchronousMetrics: Scanning /sys/class/thermal
2023.12.29 11:06:25.339383 [ 653920 ] {} <Trace> AsynchronousMetrics: Scanning /sys/block
2023.12.29 11:06:25.339665 [ 653920 ] {} <Trace> AsynchronousMetrics: Scanning /sys/devices/system/edac
2023.12.29 11:06:25.339932 [ 653920 ] {} <Trace> AsynchronousMetrics: Scanning /sys/class/hwmon
2023.12.29 11:06:25.370051 [ 653920 ] {} <Warning> Application: Cannot mlock: Code: 107. DB::ErrnoException: Cannot open file /proc/self/maps, errno: 2, strerror: No such file or directory. (FILE_DOESNT_EXIST) (version 23.11.3.23 (official build))
2023.12.29 11:06:25.371489 [ 653920 ] {} <Information> StatusFile: Status file /var/lib/clickhouse/status already exists - unclean restart. Contents:
PID: 1378
Started at: 2023-12-27 14:40:42
Revision: 54480

2023.12.29 11:06:25.373213 [ 653920 ] {} <Debug> Application: Set max number of file descriptors to 1048576 (was 1024).
2023.12.29 11:06:25.373366 [ 653920 ] {} <Debug> Application: rlimit on number of threads is 63592
2023.12.29 11:06:25.373380 [ 653920 ] {} <Debug> Application: Initializing DateLUT.
2023.12.29 11:06:25.373390 [ 653920 ] {} <Trace> Application: Initialized DateLUT with time zone 'Asia/Shanghai'.
2023.12.29 11:06:25.374336 [ 653920 ] {} <Debug> Context: Setting up /var/lib/clickhouse/tmp/ to store temporary data in it
2023.12.29 11:06:25.376698 [ 653920 ] {} <Debug> Application: Configuration parameter 'interserver_http_host' doesn't exist or exists and empty. Will use 'user-pc' as replica host.
2023.12.29 11:06:25.376891 [ 653920 ] {} <Debug> Application: Initializing interserver credentials.
2023.12.29 11:06:25.378529 [ 653920 ] {} <Information> Application: Lowered uncompressed cache size to 7.80 GiB because the system has limited RAM
2023.12.29 11:06:25.381202 [ 653920 ] {} <Trace> NamedCollectionsUtils: Loaded 0 collections from config
2023.12.29 11:06:25.382822 [ 653920 ] {} <Trace> NamedCollectionsUtils: Loaded 0 collections from SQL
2023.12.29 11:06:25.385067 [ 653920 ] {} <Debug> ConfigReloader: Loading config '/etc/clickhouse-server/config.xml'
2023.12.29 11:06:25.387773 [ 653920 ] {} <Debug> ConfigReloader: Loaded config '/etc/clickhouse-server/config.xml', performing update on configuration
2023.12.29 11:06:25.390439 [ 653920 ] {} <Information> Application: Setting max_server_memory_usage was set to 14.05 GiB (15.61 GiB available * 0.90 max_server_memory_usage_to_ram_ratio)
2023.12.29 11:06:25.390706 [ 653920 ] {} <Information> Application: Setting merges_mutations_memory_usage_soft_limit was set to 7.80 GiB (15.61 GiB available * 0.50 merges_mutations_memory_usage_to_ram_ratio)
2023.12.29 11:06:25.390713 [ 653920 ] {} <Information> Application: Merges and mutations memory limit is set to 7.80 GiB
2023.12.29 11:06:25.399364 [ 653920 ] {} <Information> BackgroundSchedulePool/BgBufSchPool: Create BackgroundSchedulePool with 16 threads
2023.12.29 11:06:25.400648 [ 653920 ] {} <Information> BackgroundSchedulePool/BgSchPool: Create BackgroundSchedulePool with 512 threads
2023.12.29 11:06:25.464058 [ 653920 ] {} <Information> BackgroundSchedulePool/BgMBSchPool: Create BackgroundSchedulePool with 16 threads
2023.12.29 11:06:25.464553 [ 653920 ] {} <Information> BackgroundSchedulePool/BgDistSchPool: Create BackgroundSchedulePool with 16 threads
2023.12.29 11:06:25.483339 [ 653920 ] {} <Information> CertificateReloader: One of paths is empty. Cannot apply new configuration for certificates. Fill all paths and try again.
2023.12.29 11:06:25.483670 [ 653920 ] {} <Debug> ConfigReloader: Loaded config '/etc/clickhouse-server/config.xml', performed update on configuration
2023.12.29 11:06:25.499422 [ 653920 ] {} <Trace> Context: Shutting down named sessions
2023.12.29 11:06:25.499460 [ 653920 ] {} <Trace> Context: Shutting down database catalog
2023.12.29 11:06:25.500624 [ 653920 ] {} <Trace> DatabaseCatalog: Shutting down system databases
2023.12.29 11:06:25.501949 [ 653920 ] {} <Trace> Context: Shutting down DDLWorker
2023.12.29 11:06:25.502154 [ 653920 ] {} <Trace> Context: Shutting down caches
2023.12.29 11:06:25.502832 [ 653920 ] {} <Trace> BackgroundSchedulePool/BgBufSchPool: Waiting for threads to finish.
2023.12.29 11:06:25.504099 [ 653920 ] {} <Trace> BackgroundSchedulePool/BgSchPool: Waiting for threads to finish.
2023.12.29 11:06:25.527456 [ 653920 ] {} <Trace> BackgroundSchedulePool/BgDistSchPool: Waiting for threads to finish.
2023.12.29 11:06:25.527585 [ 653920 ] {} <Trace> BackgroundSchedulePool/BgMBSchPool: Waiting for threads to finish.
2023.12.29 11:06:25.530120 [ 653920 ] {} <Error> Application: Code: 210. DB::Exception: Listen [::]:9009 failed: Poco::Exception. Code: 1000, e.code() = 98, Net Exception: Address already in use: [::]:9009 (version 23.11.3.23 (official build)). (NETWORK_ERROR), Stack trace (when copying this message, always include the lines below):

0. ? @ 0x000000000c500617 in ?
1. ? @ 0x000000000c589dd8 in ?
2. ? @ 0x000000000c589b30 in ?
3. ? @ 0x000000000c5a5b76 in ?
4. ? @ 0x000000000c599807 in ?
5. ? @ 0x0000000014c86586 in ?
6. ? @ 0x000000000c58a3d1 in ?
7. ? @ 0x0000000014c8f2d9 in ?
8. ? @ 0x000000000c58728a in ?
9. ? @ 0x00000000071c374e in ?
10. ? @ 0x00007f2702d660b3 in ?
11. ? @ 0x0000000005e9f36e in ?
 (version 23.11.3.23 (official build))
2023.12.29 11:06:25.530147 [ 653920 ] {} <Information> Application: shutting down
2023.12.29 11:06:25.530150 [ 653920 ] {} <Debug> Application: Uninitializing subsystem: Logging Subsystem
2023.12.29 11:06:25.530766 [ 653921 ] {} <Trace> BaseDaemon: Received signal -2
2023.12.29 11:06:25.530812 [ 653921 ] {} <Information> BaseDaemon: Stop SignalListener thread
2023.12.29 11:06:25.596016 [ 653919 ] {} <Information> Application: Child process exited normally with code 210.
```

```
2023.12.29 11:16:32.561696 [ 658958 ] {} <Information> Application: Will watch for the process with pid 658959
2023.12.29 11:16:32.561893 [ 658959 ] {} <Information> Application: Forked a child process to watch
2023.12.29 11:16:32.562180 [ 658959 ] {} <Information> SentryWriter: Sending crash reports is disabled
2023.12.29 11:16:32.562267 [ 658959 ] {} <Trace> Pipe: Pipe capacity is 1.00 MiB
2023.12.29 11:16:32.562563 [ 658959 ] {} <Information> Application: Starting ClickHouse 23.11.3.23 (revision: 54480, git hash: a14ab450b0ec41313233ceeeb38f3d7e8119878d, build id: 98093A380027728FEEE7A080672A837BCE5B63D2), PID 658959
2023.12.29 11:16:32.563043 [ 658959 ] {} <Information> Application: starting up
2023.12.29 11:16:32.563057 [ 658959 ] {} <Information> Application: OS name: Linux, version: 5.4.18-85-generic, architecture: x86_64
2023.12.29 11:16:32.566201 [ 658959 ] {} <Information> Application: Available RAM: 15.61 GiB; physical cores: 2; logical cores: 2.
2023.12.29 11:16:32.566667 [ 658959 ] {} <Trace> AsynchronousMetrics: Scanning /sys/class/thermal
2023.12.29 11:16:32.566679 [ 658959 ] {} <Trace> AsynchronousMetrics: Scanning /sys/block
2023.12.29 11:16:32.566682 [ 658959 ] {} <Trace> AsynchronousMetrics: Scanning /sys/devices/system/edac
2023.12.29 11:16:32.566686 [ 658959 ] {} <Trace> AsynchronousMetrics: Scanning /sys/class/hwmon
2023.12.29 11:16:32.568690 [ 658959 ] {} <Warning> Application: Cannot mlock: Code: 107. DB::ErrnoException: Cannot open file /proc/self/maps, errno: 2, strerror: No such file or directory. (FILE_DOESNT_EXIST) (version 23.11.3.23 (official build))
2023.12.29 11:16:32.568805 [ 658959 ] {} <Debug> Application: Set max number of file descriptors to 1048576 (was 1024).
2023.12.29 11:16:32.568819 [ 658959 ] {} <Debug> Application: rlimit on number of threads is 63592
2023.12.29 11:16:32.568822 [ 658959 ] {} <Debug> Application: Initializing DateLUT.
2023.12.29 11:16:32.568825 [ 658959 ] {} <Trace> Application: Initialized DateLUT with time zone 'Asia/Shanghai'.
2023.12.29 11:16:32.568842 [ 658959 ] {} <Debug> Context: Setting up /var/lib/clickhouse/tmp/ to store temporary data in it
2023.12.29 11:16:32.569189 [ 658959 ] {} <Debug> Application: Configuration parameter 'interserver_http_host' doesn't exist or exists and empty. Will use 'user-pc' as replica host.
2023.12.29 11:16:32.569205 [ 658959 ] {} <Debug> Application: Initializing interserver credentials.
2023.12.29 11:16:32.570579 [ 658959 ] {} <Information> Application: Lowered uncompressed cache size to 7.80 GiB because the system has limited RAM
2023.12.29 11:16:32.570642 [ 658959 ] {} <Trace> NamedCollectionsUtils: Loaded 0 collections from config
2023.12.29 11:16:32.570682 [ 658959 ] {} <Trace> NamedCollectionsUtils: Loaded 0 collections from SQL
2023.12.29 11:16:32.571639 [ 658959 ] {} <Debug> ConfigReloader: Loading config '/etc/clickhouse-server/config.xml'
2023.12.29 11:16:32.574328 [ 658959 ] {} <Debug> ConfigReloader: Loaded config '/etc/clickhouse-server/config.xml', performing update on configuration
2023.12.29 11:16:32.577800 [ 658959 ] {} <Information> Application: Setting max_server_memory_usage was set to 14.05 GiB (15.61 GiB available * 0.90 max_server_memory_usage_to_ram_ratio)
2023.12.29 11:16:32.577816 [ 658959 ] {} <Information> Application: Setting merges_mutations_memory_usage_soft_limit was set to 7.80 GiB (15.61 GiB available * 0.50 merges_mutations_memory_usage_to_ram_ratio)
2023.12.29 11:16:32.577818 [ 658959 ] {} <Information> Application: Merges and mutations memory limit is set to 7.80 GiB
2023.12.29 11:16:32.578037 [ 658959 ] {} <Information> BackgroundSchedulePool/BgBufSchPool: Create BackgroundSchedulePool with 16 threads
2023.12.29 11:16:32.579578 [ 658959 ] {} <Information> BackgroundSchedulePool/BgSchPool: Create BackgroundSchedulePool with 512 threads
2023.12.29 11:16:32.693607 [ 658959 ] {} <Information> BackgroundSchedulePool/BgMBSchPool: Create BackgroundSchedulePool with 16 threads
2023.12.29 11:16:32.703521 [ 658959 ] {} <Information> BackgroundSchedulePool/BgDistSchPool: Create BackgroundSchedulePool with 16 threads
2023.12.29 11:16:32.716749 [ 658959 ] {} <Information> CertificateReloader: One of paths is empty. Cannot apply new configuration for certificates. Fill all paths and try again.
2023.12.29 11:16:32.716797 [ 658959 ] {} <Debug> ConfigReloader: Loaded config '/etc/clickhouse-server/config.xml', performed update on configuration
2023.12.29 11:16:32.722703 [ 658959 ] {} <Trace> Context: Shutting down named sessions
2023.12.29 11:16:32.722729 [ 658959 ] {} <Trace> Context: Shutting down database catalog
2023.12.29 11:16:32.722736 [ 658959 ] {} <Trace> DatabaseCatalog: Shutting down system databases
2023.12.29 11:16:32.722745 [ 658959 ] {} <Trace> Context: Shutting down DDLWorker
2023.12.29 11:16:32.722748 [ 658959 ] {} <Trace> Context: Shutting down caches
2023.12.29 11:16:32.722798 [ 658959 ] {} <Trace> BackgroundSchedulePool/BgBufSchPool: Waiting for threads to finish.
2023.12.29 11:16:32.724410 [ 658959 ] {} <Trace> BackgroundSchedulePool/BgSchPool: Waiting for threads to finish.
2023.12.29 11:16:32.724809 [ 658959 ] {} <Trace> BackgroundSchedulePool/BgDistSchPool: Waiting for threads to finish.
2023.12.29 11:16:32.724923 [ 658959 ] {} <Trace> BackgroundSchedulePool/BgMBSchPool: Waiting for threads to finish.
2023.12.29 11:16:32.725056 [ 658959 ] {} <Error> Application: Code: 210. DB::Exception: Listen [::]:9009 failed: Poco::Exception. Code: 1000, e.code() = 98, Net Exception: Address already in use: [::]:9009 (version 23.11.3.23 (official build)). (NETWORK_ERROR), Stack trace (when copying this message, always include the lines below):

0. ? @ 0x000000000c500617 in ?
1. ? @ 0x000000000c589dd8 in ?
2. ? @ 0x000000000c589b30 in ?
3. ? @ 0x000000000c5a5b76 in ?
4. ? @ 0x000000000c599807 in ?
5. ? @ 0x0000000014c86586 in ?
6. ? @ 0x000000000c58a3d1 in ?
7. ? @ 0x0000000014c8f2d9 in ?
8. ? @ 0x000000000c58728a in ?
9. ? @ 0x00000000071c374e in ?
10. ? @ 0x00007fd19c0280b3 in ?
11. ? @ 0x0000000005e9f36e in ?
 (version 23.11.3.23 (official build))
2023.12.29 11:16:32.725070 [ 658959 ] {} <Information> Application: shutting down
2023.12.29 11:16:32.725073 [ 658959 ] {} <Debug> Application: Uninitializing subsystem: Logging Subsystem
2023.12.29 11:16:32.725519 [ 658960 ] {} <Trace> BaseDaemon: Received signal -2
2023.12.29 11:16:32.725628 [ 658960 ] {} <Information> BaseDaemon: Stop SignalListener thread
2023.12.29 11:16:32.772126 [ 658958 ] {} <Information> Application: Child process exited normally with code 210.
```