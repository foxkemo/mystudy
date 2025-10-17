Docker CLI 命令体系

docker
│
├── container （容器管理）
│   ├── ls / ps          # 列出容器
│   ├── create           # 创建容器（不启动）
│   ├── run              # 创建并启动容器
│   ├── start            # 启动已创建的容器
│   ├── stop             # 停止容器
│   ├── restart          # 重启容器
│   ├── rm               # 删除容器
│   ├── logs             # 查看容器日志
│   └── exec             # 在容器中执行命令
│
├── image （镜像管理）
│   ├── ls / list         # 列出镜像
│   ├── pull             # 拉取镜像
│   ├── push             # 推送镜像
│   ├── build            # 构建镜像
│   └── rm / rmi         # 删除镜像
│
├── volume （数据卷管理）
│   ├── ls               # 列出卷
│   ├── create           # 创建卷
│   ├── rm               # 删除卷
│   └── inspect          # 查看卷信息
│
├── network （网络管理）
│   ├── ls               # 列出网络
│   ├── create           # 创建网络
│   ├── rm               # 删除网络
│   └── inspect          # 查看网络信息
│
├── system （系统管理）
│   ├── df               # 查看空间占用
│   ├── prune            # 清理未使用资源
│   └── info             # 查看 Docker 系统信息
│
├── exec / attach / login  # 容器操作（交互）
│
└── help                  # 查看帮助