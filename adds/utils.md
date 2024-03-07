# utils

## 1.orderId随机15位订单号

```js

let pad2 = (n)=> { return n < 10 ? '0' + n : n }

let generateTimeReqestNumber = () => {
let date = new Date();
return date.getFullYear().toString() + 
pad2(date.getMonth() + 1) + 
pad2(date.getDate()) + 
pad2(date.getHours()) + 
pad2(date.getMinutes())+
pad2(date.getSeconds())
}

let getId = () => {
    return generateTimeReqestNumber() + (Math.floor(Math.random() * 900) + 100)
}


module.exports = getId
```

## 2.生成4位随机验证码

```js
export default {
  smtp:{
    get host(){
      return 'smtp.qq.com'
    },
    get user(){
      return '**@qq.com'
    },
    get pass(){
      return ''
    },
    get code(){
      return ()=>{
        return Math.random().toString(16).slice(2,6).toUpperCase()
      }
    },
    get expire(){
      return ()=>{
        return new Date().getTime()+60*60*1000
      }
    }
  }    
}
```

## 3.使用nodemailer发送验证码邮件

**使用自己的QQ邮箱发送邮件验证码给客户使用**

1.  登陆QQ邮箱--点击设置--选择账户--在POP3/IMAP/SMTP/Exchange/CardDAV/CalDAV服务 选项中将
    POP3/SMTP服务、IMAP/SMTP服务设置为开启状态，记下生成的密钥
2.  在server/dbs/config.js文件中定义smtp邮件服务协议的配置

```js
export default {
  get dbs() {
    return 'mongodb://uniguest:isguest01&&@localhost:27010/ywflag'  //mongodb 地址和账号密码
  },
  redis: {
    get host() {
      return 'localhost'
    },
    get port() {
      return '6379'
    }
  },
  //smtp服务器配置信息
  smtp: {
    get host() {
      return 'smtp.qq.com'
    },
    get user() {
      return '1761881729@qq.com'
    },
    //smtp服务器设置时生成的密钥
    get pass() { 
      return 'ivcjyacweflsbcec' || 'iyxqdulyvevlccei'
    },
    //获取4位验证码函数
    get code() {
      return () => {
        return Math.random().toString(16).substring(2, 6).toUpperCase()
      }
    },
     //获取过期时间
    get exprise() {
      return () => {
        return new Date().getTime() + 1000 * 60 * 60
      }
    }
  }
}

```

1.  在后端接口文件中使用发送邮件interface/users.js

```js
//引入邮件发送模块
import nodeMailer from 'nodemailer'
//引入smtp邮件服务配置信息
import Email from '../dbs/config' 

router.post('/verify', async (ctx, next) => {
  let username = ctx.request.body.username
  const saveExpire = await Store.hget(`nodemail:${username}`, 'expire')
  if (saveExpire && new Date().getTime() - saveExpire < 0) {
    ctx.body = {
      code: -1,
      msg: '验证请求过于频繁，1分钟内1次'
    }
    return false
  }
  let transporter = nodeMailer.createTransport({
    service: 'qq',
    auth: {
      user: Email.smtp.user,
      pass: Email.smtp.pass
    }
  })
  let ko = {
    code: Email.smtp.code(),
    expire: Email.smtp.expire(),
    email: ctx.request.body.email,
    user: ctx.request.body.username
  }
  let mailOptions = {
    from: `"认证邮件" <${Email.smtp.user}>`,
    to: ko.email,
    subject: '《慕课网高仿美团网全栈实战》注册码',
    html: `您在《慕课网高仿美团网全栈实战》课程中注册，您的邀请码是${ko.code}`
  }
  await transporter.sendMail(mailOptions, (error, info) => {
    if (error) {
      return console.log(error)
    } else {
      Store.hmset(`nodemail:${ko.user}`, 'code', ko.code, 'expire', ko.expire, 'email', ko.email)
    }
  })
  ctx.body = {
    code: 0,
    msg: '验证码已发送，可能会有延时，有效期1分钟'
  }
})
```

***

# config.js 配置文件

## 1.dbs目录下配置mongodb和smtp服务器的配置信息

