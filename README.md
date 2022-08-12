# Boathouse Pipeline

SSH进入 boathouse-pipeline-agent
密码： smartide123.@IDE
```Bash
ssh smartide@boathouse-pipeline-agent -p 6822
```

k8s创建脚本

```bash
kind create cluster \
    --image registry.cn-hangzhou.aliyuncs.com/smartide/nestybox-kindestnode:v1.20.7
```

构建脚本

```bash
PATH=/home/smartide/.nvm/versions/node/v14.17.6/bin:/opt/maven/bin:/home/smartide/.nvm/versions/node/v14.17.6/bin:/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:/home/smartide/.nvm/versions/node/v16.9.1/bin
printenv
pwd
java --version
mvn --version
mvn package
cd ruoyi-ui 
npm config set registry https://registry.npmmirror.com
npm install
npm run build:prod
cd ../docker
mkdir nginx/html && cd nginx/html && mkdir dist && cd ../../
./copy.sh
cd mysql
docker build -t registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-mysql:latest .
cd ../redis
docker build -t registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-redis:latest .
cd ../nacos
docker build -t registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-nacos:latest .
cd ../nginx
docker build -t registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-nginx:latest .
cd ../ruoyi/auth
docker build -t registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-auth:latest .
cd ../gateway
docker build -t registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-gateway:latest .
cd ../modules/system
docker build -t registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-modules-system:latest .
cd ../../../
docker compose -f docker-compose-test-pipeline.yml up -d
cd ../k8s/prod
kind load docker-image registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-mysql:latest
kind load docker-image registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-nacos:latest
kind load docker-image registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-redis:latest
kind load docker-image registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-gateway:latest
kind load docker-image registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-auth:latest
kind load docker-image registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-modules-system:latest
kind load docker-image registry.cn-hangzhou.aliyuncs.com/boathouse/ruoyi-nginx:latest
kubectl apply -f .
```