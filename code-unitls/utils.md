## 常用工具类utils

#### 首页 [index](../README.md)

#### 生成时间戳
```js
// 生成DDL时间戳函数
function getFormattedDateTime() {
    var date = new Date();
    var month = (date.getMonth() + 1).toString().padStart(2, '0'); // 月份从0开始，需要加1
    var day = date.getDate().toString().padStart(2, '0');
    var hours = date.getHours().toString().padStart(2, '0');
    var minutes = date.getMinutes().toString().padStart(2, '0');
    var seconds = date.getSeconds().toString().padStart(2, '0');

    var formattedDateTime = month + day + hours + minutes + seconds;
    return formattedDateTime;
}
```

#### 遍历对象键值对，处理对象
```js
function processValues(obj, processor) {
    const result = {};
    for (const key in obj) {
      if (obj.hasOwnProperty(key)) {
        result[key] = processor(obj[key]);
      }
    }
    return result;
  }
  
// 示例处理器函数processor：将每个值乘以2
const processedObj = processValues(obj, value => value * 2);

```