# ⚡ Query Caching

Caching options can either be defined at the model level for default values or during the query.

For example, we can set default values for a model called `Book`:

```php
class Book extends Model
{
    /**
     * Specify the amount of time to cache queries.
     * Do not specify or set it to null to disable caching.
     *
     * @var int|\DateTime
     */
    public $cacheFor = 3600;

    /**
     * The tags for the query cache. Can be useful
     * if flushing cache for specific tags only.
     *
     * @var null|array
     */
    public $cacheTags = ['books'];

    /**
     * A cache prefix string that will be prefixed
     * on each cache key generation.
     *
     * @var string
     */
    public $cachePrefix = 'books_';

    /**
     * The cache driver to be used.
     *
     * @var string
     */
    public $cacheDriver = 'dynamodb';
}
```

### Enable/Disable caching on-demand

When opting for default values, keep in mind that setting the `$cacheFor` value will cache all queries. To disable caching and instead use a non-caching version, consider `dontCache`:

```php
$uncachedBooks = Book::dontCache()->get();
```

Alternatively, if you want to cache queries by specifying this during the query, consider using `cacheFor()` during the query:

```php
$booksCount = Book::cacheFor(60 * 60)->count();
```

Using a DateTime instance like Carbon also works:

```php
$booksCount = Book::cacheFor(now()->addDays(1))->count();
```

### Prefixing cache

You may add a default global prefix to the model by specifying `$cachePrefix` or do it at the query level:

```php
$scifiBooks = Book::cachePrefix('scifi_')->count();
```

### Defining values dynamically

Sometimes, you may want to define the cache settings programmatically, by running business logic, over the static class properties, without having to bundle the application with repetitive logic at the query level in case you have multiple queries.

For example, if the authenticated user is an admin, disable the caching completely. The caching will stay active for the rest of the users, as normal.

You can define one of them or both of them. In case you define both, the function will be favored over the property.

```php
class Book extends Model
{
    /**
     * Specify the amount of time to cache queries.
     * Do not specify or set it to null to disable caching.
     *
     * @var int|\DateTime
     */
    public $cacheFor = 3600;

    /**
     * Specify the amount of time to cache queries.
     * Set it to null to disable caching.
     *
     * @return int|\DateTime
     */
    protected function cacheForValue()
    {
        if (optional(request()->user())->hasRole('admin')) {
            return null;
        }
        
        return $this->cacheFor;
    }
}
```

These are the following possible methods you can implement:

```php
class Book extends Model
{
    /**
     * Specify the amount of time to cache queries.
     * Do not specify or set it to null to disable caching.
     *
     * @return int|\DateTime
     */
    protected function cacheForValue()
    {
        return 3600;
    }

    /**
     * The tags for the query cache. Can be useful
     * if flushing cache for specific tags only.
     *
     * @return null|array
     */
    protected function cacheTagsValue()
    {
        return ['books'];
    }

    /**
     * A cache prefix string that will be prefixed
     * on each cache key generation.
     *
     * @return string
     */
    protected function cachePrefixValue()
    {
        return 'books_';
    }

    /**
     * The cache driver to be used.
     *
     * @return string
     */
    protected function cacheDriverValue()
    {
        return 'dynamodb';
    }
}
```
