#### Todo

- [x] 1. 封装`post`和`get`请求
- [x] 2. 拦截器
- [ ] 3. 页面初步加载，获取 Cookie(或 localStorage)中的 token
- [ ] 4. 获取路径参数：useLocation、useHistory、useParams、- [xs Match 几种的区别使用
- [ ] 5. 如何判断 token 失效
- [ ] 6. OauthPopup 如何使用

[GitHub OAuth](https://ruanyifeng.com/blog/2019/04/github-oauth.html)

[Cookie、localStorage 和 sessionStorage 的区别](https://juejin.cn/post/6970291738652966942#heading-4)

## 第三方库

### Sequelize[链接](https://www.sequelize.cn/)

Sequelize 是一个基于 promise 的 Node.js ORM(对象关系映射)
orm 可以简化操作数据库，更好的约束数据类型
[node orm Sequelize 的简单使用](https://juejin.cn/post/6990992410075463687#heading-0)

### loadsh[链接](https://www.lodashjs.com/)

Lodash 是一个一致性、模块化、高性能的 JavaScript 实用工具库。

### reduxjs/toolkit

[js 快速开始](https://cn.redux.js.org/tutorials/quick-start#%E4%BD%BF%E7%94%A8%E6%80%BB%E7%BB%93)
[ts 应用案例](https://cn.redux.js.org/tutorials/essentials/part-2-app-structure#%E5%BA%94%E7%94%A8%E7%9B%AE%E5%BD%95)
[Redux 步逻辑与数据请求](https://cn.redux.js.org/tutorials/essentials/part-5-async-logic/#使用-createasyncthunk-请求数据)

Redux Toolkit 是 Redux 官方强烈推荐，开箱即用的一个高效的 Redux 开发工具集。
Redux store 对异步逻辑一无所知。它只知道如何同步 dispatch action，通过调用 root reducer 函数更新状态，并通知 UI 某些事情发生了变化。需要中间件处理异步逻辑 + `createAsyncThunk` API。

<img src="https://cn.redux.js.org/assets/images/ReduxAsyncDataFlowDiagram-d97ff38a0f4da0f327163170ccc13e80.gif
" style="zoom: 33%;" />

## CSS

```scss
//控制兄弟节点的display!!!  [img与m-sub_menu互为兄弟节点]
img {
  border-radius: 20px;

  //控制兄弟节点的display!!!
  &:hover + .m-sub_menu {
    display: block;
  }
}
```

## Github Auth 流程([流程图](https://juejin.cn/post/6844903957827944462#heading-22))

### 用户授权该网站得到授权码

1. 点击按钮
2. 跳转到 github 授权网址

```js
//client_id告诉 GitHub 谁在请求 , 网站的id
//redirect_uri是稍后跳转回来的网址

https://github.com/login/oauth/authorize?
    client_id=7e015d8ce32370079895&
    redirect_uri=http:/3000/login
```

3. 用户授权，授权成功，跳转到 redirect_uri 的网址，并带上授权码

```js
http://localhost:3000/login?code=b40e2a1cf08b7884d2ea
```

### 向`github`去获取用户数据

4. 前端获取路径中的 `code`，发起一个后端请求

5. 后端中向 `github` 地址请求令牌

```js
//github.com/login/oauth/access_token?' +
https: `client_id=${clientID}&` +
  `client_secret=${clientSecret}&` +
  `code=${requestToken}`
```

6. 拿着令牌去获取 github 的用户数据

```js
https://api.github.com/user`,
headers: {
    accept: 'application/json',
    Authorization: `token ${accessToken}`
}
```

## 登录鉴权流程

1. 部分路由未登录时可访问，部分不可访问
   操作：使用 useRoutes 生成路由中，使用鉴权组件 Auth 包裹权限路由，鉴权组件 Auth 内判断当前的 store 中是否已经存储 token，没有则跳转至登录页
2. 进入登录页，点击 github 第三方登录按钮，初次会有授权页面出现，授权成功后会跳转到 redirect_uri(授权码 code 以键值对的形式在路径中)
3. redirect_uri(此处设置为/login), login 组件中加入页面初次加载事件，判断路径是否存在 code 参数，有的话，拿着 code，向后端发送一个请求
4. 后端请求中向 github 申请令牌，再拿着令牌向 github 拉取用户数据，数据库内如何未有该账户，加入该账户；并拿着用户数据生成一个 token 返回
5. token 返回后 store 与 Cookie(或 localStorage)中存储 token
6. 封装的 axios 请求中，需要每次将 token 放在请求头中，后端如果需要用户 id 等数据的话，从 token 中取
7. 整个应用初步加载时，需要判断 Cookie(或 localStorage) 是否已经有 token 了，如果有，就算登录了

## 权限控制

1. 普通按钮显示权限：在具体 HTML 位置中，根据 store 的 userInfo 判断显隐
2. 路由按钮显示权限：根据 store 的 userInfo(role)。。。
3. 路由权限：使用鉴权组件 Auth 包裹权限路由，Auth 中判断 store 中存储的 userInfo。。。。
4. api 权限：。。。。

## axios 中的 get 与 post

1. 三种请求方式：`axios.post()`,`axios.get()`,`axios()`
2. `axios.get(带参数的 url)`

```js
axios.get("http://localhost:8080/api?list=1&&age=12")
给后端的参数是 ==> list:1 age:12
```

3. `axios.post(url,{data 键值对})`

```js
axios.post("http://localhost:8080/api", {
  list: 1,
  age: 12,
})
```

4. 通用

```js
axios({
  url: "http://localhost:8080/api", // ==>请求地址
  params: {}, //==> get请求传参
  data: {}, //==> post请求传参
  method: "post/get", //==> 请求方式
})
```
