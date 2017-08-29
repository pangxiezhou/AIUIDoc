2.2. 创建配置
=============

2.2.1 评估板网络配置
--------------------

AIUI评估板支持多种配置网路的方式。

SmartConfig
    1. 确保评估板上已经安装\ :ref:`开发包 <dev_kit-label>`\ 中SmartConfigService.apk 并已随开机启动运行；
    2. 确保手机客户端已经连接到 WIFI；
    3. 短按评估板右下角的 Load 键，语音提示进入 SmartConfig 连网模式，等待手机发送账号、密码；
    4. 在手机端右上角点击快联然后设置所连接 WIFI 的账号、密码，发送给评估板。
	
Windows批处理
	1. 评估板插上电源、开机，使用 USB 线连接电脑，配置好 ADB 的环境变量。
	2. 双击打开开发包中 wifi_connect_ui.bat 文件。
	3. 按照提示键入 WIFI 的 SSID、密码、加密方式回车即可（连接成功后会保存到系统设置，重启后会自动联网无需重新配置)。
	
同屏UI操作
	1. 评估板插上电源、开机，使用 USB 线连接电脑，配置好 ADB 的环境变量；
	2. 使用TotalControl或者Vysor，通过界面直接操作评估板WIFI配置。
	
2.2.2 应用配置
---------------------

通过\ `AIUI官网 <http://www.xfyun.cn/aiui/index>`_ 购买评估板时会同时创建一个AIUI的应用，通过\ `AIUI应用管理 <http://www.xfyun.cn/aiui/index>`_\ 查看该
应用的APPID和KEY，如下所示：

.. image:: appid.png

如果无对应AIUI应用，联系购买渠道的\ :ref:`商务 <aiui_busines-label>`\ 创建解决。

进入\ `AIUI开放语义 <http://aiui.xfyun.cn/jupiter-platform/default/index>`_\ 配置应用需要的如天气，音乐等场景。

.. _aiui_client_config:

2.2.3 配置AIUI
--------------

1. 手机安装 ControlClient.apk （路径：\ :ref:`AIUI开发包 <dev_kit-label>`\ \\bin\\ControlClient.apk）
2. 打开手机端控制软件 ControlClient，请确保手机和评估板连接上同一个 WIFI 热点。注：建议使用 4g 手机分享热点，请勿使用 360WIFI 等设备，会导致扫描不到设备。
3. 点击“连接”，进入控制台页面，可以看到连接情况和评估板返回的消息。
4. 点击“配置”，按提示输入应用配置信息，**勾选启动后处理程序**，点击“同步配置”，配置成功后即可体验。

流程如下所示：

.. image:: config.png

2.2.4 常见问题
--------------

Q：同在一个WIFI下，无法搜索到设备
	A：手机端使用局域网广播包的方式扫描设备，如果路由器对这种方式进行限制就会导致无法扫描到，
	所以建议使用手机4G分享热点，重试扫描。
	
Q：手机端显示10407错误
	A：配置的APPID和KEY不匹配，仔细检查，根据应用的APPID和KEY重新配置。

.. _aiui_busines-label:

Q：AIUI商务怎么联系
	A：`AIUI解决方案 <http://www.xfyun.cn/aiui/index>`_ 页面底部有商务的联系方式。





