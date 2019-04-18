# hello-webpack 4

webpack4 各种语法 入门讲解

- [【慕课网 Webpack4.0】从基础到实战 手把手带你掌握新版Webpack4.0完整](https://coding.imooc.com/class/316.html)

看视频整理要点笔记：

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

- 3-4 使用 loader 打包静态资源（样式篇 - 下）
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
    - ### 3.打包字体文件
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

- 3-5 使用 ```plugins``` 让打包更便捷
    - ```plugins```的作用
        - ```plugins```可以做webpack运行到某个时刻的时候，帮你做一些事情
    - ### 1. ```HtmlWebpackPlugin```
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
        - #### 4. ```HtmlWebpackPlugin``` 使用模板
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
    - ### 2.```CleanWebpackPlugin```
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