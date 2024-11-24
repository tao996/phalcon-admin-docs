# 快速开始


the `phalcon-admin` 提供了两个克隆地址

* `https://github.com/tao996/phalcon-admin.git`
* `https://gitee.com/taoooo/phalcon-admin.git` CN


tip: all default `setting files (with .example in the file name)` is prepare for local development, 
this make you quick start this project on your local PC.

after you are family with this project, copy another pro setting file without the `.example`

```
# git clone --depth=1 https://gitee.com/taoooo/phalcon-admin.git 
git clone --depth=1 https://github.com/tao996/phalcon-admin.git
cd phalcon-admin
cp .env.example .env
cp docker-compose.example.yaml docker-compose.yaml
docker compose up -d # or docker-compose up -d
```

如果需要更好的控制全部的配置文件，请查看 [admin](/zh-cn/admin.md) 一节

### PHPStorm

open the project in `PHPStorm`，将下面这些文件夹排除（right click => Make Directory as > Excluded）

* backup
* docker
* src/storage