6.1 外部输入
=============

6.1.1 相关Msg&Event
--------------------

**Config** ::

	"speech": {
		"audio_captor": "alsa",
		"data_source": "sdk",
		"wakeup_mode": "cae",
	},


**Message**

     +---------------------------+---------+------+--------------------------------------------------------------------------------------------+
     |CMD_WRITE                  |   2     |  无  | | 向AIUI服务写入数据                                                                       |
     +---------------------------+---------+------+--------------------------------------------------------------------------------------------+
     |CMD_STOP_WRITE             |   3     |  无  | | 停止写入数据                                                                             |
     +---------------------------+---------+------+--------------------------------------------------------------------------------------------+

.. _speech_config-label:
	 
6.1.2 speech配置
-----------------

speech中可以对录音参数进行配置:

\ ``data_source``\ 
	用于指定音频的来源
	
		sdk
			内部录音
		
		user
			外部通过\ ``CMD_WRITE``\ 写入音频
			
\ ``audio_captor``\
	指定内部录音（data_source为sdk）时的录音来源
	
		alsa
			Alsa录音机，录制原始音频
			
		system
			Android系统接口录音方式
			
有如下三种组合：

	1. sdk alsa（评估板默认模式）
	
	2. sdk system
	
	3. user 
			
另一配置参数\ ``wakeup_mode``\ 是由上面两个参数的配置决定的。

	sdk alsa（评估板默认模式）
		\ ``wakeup_mode``\ 为cae，由AIUI内部处理唤醒，AIUI评估板默认模式
		
	sdk system
		\ ``wakeup_mode``\ 为off，由外部通过，使用系统录音机，由外部通过\ :ref:`CMD_WAKEUP <wakeup_sleep-label>`\ 同步改变
		AIUI内部唤醒状态，通过\ :ref:`录音控制 <record_control-label>`\ 控制录音开始和停止。
		
	user
		根据用户通过\ ``CMD_WRITE``\ 写入数据的不同，\ ``wakeup_mode``\ 也不同。
		
			* 通过\ ``CMD_WRITE``\ 写入96k原始音频，\ ``wakeup_mode``\ 为cae，由AIUI通过原始音频处理唤醒。

			* 通过\ ``CMD_WRITE``\ 写入16k普通音频，\ ``wakeup_mode``\ 为off，由外部通过\ :ref:`CMD_WAKEUP <wakeup_sleep-label>`\ 同步改变AIUI内部唤醒状态。
		
AIUI只有在唤醒状态下才会处理用户的交互。如果\ ``wakeup_mode``\ 为off，那只有显式
调用\ :ref:`CMD_WAKEUP <wakeup_sleep-label>`\ 之后，后续的录音写入才会有效。


.. _data_write-label:

6.1.3 数据写入
-----------------

通过\ ``CMD_WRITE``\ 向AIUI服务写入数据，params字段中指定数据类型、采样率等描述信息，例如：”data_type=audio,sample_rate=16000”。
data字段为待写入的二进制数据（如音频、图像、文本等）。 params支持的描述字段如下：

 +---------------------------+-----------------------------------------------------------------------------------+
 |参数名称                   |   | 说明                                                                          |
 +---------------------------+-----------------------------------------------------------------------------------+
 |data_type                  |   | 数据类型，取值：                                                              |
 |                           |   | audio（音频）、text（文本）、image（图像，暂不支持）。                        |
 +---------------------------+-----------------------------------------------------------------------------------+
 |sample_rate                |   | 数据采样率，                                                                  |
 |                           |   | audio数据采样取值：16000（单通道）、96000（多通道）。                         |
 +---------------------------+-----------------------------------------------------------------------------------+
 |msc.lng和msc.lat           |   | GPS经纬度信息。                                                               |
 |                           |   | 当使用weather等需要位置信息的语义服务时可以传GPS坐标定位，                    |
 |                           |   | 在具有GPS模块的Android设备上可以不传。                                        |
 +---------------------------+-----------------------------------------------------------------------------------+
 |pers_param                 |   | 生效使用动态实体配置                                                          |
 |                           |   | 示例 pers_param={\"appid\":\"\", \"uid\":\"\", \"custom_key\":\"custom_val\"} |
 |                           |   | 具体含义参考\ :ref:`个性化 <individual-label>`\                               |
 +---------------------------+-----------------------------------------------------------------------------------+
 |rec_user_data              |   | 识别用的用户私有数据，用于增强特定词语的识别效果。                            |
 |                           |   | 示例：rec_user_data="{\"recHotWords\": \"播报内容|地图显示|                   |
 |                           |   | 路径优先\", \"sceneInfo\": {}}"。                                             |
 +---------------------------+-----------------------------------------------------------------------------------+

在指定为\ :ref:`外部录音 <speech_config-label>`\ 后，通过构造\ ``CMD_WRITE``\ 写入音频，params字段中
必须指定data_type为audio，sample_rate指定写入音频采样率。

除了可以写入音频外，也可以写入文本数据，data_type指定为text。写入的文本会直接作为语义的输入，影响交互
的语义上下文，并且如正常交互一样，返回对应的\ :ref:`语义结果 <nlp_result-label>`\ 。

代码示例::

	//写入音频
	byte[] audio;
	String params = "data_type=audio,sample_rate=16000";
	AIUIMessage msg = new AIUIMessage(AIUIConstant.CMD_WRITE, 0, 0, params, audio);
	mAIUIAgent.sendMessage(msg);
	
	//写入文本
	byte[] content= "确定预定".getBytes();
	String params = "data_type=text";
	AIUIMessage msg = new AIUIMessage(AIUIConstant.CMD_WRITE, 0, 0, params, content);
	mAIUIAgent.sendMessage(msg);

	
