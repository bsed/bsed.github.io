---
title: 如何 在vscode 下新建 vue user snippets 代码片段
date: 2019-09-19 08:53:00
updated: 2019-09-19 08:56:35
tags: 
- rust
- cargo
- rustup
categories: 
- rust

---
我们在使用VScode新建一个文件的时候，比如 **vue** 文件，我们经常需要手动输入像下面的内容：


<!--more-->


```html
<template>
 
</template>

<script>
export default {
  name: "",
  data() {
    return {

    }
  }
}
</script>

<style scoped>

</style>
```

每次新建都需要这么做，显得很麻烦，所以，我们需要配置一个(`User Snippets`)模板

### 步骤

点击顶部菜单，`File-> Preferences -> User Snippets`
选择新建全局代码片段文件，随便输入一个文件名，比如vue

文件默认内容如下：

```json
{
    // Place your global snippets here. Each snippet is defined under a snippet name and has a scope, prefix, body and 
    // description. Add comma separated ids of the languages where the snippet is applicable in the scope field. If scope 
    // is left empty or omitted, the snippet gets applied to all languages. The prefix is what is 
    // used to trigger the snippet and the body will be expanded and inserted. Possible variables are: 
    // $1, $2 for tab stops, $0 for the final cursor position, and ${1:label}, ${2:another} for placeholders. 
    // Placeholders with the same ids are connected.
    // Example:
    // "Print to console": {
    //     "scope": "javascript,typescript",
    //     "prefix": "log",
    //     "body": [
    //         "console.log('$1');",
    //         "$2"
    //     ],
    //     "description": "Log output to console"
    // }
}
```


配置文件包含三个部分：

- `prefix`就是触发此模板文件的命令
- `body`是模板内容，
- `description`就是描述信息



## 配置内容

以 vue 文件为例，配置模板内容如下：

```json
"Create vue template": {
        "prefix": "vue",
        "body": [
            "<template>",
            "  <div>\n$0",
            "  </div>",
            "</template>\n",
            "<script>",
            "export default {",
            "  name: \"${1:component_name}\",",
            "  data() {",
            "    return {\n",
            "    }",
            "  }",
            "}",
            "</script>\n",
            "<style scoped>\n",
            "</style>"
        ],
        "description": "default vue template"
    }
```

将上述代码片段复制到`{}`内。

新建一个 vue 文件，输入`vue`，按回车，会自动创建模板，如图所示：

![94025-om55r2b55n.png](https://imgs.gnux.cn/usr/uploads/2019/09/2315596933.png)