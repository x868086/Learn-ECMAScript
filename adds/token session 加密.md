
# JWT token
## 1. token utils
```js
// /config/config.js文件
const tokenSecurity = {
  secret:
    environment.env === 'development'
      ? 'NEYKR37jCFEH0o5tsbmxvemR7KQv3oZY0yAo'
      : 'dW5pY21zc3lzdGVtKioq',
  accessExpiresIn: environment.env === 'development' ? '8h' : '2h',
  refreshExpiresIn: '3 days',
};

//jwt is async so use Promise
//jwt method sign's argument is expiresIn
const jwt = require('jsonwebtoken')
const bcrypt = require('bcryptjs')

const { tokenSecurity: {
    secret,
    accessExpiresIn,
    refreshExpiresIn
}, smsExpireTime } = require('../config/config')

// tokenType传入时，必须为'accessExpiresIn'或者'refreshExpiresIn'，用来区分accessToken和refreshToken
const generateToken = (userId, orgId, scopeTop, role, channelArray, tokenType, ...restToken) => {
    let token
    try {
        token = jwt.sign({
            // 复用generateToken方法生成refreshToken,剩余参数为accout,secret收敛到...restToken中 
            userId, orgId, scopeTop, role, channelArray, ...restToken
        },
            secret, {
            expiresIn: tokenType
        }
        )
    } catch (error) {
        throw new global.errs.ParametersException(`${error.message} token校验错误`, 10003, 500)
    }

    return token
}

const verifyToken = async (token) => {
    try {
        return await jwt.verify(token, secret)
    } catch (error) {
        throw new global.errs.Unauthorized(`${error.message} token验证出错`)
    }
}

// apiScope 根据apiList名单传入的目标api的scope级别，与用户的scopeTop值做对比确定token的权限
const decodedToken = async (token, apiScope) => {
    let decoded = null
    try {
        decoded = await jwt.verify(token, secret)
    } catch (error) {
        if (error.name === 'TokenExpiredError') {
            throw new global.errs.Unauthorized('token已过期', 50000)
        }
        throw new global.errs.Unauthorized(error.message)
    }
    if (decoded.scopeTop < apiScope) {
        throw new global.errs.Forbidden('权限不足')
    }
    return decoded
}

const generateSecret = (val) => {
    const salt = bcrypt.genSaltSync(10)
    return bcrypt.hashSync(val, salt)
}

const decodedSecret = (plaintext, ciphertext) => {
    return bcrypt.compareSync(plaintext, ciphertext)
}


const generateSms = {
    get smsCode() {
        return Math.random().toString(10).slice(2, 8)
    },
    get smsExpireTime() {
        return new Date().getTime() + smsExpireTime.expire
    }
}

module.exports = {
    tokenUtile: {
        generateToken,
        verifyToken,
        decodedToken
    },
    secretUtile: {
        generateSecret,
        decodedSecret
    },
    generateSms
}


//错误与代码
TokenExpiredError
如果令牌过期，则抛出错误。
错误对象：
name：'TokenExpiredError'
message：'jwt expired'
expiredAt：[ExpDate]

JsonWebTokenError
错误对象：
name：'JsonWebTokenError'
message：
jwt异常
jwt签名是必需的
无效签名
jwt观众无效 预期：[OPTIONS AUDIENCE]
jwt发行人无效。预期：[OPTIONS ISSUER]
jwt id无效。预期：[OPTIONS JWT ID]
jwt主题无效。预期：[OPTIONS SUBJECT]
```
----








# passport
## 1.passport库保存用户状态到session中
```js
//utils/passport.js 配置文件
import passport from 'koa-passport' //koa passport中间件
import LocalStrategy from 'passport-local' //本地策略
import UserModel from '../../dbs/models/users' //mongoose Model

//1.设置本地策略,new LocalStrategy(fn),fn有三个参数,用户名username,密码password,回调函数done
passport.use(new LocalStrategy(async function(username,password,done){
  let where = {
    username
  };
  let result = await UserModel.findOne(where)
  if(result!=null){
    if(result.password===password){
    //2.用户名和密码都匹配的情况下执行done回调函数，并返回mongoose的查询结果result即用户对象
      return done(null,result)
    }else{
      return done(null,false,'密码错误')
    }
  }else{
    return done(null,false,'用户不存在')
  }
}))

//3.执行序列化方法，即在账号密码验证通过后将用户对象存到session中，同时返回cookie到客户端。这里的用户对象就是上面查询结果result
passport.serializeUser(function(user,done){
  done(null,user)
})
//4.执行反序列化方法，即每次收到客户端发起的请求后先从session中读取上面保存的用户对象,然后添加到req请求对象上。
passport.deserializeUser(function(user,done){
  return done(null,user)
})

export default passport
```

```js
//Koa app.js 入口文件引入'utils/passport.js'配置文件
import passport from 'utils/passport.js'
app.use(passport.initialize())
app.use(passport.session())
```

```js
//interface/users.js 接口文件中使用passport
const Router = require('koa-router')
const router = new Router()
const passport = require(__dirname + '/passport_config.js')
// 认证登录
router.post('/users/login', function (ctx, next) {
    return passport.authenticate('local', function (err, user, info, status) {
        if (user) {
            ctx.body = 'Y'
            return ctx.login(user)
        } else {
            ctx.body = info
        }
    })(ctx, next)
})
 
// 认证登出
router.get('/users/logout', function (ctx, next) {
    ctx.logout()
    ctx.body = 'Y'
})
 
// 以下为自定义需要身份认证的路由,路由守护
router.post('/users/test', function (ctx, next) {
    if (ctx.isAuthenticated()) {
        ctx.body = '认证通过'
    } else {
        ctx.throw(401)
        ctx.body = '非法访问'
    }
})
 
module.exports = router
```

### passport扩展了HTTP Request,添加了四个方法
### ctx.login(user, options,callback)
ctx.login() 会触发序列化操作,对账号密码验证通过的用户初始化session。options可设置session为false，即不初始化session，默认为true。
#### ctx.logout()
登出用户，删除该用户session。不带参数。
#### ctx.isAuthenticated()
不带参数。作用是测试该用户是否存在于session中（即是否已登录）。若存在返回true。
#### ctx.isUnauthenticated()
不带参数。和上面的作用相反。

----



# CryptoJS 前端加密
## 1.使用MD5方法加密前端传送数据
**MD5方法加密后无法解密，两个加密前内容相同的字符串在经过MD5加密后，内容也相同**
```js
npm install --save crypto-js
import CryptoJS from 'crypto-js'
const str1='abc'
const password = CryptoJS.MD5(str1).toString()
```

# BcryptJS 前端加密
## 1.使用BcryptJS方法加密传送数据
```js
npm install --save crypto-js
import bcrypt from 'bcrypt.js'
// 生成盐需要的成本，越大生成时间越长，默认10
const salt=bcrypt.genSaltSync(10)
const password = bcrypt.hashSync(body.password, salt)
```
## 2.通过比对原密码和加密后的密码来确定原密码是否和加密后的密码一致
```js
    import bcrypt from 'bcrypt.js'
    const correct = bcrypt.compareSync(pwd1,pwd2)
    //pwd1是加密前的密码，pwd2是加密后的密码
    if (correct) {return true}
```