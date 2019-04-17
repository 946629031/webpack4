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
    - 样式loader 配置项
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
            - 2.但是问题来了，在sass文件中，@import其他 .scss 文件。当webpack处理文件由 'postcss-loader',  'sass-loader' 走到'css-loader', 的时候，遇到了 ```@import './avatar.scss';``` 它就不知道该如何解析scss文件了
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
