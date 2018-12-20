# SwiftHttpRequest(Swift4)
![](https://img.shields.io/badge/Swift-4.0-green.svg?style=flat)


这里主要介绍了swift4版本的网络请求，封装中带有Progress及错误信息处理，缓存等的统一解决方案

[简书地址https://www.jianshu.com/p/caa1a57e7423](https://www.jianshu.com/p/caa1a57e7423)

----------
* [更新说明](#version_explain)
    - [增加跨类型解析方式](#2.1update)
* [Demo说明](#demo_explain)
    - [网络部分](#网络部分)
    - [Progress及信息处理](#Progress及信息处理)
    - [数据解析](#数据解析)
    - [缓存](#缓存)
 * [三方库介绍](#三方库介绍)
 * [接口说明](#接口说明)
 * [版本更新说明](#版本)
 ---------
<h2 id="version_explain"> 更新说明：</h2>
<h4 id="2.1update">version 2.1:</h4>

`Codable`增加跨类型解析方式：感谢[hhfa008](https://github.com/hhfa008/NumberCodable)大神提供的方式

*后台最常用的类型，也是最容易让我们出错的就是`Int`和`String`类型的不确定，
这里提供了后台同一个字段返回类型`Int`和`String`不确定时的解析方式，
可自行增加`Bool`类型等。*

自定义解析类型如下,示例见demo
```Swift
///跨类型解析方式
// 一个含有int，string的类，可用于解析后台返回类型不确定的字段。即：把int\string解析成TStrInt且解析后TStrInt的int和string都有值
//----- 使用时如果报未初始化的错误，而且找不到原因时，可以尝试先修复model以外的错误，也许这个错误就会消失。。。。 这是编译器提示错误的原因
struct TStrInt: Codable {
    var int:Int {
        didSet {
            let stringValue = String(int)
            if  stringValue != string {
            string = stringValue
            }
        }
    }

    var string:String {
        didSet {
            if let intValue = Int(string), intValue != int {
            int = intValue
            }
        }
    }

    init(from decoder: Decoder) throws {
        let singleValueContainer = try decoder.singleValueContainer()

        if let stringValue = try? singleValueContainer.decode(String.self)
        {
            string = stringValue
            int = Int(stringValue) ?? 0

        } else if let intValue = try? singleValueContainer.decode(Int.self)
        {
            int = intValue
            string = String(intValue);
        } else {
            int = 0
            string = ""
        }
    }
}

```

<h2 id="demo_explain"> Demo说明</h2>
Demo主要介绍Swift的网络部分，代码已更新到swift4

<h3 id="网络部分"> 一. 网络部分</h3>

请求均采用 `Alamofire`

请求封装方式分为：
- **1.Moya（一个star很多的`Alamofire`的上层封装，为本demo推荐方式。我在使用过程中发现moya是极其优美的网络请求方式）**
- **2.链式请求（如果你刚刚从OC转到swift，可能还不适应moya的方式，那么也可以用这种请求方式）**
- **3.仿AFN式请求（这应该是OC中常见的封装方式）**


<h3 id="Progress及信息处理"> 二. Progress及信息处理</h3>

加载动画及弹出框采用 `MBProgressHUD`


<h3 id="数据解析"> 三. 数据解析</h3>

在swift4之前，我一直用的是`HandyJSON`（下面有介绍）。在swift4之后我把model的解析转到到官方的Codable。


<h3 id="缓存"> 四. 缓存</h3>

缓存部分没有接入数据库，而是直接用了`write to file`,并将缓存封装到网络请求方法中

**本demo内容可直接用于项目开发**


<h2 id="三方库介绍"> 三方库介绍</h2>

[Alamofire](https://github.com/Alamofire/Alamofire)：Swift中著名的网络请求库

[Moya](https://github.com/Moya/Moya)：著名的Alamofire封装，让网络请求看起来更加的优美，更有利于阅读与迭代

[MBProgressHUD](https://github.com/jdg/MBProgressHUD): 进度条，弹出框,OC写的库

[Kingfisher](https://github.com/onevcat/Kingfisher): 加载网络图片，类似SDWebImage

**已弃用**

[HandyJSON](https://github.com/alibaba/HandyJSON) 是阿里巴巴开源的model的映射库。使用方式类似OC中的MJExtention

[ObjectMapper](https://github.com/Hearst-DD/ObjectMapper)：json解析库，需要手动写映射关系

具体可以看[在Swift语言中处理JSON - 转换JSON和Model](http://www.cocoachina.com/swift/20161010/17711.html)

<h2 id="接口说明"> 接口说明</h2>

接口：http://app.u17.com/v3/appV3_3/ios/phone/rank/list

返回实例：
```JSON
{
    "code": 1,
    "data": {
        "stateCode": 1,
        "message": "成功",
        "returnData": {
            "rankinglist": [
                {
                    "title": "月票",
                    "subTitle": "来不及了快上车，老司机票选人气漫画，带你飞！",
                    "cover": "http://image.mylife.u17t.com/2016/12/28/1482920243_f2kF1UculvH7.jpg",
                    "argName": "sort",
                    "argValue": "23",
                    "rankingType": "月票值"
                },
                {
                    "title": "点击",
                    "subTitle": "厉害了我的大触，原来大家都在这里看漫画！？",
                    "cover": "http://image.mylife.u17t.com/2016/12/28/1482920257_yznXvXJcrvk8.jpg",
                    "argName": "sort",
                    "argValue": "25",
                    "rankingType": "点击值"
                },
                {
                    "title": "吐槽",
                    "subTitle": "不可描述的吐槽，画面太美，快来看看。",
                    "cover": "http://image.mylife.u17t.com/2016/12/28/1482920418_vA222B8m6qi3.jpg",
                    "argName": "sort",
                    "argValue": "20",
                    "rankingType": "吐槽值"
                },
                {
                    "title": "新作",
                    "subTitle": "拒绝身体被掏空，人气新作为你补补血。",
                    "cover": "http://image.mylife.u17t.com/2016/12/28/1482920437_Cmg3we0vH3me.jpg",
                    "argName": "topic",
                    "argValue": "16",
                    "rankingType": "新作值"
                }
            ]
        }
    }
}
```



<h2> 作者语：</h2>

希望能达到抛砖引玉的效果

也给新学习swift的朋友一个简单的网络处理的方式

大家互相帮助，互相学习

如果对你有帮助还请给个Star，谢谢🙏（如果问题联系我： QQ 296369928）
----
<h2 id="版本"> 版本：</h2>

### 2.2 修改文件结构。使用moya时请配置MoyaConfig和HttpRequest文件。这是一个demo，不是一个拿来即用的三方库，所以请认真阅读代码，根据自己的项目做出更改和优化

### 2.1 `Codable`跨类型解析：`Int`解析成`String`， `String`解析成`Int`

### 2.0 更新到swift4，重新整理代码

### 1.2 新增`moya`的demo

### 1.1 新增链式请求的封装
* 链式请求可以只组合需要的函数，本身默认为常用方式，简化常用的链式调用
* 对于非默认值的请求可以自定义进行设置
* 方便添加自定义行为，利于扩展

### 1.0 类OC中的AFN封装
* 利于OC转swift的同学学习
* 其中对返回值做了JSON和String两种解析，String是为了方便实用HandyJSON，不使用HandyJSON可以自行删除部分代码
