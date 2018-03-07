---
ID: 34
post_title: 使用Homestead搭建开发环境
author: javalaw2010
post_excerpt: ""
layout: post
permalink: https://www.javalaw.xyz/archives/34
published: true
post_date: 2017-08-31 22:07:27
---
<h2>缘起</h2>
当我还是个刚入门的菜鸟的时候，我习惯使用WAMP或者类似地集成套件作为自己的开发环境，一开始他们确实很不错，大部分情况下都能达到想要的效果，可是随着环境的依赖越来越多，这些集成套件开始表现出极大地局限性，比如，我难以在nginx和apache之间轻松转换，难以升级PHP的版本，这些都还不是最严重的，最为致命的是，他们无法与linux上的软件进行配合，比如，supervisor。后来，我转移到mac上进行开发，mac的环境似乎非常不错，比如，自带PHP和Apache，彼时，我还不是很熟悉mac/Linux下的一些坑，比如权限配置等等，当我第N次搞崩我的环境，并且无论怎样重装都无法使他恢复到最初的状态的时候，我开始思考有没有什么办法可以创建一整套的开发环境，能够让我自己随意折腾，即使发生了不可挽回的错误，我也能立刻使他回到最初的状态？

我们希望中的开发环境是什么状态呢？我大致总结了一下：
<ol>
 	<li>低侵入性：我们希望在我们的电脑上安装尽可能少的软件。</li>
 	<li>迅速恢复：即使发生了不可逆转的错误，我们也应该能在较短的时间内将环境恢复到最初的可用装填。</li>
 	<li>高拟真性：开发环境应当与实际生产环境高度一致，在开发环境编写通过的代码，能够不经修改直接在生产环境直接运行。</li>
</ol>
到底有没有办法呢？

答案是有的，有很多前辈已经想到这些问题了，虚拟化技术在解决创建开发环境的问题上能够发挥很好的作用，我个人倾向于使用vagrant和docker。
<h2>vagrant与Docker</h2>
如果非要把vagrant和docker放在一起比较的话，我其实是更中意docker的，当然这也并不是说vagrant就一无是处了，vagrant和docker本身就应用于不同的场景，我们应当根据不同的使用场景来决定到底使用哪一个。

vagrant通常只会使用与上线之前的流程当中，比如，开发，测试等等，由于vagrant是基于虚拟机的（大部分情况下是基于virtualbox），因此它其实并不适合用于生产环境当中，但是它的上手门槛极低，几乎不需要任何的学习成本就可以迅速搭建起一个隔离的独立的虚拟机开发环境。Docker是更加轻量级更加高效的虚拟化技术，它完全可以在生产环境当中使用。缺点是Docker其实在windows上的表现跟类Unix平台上的表现还是有一定差异的，并不能无缝跨平台，这无形中增加了我们搭建开发环境的成本，如果你是单一平台的使用者，那其实两个都不错，不过，如果经常在windows/unix平台之间切换，那么，vagrant可能是更好的选择。

强烈建议，如果你的开发严重依赖于Linux环境的话，那么还是应该优先选择mac/linux作为你的开发平台，因为根据我在windows平台的踩坑经历，即使使用了vagrant，也会遇到一些不必要的坑。
<h2>安装virtualbox</h2>
<ol>
 	<li>下载virtualbox</li>
 	<li>安装</li>
 	<li>完成</li>
</ol>
<h2>安装vagrant</h2>
<ol>
 	<li>下载vagrant</li>
 	<li>安装</li>
 	<li>完成</li>
</ol>
如果你没办法完成上面两个步骤，那么看到你的浏览器最右上角的那个叉叉了嘛？当然如果你使用mac，它有可能在浏览器的左上角，然后，按下它，再见！
<h2>使用Homestead搭建开发环境</h2>
Homestead 是由laravel开发的一个开发环境，它提供了一个vagrant盒子和一系列的vagrant的文件以及脚本，如果你还不知道vagrant是什么，你应当先Google一下。Homestead目前基于Ubuntu14.04，关于Homestead的更加详细的说明，可以看<a href="https://laravel.com/docs/5.5/homestead">这里</a>。
<h3>添加Homestead盒子</h3>
如果你使用的是windows系统并且不希望你的C盘空间被几十个G的虚拟机文件占满，那么你最好添加以下环境变量：
<pre class="lang:sh decode:true">VAGRANT_HOME D:/vagrantHome</pre>
并且打开virtualbox，在全局设定中将虚拟机文件夹选定到一个合适的文件。

