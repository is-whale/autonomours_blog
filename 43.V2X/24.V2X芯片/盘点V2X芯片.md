- [C-V2X还有没有戏？盘点V2X芯片 (qq.com)](https://mp.weixin.qq.com/s/9XG4MTxbaH85VdBafPGcdw)

C-V2X一直是雷声大，雨点小甚至是没雨点。2022年6月10日，在匈牙利布达佩斯召开的3GPP RAN第96次会议圆满结束。在此次会议上，5G Rel-17标准宣布冻结，全球5G商用迈向新阶段。不过Rel-17基本与V2X没什么关系，Rel-17主要是优化资源分配、节电及支持全新频段，主要是降低功耗，节约能源。V2X要大幅度升级要等到2023年的Rel-18。

![](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhlOAXFX9MNRj9iaxNXDCx4zJ0L5qt3TeAIjde5gYC5acIVv8oYLkFibCtQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

Rel-18的Sidelink Relay对V2X提升比较明显。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhl65sAty5mW1qibna0fiaibiapHlkcE1ZfQbe9ficpOUH84kJmm989fXLL86g/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

Rel-18主要封包如下，截止于2021年12月，其中RP-213678和RP-213585对智能驾驶比较有用，可以减少组网密度，降低组网成本。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhl0wcDicqA033nTPhx8JvRWbbT89ODwsa90dTtvwTXJN5ARhfmIkib7eKA/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

高通还是手机领域的想法，不停挤牙膏，目前市面上主流的C-V2X芯片只支持Rel-15，高通已经规划到2027年的Rel-20了。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhlvCg020Bwibic0qM3k3llLnLSFD0HqKia0mmiavLVFEYF3nz9y26AiavQNYg/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

V2X最关键的部分是芯片，没有芯片支持，制定什么样的标准都毫无意义，只有成熟的廉价的芯片支持才能商业化落地，目前主流的V2X芯片组见下表。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhlX615EZhqF8ljsXWUjogoeiaXia6H0rBOdLF7Ut0SiboelSDS1w3Fv9JWQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

注意这些都是芯片组，不是单一芯片，包含了射频子系统和Modem。最独特的是CRATON2，它还能对应DSRC标准。SA2150P还包括了AP，是一套完整的平台。高通在2022CES展上也未展示SA525M，这是目前唯一对应Rel-16标准的芯片，目前最先进的芯片，应该在去年年底才量产。华为被打压失去造芯能力后，高通占据了绝大部分市场，估计市场占有率超90%，也掌握了绝对话语权，先进的芯片一定是高通最早推出。高通拥有完备的生态体系，中国的通讯模块厂家几乎都是高通稳定的合作伙伴。失去华为后，没有人能制衡高通，实际上即使华为具备造芯能力，想制衡高通也有难度。华为一贯不卖芯片组，只卖模块，没有形成强大的生态体系。这恐怕也是欧洲放弃C-V2X的原因之一。

## **智能驾驶与智能交通对V2X性能需求分析**

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhl1z1zbSaDpmOy3Zbb0EnShXYctN7BsovXTp6tiblHfbHXVuovtmLiamRA/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

Rel-14没什么价值，只能做V2V，也就是只有PC5直接通讯，Rel-15才略有价值，具备接入网络的能力。在80公里时速的车辆上，4G的典型延迟是165毫秒-300毫秒，做有关安全的V2X功能显然不能胜任，所以要和安全相关必须上5G，要做自动驾驶，那最好是可靠性比较高的Rel-18或Rel-19。

高通的座舱芯片可以做到一年一更新，那是因为座舱芯片的基础版本都来自手机和笔记本电脑领域，而车规级V2X几乎是全新的，车规级V2X芯片的研发周期是漫长的，大约2年，芯片再到量产车型时间更长，传统车厂需要3年，新兴造车需要2-3年。目前量产车型最先进的SA415M，这是2019年底的芯片。即便是新兴造车企业，汽车的更新速度也远不能与手机比，更不要说传统车厂基本上是7-8年才有一次大的升级。而3GPP还是按照手机业的模式，不停地快速迭代，手机行业自然希望消费者一年一换机，但汽车行业的通讯系统硬件做到3年一换都不可能，至少得4-5年，丰田之类成本极度敏感的厂家甚至是10年一换。尽管如此，全球范围内丰田还是卖得很好。

要想真的有C-V2X加入无人驾驶领域，恐怕要等到2028年以后了。

那么C-V2X的老对手DSRC过得如何？

2019年12月，美国FCC撤销了5.9GHz车联网技术DSRC专用频段并重新分配，FCC仁至义尽，自从1999年开始的为期二十年的DSRC独家开放期，是前所未有的优惠待遇；美国交通部花了近十亿美元测试，最终无疾而终。2020年10月，FCC将30MHz带宽全给了C-V2X，DSRC被扫地出门，留了1年的过渡期。很多人以为DSRC彻底完了，当然没有。从2019年开始DSRC开始升级，标准从802.11p升级到802.11bd。802.11bd背后是IEEE，它可不想彻底放弃DSRC。802.11bd大约在今年底完成，最大升级就是可以在5.9GHz或60GHz频带内使用，60GHz频带内是无需授权的免费频带，老的5.9GHz是欧洲在使用，向后兼容。实际即使宣布DSRC被废止后，美国交通部仍然在部署DSRC试验，2021年底的试验OBU如LXC-V2X-OBU-HERC，由日本电装提供，是DSRC与C-V2X双模式OBU。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhloiaqGpLsgmkYSY3UklqhYE6E9xLrr8CnmpC3ZSa3DWxnsU67X0zOzKQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

实际上FCC给C-V2X 30MHz的带宽也不够，只能支持到4G的C-V2X，如果是5G NR，那就需要400MHz，这么大的带宽只有60GHz以上才有可能免费提供。

在欧洲DSRC进展顺利，欧洲的4G网络还未完善，5G更是遥不可及，特别是那些不发达的东欧和南欧国家，5G几乎是不可能的，而只有5G能让V2X真正有价值，对这些国家来说DSRC是V2X最佳选择，也是唯一选择。

欧洲的DSRC网络即ITS-G5，其主要目标是提升道路安全，而非智能驾驶。因此其推动者是国家层面，奥地利高速公路运营商Asfinag在2020年开始铺设奥地利全国的DSRC网络，推进道路安全，这是欧洲第一个国家级DSRC网络，目前已基本建成，这个网络实际就是我国ETC收费DSRC网络的加强版。很多人可能不了解，中国是世界上最大的DSRC网络部署国家，因为中国的ETC使用的就是DSRC技术。从2007年交通运输部启动示范工程，到2015年ETC全国联网，中国的DSRC网络拥有超过2亿用户，超过1万个ETC节点（门架），近1亿的OBU安装。日本和中国类似，也是建成了基于DSRC的智能交通网络。



![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhlUwTxlaraMOWMnJw4u6ibzy8QOYewaDLGicIO3zfNb0asp23vtPia2gEtQ/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

802.11bd采用比较新的LPDC编码，MIMO天线，60GHz波段，波束整形，Midambles，性能大增，C-V2X有的，它也有。延迟方面再进一步，能够做到低于5毫秒，有效距离超过1公里，相对速度支持到时速500公里以上。

## **全球60GHz频谱**

![图片](https://mmbiz.qpic.cn/sz_mmbiz_jpg/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhlD5Ewic0XNkB3LYchO5sRC846IltsicKGz4k0f6ymAbshz0TjxDicREfYQ/640?wx_fmt=jpeg&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

C-V2X商业化的日程表想必大家已很清楚了，至少在欧洲和日本是遥遥无期。欧洲不排斥C-V2X，但目前还是以DSRC为主，欧洲的思路是融合。

![图片](https://mmbiz.qpic.cn/sz_mmbiz_png/2icOarNW84W7w4fViaAe3ZsPZOhiaHBjLhl8vaTyBjpQYPH1vZ4tV2D4K6A8qyX7bu5kCm1s4OeG0rRicR6sAiaib5Kw/640?wx_fmt=png&wxfrom=5&wx_lazy=1&wx_co=1)

图片来源：互联网

升级后的DSRC即802.11bd足够强大，在安全应用领域优势明显，在方便和舒适领域C-V2X优势明显，或许融合才是真正的出路。