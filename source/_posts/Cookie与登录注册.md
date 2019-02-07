---
title: Cookie与登录注册
date: 2019-02-07 23:22:08
tags:
---

# Cookie 的特点

1. 服务器通过 Set-Cookie 响应头设置 Cookie
2. 浏览器得到 Cookie 之后，每次请求都要带上 Cookie
3. 服务器读取 Cookie 就知道登录用户的信息（email）

## 问题

1. 我在 Chrome 登录了得到 Cookie，用 Safari 访问，Safari 会带上 Cookie 吗
no

2. Cookie 存在哪
Windows 存在 C 盘的一个文件里

3. Cookie会被用户篡改吗？
Session 可以解决这个问题，防止用户篡改

4. Cookie 有效期吗？
默认有效期20分钟左右，不同浏览器策略不同
后端可以强制设置有效期
```
设置过期时间:
Set-Cookie: <cookie-name>=<cookie-value>; Expires=<date> Expires //用于定义在哪个时间过期
Set-Cookie: <cookie-name>=<cookie-value>; Max-Age=<non-zero-digit> max-age //用于定义在多久之后会过期
删除cookie:
docCookies.removeItem(name[, path],domain)
```

5. Cookie 遵守同源策略吗？
也有，不过跟 AJAX 的同源策略稍微有些不同。
当请求 qq.com 下的资源时，浏览器会默认带上 qq.com 对应的 Cookie，不会带上 baidu.com 对应的 Cookie
当请求 v.qq.com 下的资源时，浏览器不仅会带上 v.qq.com 的Cookie，还会带上 qq.com 的 Cookie

# Session 与 Cookie 的关系
一般来说，Session 基于 Cookie 来实现。

### Cookie
1. 服务器通过 Set-Cookie 头给客户端一串字符串
2. 客户端每次访问相同域名的网页时，必须带上这段字符串
3. 客户端要在一段时间内保存这个Cookie
4. Cookie 默认在用户关闭页面后就失效，后台代码可以任意设置 Cookie 的过期时间
5. 大小大概在 4kb 以内

### Session
1. 将 SessionID（随机数）通过 Cookie 发给客户端
2. 客户端访问服务器时，服务器读取 SessionID
3. 服务器有一块内存（哈希表）保存了所有 session
4. 通过 SessionID 我们可以得到对应用户的隐私信息，如 id、email
5. 这块内存（哈希表）就是服务器上的所有 session

### LocalStorage
1. LocalStorage 跟 HTTP 无关
2. HTTP 不会带上 LocalStorage 的值
3. 只有相同域名的页面才能互相读取 LocalStorage（没有同源那么严格）
4. 每个域名 localStorage 最大存储量为 5Mb 左右（每个浏览器不一样）
5. 常用场景：记录有没有提示过用户（没有用的信息，不能记录密码）
6. LocalStorage 永久有效，除非用户清理缓存

### SessionStorage（会话存储）
1、2、3、4 同上
5. SessionStorage 在用户关闭页面（会话结束）后就失效。

## Cookie 和 Session 的区别
Cookie 保存在客户端，每次都随请求发送给 Server
Session 保存在 Server 的内存里，其 Session ID 是通过 Cookie 发送给客户端的

## Cookie 和 LocalStorage 的区别
LocalStorage 不会随 HTTP 发给 Server
LocalStorage 的大小限制比 Cookie 大多了

## LocalStorage 和 SessionStorage 的区别
一个不会自动过期，一个会自动过期。

## Cache-Control: max-age=1000 缓存 与 ETag 的「缓存」有什么区别？
Cache-Control 直接不发请求。
而 ETag 要发请求才行。

---

# 登录注册


