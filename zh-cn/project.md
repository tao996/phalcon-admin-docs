## Project and Module

这个项目最开始就是为网站制作公司或者小型的创业公司准备的，方便他们快速地开发测试产品

假设你有一家网站制作公司，有很多客户，这些客户的网站，除了前台后台部分基本都相同；如果是传统的开发方式，那么你可能需要部署多个独立的站点。

```
/usr/share/nginx
    |-- customA      <-- 每个都是独立的站点，拥有相同的后台管理系统
    |-- customB
    |-- customC
    |-- ...
```

这种方式如果在更新站点就会比较麻烦啦；而使用 `phalcon-admin` 它的结构将会变成

```
/user/share/nginx
    |-- phalcaon-admin
        |-- src/App
            |-- Projects/
                |-- customA
                    |-- config/config.php |<-- 通过配置来连接不同的数据库
                |-- customB
                |-- customC
                |-- ...
            |-- Modules/
                |-- admin   |<-- 共享一个后台
```

通过这种方式你可以快速创建应用来测试市场，再配合 `php admin` 工具快速地将本地代码更新到线上；

等到 api 稳定了，再直接切换到 `workerman` 模式下，很容易就可以获得性能提升。

