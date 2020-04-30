---
title: hash路由下，允许单路由跳过登录
categories:
  - notes
tags:
  - hashrouter
  - node
  - auth
toc: true
date: 2020-04-30 20:36:38
---

# 背景
有个同事来问，在node层做sso登录验证的情况下，如果想让现有的一个路由绕过登录应该怎么做？

要解决这个问题，先看一下目前的结构：

- 前端采用react全家桶构建SPA，使用hash路由。
- 在node层用koa做http server，验证登录，和做service转发（解决跨域）

原先的方式中，koa的第一个中间件函数就是验证SSO，也就是所有路由都会经过SSO验证通过后才会继续向下走。


<!-- more -->

# 之前部分代码

## node
```js
// 跳转到sso地址完成登录后，sso会返回一个key为ticket的query参数，根据这个ticket完成后续cookie的注册。这部分逻辑不用太关心
app.use(async (ctx, next) => {
    if (ctx.cookies.get("root_principal")) {
        await next();
    }
    else {
        if (ctx.query["ticket"] !== undefined) {
            const ticket = ctx.query["ticket"];
            const principalId = sso.AppID + utils.randStr();
            const is_sso_cookie_ok = await sso.has_sso_cookie_ok(ticket, principalId);
            if (is_sso_cookie_ok) {
                ctx.cookies.set("root_principal", principalId, { "expires": utils.expireDate(7) });
            }
            ctx.redirect(ctx.origin);
        } else {
            ctx.redirect(sso.LoginUrlPrefix + ctx.origin);
        }
    }
})

```

## 页面
```js
// routes
const Routes = [
    {
        path: "/one",
        component: ComponentOne
    },
    {
        path: "/two",
        component: ComponentTwo
    },
    {
        redirect: true,
        path: "/",
        to: "/one",
    }
];


// 页面使用
const switchRoutes = (
  <Switch>
    {Routes.map((prop, key) => {
      if (prop.redirect) {
        return <Redirect from={prop.path} to={prop.to} key={key} />;
      }
      return <Route path={prop.path} component={prop.component} key={key} />;
    })}
  </Switch>
);

```

# 几个误区

## 通过node判断path
因为是hash路由，对 website.com/#/one 这样的路由, 在node端只能收到根路由，hash的内容会被自动忽略掉。

## 其它方式代替hash进行判断
因为hash路由不能被node识别，那么用node会接受的数据来判断呢？比如cookie。

在页面route切换的时候，通过document.cookie，种上标识路由的cookie。

这种方式node确实可以取到cookie值，但是

只有在浏览器第一次请求node的时候，之后的路由切换，都是在页面进行，根本不会发送请求到node层。

## brower route
页面全局路由从hash路由切换到brower路由，这样的话node就可以接收到每次请求的path，可以相对做处理。

但是这种方式侵入式较强，新的path有可能和已有的service的path冲突。这里不与采用。


# 之后的方式

## 思路
上面的误区中在思路上也有个误区，把对path的判断放在咋node层面。

这样的问题是，如果有path的调整，需要在node层也做同样的适配，不是一个可扩展性的做法。

所以新的思路：

1. 页面route中添加bool类型的auth字段，标识是否需要登录
2. 路由跳转时，当auth为true，且当前未登录（由Service返回状态）时，跳转到一个\<Login \/>的组件
> 在route的跳转中，只支持组件，不支持链接地址
3. 在Login组件中，用window.loaction.href跳转到/login地址
> 这里不带#号
4. 跳到/login时，由node接管后面的登录服务，登录完成后，跳转到ctx.origin
5. 下次路由时，已经是已登录状态，把路由交还给页面

## node部分代码
```js
// 跳转到sso地址完成登录后，sso会返回一个key为ticket的query参数，根据这个ticket完成后续cookie的注册。这部分逻辑不用太关心
app.use(async (ctx, next) => {
    if (ctx.path === '/' && ctx.query["ticket"] !== undefined) {
        const ticket = ctx.query["ticket"];
        const principalId = sso.AppID + utils.randStr();
        const is_sso_cookie_ok = await sso.has_sso_cookie_ok(ticket, principalId);
        if (is_sso_cookie_ok) {
            ctx.cookies.set("root_principal", principalId, { "expires": utils.expireDate(7) });
        }
        ctx.redirect(ctx.origin);
    } else {
        await next()
    }
})

//getUserInfo可以获取当前登录人的信息，为null时表示为登录，这里用这个service验证当前是否登录
app.use(async (ctx, next) => {
    if (ctx.path === '/getUserInfo') {
        const principalId = ctx.cookies.get("root_principal");
        const userInfo = await sso.getUserInfo(principalId);
        ctx.response.body = userInfo;
    } else {
        await next()
    }
})

// 增加了对login的处理，返回一个sso的跳转地址
// 这里在node层跳转，而不是在页面中直接跳转这个地址，是因为这个登录地址根据当前部署的环境不同会有区别，node层已经有了环境标识
app.use(async (ctx, next) => {
    if (ctx.path === '/login') {
        ctx.redirect(sso.LoginUrlPrefix + ctx.origin);
    } else {
        await next()
    }
})
```

## 页面部分代码
```js
// routes
const Routes = [
    {
        path: "/one",
        component: ComponentOne,
        auth: true
    },
    {
        path: "/two",
        component: ComponentTwo,
        auth: false
    },
    {
        redirect: true,
        path: "/",
        to: "/one",
    }
];

//路由跳转 user是从node层获取当前登录用户的数据信息。如果为null，说明未登录。从context中传到组件中。
const switchRoutes = (user) => (
  <Switch>
    {Routes.map((prop, key) => {
      if (prop.redirect) {
        return <Redirect from={prop.path} to={prop.to} key={key} />;
      }
      return <Route path={prop.path} key={key} render={() => {
        if (user == null && prop.auth) {
          return <Login />
        } else {
          return <prop.component />
        }
      }} />;
    })}
  </Switch>
);

//Login 组件
class Login extends React.Component {
    componentDidMount() {
        window.location.href = "/login";
    }
    render() {
        return (<div>正在跳转到登录页面...</div>);
    }
}
```

