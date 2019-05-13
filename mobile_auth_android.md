# 1. 开发环境配置

## 1.1. 总体使用流程

1. 调用SDK方法来获得`token`，步骤如下：

   a. 构造SDK中认证工具类AuthnHelper的对象；</br>

   b. 使用AuthnHelper中的getToken方法，获得token。

2. 使用平台本机号码校验接口，进行号码校验

   </br>

## 1.2. 新建工程并导入SDK的jar文件

将`mobile_auth_android_*.jar`拷贝到应用工程的libs目录下，如没有该目录，可新建；

</br>

## 1.3. 配置AndroidManifest

注意：为避免出错，请直接从Demo中复制带<!-- required -->标签的代码

**配置权限**

```java
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.ACCESS_WIFI_STATE" />
<uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />
<uses-permission android:name="android.permission.CHANGE_NETWORK_STATE" />
```

通过以上步骤，工程就已经配置完成了。接下来就可以在代码里使用统一认证的SDK进行开发了。

</br>

## 1.4. SDK使用步骤

**1. 创建一个AuthnHelper实例** 

`AuthnHelper`是SDK的功能入口，所有的接口调用都得通过`AuthnHelper`进行调用。因此，调用SDK，首先需要创建一个`AuthnHelper`实例，其代码如下：

```java
public void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    mContext = this;    
    ……
    mAuthnHelper = AuthnHelper.getInstance(mContext);
    }
```

**2. 实现回调**

所有的SDK接口调用，都会传入一个回调，用以接收SDK返回的调用结果。结果以`JsonObjent`的形式传递，`TokenListener`的实现示例代码如下：

```java
mListener = new TokenListener() {
    @Override
    public void onGetTokenComplete(JSONObject jObj) {
        if (jObj != null) {
            mResultString = jObj.toString();
            mHandler.sendEmptyMessage(RESULT);
            if (jObj.has("token")) {
                mtoken = jObj.optString("token");
            }
        }
    }
};
```

日志打印回调，`TraceLogger`的实现示例代码如下：

```java
mTraceLogger = new TraceLogger() {
            @Override
            public void verbose(String tag, String msg) {

            }

            @Override
            public void debug(String tag, String msg) {
                Log.d(tag, msg);
            }

            @Override
            public void info(String tag, String msg) {
                Log.i(tag, msg);
            }

            @Override
            public void warn(String tag, String msg, Throwable tr) {

            }

            @Override
            public void error(String tag, String msg, Throwable tr) {
                Log.e(tag, msg);
                if (tr!= null){
                    tr.printStackTrace();
                }

            }
        };
```



**3. 接口调用**

```java
mAuthnHelper.umcLoginByType(Constant.APP_ID, 
                            Constant.APP_KEY,
                            "50", 
                            System.currentTimeMillis()+"", 
                            "", 
                            12000, 
                            12000, 
                            mListener,
                            mTraceLogger);
```



<div STYLE="page-break-after: always;"></div>

# 2. SDK方法说明

## 2.1. 获取管理类的实例对象

### 2.1.1. 方法描述

获取管理类的实例对象

</br>

**原型**

```java
public AuthnHelper (Context context)
```

</br>

### 2.1.2. 参数说明

| 参数    | 类型    | 说明                                               |
| ------- | ------- | -------------------------------------------------- |
| context | Context | 调用者的上下文环境，其中activity中this即可以代表。 |

</br>

## 2.2. 获取校验凭证token

### 2.2.1. 方法描述

开发者向统一认证服务器获取临时凭证`token`。</br>

**原型**

```java
public void umcLoginByType(final String appId, 
                           final String appKey,
                           final String capaid, 
                           final String capaidTime, 
                           String scene, 
                           int connTimeOut, 
                           int readTimeOut, 
                           final TokenListener listener,
                           TraceLogger mTraceLogger)
```

</br>

### 2.2.2. 参数说明

**请求参数**

