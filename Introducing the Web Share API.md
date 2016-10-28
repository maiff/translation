[原文地址](https://developers.google.com/web/updates/2016/10/navigator-share)
By:Paul Kinlan----Paul is a Developer Advocate
PS：仅作为翻译爱好，没有正式授权，切勿商用，转载注明。


目录：
- 怎样开始使用分享API
- 循序渐进
- 分享正确的网址
- 相关链接

告诉大家一个好消息！ (Matt Giuca)[https://twitter.com/mgiuca] C好rome团队中的一员一直致力于一个简单的API叫 [Web Share](https://github.com/WICG/web-share/blob/master/docs/explainer.md),Web Share允许网站调用本机平台上的原生共享能力。
在平台上有很多方式调用原生分享能力，但是他们在某种意义上都有缺点。像 [Web Intent](https://en.wikipedia.org/wiki/Web_Intents)——通过registerProtocolHandler草拟的解决方案，但是在手机上的支持度是0。有一种直接分享的方式通过广为人知的URL服务像Twitter的还有 [ Android intent: URL syntax](https://paul.kinlan.me/sharing-natively-on-android-from-the-web/)（不幸的是这个仅仅被安卓支持并且需要app选择性加入）。

网站分享API很重要，因为它给了用户分享数据的方法的选择和分享哪些内容的选择。

在Chrome55中我们能有 [ Origin Trial ](https://github.com/jpchase/OriginTrials/blob/gh-pages/developer-guide.md) 可以让我们使用网站分享API在我们的网站里。Origin Trial意味着这个API仅仅申请加入的基础上使用。代替的是你需要注册使用在实验阶段。这个时期结束，这个API可能会做改变，这也是为什么我们在尽可能多的收集反馈。

网站分享API基于promise，这个方法传入一个对象，有这些属性——标题(title)、内容(text)、url。

```
navigator.share({
    title: document.title,
    text: "Hello World",
    url: window.location.href
}).then(() => console.log('Successful share'))
.catch(() => console.log('Error sharing:', error));
```
一旦调用这会打开本地的选择框然后允许你去分享用户通过app选择分享的内容。你必须提供至少一个text或者url，但是如果你喜欢，你可两个都提供。

这个api有一下几个约束：
- 你需要让你的网站在 [secure context](https://www.chromium.org/Home/chromium-security/prefer-secure-origins-for-powerful-new-features)(典型的是https)。
- 你需要提供text和URL中的一个。
- 这个API只能由用户主动去调用。(打个比方你不能用`navigator.share()`在一个`onload`中)
- 你传递给API的属性值必须是全都是字符串。

##　怎样开始使用分享API

下面的步骤很简单：

1. 下载 [ Chrome Beta Channel on Android](https://play.google.com/store/apps/details?id=com.chrome.beta)
2. (注册Orin Trial)[https://goo.gl/GR5YLI].
3. 把你的网站[加入](https://github.com/jpchase/OriginTrials/blob/gh-pages/developer-guide.md#how-do-i-enable-an-experimental-feature-on-my-origin)到Orin Trial中
4. 使用`navigator.share()`通过用户操作
5. 全部完成！你可以分享你喜欢的内容了！


## 循序渐进
这个API不是在所有的平台上都通用，所以在没有能力调用这个API环境中你必须优雅的处理。我试着尽可能的逐步地的加强，并且这个过程我放在我的[博客](https://paul.kinlan.me/)上

1. 使用推荐的分享服务像 `<a>`([ Android intent: URL syntax](https://paul.kinlan.me/sharing-natively-on-android-from-the-web/))
2. 检查API的兼容性(`navigator.share !== undefined`)
3. 等待元素可用监听并阻止单击的默认行为
4. 使用 `navigator.share()`

## 分享正确的网址
你应该考虑这个你想分享的URL。在很多情况下用户在手机上，你的网站可能含有‘m’,或者url取决用户的使用环境。你可以使用下面这个最佳实践——[canonical URL](https://support.google.com/webmasters/answer/139066?hl=zh-Hans)去提供更好的体验：

```
var url = document.location.href;
var canonicalElement = document.querySelector('link[rel=canonical]');
if(canonicalElement !== undefined) {
    url = canonicalElement.href;
}
```


## 相关链接
你能得到跟多的相关信息在 [ ChromeStatus](https://www.chromestatus.com/features/5668769141620736),但是为了节省你的点击，下面有几个重要的链接：


- [Launch tracking bug in Chrome](https://crbug.com/620973)
- [Intent to implement](https://bugs.chromium.org/p/chromium/issues/detail?id=620973)
- [Sample](https://github.com/mgiuca/web-share/blob/master/demos/share.html)
- [Share explainer](https://github.com/WICG/web-share/blob/master/docs/explainer.md)
- [Discussion on Discourse](https://discourse.wicg.io/t/web-share-api-for-sharing-content-to-arbitrary-destination/1561/3)



未来的工作还将允许他们注册为“共享接收者”，实现网络到应用程序共享，应用程序到网络共享和网络到网络共享，为网络应用程序提供平等的竞争环境。 就我个人而言，我非常兴奋。