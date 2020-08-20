# REST API Docs

The shiori main server exposes a simple REST API to connect to from any generic, HTTP-able client.

## Authorization

shiori applies a simple, anonymous authorization system based on `Basic` header auth tokens.

Each request to the server must be authorized with an `Authorization` header containing a `Basic` authorization token which is specified in the configuration of the server instance.

> Example:  
> ```
>  Authorization: Basic my_super_secret_token
> ```

## Snowflake IDs

shiori is using a snowflake ID system for generating object IDs.

A snowflake is a 64 bit unsigned integer with the following structure:
- 42 bit milliseconds timestamp  
  *Millisecond based timestamp since a specified epoche*.*
- 10 bit node ID  
  *The node ID of the node in which the snowflake was created to ensure integrity across servers.*
- 12 bit incremental  
  *Number which is incremented each time another ID was generated within the same millisecond on the same node.*

*The specified epoche for any shiori instance is the following timestamp:
```go
// Mon Jul 11 09:19:55 52603 UTC
const epoche = 1597839643195
```

If you are curious on how to calculate the contained values within the snowflake IDs, take a look at the implementation in the shiori snowflake node module:  
https://github.com/shiori-ex/shiori/blob/master/lib/snowflake/node.ex

## Objects

### Link

A link object wraps a link *(aka. bookmark)* object.

Field | Type | Description 
------|------|------------
`id` | `number` | The snowflake ID of the link.
`id_str` | `string` | The snowflake ID of the link as string.
`url` | `string` | The link URL.
`description` | `string` | A short description about the link.
`tags` | `string[]` | A list of tags for the link.

Example:
```json
{
  "description": "This is an example description",
  "id": 450355460575232,
  "id_str": "450355460575232",
  "tags": [
    "example",
    "stuff",
    "test 123"
  ],
  "url": "https://example.com"
}
```

## Endpoints

### List all Links

> ### `GET /api/links`

Get a list of all links (paged request).

**Parameters:**

Parameter | Type | Description
----------|------|------------
`limit` | `number` | Maximum ammount of results.
`offset` | `number` | List results offset.

**Response**

```json
[
  {
    "description": null,
    "id": 450185998110720,
    "id_str": "450185998110720",
    "tags": null,
    "url": "https://zekro.de"
  },
  {
    "description": "This is an example description",
    "id": 450355460575232,
    "id_str": "450355460575232",
    "tags": [
      "example",
      "stuff",
      "test 123"
    ],
    "url": "https://example.com"
  }
]
```

### Search through Links

> ### `GET /api/links/search`

Get a result list compiled from the passed search query (pasged request).

**Parameters:**

Parameter | Type | Description
----------|------|------------
`query` | `string` | Search query. **[required]**
`limit` | `number` | Maximum ammount of results.
`offset` | `number` | List results offset.

**Response**

```json
{
  "exhaustiveNbHits": false,
  "hits": [
    {
      "description": "This is an example description",
      "id": 450355460575232,
      "id_str": "450355460575232",
      "tags": [
        "example",
        "stuff",
        "test 123"
      ],
      "url": "https://example.com"
    }
  ],
  "limit": 100,
  "nbHits": 1,
  "offset": 0,
  "processingTimeMs": 5,
  "query": "test"
}
```

### Get a Likn

> ### `GET /api/links/:id`

Returns a link object by its ID.

**Response**

```json
{
  "description": "This is an example description",
  "id": 450355460575232,
  "id_str": "450355460575232",
  "tags": [
    "example",
    "stuff",
    "test 123"
  ],
  "url": "https://example.com"
}
```

### Create a Link

> ### `PUSH /api/links`

Create a link object.

**Parameters:**

Parameter | Type | Description
----------|------|------------
`url` | `string` | The link URL. **[require]**
`description` | `string` | A short description about the link.
`tags` | `string[]` | A list of tags for the link.


**Response**

```json
{
  "description": "This is an example description",
  "id": 450355460575232,
  "id_str": "450355460575232",
  "tags": [
    "example",
    "stuff",
    "test 123"
  ],
  "url": "https://example.com"
}
```

### Update a Link

> ### `PUSH /api/links`

Update (thechnically replacing) an existing link object. Empty value fields are intrepreted as set to `nil`.

**Parameters:**

Parameter | Type | Description
----------|------|------------
`url` | `string` | The link URL. **[require]**
`description` | `string` | A short description about the link.
`tags` | `string[]` | A list of tags for the link.


**Response**

```json
{
  "description": "This is an example description",
  "id": 450355460575232,
  "id_str": "450355460575232",
  "tags": [
    "example",
    "stuff",
    "test 123"
  ],
  "url": "https://example.com"
}
```

### Remove a Link

> ### `DELETE /api/links/:id`

Remove a link object.

**Response**

```json
{
  "code": 200,
  "message": "ok"
}
```