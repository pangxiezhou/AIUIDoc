.. _uart_android-label:

4.2.2 串口协议实现参考
-------------------------

4.2.2.1 Android实现
^^^^^^^^^^^^^^^^^^^^^

根据\ :ref:`通信协议和通信格式 <uart_protocal-label>`\ ，在Android平台进行了实现以供开发参考。
如果上位机平台是Android平台，可以直接集成使用。

Android SDK的接口设计同\ :ref:`AIUI SDK理解 <AIUI_SDK_deep-label>`\ 类似，通过串口架起与AIUIService交互的桥梁，
通过收发AIUI消息，获取AIUI结果和控制AIUI的运行。


4.2.2.1.1 权限
""""""""""""""""

**请确保应用有读写对应串口设备的权限（即对/dev/下的串口设备文件有rw权限）。**

4.2.2.1.2 调用流程
""""""""""""""""""

调用主要接口类是UARTAgent。

在程序首次初始化的地方调用静态方法createAgent创建UARTAgent实例，传入EventListener参数用于接收串口事件，
后面调用创建的UARTAget实例的sendMessage方法发送串口消息，在程序结束前调用UARTAgent实例的destroy方法释放资源。

4.2.2.1.3 接口说明
""""""""""""""""""

UARTAgent
	包含如下接口::
	
		//创建UARTAgent
		UARTAgent createAgent(String device, int speed, EventListener listener)

		//发送串口消息
		boolean sendMessage(MsgPacket reqPacket)

		//销毁串口，释放资源
		void destroy()

EventListener
	创建UARTAgent传入的listener，用于监听串口的状态和数据回调，定义如下::
	
		interface EventListener {
			void onEvent(UARTEvent event);
		}
		
UARTEvent
	UARTEvent即为串口事件，定义如下::
	
		class UARTEvent {
			int eventType; //串口事件类型
			Object data;   //串口事件数据
		}
	
	具体事件处理参见\ :ref:`代码示例 <uart_android_code-label>`\ 。
		
MsgPacket
	MsgPacket是构造发送给AIUI的消息的类型，可以通过PacketBuilder提供的静态工具方法很方便的构造：
	
		* 构造AIUI配置请求数据包::

			MsgPacket obtainAIUIConfPacket(String appid, String key, String sence, boolean launchDemo)

		* 构造获取WIFI状态请求数据包::

			 MsgPacket obtainWIFIStatusReqPacket()
			 
		* 构造AIUI控制请求数据包::

			 MsgPacket obtainAIUICtrPacket(int msgType, int arg1, int arg2, String params)

	
	更多消息类型的构造可以参考源码自行实现。
	
.. _uart_android_code-label:

4.2.2.1.6 代码示例
""""""""""""""""""""

下面代码示例了上位机集成使用的通用流程（更全面的代码参考开发包下的UARTKitCtrDemo)::

	mAgent = UARTAgent.createAgent("/dev/ttyS2", 115200, new EventListener() {

		@Override
		public void onEvent(UARTEvent event) {
			switch (event.eventType) {
				case UARTConstant.EVENT_INIT_SUCCESS:              //处理初始化成功事件
					Log.d(TAG, "Init UART Success");
					break;

				case UARTConstant.EVENT_INIT_FAILED:               //处理初始化失败事件
					Log.d(TAG, "Init UART Failed");
					break;

				case UARTConstant.EVENT_MSG:                       //消息回调事件
					MsgPacket recvPacket = (MsgPacket) event.data;
					processPacket(recvPacket);
					break;

				case UARTConstant.EVENT_SEND_FAILED:               //消息发送失败事件
					MsgPacket sendPacket = (MsgPacket) event.data;
					mAgent.sendMessage(sendPacket);
					break;
				default:
					break;
			}
		}
	});

	//消息处理
	private void processPacket(MsgPacket packet) {
		switch (packet.getMsgType()) {
			case MsgPacket.AIUI_PACKET_TYPE:
				Log.d(TAG, "recv aiui result" + new String(((AIUIPacket) packet).content));
				break;
			default:
				break;
		}
	}

	//发送AIUI配置信息
	if(intent.hasExtra("aiui_conf")){
		mAgent.sendMessage(PacketBuilder.obtainAIUIConfPacket("appid", "key", "main", false));
    }
	
4.2.2.2 其他平台实现
^^^^^^^^^^^^^^^^^^^^^

如果上位机是Android的话，那上位机集成开发包中串口SDK UARTKit就能很方便的开发。

如果上位机是其他平台，可以先通过\ `AIUI串口历险记 <http://bbs.xfyun.cn/forum.php?mod=viewthread&tid=17728&extra=page%3D1>`_\ 熟悉AIUI
串口协议的一些具体内容，再参考\ :ref:`串口Android SDK的示例 <uart_android_code-label>`\ 熟悉处理流程。

在Windows上也有分享的AIUI串口工具，参见\ `AIUI串口调试助手 <http://bbs.xfyun.cn/forum.php?mod=viewthread&tid=26336>`_\ 。