# Vue.js 插件
## 1. vue loadmore组件使用
1. 安装vue-infinite-loading插件
   
   `cnpm install --save vue-infinite-loading`
2. vue组件页面中引入插件后注册组件
   
   `import InfiniteLoading from 'vue-infinite-loading';`

   ```js
   components:{
        NavHeader,
        NavFooter,
        NavBread,
        InfiniteLoading
    }
   ```

3. vue组件templet标签中使用组件
   ```html
   <infinite-loading @infinite="infiniteHandler" ref="infiniteLoading">
        <span slot="no-more">
            没有更多数据了...
        </span>
    </infinite-loading>
   ```
   **【1】infinite-loding组件要紧跟在加载的列表元素li后面**

   **【2】监听infinite 事件，执行infiniteHandler回调函数**

   **【3】slot插槽中的内容是数据加载到最后一页的提示信息**

4. infiniteHandler回调函数逻辑
   ```js
        infiniteHandler($state) {
            setTimeout(() => {
                //3.如果后端获取的数据个数小于pageSize表示数据获取到最后一页了，滚动加载停止
                if(this.goodscount < this.getParam.pageSize){
                    $state.complete();
                }else{
                //3.1如果后端获取的数据个数等于8,则继续执行获取函数继续滚动加载
                    $state.loaded();
                    this.getGoodsList(true)
                }
            }, 1000);
        }
   ```

   **【1】 setTimeout延迟加载时间**
   
   **【2】 $state.complete()是满足某条件后停止加载**
   
   **【3】 $state.loaded();是继续执行加载** 

5. $state.complete()加载完成后，在其他逻辑中需要**重新**触发滚动加载时使用
   ```js
        this.$nextTick(() => {
            this.$refs.infiniteLoading.$emit('$InfiniteLoading:reset');
        });
   ```
6. 代码示例见vue shop项目
## 2.currency过滤器
1. 定义currency模块文件
```js
const digitsRE = /(\d{3})(?=\d)/g

export function currency (value, currency, decimals) {
  value = parseFloat(value)
  if (!isFinite(value) || (!value && value !== 0)) return ''
  currency = currency != null ? currency : '$'
  decimals = decimals != null ? decimals : 2
  var stringified = Math.abs(value).toFixed(decimals)
  var _int = decimals
    ? stringified.slice(0, -1 - decimals)
    : stringified
  var i = _int.length % 3
  var head = i > 0
    ? (_int.slice(0, i) + (_int.length > 3 ? ',' : ''))
    : ''
  var _float = decimals
    ? stringified.slice(-1 - decimals)
    : ''
  var sign = value < 0 ? '-' : ''
  return sign + currency + head +
    _int.slice(i).replace(digitsRE, '$1,') +
    _float
}
````
2. 在vue main.js文件中注册全局过滤器或者在vue组件文件中单独注册局部过滤器
```js
import {currency} from './utils/currency'


Vue.filter("currency",currency)
// 在new Vue前执行filter注册
new Vue({
  el: '#app',
  router,
})
```

3. 在页面中使用过滤器
```html
<div class="cart-tab-2">
    <div class="item-price">{{item.salePrice | currency('￥')}}</div>
</div>
```


## 3.图片懒加载插件
vue-lazyload
```js
cnpm install vue-lazyload ---save
import vueLazyLoad from 'vue-lazyload' 
Vue.use(vueLazyLoad,{
loading:"/static/loading/loading001.svg" //loading状态的图片
})
```
需要懒加载的图片将原来的
```js
<img src="/static/img/001.jpg">
//改成
<img v-lazy="/static/img/001.jpg">
```
这样图片加载之前执行
`Vue.use(vueLazyLoad,{loading:"/static/loading/loading001.svg"})`配置中的loading图片效果，在图片加载完成后将img标签中的v-lazy替换成真实图片路径

**滚动加载插件**

vue-infinite-scroll 
```js
cnpm install vue--infinite-scroll --save
import infinite-scroll from 'vue-infinite-scroll'
Vue.use(infiniteScroll)
```
1. 拷贝div标志元素，放置在动态加载元素的最下面，用来显示加载中......
2. 定义div标志元素中的busy数据，和loadmore方法，注意设置setTimeout选项，避免鼠标scroll滚动时发送多次请求。


## 4.axios
1. **axios get请求**
```js
axios.get(url).then(res=>{
this.pdlist=res.data.array
}).catch(error=>{
console.log(error)
})

axios.get('user', {
    params: { //get方法中，params选项是get请求传递查询字符串的参数user?ID:12345，不能不写
      ID: 12345
    },
headers:{ //请求头中添加内容
token:"jack"
}
  }).then(function (response) {
    console.log(response);
  })
  .catch(function (error) {
    console.log(error);
  });
