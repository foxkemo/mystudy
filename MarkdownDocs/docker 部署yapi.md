拉镜像

```
docker pull registry.cn-hangzhou.aliyuncs.com/anoy/yapi
```

 

创建挂载目录

```
mkdir -p ./data/yapi/mongodata
```

运行专用mongo

```
docker run --restart always -v ./data/yapi/mongodata:/data/db -d --name yapimongo mongo
```

 

 

运行容器初始化

docker run -it --rm --link yapimongo:mongo --entrypoint npm --workdir /api/vendors registry.cn-hangzhou.aliyuncs.com/anoy/yapi run install-server

初始化管理员账号成功,账号名："admin@admin.com"，密码："ymfe.org" 

 

运行服务

```
docker run -d  --restart=always --name yapi  --link yapimongo:mongo --workdir /api/vendors  -p 3001:3000  registry.cn-hangzhou.aliyuncs.com/anoy/yapi  server/app.js
```

 