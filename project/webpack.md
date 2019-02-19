## webpack 安装 
安装本地的webpack 
yarn add webpack webpack-cli -D
-D 表示development 开发环境

## webpack 可以进行0配置
- 创建src文件夹 表示源码文件夹 src目录下创建index.js
- 直接运行 npx webpack 
- 打包工具->输出后的结果(js模块)
- 打包（直接js的模块化）


## 手动配置webpack 
- mode 的值 一般是两个值 development 和 production
development 开发环境  production 生产环境 如果不配置，默认走的是生产环境
- entry 入口 可以是相对路径
- output 出口 输出 必须是绝对路径
  - path 输出路径 必须是绝对路径
  - filename:输出的文件名字
- 默认配置文件的名字是webpack.config.js
- webpack 是node写出来的 
- webpack-dev-server -D
```
 port:3000, #端口号
 progress:true, #显示进度条
 contentBase:'./dist', #目录
 compress:true  #是否开启gzip压缩
```

## 配置脚本 package.json 
- "build": "webpack --config webpack.config.js",
- "dev": "webpack-dev-server"
- 这样就可以通过npm run dev/npm run build执行相关的命令

## 处理html 

-  yarn add  html-webpack-plugin -D
```
- 删除dist目录，在src目录下面建一个index.html
- 当有插件的时候需要配置plugins 插件集合类型是数组
- 每一个插件都是通过new来调用，例：
 new HtmlWebpackPlugin({
            
        })
- 可以运行npm run dev/npm run build 查看结果
template:'./src/index.html',//模板
filename:'index.html', //编译后的文件名字
hash:true,//加hash值 

minify:{ //压缩配置   
    removeAttributeQuotes:true, //去除双引号
    collapseWhitespace: true,     //去除空格
     }
```

## 直接给文件加hash值 
```
filename:'bundle[hash].js'
可以用:后面跟数字设置hash值的长度
可以用数字设置hash值的长短 
filename:'bundle[hash:8].js'
```
## 处理样式
- . 通过require require('/index.css') 报错如下 

```You may need an appropriate loader to handle file type.    appropriate 合适的 你可能需要一个合适的loader 
Module not found：Can't resolve '/index.css' in '/Users/ruanye/Desktop/webpacklesson/src

```
- . 配置module,配置rules数组，表示很多规则，用正在匹配js、css等
执行命令yarn add css-loader style-loader -D
use后面的写法：
1.字符串 只能写一个loader
 use：'css-loader'
2.数组 可以写多个loader 
css-loader 解析require/import语法
style-loader 把css插入到header标签中
第一种写法：
use:['style-loader','css-loader']
loader的执行顺序是从右到左执行，从下到上
第二种写法：
loader里面还可以写成对象的形式， 
use:[{
					loader:'style-loader',
					options:{
						insertAt:'top' //css 放置位置可以决定css的优先级
					}
				  },
				'css-loader']
			 }
		 ] 
insertAt：top就把后来写的css展示到最前边
```
 {
     test:'/\.css/',//配置到css
     use:['style-loader','css-loader']
 }

```
- 配置less编译(less->css) 因为从右向左，从下到上执行 所以写在下边和右边
yarn add less less-loader -D
- 编译sass
node-sass sass-loader -D
- 编译stylus
stylus stylus-loader -D

##  抽离css 
- yarn add  mini-css-extract-plugin -D
 
用  MiniCssExtractPlugin.loader 代替style-loader 可以对css进行抽离 
- MiniCssExtractPlugin插件自带一个loader
-  MiniCssExtractPlugin.loader会自动把css抽离出来
new MiniCssExtractPlugin({
      filename: 'main.css'//抽离出来的css文件名
    })
    在loader里面的写法
    、、、
    {
      test：'/.css$/',
       MiniCssExtractPlugin.loader,
       'css-loader'
    }
    、、、
## 使用postcss-loder添加浏览器前缀 
- yarn add postcss-loader autoprefixer -D 
- 放到所有cssloader后面，执行顺序原因
npm run dev的时候会报错
 Error: No PostCSS Config found in: D:\study-data\webFile\ry\2019\webpack1\project\src
 没有找到postcss的默认文件
- 需要配置postcss默认文件 名字postcss.config.js
在根目录下创建postcss.config.js
postcss.config.js 文件里面的内容：
```
module.exports={
    plugins:[require('autoprefixer')]
}
```

## 配置优化项
- yarn add optimize-css-assets-webpack-plugin  uglifyjs-webpack-plugin -D 
optimize 优化  assets:资源 
optimization: {
    minimizer: [
      new UglifyJsPlugin({
        cache: true, //缓存 
        parallel: true, //是否并发打包
        sourceMap: true // 源码映射 
      }),
      new OptimizeCSSAssetsPlugin({})
    ]
  },
