1. python 并非真正意义的多线程，GIL锁的存在，导致代码级是多线程写法，但是 byte code 是单线程读取，
   验证方式， 对比如下两端代码执行后，任务管理器-性能-cpu的使用%
```python
# 多线程写法
#coding=utf-8
from multiprocessing import Pool
from threading import Thread

from multiprocessing import Process


def loop():
    while True:
        pass

if __name__ == '__main__':

    for i in range(3):
        t = Thread(target=loop)
        t.start()

    while True:
        pass
```

```python
# 多进程写法
#coding=utf-8
from multiprocessing import Pool
from threading import Thread

from multiprocessing import Process


def loop():
    while True:
        pass

if __name__ == '__main__':

    for i in range(14):
        t = Process(target=loop)
        t.start()

    while True:
        pass

```

1. io密集型和cpu密集型。一个是读写上传，一个是计算。纯python因为GIL锁步适合CPU密集型，不过，一些数学三方库用的cpython，这里会释放GIL锁。


1. 多线程上传设计思路。
   1. 本地读取，加入待上传队列。
   2. 线程取队列中的数据1，链接ftp，上传。
   3. 上传结束，关闭ftp。