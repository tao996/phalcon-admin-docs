## DB Migration

为了适应 phalcon-admin 项目，对原来的 [migrations](https://github.com/phalcon/migrations) 进行了修改。

当执行 `php artisan migration` 时，默认读取的配置顺序如下

1. 通过命令行来指定数据库文件 `php artisan migration --config=yourDbConfigFile`
2. 如果 `src/config/migration.php` 文件存在，则使用它
3. 读取 `src/config/config.php` 中，数据库的配置

```
# entry your php image service
docker-compose exec phalcon-admin-php sh

/var/www # php artisan migration


Phalcon Migrations

Help:
  Generates/Run a Migration for phalconX project;

Usage: Generate a Migration
  migration generate|g            # generate all tables, default save in storage/data/migration;
  
  examples:
  migration g --p=city            # generate for project city, only export city_ structure, save in app/Http/Projects/xxx/data/migration
  migration g --m=demo --datas    # generate for modules demo, export demo_ structure and data, save in app/Modules/xxx/data/migration
  migration g --table=demo_,tao_  # generate tables prefix with demo_ or tao_
  migration g --table=demo_,tao_ --datas  # generate tables prefix with demo_ or tao_ and tables data
  migration g --table=demo_,tao_ --datas=demo_*  # generate tables prefix with demo_ or tao_ and only demo_ data

Usage: Run a Migration
  migration run|r        # import tables(data) from generated migrations, default load from storage/data/migration
  
  examples:
  migration r --config=config/migration.php        # import tables(data) to database, db setting from config/migration.php

Usage: List all available migrations
  migration list|l

Arguments:
  help  Shows this help text

Options:
 --p=ProjectName                 导出 app/Http/Projects 目录下指定的项目 (save in app/Http/Projects/xxx/data/migration)
 --m=ModuleName                  导出 app/Modules 目录指定的模块 (save in app/Modules/xxx/data/migration)
 --directory=str                 migration 数据所在的目录，默认为 storage/data/migration
 --table[=str]                   导出表结构，默认为 @；如果需要导出多个表，则使用逗号进行分割；支持前缀 xxx_；会被 --p|--m 覆盖;(不需要添加*号)
 --datas                         同时导出表数据，默认为跟随 table 参数；如果需要导出多个表，则使用逗号进行分割；支持前缀 xxx_*;(需要添加*号)
 --version=str                   当前版本号，默认为 1.0.0
 --data=always|oncreate          oncreate(import data if table not exists); how to restore data when `migrate run`
 --config=str                    read to db config file when generate or run action;
 --force                         Forces to overwrite existing migrations
 --ts-based                      Timestamp based migration version
 --help                          Shows this help [optional]
Documentation
  https://docs.phalcon.io/latest/db-migrations/
```

我们使用最多的是 `migration g --m=ModuleName --datas` 导出模块的结构和数据； `migration r --m=ModuleName -datas` 导入模块的结构和数据；

how we use the db config data 

```php
// src/phar-src/phalcon-migrations/src/Console/Commands/Migration.php
// different with https://github.com/phalcon/migrations/blob/master/src/Console/Commands/Migration.php

if ($configFile = $this->parser->get('config')) { // use the --config=file
    $pathConfigFile = PATH_ROOT . ltrim($configFile);
    if (!file_exists($pathConfigFile)) {
        throw new \Exception($pathConfigFile . ' not exists');
    } else {
        $dbConfig = include_once $pathConfigFile;
    }
} elseif (file_exists(PATH_CONFIG . 'migration.php')) { // read from src/config/migration.php
    $data = include_once PATH_CONFIG . 'migration.php';
    $dbConfig = ['database'=> $data['database'] ];
    if (!empty($data[$action])){
        $dbConfig['database'] = array_merge($dbConfig['database'],$data[$action]);
    }
} else { // read from src/config/config.php
    $dbDriver = $this->mvc->config()->path('database.default');
    $dbConfig = [
        'database' => array_merge(
            ['adapter' => $dbDriver],
            $this->mvc->config()->path('database.stores.' . $dbDriver)->toArray()
        ),
    ];
}

$config = new Config($dbConfig);
```

### practice

This is just a practice, let you know the `php artisan migration` command result, so we use a `host.docker.internal`.

```php
// src/config/migration.php
return [
    'g' => [], 
    'r' => [], 
    'database' => [
        'adapter' => 'mysql',
        'host' => 'host.docker.internal',
        'port' => 3306,
        'dbname' => 'docker_phalcon_github', // import from docker/mysql/sql/001-create-tables.sql
        'username' => 'demo',
        'password' => '123456',
        'charset' => 'utf8mb4',
        'collation' => 'utf8mb4_unicode_ci',
        'prefix' => '',
        'options' => [
            \PDO::MYSQL_ATTR_INIT_COMMAND => "SET NAMES 'UTF8'",
            \PDO::ATTR_EMULATE_PREPARES => false,
            \PDO::ATTR_ERRMODE => \PDO::ERRMODE_EXCEPTION,
            \PDO::ATTR_DEFAULT_FETCH_MODE => \PDO::FETCH_ASSOC
        ]
    ]
];
```
1. export structs and datas

```
/var/www # php artisan migration g -m=demo --datas

Phalcon Migrations
                            
  Success: Version 1.0.0 was successfully generated
```

`src/App/Modules/demo/data/migration/1.0.0` has been creted.

```
/var/www # ls App/Modules/demo/data/migration/1.0.0/
demo_article.dat          demo_article_migrate.dat  demo_cat.dat              demo_profile.dat          demo_role.dat             demo_user.dat             demo_user_role.dat
demo_article.php          demo_article_migrate.php  demo_cat.php              demo_profile.php          demo_role.php             demo_user.php             demo_user_role.php
/var/www # 
```

2. create a empty database

`create database docker_phalcon_test1;` and we will import the module demo here.

edit the `src/config/migration.php`

```php
return [
    'g' => [],
    'r' => [
        'dbname' => 'docker_phalcon_test1', // <!-- add this line
    ],
    'database' => [
        'adapter' => 'mysql',
        // ...
    ]
]
```

run the `php artisan migration r -m=demo --datas`

```
/var/www # php artisan migration r -m=demo

Phalcon Migrations   
  Success: Version 1.0.0 was successfully migrated
```

all the `tables/datas` has been import to `docker_phalcon_test1`. and `App/Modules/demo/data/migration/.phalcon/migration-version` file has created.

if you repeat the command

```
/var/www # php artisan migration r -m=demo

Phalcon Migrations                       
  Info: Everything is up to date  
```