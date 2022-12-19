[[FLASK]]
#limiter限流器

==发送短信==
短信服务(Short Message Service)提供专业短信送达服务,支持快速发送短信验证码、短信通知等。 知名的有：腾讯短信服务，阿里云短信服务等等！容联云

**注意：**
1. 短信不能重复发送
2. 短信验证码内容需要保存起来

Flask-limiter需要安装
>==pip install limiter==

Flask-limiter为flask路由提供了速率限制的特性。他支持使用内存、redis或者memache作为存储环境的后端实现

**==默认的限制器==**
==key_func== **参数是判断函数，表示以何种条件算一次访问**？这里使用的是get_romte_addressm此函数返回的是客户端的地址
==default_limits== 是一个数组，用于依次提同一个判断条件.比如100/day是指一天100次访问限制。

**常用的访问限制字符串格式如下:**
>10 per hour
>10/hour
>10/hour;100/day;2000 per year
>100/day,500/7days
>这里的==per==可以用==/==来代替

	注意：默认的限制器对所有视图都有效，除非你自定义一个限制器用来覆盖默认限制器,或者使用limiter.exmpt装饰器来取消

```python
limiter = Limiter(
	app,
	ket_func = get_romte_address,
	default_limits=["1 per day","2 per hour"]
)
```

**基于FBV的简单使用**
FBV是前后端不分离

```python
@app.route("/slow")
@limiter.limit('1 per day')
def slow():
	return '24'
```

**基于CBV的限频方式**
CBV是前后端分离

```python
from flask import Flask
from flask_limiter import Limiter
from flask_limiter.utils import get_remote_address
from flask_restful import Resource, Api

app = Flask(__name__)

#创建限流器
limiter = Limiter(app, key_func=get_remote_address)

class MyView(Resource):
	#注意：decoratos是固定的
	decoratos=[limiter.limit("1/minute")]
	def get(self):
		
```
decoratos=[limiter.limit('1/minte')]其中==limit(传3个参数，参数1：限流速率，参数2：key_func,参数3：error_message=  超出限制之后的提示信息)==