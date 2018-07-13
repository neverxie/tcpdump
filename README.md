# tcpdump

## 1、介绍
这是一个在基于 RT-Thread 的捕获IP报文的小工具，PCAP格式文件可以保存文件系统，导出到PC上可用Wireshark软件解析。

### 1.1、依赖

- 依赖 [optparse](https://github.com/liu2guang/optparse) 软件包
- 依赖 rdb[]() 软件包 & rdb 上位机软件 
- 依赖 DFS 文件系统
- RT-Thread 3.0+，对bsp无依赖

### 1.2、获取方式
- 使用 menuconfig

```
  RT-Thread online packages --->
      IOT internet of things --->
          [*] netutils: Networking utilities for RT-Thread  --->
          [*]   Enable tcpdump tool
          [ ]     Enable tcpdump data to print on the console
          [*]     Enable tcpdump debug log output
```

使用 `pkgs --update` 命令来获取或更新软件包。

## 2、使用

### 2.1、tcpdump命令说明

-i: 指定监听的网络接口

-s: 选择保存方式（文件系统 或 rdb）

-w: 将抓到的数据包写入xxx.pcap文件中

-p: 停止抓包

-h: 帮助信息


## 3、保存文件系统中

我们这里是把SD卡挂载文件系统

### 3.1、抓包前准备

开发板上电前，插入SD卡。SD卡挂载成功，则提示：
```
SD card capacity 31023104 KB
probe mmcsd block device!
found part[0], begin: 10485760, size: 29.580GB
File System initialized!
```

挂载失败，则提示：

```
sdcard init fail or timeout: -2!
```

msh/>里，输入 "list_device" 可以查看成功挂载的设备，详情如下：

```
msh />list_device
device         type         ref count
sd0    Block Device         1       
e0     Network Interface    0             
usbd   USB Slave Device     0                   
rtc    RTC                  1       
spi4   SPI Bus              0       
pin    Miscellaneous Device 0       
uart1  Character Device     3       
msh />
```

### 3.2、抓包前检查

抓包前请确认板子的ip地址
msh/>里，输入 "ifconfig" 查看网卡名称，详情如下：

```
msh />ifconfig
network interface: e0 (Default)
MTU: 1500
MAC: 00 04 9f 05 44 e5 
FLAGS: UP LINK_DOWN ETHARP
ip address: 192.168.1.30
gw address: 192.168.1.1
net mask  : 255.255.255.0
dns server #0: 0.0.0.0
dns server #1: 0.0.0.0
msh />
```

### 3.3、开启抓包

msh/>里，输入 "tcpdump -ie0 -wsample.pcap"，详情如下：

```
msh />tcpdump -ie0 -ssd -wtext.pcap
select "e0" network card device
select "sd card" mode
save in "text.pcap"
[TCPDUMP]tcpdump start!
msh />
```

msh/> 带自动补全功能，输入 "tc"，按下tab键，详情如下：

```
msh />tc
tcpdump
msh />tcpdump
```

### 3.4、ping

msh/>里输入 "ping" [ping](https://github.com/RT-Thread-packages/netutils/blob/master/ping/README.md) 得到IP，电脑ping这个IP地址

```
msh />ping 192.168.1.29
```

也可以 "ping" 域名
```
msh />ping www.baidu.com
```

### 3.5、停止抓包

msh/>里，输入 "tcpdump -p"，详情如下：

```
msh />tcpdump -p
tcp dump thread exit
msh />
```

### 3.6、查看结果

msh/>里，输入 "ls" 查看保存结果，详情如下：

```
msh />ls
Directory /:
System Volume Information<DIR>                    
sample.pcap         1012                     
msh />
```

### 3.7、抓包后处理

使用读卡器将保存在sd卡里的xx.pcap文件拷贝到PC，使用抓包软件 wireshark 直接进行网络流的分析


## 4、抓包文件通过rdb[]() 软件包导入PC

### 4.1、开启抓包

```
msh />tcpdump -ie0 -srdb -wtext.pcap
select "e0" network card device
select "rdb" mode
save in "text.pcap"
[TCPDUMP]tcpdump start!
msh />
```

### 4.2、ping

msh/>里输入 "ping" [ping](https://github.com/RT-Thread-packages/netutils/blob/master/ping/README.md) 得到IP，电脑ping这个IP地址

```
msh />ping 192.168.1.29
```

也可以 "ping" 域名
```
msh />ping www.baidu.com
```

### 4.3、停止抓包

msh/>里，输入 "tcpdump -p"，详情如下：

```
msh />tcpdump -p
tcp dump thread exit
msh />
```

### 4.4、查看结果

msh/>里，输入 "ls" 查看保存结果，详情如下：

```
msh />ls
Directory /:
System Volume Information<DIR>                    
sample.pcap         1012                     
msh />
```

### 4.5、抓包后处理

使用rdb[]() 软件包将 xx.pcap 文件导入到PC，使用抓包软件 wireshark 直接进行网络流的分析


## 5、命令补充说明
sd卡保存 和 rdb保存的抓包流程是一样的，二者的命令有区别

带命令但不带参数，则用默认值，下面会有msh/>详情

### 5.1、只输入 tcpdump

默认网卡，保存模式，文件名，详情如下：

```
msh />tcpdump
listening on e0, link-type Ethernet
default selection "e0" network card device
default selection "sd card" mode
default selection "sample.pcap"
[TCPDUMP]tcpdump start!
msh />
```

### 5.2、默认网卡

##### 5.2.1、不带-i

此时默认网卡，详情如下：

```
msh />tcpdump -ssd -wtext.pcap
select "sd card" mode
default selection "e0" network card device
save in "text.pcap"
[TCPDUMP]tcpdump start!
msh />
```

##### 5.2.2、带-i，但不带参数

此时默认网卡，详情如下：

```
msh />tcpdump -i -ssd -wtext.pcap
select "sd card" mode
default selection "e0" network card device
save in "text.pcap"
[TCPDUMP]tcpdump start!
msh />
```

### 5.3、默认 sd card 模式

##### 5.3.1、不带-s

此时默认 sd card 模式，详情如下：

```
msh />tcpdump -ie0 -wtext.pcap    
select "e0" network card device
default selection "sd card" mode
save in "text.pcap"
[TCPDUMP]tcpdump start!
msh /> 
```

##### 5.3.2、带-s，但不带参数

此时默认 sd card 模式，详情如下：

```
msh />tcpdump -ie0 -s -wtext.pcap
select "e0" network card device
default selection "sd card" mode
save in "text.pcap"
[TCPDUMP]tcpdump start!
msh />
```

### 5.4、默认文件名

##### 5.4.1、不带-w

此时默认 sample.pcap 文件名，详情如下：

```
msh />tcpdump -ie0 -ssd 
select "e0" network card device
select "sd card" mode
default selection "sample.pcap"
[TCPDUMP]tcpdump start!
msh />
```

##### 5.4.2、带-w，但不带参数

此时默认 sample.pcap 文件名，详情如下：

```
msh />tcpdump -ie0 -ssd -w
select "e0" network card device
select "sd card" mode
default selection "sample.pcap"
[TCPDUMP]tcpdump start!
msh />
```


## 6、注意事项

- 抓包结束或者不想抓包了，请输入 "tcpdump -p" 结束抓包

## 7、联系方式 & 感谢

* 感谢：[liu2guang](https://github.com/liu2guang) 制作了optprase软件包
* 感谢：[uestczyh222](https://github.com/uestczyh222) 制作了rdb软件包 & rdb上位机
* 维护：[never](https://github.com/neverxie)
* 主页：https://github.com/RT-Thread-packages/netutils
