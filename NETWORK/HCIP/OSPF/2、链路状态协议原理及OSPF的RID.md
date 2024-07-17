# 链路状态协议原理及OSPF的RID


## OSPF -- 开放最短路径优先协议 -- 链路状态协议

- 链路状态协议就是类似所有的路由器都拥有一张完全一致的“地图”
- 对于目标，是根据“地图”计算得出的  
- OSPF的数据库 -- 其实就是OSPF协议的“地图”，每一台OSPF路由器拥有完全一致的“地图”   
  为什么会完全一致？首先会有OSPF的邻居关系  
- dis ospf lsdb //查看ospf数据库
```
[ar3]dis ospf lsdb

	 OSPF Process 10 with Router ID 33.33.33.33
		 Link State Database 

		         Area: 0.0.0.0
 Type      LinkState ID    AdvRouter          Age  Len   Sequence   Metric
 Router    11.11.11.11     11.11.11.11       1165  36    80000004       1
 Router    22.22.22.22     22.22.22.22        444  48    8000000E       1
 Router    33.33.33.33     33.33.33.33        434  48    8000000A       1
 Network   23.1.1.3        33.33.33.33        434  32    80000005       0
 Network   12.1.1.2        22.22.22.22       1156  32    80000003       0
 
[ar3]dis ospf lsdb router 33.33.33.33

	 OSPF Process 10 with Router ID 33.33.33.33
		         Area: 0.0.0.0
		 Link State Database 


  Type      : Router
  Ls id     : 33.33.33.33
  Adv rtr   : 33.33.33.33  
  Ls age    : 451 
  Len       : 48 
  Options   :  E  
  seq#      : 8000000a 
  chksum    : 0x5e8e
  Link count: 2
   * Link ID: 23.1.1.3     
     Data   : 23.1.1.3     
     Link Type: TransNet     
     Metric : 1
   * Link ID: 3.3.3.3      
     Data   : 255.255.255.255 
     Link Type: StubNet      
     Metric : 0 
     Priority : Medium

```
 ![img_9.png](img_9.png)
![img_4.png](img_4.png)
### 配置OSPF
```
AR1

<ar1>sys
Enter system view, return user view with Ctrl+Z.
[ar1]ospf 10 router-id 11.11.11.11
[ar1-ospf-10]area 0 	
[ar1-ospf-10-area-0.0.0.0]network 12.1.1.1 0.0.0.0
[ar1-ospf-10-area-0.0.0.0]di th
[V200R003C00]
#
 area 0.0.0.0 
  network 12.1.1.1 0.0.0.0 
#
return
[ar1-ospf-10-area-0.0.0.0]
```
```
AR2

<ar2>sys
Enter system view, return user view with Ctrl+Z.	
[ar2]ospf 10 router-id 22.22.22.22
[ar2-ospf-10]area 0	
[ar2-ospf-10-area-0.0.0.0]network 12.1.1.2 0.0.0.0	
[ar2-ospf-10-area-0.0.0.0]network 23.1.1.2 0.0.0.0
[ar2-ospf-10-area-0.0.0.0]di th
[V200R003C00]
#
 area 0.0.0.0 
  network 12.1.1.2 0.0.0.0 
  network 23.1.1.2 0.0.0.0 
#
return
[ar2-ospf-10-area-0.0.0.0]
```

```
AR3

<ar3>sys
Enter system view, return user view with Ctrl+Z.
[ar3]ospf 10 router-id 33.33.33.33
[ar3-ospf-10]area 0
[ar3-ospf-10-area-0.0.0.0]network 3.3.3.3 0.0.0.0
[ar3-ospf-10-area-0.0.0.0]network 23.1.1.3 0.0.0.0
[ar3-ospf-10-area-0.0.0.0]di th
[V200R003C00]
#
 area 0.0.0.0 
  network 3.3.3.3 0.0.0.0 
  network 23.1.1.3 0.0.0.0 
#
return
[ar3-ospf-10-area-0.0.0.0]
```
### 查看AR1路由表信息
```
[ar1]dis ip routing-table 
Route Flags: R - relay, D - download to fib
------------------------------------------------------------------------------
Routing Tables: Public
         Destinations : 9        Routes : 9        

Destination/Mask    Proto   Pre  Cost      Flags NextHop         Interface

        3.3.3.3/32  OSPF    10   2           D   12.1.1.2        GigabitEthernet
0/0/0
       12.1.1.0/24  Direct  0    0           D   12.1.1.1        GigabitEthernet
0/0/0
       12.1.1.1/32  Direct  0    0           D   127.0.0.1       GigabitEthernet
0/0/0
     12.1.1.255/32  Direct  0    0           D   127.0.0.1       GigabitEthernet
0/0/0
       23.1.1.0/24  OSPF    10   2           D   12.1.1.2        GigabitEthernet
0/0/0
      127.0.0.0/8   Direct  0    0           D   127.0.0.1       InLoopBack0
      127.0.0.1/32  Direct  0    0           D   127.0.0.1       InLoopBack0
127.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0
255.255.255.255/32  Direct  0    0           D   127.0.0.1       InLoopBack0

[ar1]ping 3.3.3.3
  PING 3.3.3.3: 56  data bytes, press CTRL_C to break
    Reply from 3.3.3.3: bytes=56 Sequence=1 ttl=254 time=30 ms
    Reply from 3.3.3.3: bytes=56 Sequence=2 ttl=254 time=30 ms
    Reply from 3.3.3.3: bytes=56 Sequence=3 ttl=254 time=30 ms
    Reply from 3.3.3.3: bytes=56 Sequence=4 ttl=254 time=40 ms
    Reply from 3.3.3.3: bytes=56 Sequence=5 ttl=254 time=30 ms

  --- 3.3.3.3 ping statistics ---
    5 packet(s) transmitted
    5 packet(s) received
    0.00% packet loss
    round-trip min/avg/max = 30/32/40 ms

```
### AR3重启 抓取AR2 与 AR3 之间的交互报文
![img_5.png](img_5.png)
### LSA-TYPE 常见的有 1 2 3 4 5 7
TYPE-1 类型1的LSA链路状态通告：
描述本设备的直连链路状态  
- 有没有链接ospf邻居  
有邻居的链路-Transit中转  
没邻居的链路-stub末节
- 该链路的开销是多少  
环回接口开销在华为中固定为0  
1000M的链路在华为中固定为1（公式）  

## 链路状态路由协议-LSA泛洪 （根据地图自己计算来的）

- 链路状态路由协议交互的是链路状态通告LSA，而不是路由信息
- 运行链路状态路由协议的路由器之间首先会建立邻居关系，然后彼此之间开始交互LSA（Link State
 Advertisement，链路状态通告）。
![img_6.png](img_6.png)
