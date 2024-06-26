- [V2X之V2I介绍 | XuQi's Blog (xuqi1987.github.io)](https://xuqi1987.github.io/2019/07/07/V2X之V2I介绍/)

### V2I功能列表

#### 道路危险状况提示 HLW (Hazardous Location Warning)

**场景**

![image-20190715205112678](https://xuqi1987.github.io/2019/07/07/V2X%E4%B9%8BV2I%E4%BB%8B%E7%BB%8D/image-20190715205112678.png)

**HLW** 基本工作原理如下:

- 具备短程无线通信能力的路侧单元(RSU)周期性对外广播道路危险状况提示信息;
- HV 依据自身位置信息和道路危险状况提示信息，计算与道路危险区域的距离;
- HV 依据当前速度计算到达道路危险区域的时间;
- 系统通过 HMI 对驾驶员进行及时的预警。

**SLW 基本工作原理如下:**

- HV 分析接收到的 RSU 消息。提取限速路段信息和具体限速大小;

- 根据车辆本身的定位和行驶方向，将自身定位到特定路段上;

- 如果 HV 检测到自己处在限速路段区域内，则判断自身是否在限速范围内;

- 如果不满足限速要求，则触发 SLW 报警。系统通过 HMI 对 HV 驾驶员进行相应的限速预警，

  提醒驾驶员减速。

#### 闯红灯预警 RLVW (Red Light Violation Warning)

**场景**

![image-20190715205432858](https://xuqi1987.github.io/2019/07/07/V2X%E4%B9%8BV2I%E4%BB%8B%E7%BB%8D/image-20190715205432858.png)

**RLVW 基本工作原理如下：**

- RSU定时发送路口地理信息和信号灯实时状况信息。
- HV根据本身的GNSS信息，确定信号灯的相位，并计算他与停止线的距离。
- HV根据当前速度和其他交通参数预估到达路口的时间。
- RLVW将这些信息与接收到的红灯切换时刻及红灯保留信息进行对比分析，决定是否预警。

#### 弱势交通参与者碰撞预警(VRUCW:Vulnerable Road User Collision Warning)

**场景**

![image-20190715210933202](https://xuqi1987.github.io/2019/07/07/V2X%E4%B9%8BV2I%E4%BB%8B%E7%BB%8D/image-20190715210933202.png)

![image-20190715210950800](https://xuqi1987.github.io/2019/07/07/V2X%E4%B9%8BV2I%E4%BB%8B%E7%BB%8D/image-20190715210950800.png)

**VRUCW 基本工作原理如下:**

- HV 分析接收到的行人(P)消息，筛选出与车辆行驶方向上可能发生冲突的行人;
- 进一步筛选处于一定距离或者时间范围内的行人作为潜在威胁行人;
- 计算与每一个(或者成组)行人的碰撞时间 TTC(time-to-collision)，筛选出存在碰撞威胁 的行人;
- 若存在多个威胁行人(或行人组)，则筛选出最紧急的威胁行人(或行人组);
- 系统对 HV 驾驶员进行相应的碰撞预警。

#### 绿波车速引导GLOSA (Green Light Optimal Speed Advisory)

 GLOSA 应用将给予驾驶员一个建议车速区间，以使车辆能够经济地、舒适地(不需要停车等待) 通过信号路口。本应用适用于城市及郊区普通道路信号灯控制路口。 GLOSA 应用能辅助驾驶应用，提高车辆通过交叉路口的经济性和舒适性，提升交通系统效率。

**场景**：

![image-20190715211312462](https://xuqi1987.github.io/2019/07/07/V2X%E4%B9%8BV2I%E4%BB%8B%E7%BB%8D/image-20190715211312462.png)

**GLOSA 基本工作原理如下:**

- HV 根据收到的道路数据，以及本车的定位和运行数据，判定本车在路网中所处的位置和运 行方向;
- 判断车辆前方路口是否有信号灯，提取信号灯对应相位的实时状态;若有信号灯信息，则 可直接显示给驾驶员;
- GLOSA 应用根据本车的位置，以及信号灯对应相位的实时状态，计算本车能够在本次或下次绿灯期间不停车通过路口所需的最高行驶速度和最低行驶速度，并进行提示。

**车内标牌(IVS:In-Vehicle Signage)**

 车内标牌(IVS:In-Vehicle Signage)是指，当装载车载单元(OBU)的 HV 收到由路侧单元(RSU) 发送的道路数据以及交通标牌信息，IVS 应用将给予驾驶员相应的交通标牌提示，保证车辆的安全 行驶。本应用适用于任何交通道路场景。 IVS 能提高车辆行驶的安全性。

**场景：**

![image-20190715211625545](https://xuqi1987.github.io/2019/07/07/V2X%E4%B9%8BV2I%E4%BB%8B%E7%BB%8D/image-20190715211625545.png)

#### 前方拥堵提醒TJW (Traffic Jam Warning)

提醒驾驶员，前方拥堵，合理制定行车路线

![image-20190715211821300](https://xuqi1987.github.io/2019/07/07/V2X%E4%B9%8BV2I%E4%BB%8B%E7%BB%8D/image-20190715211821300.png)

**TJW 基本工作原理如下:**

- HV 根据收到的道路数据，以及本车的定位和运行数据，判定本车在路网中所处的位置和运行方向;
- 判断车辆前方道路是否有交通拥堵。若有，则直接提醒驾驶员。