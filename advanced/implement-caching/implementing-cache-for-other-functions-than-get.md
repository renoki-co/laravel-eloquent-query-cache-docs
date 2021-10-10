# 🎇 Implementing cache for other functions than get()

Since all of the Laravel Eloquent functions are based on it, the builder that comes with this package replaces only the `get()` one:

```php
use Illuminate\Support\Arr;

class Builder
{
    public function get($columns = ['*'])
    {
        if (! $this->shouldAvoidCache()) {
            return $this->getFromQueryCache('get', Arr::wrap($columns));
        }

        return parent::get($columns);
    }
}
```

In case that you want to cache your own methods from your custom builder or, for instance, your `count()` method doesn't rely on `get()`, you can replace it using this syntax:

```php
class MyCustomBuilder
{
    public function count()
    {
        if (! $this->shouldAvoidCache()) {
            return $this->getFromQueryCache('count');
        }

        return parent::count();
    }
}
```

In fact, you can also replace any eloquent method within your builder if you use `$this->shouldAvoidCache()` check and retrieve the cached data using `getFromQueryCache()` method, passing the method name as string, and, optionally, an array of columns that defaults to `['*']`.

Notice that the `getFromQueryCache()` method accepts a method name and a `$columns` parameter. If your method doesn't implement the `$columns`, don't pass it.

Note that some functions, like `getQueryCacheCallback()` may come with an `$id` parameter. The default behavior of the package doesn't use it, since the query builder uses `->get()` by default that accepts only columns.

However, if your builder replaces functions like `find()`, `$id` is needed and you will also have to replace the `getQueryCacheCallback()` like so:

```php
use Illuminate\Support\Arr;

class MyCustomBuilder
{
    public function getQueryCacheCallback(string $method = 'get', $columns = ['*'], string $id = null)
    {
        return function () use ($method, $columns, $id) {
            $this->avoidCache = true;

            // the function for find() caching
            // accepts different params
            if ($method === 'find') {
                return $this->find($id, $columns);
            }

            return $this->{$method}($columns);
        };
    }

    public function find($id, $columns = ['*'])
    {
        // implementing the same logic
        if (! $this->shouldAvoidCache()) {
            return $this->getFromQueryCache('find', Arr::wrap($columns), $id);
        }

        return parent::find($id, $columns);
    }
}
```
