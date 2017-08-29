6.2 用户个性化
==============

.. _individual-label:

6.2.1 Config&Msg&Event
----------------------

**Config**  ::

		"audioparams": {
			"pers_param":"{\"appid\":\"\", \"uid\":\"\", \"custom_key\":\"custom_val\"}",
		}

**Message**

    +---------------------------+---------+------+--------------------------------------------------------------------------------------------+
    |msgType（消息类型）        |  取值   | 返回 | | 说明                                                                                     |
    +---------------------------+---------+------+--------------------------------------------------------------------------------------------+
    |CMD_SET_PARAMS             |  10     |  无  | | **设置参数配置**                                                                         |
    |                           |         |      | |                                                                                          |
    |                           |         |      | | 用params携带参数设置JSON字符串，                                                         |
    |                           |         |      | | 具体格式参照aiui.cfg文件，实时生效                                                       |
    |                           |         |      | |                                                                                          |
    +---------------------------+---------+------+--------------------------------------------------------------------------------------------+
    |CMD_SYNC                   |  13     |  有  | | **向AIUI服务同步数据**                                                                   |
    |                           |         |      | |                                                                                          |
    |                           |         |      | | arg1表示同步的数据类型                                                                   |
    |                           |         |      | | data表示同步数据的内容                                                                   |
    |                           |         |      | |                                                                                          |
    +---------------------------+---------+------+--------------------------------------------------------------------------------------------+
    |CMD_QUERY_SYNC_STATUS      |  24     |  有  | | **查询数据同步状态**                                                                     |
    |                           |         |      | |                                                                                          |
    |                           |         |      | | arg1表示状态查询的类型                                                                   |
    |                           |         |      | | params包含查询条件                                                                       |
    |                           |         |      | |                                                                                          |
    +---------------------------+---------+------+--------------------------------------------------------------------------------------------+

	
6.2.2 使用场景
---------------

AIUI开放平台支持开发者DIY自己的\ `自定义技能 <http://aiui.xfyun.cn/info/guide#100>`_\ ，以满足平台内置技能不能满足的需求。

在自定义技能中，开发者可以将语法定义中的语义槽与某个实体关联，实体类似一个包含所有支持说法的字典，比如开放平台支持内置的开放
实体艺术家\ ``IFLYTEK.Artist``\ 就涵盖了很多歌手的名字和对应昵称等这些说法。

除了内置的开放实体，AIUI开放平台也支持开发者定义新的实体，在后台添加对应的支持的说法，这种实体为静态实体。另外支持开发者只在后台
定义实体的数据格式，然后通过AIUI客户端接口动态上传支持说法的具体内容，这种实体为动态实体。下面提到的接口的作用即为动态上传和使用
动态实体。

自定义技能和实体的更多细节参考AIUI开放平台上\ `自定义技能 <http://aiui.xfyun.cn/info/guide#100>`_\ 。

6.2.3 动态实体
---------------

6.2.3.1 动态实体定义
^^^^^^^^^^^^^^^^^^^^^

一个动态实体的定义可以包含多个资源Resource的定义，资源定义中包含了资源名称，以及从客户端上传数据抽取说法的字段名以及生效的维度。
生效的维度目前有用户级(uid)、应用级(appid)、自定义级，在客户端上传对应的资源数据时，维度信息需要和定义时保持一致。

.. _upload_schema-label:

6.2.3.2 动态上传资源数据
^^^^^^^^^^^^^^^^^^^^^^^^^

通过\ ``CMD_SYNC`` \ 上传同步资源数据，arg1表示同步的数据类型，动态实体对应\ ``SYNC_DATA_SCHEMA``\ （常量对应值为3）。
data为同步JSON内容的utf-8二进制数据。
JSON内容示例如下::

	{
		"param": {
			"appid": "56ac196f",			// 雅典娜开放平台appid
			"id_name": "uid",			    // 维度
			"id_value": "",                 // 维度具体值，当维度取uid或appid时，该值可取空，AIUI会自动补全
			"res_name": "user_applist"	    // 资源名称
		},
		"data": "xxxxxx"		            // 与schema名称对应的数据内容base64编码
	}

\ ``id_name``\ 与动态实体定义资源时指定的维度对应，如果定义时是用户级，那此处\ ``id_name``\ 就对应\ ``uid``\ 。
\ ``id_value``\ 是维度具体值，如\ ``id_name``\ 为uid，\ ``id_value``\ 就需要是该资源针对生效的用户的具体UID，AIUI会使用
当前用户UID进行补全，appid同理。自定义维度因为是由开发者自定义，所以\ ``id_name``\ 、\ ``id_value``\ 都需要设置具体值。


