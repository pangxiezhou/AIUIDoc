-----------------
8.4 串口工装测试
-----------------

AIUI模块在出厂前就会进行工装测试，测试录播音，串口及WIFI基本功能，所以上位机在集成AIUI模块后只需要录音及截幅测试。

按照如下步骤进行：

1. 在模块上安装AIUIHardwareTest.apk

2. 外部播音同时构造如下消息放入主控消息（参见\ :ref:`串口通信格式 <uart_control_msg-label>`\ 中主控消息）发送给AIUI:

	* 录音测试(\ :download:`录音测试播音文件 <rec.pcm>`\ )::
		
			{
				"type": "factory_test",
				"content": {
					"cmd": "rec_test",
					"mic_type": "5mic", //6麦值为6mic
				}
			}

	* 截幅测试(\ :download:`截幅测试播音文件 <vibration.wav>`\ )::
		
			{
				"type": "factory_test",
				"content": {
					"cmd": "vib_test",
					"mic_type": "5mic", //6麦值为6mic
				}
			}
        	
3. AIUI在完成测试后会将测试结果通过串口返回（参见\ :ref:`串口通信格式 <uart_aiuimsg-label>`\ 中AIUI消息），结果举例如下:

	* 录音测试结果::
		
		{
			"type": "factory_test_result",
			"content": {
				"test_result": "10-25 20:30:41.808 RecTest:RecTest start.\n10-25 20:30:41.811 RecTest:START_ALSA = pass.\n10-25 20:30:51.673 RecTest:REC_TEST = not pass, fail mic = 6\n10-25 20:30:51.673 RecTest:REC_TEST = not pass, fail mic = 2\n10-25 20:30:51.674 RecTest:REC_TEST = not pass, fail mic = 5\n10-25 20:30:51.674 RecTest:REC_TEST = not pass, fail mic = 1\n10-25 20:30:51.675 RecTest:REC_TEST = not pass, fail mic = 8\n10-25 20:30:51.675 RecTest:REC_TEST = not pass, fail mic = 4\n10-25 20:30:51.675 RecTest:REC_TEST = not pass, fail mic = 7\n10-25 20:30:51.676 RecTest:REC_TEST = not pass, fail mic = 3\n10-25 20:30:51.676 RecTest:REC_TEST RESULT = not pass.\n"
			}
		}   
		
	* 截幅测试结果::

		{
			"type": "factory_test_result",
			"content": {
				"test_result": "10-25 20:28:41.638 VibrationTest:VibrationTest start.\n10-25 20:28:41.644 VibrationTest:START_ALSA = pass.\n10-25 20:28:50.969 VibrationTest:VIBRATION_TEST = pass, mic = 6\n10-25 20:28:50.970 VibrationTest:VIBRATION_TEST = pass, mic = 2\n10-25 20:28:50.970 VibrationTest:VIBRATION_TEST = pass, mic = 5\n10-25 20:28:50.971 VibrationTest:VIBRATION_TEST = pass, mic = 1\n10-25 20:28:50.972 VibrationTest:VIBRATION_TEST = pass, mic = 8\n10-25 20:28:50.972 VibrationTest:VIBRATION_TEST = pass, mic = 7\n10-25 20:28:50.972 VibrationTest:VIBRATION_TEST = pass, mic = 3\n10-25 20:28:50.973 VibrationTest:VIBRATION_TEST RESULT = pass.\n"
			}
		}