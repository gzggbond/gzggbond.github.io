---
title: JsonScheme库使用手册
date: 2024-03-01 21:07:23
excerpt: JsonScheme库用于检验传入json数据是否符合自定义规则
tags:
  - 格式校验
categories:
  - [Python,格式校验]
---

`python第三方库，用于检验传入json数据是否符合自定义规则`

**关键字介绍**

> 所有关键字均用双引号包裹

+ **type**：表示待校验元素的类型

  > 可以指定的类型有 integer, string, object, array, number, null, boolean，用双引号包裹

+ **properties**：定义待校验的 JSON 对象中，各个 key-value 对中 value 的限制条件

  > 当type取值为object时，用于指定对象中每个字段的校验规则，可以嵌套使用

  ```python
  {
      "type":"object",
      "properties":{
          "字段名1":{规则},
          "字段名2":{规则},
         	....
      }
  }
  ```

+ **required**：定义待校验的 JSON 对象中，必须存在的 key，用`[]`进行包裹

+ **const**：JSON 元素必须等于指定的内容

  ```python
  # 节选
  "properties":{
      	# 指定success取值必须为True
          "success":{"const":True}
  }
  ```

+ **pattern**：使用正则表达式约束字符串类型数据

**案例**

```python
import jsonschema
schema = {
    "type":"object",
    "properties":{
        "success":{
            "type":"boolean"
        },
        "code":{
            "type":"integer"
        },
        "message":{
            "type":"string"
        }
    },
    "required":["success","code","message"]
}

data = {
    "success":True,
    "code":10000,
    "message":"操作成功"
}
# result为None时，代表校验通过
# 报异常ValidationError时，说明数据与校验规则不符
# 报异常SchemaError时，说明校验规则编写错误
result = jsonschema.validate(instance=data,schema=schema)
print(result)
```
