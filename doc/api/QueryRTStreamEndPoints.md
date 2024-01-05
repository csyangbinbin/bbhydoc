## 查询实时视频播放地址

雷达实时回波采用Websocket协议进行播放，在播放之前需要查询指定的雷达在那个服务器上提供视频服务.

- 接口地址:
```
http://api.sanhang.info:13002/libradarapi.SDK/QueryRTStreamEndPoints
```

- HTTP方法:POST

- 输入参数:
```json
{
  "radar_id": "" //雷达ID
}
```

- 返回值
```json
{
    "radarId": "", //雷达ID
    "endpoints": [  //由于一部雷达可能有多个服务器提供服务，返回一个地址数组
        {
            "address": "72.105.98.2", //视频服务器地址
            "tcpPort": 61002,   //TCP协议服务端口
            "wsPort": 61003,    //Websocket协议端口(B/S架构使用)
            "wssPort": 0        //WSS协议目前暂未实现
        }
    ]
}
```

> 此接口需要使用JWT Token进行验证,需要增加HTTP Header,Key为Authorization ,Value为"Bearer "+ JWTTOken,
详见下面示例代码

- 示例代码

```javascript
async function QueryRTStreamEndPoints(radar_id){
    const url = 'http://api.sanhang.info:13002/libradarapi.SDK/QueryRTStreamEndPoints' ;

    //此接口需要JWT验证，需要在请求头中添加登录返回的JWT Token
    var AuthorizationValue = "Bearer "+ JwtToken ; 

    const response = await fetch(url,{
    method:"POST",
    body: JSON.stringify(radar_id) ,
    headers:{"Content-type":"application/json" ,"Authorization":AuthorizationValue}} );
  if (!response.ok) {
    const message = `QueryRTStreamEndPoints API An error has occured: ${response.status}`;
    throw new Error(message);
  }
  const endpoints = await response.json();
  return endpoints;
}
```