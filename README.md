# redis_flush
Submodule for [Drupal Redis](https://www.drupal.org/project/redis) to add robust cache flush support.

This module is designed for Drupal sites that use a single Redis instance or at least a single Redis DB (1-16) for all Drupal cache information.

## Flush Modes
There are two flush modes. The first is a standard "flushDb" call to whatever the base Redis DB is (default = 0). This is intended for development use only as it will destroy lock information and other critical cache keys that could cause issues if flushed at the wrong time in production.

However, it could be incorporated safely into a production scheme provided it is used with caution (ie. only do a cache rebuild when the site is in maintenance mode).

The second flush made is safer as it goes bin by bin. This mode is safe to use on Redis instances that are used for purposes beyond just Drupal caching even within the same DB. This means it can leave "lock" and "queue" bins intact. It makes uses of the more performant SCAN Redis command to gather keys to delete. The DELETE command itself should not cause issues unless you have a bin with thousands and thousands of entries. This mode is intended for production use however it is always recommended that you do production cache rebuilds only in case of emergency and even then it is a good idea to put your site in maintenance mode.

## Setting redis_flush mode:

A config form will be provided but it can also be done through settings.php
