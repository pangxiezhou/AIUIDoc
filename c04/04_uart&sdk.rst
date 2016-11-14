.. _uart_sdk-label:

4.4 串口和SDK
-------------

看完之前 :ref:`核心板模式 <coreboad-label>` 和 :ref:`上位机模式 <ctrboard-label>` 中的基本开发流程的介绍后，
你可能感觉的到这是两种完全截然不同的开发接口和集成模式。

其实并不然，SDK是上位机串口模式的基础:

* 串口发送主控消息到\ :ref:`ControlService <demoboard_relationship-label>`\ 之后，type为\ `aiui_msg`\ 的消息都会
  把它转换成 :ref:`AIUIMessage <aiui_msg_def-label>` 然后通过SDK的接口 :ref:`sendMessage <sendMessage-label>` 发送到AIUIService。

* 串口接收到AIUIPacket也是ControlService接收到\ :ref:`AIUIEvent <aiui_event_def-label>`\ 之后，序列化成json封装成串口格式
  发送到上位机的。

.. _aiuimessage_uart_relation-label:

4.4.1 对应关系
^^^^^^^^^^^^^^^

* 串口主控消息和AIUIMessage

  串口主控消息::

	{
		"type": "aiui_msg",
		"content": {
			"msg_type": 8, //CMD_RESET_WAKEUP 重置 AIU 唤醒状态
			"arg1": 0,
			"arg2": 0,
			"params": ""
		}
	}

  SDK AIUIMessage::

	class AIUIMessage
	{
	   int msgType;   //消息类型
	   int arg1;      //参数1
	   int arg2;      //参数2
	   String params; 
	   byte[] data;
	}
	
.. _aiuievent_uart_relation-label:
	
* 串口AIUIPacket和SDK AIUIEvent

  串口AIUIPacket::
  
	{
		"type": "aiui_event",
		"content": {
			"arg1": 0,
			"arg2": 0,
			"eventType": 4, //唤醒消息
			"info": {
				"power": 36001498005504,
				"beam": 1,
				"CMScore": 147,
				"channel": 0,
				"angle": 99
			}
		}
	}
	
  AIUIEvent::
  
	class AIUIEvent
	{
	  int eventType; //事件类型
	  int arg1;      //参数1
	  int arg2;      //参数2
	  String info;
	  Bundle data;
	}
	
	
	


