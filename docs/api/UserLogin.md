## 用户登录

使用用户名与密码进行登录，返回用户相关信息，其中包含的JWT Token将作为后续操作的相关凭证.

- 接口地址:
```
http://api.sanhang.info:13002/libradarapi.Api/UserLogin
```

- HTTP方法:POST

- 输入参数:
```json
{
  "user_name":"", //同户名
  "password": "", //密码
  "platform":1 // 登录平台代码(0-桌面平台 1-web 2-app),如果是B/S架构设置为1
}
```

- 返回值
```json
{
 "userName": "",  //用户名
 "jwtToken": "",  //返回的JWT Token用于后续操作
 "mobilePhoneNumber": "", //手机号码
 "createdAt": "2023-12-04T07:17:05Z", //账户创建时间
 "updatedAt": "2023-12-14T01:09:08Z", //账户修改时间
 "userPrivileges": [1,2,3], //用户权限列表
 "roles": [{    //用户角色列表
   "roleId": 1, // 角色ID
   "expiredAt": "2100-01-01T12:00:00Z" //角色到期时间
   }],
 "radarAllowList": {  //用户可访问的雷达列表
  "userName": "",
  "allowAllDevices": true,
  "radarIds": []
 }
}
```

- 示例代码

```javascript
async function UserLogin(user_data) {
    const url = 'http://api.sanhang.info:13002/libradarapi.Api/UserLogin' ;
    const response = await fetch(url,{
    method:"POST",
    body: JSON.stringify(user_data) ,
    headers:{"Content-type":"application/json"}} );
  if (!response.ok) {
    const message = `UserLogin API An error has occured: ${response.status}`;
    throw new Error(message);
  }
  const loginState = await response.json();
  return loginState;
}
```