##### Arthas安装(全量安装)

下载地址：

```http

```



- **linux**

  ```shell
  unzip arthas-packaging-3.x.x-bin.zip
  ##若已安装过，则删除原先版本，执行
  rm -rf ~/.arthas/lib/*
  
  cd arthas
  ./install-local.sh
  ```



  启动arthas:

  ```
  ./as.sh
  ```


- **windows**

  解压后，bin目录有as.bat

  该脚本只接受一个参数pid，所以只能诊断本机上的java进程。

  ```
  as.bat <pid>
  ```


##### 启动

```
cd Arthas
java -jar arthas-boot.jar
```

- 执行该程序的用户需要和目标进程具有相同的权限

  如：sudo su admin && java -jar arthas-boot.jar

- attatch目标进程

  ```shell
  $ java -jar arthas-boot.jar 
  [1]: 14321 bootloader.jar
  [2]: 14067 Launcher
  [3]: 28325 QuorumPeerMain
  [4]: 3494 Main
  [5]: 23774 org.eclipse.equinox.launcher_1.4.0.v20161219-1356.jar
  
  ```

  选择需要attatch的进程序号kefangwe，回车，输出日志如下:

  ```shell
  [INFO] arthas home: /home/grez/Arthas
  [INFO] Try to attach process 14067
  [INFO] Attach process 14067 success.
  [INFO] arthas-client connect 127.0.0.1 3658
    ,---.  ,------. ,--------.,--.  ,--.  ,---.   ,---.                           
   /  O  \ |  .--. ''--.  .--'|  '--'  | /  O  \ '   .-'                          
  |  .-.  ||  '--'.'   |  |   |  .--.  ||  .-.  |`.  `-.                          
  |  | |  ||  |\  \    |  |   |  |  |  ||  | |  |.-'    |                         
  `--' `--'`--' '--'   `--'   `--'  `--'`--' `--'`-----'                          
                                                                                  
  
  wiki: https://alibaba.github.io/arthas
  version: 3.0.5
  pid: 14067
  time: 2018-12-19 00:06:44
  ```



##### 非交互模式启动

启动脚本如下：

```
./as.sh <PID>[@IP:PORT]
```

参数说明：

- PID:目标java进程ID
- IP：Arthas Server侦听地址，默认127.0.0.1。
- PORT：目标服务器Arthas Server的端口号，默认端口号是3658

示例：

```
./as.sh 3494@192.168.1.4:3658
```

远程诊断：

```shell
telnet 192.168.1.4 3658
```

也可访问web console，输入远程IP 和端口



##### 退出arthas

  退出当前连接，可直接输入quit或exit命令。Attatch到目标进程上的arthas还会继续运行，端口会保持开放，下次连接可以直接连接上。

  完全退出则输入shutdown



##### Web Console

attatch成功后，浏览器可访问：

```http
http://127.0.0.1:8563/
```



