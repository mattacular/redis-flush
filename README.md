# redis_flush
Submodule for [Drupal Redis](https://www.drupal.org/project/redis) to add robust cache flush support.

This module is designed for Drupal sites that use a single Redis instance or at least a single Redis DB (1-16) for all Drupal cache information.

## Flush Modes
There are two flush modes. The first simply issues a [FLUSHDB](https://redis.io/commands/flushdb) command to whatever the base Redis DB is (default = 0) set to for your client. You can configure this for the Drupal Redis module in settings.php:

```php
$settings['redis.connection']['base'] = 0;
$settings['redis_flush']['mode'] = 'develop'
```

As the name implies, this mode is intended for development use only as it will destroy lock and queue information and other potentially critical cache keys that could cause issues if flushed at the wrong time in production. If you are not using Redis for such purposes though, it may be safe but is not recommended.

The second flush (the default) is safer as it separates the deletes into batches by cache bin:

```php
$settings['redis.connection']['base'] = 0;
$settings['redis_flush']['mode'] = 'bin'
```

This mode is safe to use on Redis instances that are used for purposes beyond just Drupal caching even within the same DB as it respects Drupal's `$settings['cache_prefix']` value. This mode automatically avoids "lock" and "queue" cache items. If you would like to omit other bins, you may provide them via settings as well. For example,:

```php
$settings['redis_flush']['keep_bins'] = array('menu', 'discovery');
```

This mode makes use of the more performant [SCAN](https://redis.io/commands/scan) command to gather keys to delete. The [DEL](https://redis.io/commands/del) command itself should not cause issues unless you have a particularly large bin. This mode is designed for production usage but it is always recommended to put your site in maintenance mode before performing a cache rebuild regardless of whether you are using Redis or this module.

**Warning:** An especially large cache store may cause a slow-down (ie. block Redis) even using this default bin-based delete mode.
