### 例子1

```java
{
  "query": {
    "bool" : {
      "must" : {
        "term" : { "author.keyword" : "jinyong"}
      },
      "filter": {
        "term" : { "price" : "30" }
      },
      "must_not" : {
        "range" : {
          "price" : { "gte" : 40, "lte" : 50 }
        }
      },
      "should" : [
        { "term" : { "title.keyword" : "神雕侠侣" } },
		{ "term" : { "title.keyword" : "天龙八部" } },
        { "term" : { "title.keyword" : "笑傲江湖" } }
      ],
      "minimum_should_match" : 1,//紧跟should，代表需要匹配should中的几个条件。
      "boost" : 1.0	//设置评分权重
    }
  }
}
```

### 例子2 ：

### 通过boost参数, 令满足某个条件的文档的得分更高, 从而使得其排名更靠前。match查询会先对搜索词进行分词,分词完毕后再逐个对分词结果进行匹配。

```java
 "query": {
        "bool": {
            "must": {
                "match": {
                    "content": {
                        //搜获content字段包含full text search的文档，match分词后分词之间默认是or						//的关系，可以通过operator改为and，就像term一样精确查询了。
                        "query": "full text search",
                        "operator": "and"
                    }
                }
            },
            "should": [
                {
                    "match": {
                    "content": {
                        "query": "Elasticsearch",
                        "boost": 3	//设置评分权重
                    }
                }
            },
            {
                "match": {
                    "content": {
                        "query": "Lucene",
                        "boost": 2	//设置评分权重
                    }
                }
            }]
        }
    }
}
```

```java
{
    "query": {
        "bool": {
            "must": [
                { "match": { "name": "编程思想"} }
            ], 
            "should": [
                { 
                   "match": { 
                        "name": {
                            "query": "艺术", 
                            "boost": 2	//设置评分权重
                        } 
                    }
                }
            ]
        }
    }
}
```

```java
{
  "query": {
    "bool" : {
      "must" : {
        "term" : { "price" : 35 }
      },
      "should" : [
        { "term" : { "title" : "神雕侠侣" } },
		{ "term" : { "title" : "天龙八部" } },
        { "term" : { "title" : "笑傲江湖" } }
      ],
      "minimum_should_match" : 1,
      "boost" : 1.0	//设置评分权重
    }
  }
}
```

### 过滤

```java
{
  "query": {
    "bool": {
      "must": {
        "match_all": {}
      },
      "filter": {
        "term": {
          "price": "30"	//获取价格是30的结果。
        }
      }
    }
  }
}
```

### 排序

```java 
{
	"query":{
		"match_all":{}
	},
	"sort":[
        {"msg_time":{"order":"desc"}}	//逆序，asc正序
    ]
}
```

### 区间查询

```java
{
	"query":{
		"range":{
			"msg_time":{
				"gte":1564588800000,"lte":1572883200000
			}
		}
	},	
	"sort":[{"msg_time":{"order":"asc"}}]
}
```

```java
{
    //搜索分页，可以通过from+size组合来进行。
    //from表示从第几行开始，size表示查询多少条文档。from默认为0，size默认为10。
    //如果搜索size大于10000，需要设置index.max_result_window参数。
	//注意：size的大小不能超过index.max_result_window这个参数的设置，默认为10000。
    //官方推荐使用 scroll 代替。
	"from":0,
	"size":2,
	"query":{
		"bool":{
            "must":
            [
                {
                    "range":{
                        "age":{
                            "from":"20","to":"100",
							"include_lower":true,
							"include_upper":true,
							"boost":1.0
                        }
                    }
                }
            ],
			"adjust_pure_negative":true,
			"boost":1.0	//设置评分权重
		}
	},
	"explain":true
}
```