```js
export default {
  get dbs() {
    return 'mongodb://uniguest:isguest01&&@localhost:27010/ywflag'
  },
  redis: {
    get host() {
      return 'localhost'
    },
    get port() {
      return '6379'
    }
  },
  smtp: {
    get host() {
      return 'smtp.qq.com'
    },
    get user() {
      return '1761881729@qq.com'
    },
    get pass() {
      return 'ivcjyacweflsbcec' || 'iyxqdulyvevlccei'
    },
    get code() {
      return () => {
        return Math.random().toString(16).substring(1, 6).toUpperCase()
      }
    },
    get exprise() {
      return () => {
        return new Date().getTime() + 1000 * 60 * 60
      }
    }
  }
}
```

## 2.axios实例配置文件

通过创建axios实例，传入自定义参数实现通过识别环境变量来使用不同的baseURL

```js
import axios from 'axios'
//1.创建axios实例,设置baseURL有环境变量时取环境变量的HOST值、PORT值，环境变量没有值时取本机主机名和端口号
//2.设置baseURL后axios发起请求时baseURL的值会自动添加到axios.post('url',{})的参数url前面
//3.设置超时时间2000ms,自定义头信息为空
const instance = axios.create({
  baseURL:`http://${process.env.HOST||'localhost'}:${process.env.PORT||3000}`,
  timeout:2000,
  headers:{}
})
export default instance
```

## 3.通过自定义配置文件，手动切换不同环境下的URL

```js
//Mock环境
let hostMock = 'http://rap2api.taobao.org/app/mock/95149'
//开发环境
let host = 'http://localhost:8080'
//开发dist环境
let hostsDevOnline = 'http://localhost:3000'
//生产环境
let hostProduct = 'http://39.104.64.68:3000'

let url = {
  signin: '/signin',
  authuser: '/authuser',
  userinfo: '/userinfo',
  searchyw: '/ywinfo',
  changepwd: '/changepwd',
  logout: '/logout',
  getUserInfo: '/getUserInfo',
  createPlan: '/plan/createPlan',
  getPlanList: '/plan/getPlanList',
  getPlanItems: '/plan/getPlanItems',
  createCustomer: '/customer/createCustomer',
  getCustomerList: '/customer/getCustomerList',
  getCustomerTel: '/customer/getCustomerTel'
}

for (let key in url) {
  if (url.hasOwnProperty(key)) {
    url[key] = host + url[key]
  }
}

export default url
```

***

## 4.日期处理

```javascript
// 检测给出的日期是否有效
const isDateValid = (...val) => !Number.isNaN(new Date(...val).valueOf());
isDateValid("December 17, 1995 03:24:00");  // true

//计算两个日期之间的间隔时间
const dayDif = (date1, date2) => Math.ceil(Math.abs(date1.getTime() - date2.getTime()) / 86400000)
dayDif(new Date("2021-11-3"), new Date("2022-2-1"))  // 90

//查找日期位于一年中的第几天
const dayOfYear = (date) => Math.floor((date - new Date(date.getFullYear(), 0, 0)) / 1000 / 60 / 60 / 24);
dayOfYear(new Date());   // 307

//时间格式化，将时间转化为hour:minutes:seconds的格式
const timeFromDate = date => date.toTimeString().slice(0, 8);
timeFromDate(new Date(2021, 11, 2, 12, 30, 0));  // 12:30:00
timeFromDate(new Date());  // 返回当前时间 09:00:00
```

## 5.字符串处理

```javascript
//将英文字符串的首字母大写处理
const capitalize = str => str.charAt(0).toUpperCase() + str.slice(1)
capitalize("hello world")  // Hello world

//将一个字符串进行翻转操作，返回翻转后的字符串
const reverse = str => str.split('').reverse().join('');
reverse('hello world');   // 'dlrow olleh'

//生成一个随机的字符串
const randomString = () => Math.random().toString(36).slice(2);
randomString();

//从指定长度处截断字符串
const truncateString = (string, length) => string.length < length ? string : `${string.slice(0, length - 3)}...`;
truncateString('Hi, I should be truncated because I am too loooong!', 36)   // 'Hi, I should be truncated because...'

//去除字符串中的HTML元素
const stripHtml = html => (new DOMParser().parseFromString(html, 'text/html')).body.textContent || '';
```

## 6.数组处理

```javascript
//移除数组中的重复项
const removeDuplicates = (arr) => [...new Set(arr)];
console.log(removeDuplicates([1, 2, 2, 3, 3, 4, 4, 5, 5, 6]));

