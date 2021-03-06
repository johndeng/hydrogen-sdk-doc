# 内容分类操作

## 获取内容分类详情

**接口**

`GET https://cloud.minapp.com/userve/v1/content/:content_group_id/category/:category_id/`

其中 `content_group_id` 是内容库的 ID, `category_id` 是内容分类的 ID

**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.get('https://cloud.minapp.com/userve/v1/content/1/category/1/').then(res => {
  console.log(res.data)
})
```

**返回示例**

```json
{
  "id": 1,
  "name": "category",
  "parent": null,
  "subcategories": [
    {
      "id": 2,
      "name": "subcategory",
      "parent": {
        "id": 1,
        "name": "category"
      },
      "subcategories": [],
      "created_at": 1519901783,
      "updated_at": 1519901783
    }
  ],
  "created_at": 1516963449,
  "updated_at": 1516963449
}
```

**返回参数说明**

|      参数     |      类型     |   说明   |
| :-------------| :----------- | :------ |
| id            | Integer      | 分类 ID |
| name          | String       | 分类名称 |
| parent        | Object       | 分类的父类 |
| subcategories | Object Array | 子类名称 |
| created_at    | Integer      | 分类创建时间 |
| updated_at    | Integer      | 分类更新时间 |

## 获取内容分类列表

**接口**

`GET https://cloud.minapp.com/userve/v1/content/:content_group_id/category/`

**提交参数**

- parent 内容分类父分类列表查询

  `https://cloud.minapp.com/userve/v1/content/:content_group_id/category/?parent__isnull=true`

- name 内容分类名称的等值查询

  `https://cloud.minapp.com/userve/v1/content/:content_group_id/category/?name=category`


**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.get('https://cloud.minapp.com/userve/v1/content/1/category/').then(res => {
  console.log(res.data)
})

```

**返回示例**

```json
{
  "meta": {
    "limit": 20,
    "next": null,
    "offset": 0,
    "previous": null,
    "total_count": 2
  },
  "objects": [
    {
      "id": 1,
      "name": "category",
      "parent": null,
      "subcategories": [
        {
          "id": 2,
          "name": "subcategory",
          "parent": {
            "id": 1,
            "name": "category"
          },
          "subcategories": [],
          "created_at": 1519901783,
          "updated_at": 1519901783
        }
      ],
      "created_at": 1516963449,
      "updated_at": 1516963449
    },
    {
      "id": 2,
      "name": "subcategory",
      "parent": {
        "id": 1516963449144537,
        "name": "category"
      },
      "subcategories": [],
      "created_at": 1519901783,
      "updated_at": 1519901783
    }
  ]
}
```

## 创建内容分类

**接口**

`POST https://cloud.minapp.com/userve/v1/content/:content_group_id/category/`

**参数说明**

Content-Type: `application/json`

|  参数   |  类型   | 必填 | 说明 |
| :----- | :-----  | :-- | :-- |
| name   | String  |  Y  | 分类名称 |
| parent | Integer |  N  | 父分类 ID |

> **warning**
> 注意：最多只允许三层嵌套分类

**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.post('https://cloud.minapp.com/userve/v1/content/1/category/', {"name": "分类1",}).then(res => {
  console.log(res.data)
})
```

**返回示例**

```json
{
  "id": 3,
  "name": "分类1",
  "parent": null,
  "subcategories": [],
  "created_at": 1519910966,
  "updated_at": 1519910966
}
```

**状态码说明**

`201`: 创建成功

`400`: 同一父分类下的子分类名不能相同；父分类 ID 不合法


## 编辑内容分类

**接口**

`PUT https://cloud.minapp.com/userve/v1/content/:content_group_id/category/:category_id/`


**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.put('https://cloud.minapp.com/userve/v1/content/1/category/3/', {"name": "分类1",}).then(res => {
  console.log(res.data)
})
```

**返回示例**

```json
{
  "id": 3,
  "name": "Test Category",
  "parent": null,
  "subcategories": [],
  "created_at": 1519910966,
  "updated_at": 1519910966
}
```

**状态码说明**

`200`: 修改成功

`400`: 同一父分类下的子分类名不能相同；父分类 ID 不合法

## 删除内容分类

**接口**

`DELETE https://cloud.minapp.com/userve/v1/content/:content_group_id/category/:category_id/`


**代码示例**

```js
var axios = require('axios').create({
  withCredentials: true
})

axios.delete('https://cloud.minapp.com/userve/v1/content/1/category/1/').then(res => {
  console.log(res.data)
})
```

**状态码说明**

`204`: 删除成功
