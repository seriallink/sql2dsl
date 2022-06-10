**SQL2DSL**
-----------

This repository is a fork of [cch123/elasticsql](https://github.com/cch123/elasticsql). I'm moving from Elasticsearch to OpenSearch, so I decided to fork this project to attend my needs. That's why I changed the name of the project to **SQL2DSL**. You should use the original one.

Overview
-----------

This tool converts sql to dsl

Currently support:

- [x] sql and expression
- [x] sql or expression
- [x] equal(=) support
- [x] not equal(!=) support
- [x] gt(>) support
- [x] gte(>=) support
- [x] lt(<) support
- [x] lte(<=) support
- [x] sql in (eg. id in (1,2,3) ) expression
- [x] sql not in (eg. id not in (1,2,3) ) expression
- [x] paren bool support (eg. where (a=1 or b=1) and (c=1 or d=1))
- [x] sql like expression (currently use match phrase, perhaps will change to wildcard in the future)
- [x] sql order by support
- [x] sql limit support
- [x] sql not like expression
- [x] field missing check
- [x] support aggregation like count(\*), count(field), min(field), max(field), avg(field)
- [x] support aggregation like stats(field), extended_stats(field), percentiles(field) which are not standard sql function
- [ ] null check expression(is null/is not null)
- [ ] join expression
- [ ] having support

Usage
-------------

> go get -u github.com/seriallink/sql2dsl

Demo :
```go
package main

import (
    "fmt"
    "github.com/seriallink/sql2dsl"
)

func main() {

    sql := `select * 
              from document 
             where id = '1234'
               and tags like '%test%'`

	dsl, _, err := sql2dsl.Convert(sql)
	if err != nil {
		panic(err)
	}
	
	fmt.Print(dsl)

}
```
will produce :
```json
{
  "query": {
    "bool": {
      "must": [{
        "match_phrase": {
          "id": {
            "query": "1234"
          }
        }
      }, {
        "wildcard": {
          "tags": {
            "value": "*test*"
          }
        }
      }]
    }
  },
  "from": 0,
  "size": 1
}
```

If your sql contains some keywords, eg. order, timestamp, don't forget to escape these fields as follows:

```
select * from `order` where `timestamp` = 1 and `desc`.id > 0
```

Warning
------------
To use this tool, you need to understand the term query and match phrase query.

Setting a field to analyzed or not analyzed will get different results.

Details
------------
For more details of convertion, please refer to the [wiki](https://github.com/seriallink/sql2dsl/wiki)

Other info
------------
When writing this tool, I tried to avoid the deprecated dsl filters and aggregations, so it is compatible with most versions of the elasticsearch

If you have any advices or ideas, welcome to submit an issue or Pull Request!

License
-----------
MIT
