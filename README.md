# hello-webpack 4

webpack4 各种语法 入门讲解

- [【慕课网 Webpack4.0】从基础到实战 手把手带你掌握新版Webpack4.0完整](https://coding.imooc.com/class/316.html)

看视频整理要点笔记：

----

**目录**
- [第1章 为什么会出现webpack?](#第1章-为什么会出现webpack?)
- [第2章 webpack究竟是什么？](#第2章-webpack究竟是什么？)
    - [2-3 Webpack 的正确安装方式](#2-3-Webpack-的正确安装方式)
- [第3章 Webpack 的核心概念](#第3章-Webpack-的核心概念)
    - [3-1 什么是loader](#3-1-什么是loader)
    - [3-2 使用 Loader 打包静态资源（图片篇）](#3-2-使用-Loader-打包静态资源（图片篇）)
    - [3-3 使用 loader 打包静态资源（样式篇 - 上）【css、scss、私有前缀】](#3-3-使用-loader-打包静态资源（样式篇---上）)
    - [3-4 使用 loader 打包静态资源（样式篇 - 下）【样式中是sass、样式的局部作用域、字体文件】](#3-4-使用-loader-打包静态资源(样式篇---下))
    - [3-5 使用 ```plugins``` 让打包更便捷【```HtmlWebpackPlugin```、```CleanWebpackPlugin```】](#3-5-使用-plugins-让打包更便捷)
    - [3-6 Entry 与 Output 的基础配置](#3-6-Entry-与-Output-的基础配置)
    - [3-7 SourceMap 的配置](#3-7-SourceMap-的配置)
    - [3-8 使用 ```WebpackDevServer``` 提升开发效率](#3-8-使用-WebpackDevServer-提升开发效率)
    - [3-9 ```Hot Module Replacement``` 模块热更新](#3-9-Hot-Module-Replacement-模块热更新)
    - [3-11 使用 ```Babel``` 处理 ES6 语法](#3-11-使用-Babel-处理-ES6-语法)
    - [3-13 Webpack 实现对React框架代码的打包](#3-13-Webpack-实现对React框架代码的打包)
    - [本章总结 最佳配置](#第3章-Webpack-的核心概念总结-最佳配置)
- [第4章](#)
----

## 第1章 为什么会出现webpack?

- 发展到现在，前端项目越来越复杂，业务逻辑越来越多，已经不是仅仅几个 html js css 文件能处理得了的。即使处理的了，可维护性也很差。

## 第2章 webpack究竟是什么？

- 2-2 webpack 是什么？
    - 模块打包工具
    - 打包命令： 
        - ```npx webpack index.js```  (局部安装)
        - ```webpack input.js output.js``` (全局安装)
    
- 2-3 Webpack 的正确安装方式
    - 环境依赖 node.js , npm
    - 项目初始化：```npm init -y```
        - 要想用webpack管理项目，要先让项目符合node的规范
        - 生成 package.json
    - 安装命令
        - ```npm i webpack webpack-cli -D```                  (简写)
        - ```npm install webpack webpack-cli --save-dev```    (全写)
        - 全局安装 存在的问题
            - ```npm install webpack webpack-cli -g```
            - ```npm uninstall webpack webpack-cli -g```      (卸载全局安装)
            - 非常不推荐全局安装 webpack
            - 原因：假如我有两个项目，一个是webpack4打包的，另一个是webpack3打包的。由于我是全局安装webpack4的，那么webpack3打包的项目是运行不起来的
        - webpack-cli 的作用
            - webpack-cli 使得 webpack index.js 或者 npx webpack 这样的命令能够在命令行中运行
    - 安装后检查是否安装成功
        - ```npx webpack -v```    检查局部安装
        - ```webpack -v```        检查全局安装
    - Webpack 文件上传github仓库
        - 一般情况下，都会把项目中的 node_modules文件夹 删除
         - ```npm install``` 可以自动把项目依赖包下载好
- 2-4 使用webpack的配置文件
    - ```npx webpack index.js```
        - 这条命令，实际上是使用webpack的默认配置，且入口文件是index.js
    - webpack.config.js 配置文件
        - 当配置文件 配置好后，打包命令为：npx webpack
        ```js
            const path = require('path')

            module.exports = {
                mode: 'production', // development
                entry: {
                    main: './src/index.js'
                },
                // entry: './src/index.js', 简写
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
        ```
    - 目录结构
        ```
        webpack-demo
        +  |- node_modules
        +  |- /dist               // 打包出口
        +  |- /src                // 源代码(入口)
               |- index.js
           |- webpack.config.js   // 配置文件
           |- package.json
           |- index.html
        ```
    - npm run bundle 打包命令
        - 问题：如何自定义打包命令的名称呢？例如把打包命令改成```npm run bundle```，而不是```npx webpack```
        - 在 package.json 文件中
            ```js
            ...
            "scripts": {
                "bundle": "webpack"
            }
            ...
            ```
            npm script 中的'webpack'会优先到本地(局部)的node_modules中查找webpack模块，如果没有才会去全局中查找
    - 手动指定配置文件
        - ```npx webpack --config config_file_name.js```

## 第3章 Webpack 的核心概念
- 3-1 什么是loader
    - 什么是 loader ?
        - 由于webpack原来只能打包js文件，但是如果要打包css或jpg/png的文件时，webpack就会不支持了。这时候就要借助loader来解决这个问题。
    - loader三步曲
        - 1. js引入一个非js文件，如：.jpg .png .txt .vue excel表格文件等
        - 2. 配置好对应的 ```webpack.config.js``` loader
        - 3. 在webpack中 安装好对应的loader ```npm i file-loader -D```
    - ```webpack.config.js``` 配置loader
        ```js
        const path = require('path')

        module.exports = {
            mode: 'development',
            entry: {
                main: './src/index.js'
            },
            module:{
                rules: [{
                    test: /\.jpg$/,
                    use: {
                        loader: 'file-loader'
                    }
                }]
            }
            output:{
                filename: 'bundle.js',
                path: path.resolve(__dirname, 'dist')
            }
        }
        ```
- 3-2 使用 Loader 打包静态资源（图片篇）
    - file-loader
        ```js
        const path = require('path')

        module.exports = {
            mode: 'development',
            entry: {
                main: './src/index.js'
            },
            module: {
                rules: [{
                    test: /\.(jpg|png|gif)$/,
                    use: {
                        loader: 'file-loader',
                        options: {
                            name: '[name].[hash].[ext]',
                            // [name] 这种语法为 placeholder 占位符
                            outputPath: 'img/'
                        }
                    }
                }]
            },
            output: {
                filename: 'bundle.js',
                path: path.resolve(__dirname, 'dist')
            }
        }
        ```
    - url-loader
        - url-loader能做一切 file-loader所能做的一切
            - url-loader 和 file-loader 非常像，只不过url-loader 多了一个limit的配置项
        - 通过 url-loader 打包完后，你会发现，图片文件并没有被打包到dist目录下
            - 其实，url-loader 默认会把图片文件转成 base64 字符串，写入了 bundle.js 文件中
                - 但是这样打包又引入了一些问题。
                    - 优点：图片打包到js里面，只要js加载完成，页面就显示出来了，不用再去额外的请求图片的地址了，省了一次http请求
                    - 缺点：如果图片本身特别大，打包生成的js文件也就会特别大，那么你加载这个js文件的时间就会很长。所以在一开始，很长的时间里面，页面上什么东西都显示不出来
                    - 最佳的使用方式：如果一个图片很小，只有几kb，那么把这些图片打包到js里，是非常好的选择，没必要让几kb的图片 再去发一次http请求，很浪费时间。反之，如果图片很大的话，就把图片打包到dist目录下。
                        - 实现：```limit: 2048``` 限制2048个字节(2kb), 大于2048则打包成图片 放入dist目录，否则打包成base64
        ```js
        const path = require('path')

        module.exports = {
            mode: 'development',
            entry:{
                main: './src/index.js'
            },
            module: {
                relus:[{
                    test: /\.(jpg|png|gif)$/,
                    use: {
                        loader: 'url-loader',
                        option: {
                            name: '[name].[hash].[ext]',
                            outputPath: 'img/',
                            limit: 2048     // 限制2048个字节
                        }
                    }
                }]
            },
            output: {
                filename: 'bundle.js',
                path: path.resolve(__dirname, 'dist')
            }
        }
        ```
- 3-3 使用 loader 打包静态资源（样式篇 - 上）
    - 1.处理css文件
        - ```npm i style-loader css-loader -D``` 安装css-loader
        - webpack.config.js
            ```js
            const path = require('path')

            module.exports = {
                mode: 'development',
                entry: {
                    main: './src/index.js'
                },
                module: {
                    rules: [{
                        test: /\.(jpg|png|gif)$/,
                        use: {
                            loader: 'file-loader',
                            options: {
                                name: '[name].[hash].[ext]',
                                outputPath: 'img/'
                            }
                        }
                    },{
                        test: /\.css$/,
                        use: ['style-loader','css-loader']
                    }]
                },
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```
    - 2.处理scss文件
        - ```npm i sass-loader node-sass -D``` 安装sass-loader
        - webpack.config.js
            ```js
            const path = require('path')

            module.exports = {
                mode: 'development',
                entry: {
                    main: './src/index.js'
                },
                module: {
                    rules: [{
                        test: /\.(jpg|png|gif)$/,
                        use: {
                            loader: 'file-loader',
                            options: {
                                name: '[name].[hash].[ext]',
                                outputPath: 'img/'
                            }
                        }
                    },{
                        test: /\.scss$/,
                        use: ['style-loader','css-loader','sass-loader']
                    }]
                },
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```
    - 3.postcss-loader自动添加 浏览器私有前缀
        - ```npm i -D postcss-loader``` 安装 postcss-loader
        - ```npm i -D autoprefixer``` 安装自动添加 浏览器私有前缀 插件
        - 新建 ```postcss.config.js``` 配置文件，放在 ```webpack.config.js``` 同级目录下
            ```js
            // postcss.config.js
            module.exports = {
                plugins: [
                    require('autoprefixer')
                ]
            }
            ```
            
            ```js
            // webpack.config.js
            const path = require('path')

            module.exports = {
                mode: 'development',
                entry: {
                    main: './src/index.js'
                },
                module: {
                    rules: [{
                        test: /\.(jpg|png|gif)$/,
                        use: {
                            loader: 'file-loader',
                            options: {
                                name: '[name].[hash].[ext]',
                                outputPath: 'img/'
                            }
                        }
                    },{
                        test: /\.scss$/,
                        use: [
                            'style-loader',
                            'css-loader',
                            'sass-loader',
                            'postcss-loader'
                        ]
                    }]
                },
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```

- 3-4 使用 loader 打包静态资源(样式篇 - 下)
    - ### 1.样式loader 配置项
        - 现在有一个问题：先看下面项目文件
            ```js
            // index.js
            import './index.scss'

            // ...other js code
            ```
            ```scss
            // index.scss
            @import './avatar.scss';

            body{
                .avatar{ width: 150px }
            }
            ```
            ```scss
            // avatar.scss
            .avatar{ color: red }
            ```
            ```js
            // webpack.config.js
            const path = require('path')

            module.exports = {
                mode: 'development',
                entry: './src/index.js',
                module: {
                    rules: [{
                        test: /\.scss$/,
                        use: [
                            'style-loader',
                            'css-loader',
                            'sass-loader',
                            'postcss-loader'
                        ]
                    }]
                },
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```
        - 问题：
            - 1.在入口文件index.js中，引入的.scss文件，webpack在处理这类文件的时候都会依次```由后向前```去走 'postcss-loader',  'sass-loader', 'css-loader', 'style-loader'
            - 2.但是问题来了，在sass文件中，@import其他 .scss 文件。当webpack处理文件由 'postcss-loader',  'sass-loader' 走到'css-loader', 的时候，遇到了 ```@import './avatar.scss';``` 它就不知道该如何解析这个@import进来的scss文件了，然后他就会直接把它按照css文件处理了，而不会解析里面scss语法
            - 所以，解决方案：给 ```css-loader``` 添加配置项 ```importLoaders``` 
            ```js
            // webpack.config.js
            const path = require('path')

            module.exports = {
                mode: 'development',
                entry: './src/index.js',
                module: {
                    rules: [{
                        test: /\.scss$/,
                        use: [
                            'style-loader',
                            {
                                loader: 'css-loader',
                                options: {
                                    importLoaders: 2
                                    // 在css文件中遇到 @import，会往前执行2个loader
                                }
                            },
                            'sass-loader',
                            'postcss-loader'
                        ]
                    }]
                },
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```
    - ### 2.样式的模块化 （样式的局部作用域）
        - 过去存在的问题：
            - 在一个页面中引入 index.css
            ```css
                .avator{ width: 150px }
            ```
            这种情况下，对于整个页面来说 .avator 的 **样式的作用域是全局的** （对页面中所有的.avator class都有影响）。但是如果是多人开发，或组件式开发的项目，这样就很容易引起 **样式冲突** 的问题。

            那么，有没有什么办法能实现 **样式的局部作用域** 呢？
        - #### 样式的局部作用域
            - **思路：** 
                - ```modules: true```   开启css模块化
                - ```let style = require('./css/index.scss')```     引入的scss赋值给变量 style
                - ```img.classList.add(style.avator)```     添加class时用 style.avator
            - 这样就能实现， index.scss 只对 其中指定的class有效 (**即使class同名**)
        
            ```js
            // webpack.config.js
            const path = require('path')

            module.exports = {
                mode: 'development',
                entry: './src/index.js',
                module: {
                    rules: [{
                        test: /\.scss$/,
                        use: [
                            'style-loader',
                            {
                                loader: 'css-loader',
                                options: {
                                    importLoaders: 2,
                                    modules: true   // 开启css模块化
                                }
                            },
                            'sass-loader',
                            'postcss-loader'
                        ]
                    }]
                },
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```

            ```js
            // index.js
            let img_src = require('./img/webpack.png')
            let style = require('./css/index.scss')     // 引入的scss赋值给变量 style
            let createAvator = require('./js/avator.js')
            createAvator()

            let dom = document.getElementById('dom')

            let img = new Image()
            img.src = `./dist/${img_src}`
            img.classList.add(style.avator)     // 添加class时用 style.avator

            dom.append(img)
            ```

            ```js
            // ./js/avator.js
            let img_src = require('../img/webpack.png')

            function createAvator(){
                let dom = document.getElementById('dom')

                let img = new Image()
                img.src = `./dist/${img_src}`
                img.classList.add('avator')

                dom.append(img)
            }

            module.exports = createAvator
            ```
    - #### 3.打包字体文件
        - 思路：通过 ```file-loader``` 把字体文件打包到```dist```目录下
        - 工程文件如下：
        ```
        项目目录
        +  |- node_modules
        +  |- /src
        +     |- /css
                 |- index.scss
        +     |- /fonts
                 |- iconfont.eot
                 |- iconfont.svg
                 |- iconfont.ttf
                 |- iconfont.woff
              |- index.js
           |- index.html
           |- package.json
           |- postcss.config.js
           |- webpack.config.js           
        ```
        ```js
        // webpack.config.js
        const path = require('path')

        module.exports = {
            mode: 'development',
            entry: {
                main: './src/index.js'
            },
            module: {
                rules: [{
                    test: /\.scss$/,
                    use: [
                        'style-loader',
                        {
                            loader: 'css-loader',
                            options: {
                                importLoaders: 2
                            }
                        },
                        'sass-loader',
                        'postcss-loader'
                    ]
                },{
                    test: /\.(eot|svg|ttf|woff|woff2)$/,
                    use: {
                        loader: 'file-loader',
                        // 此处用 'file-loader' 仅仅是利用file-loader能吧对应的文件移动到dist目录下的特性而已
                        options: {
                            outputPath: 'fonts/'
                        }
                    }
                }]
            },
            output: {
                filename: 'bundle.js',
                path: path.resolve(__dirname, 'dist')
            }
        }
        ```
        ```scss
        // index.scss
        @font-face {font-family: "iconfont";
            src: url('../fonts/iconfont.eot?...')
            src: url('../fonts/iconfont.eot?...')
            url('../fonts/iconfont.woff?...')
            url('../fonts/iconfont.ttf?...')
            url('../fonts/iconfont.svg?...')
        }
        
        .iconfont {
            font-family: "iconfont" !important;
            font-size: 16px;
            font-style: normal;
            -webkit-font-smoothing: antialiased;
            -moz-osx-font-smoothing: grayscale;
        }
        
        .icon-edit-tools:before { content: "\e615"; }
        
        .icon-calendar:before { content: "\e613"; }
        ```
        ```js
        // index.js
        require('./css/index.scss')

        let dom = document.getElementById('dom')

        dom.innerHTML = `
            <div class="iconfont icon-edit-tools"></div>
            <div class="iconfont icon-calendar"></div>
        `
        ```
        ```js
        //postcss.config.js
        module.exports = {
            plugins: [
                require('autoprefixer')
            ]
        }
        ```

- ### 3-5 使用 plugins 让打包更便捷
    - ```plugins```的作用
        - ```plugins```可以做webpack运行到某个时刻的时候，帮你做一些事情
    - #### 1. ```HtmlWebpackPlugin```
        - 之前存在的问题：在项目中，```index.html```文件总是我们手动创建并修改的，如果每次打包都需要这样的手动操作 就会显得很麻烦。那么，我们能不能通过webpack自动生成```index.html```文件呢？可以的！
        - 1.```npm i -D html-webpack-plugin``` 安装插件
        - 2.使用
            ```js
            // webpack.config.js
            const HtmlWebpackPlugin = require('html-webpack-plugin')
            const path = require('path')

            module.exports = {
                entry: 'index.js',
                output: {
                    path: path.resolve(__dirname, 'dist'),
                    filename: 'bundle.js'
                },
                plugins: [new HtmlWebpackPlugin()]
            }
            ```
            打包后，它会在```dist```目录下自动生成 ```index.html```
            ```html
            // dist/index.html
            <!DOCTYPE html>
            <html>
            <head>
                <meta charset="UTF-8">
                <title>webpack App</title>
            </head>
            <body>
                <script src="bundle.js"></script>
            </body>
            </html>
            ```
        - 3.**```HtmlWebpackPlugin``` 会在打包结束后，自动生成一个html文件，并把打包生成的js自动引入到这个html文件中**
        - ##### 4. ```HtmlWebpackPlugin``` 使用模板
            ```js
            // webpack.config.js
            const HtmlWebpackPlugin = require('html-webpack-plugin')
            const path = require('path')

            module.exports = {
                entry: 'index.js',
                output: {
                    path: path.resolve(__dirname, 'dist'),
                    filename: 'bundle.js'
                },
                plugins: [new HtmlWebpackPlugin({
                    template: 'src/index.html'
                })]
            }
            ```
            ```html
            // src/index.html
            <!DOCTYPE html>
            <html>
            <head>
                <meta charset="UTF-8">
                <title>html 模板</title>
            </head>
            <body>
                <div id="root"></div>
            </body>
            </html>
            ```
            - 打包后会生成如下html
            ```html
            // dist/index.html
            <!DOCTYPE html>
            <html>
            <head>
                <meta charset="UTF-8">
                <title>html 模板</title>
            </head>
            <body>
                <div id="root"></div>
                <script src="bundle.js"></script>
            </body>
            </html>
            ```
    - #### 2.```CleanWebpackPlugin```
        - 先看存在的问题：
            - 在开发过程中，假如我需要把打包输出的 ```bundle.js``` 改名为 ```dist.js```，但是我又不想要去手动删除打包输出的 ```dist``` 目录，那么有没有什么办法能帮我自动完成这个操作的呢？特别是在要删除的东西不是一个两个的时候（批量手动操作实在太麻烦了）。解决方法如下
        - 1.安装 ```npm i -D clean-webpack-plugin```
        - 2.使用
            ```js
            // webpack.config.js
            const HtmlWebpackPlugin = require('html-webpack-plugin')
            const CleanWebpackPlugin = require('clean-webpack-plugin')
            const path = require('path')

            module.exports = {
                entry: 'index.js',
                output: {
                    path: path.resolve(__dirname, 'dist'),
                    filename: 'bundle.js'
                },
                plugins: [
                    new HtmlWebpackPlugin({
                    template: 'src/index.html'
                    }),
                    new CleanWebpackPlugin(['dist']) // 在打包前，它会自动删除 dist 目录下的所有文件
                ]
            }
            ```
- ### 3-6 Entry 与 Output 的基础配置
    - 1.输出命名
        ```js
        // webpack.config.js
        const path = require('path')

        module.exports = {
            mode: 'development',
            entry: {
                main: './src/index.js'  // 这里对象的键名，表示打包输出文件名为main.js
            },
            output: {
                filename: 'bundle.js',  // 如果没定义输出文件名，默认为main.js
                path： path.resolve(__dirname, 'dist')
            }
        }
        ```
    - 2.打包多个文件
        ```js
        // webpack.config.js
        const path = require('path')

        module.exports = {
            mode: 'development',
            entry: {
                main: './src/index.js'
                sub: './src/sub.js'
            },
            output: {
                filename: '[name].js',  // 这里支持 placeholder占位符 各种写法
                path： path.resolve(__dirname, 'dist')
            }
        }
        ```
        输出结果：在 dist 目录下会生成 ```main.js``` 和 ```sbu.js```
        #### 原理：output 中 ```filename: '[name].js'``` [name] 会自动根据 entry入口文件 的键名去生成对应的 ```main.js``` 和 ```sbu.js```
    - 3.```publicPath```
        - 问题：如果我们把 index.html 文件给到后端作为入口文件，而其他的静态资源我们上传到 cdn 上， 如```<script src="http://cdn.com.cn/main.js"></script>```，我们希望webpack能自动帮我们在 index.html 中插入的 js文件 自动插入cdn地址，我们该怎么办呢？
        - 解决方法：
            ```js
            // webpack.config.js
            const path = require('path')

            module.exports = {
                mode: 'development',
                entry: {
                    main: './src/index.js'
                    sub: './src/sub.js'
                },
                output: {
                    publicPath: 'http://cdn.com.cn',    // 在这里配置
                    filename: '[name].js',
                    path： path.resolve(__dirname, 'dist')
                }
            }
            ```
            打包结果如下：
            ```html
            // dist/index.html
            <!DOCTYPE html>
            <html>
            <head>
                <meta charset="UTF-8">
            </head>
            <body>
                <script src="http://cdn.com.cn/main.js"></script>
                <script src="http://cdn.com.cn/sub.js"></script>
            </body>
            </html>
            ```
- ### 3-7 SourceMap 的配置
    - #### 最佳配置：
        - 开发环境
            - 优点：提示错误比较全，打包速度比较快
            ```js
            // webpack.config.js
            module.exports = {
                mode: 'development',
                devtool: 'cheap-module-eval-source-map'
                ...
            }
            ```
        - 生产环境
            - 生成环境中，可以不用 ```devtool```，或者配置如下
            ```js
            // webpack.config.js
            module.exports = {
                mode: 'production',
                devtool: 'cheap-module-source-map'
                ...
            }
            ```
    - 什么是 ```SourceMap``` ?
        - 问题再现：
            - 当你在开发过程中，```src/index.js``` 文件中的第1行 ```consele.log(hello world)``` 写错了，你却不知道的时候 执行了打包。
            ```js
            // webpack.config.js
            module.exports = {
                mode: 'development',
                devtool: 'none'         // 关闭 SourceMap
                ...
            }
            ```
            如果在 ```SourceMap``` 没开启的情况下打包，且入口文件 ```src/index.js``` 内有错误，那么它在报错的时候，就会说
             - 在 ```dist/bundle.js``` 文件中第96行发现错误。
             - 但是问题来了，我并不希望你告诉我 ```dist/bundle.js``` 哪里错了，而是希望你能告诉我源码中哪里错了，如 ```src/index.js``` 文件中的第1行 ```consele.log(hello world)``` 写错了。那我们该怎么办呢？
        - #### SourceMap 定义
            - ```SourceMap``` 它是一个映射关系，当它知道在 ```dist/bundle.js``` 文件中第96行发现错误，实际上就对应着 如 ```src/index.js``` 入口文件中的第1行 ```consele.log(hello world)``` 发现错误。
            - 一言以蔽之，```SourceMap``` 就是把输出后的js，与源码做映射。
        - #### SourceMap 用法
            - 1.开启 SourceMap
                ```js
                // webpack.config.js
                module.exports = {
                    mode: 'development',
                    devtool: 'source-map'   // 开启 SourceMap
                    ...
                }
                ```
            - 2.查找源码错误
                - 开启 ```SourceMap``` 后，重新打包，如果浏览器 ```console面板``` 提示错误，点击错误提示就会 **直接跳转到错误的源文件处了**。
        - 文档解析：
            - [文档地址](https://webpack.js.org/configuration/devtool#devtool)
            - ```inline-source-map```
                - 在 webpack.config.js 中 ```devtool: 'xxx'``` 的所有配置参数中，凡是没带 ```inline-``` 开头的，都会在 ```dist``` 目录下生成 ```bundle.js.map``` 映射文件
                - 而凡是带了 ```inline-``` 开头的，```.map``` 映射文件的内容都会被 以```base64```方式 写入输出的 ```bundle.js``` 文件中
            - ```cheap-source-map```
                - 这是 "cheap(低开销)" 的 SourceMap ,因为它没有生成列映射，只是映射行数。
                - 默认情况下，不带 ```cheap-``` 的 SourceMap 默认既映射行数，也映射列数，但是打包过程比较久，比较耗费性能。
            - ```cheap-module-source-map```
                - 存在的问题：
                    - 而且 ```cheap-``` 只映射 **业务代码** (即entry的入口文件) 的映射关系，不会去管其它的模块错误。
                - 当 ```devtool: 'cheap-module-source-map'``` 时，这时候的映射关系不仅映射 **业务代码** ，还映射业务代码引入的其他 文件或模块 ，或第三方 module 的错误。
            - ```eval```
                - 当 ```devtool: 'eval'``` 时
                    - ```eval``` 是打包速度最快的一种方式，也能映射报错位置。
                    - ```eval``` 既不生成 ```bundle.js.map``` 映射文件，也不会像 ```inline-source-map``` 中以 ```base64``` 方式吧 ```映射关系``` 写入在 ```bundle.js``` 内。
                    - 但是，```eval``` 以 ```eval("...")... sourceURL=...``` 方式把 ```映射关系``` 写入 ```bundle.js``` 中
                    - **缺点**：对于比较复杂的项目，```eval``` 这种打包方式的 **提示可能并不全面**
- ### 3-8 使用 ```WebpackDevServer``` 提升开发效率
    - ```wabpack --watch```
        - 1.配置 ```package.json```
            ```js
            // package.json
            {
                ...
                "scripts": {
                    "watch" : "webpack --watch",
                    // "bundle": "webpack"
                }
                ...
            }
            ```
        - 2.```npm run watch``` 执行该脚本
        - 3.这时候，当你改动项目中 ```src``` 下的任何文件，webpack 监听到变动，就会自动重新打包。
    - 存在问题：
        - ```wabpack --watch``` 这种方式还不足够方便
        - 如果我希望能，在我第一次执行 ```npm run watch``` 的时候，
            - 1.自动帮我打包，
            - 2.自动帮我把浏览器打开， 
            - 3.同时还可以帮我模拟一些服务器上的特性
    - #### ```WebpackDevServer```
        - 优点：
            - 监听入口文件，自动打包
            - 开启本地服务器
            - 自动刷新页面，不需要手动刷新
            - 由于是本地服务器(http://协议)，所以可以发送 ajax 请求。本地文件打开则不行(file://协议)
        - 1.安装 ```npm i -D webpack-dev-server```
        - 2.配置
            ```js
            // webpack.config.js
            const path = require('path')

            module.exports = {
                mode: 'development',
                devtool: 'cheap-module-eval-source-map',
                entry: './src/index.js',
                devServer: {
                    contentBase: './dist',   // 借助devServer起一个服务器，根路径为'./dist'
                    open: true      // 自动打开浏览器，并访问 http://localhost:8080
                },
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```
            ```js
            // package.json
            {
                ...
                "scripts": {
                    "watch" : "webpack --watch",
                    "start" : "webpack-dev-server",
                    // "bundle": "webpack"
                }
                ...
            }
            ```
        - 3.执行脚本 ```npm run start```
            - 这时候 ```webpack-dev-server``` 就开启了本地服务器 ```http://localhost:8080```
            - 而且监听入口文件，自动打包
    - 手动写一个 ```webpackDevServer``` （了解即可）
        - 配置
            ```js
            // server.js
            const express = require('express')
            const webpack = require('webpack')
            const webpackDevMiddleware = require('webpack-dev-middleware')
            const config = require('./webpack.config.js')
            const complier = webpack(config)

            const app = express()

            app.use(webpackDevMiddleware(complier, {
                publicPath: config.output.publicPath
            }))

            app.listen(3000, () => {
                console.log('server is running')
            })
            ```
            ```js
            // webpack.config.js
            const path = require('path')

            module.exports = {
                mode: 'development',
                devtool: 'cheap-module-eval-source-map',
                entry: './src/index.js',
                devServer: {
                    contentBase: './dist',   // 借助devServer起一个服务器，根路径为'./dist'
                    open: true      // 自动打开浏览器，并访问 http://localhost:8080
                },
                output: {
                    publicPath: '/',
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```
            ```js
            // package.json
            {
                ...
                "scripts": {
                    // "bundle": "webpack"
                    "watch" : "webpack --watch",
                    "start" : "webpack-dev-server",
                    "server": "node server.js"
                }
                ...
            }
            ```
            ```
            // 项目目录
            +  |- /node_modules
            +  |- /src
               |- package.json
               |- server.js
               |- webpack.config.js
            ```
        - 开启服务
            - ```npm run server```
            - 而且监听入口文件，自动打包
            - 开启服务后，访问 ```http://localhost:3000``` 预览项目

- ### 3-9 ```Hot Module Replacement``` 模块热更新
    - 1.css热更新
        - 以前存在的问题：
            - 之前执行 ```webpack-dev-server``` 的时候，自动监听 src目录，当监听到文件修改的时候，就会自动打包并刷新页面。
            - 但是有时候我们会觉得挺麻烦的，例如：给button绑定事件，每点击一次，新增一个div，当我们修改该div的css，浏览器就会自动刷新了，导致每修改一次css，都需要重新点击button，听麻烦的...
            - 那么如何才能只修改css，不重载 document 呢？用下面的 ```Hot Module Replacement``` 模块热更新
        - 配置
            1. ```devServer``` 开启 hot 和 hotOnly
            2. ```const webpack = require('webpack')```
            3. ```plugins:[ new webpack.HotModuleReplacementPlugin() ]```
            ```js
            // webpack.config.js
            const path = require('path')
            const webpack = require('webpack')

            module.exports = {
                mode: 'development',
                devtool: 'cheap-module-eval-source-map',
                entry: './src/index.js',
                devServer: {
                    contentBase: './dist',
                    open: true,
                    hot: true,     // 开启 Hot Module Replacement
                    hotOnly: true  // 构建失败时不刷新页面
                },
                plugins:[
                    new webpack.HotModuleReplacementPlugin()
                ]
                output: {
                    publicPath: '/',
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```
    - 2.js模块的热更新
        - 核心关键：
            ```js
            if(module.hot){
                // 如果文件发生变化，就执行后面的函数
                module.hot.accept('./number', ()=>{
                    // ...执行内容
                })
            }
            ```
        - 配置案例：
            - 需求：修改 ```number.js模块``` 中的1000，不影响 ```// counter.js模块``` 中已经执行的结果
            ```js
            // number.js 模块
            function number(){
                let div = document.createElement('div');
                div.setAttribute('id','number')
                div.innerHTML = 1000
                document.body.appendChild(div)
            }
            module.exports = number
            ```
            
            ```js
            // counter.js 模块
            function counter(){
                let div = document.createElement('div');
                div.setAttribute('id','counter')
                div.innerHTML = 1
                div.onclick = function(){
                    div.innerHTML = parseInt(div.innerHTML, 10) + 1 // 每次点击+1
                }
                document.body.appendChild(div)
            }
            module.exports = counter
            ```
            ```js
            // index.js
            import counter from './counter';
            import number from './number';

            counter();
            number();


            if(module.hot){
                // 如果文件发生变化，就执行后面的函数
                module.hot.accept('./number', ()=>{
                    document.body.removeChild(document.getElementById('number'))
                    number();
                })
            }
            ```
            
            ```js
            // webpack.config.js
            const path = require('path')
            const webpack = require('webpack')

            module.exports = {
                mode: 'development',
                devtool: 'cheap-module-eval-source-map',
                entry: './src/index.js',
                devServer: {
                    contentBase: './dist',
                    open: true,
                    hot: true,     // 开启 Hot Module Replacement
                    hotOnly: true  // 构建失败时不刷新页面
                },
                plugins:[
                    new webpack.HotModuleReplacementPlugin()
                ]
                output: {
                    publicPath: '/',
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```

- ### 3-11 使用 ```Babel``` 处理 ES6 语法
    - [Babel 官网](https://babeljs.io/setup#installation)
    - 1.安装 ```npm install --save-dev babel-loader @babel/core```
    - 2.配置
        ```js
        // webpack.config.js
        module:{
            rules:[
                test: /\.js$/, 
                exclude: /node_modules/,  // 排除 node_modules 目录的文件
                loader: "babel-loader",
                options: {
                    presets: ["@babel/preset-env"]
                }
            ]
        }
        ```
    - 3.```npm i @babel/preset-env -D```
        - 为什么还要安装 @babel/preset-env 呢？实际上 babel-loader 只是 webpack和babel 的通信桥梁，而 ```@babel/preset-env``` 才能 ES6 转成 ES5
    - 4.在 ```webpack.config.js```中 module.rules 增加 ```options: { presets: ["@babel/preset-env"] }```
    - 5.```polyfill```
        - 在走完前面4步之后，还是有一些 对象 或 函数，在低版本的浏览器是没有的，所以这时候 **就要把这些缺失的函数补充进来**，这时候就要用到 ```polyfill``` 了
        - 安装 ```npm i @babel/polyfill -D```
        - 使用：
            - 在业务代码 ```index.js``` 的最顶部引入 ```polyfill```
                ```js
                // index.js
                import "@babel/polyfill"

                const arr = [
                    new Promise(() => {}),
                    new Promise(() => {})
                ]

                arr.map(item => {
                    console.log(item)
                })
                ```
        - 存在的问题：
            - ```polyfill``` 配置完上面的步骤后，如果直接打包，会发现他把低版本浏览器缺失的函数和对象全都加载进来了，导致输出文件非常大。
            - 问题的解决如下：给 ```presets``` 添加选项 ```useBuiltIns: 'usage'```
    - #### 6.业务代码 最佳配置
        ```js
        // webpack.config.js

        const path = require('path')

        module.exports = {
            mode: 'development',
            entry: {
                main: './src/index.js'
            },
            module: {
                rules: [{
                test: /\.js$/, 
                exclude: /node_modules/,  // 排除 node_modules 目录的文件
                loader: "babel-loader",
                options: {
                    presets: [[
                        "@babel/preset-env", {
                        targets: {
                            edge: "17",
                            firefox: "60",
                            chrome: "67",
                            safari: "11.1",
                        },
                        useBuiltIns: 'usage'    // 添加选项，只添加 index.js 用到对象或函数
                    }]]
                }}]
            },
            output: {
                filename: 'bundle.js',
                path: path.resolve(__dirname, 'dist')
            }
        }
        ```
    - #### 7.其他场景：开发类库、第三方模块、组件库
        - 1.存在的问题
            - 通过 ```import "@babel/polyfill"``` 这种方案实际是有问题的，因为 它在补充注入缺失的对象或者函数的时候，**是通过全局变量的形式，会污染到全局环境**。 解决方法如下
        - 2.安装
            - ```npm i -D @babel/plugin-transform-runtime```
            - ```npm i -D @babel/runtime```
            - ```npm install --save @babel/runtime-corejs2```
        - 配置
            ```js
            // webpack.config.js
            const path = require('path')

            module.exports = {
                mode: 'development',
                entry: {
                    main: './src/index.js'
                },
                module: {
                    rules: [{
                    test: /\.js$/, 
                    exclude: /node_modules/,
                    loader: "babel-loader",
                    options: {
                        "plugins": [["@babel/plugin-transform-runtime", {
                            "corejs": 2,    // 默认为false, 值为2时，要多安装一个 @babel/runtime-corejs2 包
                            "helpers": true,
                            "regenerator": true,
                            "useESModules": false
                        }]]
                    }}]
                },
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```
            - 业务代码 ```index.js``` 中不需要引入 ```import "@babel/polyfill"```
                ```js
                // index.js
                const arr = [
                    new Promise(() => {}),
                    new Promise(() => {})
                ]

                arr.map(item => {
                    console.log(item)
                })
                ```
    - ```.babelrc``` babel配置文件
        - 1.存在的问题：由于 babel 的配置参数非常多，如果全都写在 ```webpack.config.js``` 则会非常长。所以可以把 ```babel-loader``` 中的 ```options``` 对象单独拿出来，写在 ```.babelrc``` 文件中，并放在 ```webpack.config.js``` 同级目录下。
        - 2.配置
            ```js
            // webpack.config.js
            const path = require('path')

            module.exports = {
                mode: 'development',
                entry: {
                    main: './src/index.js'
                },
                module: {
                    rules: [{
                    test: /\.js$/, 
                    exclude: /node_modules/,
                    loader: "babel-loader"
                    }]
                },
                output: {
                    filename: 'bundle.js',
                    path: path.resolve(__dirname, 'dist')
                }
            }
            ```
            ```js
            // .babelrc
            {
                "plugins": [["@babel/plugin-transform-runtime", {
                    "corejs": 2,
                    "helpers": true,
                    "regenerator": true,
                    "useESModules": false
                }]]
            }
            ```
            注意：```.babelrc``` 文件中不能写注释
- 3-13 Webpack 实现对React框架代码的打包
    - ... 暂不记录，请查阅视频讲解
- ### 第3章 Webpack 的核心概念总结 最佳配置
    ```js
    // webpack.config.js
    const path = require('path')
    const HTMLWebpackPlugin = require('html-webpack-plugin')
    const CleanWebpackPlugin =require('clean-webpack-plugin')
    const webpack = require('webpack')

    module.exports = {
        mode: 'development',
        devtool: 'cheap-module-eval-source-map',
        entry:{
            main: './src/index.js'
        },
        devServer: {
            contentBase: './dist',
            open: true,
            port: 8080,
            hot: true,
            hotOnly: true
        },
        module: {
            rules: [{
                test: /\.js$/,
                exclude: /node_modules/,
                loader: 'babel-loader',
                options: {
                    presets: [[
                        "@babel/preset-env", {
                        targets: {
                            edge: "17",
                            firefox: "60",
                            chrome: "67",
                            safari: "11.1",
                        },
                        useBuiltIns: 'usage'    // 只添加用到对象或函数
                    }]]
                }
            },{
                test: /\.(jpg|png|gif)$/,
                use: {
                    loader: 'url-loader',
                    options: {
                        name: '[name].[hash].[ext]',
                        outputPath: 'images/',
                        limit: 10240
                    }
                }
            },{
                test: /\.(eot|ttf|svg)$/,
                use: {
                    loader: 'file-loader'
                }
            },{
                test: /\.scss$/,
                use: [
                    'style-loader',
                    {
                        loader: 'css-loader',
                        options: {
                            importLoaders: 2
                        }
                    },
                    'sass-loader',
                    'postcss-loader'
                ]
            },{
                test: /\.css$/,
                use: [
                    'style-loader',
                    'css-loader',
                    'postcss-loader'
                ]
            }]
        },
        plugins: [
            new HtmlWebpackPlugin({
                template: 'src/index.html'
            }),
            new CleanWebpackPlugin(['dist']),
            new webpack.HotModuleReplacementPlugin()
        ],
        output: {
            filename: '[name].js',  // [name] 值根据 entry 的 key 值决定的
            path: path.resolve(__dirname, 'dist')
        }
    }
    ```