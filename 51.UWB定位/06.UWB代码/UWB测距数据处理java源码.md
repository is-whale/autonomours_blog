- [UWB测距数据处理java源码_北京华星智控的博客-CSDN博客](https://blog.csdn.net/qq_35699674/article/details/111685518)

UWB测距是利用UWB技术实现点对点或者1对多测距的应用，华星智控UWB无线脉冲测距设备可以用于天车定位，人车防撞，远距离无线测距等应用，下面介绍下如何处理测距基站输出的数据。

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/e947f31803f0d83bc3648d5dad78230d.gif#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/20201225214640871.gif#pic_center)

处理收到的原始数据

```java
package Model;
import java.util.Vector;
import PublicMethod.DellMessage;
import PublicMethod.Jiaoyan;
/**
@@author 北京华星北斗智控技术
*/
public class DellHex implements Runnable {	
    //收到的原始数据集合，将收到的数据包拆分为2个字符一组放入该集合
    static Vector<String> hexvc=new Vector<>();
    //处理数据到什么状态了
    int usart_state=0;
    //数据位的长度
    int pack_len=0;
    //收到的数据类型
    String type="";//数据类型
    //线程休眠的时间
    int sleeptime=0;
    //完整的数据包
    static StringBuffer pack_cmd=new StringBuffer();
    /**向集合插入一条数据*/
    public static void  insert_adata(String hex) {
        hexvc.add(hex);
    }
    /**用于处理接收到的报文数据*/
    public  void dellhex() {
        //原始数据集合的长度
        int size=hexvc.size();
        //如果原始数据集合长度等于0代表没有数据，让线程休眠100毫秒等待集合有数据
        if(size==0) {
            sleeptime=100;
        }else {
            sleeptime=0;
            String hex=hexvc.get(0).toUpperCase();
            //将原始数据集合中的第一条数据取出放入swich循环
            switch(usart_state) {
                case 0:
                    //如果数据是55则执行	
                    if(hex.equals("55")) {
                        usart_state=1;
                        pack_cmd.append(hex);
                    }
                    break;
                case 1:
                    //如果数据是AA则执行	
                    if(hex.equals("AA")) {//状态1情况下，等待接收到AA包头2，然后变成状态2						
                        usart_state=2;
                        pack_cmd.append(hex);
                    }
                    break;
                case 2:
                    //数据类型给TYPE	
                    type=hex;
                    pack_cmd.append(hex);
                    usart_state=3;
                    break;
                case 3:
                    //报文数据段数据长度
                    pack_len=DellMessage.decodeHEX(hex);
                    pack_cmd.append(hex);
                    usart_state=4;
                    break;
                case 4:
                    pack_cmd.append(hex);
                    if(pack_cmd.length()==(pack_len*2+8)) {						
                        usart_state=0;	
                        int len=pack_cmd.length();
                        //校验
                        String check=Jiaoyan.check2(pack_cmd.toString());					
                        String panck_cmd=pack_cmd.toString().substring(len-2,len)+pack_cmd.toString().substring(len-4, len-2);
                        //如果校验成功
                        if(check.equals(panck_cmd)) {
                            //如果是测距数据
                            if(type.equals("01")) {
                                //给到处理测距数据的方法
                                DellMessage.dell_info(pack_cmd.toString());
                                //0A注册包
                            }else if(type.equals("0A")) {
                                //02心跳包
                            }else if(type.equals("02")) {
                                //07配置成功返回
                            }else if(type.equals("07")) {
                                //返回的配置信息
                            }else if(type.equals("03")) {
                                DellMessage.dell_peizhi_messge(pack_cmd.toString());
                            }
                        }
                        pack_cmd=new StringBuffer("");
                    }
                    break;
            }
            hexvc.removeElement(hexvc.get(0));
        }
    }


    /**启动线程的方法*/
    public void startThread() {
        Thread t=new Thread(this);
        t.start();
    }

    public void run() {
        while(true) {
            try { 
                dellhex();					
                Thread.sleep(sleeptime);//休眠时间
            } catch (InterruptedException e) {
                e.printStackTrace(); 
            }
        }
    }
}
```

处理收到的信息

```java
package PublicMethod;
import java.math.BigInteger;
import Model.DellHex;
import home.IFrameNew;
import home.ShowMessage;

public class DellMessage {

	static String baotou;//包头 0x55AA 
	static String zhilingtype;//指令类型0x01正常模式；0x02心跳包
	static String lenth;//数据长度17 1BYTE
	static String xuhao;//序号 1BYTE
	static String tagid;//标签id 2BYTE
	static String anchorid;//基站id
	static String distance10;//10进制距离
	static String power;//电量
	static String button;//按键
	static String baoliu;//保留
	static String distanceMessage;//实时距离信息
	static String r_version;//版本号2BYTE XX.XX
	static String r_id;//模块id 2Byte
	static String r_hz;//通讯间隔 2Byte
	static String r_tongxunshangxian;//单次通讯基站数量上限 2Byte
	static String r_tongxunxiaxian;//小组id 2Byte
	static String r_jiaozhunzhi;//距离校准值2Byte
	static String r_leixing;//模块类型2Byte
	static String r_zhudongceju;//基站主动测距2Byte
	static String r_alrm;//报警设备
	static String r_alrmdistance1;//报警距离1
	static String r_alrmdistance2;//报警距离2
	static String r_alrmdistance3;//报警距离3
	static String r_PAIR_ID;//配对ID无作用
	static String r_HEARTBEAT;//心跳包
	static String r_modubs;//modbus模式
	static String r_gonglv;//发射功率0x36
	static String r_imu_thres;//加速度计灵敏度0x38
	static String r_sleep_time;//静止休眠时间0x3a
	static String r_dong_enable;//震动使能0x3c
	static String r_imu_enable;//加速度计使能0x3e
	static String r_lubocanshu;//滤波参数
	static String zhiling01="01";//指令类型01代表正常模式
	static String zhiling02="02";//指令类型02代表心跳包
	static String zhiling03="03";//指令类型03代表参数读写模式
	static String zhiling="";
	static String  hexshow="";
	static String sudu="";
	static int info_lenth=0;
	static boolean succpeizhi=false;

	/**获取原始测距数据*/
	public static void getmessage(byte[] bytes) {
		int lenth=bytes2Hex(bytes).length()/2;
		for(int i=0;i<lenth;i++) {
			String hex=bytes2Hex(bytes).substring(2*i, (i+1)*2);
			DellHex.insert_adata(hex);
			hex=null;
		}
	}


	/**处理55AA01开头的测距信息*/
	public static String dell_info(String infom) {
		if(IFrameNew.isHexshow()) {//hex显示
			IFrameNew.getAre().append(GetNowTime.now()+"收: "+infom+"\n");
			IFrameNew.getAre().setCaretPosition(IFrameNew.getAre().getText().length());
		}	
		int size=infom.length()/2;
		//将信息2个字符1位保存在数组中
		String[] hex=new String[size];
		for(int i=0;i<size;i++) {
			hex[i]=infom.substring(i*2, 2+i*2);
		}
		//指令类型
		zhilingtype=hex[2];
		//数据长度
		lenth= String.valueOf(decodeHEX(hex[3]));
		//包序
		xuhao=String.valueOf(decodeHEX(hex[4]));
		//模块ID tagid
		tagid=hex[6]+hex[5];	
		//模块ID anchorid
		anchorid=hex[8]+hex[7];
		/**测距距离*/
		distance10=String.valueOf(decodeHEX(hex[12]+hex[11]+hex[10]+hex[9]));
		/**电量*/
		power=String.valueOf(decodeHEX(hex[13]));
		/**按键*/
		button=String.valueOf(decodeHEX(hex[14]));
		/**保留位*/
		baoliu=String.valueOf(decodeHEX(hex[15]));
		
		/**距离信息*/
		distanceMessage=anchorid+"距离"+tagid+": "+distance10+" cm";
		if(xuhao.length()==1) {
			xuhao="  "+xuhao;
		}else if(xuhao.length()==2) {
			xuhao=" "+xuhao;
		}
		StringBuffer juli=new StringBuffer( xuhao+" 基站:"+anchorid+" 标签:"+tagid+" 距离:"+distance10+" 电量:"+power+"%");
		//自动保存报文
		if(IFrameNew.isAutosave()) {
			SaveFIleTxt.insert_intxt(GetNowTime.timestamp2()+"收： "+juli.toString(),"报文");
		}
		if(IFrameNew.get_open()) {
			IFrameNew.getAre().append(GetNowTime.now()+"收: "+juli.toString()+"\n");
			IFrameNew.getAre().setCaretPosition(IFrameNew.getAre().getText().length());
		}	
		//如果处于自动校准模式并且该数据ID等于被测设备ID
		if(Jdialog.isStarjiaozhun() && Jdialog.getBeceid().equals(tagid)) {
			IFrameNew.getDistance_vector().add(distance10);
		}	

		hex=null;
		return  juli.toString();
	}
	public static String get_realldistance() {
		return distanceMessage;
	}

	/**处理读到的配置信息*/
	public static boolean dell_peizhi_messge(String mess) {
		String[] hex=Jiaoyan.hex(mess);
			//固件版本
			r_version=Jiaoyan.decodeHEX(hex[8])+"."+Jiaoyan.decodeHEX(hex[7]);
			//模块ID
			String r_ida=hex[10];
			String r_idb=hex[9];	
			if(r_ida.length()<2) {
				r_ida="0"+r_ida;
			}
			if(r_idb.length()<2) {
				r_idb="0"+r_idb;
			}
			r_id=r_ida+r_idb;
			//标签通讯间隔
			r_hz=String.valueOf(decodeHEX(hex[12]+hex[11]));
			r_tongxunshangxian=String.valueOf(decodeHEX(hex[14]+hex[13]));//单次通讯基站数量上限
			//小组id
			r_tongxunxiaxian=String.valueOf(decodeHEX(hex[16]+hex[15]));
			//距离校准值
			String r_jiaozhunzhi1=hex[18]+hex[17];
			//带符号十六进制转换十进制
			r_jiaozhunzhi=String.valueOf((Integer.valueOf(r_jiaozhunzhi1, 16).shortValue()));
			//距离校准值
			IFrameNew.setNowdis(Integer.parseInt(r_jiaozhunzhi));
			//模块类型
			r_leixing=String.valueOf(decodeHEX(hex[20]+hex[19]));
			//基站主动测距
			r_zhudongceju=String.valueOf(decodeHEX(hex[22]+hex[21]));
			//报警设备
			r_alrm=String.valueOf(decodeHEX(hex[24]+hex[23]));
			//报警距离1
			r_alrmdistance1=String.valueOf(decodeHEX(hex[26]+hex[25]));
			//报警距离2
			r_alrmdistance2=String.valueOf(decodeHEX(hex[28]+hex[27]));
			//报警距离3
			r_alrmdistance3=String.valueOf(decodeHEX(hex[30]+hex[29]));
			//r_modubs
			r_modubs=String.valueOf(decodeHEX(hex[36]+hex[35]));
			//发射功率0x36 54
			r_gonglv=String.valueOf(decodeHEX(hex[60]+hex[59]));
			//加速度计灵敏度38 56
			r_imu_thres=String.valueOf(decodeHEX(hex[62]+hex[61]));
			//静止休眠时间3a 58
			r_sleep_time=String.valueOf(decodeHEX(hex[64]+hex[63]));
			//振动使能3c 60
			r_dong_enable=String.valueOf(decodeHEX(hex[66]+hex[65]));
			//加速度计使能3e 62
			r_imu_enable=String.valueOf(decodeHEX(hex[68]+hex[67]));
			//滤波参数40 64
			r_lubocanshu=String.valueOf(decodeHEX(hex[70]+hex[69]));
			//速度限值48
			sudu=String.valueOf(decodeHEX(hex[78]+hex[77]));
			ShowMessage.zidingyi("读取配置成功！");
			IFrameNew.getAre().append(GetNowTime.now()+"读到配置信息: "+mess+"\n");
			IFrameNew.getAre().setCaretPosition(IFrameNew.getAre().getText().length());
			succpeizhi=true;
		return succpeizhi;
	}

	/**16进制转为10进制*/
	public static int decodeHEX(String hexs){
		BigInteger bigint=new BigInteger(hexs, 16);
		int numb=bigint.intValue();
		return numb;
	}

	/**32进制转为10进制*/
	public static String change32To10(String num) {
		int f=32;
		int t=10;
		return new java.math.BigInteger(num, f).toString(t);
	}

	/** 
	 * 将byte[]转为各种进制的字符串 
	 * @param bytes byte[] 
	 * @param radix 基数可以转换进制的范围，从Character.MIN_RADIX到Character.MAX_RADIX，超出范围后变为10进制 
	 * @return 转换后的字符串 
	 */ 
	public static String binary(byte[] bytes, int radix){  
		return new BigInteger(1, bytes).toString(radix);// 这里的1代表正数  
	} 

	private static final char[] HEXES = {
			'0', '1', '2', '3',
			'4', '5', '6', '7',
			'8', '9', 'a', 'b',
			'c', 'd', 'e', 'f'
	};

	/**
	 * byte数组 转换成 16进制小写字符串
	 */
	public static String bytes2Hex(byte[] bytes) {
		if (bytes == null || bytes.length == 0) {
			return null;
		}

		StringBuilder hex = new StringBuilder();

		for (byte b : bytes) {
			hex.append(HEXES[(b >> 4) & 0x0F]);
			hex.append(HEXES[b & 0x0F]);
		}

		return hex.toString();
	}

	/**截取指定长度的BYTE数组
	 * @param src原字节数组
	 * @param 开始位置
	 * @param 截取长度*/
	public static byte[] subBytes(byte[] src, int begin, int count) {
		byte[] bs = new byte[count];
		System.arraycopy(src, begin, bs, 0, count);
		return bs;
	}

	public static String getR_version() {
		return r_version;
	}

	public static String getR_id() {
		return r_id;
	}

	public static String getR_hz() {
		return r_hz;
	}

	public static String getR_tongxunshangxian() {
		return r_tongxunshangxian;
	}

	public static String getR_tongxunxiaxian() {
		return r_tongxunxiaxian;
	}

	public static String getR_jiaozhunzhi() {
		return r_jiaozhunzhi;
	}

	public static String getR_leixing() {
		return r_leixing;
	}

	public static String getR_zhudongceju() {
		return r_zhudongceju;
	}

	public static String getR_alrm() {
		return r_alrm;
	}

	public static String getR_alrmdistance1() {
		return r_alrmdistance1;
	}

	public static String getR_alrmdistance2() {
		return r_alrmdistance2;
	}

	public static String getR_alrmdistance3() {
		return r_alrmdistance3;
	}

	public static String getR_PAIR_ID() {
		return r_PAIR_ID;
	}

	public static String getR_HEARTBEAT() {
		return r_HEARTBEAT;
	}

	public static String getR_modubs() {
		return r_modubs;
	}
	/**获取功率*/
	public static String getR_gonglv() {
		return r_gonglv;
	}
	/**获取加速度计灵敏度*/
	public static String getR_imu_thres() {
		return r_imu_thres;
	}
	/**获取静止休眠时间*/
	public static String getR_sleep_time() {
		return r_sleep_time;
	}
	/**获取振动使能*/
	public static String getR_dong_enable() {
		return r_dong_enable;
	}
	/**获取加速度计使能*/
	public static String getR_imu_enable() {
		return r_imu_enable;
	}
	/**获取滤波参数*/
	public static String getR_lubocanshu() {
		return r_lubocanshu;
	}

	public static String getAnchorid() {
		return anchorid;
	}

	public static String getDistance10() {
		return distance10;
	}


	public static String getTagid() {
		return tagid;
	}

	public static String getHexshow() {
		return hexshow;
	}


	public static String getSudu() {
		return sudu;
	}


	public static boolean isSuccpeizhi() {
		return succpeizhi;
	}


	public static void setSuccpeizhi(boolean succpeizhi) {
		DellMessage.succpeizhi = succpeizhi;
	}

}
```

无线脉冲测距基站可以用于远距离相互测距，用于天车定位，斗轮机定位测距，有轨设备测距定位实现防撞和自动化控制等用途。

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/497c238e19c6298368a5e2fffaaa4825.png#pic_center)

## 产品特点

工业级设计，支持24小时*365天连续使用；

IP67防护等级，防水防尘，使用于室内室外各种恶劣环境；

[uwb定位stm32源码zip![img](https://csdnimg.cn/release/blogv2/dist/components/img/star.png)1星超过10%的资源748KB![img](https://csdnimg.cn/release/blogv2/dist/components/img/arrowDownWhite.png)下载](https://download.csdn.net/download/z273894270/12451613)

精准测距，10厘米级实时精准测距，无累计误差，无需校准；

超远距离，支持大于500米远距离实时测距；

支持RS485输出；

支持DC12-24V宽电压供电；

测距精度不受粉尘、雨雾影响；

快速安装，简单、方便、快捷；

支持一对多实时测距。

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/f7fa6e758799d22e379c11e8bf295516.png#pic_center)

测距模式：1对多测距模式可以实现1个基站对多个基站的测距，主基站距离每个从基站的距离将从主基站输出。

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/39f625e6bffaa1ed8fa780528147ad8c.png#pic_center)

多对多测距模式将能够实现多个主机站队多个从基站测距，从主基站输出距离从基站的距离。

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/cb466a66441b5f75cd3a8e99f98cef05.png#pic_center)

![在这里插入图片描述](https://img-blog.csdnimg.cn/img_convert/3f257d5e335544432c94c047497440bd.png#pic_center)