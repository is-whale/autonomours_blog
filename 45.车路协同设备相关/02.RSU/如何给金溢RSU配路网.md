- [如何给金溢RSU配路网_aFakeProgramer的博客-CSDN博客](https://blog.csdn.net/usstmiracle/article/details/100072861)

## **一、路网数据采集：**

利用车载单元OBU的GPS定位模块在特定的路段，选取两个点，一个设定为起点，一个终点（一般大概300~400米左右）中间不能有实体将RSU的信号遮挡。

配置的时候，将定位模块放在车顶最中间的位置，车停在道路中间，（下车查看，GPS定位模块大致和路的中心线在一条直线上。）

在obu的home目录下输入gpsmon命令，可以读出GPS数据。

![img](https://img-blog.csdnimg.cn/2019082610362834.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3Vzc3RtaXJhY2xl,size_16,color_FFFFFF,t_70)

 

其中RMC和GGA是RTCM的不同数据格式。这个数据需要解析。

通过`tail -f v2x_log |grep TPM `可以查看到RTCM数据通过gpsd解析后的数据。

解析后的消息：

```json
8-19 02:56:29.185614:[D]SDH->:rx tpi tpm:{"TPM":{"time":1566183388.2,"lat":30.8685588,"lng":121.9200416,"elv":5.6,"speed":0.005,"head":0.0,"err_elv":22.08}}
```

踩点时需要注意，经纬度数字不能跳动，需要记录一个稳定的准确值。

海拔这个数据此次在这次临港项目中没有使用。

## **二、获得路网数据**

此次临港配路网需要采5个点：

十字路口红绿灯的位置定位数据和2段路网的起点和终点。

第一个经纬度是十字路口的红绿灯经纬度

第二个links下面的是二段路网的经纬度

红绿灯：

```json
"lat":30.8696293, "lng":121.9186736,
Link1  "lat":30.8685588,"lng":121.9200416," 起点
Link1  "lat":30.8693338,"lng":121.9191535 终点
Link2   lat":30.8696108,"lng":121.9186771,"终点
Link2  lat":30.8696108,"lng":121.9186981,"终点
```

限速等数据（临港测试场限速40）

## **三、写入路网数据**

连接RSU的WIFI，将采集好的数据写入RSU工程目录的map.data文件。

```javascript
{ //使用时需要去掉注释
  "MAP":
  {
    "nodes":
    [
      {
        "node_id":1,
        "name":"node1",
		"lat":30.8696293,  红绿灯经纬度  
		"lng":121.9186736,
        "ele":460.6,
        "links":
        [
          {
            "name":"Link1",
            "up_node_id":0,
            "lane_width":6,
            "points":
            [
              {
				"lat":30.871344,   路段1的起点
				"lng":121.916693
              },
              {              
				"lat":30.8696293,   路段1的终点
				"lng":121.9186736
              }
            ],
            "limits":
            [
              {
                "type":0,
                "speed":11.1                
              }
            ],
            "moves":
            [
              {
                "node_id":1,
                "phase_id":1
              }
            ],
            "lanes":
            [
              {
                "id":1
              },
			  {
                "id":2
              }
            ]
          },
		  {
            "name":"Link2",
            "up_node_id":0,
            "lane_width":6,
            "points":
            [
              {
				"lat":30.8685275,
				"lng":121.9200783
              },
              {              
				"lat":30.8693471,
				"lng":121.919146
              }
            ],
            "limits":
            [
              {
                "type":0,
                "speed":11.1                
              }
            ],
            "moves":
            [
              {
                "node_id":1,
                "phase_id":1
              }
            ],
            "lanes":
            [
              {
                "id":1
              },
			  {
                "id":2
              }
            ]
          } 		  
        ]
      }
    ]
  }
}
```

![img](https://img-blog.csdnimg.cn/20190826103644613.png)

 

配置好数据后，重新启动RSU，即可。

## 四：检查是否配置成功

注意点：连接RSU看RSU是否在发送MAP消息

```bash
[root@genvict v2x_log]# tail -f v2x_log |grep MAP

2019-08-19 03:38:38.374523:[D]AI_SPAT->:tx wmh spat:[193]{"SPAT":{"inters":[{"node_id":1,"status":32,"phases":[{"id":1,"ph_states":[{"lights":3,"start_time":0,"likely_time":4}]},{"id":2,"ph_states":[{"lights":5,"start_time":0,"likely_time":4}]}]}]}}
```

连接OBU看OBU是否收到RSU发送过来的SPAT消息：

```bash
[root@genvict log]# tail -f v2x_log |grep SPAT
```

这是WMH的发送出来的。

```bash
2019-08-19 03:42:35.151215:[D]WMH->:tx spat_r:{"SPAT":{"src":2,"inters":[{"node_id":1,"status":32,"phases":[{"id":1,"ph_states":[{"light":3,"start_time":0.0,"likely_time":9.0}]},{"id":2,"ph_states":[{"light":5,"start_time":0.0,"likely_time":9.0}]}]}]}}
```

Phases相位，红绿灯的十字路口，南北向的为同一个相位，东西向的为一个相位。