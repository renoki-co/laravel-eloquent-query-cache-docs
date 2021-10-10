# 🔑 Generating your own key

This is how the default key generation function looks like:

```php
public function generatePlainCacheKey(string $method = 'get', string $id = null, string $appends = null): string
{
    $name = $this->connection->getName();

    // Count has no Sql, that's why it can't be used ->toSql()
    if ($method === 'count') {
        return $name.$method.$id.serialize($this->getBindings()).$appends;
    }

    return $name.$method.$id.$this->toSql().serialize($this->getBindings()).$appends;
}
```

In some cases, like implementing your own Builder for MongoDB for example, you might not want to use the `toSql()` and use your own method of generating the per-sql key. You can do so by overwriting the `MyCustomBuilder` class `generatePlainCacheKey()` with your own one.

It is, however, highly recommended to use most of the variables provided by the function to avoid cache overlapping issues.

```php
class MyCustomBuilder implements QueryCacheModuleInterface
{
    use QueryCacheModule;

    public function generatePlainCacheKey(string $method = 'get', string $id = null, string $appends = null): string
    {
        $name = $this->connection->getName();

        // Using ->myCustomSqlString() instead of ->toSql()
        return $name.$method.$id.$this->myCustomSqlString().serialize($this->getBindings()).$appends;
    }
}
```
