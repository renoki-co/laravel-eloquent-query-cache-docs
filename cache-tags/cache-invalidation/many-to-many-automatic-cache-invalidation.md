# 🤔 Many-to-Many Automatic Cache Invalidation

Automatic invalidation can be quite tricky for Many-To-Many relationships because natively, there is not a specific event that gets triggered upon `attach`, `detach`, `sync` or other many-to-many variations called directly on the relation method.

For many-to-many automatic cache invalidation, a package that will enable events for the many to many relationships is needed: [chelout/laravel-relationship-events](https://github.com/chelout/laravel-relationship-events)

### Installing the package

```bash
composer require chelout/laravel-relationship-events
```

Given the Laravel documentation's example on pivots, we might have the same example with `User` and `Role`.

**Note: The `HasBelongsToManyEvents` trait is the one that will respond to `BelongsToMany`. Read more about Laravel Relationship Events to use the correct trait for your relationship:**

* ****[**belongsToMany**](https://github.com/chelout/laravel-relationship-events/blob/master/doc/3-many-to-many.md#belongstomany)****
* ****[**morphToMany**](https://github.com/chelout/laravel-relationship-events/blob/master/doc/7-many-to-many-polymorphic.md#morphtomany)****

For the observer to work, `\Chelout\RelationshipEvents\Traits\HasRelationshipObservables` must also be implemented in the parent model (in this example, it's `User`).

```php
use Rennokki\QueryCache\Traits\QueryCacheable;

class Role extends Model
{
    use QueryCacheable;
}
```

```php
use Chelout\RelationshipEvents\Concerns\HasBelongsToManyEvents;
use Chelout\RelationshipEvents\Traits\HasRelationshipObservables;
use Rennokki\QueryCache\Traits\QueryCacheable;

class User extends Model
{
    use HasBelongsToManyEvents;
    use HasRelationshipObservables;
    use QueryCacheable;
    
    /**
     * Invalidate the cache automatically
     * upon update in the database.
     *
     * @var bool
     */
    protected static $flushCacheOnUpdate = true;
    
    /**
     * The roles this user has.
     *
     * @return mixed
     */
    public function roles()
    {
        return $this->belongsToMany(Role::class);
    }
    
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
        // When the Many To Many relations are being attached/detached or updated,
        // $pivotedModels will contain the list of models that were attached or detached.
        
        // Based on the roles attached or detached,
        // the following tags will be invalidated:
        // ['user:1:roles:1', 'user:1:roles:2', ..., 'user:1:roles']

        if ($relation === 'roles') {
            $tags = array_reduce($pivotedModels->all(), function ($tags, Role $role) {
                return array_merge($tags, ["user:{$this->id}:roles:{$role->id}"]);
            }, []);
            
            return array_merge($tags, [
                "user:{$this->id}:roles",
            ]);
        }
        
        return [
            //
        ];
    }
}
```

In the example above, the `flushCacheOnUpdate` should be on the model from which you call the many-to-many relation. This will trigger automatic cache invalidation for the following query:

```php
$roles = $user->roles()
    ->cacheTags(["user:{$user->id}:roles"])
    ->get();
```

Upon triggering the `->attach()` method, the cache associated with the tag `user:[user_id]:roles` will be flushed.

```php
$user->roles()->attach(1);
```

As you might have seen, the `getCacheTagsToInvalidateOnUpdate` will also pass the related model on which the many-to-many relation will take action. In the above example, it will invalidate the roles of the user by using a [Selective cache invalidation](full-automatic-invalidation.md#selective-cache-invalidation) for specific tags.
