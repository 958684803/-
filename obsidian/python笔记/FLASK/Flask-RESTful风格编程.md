#Flask-RESTful风格编程
![[Pasted image 20221214082946.png]]
在前后端不分离的应用模式中，前端页面所展现出来的效果都是由后端控制的，由后端进行渲染重定向，也就是后端需要控制前端的展示，这种模式前后端耦合度较高

这种应用模式比较适合纯网页应用，但是当后端对接app时，可能不需要后端返回html页面，而是需要后端提供数据，这种时候就需要后端提供一套app接口
![[Pasted image 20221214084534.png]]
在前后端分离的应用模式中，后端仅返回前端所需的数据，不再渲染HTML页面，不再控制前端的效果。至于前端用户看到什么效果，从后端请求的数据如何加载到前端中，都由前端自己决定，网页有网页的处理方式，App有App的处理方式，但无论哪种前端，所需的数据基本相同，后端仅需开发一套逻辑对外提供数据即可。

在前后端分离的应用模式中，前后端的耦合度相对较低

在前后端分离模式中，通常将后端开发的每一个视图都称为一个接口或者api，前端通过访问接口来实现对数据的增删改查


# 一、RESTful定义
#Flask-RESTful风格编程/RESTful定义
*RESTFUL* 是一种网络应用程序的设计风格和开发方式,基于**HTTP** ，可以使用**XML**格式定义或者**JSON**格式定义

*RESTFUL* 适用于互联网厂商作为业务使用接口的场景
![[Pasted image 20221214101934.png]]
## RESTFUL特点包括:
#Flask-RESTful风格编程/RESTful定义/RESTful的特点 
1. 每一个url代表一种资源
2. 客户端使用GET、POST、PUT、DELETE4个表示操作方式的动词对服务端资源进行操作：==GET==：用来获取资源、==POST==：用来新建资源（更新资源）、==PUT==：更新资源、==DELETE==：删除资源
3. 通过操作资源的表现形式来操作资源
4. 资源的表现形式是==XML==或者==JSON==
5. 客户端与服务端之间的交互请求是无状态的，从客户端到服务器的每个请求都必须包含理解请求所必须的信息
**比如**：(POST/PUT/GET/DELETE) - http://127.0.0.1:8080/AppName/rest?id=1

# 二、RESTful风格的软件架构

RESTful架构是对MVC架构改进后所形成的一种架构，通过使用事先定义好的接口与不同的服务联系起来。在RESTful架构中，客户端使用POST，DELETE，PUT和GET四种请求方式分别对指定的URL资源进行增删改查操作。

因此，RESTful是通过URI实现对资源的管理及访问，具有扩展性强、结构清晰的特点。

RESTful架构将服务器分成前端服务器和后端服务器两部分，前端服务器为用户提供无模型的视图；后端服务器为前端服务器提供接口。浏览器向前端服务器请求视图，通过视图中包含的AJAX函数发起接口请求获取模型。

项目开发引入RESTful架构，利于团队并行开发。在RESTful架构中，将多数HTTP请求转移到前端服务器上，降低服务器的负荷，使视图获取后端模型失败也能呈现。但RESTful架构却不适用于所有的项目，当项目比较小时无需使用RESTful架构，项目变得更加复杂。

# 三、安装和使用
`pip install flask-restful`
## 1.普通使用
```
from flask import Flask
from flask_restful import Resource, Api

app = Flask(__name__)

#第一步：创建api接口
api = Api(app)

#第二步：创建资源
class HelloResource(Resource):
	def get(self):
		#需要返回一个JSON的字符串
		return {'hello': 'world'}

#第三步：将资源添加到api中
api.add_resource(HelloResource, '/')

```
==注意==：通过endpoint参数为路由起名：api.add_resource(HelloWorldResource, '/', endpoint='HelloWorld')
## 2.蓝图的使用
#Flask-RESTful风格编程/RESTful定义/RESTful的特点/蓝图的使用 
```
from flask import Flask, Blueprint
from flask_restful import Resource, Api

app = Flask(__name__)

#创建蓝图
app_bp = Blueprint('user', __name__)

#第一步：创建api接口
api = Api(app_bp)

#第二步：定义资源
class HelloResource(Resource):
	def get(self):
		return {'hello': '世界'}

#第三步：将资源加载到api中的蓝图中
app.add_resource(HelloResource, '/user')
```
==注意==：如果蓝图里面有url_prefifix，那么请求url = url_prefifix + resource_ur

