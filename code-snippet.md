
# javascript snippet
### 文件操作
```js
//获取文件扩展名
path.extname(file)

// 遍历文件列表
for (const file of files) {
    const filePath = path.join(process.cwd(), file);
    const stats = fs.lstatSync(filePath);

    if (filePath.endsWith('.csv')) {
        console.log(filePath)
        const innerStats = fs.lstatSync(filePath);
        console.log(innerStats.isFile())

        fs.readFile(filePath, (err, data) => {
            if (err) {
                console.error('Error reading file:', err);
            } else {
                languageEncoding(data).then(fileInfo => {
                    console.log('File encoding:', fileInfo.encoding, filePath);

                    if (fileInfo.encoding !== 'UTF-8') {
                        convertEncoding(filePath)
                    }
                }).catch(error => {
                    console.error('Error detecting encoding:', error);
                });
            }
        });
    }
}

//异步读取文件列表
async function getFileList(filePath) {
    try {
        const files = await fs.readdir(filePath)
        const excelFiles = files.filter(file => path.extname(file) === '.xlsx' || path.extname(file) === '.xls' || path.extname(file) === '.csv');
        return excelFiles
    } catch (error) {
        throw `❌   读取当前目录文件出错${error}`
    }
}

getFileList(process.cwd())
    .then(result => {
        const excelFiles = result.filter(file => path.extname(file) === '.xlsx' || path.extname(file) === '.xls' || path.extname(file) === '.csv');
        let chatChoices = [
            {
                type: 'list',
                message: '请选择一个选项:',
                name: 'choice',
                choices: excelFiles,
            },
        ];
        inquirer.prompt(chatChoices).then((answers) => {
            console.log('你选择了:', answers.choice);
        });
    })
    .catch(err => console.log('读取文件错误', err))


//写入文件
async function writeFile(filename, data) {
    let outPath = path.join(process.cwd(), filename + '.utf8.csv')
    await fs.writeFile(filename + '.utf8.csv', data.toString(), 'utf-8');
    console.log(`✔️ 成功转码${chalk.bgGreen('UTF-8')},  文件路径-->：${outPath}`);
    return {
        codeType: 'UTF-8',
        fileName: filename,
        outPath: outPath
    }
}
```




### async await promise callback
[异步函数](./async-await-promise-callback.md)

### Node.js stream
[Node.js stream 流式读取](./nodejs-stream.md)

### OS process
[Node.js OS process 系统及进程模块](./os-process.md)

### 正则表达式
[正则表达式](./regex-collection.md)

### CLI 命令行交互
[命令行交互](./cli-info.md)

### utils 
[utils](./utils.md)