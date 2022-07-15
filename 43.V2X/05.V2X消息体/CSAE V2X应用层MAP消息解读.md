- [CSAE V2X应用层MAP消息解读_哈哈不是嘿嘿的博客-CSDN博客_map消息](https://blog.csdn.net/qq_34432784/article/details/105903495#comments_19590424)

## **1.MAP消息简介**

MAP消息即地图消息，由路侧单元RSU（RodeSide Unit）广播，向车辆传递局部区域的地图信息。包括局部区域路口消息、路段消息、车道消息、道路之间的连接关系等。

下图为MAP消息的主体结构，是一个层层[嵌套](https://so.csdn.net/so/search?q=嵌套&spm=1001.2101.3001.7020)的格式，白色部分为必须包含的消息，灰色部分为可选消息。

![Map消息主体结构](https://img-blog.csdnimg.cn/20200503144725449.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDMyNzg0,size_16,color_FFFFFF,t_70)

在利用RSU广播Map消息时，由于Map消息层层嵌套，过于复杂，可采用"[xml](https://so.csdn.net/so/search?q=xml&spm=1001.2101.3001.7020) value" 格式对Map消息进行传递。下面将通过一个简单的Use Case 对Map消息进行解读。

## **2.利用xml对MAP消息进行描述**

```xml
<MapData>
	<msgCnt>0</msgCnt>
	<nodes>
		<Node>
			<id>
				<region>17</region>
				<id>13</id>
			</id>
			<refPos>
				<lat>233</lat>
				<long>456</long>
				<elevation>456</elevation>
			</refPos>
			<inLinks>
				<Link>
					<upstreamNodeId>
						<region>17</region>
						<id>12</id>
					</upstreamNodeId>
					<laneWidth>333</laneWidth>
					<movements>
						<Movement>
							<remoteIntersection>
								<region>17</region>
								<id>11</id>
							</remoteIntersection>
							<phaseId>27</phaseId>
						</Movement>
						<Movement>
							<remoteIntersection>
								<region>17</region>
								<id>14</id>
							</remoteIntersection>
							<phaseId>30</phaseId>
						</Movement>
					</movements>
					<lanes>
						<Lane>
							<laneID>23</laneID>
							<maneuvers>000000000011</maneuvers>
							<connectsTo>
								<Connection>
									<remoteIntersection>
										<region>17</region>
										<id>11</id>
									</remoteIntersection>
									<connectingLane>
										<lane>25</lane>
										<maneuver>000000000011</maneuver>
									</connectingLane>
								</Connection>
								<Connection>
									<remoteIntersection>
										<region>17</region>
										<id>14</id>
									</remoteIntersection>
									<connectingLane>
										<lane>26</lane>
										<maneuver>000000000011</maneuver>
									</connectingLane>
								</Connection>
							</connectsTo>
						</Lane>
					</lanes>
				</Link>
			</inLinks>
		</Node>
	</nodes>
</MapData>
```

## **3. 通过简单的Use Case对MAP消息进行解读**

![Use Case](https://img-blog.csdnimg.cn/20200503150006573.png?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM0NDMyNzg0,size_16,color_FFFFFF,t_70#pic_center)
msgCnt是发放方为发送的MAP消息进行编号，其值在0 - 127之间，在开始发送是随机选取数字，随后依次递增。配合时间戳timeStamp可以防止重放攻击，时间戳在本次实例中没有给出，一般通过读取GPS中的时间戳，然后加入到Map消息中进行发送。

MAP消息传递局部区域一系列的路口消息，即nodes，其包含一系列的Node，每个Node即一个路口节点，其消息包含了与上下游路口节点的连接关系。结合Use Case进行解读，第二节xml格式的MAP消息中的对图中nodeID为13的消息进行了描述。

id参数中的region参数表示节点所在区域的ID，本实例假设其为17.

id参数中的id参数当前节点的ID，本实例假设其为13.

refPos表示nodeID为13的节点的位置，用经度、纬度和海拔表示。

inLinks包含了一系列以nodeID 13为下游节点的上游节点的结合，本示例只列出了nodeID为12的上游节点的属性。

upStreamNodeID即表示上游节点的区域id和节点id。

laneWidth指上游节点id与当前节点id之前的的道路宽，按照图中应用两条道路，MAP消息只对其中一条进行了描述，具体在下文介绍。

movements指上游节点id和下游节点id定义的路段与下游路段的连接关系，由图中可知，当前路段的车可进行左转和直行，其对应的节点id（remoteIntersection）分别为11和14，进一步可得到相应的phaseID，其是MAP消息和SPAT消息的唯一联系，根据此id，可以查看SPAT消息中的信号灯数据，利用数据可以给驾驶提供驾驶辅助消息。

lanes指指上游节点id和下游节点id定义的路段，示例和MAP消息只对其中的id为23的lane进行了说明。

maneuvers指该路段能够进行的行为，000000000011指该路段能够左转和直行。具体可以在**T/CSAE 53-2017**文档进行查询。

connectsTo指该路段连接的下游路段，如果进行左转，其连接的远处节点id为11，连接的laneID为25，如果直行，其连接的远处节点id为14，连接的laneID为26.

## **4.总结**

本文对MAP消息进行了简单的解读，主要参考了**T/CSAE 53-2017**文档。