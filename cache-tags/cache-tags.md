# 📑 Cache Tags

Some caching stores accept tags. This is really useful if you plan on tagging your cached queries and invalidate only some of the queries when needed.

In fact, this is the recommended way of caching your queries because it will be much easier to invalidate them later, even in such complex situations where, for example, you will be handling a lot of queries, like queries for listing, retrieving by ID and eventually invalidating the listing cache upon a new record, updated record or deleted record.

The following example states queries that retrieve books based on their `shelf_id`. The books from shelf 1 will be invalidated, and the books from shelf 2 will be not.

```php
$shelfOneBooks = Book::where('shelf_id', 1)
    ->cacheTags(['shelf:1'])
    ->get();

$shelfTwoBooks = Book::where('shelf_id', 2)
    ->cacheTags(['shelf:2'])
    ->get();
```
