#client server flow

##登录、注册

###短信验证

- c->s: 
- 请求方式 POST
- URL：http://101.200.89.240/index.php?r=User/smsValidationCode
```
{
	"type":"sms_validation_result",
	"code":"18782945332",
	"tel":"18782945332"
}
```

- s->c: 
- 若请求成功，返回

```
{
	"type":"picture_search_response",
	"success":true,
	"error_no":0,
	"error_msg":null,
	"count":1,
	"offset":0,
	"limit":1,
	"pictures":{
		"55c99c2cf3bac6e813000033":{
			"_id":{"$id":"55c99c2cf3bac6e813000033"},
			"picture":"uploads\/18782945332\/14392760763437633d.jpeg",
			"word":"hello!",
			"createtime":"1439276076",
			"created_by":{"$id":"55c99c1613c5ea8feac65302"}
		}
	}
}
```
- 若请求失败，返回
```
{
	"type":"picture_search_response",
	"success":false,
	"error_no":1,
	"error_msg":
	"json decode failed."
}
```


- s: 检测客户端上次请求的时间，若与此次大于60s，调用第三方平台接口，发送给对应手机号码
- s->c: 短信验证响应：`sms_validation_send`,提示客户端是否成功调用了第三方短信发送接口

```
{
	"type":"sms_validation_send",
	"success":true,
	"error_no":0,
	"err_msg":""
}
```

- c: 搜集用户填写的验证码
- c->s: 短信验证吗：: `sms_validation_code`
```
{
	"type":"sms_validation_code",
	"code":"123456"
}
```

- s: 与第三方借口返回的验证码相比较，
- s->c： 短信验证结果，`sms_validation_result`,若成功应该包含一个token，用于坚定客户端是否通过了短信验证


```
{
	"type":"sms_validation_result",
	"success":false,
	"error_no":1,
	"err_msg" : "验证码不符"
}
```

###信息收集
1. c: 搜集用户信息
2. c->s: 用户信息: `user_register_info`,应该包含上一步所返回的token
3. s: 验证token，将`user_register_info`存入数据库
4. s-c： 用户信息结果： `user_register_result`,包含是否注册成功，以及用户id，token，token有效日期


##主界面

###暗恋

###秀爱社区
####查看信息
- c->s: 
- 请求方式 POST
- URL：http://101.200.89.240/index.php?r=picture/search
```
{
	"type":"picture_search_request",
	"tel":"18782945332",
	"token":"18782945332"
}
```

- s->c: 
- 若请求成功，返回

```
{
	"type":"picture_search_response",
	"success":true,
	"error_no":0,
	"error_msg":null,
	"count":1,
	"offset":0,
	"limit":1,
	"pictures":{
		"55c99c2cf3bac6e813000033":{
			"_id":{"$id":"55c99c2cf3bac6e813000033"},
			"picture":"uploads\/18782945332\/14392760763437633d.jpeg",
			"word":"hello!",
			"createtime":"1439276076",
			"created_by":{"$id":"55c99c1613c5ea8feac65302"}
		}
	}
}
```
- 若请求失败，返回
```
{
	"type":"picture_search_response",
	"success":false,
	"error_no":1,
	"error_msg":
	"json decode failed."
}
```
####用户上传图片及文字
- c->s: 
- 请求方式 POST
- URL：http://101.200.89.240/index.php?r=picture/upload
```
{
	"type":"picture_upload_request",
	"token":"18782945332",
	"tel":"18782945332",
	"words":"hello!",
	"picture":"data:image\/jpeg;base64..."

}
```

- s->c： 
- 若请求成功，则返回

```
{
	"type":"picture_upload_response",
	"success":true,
	"error_no":0,
	"error_msg":null,
	"picture":"14392797035a676b3d.jpeg"
}
```
- 若请求失败，返回
```
{
	"type":"picture_search_response",
	"success":false,
	"error_no":1,
	"error_msg":
	"json decode failed."
}
```
####点赞
- c: 用户对图片点赞
- c->s: 点赞图片请求: `picture_like_request`
```
{
	"type":"picture_like_request",
	"picture_id":"12"
}
```
- s: 处理用户点赞的动作
- s->c： 返回点赞行为的处理结果，`picture_like_response`,若成功则返回“true”,否则返回“false”


```
{
	"type":"picture_like_response",
	"success":true,
}
```
###个人资料

###设置
####号码更换
- c: 用户号码更换
- c->s: 用户号码更换请求: `picture_like_request`
####接收暗恋信息