| 参数         | 类型          | 说明                                                         |
| :----------- | :------------ | :----------------------------------------------------------- |
| appId        | String        | 应用的AppID                                                  |
| appkey       | String        | 应用密钥                                                     |
| capaid       | String        | 传200                                                        |
| capaidTime   | String        | 授权时间为13位的时间毫秒值 例如：  1509330580234             |
| scene        | String        | 场景参数 默认为空                                            |
| connTimeOut  | int           | 网络请求连接超时参数 单位为ms                                |
| readTimeOut  | int           | 网络请求读取超时参数 单位为ms                                |
| listener     | TokenListener | TokenListener为回调监听器，是一个java接口，需要调用者自己实现；TokenListener是接口中的认证登录token回调接口，OnGetTokenComplete是该接口中唯一的抽象方法，即void OnGetTokenComplete(JSONObject  jsonobj) |
| mTraceLogger | TraceLogger   | TraceLogger为回调日志打印，是一个java接口，需要 调用者自己实现； |

**响应参数**

OnGetTokenComplete的参数JSONObject，含义如下：

| 字段        | 类型   | 含义                                                         |
| ----------- | ------ | ------------------------------------------------------------ |
| resultCode  | String | 接口返回码，“103000”为成功。                                 |
| authType    | String | 登录类型返回码                                               |
| authTypeDes | String | 登录类型返回码描述                                           |
| resultDesc  | String | 失败时返回：返回错误码说明                                   |
| token       | String | 成功时返回：身份标识，字符串形式的token，第三方应用将该凭证经应用平台向统一认证平台请求认证 |
| openId      | String | 成功时返回：用户身份唯一标识                                 |
| traceId     | String | 请求唯一标示                                                 |

## 2.3. 取消获取校验凭证token

### 2.3.1. 方法描述

开发者调用SDK获取token 超过一定时间没有返回token的时候调用此方法进行取消。

**原型**

```java
public void cancel()
```

<div STYLE="page-break-after: always;"></div>

# 3. 平台接口说明

## 3.1. 获取用户信息接口

### 3.1.1. 接口说明

客户端SDK携带用户授权成功后的token来调用SDK服务端的相应访问用户资源的接口。

### 3.1.2. 接口描述

**请求地址：**https://www.cmpassport.com/unisdk/rsapi/tokenValidate

**协议：**HTTPS

**请求方法：**POST+JSON

### 3.1.3.参数说明

**请求参数**

| 参数名称      | 约束           | 层级 | 参数类型 | 说明                                                         |
| ------------- | -------------- | ---- | -------- | ------------------------------------------------------------ |
| header        | 必选           | 1    |          |                                                              |
| version       | 必选           | 2    | string   | 填1.0                                                        |
| msgid         | 必选           | 2    | string   | 标识请求的随机数即可(1-36位)                                 |
| systemtime    | 必选           | 2    | string   | 请求消息发送的系统时间，精确到毫秒，共17位，格式：20121227180001165 |
| strictcheck   | 必选           | 2    | string   | 验证源ip合法性，填写”1”，统一认证会校验sourceid与出口ip对应关系（申请sourceid时需提供业务出口ip，可以多个IP） |
| sourceid      | 可选           | 2    | string   | 业务集成统一认证的标识，需提前申请，申请指南见附录一         |
| ssotosourceid | 可选           | 2    | string   | 单点登录时使用，填写被登录业务的sourceid                     |
| appid         | 必选           | 2    | string   | 业务在统一认证申请的应用id                                   |
| apptype       | 必选           | 2    | string   | 1:BOSS<br />2:web<br />3:wap<br />4:pc客户端<br />5:手机客户端 |
| expandparams  | 扩展参数       | 2    | Map      | map(key,value)                                               |
| body          | 必选           | 1    |          |                                                              |
| sign          | 当有密钥时必填 | 2    | String   | 签名HMAC（appid+token）,key使用支付宝提供的公钥              |
| token         | 必选           | 2    | string   | 需要解析的凭证值。                                           |

**请求示例**

```
{
    "header": {
        "strictcheck": "0",
        "version": "1.0",
        "msgid": 	"40a940a940a940a93b8d3b8d3b8d3b8d",
        "systemtime": "20170515090923489",
        "appid": "10000001",
        "apptype": "5"
    },
    "body": {
 		"token":
"8484010001320200344E6A5A4551554D784F444E474E446C434E446779517A673340687474703A2F2F3139322E3136382E31322E3233363A393039302F0300040353EA68040006313030303030FF00203A020A143C6703D7D0530953C760744C7D61F5F7B546F12BC17D65254878748C"
    }
}
```

