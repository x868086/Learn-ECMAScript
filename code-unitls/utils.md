## 常用工具类utils

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