```

  

2. **axios post请求**
```js
axios.post('user',{ //post请求传递数据直接写或者在配置项中的data属性中写
firstName:'abc'
}, {
headers:{token:"jack"}
} ).then(res=>{
this.data.pdlist=res.data.array
}).catch(error=>{
console.log(error)
})
地址为user
查询参数为firstName:'abc'
```

3. **axios并发请求**
```js
let get1 = () => {
return axios.post('url1',[config])
}

let get2 = () => {
return axios.get('url2',[config])
}
axios.all([get1(),get2()]).then(callback).catch(callback2)
```

4. **创建一个axios的实例来添加自定义的配置，==相当于axios的自定义全局配置==**
```js
//axios.create([config])
axios.create({
    baseURL:'https://some-domain.com/api/',
    timeout:'1000'
    headers:{}
})

//自定义axios实例的方法
axios#request(config)
axios#get(url[, config])
axios#delete(url[, config])
axios#head(url[, config])
axios#post(url[, data[, config]])
axios#put(url[, data[, config]])
axios#patch(url[, data[, config]])

//全局axios请求和axios实例的配置config
axios.request(config)
axios.get(url[, config])
axios.post(url[, data[, config]])
axios.delete(url[, config])
axios.head(url[, config])
axios.put(url[, data[, config]])
axios.patch(url[, data[, config]])

{
  // `baseURL` 将自动加在 `url` 前面，除非 `url` 是一个绝对 URL。
  // 它可以通过设置一个 `baseURL` 便于为 axios 实例的方法传递相对 URL
  baseURL: 'https://some-domain.com/api/',
  timeout: 1000,
  // `withCredentials` 表示跨域请求时是否需要使用凭证
  withCredentials: false, // 默认的
}
```

5. **axios配置的默认值/defaults**

在通过import  axios from 'axios'引入全局的axios后，或者引入axios的实例后，可以添加额外的默认配置信息。
```js
//添加全局axios的默认配置
axios.defaults.baseURL = 'https://api.example.com';
axios.defaults.headers.common['Authorization'] = AUTH_TOKEN;
axios.defaults.headers.post['Content-Type'] = 'application/x-www-form-urlencoded';


// 创建axios实例时设置配置的默认值
var instance = axios.create({
  baseURL: 'https://api.example.com'
});
// 在axios实例已创建后修改默认值
instance.defaults.headers.common['Authorization'] = AUTH_TOKEN;
```

**全局axios的config配置和自定义axios实例的config配置的优先顺序**
在 lib/defaults.js 找到的库的默认值（全局axios的config），然后是实例的 defaults 属性（自定义axios实例的config），最后是请求的 config 参数（执行请求时传递的config）。后者将优先于前者。

6. **axios 请求响应拦截器**
在引入全局axios或者创建axios实例的时候定义全局拦截器，拦截器在then或catch前执行。创建拦截器后，==所有get,post,put,delete请求的操作都要先经过拦截器，执行拦截器中的逻辑后再发起请求。== 一般拦截器在mounted生命周期中定义。
```js
import axios from 'axios'
//添加一个请求拦截器
axios.interceptors.request.use(function(request){
  //dosomethings 在所有axios请求发出之前进行一些操作
  return request;
},function(err){
  //Do something with request error
  return Promise.reject(error);
});

//添加一个响应拦截器
axios.interceptors.response.use(function(response){
  //dosomethings 在这里对所有axios返回响应前执行一些操作
  if(response.data.status === 200) {
      return response
  } else if(response.data.status === 1008) {
      return Promise.reject(response.data.msg)
  }
},function(err){
  //Do something with response error
  return Promise.reject(error);
})

//创建实例并添加拦截器
var instance = axios.create([config])
instance.interceptors.request.user(function(){})

//取消拦截器
var myInterceptors = axios.interceptors.request(fucntion(){})
axios.interceptors.request.eject(myInterceptors)
```
axios如果是get请求，则后端通过req.param('name')中拿到查询字符串，也可还以通过req.query.name拿到查询字符串。
如果是post请求，则后端通过req.body拿到查询字符串


7. **axios  promise  async/await** 
```js
//promise
this.$axios.post('/users/login',{username,password}).then(({status, data}) => {
    if (status === 200) {
        if (data && data.code === 0) {
            this.name = data.name
        } else {
            this.err = data.msg
        }
    } else {
        this.err = `服务器错误${status}`
    }
})