## 服务端代码server.js
```
var http = require('http')
var fs = require('fs')
var url = require('url')
var port = process.argv[2]

if(!port){
  console.log('请指定端口号\nnode server.js 8888 这样')
  process.exit(1)
}

var server = http.createServer(function(request, response){
  var parsedUrl = url.parse(request.url, true)
  var pathWithQuery = request.url 
  var queryString = ''
  if(pathWithQuery.indexOf('?') >= 0){ queryString = pathWithQuery.substring(pathWithQuery.indexOf('?')) }
  var path = parsedUrl.pathname
  var query = parsedUrl.query
  var method = request.method

  /******** 从这里开始看，上面不要看 ************/

  console.log('含查询字符串的路径\n' + pathWithQuery)

  if(path === '/'){
    let string = fs.readFileSync('./index.html', 'utf8')
    let cookies =  request.headers.cookie.split('; ') // ['email=1@', 'a=1', 'b=2']
    let hash = {} //将cookie存放到一个hash表中
    for(let i =0;i<cookies.length; i++){
      let parts = cookies[i].split('=')
      let key = parts[0]
      let value = parts[1]
      hash[key] = value 
    }
    let email = hash.sign_in_email
    let users = fs.readFileSync('./db/users', 'utf8')
    users = JSON.parse(users)
    let foundUser //确认服务器是否有此用户信息
    for(let i=0; i< users.length; i++){
      if(users[i].email === email){
        foundUser = users[i]
        break
      }
    }
    console.log(foundUser)
    if(foundUser){
      string = string.replace('__password__', foundUser.password)
    }else{
      string = string.replace('__password__', '不知道')
    }
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/html;charset=utf-8')
    response.write(string)
    response.end()
  }else if(path === '/sign_up' && method === 'GET'){  //路由sign_up的逻辑
    let string = fs.readFileSync('./sign_up.html', 'utf8')
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/html;charset=utf-8')
    response.write(string)
    response.end()
  }else if(path === '/sign_up' && method === 'POST'){
    readBody(request).then((body)=>{
      let strings = body.split('&') // ['email=1', 'password=2', 'password_confirmation=3']
      let hash = {} //将用户提交的信息存到一个hash表里
      strings.forEach((string)=>{
        // string == 'email=1'
        let parts = string.split('=') // ['email', '1']
        let key = parts[0]
        let value = parts[1]
        hash[key] = decodeURIComponent(value) // hash['email'] = '1'
      })
      let {email, password, password_confirmation} = hash
      if(email.indexOf('@') === -1){ //如果用户填写的邮箱地址不含有@
        response.statusCode = 400
        response.setHeader('Content-Type', 'application/json;charset=utf-8')
        response.write(`{
          "errors": {
            "email": "invalid"
          }
        }`)
      }else if(password !== password_confirmation){
        response.statusCode = 400
        response.write('password not match')
      }else{
        var users = fs.readFileSync('./db/users', 'utf8')
        try{
          users = JSON.parse(users) // []
        }catch(exception){
          users = []  //如果解析本地db文件失败则设置为空数组
        }
        let inUse = false //确定本地是否有相同账户邮箱名
        for(let i=0; i<users.length; i++){
          let user = users[i]
          if(user.email === email){
            inUse = true
            break;
          }
        }
        if(inUse){
          response.statusCode = 400
          response.write('email in use')
        }else{
          users.push({email: email, password: password})
          var usersString = JSON.stringify(users)
          fs.writeFileSync('./db/users', usersString)
          response.statusCode = 200
        }
      }
      response.end()
    })
  }else if(path==='/sign_in' && method === 'GET'){ //sign_in逻辑与sign_up类似
    let string = fs.readFileSync('./sign_in.html', 'utf8')
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/html;charset=utf-8')
    response.write(string)
    response.end()
  }else if(path==='/sign_in' && method === 'POST'){
    readBody(request).then((body)=>{
      let strings = body.split('&') // ['email=1', 'password=2', 'password_confirmation=3']
      let hash = {}
      strings.forEach((string)=>{
        // string == 'email=1'
        let parts = string.split('=') // ['email', '1']
        let key = parts[0]
        let value = parts[1]
        hash[key] = decodeURIComponent(value) // hash['email'] = '1'
      })
      let {email, password} = hash
      var users = fs.readFileSync('./db/users', 'utf8')
      try{
        users = JSON.parse(users) // []
      }catch(exception){
        users = []
      }
      let found
      for(let i=0;i<users.length; i++){
        if(users[i].email === email && users[i].password === password){
          found = true
          break
        }
      }
      if(found){
        response.setHeader('Set-Cookie', `sign_in_email=${email}`)
        response.statusCode = 200
      }else{
        response.statusCode = 401
      }
      response.end()
    })
  }else if(path==='/main.js'){
    let string = fs.readFileSync('./main.js', 'utf8')
    response.statusCode = 200
    response.setHeader('Content-Type', 'text/javascript;charset=utf-8')
    response.write(string)
    response.end()
  }else{
    response.statusCode = 404
    response.setHeader('Content-Type', 'text/html;charset=utf-8')
    response.write(`
      {
        "error": "not found"
      }
    `)
    response.end()
  }

  /******** 代码结束，下面不要看 ************/
})

function readBody(request){
  return new Promise((resolve, reject)=>{
    let body = []
    request.on('data', (chunk) => {
      body.push(chunk);
    }).on('end', () => {
      body = Buffer.concat(body).toString();
      resolve(body)
    })
  })
}

server.listen(port)
console.log('监听 ' + port + ' 成功\n请打开 http://localhost:' + port)
```

