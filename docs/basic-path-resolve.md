---
id: basic-path-resolve
title: 模块路径解析
sidebar_label: 模块路径解析
---

## 路径的形式

你写的路径, 基本上来说会写出三种路径解析规则:

```js
// 相对路径形式
import './utils/util';
import '../components/home';

// 绝对路径形式
import mos from '/User/flowke/desktop/file'

// 模块路径形式
import _ from 'lodash';
import axios from 'axios';
import axios from 'axios/dist/axios.min.js'; // js的后缀名可以不加


```

相对路径以 `./` 或  `../` 的形式开头.

绝对路径以 `/` 开头.

模块路径前面不会以 `./` 或 `/` 开头. 直接以其他合法字符开头, 类似一个文件夹名或文件名的形式开头.

## 查找规则

当你使用 `import` 或 `require` 一个模块的时候, `webpack` 尝试把你写的所有模块路径 解析成**绝对路径**再做进一步处理.


当路径是一个相对路径: 以当前模块的所在的文件夹的绝对路径为起始路径, 进而转换出一个绝对路径

如果是一个绝对路径: 那就不会再进行转换,

如果是一个模块路径: **默认**会从 node_modules 的绝对路径为起始路径, 进而转换成一个绝对路径

```js
// 假设当前文件所在路径 : /Users/nod/desktop/project/

import a from './a' // => /Users/nod/desktop/project/a

import b from '/Users/nod'; // => /Users/nod

import c from 'lodash' // => /Users/nod/desktop/project/node_modules/lodash

```

**具体规则:**

以下是默认情况下的解析规则.

在把路径解析成上述的基本路径之后, 还不能真正的定位文件, 还需要进一步解析:

这个时候webpack的解析器会开始检查这个路径 是指向一个文件还是文件夹:

如果是个文件夹, 那么按照以下步骤定位:
  1. 尝试查找文件夹下的 package.json 这个文件
    1. 如果找到 package.json , 尝试找到里面的 main 字段描述的文件
  2. 如果没有找到 package.json 或 package.json 里面没有main字段, 或根据main字段没有找到有效的文件, 则尝试找当前文件下的 index.js 的文件

如果是个文件


## 你需要理解的 resolve 配置字段

#### modules

写模块路径的时候, 可以指定去哪个目录下搜索, 一下是其默认值:

```js
// 假设当前文件所在路径 : /Users/nod/desktop/project/

// resolve 的配置
{
  resolve: {
    modules: ['node_modules']
  }
}

// 此时 引入文件
import util from 'utils/getParams' // =>  /Users/nod/desktop/project/src/utils/getParams.js

```

#### alias

写模块路径的时候, 你可以在这里创建一个别名, 一旦别名被匹配上, 就会替换别名所指定的路径: (优先于 modules) 

```js
// 假设当前文件所在路径 : /Users/nod/desktop/project/

// resolve 的配置
{
  resolve: {
    alias: {
      utils: path.resolve(__dirname, './src/utils)
    }
  }
}

// 此时 引入文件
import util from 'utils/getParams' // =>  /Users/nod/desktop/project/src/utils/getParams.js

```

#### aliasFields


#### descriptionFiles

#### enforceExtension

#### extensions

#### mainFields

#### mainFiles