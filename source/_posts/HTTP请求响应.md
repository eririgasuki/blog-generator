---
title: HTTP请求响应
date: 2018-11-28 16:47:23
tags:
---
# HTTP请求

## 请求的格式

```
1 动词 路径 协议/版本
2 Key1: value1
2 Key2: value2
2 Key3: value3
2 Content-Type: application/x-www-form-urlencoded
2 Host: www.baidu.com
2 User-Agent: curl/7.54.0
3 
4 要上传的数据
```

0. 请求最多包含四部分，最少包含三部分。（也就是说第四部分可以为空）
1. 第三部分永远都是一个回车（\n）
2. 动词有 GET POST PUT PATCH DELETE HEAD OPTIONS 等
3. 这里的路径包括「查询参数」，但不包括「锚点」
4. 如果你没有写路径，那么路径默认为 /
5. 第 2 部分中的 Content-Type 标注了第 4 部分的格式

##用Chrome开发者工具查看 HTTP 请求内容

1. 打开 Network
2. 地址栏输入网址
3. 在 Network 点击，查看 request，**点击「view source」**
4. 如果有请求的第四部分，那么在 FormData 或 Payload 里面可以看到

---

#HTTP响应

##响应的格式

```
1 协议/版本号 状态码 状态解释
2 Key1: value1
2 Key2: value2
2 Content-Length: 17931
2 Content-Type: text/html
3
4 要下载的内容
```

* HTTP状态码（HTTP Status Code）是用以表示网页服务器HTTP响应状态的3位数字代码。
   * 1XX 不常用 代表请求已被接受，需要继续处理。
   * 2XX 成功 代表请求已成功被服务器接收、理解、并接受。
   * 3XX 重定向 这类状态码代表需要客户端采取进一步的操作才能完成请求。
   * 4XX 请求错误 这类的状态码代表了客户端看起来可能发生了错误，妨碍了服务器的处理。
   * 5XX 服务器错误 这类状态码代表了服务器在处理请求的过程中有错误或者异常状态发生，也有可能是服务器意识到以当前的软硬件资源无法完成对请求的处理。
* 状态解释仅辅助理解状态码，用处不大。
* 第 2 部分中的 Content-Type 标注了第 4 部分的格式
* 第 2 部分中的 Content-Type 遵循 [MIME规范](https://baike.baidu.com/item/MIME/2900607?fr=aladdin)

## 用Chrome开发者工具查看 HTTP 响应内容

1. 打开 Network
2. 输入网址
3. 选中第一个响应
4. 查看 Response Headers，点击「view source」，点击「view source」，点击「view source」
5. 你会看到响应的前两部分
6. 查看 Response 或者 Preview，你会看到响应的第 4 部分

---

#  curl 命令

## curl常用（tldr）

```
curl ：   Transfers data from or to a server.Supports most protocols, including HTTP, FTP, and POP3.

**下载网页** Download the contents of an URL to a file: curl http://example.com -o filename 

**下载文件** Download a file, saving the output under the filename indicated by the URL: curl -O http://example.com/filename 

**提交post内容** Send form-encoded data (POST request of type application/x-www-form-urlencoded): curl -d 'name=bob' http://example.com/form 

**提交额外header内容** Send a request with an extra header, using a custom HTTP method: curl -H 'X-My-Header: 123' -X PUT http://example.com 
```

## 请求示例

```
curl -X POST -d "name=bob" -s -v -H "X-My-Header: 123" -- "https://www.baidu.com"
```

请求的内容为

```
POST / HTTP/1.1
Host: www.baidu.com
User-Agent: curl/7.54.0
Accept: */*
X-My-Header: 123
Content-Length: 8
Content-Type: application/x-www-form-urlencoded

name=bob
```

* -X POST 表示用post请求
* -S 不用进度条或错误信息的silent模式
* -V 显示请求和响应