- mode 改成production
- npm run build 打包之后，main.css里的内容被压缩成一行
## 处理js es6转化成es5    
- yarn add babel-loader @babel/core @babel/preset-env -

@babel/core  babel核心模块
@babel/preset-env 标准语法转化成低级语法
-presets 预设 
-babel官网 https://babeljs.io/
- 箭头函数 arrow-functions
- class等和 es6@(装饰器需要安装额外的插件) 并且添加plugins集合
- yarn add @babel/plugin-proposal-class-properties @babel/plugin-proposal-decorators -D
```
{
  "plugins": [
    ["@babel/plugin-proposal-decorators", { "legacy": true }],
    ["@babel/plugin-proposal-class-properties", { "loose" : true }]
  ]
}
``` 
- babel 插件中最常用的插件
- promise genarater 需要 @babel/plugin-transform-runtime 常用
- yarn add  @babel/plugin-transform-runtime(生产环境也要用，所以后边不加-D) 
- 生产环境也需要runtime 
- yarn add @babel/runtime (根据webpack版本的问题，如果有找不到模块的报错，可以先不下载这个命令)
- es7的一些语法需要其他的  例如：includes 补丁包 所有解决不了的语法都可以用这条命令
- yarn add @babel/polyfill
在src里边的index.js写require("@babel/polyfill");

## 配置需要解析和不需要解析loader的文件路径  

- include 包含  include:path.resolve(__dirname,'src'), 
- exclude 不包含  exclude:/node_modules/

## babel 也可以独立进行配置，文件名字.babelrc
- 配置的时候loader直接写成use:'babel-loader',其他配置写在.babelrc里面
- 如果webpack options对babel-loader进行了配置，就不需要.babelrc文件，如果有就删除

## JS语法校验
- yarn add eslint eslint-loader -D
- eslint 官网 eslint.org
- 添加enforce pre 强制先执行 previous 前置loader
、、、
{
    test:'/\.js$/',
    loader:'eslint-loader',
    options:{
        enforce:'pre'
      }  
},
- 另一种配置方法 .eslint.js
module.exports = {
  //放代码
};
- .eslint.ignore    eslint的忽略项
、、、
 ## 第三方模块的使用 
- yarn add jquery
- yarn add expose-loader -D
- expose-loader 暴露全局的loader
、、、
 1.内联loader 方式配置
    import $ from "expose-loader?$!jquery"
、、、
2.正常配置
{
  test:require.resolve('jquery'),
  loader:"expose-loader?$"
}
、、、
3.在每个模块中注入$对象 不需要引入可以直接使用$ 在这里window.$是undefined
- 在plugins配置，ProvidePlugin webpack 自带插件
- 自带插件都需要引webpack模块
ProvidePlugin webpack 自带插件
 new webpack.ProvidePlugin({
      $:"jquery"
    })
##配置忽略打包项
externals:{
    jquery:"jQuery"
}
## 在webpack中引入图片的几种方式
1. 在js中创建图片来引入
import logo from './logo.png';
let img = new image ;   //会在内存里面创建一个新的图片
img.src = logo
document.body.appengChild(img)

You may need an appropriate loader to handle this file type.
你需要一个合适的loader来处理这个文件

2. 在css 引入 background(url)
3. <img src=''/>   需要把图片放到dist文件夹
## 图片处理 
yarn add file-loader  html-withimg-loader url-loader -D
file-loader 
{ test:/.(png,jpg,gif)$/, user:'file-loader' }

在html 引入图片打包会找不到文件 需要使用html-withimg-loader
url-loader 
html-withimg-loader
{ test:/.html$/, 
user:'html-withimg-loader' }

- 在图片非常小的情况下不希望走http请求，一般情况下不会直接使用
- 在图片小于多少k的时候可以做一个限制，用base64来转化,base64大小会比原来文件大3分之一 

limit 限制图片大小多大以内转换成base64
、、、
{
  test:/\.(png,jpg,gif)$/,
  user:{
    loder:'url-loader',
    options:{
      limit:1000 //limit 限制图片大小多大以内转换成base64 
    }
  }
}
、、、
- url-loader 也可以处理mp4|webm|ogg|mp3|wav|flac|aac等格式
-url-loader 可以处理各种字体格式 woff2?|eot|ttf|otf

## 打包文件分类 

1. 图片loader的options 里面添加
 options:{
      limit:1000
       outputPath:'/img/',
    }
2. css 添加在css插件里面 
  new MiniCssExtractPlugin({
      filename:'css/main.css'
    })
3. 添加域名 publicPath的用法
output: {
    filename: 'bundle.js', 
    path: path.resolve(__dirname, 'build'),
    publicPath:'http://www.baidu.cn'
  }
- 如果只需要图片添加域名
options:{
          limit:1,
          outputPath:'/img/',
          publicPath:'http://www.baidu.cn'
 }









