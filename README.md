# `createIndexedDBStorage()`

Persist Zustand state in IndexedDB.

## Motivation

I like Zustand. I also like storing non-serializable data in my state (think `FileSystemFileHandle` or any other non-serializable objects). It so happened that IndexedDB is one of the few storages that is able to persist such data. I built this package to make it easier to create and manage Zustand stores with non-serializable data.

## Usage

```sh
npm i zustand zustand-indexeddb
```

This package is meant to be used in combination with the `persist()` middleware.

```ts
import { createStore } from 'zustand'
import { persist } from 'zustand/middleware'
import { createIndexedDBStorage } from 'zustand-indexeddb'

const userStore = createStore(
  persist(() => initialUserState, {
    name: 'user',
    version: 0.1,
    storage: createIndexedDBStorage('my-app', 'refs')
  })
)
```

Since IndexedDB transactions are asynchronous, make sure to await any Zustand operations as well:

```ts
await userStore.setState(() => ({ name: 'John' }))
````

> Note: You don't have to await `.getState()` because it's _synchronous_ by design (the store asynchronously hydrates, then keeps its state in-memory, exposing you its latest values).

## API

### `createIndexedDBStorage(databaseName, storeName)`

- `databaseName` (`string`), the name of the IndexedDB database to open;
- `storeName` (`string`), the name of the IndexedDB store to create. Do not confuse it with Zustand's store—these have nothing to do with each other.

Calling this function returns an asynchronous Zustand storage that retrieves, writes, and deletes state in the respective IndexedDB database.

**The `name` option of your `persist()` middleware is used as the row name in the created store.** This allows you to persist multiple different Zustand stores within the same database+store compartment.
