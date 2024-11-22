#编译UE5并打包Android程序

###编译UE5

第一次编译使用UE5.4.0，编译耗时1小时15分钟，并在后续使用自带工具安装Android SDK
随后出现BUG，详见第二节

###字符转义BUG

使用初学者包直接进行打包，提示构建失败，Unknown Error。
查看log，得知：

`UATHelper: 打包 (Android (ASTC)): UnrealBuildTool failed. See log for more details. `

进一步向上，找到

`UATHelper: 打包 (Android (ASTC)): Execution failed for task ':app:compileDebugJavaWithJavac'.`

查网页得知该问题来自Gardle打包工具。由于烘焙缓存仍存在，前往Gardle目录，尝试手动构建。

构建失败，得到能够正常显示的gardle报错：

`D:\UE5\PACKTEST\Intermediate\Android\arm64\gradle\app\build\generated\aidl_source_output_dir\debug\out\com\android\vending\licensing\ILicenseResultListener.java:3: 错误: 非法的 Unicode 转义`

以及其他12项同类错误。定位错误文件ILicensingService.java，这是一个构建中间文件，无法手动修改。
出错的行如下：

`* Using: D:\UE5\Android\Sdk\build-tools\35.0.0\aidl......`

###报错原因？

根本原因在于，Java的转义、Windows系统的反斜杠路径，以及**激活系统时选择的user用户名**。这三个一起导致了，尽管位于注释中，但\u被解释为unicode转义，而ser并不是一个合法的Unicode字符。

解决办法：重装系统。

###不采用更换用户的方式？

该路径不止包含Gardle路径，还有其他一些缓存路径，并且由于大部分软件使用user安装，这无疑在给后续开发增加难度。

###后续

重新使用UE5.4.4打包。