通过指定\ ``id_name``\ 、\ ``id_value``\ ，上传的动态实体资源数据到服务端就有了唯一的从属，在\ :ref:`生效使用 <individual_work-label>`\ 
时就可以指定此处的\ ``id_name``\ 、\ ``id_value``\ 的值就能生效使用当前上传的动态实体资源。

data中是原始资源数据的base64编码内容。原始资源数据是包含多条json记录的文本，通过动态实体定义时的抽取字段名可以从每条
json记录中抽取出定义支持的说法。

如定义资源时数据抽取的主列名为appName，别名为alias，那就要确保每条json记录中都要包含如上的字段（可以包含冗余字段，如下面的extra字段），
示例如下::

	{"appName": "微信"， "alias": "wechat", "extra": "xxx"}
	{"appName": "新浪微博"， "alias": "微博", "extra": "yyy"}
	{"appName": "Telegram"， "alias": "电报", "extra": "zzz"}
	{"appName": "讯飞输入法"， "alias": "", "extra": "uuu"}
	
data的实际内容是将如上数据进行base64编码后的结果。

同步上传的代码示例如下::

	JSONObject syncSchemaJson = new JSONObject();
				
	JSONObject paramJson = new JSONObject();
	
	paramJson.put("appid", "56ac196f");
	paramJson.put("id_name", "uid");
	paramJson.put("res_name", "user_applist");
	
	syncSchemaJson.put("param", paramJson);
	syncSchemaJson.put("data", Base64.encodeToString(FileUtil.readAssetsFile(context, "file_path"),  Base64.DEFAULT | Base64.NO_WRAP));
	
	// 传入的数据一定要为utf-8编码
	byte[] syncData = syncSchemaJson.toString().getBytes("utf-8");
	
	AIUIMessage syncAthenaMessage = new AIUIMessage(AIUIConstant.CMD_SYNC, 
					AIUIConstant.SYNC_DATA_SCHEMA, 0, "", syncData);
					

\ ``CMD_SYNC`` \ 完成后会有\ ``EVENT_CMD_RETURN``\ 事件回调，可以获取该操作对应的sid，便于后面查询使用::

	private void processCmdReturnEvent(AIUIEvent event) {
		switch (event.arg1) {
			case AIUIConstant.CMD_SYNC: {
				int dtype = event.data.getInt("sync_dtype");
				
				//arg2表示结果
				if (0 == event.arg2) {		// 同步成功
					if (AIUIConstant.SYNC_DATA_SCHEMA == dtype) {
						mSyncSid = event.data.getString("sid");
						showTip("schema数据同步成功，sid=" + mSyncSid);
					} 
				} else {
					if (AIUIConstant.SYNC_DATA_SCHEMA == dtype) {
						mSyncSid = event.data.getString("sid");
						showTip("schema数据同步出错：" + event.arg2 + "，sid=" + mSyncSid);
					} 
				}
			} break;
		}
	}
					
						
6.2.3.3 查询打包状态
^^^^^^^^^^^^^^^^^^^^^^

通过\ ``CMD_SYNC``\ 上传同步动态实体的资源数据后，AIUI服务端会进行处理然后生效，处理的过程是异步的，可以通过\ ``CMD_QUERY_SYNC_STATUS``\ 
查询上传的资源数据是否处理成功。

arg1表示状态查询的类型，动态实体对应\ ``SYNC_DATA_SCHEMA``\ （常量对应值为3），params为json，包含需要对应同步上传操作的sid，示例如下::

	JSONObject paramsJson = new JSONObject();
	paramsJson.put("sid", mSyncSid);
		
	AIUIMessage querySyncMsg = new AIUIMessage(AIUIConstant.CMD_QUERY_SYNC_STATUS, 
									AIUIConstant.SYNC_DATA_SCHEMA, 0, 
									paramsJson.toString(), null);
	mAIUIAgent.sendMessage(querySyncMsg);
	
	
