### 模块导入导出
在Node.js项目中使用ES module规范导入模块，需要在package.json中增加配置项
`"type": "module"`

---
`import { createReadStream } from 'node:fs' `
是一种特殊的模块导入方式，用于从 Node.js 的内置模块中导入特定的函数或对象。这种导入方式是在 ECMAScript 模块语法中引入的。
在 Node.js 中，内置的模块可以通过 node: 前缀进行导入，后面跟着模块的名称，例如 node:fs。这种导入方式允许您直接从 Node.js 的内置模块中导入所需的函数或对象，而无需先通过 require 或 import 语句加载整个模块。

在上述示例中，`import { createReadStream } from 'node:fs' `使用了 ECMAScript 模块语法，从 Node.js 的内置模块 fs 中导入了 createReadStream 函数。这样，您就可以直接在代码中使用 `createReadStream`，而不需要使用完整的命名空间 `fs.createReadStream`。
需要注意的是，这种导入方式仅适用于支持 ECMAScript 模块语法的环境，例如使用较新版本的 Node.js 或支持模块语法的浏览器。如果您的环境不支持 ECMAScript 模块语法，则需要使用传统的 require 语句来导入模块。


---
`import { promises as fs } from 'fs' `
是一种使用 ES6 模块语法导入 Node.js 的 fs 模块的方式。这种导入方式允许您使用 fs 对象来访问 fs 模块中的所有功能和方法。在这种导入方式中，将 fs 模块的 `promises` 属性导入到一个名为 fs 的变量中。promises 属性提供了一组基于 Promise 的异步方法，这些方法使用了 Node.js 的 fs 模块，但返回的是 Promise 对象，使您可以使用 async/await 或 .then().catch() 等方式处理异步操作。这种导入方式仅适用于支持 ES6 模块语法的环境，例如使用较新版本的 Node.js 或支持模块语法的浏览器。如果您的环境不支持 ES6 模块语法，则需要使用传统的 require 语句来导入模块。