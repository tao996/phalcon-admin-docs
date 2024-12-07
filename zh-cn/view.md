## View

`Notice:` if the page is empty when you request, in the controller action, print `$this->vv->route()->routerOptions` to see the view template detail.

in the view tpl, use `$vv->print()` to see what data bind in the `view` service.

#### Tips

```php
// add the $vv to your tpl
<?php
/**
 * @var $vv \Phax\Helper\MyMvc
 */
?>
```

* in the view, we use the `$vv->pick` to get the var return from the controller action return data.

* use `include` instead of `include_once`

if you use `include_once`, the content disappears on the second request when it run in workerman.

* read static files

see the `docker/nginx/assets.example.conf` for more detail.

```html
<img src='/pstatic/boyu/assets/banner1.jpg' />
<!-- 
    point to src/App/Projects/boyu/views/assets/banner1.jpg
    so put you static files in your project or module,
    make it easy to share to others.
 -->
```