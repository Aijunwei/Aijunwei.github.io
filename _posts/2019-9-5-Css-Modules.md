---
layout: post
title: Css Modules初识
---

## 什么是CSS Modules

套用[CSS Modules](https://github.com/css-modules/css-modules)官网的话来说，一个css文件代表一个模块，这个css文件中的类名（class names）和 动画名（animation names）默认都有一个局部作用域，简单来说就是css也有作用域了（其实这里不是很准确，只是利用了名字转换，让css像是拥有了作用域一般）。  
Just show me the code  
原始代码
```css
    /*style.css*/
    .header {
        color: red;
        font-size: 20px;
    }
    /*或者*/
    :lcoal(.header) {
        color: red;
        font-size: 20px;
    }
```

```jsx
    import style from './style.css';
    ...
    render() {
        return <div className="page">
            <section className={style.header}>
                header
            </section>
        </div>
    }
```
转换之后代码
```css
    .style__header___YHKJLJH {
        color: red;
        font-size: 20px;
    }
```

```html
    <div class="page">
        <div class="style__header___YHKJLJH">
            header
        </div>
    </div>
```
## 为什么要用CSS Modules
   个人觉得主要是CSS Modules用于提供css局部作用域的能力，让css更加可控，避免污染全局样式。以往开发过程中往往只能靠命名规范，已经程序员自觉遵守团队的命名规范来避免样式污染。当项目越来越大，团队成员越来越多，就很难避免出现样式被污染的情况。此时改起样式难免有点束手束脚了，那么如果现在有一种方式（CSS Modules）能让你写的样式完全与其他的样式隔离，是多么爽的事情。

## 先来了解css-loader中与CSS Modules相关的配置 

   CSS Modules其实并不是官方的功能，而是项目在编译打包阶段来修改类名，替换对应的class，实质上webpack打包时是依赖css-loader来进行处理，让CSS Modules生效的。  
   |  name  | Default  | Description |
   |  ----  | ----  | ----  |
   | modules  | false | true表示开启CSS Modules |
   | sourceMap  | false | true开启sourceMap，开发环境下开启比较实用 |
   | getLocalIdent | undefined | 自定义生成的类名| 
   | localIdentName | [path]___[name]__[local]___[hash:base64:5] | 定义了类名的模板，可以适当修改

## 开始实战吧
这里我们就以reactjs为例，来开启CSS Modules之旅。

这里我们做一个默认的约定，使用.module.css或者module.scss作为文件后缀，区分全局的样式和局部样式。

+ 首先使用create-react-app创建一个my-app项目,  然后运行项目(创建过程需要一定时间，因为这里会把依赖包都安装好。)

```
npx create-react-app my-app

npm run start
```
演示项目使用的版本信息
{
  "name": "my-app",
  "version": "0.1.0",
  "private": true,
  "dependencies": {
    "react": "^16.9.0",
    "react-dom": "^16.9.0",
    "react-scripts": "3.1.1"
  },
  "scripts": {
    "start": "react-scripts start",
    "build": "react-scripts build",
    "test": "react-scripts test",
    "eject": "react-scripts eject"
  },
  "eslintConfig": {
    "extends": "react-app"
  },
  "browserslist": {
    "production": [
      ">0.2%",
      "not dead",
      "not op_mini all"
    ],
    "development": [
      "last 1 chrome version",
      "last 1 firefox version",
      "last 1 safari version"
    ]
  }
}
```
+ 不做任何修改即可使用CSS Modules  
  在my-app/src目录添加一个style.module.css文件
  ```css
    .title {
        font-size: 25px;
    }
  ```
  ```js
    import React from 'react';
    import logo from './logo.svg';
    import './App.css';
    import style from './style.module.css';

    function App() {
    return (
        <div className="App">
        <header className="App-header">
            <img src={logo} className="App-logo" alt="logo" />
            <p className={style.title}>Hello CSS Modules</p>
        </header>
        </div>
    );
    }

    export default App;
  ```  
  保存后查看浏览器，可以在控制台中看到p标签的class="style_title__2t5Z0"，对应的样式类名title也转换成style_title__2t5Z0。可见使用create-react-app, 默认已经支持CSS Modules。
  ![默认运行支持CSS MOdules](https://user-images.githubusercontent.com/13660589/64322072-6c73a980-cff4-11e9-87b4-b2ed1acc64c0.png)

  + 查看webpack配置  
  新版本create-react-app创建的项目把打包构建的脚本都放在npm包中了，所以需要用npm run eject命令解压出相关文件夹,运行成功后项目目录下会增加两个文件夹config，scripts，我们主要查看config/webpack.config.js，直接查看oneOf里面的内容中的这一块

  ```js
    /*css*/
    {
        test: cssModuleRegex,
        use: getStyleLoaders({
        importLoaders: 1,
        sourceMap: isEnvProduction && shouldUseSourceMap,
        modules: true,
        getLocalIdent: getCSSModuleLocalIdent,
        }),
    },
    /*scss*/
    {
        test: sassModuleRegex,
        use: getStyleLoaders(
        {
            importLoaders: 2,
            sourceMap: isEnvProduction && shouldUseSourceMap,
            modules: true,
            getLocalIdent: getCSSModuleLocalIdent,
        },
        'sass-loader'
        ),
    },

    //cssModuleRegex是匹配cssModule的正则，/\.module\.css$/
    //getStyleLoaders 是用于获取对应的loader，我们只需关注其中的css-loader，css-loader使用的options就是getStyleLoaders的第一个参数，modules: true 打开css modules，其实css modules是由css-loader提供支持
    const getStyleLoaders = (cssOptions, preProcessor) => {
        const loaders = [
            ...
            {
                loader: require.resolve('css-loader'),
                options: cssOptions,
            },
            ...
            return loaders;

    }
  ```
  可见在create-react-app创建的项目中CSS Modules已经开箱可用了（支持css ，scss），只需要修改一下使用样式的方式（import）,以及使用module.css 和module.scss(sass)后缀

  + 已有项目中引入CSS Modules  
  实际情况中，我们经常是需要在自己搭建的脚手架中引入CSS Modules，那也是非常简单的事情，只需要修改css-loader相关配置即可。sourceMap只在开发环境下打开，localIdentName在开发环境下显示比较详细，可以自由配置，而生产环境下直接显示编码后的hash。推荐使用module.scss，module.css作为CSS Modules文件的后缀，和已有的样式文件区分处理。

```js
    //是否开发环境
    const isDevelop = true;
    ...
     {
        test: /\.module\.scss$/,
        use: [
            'style-loader',
            {
                'css-loader',
                options: {
                    sourceMap: isDevelop,
                    modules: true,
                    localIdentName: dev ? '[path]___[name]__[local]___[hash:base64:5]' : '[hash:base64]',
                }
            }
            'postcss-loader',
            'sass-loader'
    },
```

+ 终极武器 babel-plugin-react-css-modules  
现在已经解决了在样式文件中不再需要:local来标志哪些类名需要进行转换的问题，那么在使用的时候还是有一些问题，CSS Modules推荐使用驼峰命名，What？ 
show me code
```js
    import style from './style.module.css';

    <div className={style.pageHeader}></div>

    <div className={style['page-header']}></div>
```
很明显使用驼峰简洁许多了，官方推荐也不是没有道理的。除此之外，每次写className时，都要用a.b方式，能不能更简单一些？当然可以：
```js
    import './style.module.css';
    ...
    render() {
        return (
            <div styleName="title"></div>
        )
    }
```
这样是不是感觉简单高效了许多。不过还需要借助一个babel插件babel-plugin-react-css-modules，这个插件会在打包阶段把styleName属性的值转换为对应的名字（generateScopedName定义的格式），之后把styleName属性值加到已有className中（如果没有className则创建）。

```js
    npm install babel-plugin-react-css-modules --save-dev
    //用于支持scss
    npm install postcss-scss
```
需要在webpack配置文件的babel-loader添加配置如下：
```js
    {
        test: /\.(js|mjs|jsx|ts|tsx)$/,
        loader: 'babel-loader',
        options: {
            plugins: [
                ...
                [
                    'react-css-modules',
                    {
                        webpackHotModuleReloading: true,
                        autoResolveMultipleImports: true,
                        generateScopedName: '[path]___[name]__[local]___[hash:base64:5]',
                        filetypes: {
                            '.scss': {
                                syntax: 'postcss-scss',
                            }
                        }
                    }
                ]
            ]
        }
    }    
```
其中generateScopedName如果配置，则需要和style-loader中的localIdentName保持一致，否则会导致styleName使用的名字与实际生成的classname不一致，样式无效！filetypes的配置是为了支持scss。


