# ✨ Implement Caching

Since this package modifies the `newBaseQueryBuilder()` in the model, having multiple traits that modify this function will lead to overlap.

This can happen in case you are creating your own Builder class for another database driver or simply to ease out your app query builder for more flexibility.

To solve this, all you have to do is to add the `\Rennokki\QueryCache\Traits\QueryCacheModule` trait and the `\Rennokki\QueryCache\Contracts\QueryCacheModuleInterface` interface to your `Builder` class.

```php
use Rennokki\QueryCache\Traits\QueryCacheModule;
use Illuminate\Database\Query\Builder as BaseBuilder; // the base laravel builder
use Rennokki\QueryCache\Contracts\QueryCacheModuleInterface;

class MyCustomBuilder implements QueryCacheModuleInterface
{
    use QueryCacheModule;

    // the rest of the logic here.
}
```

```php
trait MyBuilderTrait
{
    protected function newBaseQueryBuilder()
    {
        return new MyCustomBuilder(
            //
        );
    }
}
```

Make sure that the model will no longer use the original `QueryCacheable` trait and use the custom one instead:

```php
class CustomModel extends Model
{
    use MyBuilderTrait;
}

CustomModel::cacheFor(30)->customGetMethod();
```

