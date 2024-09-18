---
title: 前端随笔之七Webpack
categories: 2023前端面试梳理
tags: 前端随笔
date: 2023-04-24 09:48:00
---

## webpack 面试题汇总

### 1.你知道webpack的作用是什么吗？
* (1) **模块打包。** 可以将不同模块的文件打包整合在一起，并且保证他们之间的引用正确，执行有序，利用打包我们就可以在开发的时候根据我们自己的业务自由划分文件模块，保证项目结构的清晰和可读性
* (2) **编译兼容。** 通过“webpack”的loader机制，不不仅仅可以帮助我们对代码做“polyfill”，还可以进行斌阿姨转换如“.less”，“.vue”,”.jsx”这类在浏览器无法识别的格式文件，让我们在开发时候可以使用新特性和新语法开发，提高开发效率
* (3) **能力扩展。** 通过webpack的plugin（插件）机制，在我们实现模块化打包和编译兼容的基础上，可以进一步实现诸如按需加载，代码压缩等一系列功能，帮助我们进一步提高自动化程度，工程效率以及打包输出的质量

### 2.模块打包运行原理
webpack的整个打包流程：
> * 1.**初始化参数：**从配置文件和Shell语句中读取与合并参数，得到最终的参数
> * 2.**开始编译：**用上一步得到的参数初始化Compiler对象，加载所有配置的插件，执行对象`run`方法开始执行编译
> * 3.**确定入口：**根据配置中的`entry`找出所有的入口文件
> * 4.**编译模块：**从入口文件出发，调用所有配置的`loader`对模块进行翻译，再把翻译后的内容转成AST，通过对AST的分析找出该模块依赖的模块，在`递归`本步骤直到所有入口依赖的文件都经过了本步骤的处理
> * 5.**完成模块编译：**经过第4步使用`loader`翻译完所有模块后，得到了每个模块被翻译后的最终内容以及他们之间的`依赖关系图`
> * 6.**输出资源：**根据入口和模块之间的依赖关系，组装成一个个包含多个模块的`Chunk`，再把每个`Chunk`转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会
> * 7.**输出完成：**在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统


### 3.webpack 和 gulp 的区别是什么
> webpack是基于模块化构建工具，gulp是基于工作流的构建工具
> webpack是打包器，它以一个入口为起点，构建出整个项目的依赖关系图，然后进行打包合并，生成打包结果
> gulp是一个过程管理器，每一步做什么完全看开发人员如何配置，把每一步骤连接起来形成一个完整的构建流水线
> 两这不矛盾，完全可以在一个工程中同时使用webpack 和 gulp，将webpack作为gulp流水线中的一环

### 4.webpack中loader属性和plugins属性区别是什么
> 他们都是webpack功能的扩展点
> loader是加载器，主要用于代码转换，比如js代码降级，css预编译，模块化等
> plugins是插件，webpack打包流程中每一个环节都提供了钩子函数，可以利用这些钩子函数参与打包生命周期中，更改或增加webpack的某些功能，比如生成页面和css文件，压缩打包结果

### 5.webpack核心概念都有那些
> * loader
> 加载器，主要用于代码转换，比如js代码降级，css预编译，模块化等
> * plugin
> 插件，webpack打包流程中每一个环节都提供了钩子函数，可以利用这些钩子函数参与打包生命周期中，更改或增加webpack的某些功能，比如生成页面和css文件，压缩打包结果
> * module
> 模块。webpack将所有依赖均视为模块，无论是js，css，html，图片，统统都是模块
> * entry
> 入口。打包过程中的概念，webpack以一个或多个文件为入口，分析整个依赖关系
> * bundle
> webpack 打包结果

### 6.常用webpack Loader都有那些
> * cache-loader：启用编译缓存
> * thread-loader：启用多线程编译
> * css-loader：编译 css 代码为 js
> * file-loader：保存文件到输出目录，将文件内容转换成文件路径
> * postcss-loader：将 css 代码使用 postcss 进行编译
> * url-loader：将文件内容转换成 dataurl
> * less-loader：将 less 代码转换成 css 代码
> * sass-loader：将 sass 代码转换成 css 代码
> * ue-loader：编译单文件组件
> * babel-loader：对 JS 代码进行降级处理

### 7.说一下 webpack 常用插件都有哪些？
> * clean-webpack-plugin：清除输出目录
> * copy-webpack-plugin：复制文件到输出目录
> * html-webpack-plugin：生成 HTML 文件
> * mini-css-extract-plugin：将 css 打包成单独文件的插件
> * HotModuleReplacementPlugin：热更新的插件
> * purifycss-webpack：去除无用的 css 代码
> * optimize-css-assets-webpack-plugin：优化 css 打包体积
> * uglify-js-plugin：对 JS 代码进行压缩、混淆
> * compression-webpack-plugin：gzip 压缩
> * webpack-bundle-analyzer：分析打包结果