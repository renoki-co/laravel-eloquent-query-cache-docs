# 🍸 Automatic Invalidation

To speed up the scaffolding of invalidation within your app, you can specify the model to auto-flush the cache upon any time records gets created, updated, or deleted.

```php
class Page extends Model
{
    use QueryCacheable;

    /**
     * Invalidate the cache automatically
     * upon update in the database.
     *
     * @var bool
     */
    protected static $flushCacheOnUpdate = true;
}
```

When you set up the `$flushCacheOnUpdate` property, the package attaches an observer to your model and any `created`, `updated`, `deleted`, `forceDeleted` or `restored` event will trigger the cache invalidation.

**In order to make auto-flush work, you will need at least one base tag. Out-of-the-box, the model has a base tag set. In some cases, if you have overwritten the \`getCacheBaseTags()\` with an empty array, it might not work.**

### Selective cache invalidation

In some cases, you might not want to invalidate the whole cache of a specific model. Perhaps you got two queries that run individually and want to invalidate the cache only for them and not for all the models.

To do this, overwrite your `getCacheTagsToInvalidateOnUpdate()` method in your model:

```php
class Page extends Model
{
    use QueryCacheable;

    /**
     * Specify the amount of time to cache queries.
     * Do not specify or set it to null to disable caching.
     *
     * @var int|\DateTime
     */
    public $cacheFor = 3600;

    /**
     * Invalidate the cache automatically
     * upon update in the database.
     *
     * @var bool
     */
    protected static $flushCacheOnUpdate = true;

    /**
     * When invalidating automatically on update, you can specify
     * which tags to invalidate.
     *
     * @param  string|null  $relation
     * @param  \Illuminate\Database\Eloquent\Collection|null  $pivotedModels
     * @return array
     */
    public function getCacheTagsToInvalidateOnUpdate($relation = null, $pivotedModels = null): array
    {
        return [
            "page:{$this->id}",
            'pages',
        ];
    }
}
```

The idea behind this configuration is that we can bind `page:[some_id]` tag (where `some_id` is a given ID from the input) to the queries where we try to get the page by ID and attach the`pages` tag to the queries where we get all the pages.

```php
// i.e. $pageId is the ID from the router

$pages = Page::cacheTags(['pages'])->get();

$page = Page::cacheTags(["page:{$pageId}"])->first();
```

When the page model gets changed, it will invalidate the query in which we retrieve the page and the one that lists the page, thus invalidating exactly the two queries.

```php
$page = Page::find($pageId);

$page->update([
    'name' => 'Getting Started',
]);
```

**Please keep in mind: Setting `$flushCacheOnUpdate` to `true` and not specifying individual tags to invalidate will lead to a global flush for that model since the default tags to invalidate are the base tags and you need at least one tag to invalidate.**

**Not specifying a tag to invalidate fallbacks to the set of base tags, thus leading to Full Automatic Invalidation.**
