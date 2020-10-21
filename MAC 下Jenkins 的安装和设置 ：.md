# Jenkins-fastlane-
Jenkins+fastlane自动部署


MAC 下Jenkins 的安装和设置 ：
1.安装jenkins前确保您的电脑已经配置好JDK
  1.）下载JDK需要先注册一个Oracle账号。
  下载JDK如图：
    ![展示图片](https://github.com/diankuanghuolong/Jenkins-Fastlane/blob/main/images/1.png)

   下载成功后得到文件如图：双击安装
    ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/2.png)

 2.）JDK环境配置：（MAC）
   进入JDK安装目录 ：文件访达输入 /Library/Java/JavaVirtualMachines
  ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/3.png)
  ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/4.png)
  ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/5.png)

打开终端，进入home目录下：
  ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/6.png)
  3.）查看是否安装成功：终端输入 java -version ，如果看到jdk版本说明配置已经生效。如图：
  ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/7.png)
4.）配置.bash_profile：如果是第一次配置，没有.bash_profile文件，则新建文件 ：touch .bash_profile；如果已经有该文件，进行修改则打开：open -e .bash_profile （⚠️：当前是在home文件目录下,为了方便创建，先切换到桌面）
如图：
  ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/8.png)
在profile中输入如下配置：
JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk1.8.0_261.jdk/Contents/HomePATH=$JAVA_HOME/bin:$PATH:.CLASSPATH=$JAVA_HOME/lib/tools.jar:$JAVA_HOME/lib/dt.jar:.export JAVA_HOMEexport PATHexport CLASSPATH

其中 /Library/Java/JavaVirtualMachines/jdk1.8.0_261.jdk/Contents/Home，为你的JDK安装包中home文件目录，如图：
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/9.png)

command+s，保存并关闭。进入终端输入命令，使配置生效：source .bash_profile:
输入 echo $JAVA_HOME 显示刚才配置的路径:
如图：
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/10.png)
最后将profile文件拖到home目录下。至此，JDK安装配置完成。

1.安装jenkins，推荐使用命令方式安装：
 1.）如果你的电脑中未安装homebrew工具，先安装：

    打开终端并运行：
brew cask uninstall fastlane

    安装ruby环境。 在终端中运行：
    brew install rbenv ruby-build
    echo "" >> ~/.bash_profile
    echo 'export PATH=${HOME}/homebrew/bin:${PATH}' >> ~/.bash_profile
    echo 'if which rbenv > /dev/null; then eval "$(rbenv init -)"; fi' >> ~/.bash_profile
    rbenv install 2.6.5
    rbenv global 2.6.5
    exit

    因为我电脑已经安装，此处跳过
2.）安装jenkins：brew install jenkins
如图：
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/11.png)

安装完成，如图：
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/12.png)
3.) 链接launchd配置文件 , ln -sfv /usr/local/opt/jenkins/*.plist ~/Library/LaunchAgents
如图：
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/13.png)
4.)登陆。 在网址输入http://localhost:8080
 如图：
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/14.png)
安装过程如下：
    因为我的电脑上安装过部分插件，所以选择“选择插件来安装”。
  ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/15.png)
    默认，直接安装：
  ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/16.png)
  ![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/17.png)


安装完成：
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/18.png)
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/19.png)
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/20.png)
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/21.png)
到此，jenkins安装完成。


新建一个任务：
 1.）重启jenkins：
浏览器输入：localhost:8080/restart
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/22.png)
  2.）开始新建，如图：
![展示图片](https://github.com/diankuanghuolong/Jenkins-fastlane/blob/main/images/23.png)
