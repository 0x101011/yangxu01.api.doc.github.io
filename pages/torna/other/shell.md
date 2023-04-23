# 常用脚本

## [#](https://torna.cn/dev/common-script.html#docker重启torna脚本)docker重启Torna脚本

```shell
#!/bin/sh

# 说明：停止并删除当前torna容器，并拉取最新版本镜像重启
# 保存文件名：docker-restart-torna.sh
# 运行方式：sh docker-restart-torna.sh [version]
#
# 参数version可选，不填默认为latest，推荐手动指定版本避免拉取到的latest还是老版本
# 如重启1.17.2版本：sh docker-restart-torna.sh 1.17.2

version="latest"

if [ -n "${1}" ];then
  version="${1}"
fi

echo "stop torna container"
docker stop torna

echo "remove torna container"
docker rm torna

echo "pull new torna image : tanghc2020/torna:${version}"
docker pull tanghc2020/torna:${version}

echo "run docker image : tanghc2020/torna:${version}"

docker run --name torna --restart=always \
  -e JAVA_OPTS="-server -Xms512m -Xmx512m" \
  -v /etc/torna/application.properties:/torna/config/application.properties \
  -d tanghc2020/torna:${version}
```

## [#](https://torna.cn/dev/common-script.html#zip本地部署重启脚本)zip本地部署重启脚本

- 用途：下载最新版本Torna并重启
- 前提：需要将配置文件放在`/etc/torna/application.properties`

```bash
#!/bin/sh

# 说明：下载最新版本Torna并重启
# 前提：需要将配置文件放在`/etc/torna/application.properties`
# 保存文件名：restart-torna.sh
# 运行方式：sh restart-torna.sh <version>
#
# 如重启1.18.2版本：sh restart-torna.sh 1.18.2

version=""

if [ -n "${1}" ];then
  version="${1}"
else
  echo "请输入版本号，如：sh restart-torna.sh 1.18.2"
  exit 1
fi

file_name="torna-${version}.zip"

folder_name="torna-${version}"

if [ -f "./$file_name" ];then
  echo "${file_name}已存在跳过下载"
else
  url="https://gitee.com/durcframework/torna/releases/download/v${version}/${file_name}"

  wget $url

  unzip $file_name
fi

cd $folder_name

echo "启动Torna"

# 重启并指定配置文件。
# startup.sh脚本第一个参数可以写JVM参数
sh startup.sh -Dspring.config.location=/etc/torna/application.properties
```