# T22 shrine

> 题目：BUUCTF [WesternCTF2018]shrine

__0x00 审题__

打开题目，得到一串代码，原文是一长条，稍微改了一下：

```python
import flask 
import os 
app = flask.Flask(__name__) 
app.config['FLAG'] = os.environ.pop('FLAG') #flag所在地
@app.route('/') 
def index(): 
	return open(__file__).read() #读文件内容


@app.route('/shrine/') 
def shrine(shrine): 

	def safe_jinja(s): #jinja模板
  s = s.replace('(', '').replace(')', '') 
	blacklist = ['config', 'self'] #黑名单
  return ''.join(['{{% set {}=None%}}'.format(c) for c in blacklist]) + s
	return flask.render_template_string(safe_jinja(shrine)) 

if __name__ == '__main__': app.run(debug=True) 


```

妄图通过我少的可怜的代码知识感悟此题真意

flask，感觉存在ssti,访问/shrine/{{1*2}},返回2，可知假设成立

__0x01 解题__

1. 观察源代码

> 此题存在黑名单绕过，所以就无法使用config

* 关于如何绕过黑名单，相关的python内置函数

> url_for  flask提供的全局函数，存在__globals__键
> > __ ___globals___ __ //返回一个由当前函数可以访问到的变量，方法，模块组成的字典，不包含该函数内声明的局部变量。

2. url中输入/shrine/{{url_for.__ globals__}}

然后注意其中的：'current_app': <Flask 'app'>，flag应该在其中

3. /shrine/{{url_for.__ globals__['current_app'].config}}

得到flag