**响应参数**

| 参数名称            | 约束 | 层级 | 参数类型 | 说明                                                         |
| ------------------- | ---- | ---- | -------- | ------------------------------------------------------------ |
| header              | 必选 | 1    |          |                                                              |
| version             | 必选 | 2    | string   | 1.0                                                          |
| inresponseto        | 必选 | 2    | string   | 对应的请求消息中的msgid                                      |
| systemtime          | 必选 | 2    | string   | 响应消息发送的系统时间，精确到毫秒，共17位，格式：20121227180001165 |
| resultCode          | 必选 | 2    | string   | 返回码，返回码对应说明见附录二                               |
| body                | 必选 | 1    |          |                                                              |
| userid              | 必选 | 2    | string   | 系统中用户的唯一标识                                         |
| pcid                | 必选 | 2    | string   | 伪码id（显示和隐试登录都有）                                 |
| usessionid          | 可选 | 2    | string   | 暂忽略                                                       |
| passid              | 可选 | 2    | string   | 用户统一账号的系统标识                                       |
| andid               | 可选 | 2    | string   | 用户的“和ID”                                                 |
| msisdn              | 可选 | 2    | string   | 表示手机号码(1.返回手机号已新增校验逻辑新校验地址：[返回手机号适配逻辑](http://120.197.235.101:8090/pages/viewpage.action?pageId=3115524)  2.当appid有支付宝提供的密钥时，手机号加密返回，（使用AES加密，key为支付宝提供的公钥截取前16位） ) |
| email               | 可选 | 2    | string   | 表示邮箱地址                                                 |
| loginidtype         | 可选 | 2    | string   | 登录使用的用户标识：</br>0：手机号码</br>1：邮箱             |
| msisdntype          | 可选 | 2    | string   | 手机号码的归属运营商：</br>0：中国移动</br>1：中国电信</br>2：中国联通</br>99：未知的异网手机号码 |
| province            | 可选 | 2    | string   | 用户所属省份(暂无)                                           |
| authtype            | 可选 | 2    | string   | 认证方式，取值参见附录三                                     |
| authtime            | 可选 | 2    | string   | 统一认证平台认证用户的时间                                   |
| lastactivetime      | 可选 | 2    | string   | 暂无                                                         |
| relateToAndPassport | 可选 | 2    | string   | 是否已经关联到统一账号，暂无用处                             |
| fromsourceid        | 可选 | 2    | string   | 来源sourceid（即签发token sourceid）                         |
| tosourceid          | 可选 | 2    | string   | 目的sourceid（即被登录业务sourceid）                         |

**响应示例**

```
{
    "body": {
        "msisdntype": "0",
        "usessionid": "NjZEQUMxODNGNDlCNDgyQzg3@http://192.168.12.236:9090/",
        "passid": "000000000",
        "loginidtype": "0",
        "authtime": "2017-05-22 20:48:45",
        "msisdn": "13683329795",
        "lastactivetime": "",
        "authtype": "WAPGW",
        "relateToAndPassport": "1"
    },
    "header": {
        "inresponseto": "40a940a940a940a93b8d3b8d3b8d3b8d",
        "resultcode": "103000",
        "systemtime": "20170522204845598",
        "version": "1.0"
    }
}
```



<div STYLE="page-break-after: always;"></div>

# 4. 平台返回码说明

## 4.1. 平台返回码

| 错误编号 | 返回码描述                  |
| -------- | --------------------------- |
| 103101   | 签名错误                    |
| 103103   | 用户不存在                  |
| 103104   | 用户不支持该种登录方式      |
| 103105   | 密码错误                    |
| 103106   | 用户名错误                  |
| 103107   | 已存在相同的随机数          |
| 103108   | 短信验证码错误              |
| 103109   | 短信验证码超时              |
| 103111   | WAP网关IP不合法             |
| 103112   | 请求错误 reqError           |
| 103113   | Token内容错误               |
| 103114   | token验证 KS过期            |
| 103115   | token验证 KS不存在          |
| 103116   | token验证 sqn错误           |
| 103117   | mac异常 macError            |
| 103118   | sourceid不存在              |
| 103119   | appid不存在appidNOExist     |
| 103120   | clientauth不存在            |
| 103121   | passid不存在                |
| 103122   | btid不存在                  |
| 103123   | redisinfo不存在             |
| 103124   | ksnaf校验不一致             |
| 103125   | 手机格式错误                |
| 103126   | 手机号不存在                |
| 103127   | 证书验证，版本过期          |
| 103128   | gba webservice接口调用失败  |
| 103129   | 获取短信验证码的msgtype异常 |
| 103130   | 新密码不能与当前密码相同    |
| 103131   | 密码过于简单                |
| 103132   | 用户注册失败                |
| 103133   | sourceid不合法              |
| 103134   | wap方式手机号为空           |
| 103135   | 昵称非法                    |
| 103136   | 邮箱非法                    |
| 103138   | appid已存在                 |
| 103139   | sourceid已存在              |
| 103200   | 不需要更新ks                |
| 103204   | 缓存随机数不存在            |
| 103205   | 服务器内部异常              |
| 103207   | 发送短信失败                |
| 103212   | 校验密码失败                |
| 103213   | 旧密码错误                  |
| 103214   | 访问缓存或数据库错误        |
| 103226   | sqn过小或过大               |
| 103265   | 用户已存在                  |
| 103901   | 短信验证码下发次数已达上限  |
| 103902   | 凭证校验失败                |
| 104001   | APPID和APPKEY已存在         |
| 105001   | 联通网关取号失败            |
| 105002   | 移动网关取号失败            |
| 105003   | 电信网关取号失败            |
| 105004   | 短信上行ip检测不合法        |
| 105005   | 短信上行发送信息为空        |
| 105006   | 手机号码为空                |
| 105007   | 手机号码格式错误            |
| 105008   | 短信内容为空                |
| 105009   | 解析失败                    |

</br>

## 4.2. SDK返回码说明

| 错误编号 | 返回码描述                              |
| -------- | --------------------------------------- |
| 103000   | 成功                                    |
| 102101   | 无网络                                  |
| 102102   | 网络异常                                |
| 102223   | 数据解析异常                            |
| 102121   | 用户取消认证                            |
| 102505   | 业务未注册                              |
| 102506   | 请求出错                                |
| 102507   | 请求超时                                |
| 102201   | 自动登陆失败                            |
| 102202   | 应用签名失败                            |
| 102203   | 输入参数错误                            |
| 102204   | 正在gettoken处理                        |
| 102210   | 指定号码非本机号码                      |
| 102211   | 短信验证码验证成功后返回随机码为空      |
| 102222   | http响应头中没有结果码                  |
| 102299   | other failed                            |
| 102302   | 调用service超时                         |
| 103117   | mac异常 macError                        |
| 103200   | ks无需更新                              |
| 103203   | 缓存用户不存在                          |
| 200001   | imsi为空，跳到短信验证码登录            |
| 200002   | imsi为空，没有短信验证码登录功能        |
| 200003   | 复用中间件首次登录                      |
| 200004   | 复用中间件二次登录                      |
| 200005   | 用户未授权                              |
| 200006   | 用户未授权                              |
| 200007   | 不支持的认证方式 跳到短信验证码登录     |
| 200008   | 不支持的认证方式 没有短信验证码登录功能 |
| 200009   | 应用合法性校验失败                      |

<div STYLE="page-break-after: always;"></div>

# 附录1 认证方法标识

| authType | authTypeDes                         |
| -------- | ----------------------------------- |
| 0        | 其他（超时无法确认认证类型，返回0） |
| 1        | WIFI下网关鉴权                      |
| 2        | 网关鉴权                            |
| 3        | 短信上行鉴权                        |
| 4        | WIFI下网关鉴权复用中间件登录        |
| 5        | 网关鉴权复用中间件登录              |
| 6        | 短信上行鉴权复用中间件登录          |
| 7        | 短信验证码登录                      |