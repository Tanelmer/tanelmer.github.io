---
title: webpack-vue-cli配置详解
date: 2018-09-12
categories: 前端
tags: [Vue]
description: vue-cli脚手架工具的熟悉与配置
---
## vue-cli脚手架工具的熟悉与配置

这里主要看几个配置文件里面是干了一些什么事情，基本上分析完之后就知道了。
<!-- more -->
***
#### bulid文件夹
  1. build.js
  2. webpack.base.conf.js
  3. webpack.dev.conf.js
  4. webpack.prod.conf.js
  5. dev-server.js
  6. utils.js
***
#### config文件夹
  1. index.js
***
然后我们具体看下文件里的代码
bluid.js
```javascript
/**
 1. loading动画
 2. 删除创建目标文件夹
 3. webpack编译
 4. 输出信息
 */
require('./check-versions')()

process.env.NODE_ENV = 'production'

var ora = require('ora')
var rm = require('rimraf')
var path = require('path')
// 用于在控制台输出带颜色字体的插件
var chalk = require('chalk')
var webpack = require('webpack')
var config = require('../config')
var webpackConfig = require('./webpack.prod.conf')

var spinner = ora('building for production...')
spinner.start() //开启loading动画

// 递归删除旧的目标文件夹
rm(path.join(config.build.assetsRoot, config.build.assetsSubDirectory), err => {
  if (err) throw err
  // webpack编译
  webpack(webpackConfig, function (err, stats) {
    spinner.stop()
    if (err) throw err
    process.stdout.write(stats.toString({
      colors: true,
      modules: false,
      children: false,
      chunks: false,
      chunkModules: false
    }) + '\n\n')

    console.log(chalk.cyan('  Build complete.\n'))
    console.log(chalk.yellow(
      '  Tip: built files are meant to be served over an HTTP server.\n' +
      '  Opening index.html over file:// won\'t work.\n'
    ))
  })
})

```  
webpack.base.conf.js
```javascript
//引入依赖包
var path = require('path')
var utils = require('./utils')
var config = require('../config')
var vueLoaderConfig = require('./vue-loader.conf')

function resolve (dir) {
  return path.join(__dirname, '..', dir)
}

module.exports = {
  // 入口文件，路径相对于本文件所在的位置，可以写成字符串、数组、对象
  entry: {
    // path.resolve([from ...], to) 将to参数解析为绝对路径
    app: './src/main.js'
  },
  // 输出配置
  output: {
    // 输出文件，路径相对于本文件所在的位置
    path: config.build.assetsRoot,
    filename: '[name].js',
    // publicPath
    // 1.该属性的好处在于当你配置了图片CDN的地址，本地开发时引用本地的图片资源，上线打包时就将资源全部指向正式了，
    // 如果没有确定的发布地址不建议配置该属性，特别是在打包图片时，路径很容易出现混乱，如果没有设置，则默认从站点根目录加载
    publicPath: process.env.NODE_ENV === 'production'
      ? config.build.assetsPublicPath
      : config.dev.assetsPublicPath
  },
  resolve: {
    // require时省略的扩展名，遇到.js、.vue、.json结尾的也要去加载
    extensions: ['.js', '.vue', '.json'],

    //模块别名地址，方便后续直接引用别名，无须写长长的地址，注意如果后续不能识别该别名，需要先设置root
    //设置别名 是为了使用独立构建 npm默认是使用运行时构建 vue官网有两种构建方式的区别
    alias: {
      'vue$': 'vue/dist/vue.esm.js',
      '@': resolve('src')
    }
  },
  //模块加载器
  module: {
    rules: [
      // loader相当于gulp里的task，用来处理在入口文件中require的和其他方式引用进来的文件，
      // test是正则表达式，匹配要处理的文件；
      // loader匹配要使用的loader，"-loader"可以省略；
      // include把要处理的目录包括进来，exclude排除不处理的目录
      {
        test: /\.vue$/,
        loader: 'vue-loader',
        options: vueLoaderConfig
      },
      {
        test: /\.js$/,
        loader: 'babel-loader',
        include: [resolve('src'), resolve('test')]
      },
      {
        test: /\.(png|jpe?g|gif|svg)(\?.*)?$/,
        loader: 'url-loader',
        // 把较小的图片转换成base64的字符串内嵌在生成的js文件里 name指明了输出的命名规则
        options: {
          limit: 10000,
          name: utils.assetsPath('img/[name].[hash:7].[ext]')
        }
      },
      {
        test: /\.(woff2?|eot|ttf|otf)(\?.*)?$/,
        loader: 'url-loader',
        // 把较小的图标转换成base64的字符串内嵌在生成的js文件里 name指明了输出的命名规则
        options: {
          limit: 10000,
          name: utils.assetsPath('fonts/[name].[hash:7].[ext]')
        }
      }
    ]
  }
}

```
webpack.dev.conf.js
```javascript
// webpack.dev.conf.js在webpack.base.conf的基础上增加完善了开发环境下面的配置
/**
 1.将hot-reload相关的代码添加到entry chunks
 2.合并基础的webpack配置
 3.使用styleLoaders
 4.配置Source Maps
 5.配置webpack插件
 */
// 引入相关依赖
var utils = require('./utils')
var webpack = require('webpack')
var config = require('../config')
// 一个可以合并数组和对象的插件
var merge = require('webpack-merge')
var baseWebpackConfig = require('./webpack.base.conf')
// 一个用于生成HTML文件并自动注入依赖文件（link/script）的webpack插件
var HtmlWebpackPlugin = require('html-webpack-plugin')
// 用于更友好地输出webpack的警告、错误等信息
var FriendlyErrorsPlugin = require('friendly-errors-webpack-plugin')

// add hot-reload related code to entry chunks
// 这是上面的第一点 热重载与entry相关联
Object.keys(baseWebpackConfig.entry).forEach(function (name) {
  baseWebpackConfig.entry[name] = ['./build/dev-client'].concat(baseWebpackConfig.entry[name])
})

// 合并基础的webpack配置
module.exports = merge(baseWebpackConfig, {
  // 配置样式文件的处理规则，使用styleLoaders
  module: {
    rules: utils.styleLoaders({ sourceMap: config.dev.cssSourceMap })
  },
  // cheap-module-eval-source-map is faster for development
  // 配置Source Maps。在开发中使用cheap-module-eval-source-map更快 这是纯翻译~~~ 晚点去找找这个玩意干啥的
  devtool: '#cheap-module-eval-source-map',
  // webpack插件配置
  plugins: [
    new webpack.DefinePlugin({
      'process.env': config.dev.env
    }),
    // https://github.com/glenjamin/webpack-hot-middleware#installation--usage
    new webpack.HotModuleReplacementPlugin(),
    // 报错不会阻塞，但是会在编译结束后报错
    new webpack.NoEmitOnErrorsPlugin(),
    // https://github.com/ampedandwired/html-webpack-plugin
    new HtmlWebpackPlugin({
      filename: 'index.html',
      template: 'index.html',
      inject: true
    }),
    new FriendlyErrorsPlugin()
  ]
})

```
webpack.prod.conf.js
```javascript
// 构建的时候用到的webpack配置来自webpack.prod.conf.js，该配置同样是在webpack.base.conf基础上的进一步完善。
/**
 1. 合并基础的webpack配置
 2. 使用styleLoaders
 3. 配置webpack的输出
 4. 配置webpack插件
 5. gzip模式下的webpack插件配置
 6. webpack-bundle分析
 */
var path = require('path')
var utils = require('./utils')
var webpack = require('webpack')
var config = require('../config')
var merge = require('webpack-merge')
var baseWebpackConfig = require('./webpack.base.conf')
var CopyWebpackPlugin = require('copy-webpack-plugin')
var HtmlWebpackPlugin = require('html-webpack-plugin')
// 用于从webpack生成的bundle中提取文本到特定文件中的插件
// 可以抽取出css，js文件将其与webpack输出的bundle分离
var ExtractTextPlugin = require('extract-text-webpack-plugin')
var OptimizeCSSPlugin = require('optimize-css-assets-webpack-plugin')

var env = config.build.env

// 合并基础的webpack配置
var webpackConfig = merge(baseWebpackConfig, {
  module: {
    rules: utils.styleLoaders({
      sourceMap: config.build.productionSourceMap,
      extract: true
    })
  },
  devtool: config.build.productionSourceMap ? '#source-map' : false,
  // 配置webpack的输出
  output: {
    path: config.build.assetsRoot,
    filename: utils.assetsPath('js/[name].[chunkhash].js'),
    chunkFilename: utils.assetsPath('js/[id].[chunkhash].js')
  },
  plugins: [
    // http://vuejs.github.io/vue-loader/en/workflow/production.html
    new webpack.DefinePlugin({
      'process.env': env
    }),
    // 丑化压缩代码
    new webpack.optimize.UglifyJsPlugin({
      compress: {
        warnings: false
      },
      sourceMap: true
    }),
    // extract css into its own file
    // 抽离css文件
    new ExtractTextPlugin({
      filename: utils.assetsPath('css/[name].[contenthash].css')
    }),
    // Compress extracted CSS. We are using this plugin so that possible
    // duplicated CSS from different components can be deduped.
    new OptimizeCSSPlugin({
      cssProcessorOptions: {
        safe: true
      }
    }),
    // generate dist index.html with correct asset hash for caching.
    // you can customize output by editing /index.html
    // see https://github.com/ampedandwired/html-webpack-plugin
    new HtmlWebpackPlugin({
      filename: config.build.index,
      template: 'index.html',
      inject: true,
      minify: {
        removeComments: true,
        collapseWhitespace: true,
        removeAttributeQuotes: true
        // more options:
        // https://github.com/kangax/html-minifier#options-quick-reference
      },
      // necessary to consistently work with multiple chunks via CommonsChunkPlugin
      chunksSortMode: 'dependency'
    }),
    // split vendor js into its own file
    new webpack.optimize.CommonsChunkPlugin({
      name: 'vendor',
      minChunks: function (module, count) {
        // any required modules inside node_modules are extracted to vendor
        return (
          module.resource &&
          /\.js$/.test(module.resource) &&
          module.resource.indexOf(
            path.join(__dirname, '../node_modules')
          ) === 0
        )
      }
    }),
    // extract webpack runtime and module manifest to its own file in order to
    // prevent vendor hash from being updated whenever app bundle is updated
    new webpack.optimize.CommonsChunkPlugin({
      name: 'manifest',
      chunks: ['vendor']
    }),
    // copy custom static assets
    new CopyWebpackPlugin([
      {
        from: path.resolve(__dirname, '../static'),
        to: config.build.assetsSubDirectory,
        ignore: ['.*']
      }
    ])
  ]
})

// gzip模式下需要引入compression插件进行压缩
if (config.build.productionGzip) {
  var CompressionWebpackPlugin = require('compression-webpack-plugin')

  webpackConfig.plugins.push(
    new CompressionWebpackPlugin({
      asset: '[path].gz[query]',
      algorithm: 'gzip',
      test: new RegExp(
        '\\.(' +
        config.build.productionGzipExtensions.join('|') +
        ')$'
      ),
      threshold: 10240,
      minRatio: 0.8
    })
  )
}

if (config.build.bundleAnalyzerReport) {
  var BundleAnalyzerPlugin = require('webpack-bundle-analyzer').BundleAnalyzerPlugin
  webpackConfig.plugins.push(new BundleAnalyzerPlugin())
}

module.exports = webpackConfig

```
dev-server.js
首先来看执行”npm run dev”时候最先执行的build/dev-server.js文件。该文件主要完成下面几件事情：

