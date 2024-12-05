## php admin toolkit

`admin` is a tool that help you to manage the project, you can run `php admin` to see help infomation.

you can view what it do by the `toolkit/__init__.php` file.

下面将演示各个命令的作用及其效果

我们使用 `LocalSer` 来表示本地开发机，执行命令以 `PS phalcon-admin>` 开始；

使用 `RemoteSer` 来表示远程服务器（安装在 `VBox` 上，IP `192.168.56.118`），执行命令以 `root@debian:phalcon-admin#` 开始；

```
# RemoteSer
# 已经安装了 `git/docker/nginx` 相关的服务
mkdir /data
cd /data

# git clone --depth=1 https://gitee.com/taoooo/phalcon-admin.git 
git clone --depth=1 https://github.com/tao996/phalcon-admin.git
```

### php admin local


* php admin local -init

初始化本地环境，会将 `xxx1.example.xxx2` 都复制一份保存为 `xxx1.xxx2`

```
PS phalcon-admin> php admin local -init

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
    [8] => success copy file {src/.env}
    [9] => success copy file {src/phpunit.xml}
    [10] => success copy file {src/config/config.php}
    [11] => success copy file {src/config/services.php}
    [12] => success copy file {src/config/migration.php}

    // see toolkit/LocalProject->replaceProjectInitFiles()
    [13] => success replace file {docker-compose.yaml} content              # maybe replace the port, APP_NAME
    [14] => success replace file {docker/nginx/sites/default.conf} content  # maybe replace image cn
    [15] => success replace file {docker/nginx/host.conf} content           # replace .example
)

// backup/tmp.php is generage when you run the command
if you repeat `php admin local --init` you can find backup/local_ymdHi dir is generate
PS phalcon-admin> ls backup/
local_2411231319  tmp.php
```

you config files will backup before you run `local init`, 
and you haven't use `-y` argument,  the config file will skip to overwrite it if exists

* php admin local -clear

preview delete the files which generate by `php admin local -init`

（将你的配置文件移动到 backup/local_xxx 目录中）

```
PS phalcon-admin> php admin local -clear
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
    [7] => src/.env
    [8] => src/phpunit.xml
    [9] => src/config/config.php
    [10] => src/config/services.php
    [11] => src/config/migration.php
)
add -y to confirm delete (auto backup to sync/local_xxx dir)

PS phalcon-admin> php admin local -clear -y
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
    [7] => success backup file {src/.env}
    [8] => success backup file {src/phpunit.xml}
    [9] => success backup file {src/config/config.php}
    [10] => success backup file {src/config/services.php}
    [11] => success backup file {src/config/migration.php}
    [12] => success delete file {.env}
    [13] => success delete file {docker-compose.yaml}
    [14] => success delete file {docker/php/php.ini}
    [15] => success delete file {docker/php/supervisord.conf}
    [16] => success delete file {docker/nginx/sites/default.conf}
    [17] => success delete file {docker/nginx/assets.conf}
    [18] => success delete file {docker/nginx/host.conf}
    [19] => success delete file {src/.env}
    [20] => success delete file {src/phpunit.xml}
    [21] => success delete file {src/config/config.php}
    [22] => success delete file {src/config/services.php}
    [23] => success delete file {src/config/migration.php}
)

PS phalcon-admin> ls backup/
local_2411231319  local_2411231328  tmp.php
```

* php admin local -backup

备份当前的配置文件

## php admin sync

用来与远程服务器交互，如果你不指定操作目录，那么默认的操作目录将为 `-dir=default`；

以下在 `LocalSer` 本地开发机上操作

```
PS phalcon-admin> php admin sync -c
Array
(
    [0] => create {D:/demo/code/phpProjects/phalcon-admin/backup/default/}, overwrite=
    [1] => success create file {.env}
    [2] => success create file {docker-compose.yaml}
    [3] => success create file {docker_php_php.ini}
    [4] => success create file {docker_php_supervisord.conf}
    [5] => success create file {docker_nginx_sites_default.conf}
    [6] => success create file {docker_nginx_assets.conf}
    [7] => success create file {docker_nginx_host.conf}
    [8] => success create file {src_.env}
    [9] => success create file {src_phpunit.xml}
    [10] => success create file {src_config_config.php}
    [11] => success create file {src_config_services.php}
    [12] => success create file {src_config_migration.php}
    [13] => create success
)



PS phalcon-admin> ls backup/
default  tmp.php

PS phalcon-admin> ls backup/default/
docker-compose.yaml       docker_nginx_sites_default.conf  src_config_config.php     src_.env
docker_nginx_assets.conf  docker_php_php.ini               src_config_migration.php  src_phpunit.xml
docker_nginx_host.conf    docker_php_supervisord.conf      src_config_services.php
ssh.php #<----- important
```

