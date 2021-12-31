网络运维平台：

# 日常运维



# CMDB：



## IP地址段



## 安全域



## 成本中心



## 机房



## IP地址



# 设备管理



# 拓扑管理

1. 网元管理
2. 链路管理
3. 配置管理



# 告警收敛：



## 多个告警源合并告警

目前网络设备有两个告警源，zabbix、snmpTrap。其中snmpTrap是自建的告警源。

其中zabbix、snmpTrap都可上报端口DOWN告警。其不同是：

- zabbix周期性获取端口状态信息，端口DOWN告警有延迟，不能发现闪断类告警。
- snmpTrap即时上报端口状态变化信息

告警合并后，可有效减少50%的端口DOWN告警



## 端口状态发生变化

接收到的原始Trap消息

```
1.3.6.1.2.1.1.3.0 = 3768587121
1.3.6.1.6.3.1.1.4.1.0 = 1.3.6.1.6.3.1.1.5.3
1.3.6.1.2.1.2.2.1.1.12 = 12
1.3.6.1.2.1.2.2.1.7.12 = 1
1.3.6.1.2.1.2.2.1.8.12 = 2
1.3.6.1.2.1.2.2.1.2.12 = GigabitEthernet2/0/5
```

解析snmpTrap消息

```
{
	'snmpTrapOID': '1.3.6.1.6.3.1.1.5.3',
	'ifIndex': '12',
	'ifAdminStatus': 'up',
	'ifOperStatus': 'down',
	'ifDescr': 'GigabitEthernet2/0/5',
}
```

根据拓扑管理收集的信息，补全端口描述信息

```
{
	'snmpTrapOID': '1.3.6.1.6.3.1.1.5.3',
	'ifIndex': '12',
	'ifAdminStatus': 'up',
	'ifOperStatus': 'down',
	'ifDescr': 'GigabitEthernet2/0/5',
	'ifAlias': 'TO-[QS-7F-HW-S2300]-Ethernet0/0/23'
}
```



## 链路状态发生变化



## OSPF邻居状态发生变化



### OSPF邻居DOWN上报的Trap消息类型

#### 华为-1 原始Trap消息

```
1.3.6.1.2.1.1.3.0 = 327951221
1.3.6.1.6.3.1.1.4.1.0 = 1.3.6.1.2.1.14.16.2.16
1.3.6.1.2.1.14.1.1.0 = 192.168.255.110
1.3.6.1.2.1.14.7.1.1.132.175.22.201.0 = 132.175.22.201
1.3.6.1.2.1.14.7.1.2.132.175.22.201.0 = 0
1.3.6.1.2.1.14.7.1.12.132.175.22.201.0 = 1
```

解析后Trap消息

```json
{
    'snmpTrapOID': '1.3.6.1.2.1.14.16.2.16',
	'ospfRouterId': '192.168.255.110',
	'ospfIfIpAddress': '132.175.22.201',
	'ospfAddressLessIf': '0',
	'ospfIfState': 'down',
}
```

补充关键字段：

```json
{
    'snmpTrapOID': '1.3.6.1.2.1.14.16.2.16',
	'ospfRouterId': '192.168.255.110',
	'ospfIfIpAddress': '132.175.22.201',
	'ospfAddressLessIf': '0',
	'ospfIfState': 'down',
	'ifIndex': 7034,
	'ifDescr': 'Vlan-interface23',
	'ifAlias': 'Vlan-interface23 Interface'
}
```

告警携带字段：



告警收敛效果：

通过某一个物理接口建立多个OSPF邻居的情况下，只通过





## 域名拨测类

```json
{
	'province': '',
	'currentValue': '',
	'labelId': '',
	'image': '',
	'neType': '',
	'eventId': '2189154388',
	'secondAlarmType': '',
	'application': '',
	'costCenter': '',
	'status': 'PROBLEM:',
	'business': '',
	'advice': '【处理建议】\r 【拨测 test.cs.cmos 解析地址应为1.1.1.2。若主备DNS同时产生"结果不是1.1.1.2"告警，则需排查分公司至本部承载网链路；】\r 【拨测 本省域名，解析地址应为DNS服务器自身地址；若拨测本省域名有问题，需排查DNS服务器自身问题】',
	'time': '2021-12-27 08:54:45',
	'host': '135.130.18.146',
	'ActionId': '905',
	'info': '135.130.18.146发生甘肃DNS备转发glb/智能DNS拨测_test.cs.cmos_结果不是1.1.1.2故障',
	'level': 5,
	'host_type': '',
	'project': '',
	'item': 'dns.nslookup[test.cs.cmos,135.130.18.146]',
	'firstAlarmType': '',
	'position': '',
	'lastTime': '2021-12-27 08:54:45'
}
```

```json
{
	'province': '',
	'currentValue': 'Name: restapi.ngcrmpfcore-gx.cs.gx.cmos Address: 10.182.2.107',
	'labelId': '',
	'image': '',
	'neType': '',
	'eventId': '2189195764',
	'secondAlarmType': '',
	'application': '',
	'costCenter': '',
	'status': 'PROBLEM:Name: restapi.ngcrmpfcore-gx.cs.gx.cmos Address: 10.182.2.107',
	'business': '',
	'advice': '【处理建议】\r 【拨测 test.cs.cmos 解析地址应为1.1.1.2。若主备DNS同时产生"结果不是1.1.1.2"告警，则需排查分公司至本部承载网链路；】\r 【拨测 本省域名，解析地址应为DNS服务器自身地址；若拨测本省域名有问题，需排查DNS服务器自身问题】',
	'time': '2021-12-24 17:26:53',
	'host': '10.182.2.142',
	'ActionId': '905',
	'info': '10.182.2.142发生广西DNS主自身拨测_restapi.ngcrmpfcore-gx.cs.gx.cmos_结果不是172.20故障',
	'level': 5,
	'host_type': '',
	'project': '',
	'item': 'dns.nslookup[restapi.ngcrmpfcore-gx.cs.gx.cmos,10.182.2.142]',
	'firstAlarmType': '',
	'position': '',
	'lastTime': '2021-12-24 17:26:53'
}
```



#### 优化协议类告警规则：有端口DOWN引起的协议DOWN，只产生端口DOWN告警

1. 基于拓扑自动发现中的链路数据实现告警收敛：
2. zabbix 上报告警的二次确认：

   1. 误告警及时恢复：

      1. 无需
2. 

# 任务管理



## 配置对比任务



### 静态路由对比



## 定时任务



### 周期定时任务



### 普通定时任务



### 即时任务



1. 周期定时任务：配置备份、自动化巡检
2. 定时任务：信息采集类
3. 立即