1. 检查node和npm的版本
2. 引入相关插件和配置
3. 创建express服务器和webpack编译器
4. 配置开发中间件（webpack-dev-middleware）和热重载中间件（webpack-hot-middleware）
5. 挂载代理服务和中间件
6. 配置静态资源
7. 启动服务器监听特定端口（8080）
8. 自动打开浏览器并打开特定网址（localhost:8080）
说明： express服务器提供静态文件服务，不过它还使用了http-proxy-middleware，一个http请求代理的中间件。前端开发过程中需要使用到后台的API的话，可以通过配置proxyTable来将相应的后台请求代理到专用的API服务器
```javascript
// 检查NodeJS和npm的版本
require('./check-versions')()

var config = require('../config')
// 如果Node的环境变量中没有设置当前的环境（NODE_ENV），则使用config中的配置作为当前的环境
if (!process.env.NODE_ENV) {
  process.env.NODE_ENV = JSON.parse(config.dev.env.NODE_ENV)
}

// 一个可以调用默认软件打开网址、图片、文件等内容的插件
// 这里用它来调用默认浏览器打开dev-server监听的端口，例如：localhost:8080
var opn = require('opn')
var path = require('path')
var express = require('express')
var webpack = require('webpack')

// 一个express中间件，用于将http请求代理到其他服务器
// 例：localhost:8080/api/xxx  -->  localhost:3000/api/xxx
// 这里使用该插件可以将前端开发中涉及到的请求代理到API服务器上，方便与服务器对接
var proxyMiddleware = require('http-proxy-middleware')
var webpackConfig = require('./webpack.dev.conf')

// default port where dev server listens for incoming traffic
// dev-server 监听的端口，默认为config.dev.port设置的端口，即8080
var port = process.env.PORT || config.dev.port
// automatically open browser, if not set will be false
// 用于判断是否要自动打开浏览器的布尔变量，当配置文件中没有设置自动打开浏览器的时候其值为 false
var autoOpenBrowser = !!config.dev.autoOpenBrowser
// Define HTTP proxies to your custom API backend
// https://github.com/chimurai/http-proxy-middleware
// 定义 HTTP 代理表，代理到 API 服务器
var proxyTable = config.dev.proxyTable

var app = express()
// 根据webpack配置文件创建Compiler对象
var compiler = webpack(webpackConfig)

// webpack-dev-middleware使用compiler对象来对相应的文件进行编译和绑定
// 编译绑定后将得到的产物存放在内存中而没有写进磁盘
// 将这个中间件交给express使用之后即可访问这些编译后的产品文件
var devMiddleware = require('webpack-dev-middleware')(compiler, {
  publicPath: webpackConfig.output.publicPath,
  quiet: true
})

// webpack-hot-middleware，用于实现热重载功能的中间件
var hotMiddleware = require('webpack-hot-middleware')(compiler, {
  log: () => {}
})

// force page reload when html-webpack-plugin template changes
// 当html-webpack-plugin提交之后通过热重载中间件发布重载动作使得页面重载
compiler.plugin('compilation', function (compilation) {
  compilation.plugin('html-webpack-plugin-after-emit', function (data, cb) {
    hotMiddleware.publish({ action: 'reload' })
    cb()
  })
})

// proxy api requests
// 将 proxyTable 中的代理请求配置挂在到express服务器上
Object.keys(proxyTable).forEach(function (context) {
  var options = proxyTable[context]
  if (typeof options === 'string') {
    options = { target: options }
  }
  app.use(proxyMiddleware(options.filter || context, options))
})

// handle fallback for HTML5 history API
// 重定向不存在的URL，常用于SPA
app.use(require('connect-history-api-fallback')())

// serve webpack bundle output
// 使用webpack开发中间件
// 即将webpack编译后输出到内存中的文件资源挂到express服务器上
app.use(devMiddleware)

// enable hot-reload and state-preserving
// compilation error display
// 将热重载中间件挂在到express服务器上
app.use(hotMiddleware)

// serve pure static assets
// 将静态资源挂到express服务器上
var staticPath = path.posix.join(config.dev.assetsPublicPath, config.dev.assetsSubDirectory)
app.use(staticPath, express.static('./static'))

var uri = 'http://localhost:' + port

var _resolve
var readyPromise = new Promise(resolve => {
  _resolve = resolve
})

// 控制台输入启动信息
console.log('> Starting dev server...')
devMiddleware.waitUntilValid(() => {
  console.log('> Listening at ' + uri + '\n')
  // when env is testing, don't need open it
  if (autoOpenBrowser && process.env.NODE_ENV !== 'testing') {
    opn(uri)
  }
  _resolve()
})

// 监听端口
var server = app.listen(port)

module.exports = {
  ready: readyPromise,
  close: () => {
    server.close()
  }
}

```
utils.js
```javascript
/**
 1. 配置静态资源路径
 2. 生成cssLoaders用于加载.vue文件中的样式
 3. 生成styleLoaders用于加载不在.vue文件中的单独存在的样式文件
 */
var path = require('path')
var config = require('../config')
var ExtractTextPlugin = require('extract-text-webpack-plugin')

exports.assetsPath = function (_path) {
  var assetsSubDirectory = process.env.NODE_ENV === 'production'
    ? config.build.assetsSubDirectory
    : config.dev.assetsSubDirectory
  return path.posix.join(assetsSubDirectory, _path)
}

exports.cssLoaders = function (options) {
  options = options || {}

  var cssLoader = {
    loader: 'css-loader',
    options: {
      minimize: process.env.NODE_ENV === 'production',
      sourceMap: options.sourceMap
    }
  }

  // generate loader string to be used with extract text plugin
  function generateLoaders (loader, loaderOptions) {
    var loaders = [cssLoader]
    if (loader) {
      loaders.push({
        loader: loader + '-loader',
        options: Object.assign({}, loaderOptions, {
          sourceMap: options.sourceMap
        })
      })
    }

    // Extract CSS when that option is specified
    // (which is the case during production build)
    if (options.extract) {
      return ExtractTextPlugin.extract({
        use: loaders,
        fallback: 'vue-style-loader'
      })
    } else {
      return ['vue-style-loader'].concat(loaders)
    }
  }

  // https://vue-loader.vuejs.org/en/configurations/extract-css.html
  return {
    css: generateLoaders(),
    postcss: generateLoaders(),
    less: generateLoaders('less'),
    sass: generateLoaders('sass', { indentedSyntax: true }),
    scss: generateLoaders('sass'),
    stylus: generateLoaders('stylus'),
    styl: generateLoaders('stylus')
  }
}

// Generate loaders for standalone style files (outside of .vue)
exports.styleLoaders = function (options) {
  var output = []
  var loaders = exports.cssLoaders(options)
  for (var extension in loaders) {
    var loader = loaders[extension]
    output.push({
      test: new RegExp('\\.' + extension + '$'),
      use: loader
    })
  }
  return output
}

```
***
config文件夹下的index.js
在这里面描述了开发和构建两种环境下的配置，前面的build文件夹下也有不少文件引用了index.js里面的配置
```javascript
// see http://vuejs-templates.github.io/webpack for documentation.
var path = require('path')

module.exports = {
  build: {
    env: require('./prod.env'),
    index: path.resolve(__dirname, '../dist/index.html'),
    assetsRoot: path.resolve(__dirname, '../dist'),
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    productionSourceMap: true,
    // Gzip off by default as many popular static hosts such as
    // Surge or Netlify already gzip all static assets for you.
    // Before setting to `true`, make sure to:
    // npm install --save-dev compression-webpack-plugin
    productionGzip: false,
    productionGzipExtensions: ['js', 'css'],
    // Run the build command with an extra argument to
    // View the bundle analyzer report after build finishes:
    // `npm run build --report`
    // Set to `true` or `false` to always turn it on or off
    bundleAnalyzerReport: process.env.npm_config_report
  },
  dev: {
    env: require('./dev.env'),
    port: 8080,
    autoOpenBrowser: true,
    assetsSubDirectory: 'static',
    assetsPublicPath: '/',
    proxyTable: {},
    // CSS Sourcemaps off by default because relative paths are "buggy"
    // with this option, according to the CSS-Loader README
    // (https://github.com/webpack/css-loader#sourcemaps)
    // In our experience, they generally work as expected,
    // just be aware of this issue when enabling this option.
    cssSourceMap: false
  }
}
```
还有就是build文件夹下面两个比较简单的文件
dev-client.js似乎没有使用到，代码也比较简单，这里不多讲。
check-version.js完成对node和npm的版本检测
*** 
之前没学webpack&vue的时候用这个cli工具用的比较蛋疼，然后刚好有时间去看下这里面的配置。
还是熟悉了很多，至少知道了这些代码的意义，做的事情的哪些。算是对webpack这个打包工具的初识吧。
> 本文多处源于：[vue-cli的webpack模板项目配置文件分析](http://blog.csdn.net/hongchh/article/details/55113751)
感谢大佬的分享，此处仅做笔记。


  
