.. _event_result-label:

6. AIUI结果
==============

AIUI结果是指\ :ref:`AIUIEvent <aiuievent-label>`\ 中eventType为1(EVENT_RESULT)的结果事件。

.. seealso::

	AIUIEvent的字段定义参见\ :ref:`AIUIEvent定义 <aiui_event_def-label>`\ 。
	
eventType为1(EVENT_RESULT)时，info成员中包含了结果的一些描述信息（如结果类型，结果编码，结果的key等）。举例如下::

	{
		"data": [{
			"params": {
				"sub": "iat",  //重要字段
				"lrst": false,
				"rstid": 1
			},
			"content": [{
				"dte": "utf8",
				"dtf": "json", //dtf -> data format(结果编码)。
				"cnt_id": "0"
			}]
		}]
	}
	
sub字段指明结果的类型，目前根据sub字段区分三种结果：

* iat  
	听写结果，详情请参见\ :ref:`iat_result-label`

* nlp  
	语义结果，详情请参见\ :ref:`nlp_result-label`

* asr  
	离线识别结果，详情请参见 \ :ref:`asr_result-label`



本章节包含以下小节

.. toctree::
	:maxdepth: 1
	:glob:
	
	../c06/*
	
