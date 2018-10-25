---
id: basic-path-resolve
title: 模块路径解析
sidebar_label: 模块路径解析
---

## 路径的形式

当你使用 `import`或 `require` 一个模块的时候, `webpack` 尝试把你写的所有模块路径 解析成**绝对路径**再做进一步处理.

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
