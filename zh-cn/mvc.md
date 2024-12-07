### Controller

The biggest difference is that the Controller's Action should return data instead of assigning values to the view template.

when there is a view request uri, the action return value will auto render to the view; and if it's a api reuqest, the return value will handle as a json data.

```php
// the main code here
// src/tao996/Phax/Mvc/Controller.php
class Controller extends \Phalcon\Mvc\Controller {
    /**
     * 对响应数据进行处理
     * @param bool $isApi
     * @param mixed $data
     * @throws BlankException
     * @throws \Exception
     */
    protected function doResponse(bool $isApi, mixed $data): void
    {
        if ($isApi) {
            $this->json($data);
        } else {
            $this->vv->setVar('language', $this->vv->getLanguage());

            // bind the action return data to the tpl
            if (is_scalar($data)) {
                $this->vv->setVar('message', $data);
            } else {
                $this->vv->setVar(MyMvc::$prefix, $data);
            }
            $this->vv->doneViewResponse();
        }
    }

    /**
     * 处理响应，如果有自己的格式要求，则可以重写这部分的内容
     * @param Dispatcher $dispatcher
     * @return void
     * @throws BlankException
     */
    public function afterExecuteRoute(Dispatcher $dispatcher): void
    {
        if ($this->response->isSent()) {
            return;
        }
        if ($this->autoResponse) {
            $data = $dispatcher->getReturnedValue() ?: [];
            if ($this->isApiRequest()) {
                $data = $this->beforeJsonResponse($data);
                $this->doResponse(true, $data);
            } else { // if a view request
                $data = $this->beforeViewResponse($data);
                $this->doResponse(false, $data);
            }
        }
    }
}
```

you can see more demo in `src/App/xxx`

```php
namespace App\Modules\demo\Controllers;

use Phax\Mvc\Controller;

class TodoController extends Controller
{
    public function listAction(): array
    {
        return ['name' => 'todo list'];
    }
}
```

when http://localhost/m/demo/todo/list it render the view.

```
// src/App/Modules/demo/views/todo/list.phtml
<?php
/**
 * @var \Phax\Helper\MyMvc $vv
 */
?>
<h5>todo list: <span><?php echo $vv->pick('name') ?></span></h5>
```

if http://localhost/api/m/demo/todo/list it return a json data.

```json
{
    "name": "todo list"
}
```
