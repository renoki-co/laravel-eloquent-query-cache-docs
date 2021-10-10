# 🛑 Cache Invalidation

In the previous example with the bookshelves, you might as well invalidate tags.

```php
$shelfOneBooks = Book::where('shelf_id', 1)
    ->cacheTags(['shelf:1'])
    ->get();

$shelfTwoBooks = Book::where('shelf_id', 2)
    ->cacheTags(['shelf:2'])
    ->get();
```

Flushing is done by calling `flushQueryCache` with the respective tags.

```php
Book::flushQueryCache(['shelf:1']);
```

You can as well invalidate more tags at once if needed:

```php
Book::flushQueryCache(['shelf:1', 'shelf:2']);
```

### Tags vs SQL Hash Key

Please be careful that the same tags do not mean the same cached key. In the following example, we might have a `users` table and retrieve them by their name. 

Even when specifying the same tag, the caching will be done according to the SQL query (which is different), but we can invalidate both caches if needed later:

```php
$alice = User::whereName('Alice')
    ->cacheTags(['users'])
    ->first();

$bob = User::whereName('Bob')
    ->cacheTags(['users'])
    ->first();

Book::flushQueryCache(['users']);
```
