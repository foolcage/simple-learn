# 1. 常用操作

### a. `create`

create index:
```bash
curl -XPUT 'localhost:9200/customer?pretty&pretty'

curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```

create document without id:
```bash

curl -XPOST 'localhost:9200/customer/external?pretty&pretty' -H 'Content-Type: application/json' -d'
{
  "name": "Jane Doe"
}
'

curl -XGET 'localhost:9200/customer/external/1?pretty&pretty'
```

create document with id:
```bash
curl -XPUT 'localhost:9200/customer/external/1?pretty&pretty' -H 'Content-Type: application/json' -d'
{
  "name": "John Doe"
}
'

curl -XGET 'localhost:9200/customer/external/1?pretty&pretty'
```

### b. `read(query)`
query by id:
```bash

curl -XGET 'localhost:9200/customer/external/1?pretty&pretty'

```
### c. `update`

update document:
```bash

curl -XPUT 'localhost:9200/customer/external/1?pretty&pretty' -H 'Content-Type: application/json' -d'
{
  "name": "John Doe"
}
'

curl -XPOST 'localhost:9200/customer/external/1/_update?pretty&pretty' -H 'Content-Type: application/json' -d'
{
  "doc": { "name": "Jane Doe" }
}
'

```
### d. `delete`

```bash

curl -XDELETE 'localhost:9200/customer?pretty&pretty'
curl -XGET 'localhost:9200/_cat/indices?v&pretty'

curl -XDELETE 'localhost:9200/customer/external/2?pretty&pretty'

```
### f. `bulk`

```bash

curl -XPOST 'localhost:9200/customer/external/_bulk?pretty&pretty' -H 'Content-Type: application/json' -d'
{"index":{"_id":"1"}}
{"name": "John Doe" }
{"index":{"_id":"2"}}
{"name": "Jane Doe" }
'

curl -XPOST 'localhost:9200/customer/external/_bulk?pretty&pretty' -H 'Content-Type: application/json' -d'
{"update":{"_id":"1"}}
{"doc": { "name": "John Doe becomes Jane Doe" } }
{"delete":{"_id":"2"}}
'

```
