# StoryPage 组件Tab商品查询接口文档

## 接口概述

该接口用于查询 StoryPage 页面中支持Tab配置的组件下的商品信息。前端可以根据不同的Tab分类，动态加载对应的商品列表，实现Tab切换效果。

**当前支持的组件：**
- ✅ 秒杀组件（Seckill Component）
- ✅ 动态商品列表组件（Dynamic Item List Component）

---

## 接口信息

**请求方式：** `GET`

**接口路径：** `/cms/pages/stories/{page_key}/tabItems`

**Content-Type：** `application/json`

---

## 请求参数

### Header 参数（请求头）

| 参数名 | 类型 | 必填 | 说明 | 示例值 |
|--------|------|------|------|--------|
| token | String | 是 | 用户登录凭证 | `eyJhbGciOiJIUzI1NiJ9...` |
| y_language | String | 是 | 语言设置 | `zh_CN`（简体中文）<br>`en_US`（英文） |
| y_platform | String | 是 | 平台标识 | `ios`、`android`、`h5`、`pc` |

### Path 参数（路径参数）

| 参数名 | 类型 | 必填 | 说明 | 示例值 |
|--------|------|------|------|--------|
| page_key | String | 是 | 页面唯一标识（不含语言前缀） | `home`、`recommend` |

### Query 参数（查询参数）

| 参数名 | 类型 | 必填 | 说明 | 示例值 |
|--------|------|------|------|--------|
| cfId | Integer | 是 | 组件配置ID（component_config_id），用于唯一标识一个组件实例 | `12345` |
| subTabId | Integer | 否 | 子Tab索引，具体含义由组件类型决定<br>**秒杀组件：** 表示第几场秒杀活动（从0开始） | `0`（第一场）<br>`1`（第二场）<br>`null`（默认场次） |
| tabId | Integer | 是 | 分类Tab的ID，具体含义由组件类型决定<br>**秒杀组件：** 商品分类ID，`0`表示全部分类 | `0`（全部分类）<br>`123`（指定分类ID） |

---

## 响应数据

### 响应结构

```json
{
  "code": 0,
  "message": "success",
  "data": {
    // 具体数据结构由组件类型决定，详见下方各组件说明
  }
}
```

### 通用响应字段

| 字段名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 响应状态码<br>`0`：成功<br>`90001`：数据为空或组件ID无效 |
| message | String | 响应消息 |
| data | Object | 业务数据，结构由组件类型决定（见下方各组件详细说明） |

---

## 组件类型说明

### 1. 秒杀组件（Seckill Component）

#### 组件说明

秒杀组件支持多场秒杀活动，每场活动的商品可以按分类Tab进行筛选。前端通过 `subTabId` 指定秒杀场次，通过 `tabId` 指定商品分类。

#### 秒杀组件参数说明

| 参数 | 类型 | 必填 | 说明 | 示例 |
|------|------|------|------|------|
| cfId | Integer | 是 | 秒杀组件的配置ID | `12345` |
| subTabId | Integer | 否 | 秒杀活动场次索引（从0开始）<br>- `0`：第一场秒杀<br>- `1`：第二场秒杀<br>- 不传：默认返回第一场 | `0`、`1` |
| tabId | Integer | 是 | 商品分类ID<br>- `0`：全部分类<br>- 其他：具体分类ID（匹配L1或L2分类） | `0`、`456` |

#### 秒杀组件返回值结构

**data 字段结构（ActivitySeckillResponse）：**

```json
{
  "ps_id": "670802",
  "status": 1,
  "start_time": 1706745600,
  "end_time": 1706752800,
  "count_down": 3600,
  "item_show_type": 1,
  "item_count": 12,
  "item_info": [
    {
      "goods_id": 100001,
      "item_number": "1016028821",
      "item_title": "【限时秒杀】商品名称",
      "item_image": "https://cdn.example.com/image.jpg",
      "slug": "product-slug",
      "item_type": "new",
      "business_type": "yamibuy",
      "price": {
        "market_price": "$29.99",
        "current_price": "$19.99",
        "discount_rate": "33%"
      },
      "seckill_status": 1,
      "brand_id": 123,
      "brand_name": "品牌名称",
      "brand_slug": "brand-slug",
      "comment": {
        "score": 4.8,
        "count": 256
      },
      "badge_list": [
        {
          "type": "hot",
          "text": "热卖"
        }
      ],
      "tracking_data": {
        "position": 1,
        "component_id": 12345
      }
    }
  ]
}
```

**字段说明：**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| ps_id | String | 秒杀活动ID |
| status | Integer | 秒杀活动状态<br>`0`：已结束<br>`1`：进行中<br>`2`：未开始 |
| start_time | Integer | 活动开始时间（Unix时间戳，秒） |
| end_time | Integer | 活动结束时间（Unix时间戳，秒） |
| count_down | Long | 倒计时剩余时间（秒） |
| item_show_type | Integer | 商品展示类型<br>`1`：普通展示<br>`2`：其他展示类型 |
| item_count | Integer | 该分类下的商品总数 |
| item_info | Array&lt;ItemVO&gt; | 商品列表（详见商品对象说明） |

