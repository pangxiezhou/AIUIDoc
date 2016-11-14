----------------
8.3 自定义数据
----------------

串口通信格式中定义了网络，配置，控制，接收几类消息，这些消息大部分格式是json文本，
接收的AIUI消息的格式还是gzip压缩，对于性能较弱的机器如单片机来说，这些消息根本无法解析。

所以在串口通信格式中加入自定义数据类型，以扩展现有串口的适用性。

^^^^^^^^^^^^^^
8.3.1 消息定义
^^^^^^^^^^^^^^

+----------+--------+--------+----------+----------+--------+-------------------------+-------------+
|分类      | 同步头 | 用户ID | 消息类型 | 消息长度 | 消息ID | 消息数据                | 校检码      |
+----------+--------+--------+----------+----------+--------+-------------------------+-------------+
|自定义消息+ 0xA5   | 0x01   | 0x2A     | 见附注   | 见附注 | 自定义消息              | 见附注      |
+----------+--------+--------+----------+----------+--------+-------------------------+-------------+

^^^^^^^^^^^^^^^
8.3.2 基本流程
^^^^^^^^^^^^^^^

自定义数据的基本工作流程是这样的：

1. AIUI上开发者的Android应用发送含数据的特定广播，AIUI会把数据通过串口发送给上位机。

2. 上位机也可以发送自定义类型数据，AIUI会发送一个带自定义数据的广播，AIUI上开发者的Android应用可以接收处理。

^^^^^^^^^^^
8.3.3 细节
^^^^^^^^^^^

对应上述流程，分别对应如下广播：

1. ``com.iflytek.aiui.devboard.action.SEND_CUSTOM_DATA``

2. ``com.iflytek.aiui.devboard.action.RECEIVE_CUSTOM_DATA``

intent中包含自定义数据的类型为\ ``bytearray``\ ,key为custom_data。

^^^^^^^^^^^^^^^
8.3.4 代码示例
^^^^^^^^^^^^^^^

接收发送自定义消息的Receiver::

	class CustomDataReceiver extends BroadcastReceiver{

		private static final String ACTION_SEND_CUSTOM_DATA = "com.iflytek.aiui.devboard.action.SEND_CUSTOM_DATA";
		private static final String CUSTOM_DATA_KEY = "custom_data";

		@Override
		public void onReceive(Context context, Intent intent) {
			byte[] customData = intent.getByteArrayExtra(CUSTOM_DATA_KEY);
			Log.d("CustomDataReceiver", "recv custom data " + Arrays.toString(customData));
			
			Intent respAck = new Intent(ACTION_SEND_CUSTOM_DATA);
			respAck.putExtra(CUSTOM_DATA_KEY, new byte[]{1, 1, 2, 3, 5, 8});
			context.sendBroadcast(respAck);
			
		}
	}
	
在manifest中注册如下

.. code-block:: xml

	<receiver android:name="com.example.uartcustomdata.CustomDataReceiver">
		<intent-filter>
			<action android:name="com.iflytek.aiui.devboard.action.RECEIVE_CUSTOM_DATA"/>
		</intent-filter>
	</receiver>
