6.3 TTS合成
============

	无论是通过AIUIServiceKit或串口SDK集成AIUI，都可以使用AIUI提供的TTS能力。
	
	AIUIServiceKit SDK
		AIUIServiceKit SDK中包含\ ``SpeechSynthesizer``\ 合成接口。使用举例如下::
		
			//创建示例
			mTTS = SpeechSynthesizer.createSynthesizer(mContext, new InitListener() {
					@Override
					public void onInit(int errorCode) {
						
					}
				});
			
			//设置合成参数，关于更多可用参数参见\ `离线语音合成中的说明 <http://doc.xfyun.cn/msc_android/299552>`_
			mTTS.setParameter("emot", emotion);
				
			//使用合成
			mTTS.startSpeaking("合成测试开始", mTTSListener);
			//暂停
			mTTS.pauseSpeaking()
			//恢复
			mTTS.resumeSpeaking
			//停止合成	
			mTTS.stopSpeaking();
			
	串口SDK
		串口合成参见\ :ref:`文本合成 <uart_tts-label>`\ 。
			
			
6.3.1 情感合成
---------------

AIUI支持情感合成，但是需要特定支持情感合成的发音人资源，目前仅发音人\ *萌萌*\ 支持。

使用时需要通过\ ``setParameter``\ 设置\ ``emot``\ 参数，取值有neutral，happy，sorrow，分别代表
中立，高兴和悲伤。示例如下::

	mTTS.setParameter("emot", emotion);
	
串口情感合成支持参见\ :ref:`文本合成 <uart_tts-label>`\ 。

	
		
		
		
	