# 使用插件

Gitbook 本身功能丰富，但同时可以使用插件来进行个性化定制。[Gitbook 插件](https://plugins.gitbook.com/plugin) 里已经有100多个插件，可以在 `book.json` 文件的 `plugins` 和 `pluginsConfig` 字段添加插件及相关配置，添加后别忘了进行安装。

## book.json文件如下：
```
{
  "title": "book hulongjaing",
  "description": "这是一个例子",
  "language": "zh",
  "plugins": [
    "github",
    "editlink",
    "prism",
    "-highlight",
    "splitter",
    "sitemap",
	"expandable-chapters"
  ],
  "pluginsConfig": {
    "github": {
      "url": "https://github.com/hulongjiang1/hulongjiang.github.io.git"
    },
    "editlink": {
      "base": "https://github.com/hulongjiang1/hulongjiang.github.io/edit/master",
      "label": "编辑本页"
    },
    "sitemap": {
        "hostname": "http://zhaoda.net/"
    },
	"pluginsConfig": {
        "expandable-chapters":{}
    }
  }
}
```

## 安装插件
``` 
安装插件
$ gitbook install ./
```