## php admin toolkit

`admin` is a tool that help you to manage the project, you can run `php admin` to see help infomation.

you can view what it do by the `toolkit/__init__.php` file.

下面将演示各个命令的作用及其效果，

### php admin local


* php admin local -init

初始化本地环境，会将 `xxx1.example.xxx2` 都复制一份保存为 `xxx1.xxx2`

```
root@debian:phalcon-admin# php admin local -init

Array
(
    [0] => no backup files
    [1] => success copy file {.env}
    [2] => success copy file {docker-compose.yaml}
    [3] => success copy file {docker/php/php.ini}
    [4] => success copy file {docker/php/supervisord.conf}
    [5] => success copy file {docker/nginx/sites/default.conf}
    [6] => success copy file {docker/nginx/assets.conf}
    [7] => success copy file {docker/nginx/host.conf}
    [8] => success copy file {docker/nginx/host.location.txt}
    [9] => success copy file {src/.env}
    [10] => success copy file {src/phpunit.xml}
    [11] => success copy file {src/config/config.php}
    [12] => success copy file {src/config/config-services.php}
    [13] => success copy file {src/config/migration.php}
    [14] => success copy file {src/config/project.php}
    [15] => success replace file {docker-compose.yaml} content
    [16] => success replace file {docker/nginx/sites/default.conf} content
    [17] => success replace file {docker/nginx/host.conf} content
)

// backup/tmp.php is generage when you run the command
if you repeat `php admin local --init` you can find backup/local_ymdHi dir is generate
root@debian:phalcon-admin# ls backup/
local_2411231319  tmp.php
```

you config files will backup before you run `local init`, 
and you haven't use `-y` argument,  the config file will skip to overwrite it if exists

* php admin local -clear

preview delete the files which generate by `php admin local -init`

（将你的配置文件移动到 backup/local_xxx 目录中）

```
root@debian:phalcon-admin# php admin local -clear
待删除文件列表：
Array
(
    [0] => .env
    [1] => docker-compose.yaml
    [2] => docker/php/php.ini
    [3] => docker/php/supervisord.conf
    [4] => docker/nginx/sites/default.conf
    [5] => docker/nginx/assets.conf
    [6] => docker/nginx/host.conf
    [7] => docker/nginx/host.location.txt
    [8] => src/.env
    [9] => src/phpunit.xml
    [10] => src/config/config.php
    [11] => src/config/config-services.php
    [12] => src/config/migration.php
    [13] => src/config/project.php
)
add -y to confirm delete (auto backup to sync/local_xxx dir)

root@debian:phalcon-admin# php admin local -clear -y
clear local config files success

Array
(
    [0] => success backup file {.env}
    [1] => success backup file {docker-compose.yaml}
    [2] => success backup file {docker/php/php.ini}
    [3] => success backup file {docker/php/supervisord.conf}
    [4] => success backup file {docker/nginx/sites/default.conf}
    [5] => success backup file {docker/nginx/assets.conf}
    [6] => success backup file {docker/nginx/host.conf}
    [7] => success backup file {docker/nginx/host.location.txt}
    [8] => success backup file {src/.env}
    [9] => success backup file {src/phpunit.xml}
    [10] => success backup file {src/config/config.php}
    [11] => success backup file {src/config/config-services.php}
    [12] => success backup file {src/config/migration.php}
    [13] => success backup file {src/config/project.php}
    [14] => success delete file {.env}
    [15] => success delete file {docker-compose.yaml}
    [16] => success delete file {docker/php/php.ini}
    [17] => success delete file {docker/php/supervisord.conf}
    [18] => success delete file {docker/nginx/sites/default.conf}
    [19] => success delete file {docker/nginx/assets.conf}
    [20] => success delete file {docker/nginx/host.conf}
    [21] => success delete file {docker/nginx/host.location.txt}
    [22] => success delete file {src/.env}
    [23] => success delete file {src/phpunit.xml}
    [24] => success delete file {src/config/config.php}
    [25] => success delete file {src/config/config-services.php}
    [26] => success delete file {src/config/migration.php}
    [27] => success delete file {src/config/project.php}
)

root@debian:phalcon-admin# ls backup/
local_2411231319  local_2411231328  tmp.php
```

* php admin local -backup

备份当前的配置文件

## php admin sync

用来与远程服务器交互，如果你不指定操作目录，那么默认的操作目录将为 `-dir=default`

```
root@debian:phalcon-admin# php admin sync -c
Array(
    [0] => create success
)

root@debian:phalcon-admin# ls backup/
default  local_2411231319  local_2411231328  tmp.php

root@debian:phalcon-admin# ls backup/default/
docker-compose.yaml       docker_nginx_host.location.txt   docker_php_supervisord.conf     src_config_migration.php  src_phpunit.xml
docker_nginx_assets.conf  docker_nginx_sites_default.conf  src_config_config.php           src_config_project.php    ssh.php
docker_nginx_host.conf    docker_php_php.ini               src_config_config-services.php  src_.env
```


假设您已经在本地完成项目开发，现在需要将项目推送到线上, 假设线上系统为 `debian 13`；


```
# a example that save phalcon-admin source
mkdir /data
cd /data

# git clone --depth=1 https://gitee.com/taoooo/phalcon-admin.git 
git clone --depth=1 https://github.com/tao996/phalcon-admin.git
```