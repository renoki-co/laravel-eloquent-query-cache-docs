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
