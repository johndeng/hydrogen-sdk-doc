# 登入登出

## 登入

`my.BaaS.auth.loginWithAlipay({forceLogin, scopes, createUser})`

**参数说明**

| 参数            | 类型    | 说明         |
| :-------------- | :------ | :----------- |
| forceLogin      | Boolean | 是否强制登录 |
| scopes          | Array   | 需要用户授权的 scope 列表 |
| createUser      | Boolean | 是否创建用户，默认为 `true` |
| syncUserProfile | String  | 是否[同步第一层级用户信息](/js-sdk/account.md#同步第一层级用户信息)，可选值为 `overwrite`、`setnx`、`false`，默认值为`setnx`，仅在 forceLogin 为 true 时生效 |

- 当 `forceLogin` 为 `false` 时，为静默登录，不会弹窗授权框；
- 当 `forceLogin` 为 `true` 时，为强制登录，会弹窗授权框。授权成功后，后端可以拿到支付宝用户的用户信息。

`scopes` 参数用于在用户强制登录时，如果需要用户授权除了用户信息之外的其他权限
（例如 `auth_life_msg`、`auth_life_msg_tele` 等），则将这些额外的 scope 通过 `scopes` 参数传给接口。
（静默登录时，会忽略掉这个参数）

`createUser` 参数决定了一个新的支付宝用户第一次登录时的服务端处理行为。
默认为 `true`，服务端会有该用户创建一个知晓云用户记录。
当 `createUser` 为 `false` 时，服务端会终止登录过程，返回 404 错误码，开发者可根据该返回结果进行多平台账户绑定的处理。详见 [多平台用户统一登录](#多平台用户统一登录) 说明

{% include "/js-sdk/frag/_sync_user_profile_param.md" %}

> **info**
> 强制登录时，如果用户拒绝授权，则执行静默登录逻辑。

**返回值**
`Promise<UserRecord>`
UserRecord 对象的内容与接口请参考 [currentUser](/js-sdk/user.md)

**请求示例**

```js
// 支付宝用户登录小程序

// 静默登录
my.BaaS.auth.loginWithAlipay({
  forceLogin: false,
}).then(currentUser => {
  // 登录成功
}, err => {
  // 登录失败
})

// 静默登录
my.BaaS.auth.loginWithAlipay().then(currentUser => {
  // 登录成功
}, err => {
  // 登录失败
})

// 强制登录
my.BaaS.auth.loginWithAlipay({
  forceLogin: true,
  scopes: ['auth_life_msg', 'auth_life_msg_tele'],
  createUser: true,
}).then(currentUser => {
  // 登录成功
}, err => {
  // 登录失败
})
```

**登录成功返回示例**

`Promise<UserRecord>`
UserRecord 对象的内容与接口请参考 [currentUser](/js-sdk/user.md)

**登录失败返回示例**

err 对象结构请参考[错误码和 HError 对象](/js-sdk/error-code.md)

## 设置用户名和邮箱

用户通过支付宝登录后，可以设置邮箱、用户名、密码，以便下次可以通过用户名或者邮箱登录。

详情请参考 [更新用户名](../account.md) 和 [更新邮箱](../account.md)

## 关联支付宝账号

`UserRecord.linkAlipay({forceLogin, syncUserProfile})`

**参数说明**

| 参数            | 类型    | 说明         |
| :-------------- | :------ | :----------- |
| forceLogin      | Boolean | 是否强制登录 |
| syncUserProfile | String  | 是否[同步第一层级用户信息](/js-sdk/account.md#同步第一层级用户信息)，可选值为 `overwrite`、`setnx`、`false`，默认值为`setnx`，仅在 forceLogin 为 true 时生效 |

当使用账号密码登录账号，可以调用该接口，使支付宝账号与当前登录账号相关联。

`forceLogin` 参数的使用方法与上面登录接口一致。

{% include "/js-sdk/frag/_sync_user_profile_param.md" %}

## 多平台用户统一登录

假设现在同时支持支付宝小程序和 web 端，需要支付宝小程序新用户关联到已经注册好的用户账户，才能登录成功。
可以通过 `loginWithAlipay` 的参数 `createUser` 设置为 `false`，此时，服务端会判断该用户是否已经有账户记录，
如果没有，则返回 404 状态码。开发者可根据此状态码，跳转到需要填写用户名密码页面，进行已有账户的关联或新的账户的创建，
完成后，调用 `linkWithAlipay` 方法完成当前支付宝小程序用户与账户的绑定。下一次用户再次登录时，则会直接登录成功。

**示例代码**
```javascript
my.BaaS.auth.loginWithAlipay({
  createUser: false,
}).then(currentUser => {
  // 已经有用户记录，不是第一次登录，进入正常业务流程。
}, err => {
  // 这时候可以让用户先通过 my.auth.register() 注册一个账户，或者 my.auth.login() 登录一个已有账户，再使用 linkAlipay 进行绑定，这里以登录账户为例
  if (err.code === 404) {
    my.BaaS.auth.login({email: 'ifanrx@ifanr.com', password: 'ifanrx123'}).then(user => {
      user.linkAlipay().then(res => {
        console.log(res.statusCode)
        // 关联成功，下次可以通过 wx.BaaS.auth.loginWithAlipay 登录了
      })
    })
  }
})
```
