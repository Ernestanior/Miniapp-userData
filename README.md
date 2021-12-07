## 获取用户基本信息以及用户唯一标识openID的获取步骤

### 1. 首先是用户基本信息的获取

小程序的更新迭代很快，旧版本获取用户基本信息的方式在现在最新版本已经获取不到了（官方文档说明：在4月13日前发布的，线上环境2.16.0基础库以下已经不支持老版getUserInfo方法, 在4月13日前发布的小程序版本getUserInfo不受影响）

#### 先来看看旧版本的用户获取（新版本已废除，此处仅作参考）

wxml文件
```wxml
<button open-type="getUserInfo" bindgetuserinfo="bindGetUserInfo">授权登录</button>
```

js文件
```js
wx.getUserInfo({
  success: function(res) {
    console.log(res)
  }
})
```