## sign_up
```
<body>
  <div class="form-wrapper">
    <h1>注册</h1>
    <form id="signUpForm">
      <div class="row">
        <label>邮箱</label>
        <input type="text" name="email">
        <span class="error"></span>
      </div>
      <div class="row">
        <label>密码</label>
        <input type="password" name="password">
        <span class="error"></span>
      </div>
      <div class="row">
        <label>确认密码</label>
        <input type="password" name="password_confirmation">
        <span class="error"></span>
      </div>
      <div class="row">
        <input type="submit" value="注册">
      </div>
    </form>
  </div>
  <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
  <script>
    let $form = $('#signUpForm')
    $form.on('submit', (e)=>{
      e.preventDefault()
      let hash = {}
      let need = ['email', 'password', 'password_confirmation']
      need.forEach((name)=>{
        let value = $form.find(`[name=${name}]`).val()
        hash[name] = value
      })
      $form.find('.error').each((index, span)=>{
        $(span).text('')
      })
      if(hash['email'] === ''){
        $form.find('[name="email"]').siblings('.error')
          .text('填邮箱呀同学')
        return
      }
      if(hash['password'] === ''){
        $form.find('[name="password"]').siblings('.error')
          .text('填密码呀同学')
        return
      }
      if(hash['password_confirmation'] === ''){
        $form.find('[name="password_confirmation"]').siblings('.error')
          .text('确认密码呀同学')
        return
      }
      if(hash['password'] !== hash['password_confirmation']){
        $form.find('[name="password_confirmation"]').siblings('.error')
          .text('密码不匹配')
        return
      }
      $.post('/sign_up', hash)  
        .then((response)=>{
          console.log(response)
        }, (request)=>{
          let {errors} = request.responseJSON
          if(errors.email && errors.email === 'invalid'){
            $form.find('[name="email"]').siblings('.error')
              .text('邮箱格式错误')
          }
        })
    })
  </script>
</body>
```

## sign_in
```
<body>
  <div class="form-wrapper">
    <h1>登录</h1>
    <form id="signInForm">
      <div class="row">
        <label>邮箱</label>
        <input type="text" name="email">
        <span class="error"></span>
      </div>
      <div class="row">
        <label>密码</label>
        <input type="password" name="password">
        <span class="error"></span>
      </div>
      <div class="row">
        <input type="submit" value="登录">
      </div>
    </form>
  </div>
  <script src="https://cdn.bootcss.com/jquery/3.3.1/jquery.min.js"></script>
  <script>
    let $form = $('#signInForm')
    $form.on('submit', (e)=>{ 
      e.preventDefault()  
      let hash = {}  
      let need = ['email', 'password']
      need.forEach((name)=>{
        let value = $form.find(`[name=${name}]`).val()
        hash[name] = value
      })
      $form.find('.error').each((index, span)=>{
        $(span).text('')
      })
      if(hash['email'] === ''){
        $form.find('[name="email"]').siblings('.error')
          .text('填邮箱呀同学')
        return
      }
      if(hash['password'] === ''){
        $form.find('[name="password"]').siblings('.error')
          .text('填密码呀同学')
        return
      }
      $.post('/sign_in', hash)  
        .then((response)=>{
          window.location.href = '/'
        }, (request)=>{
          alert('邮箱与密码不匹配')
        })
    })
  </script>
</body>

```

## index.html
```
<body>
  <h1>你的密码是：__password__</h1>
</body>
```