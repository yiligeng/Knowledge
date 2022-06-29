# FLASK多进程和配置


## 一. 主流的方式

### 1.自带的server:flask run 测试开发环境
flask run启动, 配置文件需要安装python-dotenv(相当于linux系统环境配置)
from dotenv import load_dotenv
配置.flaskenv文件如下
```
FLASK_APP = "main.py" #启动文件
FLASK_ENV = "development" #启动环境
FLASK_DEBUG = 0 #debug mode
FLASK_RUN_HOST = '0.0.0.0' # ip
FLASK_RUN_PORT = '5003' # 端口5
DATABASE_URL = ''
```

## 2. gunicorn正式环境使用
环境依旧可以使用python-dotenv, 但是端口,ip这些都没用,需要独立配置文件
需要安装pip install gunicorn
配置gunicorn.conf.py文件如下:


[模式选择推荐文](https://dev.to/lsena/gunicorn-worker-types-how-to-choose-the-right-one-4n2c)


## 三. 注意事项

(1) 图片1 vpn教程比较详细
(2) 图片2 参数设置重要
(3) 两个步骤或有重复
一定注意路由表和安全组的设置



如果您遵循本指南的制作流程而发现有问题或可以改进的流程，请提出 Issue 或 Pull request 。