**商品对象（ItemVO）主要字段：**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| goods_id | Integer | 商品ID |
| item_number | String | 商品编号（SKU） |
| item_title | String | 商品标题（根据语言返回） |
| item_image | String | 商品主图URL |
| slug | String | 商品URL slug |
| item_type | String | 商品类型：`new`-新品/`clearance`-清仓/`preorder`-预售等 |
| business_type | String | 业务类型：`yamibuy`-自营/`third_vendor`-第三方等 |
| price | PriceVO | 价格信息对象 |
| seckill_status | Integer | 秒杀状态<br>`0`：未开始<br>`1`：进行中<br>`5`：已结束 |
| end_time | Long | 秒杀结束时间（毫秒时间戳） |
| brand_id | Integer | 品牌ID |
| brand_name | String | 品牌名称（根据语言返回） |
| brand_slug | String | 品牌URL slug |
| comment | CommentVO | 评论信息（评分>4.5时返回） |
| badge_list | Array&lt;BadgeVO&gt; | 商品Badge列表 |
| coupon_desc | String | 优惠券描述 |
| seller | SellerVO | 商家信息 |
| tracking_data | TrackingData | 埋点数据 |
| status | String | 商品状态：`on_shelf`-上架/`out_of_stock`-缺货等 |
| promotion_short | String | 短促销语 |
| weekly_qty | String | 周销量（如`100+`） |
| weekly_qty_sign | String | 周销量单位文案 |
| rank | RankVO | 榜单信息 |
| group_num | Integer | 商品组商品数量 |
| bundle | String | Bundle商品单价描述 |
| bundle_info | BundleVO | Bundle商品价格详情 |

---

### 2. 动态商品列表组件（Dynamic Item List Component）

#### 组件说明

动态商品列表组件支持分类Tab配置。前端通过 `tabId` 指定分类Tab（`0` 表示“全部”），服务端基于动态数据源返回该Tab下的商品列表。

#### 动态商品组件参数说明

| 参数 | 类型 | 必填 | 说明 | 示例 |
|------|------|------|------|------|
| cfId | Integer | 是 | 动态商品组件的配置ID | `12345` |
| subTabId | Integer | 否 | 动态商品组件不使用该参数，传 `null` 或不传 | `null` |
| tabId | Integer | 是 | 分类Tab ID<br>- `0`：全部分类<br>- 其他：具体分类ID（匹配 L1/L2/L3 分类） | `0`、`456` |

#### 动态商品组件返回值结构

**data 字段结构（DynamicItemTabResponse）：**

```json
{
  "item_info": [
    {
      "tab_text": "全部",
      "tab_value": 0,
      "item_count": 12,
      "items": [
        {
          "goods_id": 100001,
          "item_number": "1016028821",
          "item_title": "商品名称",
          "item_image": "https://cdn.example.com/image.jpg",
          "slug": "product-slug",
          "item_type": "new",
          "business_type": "yamibuy",
          "price": {
            "market_price": "$29.99",
            "current_price": "$19.99",
            "discount_rate": "33%"
          },
          "brand_id": 123,
          "brand_name": "品牌名称",
          "brand_slug": "brand-slug",
          "comment": {
            "score": 4.8,
            "count": 256
          },
          "badge_list": [
            {
              "type": "hot",
              "text": "热卖"
            }
          ],
          "tracking_data": {
            "position": 1,
            "component_id": 12345
          }
        }
      ]
    }
  ]
}
```

**字段说明：**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| item_info | Array&lt;TabSetting&gt; | Tab结果列表 |
| item_info.tab_text | String | Tab名称 |
| item_info.tab_value | Integer | Tab分类ID |
| item_info.item_count | Integer | 该Tab下商品数量 |
| item_info.items | Array&lt;ItemVO&gt; | 商品列表（详见商品对象说明） |

**商品对象（ItemVO）主要字段：**

| 字段名 | 类型 | 说明 |
|--------|------|------|
| goods_id | Integer | 商品ID |
| item_number | String | 商品编号（SKU） |
| item_title | String | 商品标题（根据语言返回） |
| item_image | String | 商品主图URL |
| slug | String | 商品URL slug |
| item_type | String | 商品类型：`new`-新品/`clearance`-清仓/`preorder`-预售等 |
| business_type | String | 业务类型：`yamibuy`-自营/`third_vendor`-第三方等 |
| price | PriceVO | 价格信息对象 |
| brand_id | Integer | 品牌ID |
| brand_name | String | 品牌名称（根据语言返回） |
| brand_slug | String | 品牌URL slug |
| comment | CommentVO | 评论信息（评分>4.5时返回） |
| badge_list | Array&lt;BadgeVO&gt; | 商品Badge列表 |
| tracking_data | TrackingData | 埋点数据 |
