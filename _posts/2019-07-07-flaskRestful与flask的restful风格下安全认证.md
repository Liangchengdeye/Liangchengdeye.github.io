---
layout: post
title: flask ʵ�� flask Restful
subtitle: flask ʵ�� flask Restful
categories: Code
tags:
    - ����ʼ�
    - LINUX 
    - flask
---
# flask ʵ�� flask Restful
ʲô��restful���Ͻ��ͺܶ࣬��ô��flaskʵ��restful����Ҳ����ʤ���������в��ģ���ƪ��Ҫ�ǲ�ͬ���ʵ��restfulʱ��İ�ȫ��֤��

## ע�ⷽʽʵ�ֵİ�ȫ��֤��Ҳ�������������ķ�ʽ
����Ҫ��ȫ��֤�ķ���ǰ���ϰ�ȫ��֤ע�⣺@basic_auth.login_required�������������һ�δ����С�
ע�⣺flask�����������֤ʧ�ܵ�״̬����Ҫ��return��ʽ���أ��磺return make_response(jsonify({'error': 'Not Find'}), 401)����flask-restful��֤ʧ��״̬��������дabort��ʽ���أ����������ַ�ʽ�·�����֤ʧ�ܵĲ��졣
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
@describe: flask ʵ�� flask Restful
��ȫ��֤����ע�ⷽʽ
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
## ��ȫ��֤������
������������֤��ʽ��һ��Ϊ�û������뷽ʽ��֤��һ��ʹ�����ƣ�Token����ʽ��֤��������MultiAuth(basic_auth, token_auth)������������֤�ۺ�������ֻҪ��������һ����֤����Ϊ��֤ͨ����ͬ�������뷽ʽ��֤�ж������벿�ֲ���generate_password_hash������ʽ���м��ܣ�����ֱ�ӱ���Ϊ���Ĳ���ȫ����������У�飬ʹ��check_password_hash������������Ȼ�޷�ֱ�ӷ�������ԭ�ģ����ǿ��Բ��ô˷���ֱ�ӱȶ��������������ԭ���������Ƿ���ͬ��
��ȫ��֤���֣������㣬���ַ�ʽ���в�ͬ��flask ��Ҫ��return ���ַ�ʽ�����Զ����쳣��return make_response(jsonify({'error': 'Not Find'}), 401)���� flask_restful ���޸�abort��ʽ�����Զ����쳣�� abort(401, message={'error': 'Unauthorized access'})
�û������뷽ʽ��֤�����û�����Tom,���룺111111�������󷽷��в�ƥ������֤ʧ�ܣ�
����Token��ʽ��֤��HTTPTokenAuth(scheme='Bearer')������Bearer��Token��secret-token-1��ֻ�����������������֤����ͨ�������Է�����curl -X GET -H "Authorization: **Bearer secret-token-1**" http://localhost:5000/
���ڰ�ȫ��֤���������ɲο���[��ȫ��֤](!https://blog.csdn.net/JackLiu16/article/details/82730727)
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
@describe: flask ����ȫ��֤
"""
import sys
import os

from flask_restful import abort
from flask_httpauth import HTTPBasicAuth, HTTPTokenAuth, MultiAuth
from werkzeug.security import generate_password_hash, check_password_hash

sys.path.append(os.path.abspath(os.path.dirname(__file__) + '/' + '..'))
sys.path.append("..")

# �û������뷽ʽ��֤
basic_auth = HTTPBasicAuth()
# ���ƣ�Token����֤
token_auth = HTTPTokenAuth(scheme='Bearer')
# �������ֶ�����֤-������֮һͨ��������֤ͨ��
multi_auth = MultiAuth(basic_auth, token_auth)

users = [
    {'username': 'Tom', 'password': generate_password_hash('111111')},  # ��������,���ڸ������ַ�������������εĹ�ϣֵ
    {'username': 'kevin', 'password': generate_password_hash('123456')}
]

tokens = {
    "secret-token-1": "John",
    "secret-token-2": "Susan"
}


@token_auth.verify_token
def verify_token(token):
    """
    token��֤
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
    ��֤ʧ��
        flask ��Ҫ��return ���ַ�ʽ�����Զ����쳣
            return make_response(jsonify({'error': 'Not Find'}), 401)
        flask_restful ���޸�abort��ʽ�����Զ����쳣
            abort(401, message={'error': 'Unauthorized access'})
    :return:
    """
    abort(401, message={'error': 'Unauthorized access'})


@basic_auth.verify_password
def verify_password(username, password):
    """
    �û���������֤
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

## flask-restfulʵ��restful
[�������](!http://www.pythondoc.com/flask-restful/second.html)�� �����ⲿ����Ϊ�����Ĺ��������ҽ��ܺ���ϸ������ֱ�Ӳ��Ĳ�ͬ����ʵ�֡�
ʾ��������restful���ʵ�ֺ����������޶������Ҫ�ǰ�ȫ��֤���ֽ��м򵥱�д����Ϊ�����������������֤�������ڴ淽ʽֱ��Ӳ���뵽�����У�����AuthSafety��������һ�ζ�ȡ�ļ����ܣ��������г��ԡ�
����flask-restful�а�ȫ��֤�������н��ܼܺ򵥣���ʵ����������ֱ������һ�䣺decorators = [multi_auth.login_required]  # flask_restful ��ȫ��֤��ʽ��������flaskע�⣬ȫ����֤�����������룬multi_auth������������У�������֤��ʽ����������ȫ����֤Ҳ�в��㣬��������ֻ����delete�����мӰ�ȫ��֤��get�����в�����ô�죬��ô�Ͳ��ܲ���ȫ����֤��ʽ������flask-restful���ǻ���flask�ķ�װ���������ɿ��Բ���flaskԭ����ʽ������֤�����������get��ʽ�еģ� 

```
# @multi_auth.login_required  # �����ۺ���֤��ʽ��������һ����
def get(self, todo_id):
```
�������ʵ��ֻ��get��ʽ�мӰ�ȫ��֤�������������ɿ����ˡ�
��������
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
@describe: flask_restful Restful ���
pip install flask-restful
�ο��ԣ���ȫ��֤��https://blog.csdn.net/JackLiu16/article/details/82730727
flask_restful�ٷ���飺http://www.pythondoc.com/flask-restful/second.html
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


# �����Ƿ�����Դ
def abort_if_todo_doesnt_exist(todo_id):
    if todo_id not in TODOS:
        abort(404, message="������Դ������".format(todo_id))


def not_found():
    abort(404, message={'error': 'Not Find'})


def auth_error():
    abort(404, message={"error": "username or password is None"})


parser = reqparse.RequestParser()
parser.add_argument('task')
parser.add_argument('auth_key', type=str)
parser.add_argument('auth_value', type=str)


class Todo(Resource):
    decorators = [multi_auth.login_required]  # flask_restful ��ȫ��֤��ʽ��������flaskע�⣬ȫ����֤
    '''����flaskע�ⷽʽ��֤���Ǹ�������Ҫ��֤����ע��ӵ��˴�'''

    # @basic_auth.login_required  # �û���������֤��ʽ
    # @token_auth.login_required  # token��֤��ʽ
    # @multi_auth.login_required  # �����ۺ���֤��ʽ��������һ����
    def get(self, todo_id):
        """
        curl -X GET -H "Authorization: Bearer secret-token-1" http://localhost:5000/todos/todo1
        curl -u Tom:111111 -i -X GET http://localhost:5000/todos/todo1
        :param todo_id: todo1,�����ؼ���
        :return:
        """
        abort_if_todo_doesnt_exist(todo_id)  # �����Դ������
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
            not_found()  # ����Դ
        return TODOS

    def post(self):
        args = parser.parse_args()
        todo_id = int(max(TODOS.keys()).lstrip('todo')) + 1
        todo_id = 'todo%i' % todo_id
        TODOS[todo_id] = {'task': args['task']}
        return TODOS[todo_id], 201


class AuthSafety(Resource):
    """��֤�û�ע��"""

    def post(self, auth_type):
        """
        ע�����û�
        :param auth_type: name���������뷽ʽע�᣻token��token��ʽע��
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


# ����·�ɹ���
api.add_resource(TodoList, '/todos')
api.add_resource(Todo, '/todos/<todo_id>')
api.add_resource(AuthSafety, '/auth/<auth_type>')

if __name__ == '__main__':
    app.run(debug=True)

```
