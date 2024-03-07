### inquirer 库

```js
// 命令行打印文件列表
async function diyFiles(lists) {
    const promptList = [
        {
            type: 'list',
            message: '请选择一个选项:',
            name: 'choice',
            choices: lists,
        },
    ];

    let answers = await inquirer.prompt(promptList)
    return answers.choice
}
```

### ora库
```js
import ora from 'ora';

// 创建loading对象，prefixText是loading对象的开头字符,suffixText是loading对象的末尾字符
const loading = ora({
    color: 'green',
    text: 'Loading...',
    prefixText: '文件编码',
    suffixText:`  MemoryUsed ${(((process.memoryUsage()).heapTotal) / 1024 / 1024).toFixed(2)} MB   TotalMemory ${defaultHeapSize} MB`
});

//加载过程中修改对象信息
loading.text = 'Loading...'
loading.suffixText =''
loading.start()

//加载结束后调用
loading.succeed(`文件成功编码为${chalk.bgGreen('UTF-8')}`);
loading.stop()
```