//判断数组是否为空
const isNotEmpty = arr => Array.isArray(arr) && arr.length > 0;
isNotEmpty([1, 2, 3]);  // true

//合并两个数组
const merge = (a, b) => a.concat(b);
const merge = (a, b) => [...a, ...b];
```

## 7.数字操作

```javascript
//判断一个数字是奇数还是偶数
const isEven = num => num % 2 === 0;
isEven(996);

//获得一组数的平均值
const average = (...args) => args.reduce((a, b) => a + b) / args.length;
average(1, 2, 3, 4, 5);   // 3

//获取两个整数之间的随机整数
const random = (min, max) => Math.floor(Math.random() * (max - min + 1) + min);
random(1, 50);

//指定位数四舍五入
const round = (n, d) => Number(Math.round(n + "e" + d) + "e-" + d)
round(1.005, 2) //1.01
round(1.555, 2) //1.56
```

## 8.颜色操作

```javascript
//将RGB转化为十六机制
const rgbToHex = (r, g, b) => "#" + ((1 << 24) + (r << 16) + (g << 8) + b).toString(16).slice(1);
rgbToHex(255, 255, 255);  // '#ffffff'

//获取随机十六进制颜色
const randomHex = () => `#${Math.floor(Math.random() * 0xffffff).toString(16).padEnd(6, "0")}`;
randomHex();
```

## 9.浏览器操作

```javascript
//使用navigator.clipboard.writeText 来实现将文本复制到剪贴板
const copyToClipboard = (text) => navigator.clipboard.writeText(text);
copyToClipboard("Hello World");

//使用 document.cookie 来访问 cookie 并清除存储在网页中的所有 cookie
const clearCookies = document.cookie.split(';').forEach(cookie => document.cookie = cookie.replace(/^ +/, '').replace(/=.*/, `=;expires=${new Date(0).toUTCString()};path=/`));

//通过内置的 getSelection 属性获取用户选择的文本
const getSelectedText = () => window.getSelection().toString();
getSelectedText();

//检测当前的环境是否是黑暗模式，它是一个布尔值
const isDarkMode = window.matchMedia && window.matchMedia('(prefers-color-scheme: dark)').matches
console.log(isDarkMode)

//在页面中滚动返回顶部
const goToTop = () => window.scrollTo(0, 0);
goToTop();

//检测当前标签页是否已经激活
const isTabInView = () => !document.hidden; 

//判断当前是否是苹果设备
const isAppleDevice = () => /Mac|iPod|iPhone|iPad/.test(navigator.platform);
isAppleDevice();

//于判断页面是否已经底部
const scrolledToBottom = () => document.documentElement.clientHeight + window.scrollY >= document.documentElement.scrollHeight;

//重定向到一个新的URL
const redirect = url => location.href = url
redirect("https://www.google.com/")

//打开浏览器的打印框
const showPrintDialog = () => window.print()
```

## 10.其他操作

```javascript
//返回一个随机的布尔值，使用Math.random()可以获得0-1的随机数，与0.5进行比较，就有一半的概率获得真值或者假值。
const randomBoolean = () => Math.random() >= 0.5;
randomBoolean();

//变量交换，使用以下形式在不使用第三个变量的情况下，交换两个变量的值
[foo, bar] = [bar, foo];

//获取一个变量的类型
const trueTypeOf = (obj) => Object.prototype.toString.call(obj).slice(8, -1).toLowerCase();
trueTypeOf('');     // string
trueTypeOf(0);      // number
trueTypeOf();       // undefined
trueTypeOf(null);   // null
trueTypeOf({});     // object
trueTypeOf([]);     // array
trueTypeOf(0);      // number
trueTypeOf(() => {});  // function

//华氏度和摄氏度之间的转化
const celsiusToFahrenheit = (celsius) => celsius * 9/5 + 32;
const fahrenheitToCelsius = (fahrenheit) => (fahrenheit - 32) * 5/9;
celsiusToFahrenheit(15);    // 59
celsiusToFahrenheit(0);     // 32
celsiusToFahrenheit(-20);   // -4
fahrenheitToCelsius(59);    // 15
fahrenheitToCelsius(32);    // 0

//检测一个JavaScript对象是否为空
const isEmpty = obj => Reflect.ownKeys(obj).length === 0 && obj.constructor === Object;
```

