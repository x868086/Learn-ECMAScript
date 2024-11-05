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
1. for...in循环
**for...in** 循环可以遍历对象的所有可枚举属性。你可以在这个循环中对每个值进行处理，并将其添加到新的对象中。for...in 循环：简单直观，适合大多数情况。
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
2. Object.keys()和reduce
**Object.keys** 方法可以获取对象的所有可枚举的自身属性名，并返回一个数组。然后可以使用 **Array.prototype.reduce** 方法来处理这些值并构建新的对象。Object.keys 和 reduce：功能强大，适合需要更复杂的处理逻辑。
```js
const obj = { a: 1, b: 2, c: 3 };

function processValues(obj, processor) {
  return Object.keys(obj).reduce((result, key) => {
    result[key] = processor(obj[key]);
    return result;
  }, {});
}

// 示例处理器函数：将每个值乘以2
const processedObj = processValues(obj, value => value * 2);
console.log(processedObj); // 输出: { a: 2, b: 4, c: 6 }

```

3. 使用 Object.entries 和 map
**Object.entries** 方法可以获取对象的所有可枚举的自身属性的键值对数组。然后可以使用 **Array.prototype.map** 方法来处理这些键值对，并使用 **Object.fromEntries** 将其转换回对象。Object.entries 和 map：简洁易读，适合简单的键值对处理。
```js
const obj = { a: 1, b: 2, c: 3 };

function processValues(obj, processor) {
  return Object.fromEntries(
    Object.entries(obj).map(([key, value]) => [key, processor(value)])
  );
}

// 示例处理器函数：将每个值乘以2
const processedObj = processValues(obj, value => value * 2);
console.log(processedObj); // 输出: { a: 2, b: 4, c: 6 }

```

4.使用 Reflect.ownKeys 和 reduce
如果你需要处理包括不可枚举属性在内的所有自身属性，可以使用 **Reflect.ownKeys** 方法。然后可以使用 **Array.prototype.reduce** 方法来处理这些值并构建新的对象。 Reflect.ownKeys 和 reduce：处理包括不可枚举属性在内的所有自身属性。
```js
const obj = { a: 1, b: 2, c: 3 };
Object.defineProperty(obj, 'd', { value: 4, enumerable: false });

function processValues(obj, processor) {
  return Reflect.ownKeys(obj).reduce((result, key) => {
    result[key] = processor(obj[key]);
    return result;
  }, {});
}

// 示例处理器函数：将每个值乘以2
const processedObj = processValues(obj, value => value * 2);
console.log(processedObj); // 输出: { a: 2, b: 4, c: 6, d: 8 }

```