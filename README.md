# Hexo-Blog
GitHub+Hexo框架搭建个人博客源码

### 概述
Hexo是一款基于Node.js的静态博客框架，依赖少易于安装使用，可以方便的生成静态网页托管在GitHub和Heroku上，是搭建博客的首选框架。这里我们选用的是GitHub。Hexo同时也是GitHub上的开源项目，参见：[hexojs/hexo](https://github.com/hexojs/hexo)。 如果想要更加全面的了解Hexo，可以到其官网 [Hexo](https://hexo.io/) 了解更多的细节。

### 搭建步骤
- 获得个人网站域名
- GitHub创建个人仓库
- 安装Git
- 安装Node.js
- 安装Hexo
- 推送网站
- 更换主题
- 初识MarkDown语法
- 发布文章
- 寻找图床
- 其他个性化设置
- 附录

##### 获得个人网站域名

域名是网站的入口，也是网站的第一印象，比如饿了么的官网的域名是：[ele.me/ ](https://www.ele.me/) ，很是巧妙。常见的有com,cn,net,org等后缀，也有小众的xyz,me,io等后缀，根据你自己的喜好，选择不同的后缀，比如我选择就是常见的com后缀。很多小众奇特的后缀在大陆是没办法备案的，网站也就无法上线。然而使用GitHub托管我们的网站，完全不需要备案，因为托管我们的网站内容的服务器在美国，而且在国内备案流程也比较繁杂，时间需要一周左右。

申请域名的地方有很多，这里推荐阿里云：[阿里云-为了无法计算的价值](https://www.aliyun.com/) 申请入口：[域名注册](https://wanwang.aliyun.com/domain/) 购买域名这也是我们整个搭建过程中惟一一个需要花钱的地方。如果你已经有了空闲域名就无需购买，直接使用即可。

##### GitHub创建个人仓库

登录到GitHub,点击GitHub中的New repository创建新仓库，仓库名应该为：用户名.[github.io](https://pages.github.com/) 这个用户名使用你的GitHub帐号名称代替，这是固定写法，比如我的仓库名为：lambdaxiao.github.io

##### 安装Git

Git安装就不赘述了，我们网站在本地搭建好了，需要使用Git同步到GitHub上。

##### 安装Node.js

Hexo基于Node.js，Node.js下载地址：[Download | Node.js](https://nodejs.org/en/download/) 下载安装包，注意安装Node.js会包含环境变量及npm的安装，安装后，
检测Node.js是否安装成功，在命令行中输入 node -v ，
检测npm是否安装成功，在命令行中输入npm -v ，到这了，安装Hexo的环境已经全部搭建完成。

##### 安装Hexo

Hexo就是我们的个人博客网站的框架， 这里需要自己在电脑常里创建一个文件夹，可以命名为Blog，Hexo框架与以后你自己发布的网页都在这个文件夹中。创建好后，cmd命令行进入刚才创建的文件夹的目录下，如：F:\Project\MyProject\Blog>

使用npm命令安装Hexo，输入：

```
npm install -g hexo-cli 
```
这个安装时间较长耐心等待，安装完成后，初始化我们的博客，输入：

```
hexo init blog
```
> 注意，这里的命令都是作用在刚刚创建的Blog文件夹中。

为了检测我们的网站雏形，分别按顺序输入以下三条命令：

```
hexo new test_my_site

hexo g

hexo s
```
这些命令在后面作介绍，完成后，打开浏览器输入地址：

localhost:4000

可以预览我们写出第一篇博客。
![](https://github.com/LambdaXiao/screenshot/raw/master/screenshots/blog1.png)

现在来介绍常用的Hexo 命令

```
npm install hexo -g #安装Hexo
npm update hexo -g #升级
hexo init [新建文件夹名] #初始化博客

//命令简写
hexo n "我的博客" == hexo new "我的博客" #新建文章
hexo g == hexo generate #生成
hexo s == hexo server #启动服务预览
hexo d == hexo deploy #部署

hexo server #Hexo会监视文件变动并自动更新，无须重启服务器
hexo server -s #静态模式
hexo server -p 5000 #更改端口
hexo server -i 192.168.1.1 #自定义 IP
hexo clean #清除缓存，若是网页正常情况下可以忽略这条命令
```

刚刚的三个命令依次是新建一篇博客文章、生成网页、在本地预览的操作。

##### 推送网站

上面只是在本地预览，接下来要做的就是就是推送网站，也就是发布网站，让我们的网站可以被更多的人访问。在设置之前，需要解释一个概念，在blog根目录里的_config.yml文件称为站点配置文件，如下图![](https://github.com/LambdaXiao/screenshot/raw/master/screenshots/blog2.png)

进入根目录里的themes文件夹，里面也有个_config.yml文件，这个称为主题配置文件，如下图![](https://github.com/LambdaXiao/screenshot/raw/master/screenshots/blog3.png)

下一步将我们的Hexo与GitHub关联起来，打开站点的配置文件_config.yml，翻到最后修改为：

deploy:

type: git

repo: 这里填入你之前在GitHub上创建仓库的完整路径，记得加上 .git

branch: master

参考如下：
![](https://github.com/LambdaXiao/screenshot/raw/master/screenshots/blog4.png)

保存站点配置文件。

其实就是给hexo d 这个命令做相应的配置，让hexo知道你要把blog部署在哪个位置，很显然，我们部署在我们GitHub的仓库里。最后安装Git部署插件，输入命令：

```
npm install hexo-deployer-git --save
```
这时，我们分别输入三条命令：

```
hexo clean 
hexo g 
hexo d
```
其实第三条的 hexo d 就是部署网站命令，d是deploy的缩写。完成后，打开浏览器，在地址栏输入你的放置个人网站的仓库路径，即 http://xxxx.github.io 比如我的xxxx就是我的GitHub用户名：![](https://github.com/LambdaXiao/screenshot/raw/master/screenshots/blog5.png)

你就会发现你的博客已经上线了，可以在网络上被访问了。


##### 更换主题

如果你不喜欢Hexo默认的主题，可以更换不同的主题，主题传送门：[Themes](https://hexo.io/themes/) 我自己使用的是Next主题，可以在blog目录中的themes文件夹中查看你自己主题是什么。现在把默认主题更改成Next主题，在blog目录中（就是命令行的位置处于blog目录）打开命令行输入：
```
git clone https://github.com/iissnan/hexo-theme-next themes/next
```

这是将Next主题下载到blog目录的themes主题下的next文件夹中。打开站点的_config.yml配置文件，修改主题为next


打开主题的_config.yml配置文件，不是站点主题文件，找到Scheme Settings


next主题有三个样式，我用的是Pisces，你们可以自己试试看，选择你自己喜欢的样式（只需要把行首的#去除，#是注释），选择好后，再次部署网站，hexo g、hexo d，查看效果。选择其他主题，按照上述过程即可实现。


##### 发布文章

我们开始正式发布上线博客文章，在命令行中输入：
```
hexo n "博客名字"
```
我们会发现在blog根目录下的source文件夹中的_post文件夹中多了一个 博客名字.md 文件，使用Markdown编辑器打开，就可以开始你的个人博客之旅了。通过带有预览样式的Markdown编辑器实时预览书写的博文样式，也可以通过命令 hexo s --debug 在本地浏览器的localhost:4000 预览博文效果。写好博文并且样式无误后，通过hexo g、hexo d 生成、部署网页。随后可以在浏览器中输入域名浏览。

##### 寻找图床

图床，当博文中有图片时，若是少量图片，可以直接把图片存放在source文件夹中，但这显然不合理的，因为图片会占据大量的存储的空间，加载的时候相对缓慢 ，这时考虑把博文里的图片上传到某一网站，然后获得外部链接，使用Markdown语法，![图片信息](外部链接) 完成图片的插入，这种网站就被成为图床。常见的简易的图床网站有：[贴图库](http://www.tietuku.com/)图片外链 国内算比较好的图床我认为有两个：新浪微博和 [七牛云](https://www.qiniu.com/) ，七牛云的使用方法可以参看其他文章。图床最重要的就是稳定速度快，所以在挑选图床的时候一定要仔细


##### 其他个性化设置

[主题配置 - NexT 使用文档](http://theme-next.iissnan.com/theme-settings.html)

[第三方服务集成 - NexT 使用文档](http://theme-next.iissnan.com/third-party-services.html)

[内置标签 - NexT 使用文](http://theme-next.iissnan.com/tag-plugins.html)

[档进阶设定 - NexT 使用文档](http://theme-next.iissnan.com/advanced-settings.html)

[为Hexo Next主题添加哈林摇特效（五）](http://www.iamlj.com/2016/08/add-special-effect-harlem-shake-for-hexo/)


##### 附录

博客展示

[ngudream](http://ngudream.com/)

[谁把钱丢了](https://jmyblog.top/)

[Cocoon](https://asmire.site/)

[李瑞豪的博客](https://lruihao.cn/)

> 参考文章：https://zhuanlan.zhihu.com/p/26625249