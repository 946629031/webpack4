# hello-webpack 4

webpack4 各种语法 入门讲解

- [【慕课网 Webpack4.0】从基础到实战 手把手带你掌握新版Webpack4.0完整](https://coding.imooc.com/class/316.html)

看视频整理要点笔记：

----

## 第2章 webpack究竟是什么？

- 2-1 为什么会出现webpack?
    - 发展到现在，前端项目越来越复杂，业务逻辑越来越多，已经不是仅仅几个 html js css 文件能处理得了的。即使处理的了，可维护性也很差。
- 2-2 webpack 是什么？
    - 模块打包工具
    - 打包命令： npx webpack index.js
- 2-3 Webpack 的正确安装方式
    - 环境依赖 node.js , npm
    - 项目初始化：npm -init -y
        - 要想用webpack管理项目，要先让项目符合node的规范
        - 生成 package.json
    - 安装命令
        - npm i webpack webpack-cli -D                  (简写)
        - npm install webpack webpack-cli --save-dev    (全写)
        - 全局安装 存在的问题
            - npm install webpack webpack-cli -g
            - 非常不推荐全局安装 webpack
            - 原因：假如我有两个项目，一个是webpack4打包的，另一个是webpack3打包的。由于我是全局安装webpack4的，那么webpack3打包的项目是运行不起来的
            - npm uninstall webpack webpack-cli -g      (卸载全局安装)
    - 安装后检查是否安装成功
        - npx webpack -v    (局部安装命令)
        - webpack -v        (全局安装命令)
    - Webpack 文件上传github仓库
        - 一般情况下，都会把项目中的 node_modules文件夹 删除
         - npm install -- 该命令可以自动把项目依赖包下载好