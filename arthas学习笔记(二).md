#### 使用命令



##### 一、JVM相关命令

**dashboard**

数据说明：

- ID：Java级别的线程ID，注意这个ID不能跟jstack中的nativeID一一对应
- NAME: 线程名
- GROUP: 线程组名
- PRIORITY: 线程优先级, 1~10之间的数字，越大表示优先级越高
- STATE: 线程的状态
- CPU%: 线程消耗的cpu占比，采样100ms，将所有线程在这100ms内的cpu使用量求和，再算出每个线程的cpu使用占比。
- TIME: 线程运行总时间，数据格式为`分：秒`
- INTERRUPTED: 线程当前的中断位状态
- DAEMON: 是否是daemon线程



**thread**

可查看当前线程信息，线程堆栈

参数说明：

| 参数名称      | 参数说明                              |
| ------------- | ------------------------------------- |
| id            | 线程id                                |
| [n:]          | 指定最忙的前N个线程并打印堆栈         |
| [b]           | 找出当前阻塞其他线程的线程            |
| [i `<value>`] | 指定cpu占比统计的采样间隔，单位为毫秒 |

使用示例：

```shell
#显示指定线程的运行堆栈
thread 1

#显示最忙的前n个线程堆栈
thread -n 3

#显示当前阻塞其他线程的线程
thread -b

#指定采样时间间隔，单位ms
thread 1 -i 100
```

**JVM**

相关描述：

THREAD相关

- COUNT：JVM当前活跃的线程
- DAEMON-COUNT：JVM当前活跃的守护线程数
- PEAK-COUNT：从JVM启动开始曾经活跃的最大线程数
- STARTED-COUNT: 从JVM启动开始总共启动过的线程次数
- DEADLOCK-COUNT: JVM当前死锁的线程数

文件描述符FILE-DESCRIPTOR相关

- MAX-FILE-DESCRIPTOR-COUNT：JVM进程最大可以打开的文件描述符数
- OPEN-FILE-DESCRIPTOR-COUNT：JVM当前打开的文件描述符数