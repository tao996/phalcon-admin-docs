## Model

the `src/tao996/Phax/Mvc/Model.php` file make it easy to use Model

you can find many demo in `src/App/Modules/demo/Models/`


* 关联查询

```php
namespace App\Modules\demo\Models;

use App\Modules\demo\DemoBaseModel;

class User extends DemoBaseModel
{
    // ...

    public function articles()
    {
        return $this->hasManyPhx(Article::class);
    }

    public function profile()
    {
        return $this->hasOnePhx(Profile::class);
    }

    public function roles()
    {
        return $this->hasManyToManyPhx(Role::class,UsersRoles::class);
    }

}
```

### DB manager

`src/tao996/Phax/Db`, find test in `src/tests/Unit/tao996/phax/Db`


###### Parameter.php

generate model params, if you have use larval/Thinkphp before, you are family with it;

###### Layer.php
 
make it easy to use `https://docs.phalcon.io/latest/db-layer/`, use `YourModel::layer()` to get a instance.

* `insert(array $kv, array|null $valueTypes = null): bool`
* `update(array $updateKv,array $conditionKv,array|null $updateValueTypes = null,array|null $conditionValueTypes = null): bool`
* `incr(string $column, array $conditions, int $num = 1): bool`
* `desc(string $column, array $conditions, int $num = 1): bool`
* `delete(array $kv, array $valueTypes = []): bool`
* `execute(string $sql, true $replaceTableName = true): bool`
* `batchInsert(array $rows, string|array $fields = [], bool $transaction = true): \PDO|null`

###### QueryBuilder

query data for model, most methods will skip the query condition when the value is empty.

```php
// user query from the frontend
$queryData = [
    'name'=>'aaa',
    'age'=>0
];

Demo::queryBuilder()
    ->string('name', $queryData['name'])
    ->int('age', $queryData['name']) // skip because age is empty
    ->find();
```

###### Transaction

* `db(\Phalcon\Db\Adapter\Pdo\AbstractPdo $db, callable $handle): void`

will trigger model events

```php
Transaction::db($this->helper->mvc->db(), function () use ($user, $openidRecord, $unionidRecord) {
    $this->helper->mvc->userService()->create($user);

    $openidRecord->user_id = $user->id;
    if (!$openidRecord->save()) {
        throw new \Exception($openidRecord->getFirstError());
    }

    if ($unionidRecord) {
        $unionidRecord->user_id = $user->id;
        if (!$unionidRecord->save()) {
            throw new \Exception($unionidRecord->getFirstError());
        }
    }
});
```

* `pdo(\PDO $pdo, callable $handle): void`

origin SQL, not trigger events.

```php
Transaction::pdo($this->mvc->pdo(), function (\PDO $db) use ($tableName, $params, $handle) {
    $stmt = $db->prepare('INSERT INTO ' . $tableName . ' (created_at,version,summary) values(?,?,?)');
    if (!$stmt->execute($params)) {
        Logger::message('insert migration record failed', $db->errorInfo());
    }
    if ($db->lastInsertId() < 1) {
        throw new \Exception('could not get the lastInsertId when insert migrate record');
    }
    $handle($db);
});
```