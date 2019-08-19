---
published: true
layout: post
title: flask 实现 flask Restful
subtitle: flask实现flask Restful
categories: Code
tags:
    - 程序笔记
    - LINUX 
    - flask
---
# flask 实现 flask Restful
什么是restful网上解释很多，怎么用flask实现restful例子也数不胜数，可自行查阅，本篇主要是不同风格实现restful时候的安全认证。

## 注解方式实现的安全认证，也是网上例子最多的方式
在需要安全认证的方法前加上安全认证注解：@basic_auth.login_required，这个方法在下一段代码中。
注意：flask错误码或者认证失败的状态码需要以return方式返回，如：return make_response(jsonify({'error': 'Not Find'}), 401)，而flask-restful认证失败状态码需以重写abort方式返回，这算是两种方式下返回认证失败的差异。
```
#!/usr/bin/python3  
# encoding: utf-8  
""" 
@version: v1.0 
@author: W_H_J 
@license: Apache Licence  
@contact: 415900617@qq.com 
@software: PyCharm 
@file: flaskBaseRestful.py
@time: 2019/7/3 17:30 
@describe: flask 实现 flask Restful
安全认证采用注解方式
"""
import json
import sys
import os

from flask import Flask, jsonify, abort, make_response, request

sys.path.append(os.path.abspath(os.path.dirname(__file__) + '/' + '..'))
sys.path.append("..")
from authSafety import basic_auth

app = Flask(__name__)
tasks = [
    {
        'id': 1,
        'title': u'Buy groceries',
        'description': u'Milk, Cheese, Pizza, Fruit, Tylenol',
        'done': False
    },
    {
        'id': 2,
        'title': u'Learn Python',
        'description': u'Need to find a good Python tutorial on the web',
        'done': False
    }
]


@app.route('/')
def index():
    return "Hello, World!"


@app.route('/todo/api/v1/tasks', methods=['GET'])
@basic_auth.login_required
def get_tasks():
    return jsonify({'tasks': tasks})


@app.route('/todo/api/v1/tasks/<int:task_id>', methods=['GET'])
def get_tasks_id(task_id):
    task = list(filter(lambda t: t['id'] == task_id, tasks))
    if len(task) == 0:
        abort(404)
    return jsonify({'task': task[0]})


@app.errorhandler(404)
def not_found():
    return make_response(jsonify({'error': 'Not Find'}), 404)


@app.route('/todo/api/v1/tasks/put', methods=['POST'])
def create_task():
    data = json.loads(request.data)
    if not data or not 'title' in data:
        abort(400)
    task = {
        'id': tasks[-1]['id'] + 1,
        'title': data['title'],
        'description': "",
        'done': False
    }
    tasks.append(task)
    return jsonify({'task': task}), 201


@basic_auth.error_handler
def unauthorized():
    return make_response(jsonify({'error': 'Not Find'}), 401)


if __name__ == '__main__':
    app.run(debug=True, port=8080)

```
## 安全认证方法体
采用了两种认证方式，一种为用户名密码方式认证，一种使用令牌（Token）方式认证，最后采用MultiAuth(basic_auth, token_auth)方法将两种认证综合起来，只要满足其中一种认证则认为认证通过。同户名密码方式认证中对于密码部分采用generate_password_hash（）方式进行加密，否则直接保存为明文不安全，对于密码校验，使用check_password_hash（）方法，虽然无法直接反义密码原文，但是可以采用此方法直接比对新输入的密码与原加密密码是否相同。
安全认证部分，有两点，两种方式略有不同：flask 需要以return 这种方式返回自定义异常：return make_response(jsonify({'error': 'Not Find'}), 401)；而 flask_restful 则修改abort方式返回自定义异常： abort(401, message={'error': 'Unauthorized access'})
用户名密码方式认证：如用户名：Tom,密码：111111；若请求方法中不匹配则认证失败；
令牌Token方式认证：HTTPTokenAuth(scheme='Bearer')，令牌Bearer，Token：secret-token-1，只有请求中满足这个认证，则通过，测试方法：curl -X GET -H "Authorization: **Bearer secret-token-1**" http://localhost:5000/
关于安全认证其他方法可参考：[安全认证](!https://blog.csdn.net/JackLiu16/article/details/82730727)
```
#!/usr/bin/python3  
# encoding: utf-8  
""" 
@version: v1.0 
@author: W_H_J 
@license: Apache Licence  
@contact: 415900617@qq.com 
@software: PyCharm 
@file: authSafety.py 
@time: 2019/7/4 16:00 
@describe: flask 请求安全认证
"""
import sys
import os

from flask_restful import abort
from flask_httpauth import HTTPBasicAuth, HTTPTokenAuth, MultiAuth
from werkzeug.security import generate_password_hash, check_password_hash

sys.path.append(os.path.abspath(os.path.dirname(__file__) + '/' + '..'))
sys.path.append("..")

# 用户名密码方式认证
basic_auth = HTTPBasicAuth()
# 令牌（Token）认证
token_auth = HTTPTokenAuth(scheme='Bearer')
# 以上两种多重认证-若期中之一通过，则认证通过
multi_auth = MultiAuth(basic_auth, token_auth)

users = [
    {'username': 'Tom', 'password': generate_password_hash('111111')},  # 混淆密码,对于给定的字符串，生成其加盐的哈希值
    {'username': 'kevin', 'password': generate_password_hash('123456')}
]

tokens = {
    "secret-token-1": "John",
    "secret-token-2": "Susan"
}


@token_auth.verify_token
def verify_token(token):
    """
    token认证
        curl -X GET -H "Authorization: Bearer secret-token-1" http://localhost:5000/
    :param token: Bearer secret-token-1
    :return:
    """
    if token in tokens:
        return True
    return False


@token_auth.error_handler
def unauthorized():
    """
    认证失败
        flask 需要以return 这种方式返回自定义异常
            return make_response(jsonify({'error': 'Not Find'}), 401)
        flask_restful 则修改abort方式返回自定义异常
            abort(401, message={'error': 'Unauthorized access'})
    :return:
    """
    abort(401, message={'error': 'Unauthorized access'})


@basic_auth.verify_password
def verify_password(username, password):
    """
    用户名密码认证
        curl -u Tom:111111 -i -X GET http://localhost:5000/
    :param username:
    :param password:
    :return:
    """
    for user in users:
        if user['username'] == username:
            if check_password_hash(user['password'], password):
                return True
    return False


@basic_auth.error_handler
def unauthorized():
    abort(401, message={'error': 'Unauthorized access'})
```

## flask-restful实现restful
[官网简介](!http://www.pythondoc.com/flask-restful/second.html)， 对于这部分因为有中文官网，并且介绍很详细，可以直接查阅不同方法实现。
示例代码中restful风格实现和网上例子无多大差别，主要是安全认证部分进行简单编写，因为例子中所有密码或认证都采用内存方式直接硬编码到代码中，所以AuthSafety方法中有一段读取文件加密，可以自行尝试。
对于flask-restful中安全认证，官网中介绍很简单，其实就是在类中直接声明一句：decorators = [multi_auth.login_required]  # flask_restful 安全认证方式，类似于flask注解，全局认证，详见下面代码，multi_auth采用上面代码中，联合认证方式。但是这种全局认证也有不足，比如我们只想在delete方法中加安全认证，get方法中不加怎么办，那么就不能采用全局认证方式，其是flask-restful还是基于flask的封装，所以依旧可以采用flask原生方式进行认证，如下面代码get方式中的： 

```
# @multi_auth.login_required  # 两种综合认证方式，满足其一即可
def get(self, todo_id):
```
这样便可实现只在get方式中加安全认证，其他方法自由控制了。
完整代码
```
#!/usr/bin/python3  
# encoding: utf-8  
""" 
@version: v1.0 
@author: W_H_J 
@license: Apache Licence  
@contact: 415900617@qq.com 
@software: PyCharm 
@file: flaskRestFul.py
@time: 2019/7/3 18:17 
@describe: flask_restful Restful 风格
pip install flask-restful
参考自：安全认证：https://blog.csdn.net/JackLiu16/article/details/82730727
flask_restful官方简介：http://www.pythondoc.com/flask-restful/second.html
"""
import sys
import os

from flask import Flask
from flask_restful import reqparse, abort, Api, Resource
from werkzeug.security import generate_password_hash

sys.path.append(os.path.abspath(os.path.dirname(__file__) + '/' + '..'))
sys.path.append("..")
from authSafety import multi_auth, basic_auth, token_auth

app = Flask(__name__)
api = Api(app)

TODOS = {
    'todo1': {'task': 'hello python'},
    'todo2': {'task': 'hello java'},
    'todo3': {'task': 'hello flask'},
}


# 检索是否有资源
def abort_if_todo_doesnt_exist(todo_id):
    if todo_id not in TODOS:
        abort(404, message="所查资源不存在".format(todo_id))


def not_found():
    abort(404, message={'error': 'Not Find'})


def auth_error():
    abort(404, message={"error": "username or password is None"})


parser = reqparse.RequestParser()
parser.add_argument('task')
parser.add_argument('auth_key', type=str)
parser.add_argument('auth_value', type=str)


class Todo(Resource):
    decorators = [multi_auth.login_required]  # flask_restful 安全认证方式，类似于flask注解，全局认证
    '''采用flask注解方式认证，那个方法需要认证，则将注解加到此处'''

    # @basic_auth.login_required  # 用户名密码认证方式
    # @token_auth.login_required  # token认证方式
    # @multi_auth.login_required  # 两种综合认证方式，满足其一即可
    def get(self, todo_id):
        """
        curl -X GET -H "Authorization: Bearer secret-token-1" http://localhost:5000/todos/todo1
        curl -u Tom:111111 -i -X GET http://localhost:5000/todos/todo1
        :param todo_id: todo1,检索关键字
        :return:
        """
        abort_if_todo_doesnt_exist(todo_id)  # 如果资源不存在
        return TODOS[todo_id]

    def delete(self, todo_id):
        abort_if_todo_doesnt_exist(todo_id)
        del TODOS[todo_id]
        return '', 204

    def put(self, todo_id):
        args = parser.parse_args()
        task = {'task': args['task']}
        TODOS[todo_id] = task
        return task, 201


class TodoList(Resource):
    decorators = [multi_auth.login_required]

    def get(self):
        if len(TODOS) == 0:
            not_found()  # 无资源
        return TODOS

    def post(self):
        args = parser.parse_args()
        todo_id = int(max(TODOS.keys()).lstrip('todo')) + 1
        todo_id = 'todo%i' % todo_id
        TODOS[todo_id] = {'task': args['task']}
        return TODOS[todo_id], 201


class AuthSafety(Resource):
    """认证用户注册"""

    def post(self, auth_type):
        """
        注册新用户
        :param auth_type: name：姓名密码方式注册；token：token方式注册
        :return:
        """
        args = parser.parse_args()
        auth_key = args['auth_key']
        auth_value = args['auth_value']
        if auth_key is None or auth_value is None:
            auth_error()
        if auth_type == 'name':
            with open("pws.txt", 'a', encoding='utf-8') as f:
                f.write(str({'username': auth_key, 'password': generate_password_hash(auth_value)})+'\n')
            return {"UserName": auth_key, "PassWord": auth_value, "type": "name"}, 200
        elif auth_type == 'token':
            return {"UserName": auth_key, "PassWord": auth_value, "type": "token"}, 200
        else:
            auth_error()

    def get(self, auth_type):
        dict_pwd = {}
        with open("pws.txt", 'r', encoding='utf-8') as f:
            data = f.readlines()
        for msg in data:
            msg = eval(msg)
            dict_pwd[msg['username']] = msg['password']
        return dict_pwd


# 设置路由规则
api.add_resource(TodoList, '/todos')
api.add_resource(Todo, '/todos/<todo_id>')
api.add_resource(AuthSafety, '/auth/<auth_type>')

if __name__ == '__main__':
    app.run(debug=True)

```