添加homestead的盒子与添加其他的vagrant盒子并没有太大的不同，如果你的网络环境良好——能够告诉无障碍访问境外网站，那么找一个你喜欢的目录，执行
<pre class="lang:sh decode:true">vagrant box add laravel/homestead</pre>
即可，如果你在国内，那么我们可能得费一番波折，你可以运行以上的命令，睁大眼睛仔细观察输入，你可以找到盒子的下载地址，复制它，使用迅雷下载或者其他下载工具下载吧，当然，使用这种下载方式下载的盒子并不总是完好无缺的，运气不好的话，你可能得多下载几次了。由于Homestead的脚本会校验盒子的版本，因此，你不能直接像导入其他的盒子一样直接<span class="lang:sh decode:true crayon-inline">vagrant box add namespace/box /path/to/download/box</span> 即可,你得在下载的盒子的目录下创建一个metadata.json文件，写入以下内容
<pre class="lang:sh decode:true">{
    "name": "laravel/homestead",
    "versions":
    [
        {
            "version": "3.0.0",
            "providers": [
                {
                  "name": "virtualbox",
                  "url": "virtualbox.box"
                }
            ]
        }
    ]
}</pre>
其中，version部分是盒子的版本号，你可以在下载盒子的链接当中找到，url是你下载后盒子在本地的路径。

然后执行：
<pre class="lang:sh decode:true">vagrant box add laravel/homestead metadata.json</pre>
&nbsp;
<h3>安装Homestead</h3>
Homestead有两种安装方式：全局安装与按项目安装，我个人比较推荐全局安装，这样比较省事，你只需要再安装一个git就可以了，分项目安装你还得安装php和composer，稍显的繁琐一点，不过也无甚大碍，这两种方式都没有很强的侵入性。当然如果你有洁癖——比如像我这种，不希望在电脑上再安装多余的软件了，你可以使用一个第三方的盒子安装php和git并挂载你的整个工作空间，作为你的全局workspace，然后再去安装Homestead等等，这种做法看起来比较反人类，有一种脱裤子放屁的羞耻感，不过如果这种做法可以满足我们的电子洁癖，那也不失为一个“最佳实践”。

全局安装：
<pre class="lang:sh decode:true">git clone https://github.com/laravel/homestead.git Homestead

cd Homestead

git checkout v6.1.0

// Mac / Linux...
bash init.sh

// Windows...
init.bat
</pre>
分项目安装：
<pre class="lang:sh decode:true ">composer require laravel/homestead --dev

//mac/Linux
php vendor/bin/homestead make

//windows
vendor\\bin\\homestead make</pre>
然后：
<pre class="lang:sh decode:true">//如果你是使用的windows，务必打开一个以管理员身份运行的CMD，否则npm无法很好的工作
vagrant up

vagrant ssh</pre>
开始放肆吧！
<h2>一些配置建议</h2>
为了能够爽玩虚拟机开发环境，我们还需要做一些额外的配置，比如：
<pre class="lang:sh decode:true">//ubuntu 国内源
sudo sed -i 's/archive.ubuntu.com/mirrors.ustc.edu.cn/g' /etc/apt/sources.list

//composer 国内镜像
composer config -g repo.packagist composer https://packagist.phpcomposer.com

//npm淘宝源
npm config set registry https://registry.npm.taobao.org

//最新版本的npm在虚拟机使用可能有bug，所以可以使用Yarn来代替
npm install -g yarn
yarn config set registry https://registry.npm.taobao.org</pre>
如果你还使用了其他的一些源或包管理，添加国内镜像的步骤自然不需要我多说。此外，如果你是创建了laravel项目，并且使用browsersync来同步刷新浏览器，那么强烈建议在你的mix webpack的配置文件中加入以下配置：
<pre class="lang:sh decode:true ">mix.browserSync({
     proxy:'homestead.app',
     host:'192.168.10.10',
     watchOptions: {
          usePolling: true,
          interval: 500
     },
    // files:['**/**/*.html'] //如果你的laravel项目使用了前后端分离，使用html页面作为前端文件而不是.blade.php，那么强烈建议加上这一行，以保证browsersync会检测到html文件的变化
});
</pre>
&nbsp;