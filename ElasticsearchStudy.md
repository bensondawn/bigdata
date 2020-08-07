1. #### **Elasticsearch数据类型**

- 字符串：text、keyword，text是分词，keyword是不分词的
- 数值型：long、integer、short、byte、double、float、half_float、scaled_float
- 布尔：boolean
- 日期：date
- 二进制：binary
- 范围类型：integer_range、float_range、long_range、double_range、date_range

2. #### **文档的元数据：用于标注文档的相关信息**

- _index：文档所在的索引名
- _type：文档所在的类型名
- _id：文档唯一id
- _score：相关性算分
- _uid：组合id，有_type和_id组成（从6.x开始_type不再起作用，同_id一样）
- _source：文档的原始json数据，可以从这里获取每个字段的内容
- _all：整合所有字段内容到该字段，默认下禁用

3. #### **正排索引和倒排索引**

- **正排索引**：维护文档id到内容、单词的关联关系，例如

![](E:\GitStorage\bigdata\picture\20181226155655824.png)

- **倒排索引**：维护单词到文档id的关联关系，例如

![](E:\GitStorage\bigdata\picture\2018122615594726.png)

​		比如，获取包含"领事馆"的文档id，则先根据倒排索引得到包含领事馆的文档id为1，再根据正排索引找到相应的文档内容，并将其返回即可。因为在es的索引中，每一个文档对应的数据结构都是json结构形式，每个json中的字段都会维护自己的倒排索引（未开启的除外，比如设置了index=false），所以查找起来也非常快。

4. #### **Elasticsearch之分词**

**分词（Analysis）**：将文本切分为一系列单词的过程，比如 "美国留给伊拉克的是个烂摊子吗?"经过分词后的后果为:美国、伊拉克、烂摊子。

**分词器（Analyzer）**：elasticsearch中执行的分词的主体，官方把分词器分成三个层次：

- Character Filters:针对文档的原始文本进行处理，例如将印度语的阿拉伯数字"0 12345678 9"转换成拉丁语的阿拉伯数字"0123456789"，或者去除HTML中的特殊标记符号，Character Filters可以有零或多个，安装顺序应用;

- Tokenizer:核心，将文档的原始文本按照一定规则切分为单词，Tokenizer只能有一个;

- Token Filter:对经过Tokenizer处理过后的单词进行二次加工，比如转换为小写，Token Filter也可以有多个，按顺序依次调用.

- 三者的调用顺序：Character Filters--->Tokenizer--->Token Filter

5. #### **分词使用时机**

- 创建或更新文档时候，es会对相应的文档数据进行分词处理，比如你某个索引字段类型为text，那么插入一条文档时候就会对该字段进行分词处理，维护该字段文本内容的倒排索引，这种我们称为**索引时分词**，也叫写时分词；
  - 文档写入的时候会根据字段设置的分词器类型进行分词，如果不指定就是默认的standard分词器。
  - 写时分词器需要在mapping中指定，而且一旦指定就不能再修改，若要修改必须重建索引。
- 查询时候，会对你的查询文本进行分词，比如你要查询"苹果手机"，则会分词为"苹果、手机"两个单词，这种我们称为**查询时分词**，也叫读时分词；
  - 读时分词器默认与写时分词器保持一致，mapping中如果不配置search_analyzer，则读时分词器默认同写时分词器保持一致，都使用analyzer；这种默认设定也是非常容易理解的，读写采用一致的分词器，才能尽最大可能保证分词的结果是可以匹配的。
  - 如果想单独配置读时分词器，则需要在mapping中配置search_analyzer。
  - mapping中配置好分词器，查询语句就不需要再指定分词器了(当然查询语句中是可以指定分词器的)。

6. #### **Elastic Search之自定义Mapping

> 参考：[Elastic Search之自定义Mapping](https://blog.csdn.net/fanrenxiang/article/details/85317344)

7. #### ES 中 Text keyword 两种基本类型区别

- string字段被拆分成两种新的数据类型: text用于全文搜索的, 而keyword用于关键词搜索。
- 你可以在productID字段上进行全文搜索, 也可以通过productID.keyword字段实现关键词搜索及数据聚合。
- 字符串将默认被同时映射成text和keyword类型，就像下面的这种结构：

```json
"productID": {
  "type": "text",//默认的type类型
  "fields": {
	 "keyword": {//字段名称,可以是任意你想要的单词,比如raw;如果你没有设置mapping,在put数据的时候es会默认给每个string字段加keyword类型;
		"type": "keyword",//keyword类型，不分词，用于聚合或排序。
		"ignore_above": 256//字符长度上限256个字符，超出字符长度后可以正常存入，但不被检索。
	 }
  }
}

查询 
{
  "query":{
    "term": {
      "productID": "haha" // 分词
    }
  }
}

{
  "query":{
    "term": {
      "productID.keyword": "haha" // 不分词
    }
  }
}

没有keyword的字段 你可以通过这种方式添加
PUT test/test/_mapping
{
	"test": {
	"properties": {
			"name": {
				"type": "text",
				"fields": {
					"keyword": {
						"type": "keyword",
						"ignore_above": 256
					}
				}
			}
		}
	}
}
```

8. #### 自定义Mapping

> 参考：[Elasticsearch篇之Mapping自定义设置](https://blog.csdn.net/qq_39337886/article/details/103928791)

​		创建索引，插入文档时，会根据文档的数据类型自动生成mapping，如果需要自定义mapping，可以先插入一条文档，然后获取mapping修改属性，最后put。如果不需要分词(索引时分词/查询时分词 详见第五节)，可以不自定义mapping，用默认生成的即可。



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
{
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
            }
            ]
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

