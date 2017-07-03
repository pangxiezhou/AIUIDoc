5.1.2 AIUI配置Provider
-------------------------

AIUI Service通过\ ``android:authorities="com.iflytek.aiui.cfg.provider"``\ 的ContentProvider读取配置，配置文件的格式和字段说明在\ :ref:`aiui_cfg_label`\ 有详细的说明。

ControlService中实现的提供AIUI配置的ContentProvider简略代码如下::

	public class AIUIConfigProvider extends ContentProvider {
		private static final String TAG = "AIUIConfigProvider";
		

		
		@Override
		public Bundle call(String method, String arg, Bundle extras) {
			if ("readAIUICfg".equals(method)) {
				///sdcard/AIUI/cfg/aiui.cfg配置文件由ControlService管理的，ControlService被卸载后，该配置文件就没用了
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

5.1.2.1 注意事项
^^^^^^^^^^^^^^^^^

谁定义了配置的ContentProvider，就读取谁的配置。

ControlService和AIUIDemo都定义了相同的ContentProvider，这是不能同时安装的原因。

ControlService:
	配置文件位于/sdcard/AIUI/cfg/aiui.cfg
	
AIUIDemo:
	配置文件位于工程assets/cfg/aiui.cfg下


/sdcard/AIUI/cfg/aiui.cfg配置文件由ControlService管理，如果ControlServie被卸载后，这个配置文件就没用了。

