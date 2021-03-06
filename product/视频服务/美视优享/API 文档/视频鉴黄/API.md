开发者可以在 [用户体验平台](http://www.qcloud.com/event/pd) 体验智能鉴黄的效果(用户体验平台不需要注册账号)。

## 1. 基本概念

| 概念     | 解释               |
| ------ | ---------------- |
| appid  | 项目 ID , 用于唯一标识接入项目 |
| bucket | 开发者添加的空间名称       |

> **注意：**
> 如果开发者使用的是 V1 版本，appid 为其当时生成的 appid

## 2. 描述

智能鉴黄接口采用 http 协议，支持多 url 和多本地图片文件，每个请求最多支持 20 张图片或 url 。
**接口：**  http://service.image.myqcloud.com/detection/porn_detect
**方法：**  POST

## 3. 图片 url 鉴黄

### 3.1 请求语法


```
POST /detection/porn_detect HTTP/1.1
Authorization: Signature
Host: service.image.myqcloud.com
Content-Length: ContentLength
Content-Type: "application/json"

{
    "appid": "appid",
    "bucket": "bucket",
    "url_list": [
        "url",
        "url"
    ]
}
```

**请求包的 http header：**

| 参数             | 是否必须 | 描述                              |
| -------------- | ---- | ------------------------------- |
| Host           | 是    | 访问域名，service.image.myqcloud.com |
| Authorization  | 是    | 鉴权签名，详见下面鉴权章节                   |
| Content-Type   | 是    | 标准的 application/json             |
| Content-Length | 是    | http body 总长度                    |

**请求包 http body：**

| 参数     | 是否必须 | 类型     | 描述      |
| ------ | ---- | ------ | ------- |
| appid  | 是    | Uint   | 业务 ID    |
| bucket | 是    | String | 图片空间    |
| url    | 是    | String | 图片 url 列表 |

### 3.2 返回内容

**响应 http body(json 格式)：**

| 参数          | 是否必须   | 类型           |
| ----------- | ------ | ------------ |
| result_list | json 数组 | 具体查询数据，内容见下表 |

**result_list（json 数组）中每一项的具体内容：**

| 参数      | 类型     | 描述           |
| ------- | ------ | ------------ |
| code    | Int    | 服务器错误码，0 为成功  |
| message | String | 服务器返回的信息     |
| url     | String | 当前图片的 url     |
| data    |        | 具体查询数据，具体见下表 |

**data 字段具体内容：**

| 参数            | 类型     | 描述                                       |
| ------------- | ------ | ---------------------------------------- |
| result        | Int    | 供参考的识别结果，0 正常，1 黄图，2 疑似图片                   |
| confidence    | Double | 识别为黄图的置信度，范围 0-100；是 normal_score, hot_score, porn_score 的综合评分 |
| normal_score  | Double | 图片为正常图片的评分                               |
| hot_score     | Double | 图片为性感图片的评分                               |
| porn_score    | Double | 图片为色情图片的评分                               |
| forbid_status | Int    | 封禁状态，0 表示正常，1 表示图片已被封禁（只有存储在万象优图的图片才会被封禁）  |

> **说明：**
> 1、当 result=0 时，表明图片为正常图片；
> 2、当 result=1 时，表明该图片是系统判定的 100% 为违禁的图片；如果该图片存储在万象优图，则会直接被封禁掉；
> 3、当 result=2 时，表明该图片是疑似图片，即为黄图的可能性很大（目前 confidence 大于等于 83 小于 91 定为疑似图片）。

### 3.3 示例

**http 请求：**

```
POST /detection/porn_detect HTTP/1.1
Authorization: FCHXddYbhZEBfTeZ0j8mn9Og16JhPTEwMDAwMzc5Jms9QUtJRGVRZDBrRU1yM2J4ZjhRckJiUkp3Sk5zbTN3V1lEeHN1JnQ9MTQ2ODQ3NDY2MiZyPTU3MiZ1PTAmYj10ZXN0YnVja2V0JmU9MTQ3MTA2NjY2Mg==
Host: service.image.myqcloud.com
Content-Length: 238
Content-Type: "application/json"

{
    "appid": 10000379,
    "bucket": "testbucket",
    "url_list": [
        "http://www.bz55.com/uploads/allimg/140805/1-140P5162300-50.jpg",
        "http://img.taopic.com/uploads/allimg/130716/318769-130G60P30462.jpg"
    ]
}
```

**响应 http body（application/json 格式）：**

```
{
    "result_list": [
        {
            "code": 0,
            "message": "success",
            "url": "http://www.bz55.com/uploads/allimg/140805/1-140P5162300-50.jpg",
            "data": {
                "result": 0,
                "forbid_status": 0,
                "confidence": 12.509,
                "hot_score": 87.293,
                "normal_score": 12.707,
                "porn_score": 0.0
            }
        },
        {
            "code": 0,
            "message": "success",
            "url": "http://img.taopic.com/uploads/allimg/130716/318769-130G60P30462.jpg",
            "data": {
                "result": 0,
                "forbid_status": 0,
                "confidence": 14.913,
                "hot_score": 99.997,
                "normal_score": 0.003,
                "porn_score": 0.0
            }
        }
    ]
}
```

## 4. 图片文件鉴黄

**描述：**图片文件鉴黄使用 HTML 表单上传一个或多个文件，文件内容通过多重表单格式（multipart/form-data）编码。

### 4.1 请求语法

```
POST /detection/porn_detect HTTP/1.1
Content-Type: multipart/form-data;boundary=-------------------------acebdf13572468
Authorization: Signature
Host: service.image.myqcloud.com
Content-Length: ContentLength

---------------------------acebdf13572468
Content-Disposition: form-data; name="appid";

appid
---------------------------acebdf13572468
Content-Disposition: form-data; name="bucket";

bucket
---------------------------acebdf13572468
Content-Disposition: form-data; name="image[0]"; filename="image _1.jpg"
Content-Type: image/jpeg

image_content
---------------------------acebdf13572468
Content-Disposition: form-data; name="image[1]"; filename="image _2.jpg "
Content-Type: image/jpeg

image_content
---------------------------acebdf13572468--
```

**请求包 http header：**

| 参数             | 是否必须 | 描述                              |
| -------------- | ---- | ------------------------------- |
| Host           | 是    | 访问域名，service.image.myqcloud.com |
| Authorization  | 是    | 鉴权签名，详见下面鉴权章节                   |
| Content-Type   | 是    | 标准的 application/json             |
| Content-Length | 是    | http body 总长度                    |

**表单域：**

| 参数     | 是否必须 | 类型         | 描述                                       |
| ------ | ---- | ---------- | ---------------------------------------- |
| appidt | 是    | Uint       | 业务 ID |
| bucket | 是    | String     | 图片空间                                     |
| image  | 是    | Image/Jpeg | 图片文件，支持多个。参数名须为 “image[0]”、“image[1]” 等，每张图片需指定 filename |

### 4.2 返回内容

**响应 http body (json 格式)：**

| 参数          | 类型     | 描述           |
| ----------- | ------ | ------------ |
| result_list | json 数组 | 具体查询数据，内容见下表 |

**result_list（json 数组）中每一项的具体内容：**

| 参数       | 类型     | 描述                            |
| -------- | ------ | ----------------------------- |
| code     | Int    | 服务器错误码，0 为成功                   |
| message  | String | 服务器返回的信息                      |
| filename | String | 当前图片的 filename，与请求包中 filename 一致 |
| data     |        | 具体查询数据，内容见下表                  |

**data 字段具体内容：**

| 参数            | 类型     | 描述                                       |
| ------------- | ------ | ---------------------------------------- |
| result        | Int    | 供参考的识别结果，0 正常，1 黄图，2 疑似图片                   |
| confidence    | Double | 识别为黄图的置信度，范围 0-100；是 normal_score, hot_score, porn_score 的综合评分 |
| normal_score  | Double | 图片为正常图片的评分                               |
| hot_score     | Double | 图片为性感图片的评分                               |
| porn_score    | Double | 图片为色情图片的评分                               |
| forbid_status | Int    | 封禁状态，0 表示正常，1 表示图片已被封禁（只有存储在万象优图的图片才会被封禁）  |

> ** 说明：**
> 1、 当 result=0 时，表明图片为正常图片；
> 2、 当 result=1 时，表明该图片是系统判定的违禁的图片；
> 3、 当 result=2 时，表明该图片是疑似图片，即为黄图的可能性很大（目前 confidence 大于等于 83 小于 91 定为疑似图片）。

### 4.3 示例

**http 请求：**

```
POST /detection/ porn_detect HTTP/1.1
Content-Type:multipart/form-data;boundary=-------------------------acebdf13572468
Authorization:FCHXddYbhZEBfTeZ0j8mn9Og16JhPTEwMDAwMzc5Jms9QUtJRGVRZDBrRU1yM2J4ZjhRckJiUkp3Sk5zbTN3V1lEeHN1JnQ9MTQ2ODQ3NDY2MiZyPTU3MiZ1PTAmYj10ZXN0YnVja2V0JmU9MTQ3MTA2NjY2Mg==
Host: service.image.myqcloud.com
Content-Length: 61478

---------------------------acebdf13572468
Content-Disposition: form-data; name="appid";

10000379
---------------------------acebdf13572468
Content-Disposition: form-data; name="bucket";

testbucket
---------------------------acebdf13572468
Content-Disposition: form-data; name="image[0]"; filename="1.jpg"
Content-Type: image/jpeg

<@INCLUDE *D:\185839ggh0oedgnog04g0b.jpg.thumb.jpg*@>
---------------------------acebdf13572468
Content-Disposition: form-data; name="image[1]"; filename="2.jpg"
Content-Type: image/jpeg

<@INCLUDE *D:\200132svnmybmhbmmgbmga.jpg.thumb.jpg*@>
---------------------------acebdf13572468
```

** 响应 http body(application/json 格式)：**

```
{
    "result_list": [
        {
            "code": 0,
            "message": "success",
            "filename": "1.jpg",
            "data": {
                "result": 1,
                "forbid_status": 0,
                "confidence": 96.853,
                "hot_score": 0.0,
                "normal_score": 0.0,
                "porn_score": 100.0
            }
        },
        {
            "code": 0,
            "message": "success",
            "filename": "2.jpg",
            "data": {
                "result": 0,
                "forbid_status": 0,
                "confidence": 41.815,
                "hot_score": 19.417,
                "normal_score": 0.077,
                "porn_score": 80.506
            }
        }
    ]
}
```



## 5 错误码

| 错误码   | 含义                                  |
| ----- | ----------------------------------- |
| 3     | 错误的请求                               |
| 4     | 签名为空                                |
| 5     | 签名串错误                               |
| 6     | appid/bucket/url 不匹配                 |
| 7     | 签名编码失败（内部错误）                        |
| 8     | 签名解码失败（内部错误）                        |
| 9     | 签名过期                                |
| 10    | appid 不存在                            |
| 11    | secretid 不存在                         |
| 12    | appid 不匹配                            |
| 13    | 重放攻击                                |
| 14    | 签名失败                                |
| 15    | 操作太频繁，触发频控                          |
| 16    | 内部错误                                |
| 17    | 未知错误                                |
| 200   | 内部打包失败                              |
| 201   | 内部解包失败                              |
| 202   | 内部链接失败                              |
| 203   | 内部处理超时                              |
| -1300 | 图片为空                                |
| -1308 | url 图片下载失败                           |
| -1400 | 非法的图片格式                             |
| -1403 | 图片下载失败                              |
| -1404 | 图片无法识别                              |
| -1505 | url 格式不对                             |
| -1506 | 图片下载超时                              |
| -1507 | 无法访问 url 对应的图片服务器                     |
| -5062 | url 对应的图片已被标注为不良图片，无法访问（专指存储于腾讯云的图片） |
