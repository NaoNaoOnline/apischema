# apischema

This is the source of truth for the NaoNao RPC API. The apischema defines how
RPC calls are shaped and function using Protocol Buffer definitions. Having a
unified abstraction helps us generate client and server code across different
languages and remain mostly independent of transport layer specifics.



### Query Objects

All APIs allow for multiple action specific query objects, each of which defines
a certain behaviour. Create APIs allow for multiple create query objects. Search
APIs allow for multiple search query objects. Each object represents a context
defining its own behaviour. Some search query objects may yield a union of
results, so that a multitude of search criteria can be provided in order to
receive a response containing all merged results.



##### Object.Intern

`Object.Intern` search queries reference internally managed fields of the
underlying resource objects. The search query below returns the event object
associated to the specified event ID, which in turn originated from the system
at resource creation.

```json
{
    "object": [
        {
            "intern": {
                "evnt": "1234"
            },
            "public": {},
            "symbol": {}
        }
    ]
}
```



##### Object.Public

`Object.Public` search queries reference publicly provided fields of the
underlying resource objects. The search query below returns a list of event
objects associated to the specified label IDs, which were added to the event by
the user upon resource creation.

```json
{
    "object": [
        {
            "intern": {},
            "public": {
                "cate": "4567",
                "host": "6789"
            },
            "symbol": {}
        }
    ]
}
```



##### Object.Symbol

`Object.Symbol` search queries execute arbitrary logic to return resource objects
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



##### Response.Reason

`Response.Reason` provides the caller with a possible explanation for the
returned result. Every API may implement specific behaviour to manage the
resources it is responsible for. Such behaviour may imply authentication,
authorization or other preconditions that the caller must comply with. Since not
all API behaviour warrants an implicit error response that expresses failure, a
list of reasons for the returned result may be presented in order to inform
about the logical decisions an API had to make when serving the callers request
as presented. We may refer to these reasons as soft errors. They are errors, but
not really. They imply an error condition occurred which is expected to happen
for some users of the system. Then, instead of failing strictly with an error
response, the returned result may simply be empty, accompanied with an
additional explanation of why that is the case.

```json
{
    "object": [],
    "reason": [
        {
            "desc": "This is why the soft error occurred.",
            "kind": "someSoftError"
        }
    ]
}
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



### Operators

Filter operators we may support per resource are defined as follows.

- `bet` expresses that only numerical properties within the given range must
  match when returning requested data objects
- `not` expresses that no given property must be represented when returning
  requested data objects



### Formatting

```bash
clang-format -i $(find ./pbf -name "*.proto")
```
