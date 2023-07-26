---
contributors: Esther Agbaje, Kevin Lewis
description: Learn how to use the Directus JavaScript SDK to work with your data and files.
---

# Directus JavaScript SDK

Learn how to use the new Directus JavaScript SDK to easily access and manipulate your data.

::: info Older SDK Documentation

You can find the SDK reference for older versions (prior to v11) [here](/reference/old-sdk). Version 11 introduced a
complete rewrite, and was released in July 2023.

:::

## Features

- **TypeScript first:** The SDK provides a robust and type-safe development experience
- **Modular architecture:** The SDK is split into separate modules, giving you granular control over which features to
  include and which can be pruned at build-time
- **Lightweight and dependency-free:** It does not require external libraries, ensuring a lighter bundle and streamlined
  experience.

## Installation

Install the SDK:

```shell
npm install @directus/sdk
```

## Basic Usage

Create a Directus client using the `createDirectus` hook from `@directus/sdk`. The client gives you access to your
Directus project and data.

```js
import { createDirectus } from '@directus/sdk';

const client = createDirectus('http://directus.example.com');
```

## Creating a Composable Client

The Directus SDK is a "Composable Client" that allows you to customize and build a client with the specific features you
need. The client starts as an empty wrapper without any functionality. To add features, use the following composables:

- `rest()`: REST request functions, adds `.request(...)` to the client.
- `graphql()`: GraphQL request functions, adds `.query(...)` to the client.
- `authentication()`: Authentication functions including refresh logic, adds `.login(...)`, `.logout()`, and
  .`refresh()` to the client.
- `staticToken()`: Authentication functions for static tokens.
- `realtime()`: WebSocket connectivity, adds `.subscribe(...)`, `.sendMessage(...)` and `.onWebSocket(...)` to the
  client.

For example, to create a client with REST or GraphQL support, use the following:

::: code-group

```js [JavaScript]
import { createDirectus } from '@directus/sdk';
import { rest } from '@directus/sdk/rest';
import { graphql } from '@directus/sdk/graphql';

// Client with REST support
const client = createDirectus('http://directus.example.com').with(rest());

// Client with GraphQL support
const client = createDirectus('http://directus.example.com').with(graphql());
```

```ts [TypeScript]
import { createDirectus } from '@directus/sdk';
import { rest } from '@directus/sdk/rest';
import { graphql } from '@directus/sdk/graphql';

interface Article {
	id: number;
	title: string;
	content: string;
}

interface Schema {
	articles: Article[];
}

// Client with REST support
const client = createDirectus<Schema>('http://directus.example.com').with(rest());

// Client with GraphQL support
const client = createDirectus<Schema>('http://directus.example.com').with(graphql());
```

:::

If using TypeScript, you need to provide a `Schema` when creating a Directus client to make use of type hinting and
completion. This schema contains definitions for each collection and provides you with type hints (on input) and
completion (on output).

## Authentication

Use the `authentication()` composable to add user login and tokens auto-refresh to the client.

For example, to login to your directus instance, invoke the `login` method

```js
import { createDirectus } from '@directus/sdk';
import { authentication } from '@directus/sdk/auth';

const client = createDirectus('http://directus.example.com').with(authentication());

await client.login(email, password);
```

## Making Requests

To query or update your collection in Directus, use the `rest()` or `graphql()` composable.

### Using REST

Add the `rest()` composable to the client, this enables the `.request(...)` method to query the collection.

For example, to make a request to an `articles` collection.

#### Read a single item

```js
import { createDirectus } from '@directus/sdk';
import { rest, readItem } from '@directus/sdk/rest';

const client = createDirectus('http://directus.example.com').with(rest());

const article_id = 5;
const result = await client.request(readItem('articles', article_id));
```

#### Read all items

```js
import { createDirectus } from '@directus/sdk';
import { rest, readItems } from '@directus/sdk/rest';

const client = createDirectus('http://directus.example.com').with(rest());

const result = await client.request(readItems('articles'));
```

#### Read specific fields

```js
import { createDirectus } from '@directus/sdk';
import { rest, readItems } from '@directus/sdk/rest';

const client = createDirectus('http://directus.example.com').with(rest());

const result = await client.request(
	readItems('articles', {
		fields: ['id', 'title'],
	})
);
```

#### Read all fields

```js
import { createDirectus } from '@directus/sdk';
import { rest, readItems } from '@directus/sdk/rest';

const client = createDirectus('http://directus.example.com').with(rest());

const result = await client.request(
	readItems('articles', {
		fields: ['*'],
	})
);
```

#### Read nested fields

```js
import { createDirectus } from '@directus/sdk';
import { rest, readItems } from '@directus/sdk/rest';

const client = createDirectus('http://directus.example.com').with(rest());

const result = await client.request(
	readItems('articles', {
		fields: ['*', { author: ['*'] }],
	})
);
```

### Using GraphQL

Add the `graphql()` composable to the client, this enables the `.query(...)` method to query the collection.

For example, to make a request to an `articles` collection with TypeScript:

```ts
import { createDirectus } from '@directus/sdk';
import { graphql } from '@directus/sdk/graphql';

interface Article {
	id: number;
	title: string;
	content: string;
}

interface Schema {
	articles: Article[];
}

const client = createDirectus<Schema>('http://directus.example.com').with(graphql());

const result = await client.query<Article[]>(`
    query {
        articles {
            id
            title
            content
        }
    }
`);
```

:::tip Importing SDK Composables

All SDK composables can also be conveniently imported from the root package `@directus/sdk`.

:::

## Next Steps

You can find code examples using the Directus SDK throughout our [API reference](/reference/items).

You can also find an auto-generated [TypeDoc of the new SDK](/packages/@directus/sdk/).
