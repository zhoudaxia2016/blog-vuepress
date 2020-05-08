---
title: 第一次开发小程序总结
date: 2019-11-18
categories: frontend
tags:
  - miniprogram
---

> 这是我第一次进行小程序开发，在这里对小程序开发做一些总结吧。
希望可以对大家有所帮助。

### 小程序是什么

对于开发者来说，小程序就是一个开发框架，和vue，react是差不多的，
语法也差不多。在小程序提供的框架下，我们进行小程序的开发，把框架
的坑填完了，就算开发完成了。

不同的是，这个框架只能运行在微信这个容器里。

下面是一些资源：
* 官方文档 [https://developers.weixin.qq.com/miniprogram/dev/index.html](https://developers.weixin.qq.com/miniprogram/dev/index.html)
  查看api什么的，直接去这吧。

* 官方社区 [https://developers.weixin.qq.com/](https://developers.weixin.qq.com/)
  需要提bug，可以去这 :smile:

* 资源整理 [https://github.com/justjavac/awesome-wechat-weapp](https://github.com/justjavac/awesome-wechat-weapp)

### 小程序框架介绍

这里就只做简单的介绍吧，因为是用mpvue来开发，对于小程序原生框架没有
进行深入的研究（这个可以是使用mpvue的优点，也可以是它的缺点）。

首先我们得了解小程序的目录结构，然后再在里面填充内容。基本结构为：

- project.config.json
- app.json
- app.js
- app.wxss
- pages/
  - index/
    - main.js
    - main.json
    - main.wxss
    - main.wxml
- components/
  - button/
    - main.js
    - main.json
    - main.wxss
    - main.wxml

project.config.json是项目的配置的配置文件，类似于package.json。可以用来
配置appid，项目名字等等。这是给微信开发工具看的，用来配置开发工具的一些
功能。当你使用开发工具配置一些选项时，也会自动修改到这个文件。

app.json是小程序的配置，比如小程序的导航栏和底部tabbar的配置。

app.js里，调用App()来初始化小程序，该函数的参数可以用来配置小程序的生命周期，
全局变量等等。

app.wxss，全局样式文件，自动被小程序引入（上面的文件一样，都是自动被小程序
处理的）

pages和components目录是页面和组件目录。这些不是小程序自动处理，需要我们进行
引入的配置。比如在app.json里的pages配置项，就是用来配置页面路径的。如上面的
目录，要使小程序能打开pages/index/main页面，要在app.json中写入：
```
{
  "pages": [
    "pages/index/main"
  ]
}
```
在其他页面，可以调用wx.navigateTo('/pages/index/main')来跳转到这个页面（如果pages/index/main
这个页面是tabbar页面，则需调用wx.switchTab)。组件的引入，也类似这样子。

可以看到，页面目录下有四个文件，而我们只是写了"pages/index/main"，小程序就会
处理js,wxss,wxml,json四个文件，组成一个页面，不需要一个一个引入。下面介绍这几个文件的作用：

* js 页面（组件）的逻辑代码。
通过Page方法来初始化页面。Page的参数可以设置页面data和生命周期，还有页面方法。
* wxss 页面（组件）的样式。阉割版css。直接写css就可以了。
* wxml 页面（组件）的结构。阉割版html。直接写html就可以了。可以使用js文件定义
的方法和data。
* json 页面（组件）的配置。有些小程序的逻辑和结构，小程序已经写好了，我们只需要在json
文件配置一下就行了。

Page详细介绍：[https://developers.weixin.qq.com/miniprogram/dev/reference/api/Page.html](https://developers.weixin.qq.com/miniprogram/dev/reference/api/Page.html)


#### 1. 小程序能力

* ##### 组件化开发

一开始小程序并没有支持组件化开发的能力，所以应运而生了许多第三方组件化的框架，
来解决这个开发的痛点。不过很早之前，小程序也支持了组件化开发。

组件的目录结构和page的结构是一样的。在js文件里，调用的是Component方法而不是
Page方法。生命周期也不一样。

在组件里的json文件中，要配置：
```
{
  "component": true
}
```

在组件的wxss文件的样式中，只作用于该组件。在wxml里，我们也可以使用类似vue的
slot的能力。

在需要使用该组件的页面的json文件里，配置：
```
{
  "usingComponents": {
    "component-name": "path-to-component"
  }
}
```
这样，我们就能在wxml里使用`<component-name/>`

自定义组件详细介绍：[https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/](https://developers.weixin.qq.com/miniprogram/dev/framework/custom-component/)

* ##### api

小程序通过wx对象，给开发者开放了许多能力：

  - 路由切换 如wx.navigateTo（小程序已经帮我们写好路由的框架了，不需要vue-router
  这样的库了）
  - 存储能力 如wx.setStorageSync
  - 网络请求 如wx.request
  - dom查询  wx.createSelectorQuery 不过这可以算是对其不足的补充（小程序不能使用dom
  方法）
  - 扫码     wx.scanCode
  - canvas   

详细文档：[https://developers.weixin.qq.com/miniprogram/dev/api/wx.canIUse.html](https://developers.weixin.qq.com/miniprogram/dev/api/wx.canIUse.html)

* ##### 数据绑定

数据绑定是前端框架的标配，所以小程序也是提供了的。

```
// js文件
Page({
  data: {
    a: 'hello'
  }
})
// wxml文件
<view>{{ a }}</view>
```
在页面的生命周期和方法里，可以通过`this.setDate({a: 'world'})`来修改data，
最终也会改变wxml里绑定的数据。

* ##### 模块化

小程序原生已经实现了类似commonjs规范的模块化方案。

* ##### 插件开发

我们可以通过插件开发将一组js接口，自定义组件或者页面封装起来，供其他项目使用，
而插件使用者无法看到插件的代码。

因为这次项目并没有使用插件，所以不作过多介绍。想了解的话，可以查看官方文档：[https://developers.weixin.qq.com/miniprogram/dev/framework/plugin/](https://developers.weixin.qq.com/miniprogram/dev/framework/plugin/)

#### 2. 开发工具

* 模拟器　类似于chrome的开发工具，简单介绍：

  1. AppData面板可以查看组件和页面数据
  2. Wxml面板不能查看伪类
  3. Stroage面板可以查看本地存储数据。
  4. 模拟器的导航栏按钮可以模拟切后台和分享等操作。


* 调试器
预览可以选择扫描二维码方式和自动预览方式，自动预览只需点击或者输入快捷键，手机只要打开微信，
小程序就会自动刷新了。

* 编译模式
点击普通编译，可以看到添加编译模式选项。添加编译模式后，选择该模式，每次编译都会自动进入该
编译模式设置的页面，并且带上设置的启动参数和场景值。

* 右上角详情
详情可查看项目信息，如代码大小，appId等等。还可以设置基础库版本，是否校验合法域名等等。

### mpvue介绍

mpvue是一款小程序框架。看名字就知道它是基于vue的语法的，使得熟悉vue的前端开发者可以快速
上手开发小程序。

#### 优势

1. 会vue的开发者基本没有学习成本。
2. 可以使用一些vue生态的工具，比如vuex
3. mpvue2.0支持一次编写vue代码，转译成多端小程序代码。

#### 缺陷
1. 组件使用时不支持class和style绑定
2. Vue.component第一个参数必须是静态字符串
3. 修改wxml时经常需要重启dev
4. mpvue使用data-v类名来实现样式的局部作用域，但是它只能生成一个,导致组件样式难以覆盖。
5. ~~slot的作用域是子组件作用域。所以组件最好还是不用slot~~
解决方案：[https://github.com/Meituan-Dianping/mpvue/issues/526](https://github.com/Meituan-Dianping/mpvue/issues/526)
6. ~~重新进入页面组件的data不刷新，是因为data方法只执行一次。~~
解决方案：写一个mixins：
```
onUnload() {
    if (!this.notRefreshData && typeof this.$options.data === 'function') {
      this.$nextTick(() => {
        Object.assign(this.$data, this.$options.data())
      })
    }
}
```
使用nextTick是因为有一些页面的unload生命周期需要用到data数据。

### 开发总结

#### 小程序开发

##### 1. 样式方面使用rpx来代替rem，原理是一样的。

##### 2. 自定义tabbar

原生配置的tabbar样式不能自定义，且自定义的遮罩组件无法遮盖原生tabbar，
所以需要自定义tabbar。可以参考文档：[https://developers.weixin.qq.com/miniprogram/dev/framework/ability/custom-tabbar.html?search-key=%E8%87%AA%E5%AE%9A%E4%B9%89tabbar](https://developers.weixin.qq.com/miniprogram/dev/framework/ability/custom-tabbar.html?search-key=%E8%87%AA%E5%AE%9A%E4%B9%89tabbar)

但是要在mpvue里面使用的话，需要稍微做一些修改。

  * 配置webpack将自定义组件目录custom-tab-bar原封不动搬到打包目录：
  ```
  new CopyWebpackPlugin([{
    from: 'custom-tab-bar/*',
    to: ''
  }], {
    context: 'src/'
  }),
  ```
  * 修改页面里引入tabbar的代码：
  ```
  this.$root.$mp.page.getTabBar()
  ```

##### 3. 小程序登录流程

项目使用的是通过微信授权手机号进行登录的方式，所以需要了解小程序登录，授权，解码用户信息的操作。

小程序登录是为了获取小程序的session_key,通过session_key我们才能解码一些用户数据，这里我们需要
解码手机号。首先我们调用wx.login获取登录凭证code（只有几分钟有效期），然后将code发送给后端，
后端通过[code2Session](https://developers.weixin.qq.com/miniprogram/dev/api-backend/code2Session.html?search-key=code2)接口来获取session_key。其实前端也能直接调用wx.request来获取session_key，
但是这样不安全，不建议前端保存session_key。

后端获取了session_key后，我们通过[getPhoneNumber](https://developers.weixin.qq.com/miniprogram/dev/framework/open-ability/getPhoneNumber.html)按钮来获取加密之后手机号数据，将这些数据交给后端解码，再返回来。

为了方便，这次的项目传给后端的code和手机号加密数据是放在同一个接口里的，这样后端可以不必要保存和映射session_key。

其他的用户加密的数据也是通过这样的方法获取的。

##### 4. promise式api

小程序的api是回调式的，可以选择自己封装成promise式的，或者使用npm库。比如[minapp-api-promise](https://github.com/bigmeow/minapp-api-promise)

##### 5. 小程序请求

对于接口请求的合法域名，我们需要在小程序后台配置好。并且这个服务器是需要安装https证书的。
在小程序后台配置之前，需要下载校验文件到服务器上，使得这个文件在该域名根目录下可以正常访问，
才能配置合法域名成功。

一般在线下环境，我们没有配置合法域名，需要在小程序导航栏的胶囊按钮选择打开调试，并且将请求
都改为http才能请求接口成功。

webview打开的页面，需要另外配置业务域名。

#### 小程序测试

我们开发使用的二维码是很快就会过期的，所以给测试使用的二维码需要使用体验版的二维码。
在开发工具中点击上传，就会覆盖上次上传的代码了。然后再小程序后台，我们将上传的代码
发布为体验版，就可以生成体验版二维码了。测试的微信账号还需要在后台添加体验权限，这样子
添加体验权限的微信账号就可以扫码体验了。

每次上传都会覆盖上传的代码，上传的代码没有版本记录的。每次打开体验版小程序，也会直接请求
最新的代码，不需要重新扫码。

#### 小程序发布

在小程序后台进行已经上传代码的审核与发布。审核一般需要不到半天就可以了。发布需要运营权限。第一次
发布以后，以后发布可以选择全量发布和按阶段发布。按阶段是指可以选择0到100%的微信用户进行发布，不过
是哪些用户我们不能确定，在按阶段发布过程中，可以逐步调节这个比例，在15天后，如果没有撤销发布，
最终会覆盖到100%微信用户。

### 小程序缺陷
1. 不能使用svg标签
2. 样式导入的路径不能是网络路径
3. label标签不支持input输入框

### 总结
感觉微信小程序还是有太多问题，周边框架也不成熟。虽然使用的是前端的技术，对许多规范都支持得不好，
开发体验可以说非常差。

不过通过这次开发小程序，也学习到了许多开发经验。比如熟悉比较完整的开发过程，养成了向官方求助的习惯等。
