https://www.cnblogs.com/cyxroot/p/13754475.html

https://www.i4k.xyz/article/qq_33154343/110633976

https://www.i4k.xyz/article/qq_33154343/110633976#_gitbook_125


一、nvm、node、npm 区别


nvm：nodejs 版本管理工具，也就是说：一个 nvm 可以管理很多 node 版本和 npm 版本。


nodejs：在项目开发时的所需要的代码库。


npm：nodejs 包管理工具，在安装的 nodejs 的时候，npm 也会跟着一起安装，它是包管理工具，npm 管理 nodejs 中的第三方插件。


二、nvm、node、npm 关系

nvm 管理 nodejs 和 npm 的版本，npm 可以管理 nodejs 的第三方插件。
链接：https://juejin.cn/post/7000652162950758431

先安装  nvm
手动安装：https://github.com/nvm-sh/nvm


输入 nvm
输出：Node Version Manager 代表安装成功
查看 nvm 版本
nvm verison

(0) nvm version ：查看 nvm 版本 (常用)
(1) nvm install stable：安装最新稳定版 node
(2) nvm install <version>：安装指定版本 For Example:
(4) nvm ls：查看已经下载安装的 node 版本
(5) nvm current：查看当前使用的 node 版本
(6) nvm use v10.13.0：切换到 10.13.0node 版本
(7) nvm alias default 10.12.0：设置默认 node 版本


nodejs 需要V10.23.0

在按照 nodejs

再按照 gitbook-cli
npm uninstall -g gitbook-cli
--清除缓存
npm cache clean -f
npm install -g gitbook-cli


gitbook -V      查看版本号
gitbook init    初始化
gitbook serve   预览
gitbook build   生成
gitbook build --gitbook=2.6.7 生成时指定gitbook的版本, 本地没有会先下载
gitbook uninstall 2.6.7   卸载指定版本号的gitbook
gitbook fetch [version]      获取[版本]下载并安装<版本>
gitbook --help   显示帮助文档
gitbook ls-remote  列出NPM上的可用版本：



错误：
nvm use 报错：You do not have sufficient privilege to perform this operation
解决：
尝试启动提升的命令提示符（即：开始>键入cmd>右键单击并以管理员身份运行）
然后在以管理员身份运行的终端中输入nvm use x.x.x


初始化报错：
TypeError [ERR_INVALID_ARG_TYPE]: The "data" argument must be of type string or an instance of Buffer, TypedArray, or DataView. Received an instance of Promise

或者  
安装gitbook卡在installing GitBook 3.2.3


经排查，node版本过高，降低一下nodejs版本即可


参考文档：
https://www.i4k.xyz/article/qq_33154343/110633976#_gitbook_125

整体搭建参考的文档：
https://skyao.gitbooks.io/learning-gitbook/content/creation/generated.html
https://segmentfault.com/a/1190000040792967
https://www.chengweiyang.cn/gitbook/installation/README.html
https://juejin.cn/post/6931225754264928269
https://www.cnblogs.com/gongyuhonglou/p/13535026.html


本机同步使用github  gitee
https://www.cnblogs.com/lavard/p/15240737.html
https://www.cnblogs.com/leyili/p/git_ssh_key.html



编译一下 gitbook _docs
https://www.jianshu.com/p/8a513823a988

gitbook build . docs