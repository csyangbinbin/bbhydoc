# 实时雷达回波显示

实时雷达回波显示Javascript库为c++开发的webassembly模块。包含LibradarWebSDK.wasm与LibradarWebSDK.js两个文件.  
* LibradarWebSDK.wasm为编译后的webassembly模块。  
* LibradarWebSDK.js为胶水代码.  

组件内部采用Websocket协议与服务器端实时通信，读取压缩后的雷达回波数据，在客户浏览器端进行实时解压缩与渲染操作，并将最终渲染的图像显示在开发者提供的canvas标签上,开发者可对渲染后的canvas进行缩放与移动放置到地图上适当的位置。

##### 视频播放需要几个主要流程:
1. 使用用户名与密码进行登录,获取JWT Token,后续视频播放接口需要用到。具体参考[UserLogin](/api/UserLogin.md)
2. 查询指定雷达视频服务器地址。具体参考[QueryRTStreamEndPoints](/api/QueryRTStreamEndPoints.md)
3. 调用接口播放视频。参考```RRS_ConnectAndPlay```
4. 对视频进行控制。
5. 停止播放并断开网络连接。参考```RRS_Disconnect```


##### 参考代码:
> https://gitee.com/csyangbinbin/bbhydoc/tree/master/src
------

### 接口说明

- 视频播放 
```javascript
function RRS_ConnectAndPlay(config, RTConnectStatusFn, RTTracksUpdateFn)
 var config = {
      "wsAddress": gWSAddress,  //视频播放地址
      "initChannel": 0,         //初始播放通道(部分雷达有两个视频通道可选择0或者1,如果只有一个通道的雷达设置为0)
      "jwtToken": JwtToken,    //JWT Token
      "userName": "",    //用户名
      "requestDeviceID": "",  //请求视频的雷达ID
      "renderCanvasID": "#canvas-radar-image", //视频渲染的canvas的选择子
      "platformCode":1  //平台选择(B/S目前固定为1)
    };
    
function RTConnectStatusFn(handle, deviceID, statusCode) {
    //statusCode>=0代表成功 ,<0表示失败，具体原因参照状态码说明
    rt_stream_handle = handle; //保存全局视频播放句柄,后续操作需要此句柄
}
//目标回调函数目前没有用，仅用于占位
function RTTracksUpdateFn(handle, deviceID, obj) {}
```

| 状态码说明 | 宏定义  |	状态代码 |
|:--------| :---------:|--------:|
| 连接服务器成功 | WS_CONNECT_STATUS_OK |				0 |
|鉴权成功 |WS_CONNECT_STATUS_AUTH_OK	|			 1|
|参数错误	  |WS_CONNECT_STATUS_INVALID_ARGUMENT|		-1	|
|用户未登录 |WS_CONNECT_STATUS_NOT_SIGNIN		|	-2	|
|JWT Token错误或者超期	 |WS_CONNECT_STATUS_TOKEN_INVALID|		 -3	|
|客户端鉴权回复错误	| WS_CONNECT_STATUS_AUTH_RESP_ERROR	|	-4	|
|请求的设备不可访问	| WS_CONNECT_STATUS_DEVICE_ACCESS_DENIED|	-5 |
|请求的设备离线	| WS_CONNECT_STATUS_DEV_OFFLINE	|		-6		|
|其他未知错误	| WS_CONNECT_STATUS_UNKNOWN_ERROR|		-7	|
|无法连接服务器 |WS_CONNECT_STATUS_CONNECT_FAILED	|	-8|
|务器主动断开连接 |WS_CONNECT_STATUS_SERVER_DISCONNECT	|	-9|


- 停止播放并断开网络连接
```javascript
function RRS_Disconnect(handle)
```

- 改变播放的雷达视频通道
```javascript
//如果雷达有两个视频通道可选择0或者1 ，如果只有一个通道则只能为0
function RSS_ChangeStreamChannel(handle,channelIndex);
```

- 获取当前播放的雷达通道量程
```javascript
//返回值为雷达扫描半径,单位:米
function getRadarRange(handle)
```

- 获取/设置雷达回波透明度
```javascript
//透明度取值范围[0,1],0表示完全透明，1表示完全不透明
function setTransparency(handle,value)
function getTransparency(handle)
```

- 余晖颜色设置

```javascript
/*
余晖时间设置值:
0,表示不显示余晖
-1,表示一直显示余晖，不清除
其他大于0的值,表示余晖显示的秒数
*/
function setTrailsTime(handle,tm)
function getTrailsTime(handle) // 获取余晖设置时间
function clearTrails(handle) //清除当前的余晖
```

- 获取/设置余晖颜色

```javascript
//颜色采用数组表示[R,G,B,A] ,每个分量的取值范围[0,255]
function setTrailsColor(handle,[R,G,B,A])
function getTrailsColor()

```

- 获取/设置回波显示颜色表

```javascript
//当前的雷达回波颜色显示模式(目前实现了两种颜色模式:0-彩色模式,1-绿色灰度模式)
function setDisplayMode(handle,value)
function getDisplayMode()

```