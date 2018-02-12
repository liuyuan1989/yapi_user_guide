1.安装并启动MongoDB
# brew install mongodb

# brew services start mongodb
==> Tapping homebrew/services
Cloning into '/usr/local/Homebrew/Library/Taps/homebrew/homebrew-services'...
remote: Counting objects: 14, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 14 (delta 0), reused 8 (delta 0), pack-reused 0
{
Unpacking objects: 100% (14/14), done.
Tapped 0 formulae (43 files, 55.4KB)
==> Successfully started `mongodb` (label: homebrew.mxcl.mongodb)

2.安装YAPI
# cd ~/workspace
# mkdir yapi && cd yapi
# git clone https://github.com/YMFE/yapi.git vendors --depth=1

3.修改配置
# cp vendors/config_example.json ./config.json
# vim ./config.json

{
  "port": "3000",
  "adminAccount": "admin@admin.com",
  "db": {
    "servername": "127.0.0.1",
    "DATABASE":  "yapi",
    "port": 27017,
    "user": "test1",
    "pass": "test1"
  },
  "mail": {
    "enable": true,
    "host": "smtp.163.com",
    "port": 465,
    "from": "***@163.com",
    "auth": {
        "user": "***@163.com",
        "pass": "*****"
    }
  }
}

4.安装依赖
# cd vendors
# npm install --production --registry https://resgistry.npm.taobao.org

5.初始化
# npm run install-server

6.启动服务
# node server/app.js >> yapi.log 2>&1 &

7.浏览器
URL：127.0.0.1:3000/login
用户名：admin@admin.com
密码：ymfe.org

8.常见问题

日志描述：
> yapi@1.3.5 install-server /Users/imathliu/workspace/github/yapi/vendors
>  node server/install.js

error: MongoError: Authentication failed., mongodb Authentication failed
error: MongoError: Authentication failed.mongodb connect error
(node:11973) UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): Error: Authentication failed.
(node:11973) [DEP0018] DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code.

原因分析：
因MongoDB开启Auth认证 检查MongoDB是否已经创建相关库和配置的用户添加相关权限

解决方法：
# mongo
MongoDB shell version v3.6.2
connecting to: mongodb://127.0.0.1:27017
MongoDB server version: 3.6.2
Welcome to the MongoDB shell.

#切换admin库
> use admin
switched to db admin

#创建管理员用户myadmin并赋权
> db.createUser({user:"myadmin",pwd:"secret",roles:[{role:"root",db:"admin"}]})

Successfully added user: {
	"user" : "myadmin",
	"roles" : [
		{
			"role" : "root",
			"db" : "admin"
		}
	]
}

#检查用户
> show users
{
	"_id" : "admin.myadmin",
	"user" : "myadmin",
	"db" : "admin",
	"roles" : [
		{
			"role" : "root",
			"db" : "admin"
		}
	]
}

#创建yapi库
> use yapi
switched to db yapi

#创建用户test1并赋权
> db.createUser({user:"test1",pwd:"test1",roles:[{role:"readWrite",db:"yapi"}]})

Successfully added user: {
	"user" : "test1",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "yapi"
		}
	]
}

#检查用户
> show users
{
	"_id" : "yapi.test1",
	"user" : "test1",
	"db" : "yapi",
	"roles" : [
		{
			"role" : "readWrite",
			"db" : "yapi"
		}
	]
}

9.参考资料
9.1.https://github.com/YMFE/yapi/blob/master/doc/page/plugin/redev.md
9.2.https://github.com/YMFE/yapi/issues/103
9.3.http://blog.csdn.net/ling811/article/details/56844395
