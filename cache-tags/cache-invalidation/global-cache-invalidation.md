# 🌍 Global Cache Invalidation

Each model that uses the query caching trait has an array attached that's called `getCacheBaseTags()`. This is useful to attach whenever we want to flush all queries for a specific model, without passing tags.

By default, the cache base tags array contains only one element, and it's the class name (as string). This way, Eloquent Query Cache will attach the tags on any query and whenever you want to flush all queries' caches (without flushing the entire caching storage), consider using this method:

```php
class User extends Model
{
    use QueryCacheable;

    /**
     * Set the base cache tags that will be present
     * on all queries.
     *
     * @return array
     */
    protected function getCacheBaseTags(): array
    {
        return [
            'custom_user_tag',
        ];
    }
}
```

From now, each query made from the `User` model will contain a tag called `custom_user_tag` and the package will know what to flush:

```php
User::flushQueryCache();
```
