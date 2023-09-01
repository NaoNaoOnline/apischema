# apischema

This is the source of truth for the NaoNao RPC API. The apischema defines how
RPC calls are shaped and function using Protocol Buffer definitions. Having a
unified abstraction helps us generate client and server code across different
languages and remain mostly independent of transport layer specifics.



### Contexts

All APIs allow for multiple action specific contexts, each of which defines a
certain behaviour. Create APIs allow for multiple create query objects. Search
APIs allow for multiple search query objects. Each object represents a context
defining its own behaviour.

```json
{
    "object": [
        {
            "intern": {
              "evnt": "123"
            },
            "public": {}
        },
        {
            "intern": {},
            "public": {
              "cate": "456",
              "host": "789",
            }
        },
    ]
}
```



### Formatting

```bash
clang-format -i $(find ./pbf -name "*.proto")
```



### JSON-Patch

Complex updates require JSON-Patch operations. If a resource for instance
defines a list of strings it would require the whole list to be provided only in
order to add or remove a single item of that resource's list property. For such
operations the resource's service in question provides the option of JSON-Patch
operations.

- https://datatracker.ietf.org/doc/html/rfc6902
- https://jsonpatch.com
- https://github.com/evanphx/json-patch
- https://github.com/Starcounter-Jack/JSON-Patch



### Operators

Filter operators we may support per resource are defined as follows.

- `bet` expresses that only numerical properties within the given range must
  match when returning requested data objects
- `not` expresses that no given property must be represented when returning
  requested data objects



### Pagination

Pagination is not yet supported, though the specification for splitting up
responses of larger results would work as follows. In the request example below
the response would contain the first batch of 100 items. Consecutive calls would
require the pointer returned with the preceding response in order to continue
fetching the next batches accordingly.

```json
{
    "filter": {
        "chunking": {
            "pointer": 0,
            "perpage": 100
        },
    }
}
```

With pagination the response would as well contain `filter.chunking`. When
receiving search responses there will always be the `result` list of requested
data objects.

```json
{
    "filter": {
        "chunking": {
            "pointer": 123,
            "intotal": 750
        }
    },
    "result": [
        {},
        {},
        {}
    ]
}
```
