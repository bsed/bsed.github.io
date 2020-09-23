---
title: elasticsearch查询api：term query
date: 2015-10-21 19:59:33
updated: 2015-10-21 19:59:33
tags: 
- jquery
categories: 
- js

---
## 1. term query查询
	
+ 在给定的字段里查询词或者词组；
+ 提供的查询词是不分词的(not analyzed)，即只有完全包含才算匹配；
+ 支持boost属性，boost可以提高field和document的相关性；

## 2. REST API

	$ curl -XGET 'localhost:9200/video/video_info/_search?pretty' -d @term_query.json

term_query.json 
简单的字段查询：

	{
		"query": {
			"term": {
				"tvName": "童年"
			}
		}
	}


<!--more-->


添加boost属性：

	{
		"query": {
			"term": {
				"tvName": {
					"value": "童年",
					"boost": 10
				}
			}
		}
	}

## 3. Java API

	public static void termQuery() {
		String queryWord = "巨头";
		QueryBuilder queryBuilder = QueryBuilders.termQuery(ConstantUtil.FIELD_TV_NAME, queryWord)
				.boost(10);
		SearchResponse response = searchRequestBuilder.setQuery(queryBuilder)
				.setFrom(0).setSize(5).execute().actionGet();
		printResult(response);
	}

## 4. 看看Java源码

以下是term_query相关的java源码：

    public static TermQueryBuilder termQuery(String name, String value) {
        return new TermQueryBuilder(name, value);
    }

    public static TermQueryBuilder termQuery(String name, int value) {
        return new TermQueryBuilder(name, value);
    }

    public TermQueryBuilder(String name, Object value) {
        this.name = name;
        this.value = value;
    }

    public TermQueryBuilder boost(float boost) {
        this.boost = boost;
        return this;
    }

> 从源码可以看到：termQuery方法内部都是调用TermQueryBuilder的构造函数，而TermQueryBuilder有很多重载的构造函数，最主要的就是上面的第三个函数，即第二个参数是Object类型，其它的构造函数都是调用该函数实现的。

### 参考资料：

+ [elasticsearch-guide](http://www.elasticsearch.org/guide/en/elasticsearch/reference/current/query-dsl-term-query.html)
