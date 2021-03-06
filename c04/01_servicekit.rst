.. _AIUIServiceKitSDK-label:

4.1 AIUIServiceKit SDK
-----------------------

.. _AIUI_SDK_deep-label:

4.1.1 交互理解
^^^^^^^^^^^^^^^

AIUI旨在封装交互的复杂性，为开发者提供简单易用的接口。

AIUIServiceKit中提供的AIUIAgent就是和AIUIService交互的桥梁，通过发送不同的\ ``AIUIMessage``\ 控制AIUI的运行，
AIUI通过\ ``AIUIListener``\ 将不同的\ ``AIUIEvent``\ 抛出来给开发者程序解析。


4.1.2 接口说明
^^^^^^^^^^^^^^
.. _sendMessage-label:

AIUIAgent
	AIUIServiceKit中用于与AIUIService交互的接口类为\ ``AIUIAgent``\ 。

	\ ``AIUIAgent``\ 提供如下接口::

		//创建AIUIAgent实例
		static void createAgent(Context context, AIUIListener listener)
		
		//发送AIUI消息
		void sendMessage(AIUIMessage message)
		
		//销毁AIUIAgent实例
		void destroy()
		
		
AIUIListener
	创建\ ``AIUIAgent``\ 时传递的参数\ ``AIUIListener``\ 是用于接收AIUIService抛出事件的监听器。

	\ ``AIUIListener``\ 定义如下::

		interface AIUIListener
		{
		  void onEvent(AIUIEvent event);
		}

.. _aiui_event_def-label:

AIUIEvent	
	\ ``AIUIListener``\ 中监听的抛出事件的类型是\ ``AIUIEvent``\ 。

	\ ``AIUIEvent``\ 定义如下::

		class AIUIEvent
		{
		  int eventType; //事件类型
		  int arg1;      //参数1
		  int arg2;      //参数2
		  String info;
		  Bundle data;
		}
	
	AIUI定义了多种\ ``AIUIEvent``\，有不同\ ``eventType``\。当\ ``AIUIEvent``\ 取不同的\ ``eventType``\时，
	其余字段有不同的定义，详细定义请见\ :ref:`AIUIEvent <aiuievent-label>`\ 的定义说明。

.. _aiui_msg_def-label:
	
AIUIMessage
	AIUIAgent中\ ``sendMessage``\ 方法用于向AIUIService发送AIUI消息。消息类型是\ ``AIUIMessage``\ 。

	\ ``AIUIMessage``\ 定义如下::

		class AIUIMessage
		{
		   int msgType;   //消息类型
		   int arg1;      //参数1 默认空值 0
		   int arg2;      //参数2 默认空值0
		   String params; //默认空值 ""
		   byte[] data;   //默认空值 null
		}
		
	AIUI定义了多种\ ``AIUIMessage``\ ，有不同的\ ``msgType``\ 。当\ ``AIUIMessage``\取不同的\ ``msgType``\时，
	其他成员有不同的定义，详细定义解释请见\ :ref:`AIUIMessage <aiuimessage-label>`\ 的定义说明。



.. _aiui_servicekit_sdk-label:

4.1.3 调用流程
^^^^^^^^^^^^^^^

AIUIServiceKit的一般调用流程如下::

	AIUIListener listener = new AIUIListener() {

		@Override
		public void onEvent(AIUIEvent event) {
			switch (event.eventType) {
				//唤醒事件
				case AIUIConstant.EVENT_WAKEUP:	
				{
					break;
				}
				//结果事件（包含听写，语义，离线语法结果，定义和解析格式参见4.6 AIUIEvent一节）	
				case AIUIConstant.EVENT_RESULT:	
				{
					break;
				}
				//休眠事件
				case AIUIConstant.EVENT_SLEEP: 
				{
					break;
				}
				//错误事件
				case AIUIConstant.EVENT_ERROR: 
				{
					break;
				}
			}
	}


	AIUIAgent agent = AIUIAgent.createAgent(MainActivity.this, listener);
	
	//只是为了演示sendMessage的调用逻辑，放在此处并没有什么意义
	//agent.sendMessage(new AIUIMessage(AIUIConstant.CMD_RESET_WAKEUP, 0, 0, null, null);

	
具体的实现编码可以参考开发包中的AIUIDemo源码实现。