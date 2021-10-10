# 🙌 Showcase

The package has the ability to track the SQL used and use it as a key in the cache storage, making the caching query-by-query a breeze, independent of your ORM usage.

By default, caching is disabled unless you specify a value for `$cacheFor` .

```php
use Rennokki\QueryCache\Traits\QueryCacheable;

class Article extends Model
{
    use QueryCacheable;

    /**
     * Specify the amount of time to cache queries.
     * Do not specify or set it to null to disable caching.
     *
     * @var int|\DateTime
     */
    public $cacheFor = 3600; // cache time, in seconds
}
```

Both of the following queries have different keys in the cache storage, thus it won't overlap with other cached queries.

```php
// For the below query, a hash will be made using the following SQL:
// SELECT * FROM articles ORDER BY created_at DESC LIMIT 1;
$latestArticle = Article::latest()->first();

// SELECT * FROM articles WHERE published = 1;
$publishedArticles = Article::wherePublished(true)->get();
```