//async await
const {status, data} = await this.$axios.post('/users/login',{username,password})
if (status === 200) {
    if (data && data.code === 0) {
        this.name = data.name
    } else {
        this.err = data.msg
    }
} else {
    this.err = `服务器错误${status}`
} 
```

8. **axios 发送basic-auth验证请求** 
```js
10. const axios = require('axios')

var session_url = 'http://localhost:3000/v1/classic/latest';
var uname = 'eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1aWQiOjUsInNjb3BlIjo4LCJpYXQiOjE1NzMyMjI4NTcsImV4cCI6MTU3MzI1Mjg1N30.SFETHfKFiyrImlX-MeRl9w_zxp7Q-n7Rz9oTnKNEUuU';
var pass = '';
axios.get(session_url, {
  auth: {
    username: uname,
    password: pass
  }
}).then(response=>console.log(response))
.catch(error=>console.log(error));

// 验证请求
class Auth {
    constructor(level = 1) {
        this.level = level
    }
    m() {
        return async(ctx, next) => {
            const userToken = basicAuth(ctx.req)
            if (!userToken || !userToken.name) {
                throw new global.errs.Forbidden('token信息不合法')
            }
            let decoded = await tokenUtils.tokenVerify(userToken.name, this.level)
            ctx.auth = {
                uid: decoded.uid,
                scope: decoded.scope
            }
            await next()
        }
    }
}
```

9. ** 完整的axios全局拦截器实例 ** 
```js
 // axios 全局拦截器
import axios from 'axios'
import { MessageBox, Message } from 'element-ui'
import store from '@/store'
import {
  getAccessToken,
  getRefreshToken,
  setAccessToken,
  setRefreshToken
} from '@/utils/auth'
import { _encode } from './encode-token'
import { tokenRefresh } from '../api/user'

// create an axios instance
const service = axios.create({
  baseURL: process.env.VUE_APP_BASE_API, // url = base url + request url
  // withCredentials: true, // send cookies when cross-domain requests
  timeout: 5000 // request timeout
})

service.interceptors.request.use(
  (config) => {
    // do something before request is sent
    if (store.getters.accessToken) {
      config.headers['Authorization'] = _encode(getAccessToken())
    }
    return config
  },  (error) => {
    // do something with request error
    console.log(error) // for debug 
    return Promise.reject(error)
  }
)

service.interceptors.response.use(
  (response) => {
    const res = response.data
    
    if (![200, 201, 202, 204].includes(response.status)) {
      Message({
        message: response.msg || 'Error',
        type: 'error',
        duration: 5 * 1000
      })
      
      // return Promise.reject(new Error(res.message || 'Error'))
      return Promise.reject(new Error(response.msg || 'Error'))
    } else {
      const {
        data: { error_code },
        status
      } = response

      // 成功提示消息
      if (error_code === 0 && [200, 201].includes(status)) {
        Message({
          message: response.data.msg || '操作成功',
          type: 'success',
          duration: 5 * 1000
        })
      } else if (error_code === 0 && status === 202) {
        // 更新提示消息
        Message({
          message: response.data.msg || '数据已更新',
          type: 'warning',
          duration: 5 * 1000
        })
      }

      return res
    }
  },
  (error) => {
    console.log('err' + error) // for debug

    // accessToken过期后通过refreshToken拉取新的accessToken和refreshToken
    if (
      error.request.status === 401 &&
      JSON.parse(error.request.response).error_code === 50000
    ) {
      tokenRefresh(_encode(getRefreshToken()))
        .then((response) => {
          const {
            accessToken = undefined,
            refreshToken = undefined
          } = response.data
          setAccessToken(accessToken)
          setRefreshToken(refreshToken)
          location.reload()
        })
        .catch((error) => {
          // refreshToken过期，通过上面tokenRefresh抛出错误来识别，弹出重登陆框
          if (
            error.request.status === 401 &&
            JSON.parse(error.request.response).error_code === 55000
          ) {
            MessageBox.confirm('已登出, 请刷新页面或退出再登陆', '超时', {
              confirmButtonText: '登陆',
              cancelButtonText: '取消',
              type: 'warning'
            }).then(() => {
              store.dispatch('user/resetToken').then(() => {
                location.reload()
              })
            })
          }
        })
    } else {
      const message = JSON.parse(error.request.response).msg
      Message({
        message: message,
        type: 'error',
        duration: 5 * 1000
      })
      return Promise.reject(error)
    }
  }
)

export default servic
```


## 5.module alias别名模块
```js
cnpm install module-alias
// 1.在项目的package.json配置文件中增加如下配置信息, 别名路径的参考起始目录是package.json所在的文件夹
"_moduleAliases": {
    "@root": ".",
    "@models": "app/models"
}
// 2.app.js文件中引入别名模块
require('module-alias/register')
// 3.使用别名模块简化路径的输入
const { Favor } = require('../../models/favor')
const { Favor } = require('@models/favor')

