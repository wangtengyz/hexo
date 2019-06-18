### ****从零开始搭建自己的个人博客--Hexo博客框架

​	每个程序员都应该有个自己的个人博客，这样更有IT范一些，哈哈。

​	搭建个人博客的框架有很多比如django、jeklly、hugo和Hexo等等，为啥向大家推荐Hexo，一是Hexo框架现如今很火，就是说“流行正时尚”，另一因素就是假如你在搭建博客框架的过程中出现各种各样的问题，它的网上的解决方案比较成熟，大神们讨论研究的东西也比较多，这对于咱们初入博客之门的大家来说还是非常友好的。

​	搭建个人博客的过程就和日常开发项目一样，肯定会出现各种各样的问题，咱们不要畏惧，大不了重来一次。

#### 第一步  下载安装[node.js](https://nodejs.org/en/)

1. 下载LTS稳定版的node.js;

2. 安装很简单，不停的下一步...;

3. 安装完后会有两个组件，一个是nodejs本省，还有一个就是npm包管理器;

4. 查看是否安装成功，打开cmd终端（按住shift+鼠标右键，选择‘在此处打开命令窗口’），输入node -v 和npm -v 查看版本，若是出现版本号则安装成功;

   ![001](images/001.png)

#### 第二步   全局安装hexo-cli博客框架

1. 由于国内下载npm包比较慢，而且`npm install`的时候**巨慢**,甚至同一个项目，安装的时候**无法保持一致性**(听说npm5.0在速度和使用上确实有了很大提升)等问题。一般都会使用cnpm或者yarn安装包，同时会使用**淘宝镜像源**。

2. 个人习惯用yarn安装，他有三个优点：

   ```
   1.npm安装是串行、而yarn是并行、速度快。
   2.已经下载过的包会被缓存、支持离线安装。
   3.优化的输出信息。
   ```

3. 终端输入命令：` npm  install -g  yarn`或者到[yarn官网](https://yarnpkg.com/zh-Hant/docs/install#windows-stable)下载安装，安装完成后，你可以测试下自己的版本 yarn --version

   再输入`yarn config set registry https://registry.npm.taobao.org`，安装成功会出现：

   ![002](images/002.png)

4. 全局安装hexo-cli,终端输入命令:`yarn global add hexo-cli`，安装成功会出现:


   ![003](images/003.png)

   当你输入hexo -v 查询hexo的版本会报错；

   ![004](images/004.png)

   原因是因为，咱们安装yarn是通过npm的方式安装(如果你是yarn官网下载msi文件，双击安装，安装成功后既可全局使用yarn命令，则不会出现该情况)，yarn的全局安装命令需要配置环境变量，在终端输入`yarn global bin`命令

   ![005](images/005.png)

   将该地址`C:\Users\Administrator\AppData\Local\Yarn\bin;`配置到系统变量内保存；

   ![006](images/006.png)

   然后重启命令行工具，输入hexo -v 出现下图则ok；

   ![007](images/007.png)

#### 第三步    开始使用Hexo搭建自己的博客

1. 先找个容量大点的盘（我是F盘）建一个blog文件夹，在此文件里打开终端，在该目录终端输入`hexo init`生成我们的博客，在一阵等待之后，就安装成功了；

   ![008](images/008.png)

2. hexo会在blog文件里生成一个基础的博客框架，后续咱们的开发都是在这个基础上开发；

3. 咱们现在运行下咱们的博客框架，运行非常简单，终端输入`hexo s`就启动了；

   ![009](images/009.png)

4. 然后打开启动成功的网址`http://localhost:4000`，就可以看到我们的博客雏形了。

   ![010](images/010.png)

5. 进入页面后会告诉你几个快速命令：

   - hexo new '我的新帖子
   - hexo server 运行服务器
   - hexo generate 生成静态文件
   - hexo deploy 部署到远程站点

#### 第四步    生成自己的第一个博客

1. 终端输入`hexo n "我的第一篇博客"`生成md文件,根据保存的路径找到该文件，用vscode或者typora等能编辑md格式文件的软件打开编辑；

   ![011](images/011.png)

2. 博主用的是typora，随便编辑了下内容，打开就是这个样子；

   ![012](images/012.png)

3.  然后保存文件退出软件，还是blog文件夹打开终端，在终端输入`hexo clean`清理下，输入`hexo g`生成文件，最后输入`hexo s` 生成服务;

   ![013](images/013.png)

4.  打开`http://localhost:4000/`你的第一篇博客就已经生成好了；

   ![013](/images/014.png)

#### 第五步  将博客部署到github上公开使用

1. 打开个人的[github网站](https://github.com/),具体如何申请账号，百度下，网上一大堆；
2.  
3.  
4.  
5.  

#### 第六步

#### 第七步

#### 第八步

#### 第九步




