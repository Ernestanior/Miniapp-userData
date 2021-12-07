## 获取用户基本信息以及用户唯一标识unionID,openID的获取步骤

### 1. 首先是用户基本信息的获取

小程序的更新迭代很快，旧版本获取用户基本信息的方式在现在最新版本已经获取不到了（官方文档说明：在4月13日前发布的，线上环境2.16.0基础库以下已经不支持老版getUserInfo方法, 在4月13日前发布的小程序版本getUserInfo不受影响）

#### 先来看看旧版本的用户获取（新版本已废除，此处仅作参考）

.wxml
```
<button open-type="getUserInfo" bindgetuserinfo="bindGetUserInfo">授权登录</button>
```

.js
```
wx.getUserInfo({
  success: function(res) {
    console.log(res);
  }
})
```

#### 再来看看目前新版本的用户获取

.wxml
```
<button bindtap="getUserProfile">授权登录</button>
```

.js
```
getUserProfile(e) {
    wx.getUserProfile({
      success: (res) => {
        console.log(res);
      }
    })
  },
```

#### 之前的getUserInfo已经不再弹窗，获取的也是默认数据，新版本通过getUserProfile获取

### 2. 接下来是敏感数据，用户唯一标识unionID,openID的获取

在小程序中，能够作为每个用户账号的唯一标识就是unionID和openID, 这时有人会问以下问题：
1. 这两个ID都有什么意义？
2. 唯一标识有一个就够了为什么这有两个？
3. 这两个ID有什么区别，我们平时应该用哪一个作为唯一标识比较好？

回答：
OpenID: 这个ID就好比我们的身份证，在我们不知道某个人的情况，可通过OpenID来查询某个用户，这个OpenID不是我们手动生成的，需要前端通过微信提供的API wx.login()拿到一个code，再用这个code发请求到微信sns后台获取sessionKey和OpenID.
UnionID: 其实unionID和OpenID在本质上是没有任何区别的，如果开发者拥有多个移动应用、网站应用、和公众帐号（包括小程序），这时候同一个用户在使用不同小程序的时候，OpenID是不一样的，此时此刻将会用到unionID，因为unionID只要是在同一主体下面，unionID这个值永远是一样的，可以用来判断是否为同一个人。但如果只针对于一个小程序来说,openID和unionID基本没什么区别。
一句话概括：同一用户，对同一个微信开放平台下的不同应用，UnionID相同，openID不同。

#### OpenID获取：

获取OpenID需要向微信提供的特定url发送请求，
请求地址：https://api.weixin.qq.com/sns/jscode2session(微信官方提供的),

请求参数：
<img src="https://github.com/Ernestanior/Miniapp-userData/blob/087e5046098d5a0c854dc38b8e5df84330190ab1/screenshot/p1.png" width="1520px">

首先你要获取appId和appSecret这两个参数，可以从微信公众平台里的开发管理中获取，如下图：

<img src="https://github.com/Ernestanior/Miniapp-userData/blob/087e5046098d5a0c854dc38b8e5df84330190ab1/screenshot/p2.png" width="320px">

接下来是代码：
.wxml
```
 <button bindtap="getUserProfile">登录</button>
```

.js
```
getUserProfile(e) {
    wx.login({
      success(res){
        const {code}=res;
        wx.request({
          url: 'https://api.weixin.qq.com/sns/jscode2session',
          data:{
            appid:appID,//获取方式在上图中
            secret:appSecret,//获取方式在上图中
            js_code:code,
            grant_type:'authorization_code'
          },
          method:"GET",
          success(res){
            console.log(res);//里面包含openID和session_key
          }
        })
      }
    })
  },
```
点击按钮之后就能够拿到openID了,数据如下：

<img src="https://github.com/Ernestanior/Miniapp-userData/blob/27a7f62abc6299dcbe8c7bf362b26c57e52167c0/screenshot/p3.png" width="1520px">

#### UnionID获取：（官方给出的获取方式）

绑定了开发者帐号的小程序，可以通过以下途径获取 UnionID。
1. 开发者可以直接通过 wx.login + code2Session 获取到该用户 UnionID，无须用户授权。
2. 小程序端调用云函数时，可在云函数中通过 Cloud.getWXContext 获取 UnionID。
3. 用户在小程序（暂不支持小游戏）中支付完成后，开发者可以直接通过getPaidUnionId接口获取该用户的 UnionID，无需用户授权。注意：本接口仅在用户支付完成后的5分钟内有效，请开发者妥善处理
