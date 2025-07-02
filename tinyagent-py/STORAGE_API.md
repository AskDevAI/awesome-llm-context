# TinyAgent Storage Submodule API Documentation

## Base Storage Interface (base.py)
Defines abstract methods for storage backends.

### Class BaseStorage
Abstract storage interface.

#### Methods
- async save(key: str, data: Any) → None: Persist data by key.
- async load(key: str) → Any: Retrieve data by key.
- async delete(key: str) → None: Remove stored data.
- async list_keys() → List[str]: List all keys.

## RedisStorage (redis_storage.py)
Persistent storage using Redis.

**Constructor**
```python
RedisStorage(redis_url: str)
```

#### Methods
- Inherits BaseStorage methods.

## PostgresStorage (postgres_storage.py)
Persistent storage using PostgreSQL.

**Constructor**
```python
PostgresStorage(dsn: str)
```

#### Methods
- Inherits BaseStorage methods.

## JsonFileStorage (json_file_storage.py)
File-based storage using JSON.

**Constructor**
```python
JsonFileStorage(path: str)
```

#### Methods
- save(key: str, data: Any) → None
- load(key: str) → Any
- delete(key: str) → None
- list_keys() → List[str]

## SqliteStorage (sqlite_storage.py)
File-based storage using SQLite.

**Constructor**
```python
SqliteStorage(db_path: str)
```

#### Methods
- save(key: str, data: Any) → None
- load(key: str) → Any
- delete(key: str) → None
- list_keys() → List[str]