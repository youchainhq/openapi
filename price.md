
# API 列表 (需 scope 为 snsapi_base)

## /price/you

```
GET
https://dev-open.youchainapi.com/price/you
```

返回说明：

正确时返回的JSON数据包如下：

```
{
    "msg": "",
    "ret": 0,
    "data":
        {
            "priceUsdt": "0.0059",
            "priceCny": "0.0388"
        }
}
```

|字段|说明|
| --- | --- |
|priceUsdt|you-usdt价格|
|priceCny|人民币价格|

