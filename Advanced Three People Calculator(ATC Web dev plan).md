# Advanced Three People Calculator(ATC Web dev plan)

## 目录
-----
* [1. 概述](#1)
* [2. 产品](#2)
    * [2.1 后端网页API](#2.1)
    * [2.2 前端网页逻辑设计](#2.2)
-----

<h2 id="1">概述</h2>

-----
ATC是一个为了<u>宿舍三人记录日常开销以及简化计算和平摊多人费用的应用网站</u>。其具体功能有: 
* <font color="blue">创建账单</font>
* <font color="blue">添加账单用品</font>
* <font color="blue">计算每人在账单里平均分摊的费用</font>
* <font color="blue">生成账单总结文档</font>
-----

<h2 id="2">产品</h2>

-----
1. ATC Web (前端网页)\[<font color="orange">OFF</font>\]
(TBD)
2. ATC Web API (Spring后端) \[<font color="green">ON</font>\]
[Sorce code: https://github.com/GareArc/ATCWebApi.git]
[Release: https://github.com/GareArc/ATCWebApi/releases/tag/v0.0.1]

-----

<h2 id="2.1">后端 API 接口设计</h2>

-----
* POST: /atc/api/{version}/order 
上传账单,附加JSON Body。返回JSON包含此账单的uuid。
JSON Body示例:
```json    
{   
    "ThreePeople":[
        {"price":10,"quantity":1,"isTaxed":false,"relation":"ALL","shop":"NORMAL"}
    ],
    "Target1":[
        {"price":10,"quantity":1,"isTaxed":true,"relation":"INDIVIDUAL","shop":"NORMAL"},
        {"price":20,"quantity":1,"isTaxed":false,"relation":"SHARED","shop":"UBER"}
    ],
    "Target2":[
        {"price":20,"quantity":1,"isTaxed":false,"relation":"SHARED","shop":"UBER"}
    ],
    "Target1Info":"Charlie",
    "Target2Info":"Gareth"
}
```
返回JSON示例:
```json
"7d9b2ae7-6ace-4e33-ac91-25123b59db78"
```

* GET: /atc/api/{version}/order/all
返回JSON包含所有储存账单的uuid。
返回JSON示例:
```json
[
    "7d9b2ae7-6ace-4e33-ac91-25123b59db78",
    "b78a7f09-e869-4b4d-baf1-f8dadfa63645"
]
```

* GET: /atc/api/{version}/order/{id}
id为请求账单对应的uuid, 返回JSON格式的对应账单。
请求示例：
```
curl -X GET \
  www.garethcxy.tk:8080/atc/api/1.0/order/7d9b2ae7-6ace-4e33-ac91-25123b59db78 \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: be01a8cc-70e4-402c-948d-30fd08596967' \
  -H 'cache-control: no-cache'
```
返回JSON示例:
```json
{
    "orderDate": "2021-07-12T13:33:21.266",
    "uuid": "7d9b2ae7-6ace-4e33-ac91-25123b59db78",
    "summary": {
        "txtString": "-----------------------------------\n订单UUID: 7d9b2ae7-6ace-4e33-ac91-25123b59db78\n创建时间: 2021-07-12 13:33:21\n账单对象: Charlie, Gareth\n-----------------------------------\n三人物品: \n    Item: (普通超市) (三人) (无税) $10.00 * 1\n-----------------------------------\nCharlie: \n    Item: (普通超市) (个人) (有税) $10.00 * 1\n    Item: (Uber Eats) (双人) (无税) $20.00 * 1\n-----------------------------------\nGareth: \n    Item: (Uber Eats) (双人) (无税) $20.00 * 1\n-----------------------------------\nCharlie: 25.762999999999998Gareth: 14.463000000000001\nEnd.",
        "target1Info": "Charlie",
        "target2Info": "Gareth",
        "itemsForTarget1": [
            "Item: (普通超市) (个人) (有税) $10.00 * 1",
            "Item: (Uber Eats) (双人) (无税) $20.00 * 1"
        ],
        "itemsForTarget2": [
            "Item: (Uber Eats) (双人) (无税) $20.00 * 1"
        ],
        "itemsForAll": [
            "Item: (普通超市) (三人) (无税) $10.00 * 1"
        ],
        "totalTaregt1": 25.762999999999998,
        "totalTaregt2": 14.463000000000001
    },
    "Target1Info": "Charlie",
    "Target2Info": "Gareth",
    "ThreePeople": [
        {
            "description": "Item",
            "price": 10.0,
            "quantity": 1,
            "relation": "ALL",
            "isTaxed": false,
            "shop": "NORMAL"
        }
    ],
    "Target1": [
        {
            "description": "Item",
            "price": 10.0,
            "quantity": 1,
            "relation": "INDIVIDUAL",
            "isTaxed": true,
            "shop": "NORMAL"
        },
        {
            "description": "Item",
            "price": 20.0,
            "quantity": 1,
            "relation": "SHARED",
            "isTaxed": false,
            "shop": "UBER"
        }
    ],
    "Target2": [
        {
            "description": "Item",
            "price": 20.0,
            "quantity": 1,
            "relation": "SHARED",
            "isTaxed": false,
            "shop": "UBER"
        }
    ]
}
```

* Get: /atc/api/{version}/order/summary/{id}
id为请求账单对应uuid，返回JSON包含对应账单的summary信息。
请求示例:
```
curl -X GET \
  www.garethcxy.tk:8080/atc/api/1.0/order/summary/7d9b2ae7-6ace-4e33-ac91-25123b59db78 \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: be01a8cc-70e4-402c-948d-30fd08596967' \
  -H 'cache-control: no-cache'
```
返回JSON示例:
```json
{
    "txtString": "-----------------------------------\n订单UUID: 7d9b2ae7-6ace-4e33-ac91-25123b59db78\n创建时间: 2021-07-12 13:33:21\n账单对象: Charlie, Gareth\n-----------------------------------\n三人物品: \n    Item: (普通超市) (三人) (无税) $10.00 * 1\n-----------------------------------\nCharlie: \n    Item: (普通超市) (个人) (有税) $10.00 * 1\n    Item: (Uber Eats) (双人) (无税) $20.00 * 1\n-----------------------------------\nGareth: \n    Item: (Uber Eats) (双人) (无税) $20.00 * 1\n-----------------------------------\nCharlie: 25.762999999999998Gareth: 14.463000000000001\nEnd.",
    "target1Info": "Charlie",
    "target2Info": "Gareth",
    "itemsForTarget1": [
        "Item: (普通超市) (个人) (有税) $10.00 * 1",
        "Item: (Uber Eats) (双人) (无税) $20.00 * 1"
    ],
    "itemsForTarget2": [
        "Item: (Uber Eats) (双人) (无税) $20.00 * 1"
    ],
    "itemsForAll": [
        "Item: (普通超市) (三人) (无税) $10.00 * 1"
    ],
    "totalTaregt1": 25.762999999999998,
    "totalTaregt2": 14.463000000000001
}
```

* POST: /atc/api/{version}/order/del/{id}
删除某个账单。
id为对应账单uuid，返回JSON包含结果true/false。
请求示例:
```
curl -X GET \
  www.garethcxy.tk:8080/atc/api/1.0/order/del/7d9b2ae7-6ace-4e33-ac91-25123b59db78 \
  -H 'Content-Type: application/json' \
  -H 'Postman-Token: be01a8cc-70e4-402c-948d-30fd08596967' \
  -H 'cache-control: no-cache'
```
返回示例:
```json
true
```

* POST: /atc/api/{version}/order/clear
清空所有保存订单。
返回JSON包含请求结果true/false。
返回示例:
```json
true
```
-----

<h2 id="2.2">前端网页逻辑设计</h2>

-----





