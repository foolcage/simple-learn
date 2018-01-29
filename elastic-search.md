* [1. 问题](elastic-search.md#1-问题)
   * [1.1 最快的查询？](elastic-search.md#11-最快的查询)
   * [1.2 如何查(查询条件)？](elastic-search.md#12-如何查查询条件)
      * [a. 全文搜索](elastic-search.md#a-全文搜索)
      * [b. 结构化搜索](elastic-search.md#b-结构化搜索)
   * [1.3 如何索引？](elastic-search.md#13-如何索引)
      * [a. 索引的value(doc id or terms directly)](elastic-search.md#a-索引的valuedoc-id-or-terms-directly)
      * [b. 索引的key(term的选取问题)](elastic-search.md#b-索引的keyterm的选取问题)
* [2. 核心概念](elastic-search.md#2-核心概念)
   * [2.1. 文档(document)](elastic-search.md#21-文档document)
   * [2.2. 索引(index)](elastic-search.md#22-索引index)
   * [2.3. 类型(type)](elastic-search.md#23-类型type)
   * [2.4. 字段(field)](elastic-search.md#24-字段field)
   * [2.5. 数据类型(filed type)](elastic-search.md#25-数据类型filed-type)
   * [2.6. 映射(mapping)](elastic-search.md#26-映射mapping)
   * [2.7. 单词(term)](elastic-search.md#27-单词term)
* [3. 常用操作](elastic-search.md#3-常用操作)
   * [3.1. 格式](elastic-search.md#31-格式)
   * [3.2. create](elastic-search.md#32-create)
   * [3.3. read(query)](elastic-search.md#33-readquery)
   * [3.4. update](elastic-search.md#34-update)
   * [3.5. delete](elastic-search.md#35-delete)
   * [3.6. bulk](elastic-search.md#36-bulk)

# 1. 问题

## 1.1 最快的查询？
在目前认知水平下，时间复杂度为O(1)的查询可认为最快的查询，构造这样的查询常用的方式即索引。

## 1.2 如何查(查询条件)？

### a. 全文搜索
比如普通的搜索引擎，你输入关键字"java"时,可以理解为全文索引，因为可能从"食物的表"去查，也可能从"编程语言的表"去查。
### b. 结构化搜索
查询条件里面包含了足够的信息去定位"表"。

## 1.3 如何索引？
如何索引，很大程度上由如何搜索决定；如何索引，决定了提供的搜索能力。  
索引本质上是一个kv结构，根据k能够快速的找到v;  
文档就是term(with filed)的集合,倒排后，value为文档id,key为term;  
k->v->[kv]  
term->doc id->doc terms  
搜索，就是根据查询条件，得到term,从而找到相应的doc。

关于如何索引，有如下选项
### a. 索引的value(doc id or terms directly)  
  解决如何找到document field的问题，是从整个文档(_source)里面查出来还是直接得到  
  1. _source（全量）  
  2. store(单独存储，从而直接得到)  
  这个一般用默认即可，即_source

### b. 索引的key(term的选取问题)
  解决查询匹配空间的问题
  1. _all（ignore field搜索时能搜到,相当于全文索引）
  如果搜索总是指定field,可以设置为false  
  2. term
  是否需要analyze成多个term来进行索引，搜索时也有这个选项:即是否

# 2. 核心概念
## 2.1. 文档(document)

## 2.2. 索引(index)

## 2.3. 类型(type)

## 2.4. 字段(field)

## 2.5. 数据类型(filed type)
 * String
 * Numeric
 * Date
 * Boolean

## 2.6. 映射(mapping)

## 2.7. 单词(term)  
跟string相关，会对其进行分词等操作(analyze);索引时可指定analyzed (the default),
not_analyzed, or no

# 3. 常用操作

## 3.1. 格式
从rest接口的角度，操作形式如下:

```
curl <REST Verb> /<Index>/<Type>/<ID>
```
由于URL路径代表了资源的层次，需要一些特殊路径来做"全局"的操作，比如：
查询所有indices:
```bash
curl -XGET 'localhost:9200/_cat/indices?v&pretty'
```
健康检查:
```bash
curl -XGET 'localhost:9200/_cat/health?v&pretty'
```
## 3.2. create

create index
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

## 3.3. read(query)
query by id:
```bash

curl -XGET 'localhost:9200/customer/external/1?pretty&pretty'

```

query by term filter:
```bash
curl -XPOST 'localhost:9200/balance_sheet/_search?pretty' -H 'Content-Type: application/json' -d'
{
  "query": {
    "constant_score": {
      "filter": {
        "term": {
          "securityId": "stock_sz_000002"
        }
      }
    }
  }
}
'

```
## 3.4. update

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
## 3.5. delete

```bash

curl -XDELETE 'localhost:9200/customer?pretty&pretty'
curl -XGET 'localhost:9200/_cat/indices?v&pretty'

curl -XDELETE 'localhost:9200/customer/external/2?pretty&pretty'

```

delete by query  
```bash
curl -XPOST 'localhost:9200/stock_sz_000002_bfq_day_kdata/_delete_by_query?pretty' -H 'Content-Type: application/json' -d'
{"query": {"match_all": {}}}

 curl -XPOST 'localhost:9200/account/_delete_by_query?pretty' -H 'Content-Type: application/json' -d'
 {"query": {"term": {"traderId": "aa"}}}'

```
## 3.6. bulk

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
