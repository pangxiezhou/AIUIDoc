3.3 AIUI配置Provider
^^^^^^^^^^^^^^^^^^^^^

AIUI Service通过\ ``android:authorities="com.iflytek.aiui.cfg.provider"``\ 的ContentProvider读取配置，配置文件的格式和字段说明在3.2节有详细的说明。

ControlService中实现的提供AIUI配置的ContentProvider简略代码如下::

	public class AIUIConfigProvider extends ContentProvider {
		private static final String TAG = "AIUIConfigProvider";
		

		
		@Override
		public Bundle call(String method, String arg, Bundle extras) {
			if ("readAIUICfg".equals(method)) {
				String config = ConfigUtil.readSdcardCfg(ServiceConstant.SDCARD_AIUI_CFG_PATH);
				Bundle bundle = new Bundle();
				
				bundle.putString("config", config);
				
				return bundle;
			}
			
			return super.call(method, arg, extras);
		}
	}
	
Manifest中注册信息如下source::

	 <!-- AIUI配置provider -->
        <provider
            android:name="com.iflytek.aiui.devboard.controlservice.provider.AIUIConfigProvider"
            android:authorities="com.iflytek.aiui.cfg.provider"
            android:exported="true"
            android:multiprocess="false">
            
        </provider>
		
如上的配置可以参考开发包中AIUIDemo中\ ``AIUIConfigProvider.java`` \ 的实现。
	
评估板控制端ControlClient配置的appid和key就是通过tcp发送给ControlService，ControlService再通过如上的ContentProvider提供给AIUI Service。
