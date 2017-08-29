.. _AIUI_Features-label:

5. 功能特性
=============

AIUI的交互类似一个IO系统，I(Input)就是发送给AIUI的\ :ref:`AIUIMessage <aiuimessage-label>`\ ，
O(Output)就是AIUI抛出来的\ :ref:`AIUIEvent <aiuievent-label>`\ 。开发者通过集成AIUI SDK，
发送AIUIMessage、接收AIUIEvent。

通过前文介绍的AIUI的各种集成方式和AIUI SDK集成，你会发现AIUIServiceKit定义的\ :ref:`AIUIMessage <aiui_msg_def-label>`\ ,
\ :ref:`AIUIEvent <aiui_event_def-label>`\ 和串口SDK中定义的\ :ref:`AIUI控制消息 <uart_aiui_message_label>`\ ，
\ :ref:`AIUI结果事件<uart_aiui_event-label>`\ 非常类似，这主要源于AIUI希望提供给各种集成方式相似的接口，提高集成的便利。


除了AIUIMessage和AIUIEvent控制使用AIUI的功能外，还可以通过AIUI配置文件不同字段配置控制AIUI的功能。
通过AIUI配置文件和AIUIMessage的组合配置使用，可以使用AIUI提供的不同功能点。

下面将详细介绍

.. toctree::
	:maxdepth: 1
	:glob:
	
	../c05/*