# async await promise callback
```js
// 异步函数起始运行
handleFile(process.cwd()).catch(err => {
    console.log(`❌ 读取命令行发生错误：, ${err}`)
    throw err
})
```

//异步函数中增加promise 将异步操作变成同步的形式
```js
async function fileSaveAsCsv(filename) {
    // 这里workbookReader on 绑定的事件函数是异步函数
    workbookReader.on('worksheet', worksheet => {
        worksheet.on('row',row=>{
            // doSomethings
        })
    }

    // 将绑定的异步函数通过promise转换成同步的形式，等待该事件函数执行完之后再返回return
    await new Promise((resolve, reject) => {
        csvStream.on('end', () => {
            // csvStream.end()方法触发writeStream.end()
            writeStream.end()
            resolve('WTF!!!!!')
        })
        csvStream.on('error', () => {
            reject(new Error('临时表格创建错误'))
        })
    })

    // 异步函数执行完成后让fileSaveAsCsv函数有返回值
    return {
        codeType: 'UTF-8',
        fileName: filename,
        outPath: tempFilePath
    }

}

```