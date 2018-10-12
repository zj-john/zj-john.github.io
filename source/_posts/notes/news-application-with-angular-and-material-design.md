---
title: 如何利用Angular 6和Material Design构建新闻应用程序
categories:
  - angular6
  - material
  - javascript
  - application
  - 2018.10.05
tags:
  - JavaScript Weekly
toc: true
date: 2018-10-12 12:04:15
---

在本文中，我们将使用Angular 6和Google的Material Design构建一个新闻应用程序，这将帮助您在Web浏览器和移动设备中使用Angular达到很赞的效果。

<!-- more -->
您是否希望将Google的 [Material Design](https://material.angular.io)与[Angular](https://angular.io/)相结合？

在本教程中，我们将使用两个最强大和最受欢迎的资源Angular 6和Material Design来构建一个新闻应用程序。您将学习如何将Google的Material Design组件应用到Angular应用程序模板中，以便以专业的方式更改和设置应用程序样式。本教程还帮助您如何使用[News API](https://newsapi.org/)发出HTTP请求以获取实时新闻文章数据。

在我们开始构建应用程序之前，让我们快速回顾一下我们将要使用的资源，Angular和Material Design。

 ![使用Angular 6和Material Design的新闻应用程序](/images/JS_Weekly/news-application-with-angular-and-material-design/introduction.png)

### Angular是什么?
Angular - 根据[官方文档](https://angular.io/docs#what-is-angular) - 描述如下：

>“Angular可以帮您在Web平台上轻松构建应用程序。Angular结合了声明性模板，依赖注入，端到端工具以及一些集成好的最佳实践来解决开发挑战。Angular使开发人员能够构建Web，移动设备或桌面上的应用程序。”

简而言之，它是用于构建高度交互式和动态Web应用程序的最强大的JavaScript框架之一。

>“如前所述，Angular不仅功能强大，而且很受欢迎，目前使用它的公司有Upwork，Freelancer，Udemy，YouTube，Paypal，Nike，Google，Telegram，Weather，iStockphoto，AWS，Crunchbase等。”

### Google’s Material Design是什么?

Material Design是Google于2014年夏季为Android的新操作系统引入的一种设计语言。虽然它最初的重点是基于触摸的移动应用程序，但现在它的功能已经扩展到网络设计世界。

它是一个适应性强系统，可以作为指南，组件和工具，支持用户界面设计的最佳实践。它还得到了开源代码的支持，并得到了大量设计师和开发人员的支持，他们正在合作共同构建漂亮的产品。

### 为什么选择Angular和谷歌的Material Design？

这是一个选择问题。没有JavaScript框架是完美的。这完全取决于您的项目需求。编程语言也是如此。


现在，我不打算概述Angular的优点和功能。而是，我将与您分享为什么我选择Angular专门用于构建新闻应用程序。

1. 与任何新闻应用程序一样，通过HTTP协议与后端服务进行通信是至关重要的部分。最新的Angular中的 **HttpClient** 模块，它是旧Http的改进版本，可以帮助我们轻松地与服务API交互。

2. 当将存储在对象中的远程数据绑定到我们的应用程序模板中时，Angular 的 **model-view-viewmodel**（MVVM）将非常方便，其中组件扮演控制器/视图模型的一部分，模板代表视图。这就是我们所说的Angular模板语言。
**The model-view-viewmodel** (MVVM) of Angular will be handy when it comes to binding the remote data that will be stored in objects into our application template, where the component plays the part of the controller/viewmodel and where the template represents the view.

3. **双向绑定系统**，这意味着应用程序状态的任何更改都将自动反映到视图中，反之亦然。您会注意到，从侧边菜单中选择新闻资源时，这将改变我们新闻文章的状态。

4. 我最喜欢Angular的是 **SPA技术**。仅加载需要更改的页面部分肯定有助于我们的应用程序加载并更快速，更顺畅地执行。

当然，Angular还有许多其他优点和功能，您可以通过在线搜索快速查找。

### UI方面怎么样？

我们选择了Material Design，因为它的语言非常适合Angular，并且易于实现。

它也是一种非常流行的视觉语言; 它响应迅速，大多数Google应用程序都是用它构建的。我们希望我们的应用尽可能像Google应用。


我们的介绍到此为止。现在是时候查看项目概述，然后跳转到构建过程。

*   [项目概况](#项目概况)
*   [先决条件](#先决条件)
*   [创建Angular项目](#创建Angular项目)
*   [安装依赖项](#安装依赖项)
*   [获取免费的API密钥](#获取免费的API密钥)
*   [处理组件](#处理组件)
*   [定义Material的默认样式](#定义Material的默认样式)
*   [定义模板](#定义模板)
*   [结论](#结论)

### 项目概况
>“从各种[来源](https://newsapi.org/sources)获取最新的直播新闻文章，包括BBC新闻，CNN，TechCrunch，赫芬顿邮报等，以及不同的类别，如技术，体育，商业，科学和娱乐。”

这是您的应用程序完成后的外观：

 ![新闻应用预览图](/images/JS_Weekly/news-application-with-angular-and-material-design/project-overview.png)

这应该让你兴奋，不应该吗？让我们从构建应用程序开始。

### 先决条件

这是您实践本教程所需的内容：

*   已安装[Node.js](https://nodejs.org/en/)和npm;
*   已安装[Angular CLI](https://cli.angular.io/);
*   **Angular** 基本知识.

一旦这些东西不碍事，我们就可以继续了。

### 创建Angular项目
在本节中，我们将使用Angular命令行界面（CLI）生成一个新的Angular项目。为此，请转到CLI并运行以下命令：
```shell
    ng new news-app
```
接下来，通过运行以下命令将命令行指向项目的根文件夹：
```shell
    cd news-app
```

### 安装依赖项

要设置我们的依赖项，我们将只使用一个命令安装本教程所需的所有依赖项。别担心，我会在一秒钟内解释一下：

```shell
    npm install --save @angular/material @angular/animations @angular/cdk
```
我们使用此命令安装了三个软件包。

#### @angular/material
这是专为Angular框架使用的官方Material Design包。

#### @angular/animations
把Angular动画包，与Angular核心库分开，单独安装是必须的。某些material组件需要访问动画库，这就是我们在此处安装它的原因。(通过cli生成的项目，自带了@angular/animations包)

#### @angular/cdk
因为现代网络的发展都是基于组件的，所以有了 **CDK** 这样的“组件开发工具包”，这为我们提供了为您的组件高品质的预定义的行为，

当您想要将Google的material design与Angular应用结合时，建议安装下Angular CDK。

要了解有关Angular CDK的更多信息，请查看此[文章](https://blog.angular.io/a-component-dev-kit-for-angular-9f06e3b4b3b4)。

让我们运行我们的应用程序，看看一切正常。您可以通过运行以下命令来启动开发服务器：
```shell
    ng serve
```
现在，如果您在浏览器中访问[http://localhost:4200/](http://localhost:4200/)，您应该看到以下页面：

 ![Welcome to news app](/images/JS_Weekly/news-application-with-angular-and-material-design/welcome-to-news-app.png)]

现在，在代码编辑器中，导航到该文件`/src/app/app.module.ts`，然后添加我们刚刚安装的以下包：
```js
    … Other imports …
    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { MatButtonModule, MatCardModule, MatMenuModule, MatToolbarModule, MatIconModule, MatSidenavModule, MatListModule } from '@angular/material';
```
重要的是要了解这里发生了什么。首先，我们将导入动画包以动画我们的应用程序。

> 下一个重要的是Angular material的独特之处。之前，我们只包含一个material模块。现在，我们必须导入我们打算使用的每个material组件。

如您所见，我们在这里添加了七个不同的模块，用于素材按钮，卡片，菜单，列表工具栏，侧面导航和图标。

将这些包添加到您的`app.module.ts`文件后，请确保您的文件与以下内容匹配：
```js
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { HttpClientModule } from '@angular/common/http';
    import { NewsApiService } from './news-api.service';

    import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
    import { MatButtonModule, MatCardModule, MatMenuModule, MatToolbarModule, MatIconModule, MatSidenavModule, MatListModule } from '@angular/material';

    import { AppComponent } from './app.component';

    @NgModule({
      declarations: [
        AppComponent
      ],
      imports: [
        BrowserModule,
        BrowserAnimationsModule,
        HttpClientModule,
        MatButtonModule,
        MatMenuModule,
        MatCardModule,
        MatToolbarModule,
        MatIconModule,
        MatSidenavModule,
        MatListModule,
      ],
      providers: [NewsApiService],
      bootstrap: [AppComponent]
    })
    export class AppModule { }
```
**注意**：上述文件中的 **import { HttpClientModule } from @angular/common/http** 不是自动生成的，而是手动添加的。所以，请确保你也这样做。并且不要担心 **NewsApiService** 服务提供商，因为我们稍后会处理这个问题。

您可能想知道，我怎么知道要导入的模块的名称？官方[Angular material文档](https://material.angular.io/components)为您提供导入每个模块所需的确切代码。

如果单击左侧菜单中的任何组件，然后单击“API”选项卡，它将为您提供需要使用的确切导入行。

 ![API reference for Angular Material Components](/images/JS_Weekly/news-application-with-angular-and-material-design/api-reference.png)]

在设置方面，在我们真正开始在模板中使用和集成material components之前，我们需要做的就是这些。

您只需记住导入您计划使用的每个唯一组件。

### 获取免费的API密钥
我们将使用[News API](https://newsapi.org/)的JSON数据为我们提供一些新闻标题，我们将在应用程序模板中实现这些数据。

什么是News API服务？

News API是一个简单的HTTP REST API，用于搜索和检索来自整个Web的实时文章。

既然您知道新闻API是什么，下一步就是获得一个免费的[API密钥](https://newsapi.org/register)，它将帮助我们向服务器发出一些请求并获取新闻文章。

您只需注册30秒即可。您只需提供您的名字，电子邮件地址和密码即可。就这样。

注册后，您将在dashboard中找到已为您生成的API密钥。只需将其保存在桌面上的某个文本文件中即可; 因为我们将在下一章中使用它。

### 处理组件
在开始处理组件之前，您需要创建服务提供程序来管理与News API服务的交互。

#### 创建服务提供商

输入此命令以生成新的服务提供者：
```shell
    ng generate service NewsApi
```

之后，转到生成的`/src/app/news-api.service.ts`文件，并将以下代码添加到其中：
```js
    import { Injectable } from '@angular/core';
    import { HttpClient  } from '@angular/common/http';


    @Injectable({
      providedIn: 'root'
    })
    export class NewsApiService {

      api_key = 'PUT_YOUR_API_KEY_HERE';

      constructor(private http:HttpClient) { }
      initSources(){
         return this.http.get('https://newsapi.org/v2/sources?language=en&apiKey='+this.api_key);
      }
      initArticles(){
       return this.http.get('https://newsapi.org/v2/top-headlines?sources=techcrunch&apiKey='+this.api_key);
      }
      getArticlesByID(source: String){
       return this.http.get('https://newsapi.org/v2/top-headlines?sources='+source+'&apiKey='+this.api_key);
      }
    }
```
是时候使用我们的 **API密钥** 了。只需将其粘贴到“Put_YOUR_API_KEY_HERE”字样即可。

我们已导入`HttpClient`，它将负责对我们的端点进行API调用并为我们提取新闻标题。

现在，对于该`initSources`功能，我们只需使用一些新闻资源准备我们的左侧菜单。之后，我们创建了另一个函数，`initArticles`，它在应用程序启动后从 **TechCrunch** 中检索第一篇文章。

至于最后一个函数，`getArticlesByID`,它只是为根据参数带来一些文章。

#### The Main Component
服务提供商已完成。让我们转到该`/src/app/app.component.ts`文件并添加以下代码：
```js
    import { Component } from '@angular/core';
    import { NewsApiService } from './news-api.service';


    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.css']
    })
    export class AppComponent {

      mArticles:Array<any>;
      mSources:Array<any>;

      constructor(private newsapi:NewsApiService){
        console.log('app component constructor called');         
      }

      ngOnInit() {
            //load articles
          this.newsapi.initArticles().subscribe(data => this.mArticles = data['articles']);
        //load news sources
        this.newsapi.initSources().subscribe(data=> this.mSources = data['sources']);  
        }


      searchArticles(source){
        console.log("selected source is: "+source);
        this.newsapi.getArticlesByID(source).subscribe(data => this.mArticles = data['articles']);
      }

    }
```
我们在这里定义的两个属性：**mArticles**，保存新闻文章，和 **mSources**，保存新闻资源。两者都定义为数组。

在构造函数中，我们只是创建一个**NewsAPIService**实例。

接下来，我们在 **ngOnInit()** 函数上使用该实例来初始化我们的两个属性。

对于 **searchArticles** 函数，只要用户从左侧菜单中选择特定资源，就会触发它。然后我们将此参数传递给 **getArticlesByID** 服务提供程序函数以检索它的文章。


### 定义Material的默认样式
在我们由Angular CLI生成的`/src/styles.css`文件中，让我们添加以下内容：
```js
    @import '~@angular/material/prebuilt-themes/indigo-pink.css';
    body {
        padding: 2em 23em;
        background:lightgray;
    }
```
根据您的偏好，您可以更改`indigo-pink.css`为：

*   deeppurple-amber.css
*   indigo-pink.css
*   pink-bluegrey.css
*   purple-green.css

我还在`body`标签中添加了一些CSS ，只是为了演示这种布局。这有助于它看起来更像一个App，即使在桌面上。

我们还在 `/src/index.html` 结束head标记之前向我们的文件中添加两行：
```html
    <link href="https://fonts.googleapis.com/icon?family=Material+Icons" rel="stylesheet">
    <link rel="stylesheet" href="https://fonts.googleapis.com/css?family=Roboto:300,400,500,700,400italic">
```
第一行导入material design图标字体，第二行是Roboto字体，material design团队使用该字体。

### 定义模板
让我们首先在`/src/app/app.component.html`文件中添加以下模板：
```html
    <mat-toolbar color="primary">
      <button mat-button (click)="sidenav.open ()" ><mat-icon>menu</mat-icon></button>
      <span>News Headlines</span>  
      <span class="example-spacer"></span>
      <button mat-button [matMenuTriggerFor]="appMenu"><mat-icon>settings</mat-icon></button>
    </mat-toolbar>
    <mat-menu #appMenu="matMenu">
      <button mat-menu-item> Settings </button>
      <button mat-menu-item> Help </button>
    </mat-menu>
    <mat-sidenav-container class="example-container">

      <mat-sidenav #sidenav class="example-sidenav">
        <mat-list class="list-nav">
            <mat-list-item class="list-item" *ngFor="let source of mSources" (click)="searchArticles(source.id);sidenav.close();">

              <div mat-card-avatar [ngStyle]="{'background-image': 'url(../assets/images/'+ source.id +'.png)'}" class="example-header-image"></div>

              <span class="source-name"> {{source.name}}</span>

            </mat-list-item>
        </mat-list>
      </mat-sidenav>
      <mat-card class="example-card"  *ngFor="let article of mArticles">
        <mat-card-header>
          <div mat-card-avatar [ngStyle]="{'background-image': 'url(../assets/images/'+ article.source.id +'.png)'}" class="example-header-image"></div>
          <mat-card-title class="title">{{article.title}}</mat-card-title>
          <mat-card-subtitle>{{article.source.name}}</mat-card-subtitle>
        </mat-card-header>
        <img mat-card-image class="img-article" src={{article.urlToImage}} alt="">
        <mat-card-content>
          <p>
            {{article.description}}
          </p>
        </mat-card-content>
        <mat-card-actions class="action-buttons">
          <button mat-button color="primary"><mat-icon>thumb_up_alt</mat-icon> 12 Likes</button>
          <button mat-button color="primary"><mat-icon>comment</mat-icon> Comments</button>
          <button mat-button color="primary"><mat-icon>share</mat-icon> Share</button>
          <a mat-button color="primary" href={{article.url}} target="_blank" ><mat-icon>visibility</mat-icon> More</a>
        </mat-card-actions>
      </mat-card>
    </mat-sidenav-container>
```
那么，我们在这做了什么？

首先，我们定义一个带有左侧菜单的工具栏，以及应用程序的主标题和设置的右侧菜单。

接下来，我们把`*ngFor`用在源和文章上，并且这样做，我们的左侧菜单将保存新闻资源，主要内容将保存新闻文章。


需要注意的是，在`click`列表项的事件中，我们添加了两个函数，第一个功能是`searchArticles`我们已经解释过的，第二个功能是`sidenav.close()`在用户选择资源后自动关闭我们的左侧菜单。

#### 设计我们的组件
最后一件事就是访问该`/src/app.component.css`文件并将以下代码粘贴到其中：
```css
    .example-spacer {
      flex: 1 1 auto;
    }

    .example-card{
        margin-top: 4px;
    }

    .example-header-image {
      background-size: cover;
    }

    .title{
        font-weight: bold;
    }

    .img-article{
        height: 350px;
    }

    .action-buttons{
        text-align: center;
    }

    .example-container {
        width: 100%;
        height: auto;
        border: 1px solid rgba(111, 111, 111, 0.50);
    }

    .example-sidenav-content {
        display: flex;
        height: 75%;
        align-items: center;
        justify-content: center;
    }

    .example-sidenav {
        padding: 20px;
    }

    .source-name {
        margin-left:5px;
    }

    .list-item:hover{
        cursor: pointer;
        background-color: #3f51b5;
        color: white;
    }
```

#### 设置新闻资源的图像

移动到`/src/assets`目录，并创建一个名为的新文件夹`images`。然后，从[Google云端硬盘链接](https://drive.google.com/open?id=1tst2G_pVlpo41A3XmmrnGOP1PzaNJJOi)或[GitHub存储库](https://github.com/rachidsakara/angular-news-application-with-material/tree/master/src/assets/images)下载这些图像。

它们是我们新闻资源的logo。下载后，将所有图像文件复制并粘贴到 `images`刚刚创建的文件夹中。

一切都完成后，运行：
```shell
    ng serve
```

现在，您的应用应该如下面的截图。太棒了，呵呵！

 ![the news application final product](/images/JS_Weekly/news-application-with-angular-and-material-design/angular-material-design-final-product.png)]

一切都完成后启动应用程序 ([预览](/images/JS_Weekly/news-application-with-angular-and-material-design/angular-material-design-final-product.png))

请注意，当新闻片段加载到主页面上时，“更多”按钮（如上图所示）会让用户阅读全部故事。

### 结论
我希望这个教程很有用，你很享受能够构建这个应用程序。与此同时，[Angular Material](https://material.angular.io/)文档非常酷。它为您提供了每个组件的概述，API和示例。


这个应用程序的完整源代码可以在[GitHub](https://github.com/rachidsakara/angular-news-application-with-material)上找到。您还可以查看[此网站](https://world-headlines.herokuapp.com)，该网站已实现本教程中使用的服务API。

## 资源
* 原文：https://www.smashingmagazine.com/2018/10/news-application-with-angular-and-material-design/
* Angular Material：https://material.angular.io/
