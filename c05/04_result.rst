.. _aiui_result_parse-label:

5.4 结果解析
=================

5.4.1 Msg&Event
-------------------

**Event**
	
	+---------------------+-------+-----------------------------------------------------------------------------------------------+
	|eventType(事件类型)  |  取值 |    | 说明                                                                                     |
	+---------------------+-------+-----------------------------------------------------------------------------------------------+
	|EVENT_RESULT         |   1   |    | **结果事件**                                                                             |
	|                     |       |    |                                                                                          |
	|                     |       |    | data字段携带结果数据，info字段为描述数据的JSON字符串。                                   |
	+---------------------+-------+-----------------------------------------------------------------------------------------------+

5.4.2 AIUI结果格式
-------------------

AIUI交互结果中除了唤醒结果其他所有结果都通过\ ``EVENT_RESULT``\ 事件抛出，包括:

	* 听写结果(iat)
	* 语义结果(nlp)
	* 离线命令词结果(asr)
	* 后处理服务结果(tpp)
	
\ ``EVENT_RESULT``\ 的info字段中json包容如下格式的内容::
	
		{
			"data": [{
				"params": {
					"sub": "iat",
				},
				"content": [{
					"dte": "utf8",
					"dtf": "json",
					"cnt_id": "0"
				}]
			}]
		}
		
通过\ ``sub``\ 字段的值确定对应的结果类型，如果\ ``EVENT_RESULT``\ 包含的是语义结果，
那info数据描述的中\ ``sub``\ 的值就对应为\ ``nlp`` \。

\ ``EVENT_RESULT``\ 的data中包含结果的数据，需要根据info描述信息获取，不同SDK获取的方式有差异：

AIUIServiceKit SDK
	AIUIEvent定义中，data的类型为Bundle，获取结果数据的示例代码如下::
	
		private void processResult(AIUIEvent event) {
			JSONObject data = new JSONObject(event.info).getJSONArray("data").getJSONObject(0);
			String sub = data.getJSONObject("params").optString("sub");
			JSONObject content = data.getJSONArray("content").getJSONObject(0);

			if (content.has("cnt_id")) {
				String cnt_id = content.getString("cnt_id");
				JSONObject result = new JSONObject(new String(event.data.getByteArray(cnt_id), "utf-8"));
				
串口SDK
	AIUI结果事件，直接使用result字段对应的json即可，格式如下::
	
		{
			"content": {
				"result": {
					"sid": "cid6f1c6d19@ch00270cb15736010016",
					"text": {
						"bg": 0,
						"sn": 2,
						"ws": [{
							"bg": 0,
							"cw": [{"w": "","sc": 0,"wb": 0,"wc": 0,"we": 0}]
						}],
						"ls": true,
						"ed": 0
					}
				},
				"arg2": 0,
				"eventType": 1,
				"arg1": 0,
				"info": {
					"data": [{
						"content": [{"dtf": "json","cnt_id": "0","dte": "utf8"}],
						"params": {"sub": "iat",}
					}]
				}
			},
			"type": "aiui_event"
		}
	
5.4.3 听写结果格式
-------------------

5.4.3.1 示例
^^^^^^^^^^^^^^

举例如下::

	{
		"text": {
			"bg": 0,
			"sn": 1,
			"ws": [{
				"bg": 0,
				"cw": [{
					"w": "叫",
					"sc": 0
				}]
			},
			{
				"bg": 0,
				"cw": [{
					"w": "什么",
					"sc": 0
				}]
			},
			{
				"bg": 0,
				"cw": [{
					"w": "名字",
					"sc": 0
				}]
			}],
			"ls": false,
			"ed": 0
		}
	}

5.4.3.2 听写结果说明
^^^^^^^^^^^^^^^^^^^^^

+--------+-----------------+---------+--------------+
|JSON字段| 英文全称        |  类型   | 说明         |
+--------+-----------------+---------+--------------+
| sn     | sentence        | number  | 第几句       |
+--------+-----------------+---------+--------------+
| ls     | last sentence   | boolean | 是否最后一句 |
+--------+-----------------+---------+--------------+
| bg     | begin           | number  | 开始         |
+--------+-----------------+---------+--------------+
| ed     | end             | number  | 结束         |
+--------+-----------------+---------+--------------+
| ws     | words           | array   | 词           |
+--------+-----------------+---------+--------------+
| cw     | chinese  word   | array   | 中文分词     |
+--------+-----------------+---------+--------------+
| w      | word            | string  | 单字         |
+--------+-----------------+---------+--------------+
| sc     | score           | number  | 分数         |
+--------+-----------------+---------+--------------+

.. _nlp_result-label:

5.4.3 语义结果格式
-------------------

格式参考\ `AIUI开放平台语义协议 <http://aiui.xfyun.cn/info/protocol>`_。

5.4.4 离线命令词结果
---------------------

解析参考\ :ref:`离线命令词识别 <aiui_offline_cmd-label>`\ 。

5.4.5 后处理服务结果
---------------------

后处理结果由开发者配置的后处理服务构造。