```

## 6.koa2-cors实现跨域
实现跨域ajax请求的方式有很多，其中一个是利用CORS，而这个方法关键是在服务器端进行配置。
CORS将请求分为简单请求和非简单请求，可以简单的认为，

**简单请求**：就是没有加上额外请求头部的get和post请求，并且如果是post请求，请求格式不能是application/json。

**非简单请求**：put请求,post请求, 以及带有自定义请求头的请求，Content-Type为application/json的请求就是非简单请求。如果设置`ctx.set('Access-Control-Allow-Origin', 'http://localhost:9527')`后,这时候可以发起简单请求。发送非简单请求还需要其他配置，**允许OPTIONS方法**，开启该方法以后，当第一次发出非简单请求的时候，实际上会**发出两个请求**，**第一次发出**的是preflight request，这个请求的请求方法是**OPTIONS**，这个请求是否通过决定了这一个种类的非简单请求是否能成功得到响应。

`Access-Control-Max-Age: 86400`

这个响应头的意思是，设置一个相对时间，当一次非简单请求在服务器端通过检验后，在Access-Control-Max-Age设置的时间范围内，后续的非简单就不需要再进行预检，可以直接发送一次请求。
此设置对请求为简单请求的不生效。
```js
//app.js   koa后端的入口文件
const cors = require('koa2-cors')

const app = new Koa()
app.use(cors({
    origin: function (ctx) {
        // if (ctx.url === '/test') {
        //     return "*"; // 允许来自所有域名请求
        // }
        //这里只允许本机9528端口向koa运行的http://127.0.0.1:3000发起请求
        return 'http://localhost:9528'; 
    },
    exposeHeaders: ['WWW-Authenticate', 'Server-Authorization'],
    maxAge: 5, //发起特殊请求前先发起options预请求的间隔时间，小于该时间则不用发起options预请求
    //如果跨域访问时request header中需要传递cookie信息，则服务器端需开启credentials: true
    credentials: true,
    // 必须允许OPTIONS方法，才能发起预请求，才能实现跨域
    allowMethods: ['GET', 'POST', 'DELETE', 'OPTIONS'],
    // 允许Authorzation后才能使用basicAuth。 x-token是自定义的一个头名称
    allowHeaders: ['Content-Type', 'Authorization', 'Accept', 'x-token']
}))
```

## 7.Basic Auth增加token信息
**Http Basic Auth传递令牌信息**

在HTTP中，基本认证（Basic access authentication）是一种用来允许网页浏览器或其他客户端程序在请求时提供用户名和口令形式的身份凭证的一种登录验证方式。在发送之前是以用户名追加一个冒号然后串接上口令，并将得出的结果字符串再用Base64算法编码。例如，提供的用户名是Aladdin、口令是open sesame，则拼接后的结果就是Aladdin:open sesame，然后再将其用Base64编码，得到QWxhZGRpbjpvcGVuIHNlc2FtZQ==，**放在请求的 Header 中**，最终将Base64编码的字符串发送出去，由接收者解码得到一个由冒号分隔的用户名和口令的字符串。==好处是不会直接看到账号密码，但是本质还是明文传输账号、密码，所以如果不是 HTTPS 会很容易被监听到，Http Basic Auth主要是为了解决账号、密码中可能存在的编码问题。编码这一步骤的目的并不是安全与隐私，而是***为将用户名和口令中的不兼容的字符转换为均与HTTP协议兼容的字符集**。== 缺点：HTTP没有为服务器提供一种方法指示客户端丢弃这些被缓存的密钥。这意味着服务器端在用户不关闭浏览器的情况下，并没有一种有效的方法来让用户退出。基于Http Basic Auth机制可以将token信息放入name中，pass为空，这样就能传递token信息了。

**后端解析Http Basic Auth发送的信息需要用到'basic-auth'库**
```js
const basicAuth = require('basic-auth')
const token = basicAuth(ctx.req)  //自动从ctx.req对象上获取到token信息
```
后端还必须设置allow-headers允许Authorization方法
`Access-Control-Allow-Headers: Authorization`

**http请求头中基于basicAuth的token格式如下**
```js
headers: {
    'Authorization': 'Basic ' + btoa(username + ":" + password) //注意Basic后有空格
 }
 /*其中btoa是base64的转换方法*/
 /*成功后在浏览器的Request Header里会出现*/
 Authorization： BasicYWRtaW46T25lVGlubWFuT25lRHJlYW02Ng==
