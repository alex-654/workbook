first run console redis client
```bash
redis-cli
```

Then use commands like below  
Check which DB has keys
```redis
INFO keyspace
```

Choose DB
```redis
SELECT 3
```

List of all keys
```redis
KEYS *
```

List of all keys by pattern
```redis
KEYS *pattern*
```

delete key
```redis
DEL key_name
```

Time to live in seconds of key
```redis
TTL key_name
```