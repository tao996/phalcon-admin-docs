## Workerman support

in `phalcon`, some services are depends on PHP global var, like `$_SERVER ...`, in order to make the `view/api` response data are same as `phalcon`(run in php-fpm) request in `workerman`, the `phalcon-admin` rewrite some services, you can find them in `src/tao996/Phax/Bridge`

* `HttpDi.php` replace the `Phalcon\Di\Di`, let di services are independent in every request
* `Coroutine.php` make all work together
* ...

in develop mode, you can open http://localhost:8072/sw/ direct.
but it's better to write a nginx config file in your local PC

```
// phadmin.test.conf
server {
    listen 80;
    server_name phadmin.test;

    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;  
    proxy_set_header X-Forwarded-Host $host;   
    proxy_set_header X-Forwarded-Proto $scheme;  
	
    location ^~ /sw/ {
        proxy_pass http://localhost:8072/sw/;
    }
	
	location ^~ / {
        proxy_pass http://localhost:8071/;
    }
}

// hosts
127.0.0.1 phadmin.test
```

then open the http://phadmin.test/sw/m/demo and http://phadmin.test/m/demo, you will see the result