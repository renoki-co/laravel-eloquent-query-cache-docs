# 👥 Relationships Caching

Relationships are using queries beneath. They can be intercepted and modified before the database is hit with the query. The following example needs the `Author` model (or the model associated with the `author` relationship) to include the `QueryCacheable` trait.

```php
use Rennokki\QueryCache\Traits\QueryCacheable;

class Book extends Model
{
    use QueryCacheable;
    
    public function author()
    {
        return $this->belongsTo(Author::class);
    }
}
```

```php
use Rennokki\QueryCache\Traits\QueryCacheable;

class Author extends Model
{
    use QueryCacheable;
}
```

```php
$book = Book::with([
    'author' => function ($query) {
        return $query->cacheTags(["book:{$bookId}:author"]);
    },
])->findOrFail($bookId);

$author = $book->author; // cached
```

This also works when retrieving lists:

```php
$books = Book::with([
    'buyers' => function ($query) {
        return $query->cacheTags(['book:buyers']);
    },
])->get();

foreach ($books as $book) {
    // $book->buyers
}
```

As the last example, using `load()` or `loadMissing()` also works:

```php
$book = Book::findOrFail($bookId);

$book->load([
    'buyers' => function ($query) {
        return $query->cacheTags(["book:{$bookId}:buyers"]);
    },
]);

foreach ($book->buyers as $buyer) {
    //
}
```

### Relationship Classes

Although you can alter the query during `with()` and `load()`, you can also use the `Relation` classes (like `BelongsTo` or `HasMany`) and call Query Cache methods:

```php
use Rennokki\QueryCache\Traits\QueryCacheable;

class Buyer extends Model
{
    use QueryCacheable; // really important

    // ...
}
```

```php
use Rennokki\QueryCache\Traits\QueryCacheable;

class Book extends Model
{
    use QueryCacheable;

    public function buyers()
    {
        return $this->hasMany(Buyer::class);
    }
}
```

```php
$book = Book::findOrFail($bookId);

$latestBuyer = $book->buyers()
    ->cacheTags(["book:{$bookId}:latest_buyer"])
    ->latest()
    ->get();
```
