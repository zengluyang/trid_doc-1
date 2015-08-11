#client server flow

##登录、注册

###短信验证

- c->s: 短信验证请求 ：`sms_validation_request`

```
{
	"type":"sms_validation_request"
	"tel":"18615794931",
	"time":"2015-8-4 1:44"
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
- c->s: 信息请求 ：`picture_info_request`

```
{
	"type":"picture_info_request"
	"token":"user1",
}
```


- s: 按照时间查询，发送最近20条社区消息给客户端
- s->c: 周边信息响应：`picture_info_response`

```
{
	"type":"picture_info_response",
	"information":[{
		"picture_id":"200",
		"picture_publish_time":"2015-8-4 1:44",
		"picture_content":"",
		"picture_words":"it's a picture",
		"picture_like_num":"7",
		"picture_owner":"zhang",
		"picture_comment":[{
			"comment_own":"wang",
			"comment_msg":"hello",
			"comment_response":[{
				"comment_response_own":"zhang"
				"comment_response_msg":"hi"
			},
			...
			]
		},
		...
		],
	},
	...
	]
	"success":true,
	"error_no":0,
	"err_msg":""
}
```
####用户上传图片及文字
- c->s: 
- 请求方式 POST
- URL：http://101.200.89.240/index.php?r=picture/upload
```
{
	"type":"picture_upload_request",
	"token":"18782946332",
	"tel":"18782946332",
	"words":"hello!",
	"picture":"data:image\/jpeg;base64..."

}
```

- s: 处理并保存用户上传图片及文字
- s->c： 用户上传的图片及文字的处理结果，`picture_upload_response`,若成功则返回“true”,否则返回“false”


```
{
	"type":"picture_upload_response",
	"success":false,
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