before you upload the files to the remote server, check the files。

（如果是在虚拟机中测试，则除了 `ssh.php` 其它的都不需要修改，默认就是本地开发环境使用的）

* 修改 `ssh.php` 文件
* `docker_nginx_host.conf` 服务名 `server_name` 修改成你需要的，如果需要，配置好 SSL 证书
* `docker_php_php.ini` 修改成生产环境的
* `docker_php_supervisord.conf` 生产环境需要在 `command` 后追加 `-d`
* `src_.env` 关闭 debug
* `src_config_config.php` 设置好各种密钥，修改 `demo`, `superAdmin`, `test.open` 的值；`metadata.driver` 修改为 `redis` 或其它，不再需要 `memory`
* `serc_config_services.php` 各种数据库连接信息

```
# a example of backup/default/ssh.php , connect to the remote server
<?php return array (
  'ip' => '192.168.56.118',
  'port' => 22,
  'username' => 'root',
  'password' => '123456',         // 连接到服务器，支持通过 账号/密码，或者私䄴 连接
  'private_ssh_key' => '',
  'path' => '/data/phalcon-admin',   // 远程服务器也同样 git clone 了项目
  'nginx' => '/etc/nginx/conf.d/',   // the nginx application on remoteSer path
  'certs' => ['key' => '','pem' => '', 'crt' => '',], // 当前目录下的证书会被上传到 nginx/conf.d 目录下，注意不要与远程证书文件同名，否则可能导致覆盖
  'hosts' => ['a1.test'],            // 此处域名会被添加到 /etc/hosts 文件中
  'logs' => [                        // 需要下载的日志文件
    'docker/log/nginx/access.log',
    'docker/log/nginx/error.log',
    'docker/php/php_errors.log',
    [ 'src/storage/logs']           // 使用数组来表示一个目录
  ],
  'backup' => [],                   // 其它需要备份的文件，在执行 php admin sync -d 时也会一同下载
  'projects' => [],                 // projects 由系统自动更新，用于记录推送应用（src/App/Projects/xxx）的时间
);

PS phalcon-admin> php admin sync -ping  // check the ssh.php config

Array
(
    [0] => ping success
)

PS phalcon-admin> php admin sync -u

Array
(
    [0] => 准备上传项目配置文件
    [1] => 上传成功 {/data/phalcon-admin/.env}
    [2] => 上传成功 {/data/phalcon-admin/docker-compose.yaml}
    [3] => 上传成功 {/data/phalcon-admin/docker/php/php.ini}
    [4] => 上传成功 {/data/phalcon-admin/docker/php/supervisord.conf}
    [5] => 上传成功 {/data/phalcon-admin/docker/nginx/sites/default.conf}
    [6] => 上传成功 {/data/phalcon-admin/docker/nginx/assets.conf}
    [7] => 上传成功 {/data/phalcon-admin/src/.env}
    [8] => 上传成功 {/data/phalcon-admin/src/phpunit.xml}
    [9] => 上传成功 {/data/phalcon-admin/src/config/config.php}
    [10] => 上传成功 {/data/phalcon-admin/src/config/services.php}
    [11] => 上传成功 {/data/phalcon-admin/src/config/migration.php}
    [12] => 准备上传 nginx 配置文件
    [13] => 上传成功 {/etc/nginx/conf.d/docker_nginx_phalcon-admin.conf}
    [14] => 准备上传 ssl 证书
    [15] => 准备处理 /etc/hosts 文件
)


# the remote docker_nginx_phalcon-admin.conf from the local `backup/default/docker_nginx_host.conf`
# the name `phalcon-admin` from the basename of `/data/phalcon-admin` in ssh.php `path` value
```

现在登录远程服务器 `192.168.56.118`

```
# remoteSer
root@debian:phalcon-admin# cd /data/phalcon-admin
root@debian:phalcon-admin# ls -a
.   admin   docker                       docker-compose.yaml  .env.example  .gitignore   readme.md  toolkit
..  backup  docker-compose.example.yaml  .env                 .git          LICENSE.txt  src
```

TODO

* 启动服务
* `nginx -s reload`
