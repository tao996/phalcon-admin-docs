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

php admin quick
# NOTICE: if you don't want to run `php admin quick`, you can run the below commands step by step
# cp .env.example .env
# cp docker-compose.example.yaml docker-compose.yaml
# cp src/config/config.example.php src/config/config.php

# NOTICE: if local system is Linux, run `chmod -R 777 str/storage` to make the docker images can write log here
docker compose up -d # or docker-compose up -d
```

ok, now open http://localhost:8071 `(run in php-fpm)`, http://localhost:8072 `(run in workerman)`, and `8072` is more faster than `8071`.

see the entiry `src/App/Http`,  good luck.

* 如果需要更好的控制全部的配置文件，请查看 [admin](/zh-cn/admin.md) 一节；
* if you want to build your phalcon image, please go to https://github.com/tao996/phalcon-docker-images

more info coming soon ... TODO

### PHPStorm

open the project in `PHPStorm`，将下面这些文件夹排除（right click => Make Directory as > Excluded）

* backup
* docker
* src/storage