# 四、Request和RequestParser类
#Flask-RESTful风格编程/RequestParser类 

## 1.RequestParser类

Flask_restful 提供了RequestParser 类，用来帮助我们检验和转换请求数据。

==方法步骤==：
1. 创建RequestParser对象
2. 向Requstparser对象中传需要校验和转换参数声明
3. 使用parse_args() 方法启动检验处理
4. 检验之后从检验结果中获取参数时可按照 字典操作或者对象属性操作
		` args.rate  args['rate'] ' 	

## 2.参数说明
#Flask-RESTful风格编程/RequestParser类/参数说明 `
### 1.required
#Flask-RESTful风格编程/RequestParser类/参数说明/required 
描述请求中是否一定要携带参数,==默认值==为required=False
- True强制要求携带参数
	若未携带参数，则校验失败，服器报错404
- False 不强制要求携带参数
	若不强制携带参数，在客户端请求未携带参数时，输出值为None
	
```python
from flask import Flask, Blueprint
from flask_restful import Resource, Api, reqparse

app = Flask(__name__)

#创建蓝图
app_bp = Blueprint('user', __name__)

#第一步：创建api接口
api = Api(app_bp)

#第二步：定义资源
class HelloResource(Resource):
	def get(self):
		return {'hello': '世界'}
		
	def post(self):
		#第一步：创建创建请求参数对象
		rq = reqparse.RequestReqparse()
		#第二步：定义参数的校验声明
		rq.add_argument('a', required=True)
		#第三步：使用parse_args()方法启动校验处理
		req = rq.parse_args()
		#第四步：从检验结果中获取参数
		result = req.a
		return {'post': 'hello!','a': result}
	
#第三步：将资源加载到api中的蓝图中
app.add_resource(HelloResource, '/user')
```

### 2.help
#Flask-RESTful风格编程/RequestParser类/参数说明/help 

==参数检验错误时返回错误的描述信息==
	`rq.add_argment('a', required=True, help='自定义错误信息') `

### 3.action
#Flask-RESTful风格编程/RequestParser类/参数说明/action 

==描述对于请求参数中出现多个同名参数时的处理方式==

- action='store' 保留出现的第一个默认
- action='append' 以列表追加保存所有同名参数的值
`rq.add_argment('a', required=True, help='自定义错误信息', action='append')`

### 4.type
#Flask-RESTful风格编程/RequestParser类/参数说明/type 

==描述参数应该匹配的数据类型==，可以使用python的标准数据类型string、int，也可使用Flask-RESTflu提供的验证方法，还可以自己定义
- 标准类型
` rq.add_argment('a', type=int, required=True, help='错误信息', action='append')`
	type=int， 表示客户端传的参数必须时整型
- Flask-RESTful提供
	检验类型方法在==flask-restful.inputs==模块中
	 - url
	 - regex(指定正则表达式)
		` from flask_restful import inputs`
		 `rq.add_argment('a',type=inputs.regex(r'^\d{2}$'))
	 - natural （自然数 0 、1、 2...）
	 - positave （正整数 1、2、3...）
	 - int_range(low, high) （整数范围）
		`rq.add_argment('a', type=inputs.int_range(1,100)) # 指的是 1-100 包含100 `
	 - boolean (布尔)
- 自定义
```python
def mobile(mobile_str):
	"""
	检验手机号格式
	:param mobile_str: str 被检验字符串
	:return: mobile_str
	"""
	if re.match(r'^1[3-9]\d{9}$', mobile_str):
	return mobile_str
	else:
		raise ValueError('{} is not a valid mobile'.format(mobile_str))
		
rp.add_argument('a', type=mobile)
```



### 5.choices
#Flask-RESTful风格编程/RequestParser类/参数说明/choices 

==参数定义选择范围==
` rq.add_argment('a', type=str, choices=['男', '女'])`


### 6.location
#Flask-RESTful风格编程/RequestParser类/参数说明/location 

==描述参数应该在请求数据中出现的位置==

```python
# Look only in the POST body
parser.add_argument('name', type=int, location='form')

# Look only in the querystring
parser.add_argument('PageSize',type=int,location='args')

# From the request headers
parser.add_argument('User-Agent', location='headers')

# From http cookies
parser.add_argument('session_id', location='cookies')

# From json
parser.add_argument('user_id', location='json')

# From file uploads
parser.add_argument('picture', location='files')
```