```

**1.在postman调试环境中使用basicAuth发送token**

将token信息填入到Username选项中

**2.在实际生产环境中设置http request header添加基于basicAuth的token信息**
```js
// encode-token.js 按basicAuth的规则拼接token信息
import { Base64 } from 'js-base64'
const _encode = (token) => {
  const result = Base64.encode(token + ':')
  // 格式：Authorization: Basic $(base64_encode({username}:{password}))
  return 'Basic ' + result
}
export {
  _encode
}

// 设置http-request header 在http请求头中添加basicAuth信息
import { _encode } from './encode-token'
header: {
   'content-type': 'application/json',
   Authorization:  _encode(getAccessToken())
}
```




---
# ui组件
## 1.使用vant ui引入自定义字体
1. 下载自定义字体（.ttf）格式
2. 将下载的字体拷贝到node_modules/vant/lib/icon目录下
3. 编辑项目中node_modules/vant/lib/vant-css/icon-local.css文件,
示例：引入了两种字体，名称为vant-icon和kso,引入多种字体时@font-face语句不加分号
```css
@font-face{font-style:normal;font-weight:400;font-family:vant-icon;src:url(../icon/RuiZiZhenYanTiMianFeiShangYong-2.ttf) format('truetype')}
@font-face{font-style:normal;font-weight:400;font-family:kso;src:url(../icon/MoMoSuiXiangTiXi-2.ttf) format('truetype')}
```
4. vue组件页面中引入css模块
```js
import 'vant/lib/vant-css/icon-local.css';
```
5. 在style 样式中使用字体font-family:vant-icon

## 2.vant ui中tabBar引入自定义图标
1. 下载png格式的图标文件
2. 在vue组件文件内引入图标文件
```js
import img1 from '../assets/iconpng/icon-contact.png';
import img2 from '../assets/iconpng/icon-contact_1.png';
```
3. 在vue组件内引入自定义tabBar组件
```js
import { Tabbar, TabbarItem,Icon } from 'vant';
Vue.use(Tabbar).use(TabbarItem).use(Icon);
```
4. template标签中使用自定义tabBar组件
**注意：props.active判断索引号的三元运算符**，组件已自动设置好可直接使用，不用手动判断active的索引号是否是当前点击的图标。
```html
<van-tabbar v-model="active">
        <van-tabbar-item info="3">
            <span>自定义</span>
            <img
            slot="icon"
            slot-scope="props"
            :src="props.active ? icon.img1 : icon.img2"
            >
        </van-tabbar-item>
        <van-tabbar-item icon="chat">标签</van-tabbar-item>
        <van-tabbar-item icon="records">标签</van-tabbar-item>
    </van-tabbar>
```

## 3.vant ui中自定义全局样式和主题
```js
git clone git@github.com:youzan/vant.git
cd packages/vant-css
```
1. 在本地 vant-css 仓库中，修改 src/common/var.css 中的颜色变量，然后执行以下构建命令，即可生成对应的样式文件
2. 在vant/packages/vant-css目录下执行`npm run build` 构建名称，在当前目录下会生成一个lib文件夹，lib文件夹下的css文件即修改后的样式。全选复制
3. 进入项目目录下的/node_modules/vant/lib/vant-css/ 粘贴替换当前默认的样式文件
4. 在项目vue文件中加载组件，使用组件，引入组件对应的自定义样式文件，比如
```js
import '../../node_modules/vant/lib/vant-css/nav-bar.css'
```


## 4. element-ui form表单组件账号密码邮箱的校验及参数绑定
```html
<el-form
        ref="ruleForm"
        :model="ruleForm"
        :rules="rules"
        label-width="100px"
        class="demo-ruleForm"
      >
        <el-form-item label="昵称" prop="name">
          <el-input v-model="ruleForm.name" />
        </el-form-item>
        
        <el-form-item label="邮箱" prop="email">
          <el-input v-model="ruleForm.email" />
        <el-form-item>
        
        <el-form-item label="密码" prop="pwd">
          <el-input v-model="ruleForm.pwd" type="password" autocomplete="off" />
        </el-form-item>
        
        <el-form-item label="确认密码" prop="cpwd">
          <el-input v-model="ruleForm.cpwd" type="password" autocomplete="off" />
        </el-form-item>
