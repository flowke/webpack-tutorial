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

如果是一个模块路径: **默认**会从 `node_modules` 的绝对路径为起始路径, 进而转换成一个绝对路径

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

如果是个文件夹, 那么 webpack 会假定这个文件夹是一个程序包, 它会首先尝试查找这个包的描述文件, 也就是 `package.json`文件.

所以如果碰到个文件夹, 他会进一步进行如下解析:

  1. 尝试查找文件夹下的 `package.json` 这个文件
    1. 如果找到 `package.json` , 尝试找到里面的 main 字段描述的文件
  2. 如果没有找到 `package.json` 或 `package.json` 里面没有main字段, 或根据main字段没有找到有效的文件, 则尝试找当前文件下的 index.js 的文件

如果是个文件


## 你需要理解的 resolve 配置字段

#### modules

写模块路径的时候, 可以指定去哪个目录下搜索, 以下是其默认值:

```js
// 假设当前文件所在路径 : /Users/nod/desktop/project/

// resolve 的配置
{
  resolve: {
    modules: ['node_modules']
  }
}

// 此时 引入文件
// 这意味着, 它会先去 node_modules 下去搜索有没有 lodash 这个文件或文件夹
import _ from 'lodash'

```

以上的默认值是一个相对路径, 如果是相对路径, 那么webpack会从所指定的路径为起点递归查找:

如果是 `modules: ['node_modules']`, 会逐级往上查找,  `'./node_modules', '../node_modules', ...` 直到找到根路径为止.

如果只搜索了一两层目录就找到了还好, 如果遍历了所有父级目录都没有找到, 那么无疑会拖慢打包速度.

所以如果指定了一个绝对路径, 如: `path.resolve(__dirname, 'node_modules')`, 那么只会从指定的目录找, 如果找不到就直接放弃查找了.

当然, 你可以指定多个查找目录, 如:

```js

{
  resolve: {
    modules: ['node_modules', path.resolve(__dirname, 'src')]
  }
}

// 会先尝试从 node_modules 里找到 comp这个文件夹, 再去里面找home
// 如果node_modules 里找不到, 就会在 src 文件夹下找
import Home from 'comp/home'

```



#### alias

写模块路径的时候, 你可以在这里创建一个别名, 一旦别名被匹配上, 就会替换别名所指定的路径: (优先于 modules)

```js
// 假设当前文件所在路径 : /Users/nod/desktop/project/

// resolve 的配置
{
  resolve: {
    alias: {
      utils: path.resolve(__dirname, './src/utils')
    }
  }
}

// 此时 引入文件
import util from 'utils/getParams' // =>  /Users/nod/desktop/project/src/utils/getParams.js

```

#### descriptionFiles

我们之前说过, 当解析出一个路径的时候发现是一个文件夹, 会假定它是个包, 查找包的描述文件 package.json, 为什么是package.json呢, 其实就是这个字段指定的:

以下是其默认值, 当然你也可以指定其他的 json 文件, 或多个 json 文件.
```js
module.exports = {
  //...
  resolve: {
    descriptionFiles: ['package.json']
  }
};

```

#### mainFields

我们之前说, 如果找到一个文件夹, 就会先看里面的描述文件, 如果找到描述文件, 就会查找里面的 main 字段指定的文件. 为什么是main字段呢, 这个字段的配置决定的, 它的默认值 根据 webpack 的 target 的配置不同, target 的默认值是 'web', 所以:

```js
module.exports = {
  target : 'web', // target 默认值是 web
  resolve: {
    // 此时 mainFields 的默认值
    mainFields: ['browser', 'module', 'main']
  }
};
```
如果指定其他的其他的targer, 比如 node, 那么

```js
module.exports = {
  target : 'node', // target 值是 node
  resolve: {
    // 此时 mainFields 的默认值
    mainFields: [ 'module', 'main']
  }
};
```

当然你也可以自己指定 `mainFields` 的值, 这样就不会依据 `target` 的变化而变化.

我们之前说webpack 会直接去查找 `main` 字段的值, 这是片面的, 它其实是会从前往后找, 如果是 `['browser', 'module', 'main']`, 就会先找 `browser`, 如果没找到, 再找 `module` 看看有没有指定一个有效值, 最后才是 `main`, 但为什么说 `main` 呢, 因为大多数包的描述文件里面, 只指定了 `main` 字段. 不过也有很多包会实现多个字段.

所以具体查找哪个字段, 你要看此时的 `mainFields` 的配置.

#### mainFiles

如果解析出一个文件夹, 但是有没有包描述文件或 `mainFields` 提供的字段没提供一个有效的文件地址, 那么就会 找一个 `index` 的文件, 其实就是这个字段知道的, 它默认就是 `index`:

```js
module.exports = {
  //...
  resolve: {
    mainFiles: ['index']
  }
};
```

#### extensions

默认值为: `['.wasm', '.mjs', '.js', '.json']`, 如果你引入 `'./aas'`, 如果找不到一个叫作 `aas`的文件或`aas`的文件夹, 那么就会一次给 `aas`添加后缀, 看看有没有这个文件.

#### enforceExtension

是否强制需要些扩展名, 默认 false, 如果写 true, 那么引入 `'./aas'`, 如果没找到 aas 的文件或文件夹, 那么直接放弃查找, 不再尝试为其添加后缀.
