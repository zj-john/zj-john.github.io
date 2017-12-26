---
title: img标签的src为一个远程图片的base64编码
categories:
  - tips
tags:
  - image
  - node
  - base64
toc: true
date: 2017-12-15 10:55:57
---
> 问题：img标签的src为一个远程图片的base64编码

在修改一个fork的项目时，有一个展示用户头像的地方，用的是后端一个判断处理后返回图片编码的url。  
原先的内容是从本地读取，现在需要改成从一个远端获取的图片地址。

<!-- more -->

## 解决方法
方法如下：
request请求图片url返回的response body本身就可以直接使用。不过当时在编码部分饶了很久。

```JavaScript
async avatar(ctx) {
    try{
        // ...
        let dataBuffer, type;
        const uri = 'https://***.google.cn/img/userPic/user_id.jpg';
        const options = {
          "uri": uri,
          "strictSSL": false,
          // 关键在这里
          "encoding": null
        }
        try {
          // getPromiseByAjaxRequest 返回的是 request.body
          let getImage = await common.getPromiseByAjaxRequest(options);
          dataBuffer = getImage;
          type = 'image/jpeg';

          // 二进制转为base64编码
          // dataBuffer = getImage.toString('base64');
          // 如果使64编码，应该要加上data:image/jpg;base64,这个前缀。
          // ！！！！！！这个暂时未测试，后续测下
        } catch(e) {
          // 默认头像
          dataBuffer = fs.readFileSync(path.join(WEBROOT, 'static/image/avatar.png'));
          type = 'image/png'
        }
        ctx.set('Content-type', type);
        ctx.body = dataBuffer;
    }catch(err){
        ctx.body = 'error:' + err.message
    }
}
```
request包option中encoding的解释如下：
>encoding - encoding to be used on setEncoding of response data. If null, the body is returned as a Buffer. Anything else (including the default value of undefined) will be passed as the encoding parameter to toString() (meaning this is effectively utf8 by default). (Note: if you expect binary data, you should set encoding: null.)

也就是说传null的时候，会返回一个二进制数据构成的Buffer。

> 补充下：貌似二进制的转码不能用缓存还是localstorage，base64可以，这个之后要试下