<el-form>
```
```js
export default {
  layout: 'blank',
  data() {
    return {
      ruleForm: {
        name: '',
        pwd: '',
        cpwd: '',
        age: ''
      },
      statusMsg: '',
      error: '',
      rules: {
        name: [{
          required: true,
          type: 'string',
          message: '请输入昵称',
          trigger: 'blur'
        }],
        email: [{
          required: true,
          type: 'email',
          message: '请输入邮箱地址',
          trigger: 'blur'
        }],
        pwd: [{
          required: true,
          message: '创建密码',
          trigger: 'blur'
        }],
        cpwd: [{
          required: true,
          message: '确认密码',
          trigger: 'blur'
        }, {
          validator: (rule, value, callback) => {
            if (value === '') {
              callback(new Error('请再次输入密码'))
            } else if (value !== this.ruleForm.pwd) {
              callback(new Error('两次输入密码不一致'))
            } else {
              callback()
            }
          },
          trigger: 'blur'
        }]
      }
    }
  }
}
```
1. 表单包裹元素el-form标签的属性必须声明绑定form表单的数据对象:model="ruleForm"、ref="ruleForm"，绑定校验规则:rules="rules"
2. el-form-item标签是表单组件的表单成员，el-form-item标签上是否绑定prop属性，以及表单的type=""属性需参考element-ui官方示例，但必须绑定v-model属性所对应的ruleForm对象上的属性。
3. 在data数据对象中描述rules的规则，required表示必须具有值，type属性在input是文本输入框是="string"，type属性在input输入框时email是="email",message属性是校验提示信息，**trigger属性是触发校验规则的事件**，validator属性对应的是自定义的校验函数.rules中每个属性的规则是用一个或者多个数组对象描述的，如果要添加属性的validator自定义校验函数则写为一个数组对象的成员，validator也能有自己的trigger事件。
4. validator 对应的自定义校验函数有三个参数
```js
validator: (rule, value, callback) => {
}
//rule是当前属性的自生的校验规则，value是当前属性的值，callback是校验提示时执行的函数
```

## 5. 自定义星星评分组件
+ 实现思路：上层空心图片，下层实心图片，实心图片使用绝对定位，控制实心图片显示为overflow:hidden;和实心图片相对于父元素的宽度来显示实心图片的大小
----
```html
<template>
    <div class="rate">
        <span>☆☆☆☆☆</span>
        <div class="hollow" :style="style">
            ★★★★★
        </div>
    </div>
</template>

<script>
    export default {
       props:{
           value:{type:[Number,String],default:'0'}
       },
       // 计算属性，返回可变 的style值
       computed: {
           style() {
               return `width:${this.value/2}em`
           }
       }
    }
</script>

<style lang="scss">
    .rate {
        position:relative;
        display:inline-block;
        .hollow {
            position:absolute;
            display:inline-block;
            top:0;
            left:0;
            width:0em;
            overflow:hidden;
        }
    }
</style>

//组件的调用,评分最高10分，有5个星星，所以每颗星星占两分。显示实心星星的时候通过传入不同的rate分值来控制
<rate :value="book.rate"></rate>
```

## 6. element-ui upload组件上传文件
```html
html文件
    <el-upload
      class="upload-content"
      drag
      :action="uploadUrl"
      :headers="uploadSetHeaders"
      :multiple="false"
      :on-success="uploadSuccess"
      :on-error="uploadError"
      :before-upload="uploadValidate"
      name="file"
    >
      <i class="el-icon-upload" />
      <div class="el-upload__text">
        将文件拖到此处，或
        <em>点击上传</em>
      </div>
      <div slot="tip" class="el-upload__tip">只能上传.xlsx/.docx类型文件,且大小不超过10Mb</div>
    </el-upload>
```
```js
.vue文件
/*
drag: 是否支持拖拽上传
action: 必须参数，文件上传地址
headers: 设置上传请求头 (obj)
multiple: 是否支持多选文件
on-success: 文件上传成功对应的回调
on-error: 文件上传失败对应的回调
before-upload: 文件上传之前的回调
name: 文件上传的字段名称,填写后后端才能通过这个name获取到上传的文件
*/
export default {
    name: 'Upload',
    data() {
        return {
          uploadUrl: `${process.env.VUE_APP_BASE_API}/thomas/uploadfile`,
          uploadSetHeaders: {
          // 设置header 放入BasicAuth token
            Authorization: _encode(getAccessToken())
          },
        }
    },
    methods: {
        uploadValidate(file) {
          const extension = file.name.split('.').slice(-1)[0]
          const extensionReg = new RegExp('(xlsx|docx)$', 'g')
          if (!extensionReg.test(extension)) {
            this.$message({
              message: `不支持上传 .${extension} 类型文件`,
              type: 'error'
            })
            return false
          }
          if (file.size > 10485760) {
            this.$message({
              message: `文件大小 ${(file.size / 1024 / 1024).toFixed(
                2
              )}MB 超出上限`,
              type: 'error'
            })
            return false
          }
        },
        uploadSuccess(res, file, filelise) {
          location.reload()
          this.$message({
            message: `${res.fileName} 导入成功,文件大小${res.fileSize}`,
            type: 'success'
          })
        },
        uploadError(err, file) {
          const message = JSON.parse(err['message'])['msg']
          this.$message({
            message: `${message}`,
            type: 'error'
          })
        },        
    }
}