\ ``CMD_QUERY_SYNC_STATUS``\ 执行完成后会有\ ``EVENT_CMD_RETURN``\ 事件回调，表示查询结果，解析示例如下::

	private void processCmdReturnEvent(AIUIEvent event) {
		switch (event.arg1) {
		    //schema数据打包结果查询结果
			case AIUIConstant.CMD_QUERY_SYNC_STATUS: {	
				int syncType = event.data.getInt("sync_dtype");
				
				if (AIUIConstant.SYNC_DATA_QUERY == syncType) {
					String result = event.data.getString("result");
					
					if (0 == event.arg2) {
						showTip("查询结果：" + result);
					} else {
						showTip("schema数据状态查询出错：" + event.arg2 + 
								", result:" + result);
					}
				}
			} break;
		}
	}

.. _individual_work-label:
						
6.2.3.4 生效使用
^^^^^^^^^^^^^^^^^

通过\ ``CMD_SET_PARAMS``\ 设置\ ``pers_param``\ 即可使用已设置的动态实体（\ ``CMD_SET_PARAMS``\ 具体用法参见\ :ref:`动态配置 <set_params-label>`），
\ ``pers_param``\ 的示例如下::

	"audioparams": {
		"pers_param":"{\"appid\":\"\", \"uid\":\"\", \"custom_key\":\"custom_val\"}",
	}	

如果需要在本机器上生效当前应用对应的所有应用级的动态实体，在\ ``pers_param``\ 加入\ ``\"appid\":\"\"``\ （值留空，
AIUI中会自动补全appid和uid的值），同理用户级动态实体生效需要加入\ ``\"uid\":\"\"``\。

对于自定义维度需要用后台定义实体时的自定义维度名作为key，使用\ :ref:`动态上传 <upload_schema-label>`\ 指定的自定义维度作为值。如
后台定义的自定义维度名为\ ``vendor``\ ，那在\ :ref:`动态上传 <upload_schema-label>`\ 时就需要构造如下数据进行上传::

	{
		"param": {
			"appid": "56ac196f",			// 雅典娜开放平台appid
			"id_name": "vendor",			// 自定义维度名
			"id_value": "spec_vendor",      // 自定义维度value
			"res_name": "user_applist"	    // 资源名称
		},
		"data": "xxxxxx"		            // 与schema名称对应的数据内容base64编码
	}

那对应需要在交互时使用该自定义维度对应的动态实体就需要加入\ ``\"vendor\":\"spec_vendor\"``\ 。


除了通过\ ``CMD_SET_PARAMS``\ 设置\ ``pers_param``\ ，也可以在\ :ref:`写入音频 <data_write-label>`\ 时设置该参数。

6.2.4 所见即可说
------------------

6.2.4.1 所见即可说定义
^^^^^^^^^^^^^^^^^^^^^^^^^

动态实体还有一种特殊的维度，名为所见即可说。该种动态实体上传的内容只会生效使用一次，在上传后生效交互一次后，即会恢复无数据的状态。
该维度适用于一些临时数据，如只在当前屏幕显示的信息，在上传当前屏幕内容数据后，用户根据当前屏幕的内容进行交互，交互过后屏幕内容即会刷新，
更多示例和解释参考AIUI开放平台上\ `自定义技能 <http://aiui.xfyun.cn/info/guide#100>`_\ 。


6.2.4.2 动态上传资源数据
^^^^^^^^^^^^^^^^^^^^^^^^^

通过\ ``CMD_SYNC`` \ 上传同步资源数据，arg1表示同步的数据类型，所见即可说对应\ ``SYNC_DATA_SPEAKABLE``\ （常量对应值为5）。
data为同步JSON内容的utf-8二进制数据。
JSON内容示例如下::

	{
		// 识别数据
		"iat_user_data": {		
			"recHotWords": "播报内容|地图显示|路径优先",
			"sceneInfo": {}
		},
		
		// 语义数据
		"nlp_user_data": {		
			"res": [
				{
					"res_name": "vendor_applist",		// 资源名称
					"data": "xxxxxx"		// 数据的base64编码
				}
			],
			"skill_name": "telephone"					// 对应的技能名称
		}
	}

上传数据中包含识别和语义数据，识别数据包含所有识别热词，以|分隔，语义数据中包含资源名称，资源数据，资源对应的技能名称。
资源名称和资源数据与上面描述的动态实体中的字段相同，技能名称是该实体对应生效的技能。

上传的代码示例参考如上\ :ref:`动态实体数据构造上传部分 <upload_schema-label>`\ 。

6.2.4.4 生效使用
^^^^^^^^^^^^^^^^^

与\ :ref:`动态实体生效使用 <individual_work-label>`\ 类似，需要在pers_param中添加uid即可生效使用::

	"pers_param":"{\"uid\":\"\"}",
	
