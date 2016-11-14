7.3. 开发使用
-------------

7.3.1. 离线命令语法
^^^^^^^^^^^^^^^^^^^^

离线命令语法是使用巴科斯范式（BNF）描述诧音识别的语法。语法文件包括HEADER和BODY。

.. code-block:: none 

	!grammar main;
	!slot <sleep>;
	!start <sleep>;

	<sleep>:你去睡觉吧!id(2001)|你去休息吧!id(2001)|睡觉去吧!id(2001)
	
语法编写的具体文档可以参考讯飞云平台上\ `识别语法分享--在线语法和离线语法编写指南 <http://bbs.xfyun.cn/forum.php?mod=viewthread&tid=7595>`_。
章节附录也有AIUIProdutDemo使用离线语法示例。

7.3.2. 构建离线语法
^^^^^^^^^^^^^^^^^^^^

在AIUI中定义了CMD_BUILD_GRAMMAR命令构建语法。

SDK使用::

	AIUIMessage buildGrammar = new AIUIMessage(AIUIConstant.CMD_BUILD_GRAMMAR, 
		0, 0, grammar_str, null); //grammar_str为编写的离线语法
	mAIUIAgent.sendMessage(buildGrammar);
	
串口::

		//构造如下json，放入主控消息类型中发送给AIUI。

		{
			"type": "aiui_msg",
			"content": {
				"msg_type": 16, //CMD_BUILD_GRAMMAR  重置AIUI唤醒状态
				"arg1": 0,
				"arg2": 0,
				"params": "grammar_str" //grammar_str为编写的离线语法
			}
		}
		
每次调用命令构建的语法都会覆盖先前对应的GrammerID的离线语法。


7.3.3. 离线语法槽更新
^^^^^^^^^^^^^^^^^^^^^

在离线语法中有槽的定义(具体定义可以参考上面提到的语法文档)，主要作用就是在语法构建生效后，可以动态更新当前生效的语法中的部分内容。

定义了一个打电话的离线语法

.. code-block:: none 

	!slot contact
	!start <call>

	<call>:打电话给<contact>
	<contact>:张三|李四
	
已经通过AIUI构建了语法，并且使用了。假如当前通讯录有新联系人添加，那我们也要更新离线语法中(slot)槽contact的内容::

	{
		"name": "<contact>",     //槽名称
		"content":"张三\n李四\n王二\n" //词表内容
	}
	
使用命令CMD_UPDATE_LOCAL_LEXICON(17)，将params设置为上面的json，发送给AIUI即可。

SDK或者串口的具体使用方式可以参考上面构建语法时的操作。

.. _asr_result-label:

7.3.4. 结果解析
^^^^^^^^^^^^^^^

通过解析结果params下的sub字段，判断结果属于那种类型。

目前AIUI结果sub有iat（云端听写结果），nlp（云端语义结果），asr（离线识别结果）。

离线命令识别结果（asr）举例如下::

	"result": {
		"sid": "",
		"intent": {
			"ws": [{
				"slot": "<sleep>",
				"cw": [{
					"w": "你去睡觉吧",
					"id": 2001,
					"sc": 54,
				}]
			}],
			"rc": 0,
		}
	},
	"info": {
		"data": [{
			"params": {
				"sub": "asr"
			}
		}]
	}
	
主要解析intent中ws下的字段，根据先前编写的离线语法文件，很容易理解代表的字段。

id的字段跟语法中声明的对应，id的声明方法参考上面提到的语法文档。