后端http请求的API接口文件 /api/v/thomas.js
const Router = require('koa-router');
const multer = require('@koa/multer'); // 使用koa/multer来解析上传的form文件
const upload = multer();
let { ThomasService } = require('../../services/thomas');
let { UserModel } = require('../../models/user');
const router = new Router({
  prefix: '/v1/thomas',
});

// 这里的'file'就是前端上传组件中设置的name属性对应的值，前端必须设置，后端才能在
// ctx.request对象中通过前端设置的name值获取到上传的文件
router.post('/uploadfile', upload.single('file'), async (ctx, next) => {
  let { userId = undefined } = ctx.auth;
  let { nick_name = undefined } = await UserModel.findOne({
    where: {
      user_id: userId,
    },
  });
  let uploadTime = new Date().getTime();
  let { file } = ctx.request;
  Object.assign(file, { operateAuthor: nick_name, uploadTime: uploadTime });

  try {
    let { fileName, fileSize } = await new ThomasService(
      file
    ).writeFileStream();
    ctx.body = { fileName, fileSize };
  } catch (error) {
    let message = error.msg || error.message;
    throw new global.errs.HttpException(
      `${message} 导入文件失败,请重试`,
      10006
    );
  }
});

module.exports = {
  thomas: router,
};
```
## 8. 将数据导出为CSV格式下载
```html
html文件
<el-button
  type="primary"
  icon="el-icon-document"
  class="export-button"
  size="medium"
  @click.native="exportFile"
>导出CSV文件</el-button>
```
```js
import { getb2iserial, allocate, reject, remove } from '@/api/b2i2c'
import { exportCsv } from '@/utils/export-csv'
export default {
    name: 'b2iserial-rest',
    data() {
        return {
            
        }
    },
    methods: {
        async exportFile() {
          const { result = null } = await getb2iserial({
            offset: 0,
            limit: 10000
          })
          exportCsv(result)
      }
    }
}
```

```js
export-csv.js文件

