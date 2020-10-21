# Jenkins-fastlane-
Jenkins+fastlane自动部署

iOS Fastlane 自动打包

一：开始使用前的准备工作检查环境配置：
 因为 fastlane 其实是一个 Ruby 脚本的集合，你必须安装正确的 Ruby 版本
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
    再次打开终端并运行：
gem install fastlane
fastlane -v

确保安装了最新Xcode命令行工具
xcode-select --install

二：配置fastlane
 1.cd到你项目文件夹下，执行 fastlane init命令
    如下图：
    ![展示图片](https://github.com/diankuanghuolong/Jenkins-Fastlane/blob/main/fastlane_images/1.png)

  上图4个选项：
    翻译：
    1. 自动截屏。这个功能能帮我们自动截取APP中的截图，并添加手机边框（如果需要的话），我们这里不选择这个选项，因为我们的项目已经有图片了，不需要这里截屏。
    2. 自动发布beta版本用于TestFlight，如果大家有对TestFlight不了解的，可以参考王巍写的这篇文章
    3. 自动的App Store发布包。我们的目标是要提交审核到APP Store，按道理应该选这个，但这里我们先不选，因为选择了以后会需要输入用户名密码，以及下载meta信息，需要花费    一定时间，这些数据我们可以后期进行配置。
    4. 手动设置。
    这里我们选择4，手动配置
如下图：
    ![展示图片](https://github.com/diankuanghuolong/Jenkins-Fastlane/blob/main/fastlane_images/2.png)

    项目中会多出fastlane 和Gemfile Gemfile.lock三个文件。其中，fastlane中只有Appfile和Fastfile两个文件，后续步骤中会再加。
2.fastlane 文件解释
    Appfile: 存储有关开发者账号相关信息
    Fastfile: 核心文件，用于命令行调用和处理具体的流程，lane相对于一个action方法或函数 
    Gemfile 类似于cocopods 的Podfile文件
    .env 配置环境变量（在fastlane init进行初始化后并不会自动生成，如果需要可以自己创建
    Deliverfile: deliver工具的配置文件，上传截图苹果和后台一些app信息  (默认不生成，需要sudo gem install deliver安装)然后在fastlane 目录下执行deliver init 即可）
    要注意的点：
    build_app命令等同于gym(别名)
    deliver 命令相当于upload_to_app_store(别名)
    
其中，fastlane文件可以如下配置：
下边的“Langooo”都是我公司的scheme

default_platform(:ios)

platform :ios do
  desc "上传到 App Store"
  lane :release do
    build_app(
        workspace: "Langooo.workspace", 
        scheme: "Langooo",
         #自动管理证书的时候，Xcode 9及以上没有权限获取钥匙串里面的证书，必须加上这个才能打包成功
            export_xcargs: "-allowProvisioningUpdates"
          )
    upload_to_app_store(
          skip_screenshots:true,# 不上传屏幕截图
          skip_metadata:true,# 不上传元数据
      )
  end
 
   desc "打包到蒲公英"
     #bundle exec fastlane add_plugin pgyer(运行该命令，安装pgy插件)
     lane :to_pgy do
     build_app(
        export_method: "ad-hoc",
            #自动管理证书的时候，Xcode 9及以上没有权限获取钥匙串里面的证书，必须加上这个才能打包成功
            export_xcargs: "-allowProvisioningUpdates"
        )
     pgyer(api_key: "f0867d39cxxxxxxxx",user_key:"e94axxxxxxxx”, update_description: "update by fastlane")
#pgyer(api_key: "f0867xxxxxxxx”,user_key:"e94xxxxxxxxxxxx”password: "123456", install_type: "2")
   end

   desc "打包ipa"
     lane :archive_ipa do
     #打包的ipa存放路径
     outputDir = "~/Desktop/ipa"
     #打包的ipa名称
     outputName = "langoo"
     gym(
       scheme: "Langooo", #改为你项目的scheme
       #workspace: "Langooo", #如果项目使用CocoaPods需要加上
       configuration: "Release",
       output_directory: outputDir,
       output_name: outputName,
       include_bitcode: false,
       include_symbols: true,
       codesigning_identity: ENV["CODESIGNING_IDENTITY_TO_FIRIM"],
       silent: true,
       export_options: {
          method: "ad-hoc", #根据具体情况定
          thinning: "<none>",
      #自动管理证书的时候，Xcode 9及以上没有权限获取钥匙串里面的证书，必须加上这个才能打包成功
          export_xcargs: "-allowProvisioningUpdates"
                      }
        )
   end
    
   desc "打包到fir"
  lane :to_firim do
      # 如果你用 pod install
      cocoapods
      # 如果你没有申请adhoc证书，sigh会自动帮你申请，并且添加到Xcode里
      sigh(adhoc: true)
      # 以下两个action来自fastlane-plugin-versioning，
      # 第一个递增 Build，第二个设定Version。
      # 如果你有多个target，就必须指定target的值，否则它会直接找找到的第一个plist修改
      # 在这里我建议每一个打的包的Build都要不一样，这样crash了拿到日志，可以对应到ipa上
      increment_build_number_in_plist(target:ENV['SCHEME_NAME'])
      increment_version_number_in_plist(
        target:ENV['SCHEME_NAME'],
        version_number: ENV['APP_VERSION_RELEASE']
        )
      # gym用来编译ipa
      gym(
        output_directory: './firim',
        export_options: {
          method: "ad-hoc",#打包类型app-store, ad-hoc, enterprise(企业账号打包), development
          thinning: "<none>"
        },
        scheme: ENV['SCHEME_NAME']
        )
      # 上传ipa到fir.im服务器，在fir.im获取firim_api_token
      firim(firim_api_token:ENV['Firim_Api_Token'])
    end


end


3.match文件配置
       添加match全家桶
       cd到项目目录下，fastlane match init
    
    cd到创建的存放Cer证书的的本地仓库目录下，fastlane match
    需要一个git仓库用来存放Cer证书。
   创建一个git仓库，然后将项目的Cer证书上传到仓库，并关联。
    
4.安装：deliver
   fastlane deliver
该命令执行后，fastlane下会多出一些文件，
如图：
 ![展示图片](https://github.com/diankuanghuolong/Jenkins-Fastlane/blob/main/fastlane_images/3.png)

5.配置相关文件信息
    fastlane ios
6.安装蒲公英插件
  bundle exec fastlane add_plugin pgyer






最后打包命令：

上传到 App Store：
    fastlane release 

打包到蒲公英
    fastlane to_pgy

打包ipa 到桌面
    fastlane archive_ipa

