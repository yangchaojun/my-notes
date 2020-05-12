#### http 请求概述

- DNS解析，建立TCP链接，发送http请求
- server接收到http请求，处理，并返回
- 客户端接收到返回数据，处理数据（如渲染页面、执行js）

#### nodejs处理http请求

- get请求和querystring
- post请求和postdata
- 路由

#### nodejs处理get请求

- get请求，即客户端要向server端获取数据，如查询博客列表
- 通过querystring来传递数据，如a.html?a=100&b=200
- 浏览器直接访问，就发送get请求

#### nodejs处理post请求

- post请求，即客户端要向服务端传递数据，如新建博客
- 通过post data传递数据
- 浏览器无法直接模拟，需要手写js，或者使用postman

#### nodejs处理路由

- https://github.com/
- https://github.com/username
- https://github.com/username/xxx