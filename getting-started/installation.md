# 🚀 Installation

### Installing the package

Hop into your console and install the package via Composer:

```bash
composer require rennokki/laravel-eloquent-query-cache
```

### Preparing the models

 Each model you want to cache on should use the `Rennokki\QueryCache\Traits\QueryCacheable` trait.

```php
use Rennokki\QueryCache\Traits\QueryCacheable;

class Podcast extends Model
{
    use QueryCacheable;
}
```
