---
layout:     post
title:      "【学习笔记】 webpack"
subtitle:   ""
author:     "wml"
header-img: "img/bg-1.png"
header-mask:  0.5
catalog: true
tags:

    - 笔记
---

## webpack学习

### webpack概念

#### 入口(entry)

指定入口，指示webpack使用哪个模块作为构建起内部依赖图的开始。配置一个或多个入口起点，默认值`./src`

webpack.config.js
```javascript
module.exports = {
    entry: './path/file.js'
};
```

单个入口

> 用法：entry: string\|Array&lt;string&gt;

```javascript
const config = {
    entry: './path/file.js'
};

module.exports = config;
```

entry属性的单入口语法，是下面的简写

```javascript
const config = {
    entry: {
        main: './path/file.js'
    }
};

module.exports = config;
```

多入口

> 用法：entry: {[entryChunkName: string]: string\|Array&lt;string&gt;}

分离应用程序[app]和第三方库[vendor]入口

```javascript
const config = {
    entry: {
        app: './path/app.js',
        vendors: './src/vendors.js'
    }
};
```

这种设计允许从应用程序bundle中提取vandor到vendor bundle,并把引用vendor的部分替换为`__webpack_require__()`调用。

使用`CommonsChunkPlugin`为每个页面间的应用程序共享代码创建bundle，由于入口点增多，多页应用能够复用入口起点间的大量代码和模块。

#### 输出(output)

指定输出，默认值`./dist`

```javascript
const path = require('path');

module.exports = {
    entry: './path/file.js',
    output: {
        path: path.resolve(__dirname, 'dist'),
        filename: 'my_webpack.bundle.js'
    }
}
```

配置output选项可以控制webpack如何向硬盘写入便以文件。即使存在多个入口起点，但只能指定一个output配置。

> 用法； webpack中配置output属性的最低要求是将它的值设置为一个对象，包括：
> * filename  用于输出文件的文件名。

```javascript
module.exports = {
    output: {
        filename: 'bundle.js',
    }
};
```

以上配置将会输出一个单独的bundle.js到dist目录。

多个入口

如果配置了多个单独的“chunk”（如，使用多个入口或使用CommonsChunkPlugin 这样的插件），则应该使用占位符来确保每个文件具有唯一的名称。

```javascript
module.exports = {
    entry: {
        app: './src/app.js',
        search: './src/search.js'
    },
    output: {
        filename: '[name].js',
        path: __dirname + '/dist'
    }
};

// 写入到硬盘： ./dist/app.js, ./dist/searchs.js
```

使用cdn和资源hash的示例：

```javascript
output: {
    path: '/home/pro/cdn/assets/[hash]',
    publicPath: "http://cdn.example.com/assets/[hash]/"
}
```

在编译是不知道最终输出文件的publicPath时，可以留空，在入口起点文件运行时动态设置`__webpack_public_path__`

```javascript
__webpack_public_path__ = myRuntimePublicPath
```

#### loader

处理非JavaScript文件（webpack自身只理解JavaScript）对源码进行转换。loader能够`import`导入任何类型的模块（eg: .css文件）。

loader有两个目标：

1. `test`属性，用于标识出应该被对应的loader进行转换的某个或某些文件。

2. `use`属性，表示进行转换时，应该使用哪个loader。

```javascript
const path = require('path');

const config = {
    output: {
        filename: 'my_webpack.bundle.js'
    },
    module: {
        rules: [
            {
                test: /\.txt$/,
                use: 'raw-loader'
            }
        ]
    }
};

module.exports = config;
```

> 在webpack配置中定义loader时，要定义在module.rules中而不是rules。

loader使用方式：

* 配置，在webpack.config.js文件中指定loader，module.rules 允许你在 webpack 配置中指定多个 loader

```javascript
module: {
    rules: [
        {loader: 'style-loader'},
        {
          loader: 'css-loader',
          options: {
              modules: true
          }
        }
    ]
}
```

* 内联，在import语句中指定loader，使用!将资源中的loader分开。

`import Styles from 'style-loader!css-loader?modules!./styles.css'`

* CLI

`webpack --module-bind jade-loader --module-bind 'css=style-loader!css-loader'`

#### 插件（plugins）

loader被用于转换某些类型的模块，而插件则用于执行从打包优化和压缩，一直到重新定义环境中的变量等任务。使用一个插件，需要`require()`它，然后添加到`plugins`数组中。可以在一个配置文件中因为不同目的而多次使用同一个插件，这时需要通过使用 new 操作符来创建它的一个实例。

```javascript
const HtmlWebpackPlugin = require('html-webpack-plugin'); // 通过npm安装
const webpack = require('webpack'); // 用于访问内置插件

const config = {
    module: {
        rules: [
            {test: /\.txt$/, use: 'raw-loader'}
        ]
    },
    pulgin: [
        new webpack.optimize.UglifyJsPlugin(),
        new HtmlWebpackPlugin({
            template: './src/index.html'
        })
    ]
};

module.exports = config;
```

#### 模式

通过选择`development`或`production`中的一个来设置`mode`参数，启用相应模式下的webpack内置优化。相应将`progress.env.NODE_ENV`的值设置为`development`或`production`。

```javascript
module.export = {
    mode: 'production'
};
```

#### 模块热替换(hot module replacement)

在应用程序运行过程中替换、添加或者删除模块，而无需加载整个页面。主要通过以下方式加快开发速度：

* 保留在完全重新加载页面时丢失的应用程序状态；
* 只更新变更内容，以节省宝贵开发时间；
* 调整演示更加快速，几乎相当于在浏览器调试器中更改。
