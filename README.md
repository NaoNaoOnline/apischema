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



##### Intern

Intern search queries reference internally managed fields of the underlying
resource objects. The search query below returns the event object associated to
the specified event ID, which in turn originated from the system at resource
creation.

```json
{
    "object": [
        {
            "intern": {
              "evnt": "123"
            },
            "public": {},
            "symbol": {}
        }
    ]
}
```



##### Public

Public search queries reference publicly provided fields of the underlying
resource objects. The search query below returns a list of event objects
associated to the specified label IDs, which were added to the event by the user
upon resource creation.

```json
{
    "object": [
        {
            "intern": {},
            "public": {
              "cate": "456",
              "host": "789",
            },
            "symbol": {}
        }
    ]
}
```



##### Symbol

Symbol search queries execute arbitrary logic to return resource objects
matching certain use case requirements. The search query below resolves to the
list of events the calling user reacted to in the past.

```json
{
    "object": [
        {
            "intern": {},
            "public": {},
            "symbol": {
              "rctn": "default"
            }
        }
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
        "paging": {
            "perpage": "50",
            "pointer": "100"
        }
    }
}
```

With pagination the response would as well contain `filter.paging`. When
receiving search responses there will always be the `result` list of requested
data objects.

```json
{
    "filter": {
        "paging": {
            "intotal": "750",
            "pointer": "150"
        }
    },
    "result": [
        {},
        {},
        {}
    ]
}
```
