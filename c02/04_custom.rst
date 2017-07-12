2.4 自定义配置
==============

AIUI允许开发者通过本地配置文件或者云端语义技能的配置，改变增强AIUI默认能力。

2.4.2 唤醒词和发音人替换
------------------------

评估板默认唤醒词为\ *叮咚叮咚*\ ，合成的发音人为\ *萌萌（mengmeng）*\ ，如想体验其他效果，可以根据以下步骤进行更新

第一步：获取新资源
	登录\ `AIUI应用管理 <http://www.xfyun.cn/aiui/manage>`_\ ，进入应用配置界面，在语音资源中选择下载发音人
	及唤醒词资源。

第二步：拷贝资源
	将下载的资源拷贝到评估板，路径可自定义。 ::
	
		adb push xxxxxx.jet /sdcard/AIUI/xxxxxxxx.jet

第三步：更新配置
	AIUI首次启动后会自动在\ ``/sdcard/AIUI/cfg/aiui.cfg``\ 生成aiui.cfg文件，
	使用adb命令将文件导出：\ ``adb pull /sdcard/AIUI/cfg/aiui.cfg ./``\ 。
	
	修改配置文件中资源路径，确保和拷贝的资源路径一致::
	
		adb push aiui.cfg /sdcard/AIUI/cfg/aiui.cfg
		
	示例::
	
		// 注：资源在sdcard中，res_type一定要写path；
		// 多个资源间用“;”分割；（合成需要同时替换common.jet 和 发音人.jet）
		// push资源前先确保目录已经创建；
		//阵列参数---唤醒词资源
		"ivw":{
			"res_type":"path",
			"res_path":"/sdcard/AIUI/ivw/xxxxxxxx.jet"
		},
		// 合成参数
		"tts":{
			"engine_type":"local",
			"res_type":"path",
			"res_path":"/sdcard/AIUI/tts/common.jet;/sdcard/AIUI/tts/xiaoyan.jet ",
			"voice_name":"xiaoyan"
		},
		
	**注：资源在sdcard中，res_type一定要写path；**

第四步：重启
	重启系统：\ ``adb shell reboot``\ 或者在手机端点击“同步配置”都可以让配置加载生效。
	
	**注：如需恢复默认配置，删除/sdcard/aiui/cfg/aiui.cfg 后重启即可。**

2.4.3 自定义问答和语义配置
----------------------------

设置参考 `AIUI开放语义新手介绍 <http://aiui.xfyun.cn/info/platform>`_\ 。

2.4.4 常见问题
---------------

Q: 唤醒词替换后，无法唤醒
	A: 查看手机端的错误码信息并且AIUI模块开机后立即开始保存logcat日志3分钟，
	发送给\ :ref:`我们 <contact_us-label>`\ 。
	

Q: 发音人替换后其他声音改变，但是唤醒后还是萌萌的声音
	A: 目前处理唤醒为了更好的体验效果，播放的是录制好的音频，而不是合成的声音。所以换了合成资源，
	只会影响后面交互的合成播放。如需修改，可以修改开发包中开放的AIUIProductDemo中对应位置源码实现。
	
Q: 替换配置文件后，开机后提示APPID未配置
	A: 如果SD卡下的aiui.cfg配置文件不是\ **UTF-8无BOM的JSON**\ 格式，配置文件会被自动清空，导致开机后出现该提示。
	
Q: 替换发音人，闲聊技能下某些可不能播报，音乐天气等技能正常
	A：AIUI在闲聊的回答中会情感标签，目前仅有萌萌的发音人支持\ :ref:`情感合成 <emotion_tts-label>`\ ，所以替换不支持情感合成的发音人后，
	会导致不能播报的情况。
