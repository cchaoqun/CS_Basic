https://www.bilibili.com/video/BV1hh411D7sb?from=search&seid=17313866436708943399&spm_id_from=333.337.0.0

尚硅谷ES

ES历史版本下载地址 https://www.elastic.co/cn/downloads/past-releases

https://alvin.onloading.cn/2018/10/06/elasticsearch-install/

# HTTP入门

- PUT  创建索引

```
http://127.0.0.1:9200/shopping
```

- GET 获取索引详细信息

```
http://127.0.0.1:9200/_cat/indices?v
```

- DELETE 删除索引

```
http://127.0.0.1:9200/shopping
```

- POST 添加文档 (不是幂等性) 创建文档

```
http://127.0.0.1:9200/shopping/_doc
```

- 添加指定的id (1001)

```
http://127.0.0.1:9200/shopping/_doc/1001
```

p28