==也可指明多个位置==
`parser.add_argument('text',location['headers','json'])`

# 五、RESTful 的响应处理
#Flask-RESTful风格编程/RESTful的响应处理 

## 1.序列化数据
#Flask-RESTful风格编程/RESTful的响应处理/序列化数据 

==flask-restful提供了marshal工具，用来将序列化数据转化为特定格式的字典数据，以便于作为视图函数的返回值==

**有两种方式**：

==一、通过装饰器==
```python
from flask import Flask
from flask_restfule import Resource, Api, filds, marshal, marshal_with

app = Flaks(__name__)

#创建客户端对接的API
#第一步：创建restful的api
api = Api(app)

#自定义一个属性转化字典格式,为了把模型对象转换成字典
property_fields = {
	'pwd': passowrd,
	'uanem': username
	

class User(object):
	def __init__(self, user_id, username, password):
		self.user = username
		self.pwd = password
		self.uid = user_id

#第二步：定义资源
class HelloResource(Resource):
	@marshal_with(property_fields) #装饰器至少要传一个参数
	def get(self)
		user = User('张三', '123123', 1001)
		return user
		

#第三步：将资源加载到api中
api.add_resource(HelloReource, '/user')
```

==二、不用装饰器==
```python
#自定义一个属性转化字典格式,为了把模型对象转换成字典
property_fields = {
	'pwd': passowrd,
	'uanem': username
	}
	
class User(object):
	def __init__(self, user_id, username, password):
		self.user = username
		self.pwd = password
		self.uid = user_id

#第二步：定义资源
class HelloResource(Resource):
	def get(self)
		user = User('张三', '123123', 1001)
		return mashal(user, property_fields, envelope='data1')
	
```

## 2.定制返回的json格式

**需求**

==想要接口返回的JSON数据具有如下统一的格式==

`{"message": "描述信息", "data": {要返回的具体数据}}`

在接口处理正常的情况下，message返回ok即可，但是若想每个接口正确返回时 省略message字段
```python
class DemoResource(Resource):

	def get(self):

		return {'user_id':1, 'name': 'laoxiao'}
```
对于诸如此类的接口，能否在某处统一格式化成上述需求格式？
`{"message": "OK", "data": {'user_id':1, 'name': 'laoxiao'}}`

**解决**

==Flask-RESTful的Api对象提供了一个representation的装饰器，允许定制返回数据的呈现格式==

```python
api = Api(app)

@api.representation('application/json')

def handle_json(data, code, headers):

	# TODO 此处添加自定义处理
	return resp
```

Flask-RESTful原始对于json的格式处理方式如下：
`代码出处： flask_restful.representations.json`

```python
from flask import make_response, current_app
from flask_restful.utils import PY3
from json import dumps

def output_json(data, code, headers=None):

	"""Makes a Flask response with a JSON encoded body"""

	settings = current_app.config.get('RESTFUL_JSON', {})

	# If we're in debug mode, and the indent is not set, we set it to a
	# reasonable value here. Note that this won't override any existing value
	# that was set. We also set the "sort_keys" value.

	if current_app.debug:
		settings.setdefault('indent', 4)
		settings.setdefault('sort_keys', not PY3)

	# always end the json dumps with a new line
	# see https://github.com/mitsuhiko/flask/pull/1262
	dumped = dumps(data, **settings) + "\n"
	
	resp = make_response(dumped, code)
	
	resp.headers.extend(headers or {})
	
	return resp
```

==为满足需求，做如下改动即可==

```python
@api.representation('application/json')
def output_json(data, code, headers=None):
"""Makes a Flask response with a JSON encoded body"""

	# 此处为自己添加***************
	if 'message' not in data:
		data = {
		'message': 'OK from msb',
		'data': data
		}
		
	# **************************

	settings = current_app.config.get('RESTFUL_JSON', {})

	# If we're in debug mode, and the indent is not set, we set it to a
	# reasonable value here. Note that this won't override any existing value
	# that was set. We also set the "sort_keys" value.

	if current_app.debug:
		settings.setdefault('indent', 4)
		settings.setdefault('sort_keys', not PY3)
	# always end the json dumps with a new line
	# see https://github.com/mitsuhiko/flask/pull/1262
	
	dumped = dumps(data, **settings) + "\n"
	resp = make_response(dumped, code)
	resp.headers.extend(headers or {})
	return resp
	```