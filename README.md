# @cocreate/crud-client

A high-performance, database-agnostic client-side CRUD engine for querying, synchronizing, and persisting application data using a single unified payload object. It provides an offline-first architecture, transparent synchronization between local and remote storage providers, and seamless integration with the CoCreate real-time collaboration ecosystem. A single CRUD request can target one or many storage providers simultaneously, allowing applications to maintain synchronized data across distributed databases with minimal application logic.

---

## Documentation

For complete API references, storage adapter configuration, synchronization strategies, and advanced examples, refer to the CoCreate CRUD Client documentation.

## Table of Contents

* [Features](#features)
* [Installation](#installation)
* [Usage](#usage)
* [How it Works](#how-it-works)
* [Architecture and Payload Specs](#architecture-and-payload-specs)
* [Offline Synchronization](#offline-synchronization)
* [Storage Providers](#storage-providers)
* [Announcements](#announcements)
* [Roadmap](#roadmap)
* [How to Contribute](#how-to-contribute)
* [License](#license)

---

## Features

* **Unified CRUD API:** Create, read, update, and delete data using a consistent payload structure regardless of the underlying storage provider.
* **Multi-Storage Execution:** Execute a single CRUD operation against one or many storage providers simultaneously.
* **Parallel Database Synchronization:** When multiple storage providers are specified, operations execute concurrently to keep distributed databases synchronized.
* **Offline-First Architecture:** Continue reading and writing data while offline using local storage providers.
* **Real-Time Synchronization:** Seamlessly integrates with the CoCreate Socket ecosystem to synchronize data across clients and servers.
* **Database Agnostic:** Supports IndexedDB, SQLite, MongoDB, PostgreSQL, MySQL, Firestore, and additional adapters through a unified payload format.
* **Optimistic Updates:** Local operations execute immediately while remote synchronization occurs in the background.
* **Stateless Operations:** Every CRUD request is completely self-contained, making requests easy to serialize, queue, retry, and replay.

---

## Installation

```bash
npm install @cocreate/crud-client
```

---

## Usage

### Initialize the Client

```javascript
import crud from "@cocreate/crud-client";

await crud.init({
	organization_id: "64b9a32e18f21bc56789abcd",
	host: window.location.host
});
```

---

### Read Data

```javascript
const response = await crud.send({
	method: "object.read",
	array: "users"
});

console.log(response);
```

---

### Read from a Specific Storage Provider

```javascript
const response = await crud.send({
	method: "object.read",
	array: "users",
	storage: "mongodb"
});
```

When `storage` is a string, the operation executes only against the specified storage provider.

---

### Create Data in a Single Storage Provider

```javascript
await crud.send({
	method: "object.create",
	array: "users",
	storage: "mongodb",
	object: [{
		name: "John Doe",
		email: "john@example.com"
	}]
});
```

---

### Create Data in Multiple Storage Providers

```javascript
await crud.send({
	method: "object.create",
	array: "users",
	storage: [
		"mongodb",
		"sqlite"
	],
	object: [{
		name: "John Doe",
		email: "john@example.com"
	}]
});
```

When `storage` is an array, the same payload is executed in parallel across every matching storage provider. This allows applications to synchronize data across multiple databases using a single CRUD request.

---

### Update Data

```javascript
await crud.send({
	method: "object.update",
	array: "users",
	storage: [
		"mongodb",
		"sqlite"
	],
	object: [{
		_id: "64b9a32e18f21bc56789abcd",
		status: "active"
	}]
});
```

---

### Delete Data

```javascript
await crud.send({
	method: "object.delete",
	array: "users",
	storage: "mongodb",
	object: [{
		_id: "64b9a32e18f21bc56789abcd"
	}]
});
```

---

## How it Works

1. Every CRUD request is expressed as a standardized payload object.
2. The client determines which storage providers should process the request.
3. Local providers execute immediately for fast application responsiveness.
4. Remote providers transmit requests through the active CoCreate communication layer.
5. When multiple storage providers are specified, operations execute concurrently.
6. Responses are normalized into a consistent result regardless of the underlying database technology.
7. Offline mutations are queued automatically and synchronized once connectivity is restored.

---

## Architecture and Payload Specs

### Unified Payload Object Schema

| Property          | Type                      | Description                                                                                                    |
| ----------------- | ------------------------- | -------------------------------------------------------------------------------------------------------------- |
| `method`          | `String`                  | CRUD operation (`object.create`, `object.read`, `object.update`, `object.delete`, `array.read`, etc.).         |
| `array`           | `String`                  | Collection, table, or dataset name.                                                                            |
| `object`          | `Array<Object>`           | Documents to create, update, or delete.                                                                        |
| `storage`         | `String \| Array<String>` | Target storage provider(s). When an array is provided, all matching providers execute the request in parallel. |
| `filter`         | `Object`                  | Query filters, limits, sorting, indexes, projections, and search criteria.                                     |
| `organization_id` | `String`                  | Multi-tenant workspace identifier.                                                                             |

---

## Offline Synchronization

The CRUD Client follows an offline-first execution model.

When connectivity is unavailable:

* Reads continue using local storage providers.
* Writes are committed locally.
* Pending mutations are automatically queued.
* Queued operations synchronize when connectivity returns.
* Applications remain responsive without requiring constant network connectivity.

---

## Storage Providers

The `storage` property determines where CRUD operations execute.

A storage may reference a configured provider such as MongoDB, SQLite, IndexedDB, PostgreSQL, or any custom adapter.

```javascript
storage: "primary"
```

Storage names are aliases defined by your application configuration.

Multiple storage providers can be targeted simultaneously.

```javascript
storage: [
	"primary",
	"backup",
	"analytics"
]
```

or

```javascript
storage: [
	"mongodb",
	"sqlite"
]
```

When an array of storage providers is supplied, the CRUD Client executes the same operation concurrently across every matching provider, allowing applications to maintain synchronized data across multiple databases with a single request.

---

## Announcements

Release notes, new features, breaking changes, and migration guides are published in the project's CHANGELOG and GitHub Releases.

---

## Roadmap

Upcoming enhancements include:

* CRDT-based conflict resolution
* Background synchronization workers
* Delta synchronization
* Transaction batching
* Graph query support
* Pluggable storage adapters
* Edge synchronization
* Advanced conflict resolution strategies

---

## How to Contribute

We encourage contributions to our libraries (you might even score some nifty swag). Please see our **CONTRIBUTING.md** guide for details. We want this library to be community-driven and CoCreate-led.

Help prioritize development by creating issues, submitting pull requests, reviewing existing discussions, or participating in feature requests.

---

## License

This software is dual-licensed under the GNU Affero General Public License version 3 (AGPLv3) and a commercial license.

* **Open Source Use:** For open-source projects and non-commercial use, this software is available under the AGPLv3. For the full license text, see the LICENSE file.
* **Commercial Use:** Commercial organizations intending to use this software outside the AGPL requirements must obtain a commercial license through CoCreate.
