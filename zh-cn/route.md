## 路由

when a `$_SERVER['REQUEST_URI']` is request, 我们会使用 `Phax\Foundation\Route` 来分析请求地址的信息； then we will add the uri route infomation to the `\Phalcon\Mvc\Router` (this meats we only add one route in running).

detail see the `src/tao996/Phax/Foundation/Application.php->routeWith` method.

以下为一个请求示例 `http://phadmin.test/sw/m/demo`

```
// route->urlOptions
Array
(
    [sw] => 1           # this is a workerman request
    [language] =>       # language
    [api] =>            # a view, not a api
    [project] =>        # not a App/Projects/xxx
    [module] => 1       # to App/Modules/xxx
    [path] => demo      # the module name, map to App/Modules/demo
)

// route->routerOptions
Array
(
    [pattern] => /sw/m/:module
    [paths] => Array
        (
            [module] => 1
            [controller] => index
            [action] => index
        )

    [pathsname] => Array
        (
            [module] => demo
            [controller] => index
            [action] => index
        )

    [namespace] => App\Modules\demo\Controllers
    [viewpath] => /var/www/App/Modules/demo/views
    [module] => /var/www/App/Modules/demo/Module.php
    [name] => demo
    [registerModules] => Array
        (
            [demo] => Array
                (
                    [path] => /var/www/App/Modules/demo/Module.php
                    [className] => App\Modules\demo\Module
                )

        )

    [route] => /sw/m/:module                                # use for \Phalcon\Mvc\Router
    [mainView] => /var/www/App/Modules/demo/views/index
    [pickview] => index/index
)
```

then be add to `\Phalcon\Mvc\Router`

```php
/**
 * @var \Phalcon\Mvc\Router $router
 */
$router = $di->getShared('router');
$router->setDefaultNamespace($route->routerOptions['namespace']);
$router->add($route->routerOptions['route'], $route->routerOptions['paths']);
```

ok, it works.