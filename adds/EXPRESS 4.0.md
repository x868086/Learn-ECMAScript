# EXPRESS 4.x
## 1. express req.param方法获取查询参数
```js
var router = express.Router()
    router.get("/",(req,res,next)=>{
        //获取前端发起的查询参数
        let sortParam=req.param('sortParam');
        let pageNum=Number(req.param('pageNum'));
        let pageSize=Number(req.param('pageSize'));
        console.log(req)
        //根据页数和每页的数量计算要跳过的条数
        let skips=(pageNum-1) * pageSize
    })
```

## 2. express实现登录
1. express后端，路由/users代码：
```js
//定义查询数据库取用户信息并返回用户信息
let loginMethod=(req,res,next)=>{
  let param={
    userName:req.body.userName,
    userPwd:req.body.userPwd
  }
  
  userModel.findOne(param,(err,doc)=>{
    if(err){
      res.json({'statusCode':0,'msg':err.message});
    }else{
      if(doc){
        res.cookie('userId',doc.userId,{path:'/'})
        res.json({'statusCode':1,'msg':'login auth...','result':doc.userName});
      }else{
        res.json({'statusCode':1,'msg':'user not find','result':null});
      }
    }
  })
}


//1.1处理/users/login路由
router.post('/login', function(req, res, next) {
  loginMethod(req, res, next)
});

router.post('/logout', function(req, res, next) {
    loginMethod(req,res,next)
    //登出时清空cookie
    res.clearCookie('userId', { path: '/' });
});

```

2. Vue前端代码：
```JS
    methods:{
        getLogin(){
            let userInfo={userName:this.userName,userPwd:this.userPwd}
            axios.post('/users/login',userInfo).then((res)=>{
                //1.1 从后端获取用户名后赋值给loginUser，要么为空，要么有用户名
                this.loginUser=res.data.result
                if(res.data.statusCode===1 && res.data.result){
                    //1.2控制用户名密码错误提示是否显示
                    this.errTips=false
                    this.loginState=true
                }else{
                    this.errTips=true
                    this.loginState=false
                }
            })
        },
        logout(){
            //1.3登出，将登录用户信息清空，同时更改loginState
            axios.post('/users/logout').then(res=>{
                if(res.data.statusCode===1){
                    this.loginUser=res.data.result
                }
            })

        }
    }
```

----