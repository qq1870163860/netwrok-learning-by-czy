# OSPF基础回顾
## OSPF
### 1、工作原理  
* 链路状态协议   
OSPF/ISIS
* 距离矢量协议  
RIP
* 链路状态和距离矢量的区别在  
距离矢量：目标距离有多远，方向在哪里
链路状态：
建立邻居
交互LSA（链路状态通告）
以自己为根，通过SPF算法计，LSDB数据库计算出结果  
写入RIB表中
![img_16.png](img_16.png)
### 2、工作区域 
* IGP内部网络协议  
OSPF/ISIS/RIP
* EGP外部网络协议  
BGP
### 3、版本
* OSPFV2 RFC2328 → IPV4
* OSPFV3 RFC2740 → IPV6（华为）/ IPV4 ＆ IPV6 （CISCO）
### 4、术语
* RID → OSPF路由标识 ‘唯一’  
手工配置 （优先级最高）  
lookback ip 大的  
选择物理接口 ip 大的  
RID 具有稳定性，一旦选举，修改RID →   
1、重启OSPF进程（一般选这个）  
2、重启路由器
