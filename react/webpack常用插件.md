# webpack 常用插件

## ExtractTextWebpackPlugin

从一个或多个 bundle 中提取文本到单独的文件中。

### 用法

```javascript
const ExtractTextPlugin = require('extract-text-webpack-plugin')

module.exports = {
    module: {
        rules: [
            {
                test: /\.css$/,
                use: ExtractTextPlugin.extract({
                    fallback: 'style-loader',
                    use: 'css-loader'
                })
            }
        ]
    },
    plugins: [
        new ExtractTextPlugin('style.css'),
    ]
}
```