// 仅适用于Array json格式的数据导出为csv格式
import NProgress from 'nprogress'
export function exportCsv(data) {
  NProgress.start()
// 添加\uFEFE即添加utf-8 Bom文件头，标识文件以utf-8编码方式编码
  let csvContent = 'data:text/csv;charset=utf-8,\uFEFF'
  
  // 在第一条数据中获取字段名称，生成表头，注意加换行转义符
  const dataKeys = Reflect.ownKeys(data[0])
  const tableHeader = dataKeys.join(',') + `\n`
  let str = tableHeader

  // 遍历每条数据
  data.map(e => {
    let keyStr = ''
    dataKeys.map(item => {
      //   str += `${e.serial_number},${e.product_name},${e.id_desc},${e.fee},${e.contact_phone},${e.dev_name},${e.dev_phone},${e.operate_time},${e.operate}\n`
      // 将每条数据的json对象的key对应的值value 拼接到一起,注意加逗号
      keyStr += `${e[item]},`
    })
    // 数据拼接完成后要加换行符
    str += keyStr + `\n`
  })
  // 编码UTF8
  str = encodeURIComponent(str)
  csvContent += str

  const fileName = `${(new Date().toJSON().substr(0, 10)).replace(/-/g, '')}${parseInt(Math.random() * 10000)}.csv`
  
  //创建a标签并模拟点击下载
  const link = document.createElement('a')
  link.setAttribute('href', csvContent)
  link.setAttribute('download', fileName)
  document.body.appendChild(link)

  link.click()

  NProgress.done()
}
```

## 9. vue页面监听刷新和跳转事件
```js
created() {
       window.addEventListener("onunload",()=>{
               this.saveArticalInfo()
      // })
       window.addEventListener("pagehide",()=>{
               this.saveArticalInfo()
       })    
}
// pageshow事件：这个事件在用户浏览网页时触发，pageshow 事件类似于 onload 事件，onload 事件在页面第一次加载时触发， pageshow 事件在每次加载页面时触发，即 onload 事件在页面从浏览器缓存中读取时不触发。
//pagehide事件：该事件会在用户离开网页时触发。离开网页有多种方式。如点击一个链接，刷新页面，提交表单，关闭浏览器等。pagehide 事件有时可以替代 unload事件，但 unload 事件触发后无法缓存页面。
```


# Js原生方法
```js
// 初始化时清空一个对象
      this.form = Object.assign(
        {},
        {
          form: {
            devName: '',
            devPhone: '',
            contactPhone: ''
          }
        }
      )
      
// switch(true) 方法根据条件返回不同的值
// 根据days的值在不同的条件下返回'info','warning','danger'
isendDate(end_date) {
  let value1 = end_date.split('/')
  value1.splice(1, 1, (value1[1] - 1).toString())
  let endDateTimeStramp = new Date(...value1).getTime()
  let days = parseInt(
    (endDateTimeStramp - new Date().getTime()) / 1000 / 60 / 60 / 24
  )
  switch (true) {
    case days >= 30:
      return 'info'
    case days > 0 && days < 30:
      return 'warning'
    case days < 0:
      return 'danger'
  }
}
```

# 常用正则表达式
```js
1.正则校验手机号
export function isPhone(str) {
  /**
     * 手机号码
     * 移动：134 135 136 137 138 139 147 150 151 152 157 158 159 178 182 183 184 187 188 198
     * 联通：130 131 132 145 155 156 166 171 175 176 185 186
     * 电信：133 149 153 173 177 180 181 189 199
     * 虚拟运营商: 170
     */
let res=/^(0|86|17951)?(13[0-9]|15[012356789]|16[6]|19[89]]|17[01345678]|18[0-9]|14[579])[0-9]{8}$/
  if (!rest.test(str) || !str) {
    return false
  } else {
    return true
  }
}

2.正则检测文件类型
new RegExp('(xlsx|docx|csv|jgp|png|jpeg)$', 'g')

3.正则匹配API接口
//匹配/v1/role/:serialnumber/remove的接口
new RegExp('/\/v1\/role\/\w*\/remove/', 'g')
```



# 其他常用方法
```js
// 将中文内容编码 encodeURIComponent与encodeURI方法比encodeURIComponent支持范围更广的字符集
window.encodeURIComponent(this.name)
// nodejs中对中文内容编码
encodeURI(this.name) //encodeURI是内置全局函数

//nodejs中使用util.format替换原字符串变量中占位符内容，url是原字符串，global.config.wx.appId/appSecret，code是要替换的内容
const url = 'https://api.weixin.qq.com/sns/jscode2session?appid=%s&secret=%s&js_code=%s&grant_type=authorization_code'
const util = require('util')
const code = 'abcdef'
const urlStr = util.format(url, global.config.wx.appId, global.config.wx.appSecret, 
code)


//html页面中使用a标签链接
<a href="http://www.abc.com">点击跳转</a>

//前端js环境中使用location全局对象刷新页面
location.reload()

//使用浏览器API跳转页面
window.location.href= '/login'

//vue的编程式导航
router.push('home')
router.push({ name: 'user', params: { userId: '123' }})
router.push({ path: 'register', query: { plan: 'private' }})

//vue的router-link组件导航
<nuxt-link to="/about">关于</nuxt-link>


//nuxt.js的nuxt-link组件导航，与router-link一样
<nuxt-link to="/about">关于</nuxt-link>

//http请求中发送basic Auth请求
wx.request({
    url:'http://localhost:3000/v1/classic/latest',
    method: 'GET',
    success: res=> console.log(res.data),
    header: {
        Authorization: this._encode()
    }
})

// npm install --save js-base64
import { Base64 } from 'js-base64'
_encode() {
    const token = wx.getStorageSync('token')
    const base64str = Base64.encode(token + ':')
    return 'Basic ' + base64str //注意Basic后面有空格
}
```


# 常用JS轮子

#### 标准库的扩充
1. underscore.js 扩充了 Array 和 Object 相关 API
2. moment.js 扩充了 Date
3. bluebird.js / hax/my-promise 实现了 Promise
4. async.js 模拟了 async 操作符
5. es5shim 用 ES 3 语法部分实现了 ES 5 特性
6. handlebars.js 实现模板字符串功能
7. DOM 的扩充
8. jQuery.js 操作 DOM
9. video.js 操作 video
10. Fabric.js 操作 canvas

#### UI 组件
纯 CSS 的 UI 组件库，如 Bulma
大而全的 UI 框架（CSS + JS），如 Bootstrap、Element UI

#### 垂直领域的 UI 组件
1. 专门做轮播的 Swiper
2. 专门做输入提示的 typeahead.js
3. 专门做文件上传的 fine-uploader
4. 专门做 3D 瓦片效果的 vanilla-tilt.js
5. 专门做视差效果的 parallax.js
6. 专门做数据可视化的 D3.js
7. 专门做图表的 echarts.js
8. 专门做动画的 velocity.js
9. 专门做粒子效果的 particle.js
10. 专门做手势识别的 hammer.js
11. 各种轮播特效的 vue-awesome-swiper