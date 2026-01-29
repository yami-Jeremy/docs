# getItemInfoAllV4 接口文档

> **接口名称**: PC商品详情页接口（V4版本）  
> **接口路径**: `GET /items/getItemInfoAllV4`  
> **版本说明**: 在 getItemInfoAllV3 基础上增加用户趋势数据  
> **文档创建**: 2026-01-29  
> **适用对象**: 前端开发人员

---

## 📋 目录

1. [接口概述](#接口概述)
2. [请求参数](#请求参数)
3. [返回数据结构](#返回数据结构)
4. [字段详细说明](#字段详细说明)
5. [使用示例](#使用示例)
6. [注意事项](#注意事项)

---

## 接口概述

### 功能描述
获取商品详情页所需的完整信息，包括：
- 商品基本信息（价格、库存、图片等）
- 商家信息
- 品牌信息
- 促销活动信息
- 用户趋势数据（购买人数、浏览数据等）
- 商品组信息
- 赠品活动列表
- 优惠券信息
- 推荐商品列表

### 版本特性
相比 V3 版本，V4 新增：
- ✨ 用户趋势数据（购买人数、用户头像等社交证明）
- ✨ 商品徽章列表
- ✨ 营养成分图片
- ✨ 优惠券码和描述优化
- ✨ Tab展示顺序配置
- ✨ 用户类型区分（non用户专属内容）

---

## 请求参数

### Query Parameters（URL参数）

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| **item_number** | String | ✅ 是 | - | 商品编号，商品的唯一标识 |
| **platform_code** | String | ❌ 否 | B2C | 平台代码 |
| **channel_code** | String | ❌ 否 | Computer | 渠道代码 |
| **banner_page_param** | String | ❌ 否 | - | Banner页面参数 |
| **termini** | String | ❌ 否 | yamibuy_app | 终端标识 |
| **group_op** | String | ❌ 否 | "" | 商品组操作标识<br>- 格式：`{last_item_number},{type}`<br>- type=1: 商品组内切换商品<br>- type=2: 商品组内点击过last pdp |
| **region_id** | String | ❌ 否 | - | 区域ID |
| **recycle_key** | String | ❌ 否 | "" | 回收站商品访问密钥 |

### Header Parameters（请求头参数）

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| **token** | String | ✅ 是 | - | 用户认证令牌（YamibuyConstant.KEY_TOKEN） |
| **CF-Connecting-IP** | String | ❌ 否 | "" | 用户IP地址（Cloudflare代理） |
| **y_platform** | String | ❌ 否 | h5 | 平台类型：pc / h5 / ios / android |
| **y_version** | Integer | ❌ 否 | - | APP版本号 |
| **app_name** | String | ❌ 否 | "" | APP名称 |
| **y_language** | String | ❌ 否 | zh_CN | 语言：zh_CN（中文）/ en_US（英文） |
| **device_id** | String | ❌ 否 | - | 设备ID |
| **ym_id** | String | ❌ 否 | - | Yamibuy用户ID |
| **User-Agent** | String | ❌ 否 | - | 用户代理字符串 |

### 请求示例

```http
GET /items/getItemInfoAllV4?item_number=1234567&platform_code=B2C&channel_code=Computer HTTP/1.1
Host: api.yamibuy.com
token: your_token_here
y_platform: pc
y_language: zh_CN
CF-Connecting-IP: 192.168.1.1
```

---

## 返回数据结构

### 响应格式

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "itemInfo": { /* 商品主体信息 */ },
    "sellerInfo": { /* 商家信息 */ },
    "brandInfo": { /* 品牌信息 */ },
    "giftActivityList": [ /* 赠品活动列表 */ ],
    "itemDescList": [ /* 商品属性列表 */ ],
    "same_day_delivery": { /* 一日达配送信息 */ },
    "group": { /* 商品组信息 */ },
    "itemRelateUserData": { /* 用户趋势数据（V4新增） */ },
    "tabList": [ /* Tab展示顺序（V4新增） */ ],
    "nutritionalFacts": [ /* 营养成分图片（V4新增） */ ],
    "psCode": "COUPON123",
    "couponCodeDesc": "满$50减$10，优惠码：COUPON123",
    "badgeList": [ /* 商品徽章列表（V4新增） */ ],
    "itemTags": [ /* 商品标签（V4新增） */ ],
    "vipTipInfo": { /* 会员升级提示 */ },
    "config": { /* 配置信息 */ },
    "currency": "$",
    "purchaseProtection": { /* 购物保障（non用户） */ },
    "recommendTabList": [ /* 推荐Tab列表 */ ],
    "topImage": "https://...",
    "topImageBackgroundColor": "#FFFFFF",
    "userType": "U1",
    "googleSeo": "{ /* SEO信息（仅PC） */ }",
    "collectionPage": [ /* 关联商品链接（仅PC） */ ],
    "essayList": [ /* 专辑列表（仅PC） */ ]
  }
}
```

### 返回字段总览

| 字段名 | 类型 | 说明 | 平台 | 版本 |
|--------|------|------|------|------|
| **itemInfo** | Object | 商品主体信息 | 全部 | V3/V4 |
| **sellerInfo** | Object | 商家信息 | 全部 | V3/V4 |
| **brandInfo** | Object | 品牌信息 | 全部 | V3/V4 |
| **giftActivityList** | Array | 赠品活动列表 | 全部 | V3/V4 |
| **itemDescList** | Array | 商品属性列表 | 全部 | V3/V4 |
| **same_day_delivery** | Object | 一日达配送信息 | 全部 | V3/V4 |
| **group** | Object | 商品组信息 | 全部 | V3/V4 |
| **config** | Object | 配置信息 | 全部 | V3/V4 |
| **currency** | String | 货币符号 | 全部 | V3/V4 |
| **vipTipInfo** | Object | 会员升级提示 | 全部 | V3/V4 |
| **recommendTabList** | Array | 推荐Tab列表 | 全部 | V3/V4 |
| **itemRelateUserData** | Object | 用户趋势数据 | 全部 | ✨ V4 |
| **tabList** | Array | Tab展示顺序 | 全部 | ✨ V4 |
| **nutritionalFacts** | Array | 营养成分图片 | 全部 | ✨ V4 |
| **psCode** | String | 优惠券码 | 全部 | ✨ V4 |
| **couponCodeDesc** | String | 优惠券描述 | 全部 | ✨ V4 |
| **badgeList** | Array | 商品徽章列表 | 全部 | ✨ V4 |
| **itemTags** | Array | 商品标签 | 全部 | ✨ V4 |
| **purchaseProtection** | Object | 购物保障 | 全部 | ✨ V4（non用户） |
| **topImage** | String | 顶部图片 | 全部 | ✨ V4（non用户） |
| **topImageBackgroundColor** | String | 顶部图片背景色 | 全部 | ✨ V4（non用户） |
| **userType** | String | 用户类型 | 全部 | ✨ V4 |
| **googleSeo** | String | SEO信息 | 仅PC | V3/V4 |
| **collectionPage** | Array | 关联商品链接 | 仅PC | V3/V4 |
| **essayList** | Array/Object | 专辑列表 | PC独立字段<br>非PC合并到根级别 | V3/V4 |

---

## 字段详细说明

### 1. itemInfo（商品主体信息）

商品的核心信息对象，包含商品的所有基本属性。

#### 基本信息

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **goods_id** | Integer | 商品ID（内部使用） |
| **item_number** | String | 商品编号（唯一标识） |
| **item_type** | Integer | 商品类型 |
| **business_type** | Integer | 业务类型 |
| **slug** | String | 商品URL友好标识 |
| **giftcard_item** | String | 是否礼品卡商品 |

#### 商品描述

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **item_title** | String | 商品标题 |
| **line_description** | String | 商品简短描述 |
| **selling_point** | String | 商品卖点 |
| **detail_specification** | String | 详细规格说明（HTML） |
| **overview** | String | 商品概述（HTML） |
| **goods_disclaimer** | String | 免责声明 |
| **oneDesc** | String | 商品一句话描述 |
| **oneDescColor** | String | 一句话描述颜色（默认：#333333） |

#### 图片信息

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **images** | Array<String> | 商品图片URL列表 |
| **primary_image** | String | 主图URL |
| **image_alt** | String | 图片alt文本（SEO） |
| **videoAndImageList** | Array<Object> | 视频和图片混合列表 |
| **itemImageList** | Array<Object> | 商品图片详细列表 |
| **nfImageList** | Array<String> | 营养成分图片列表 |

#### 价格信息

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **market_price** | BigDecimal | 市场价 |
| **promotion_price** | BigDecimal | 促销价 |
| **is_promotion** | String | 是否促销：Y / N |
| **promote_start_date** | Long | 促销开始时间（时间戳） |
| **promote_end_date** | Long | 促销结束时间（时间戳） |
| **unit_price** | BigDecimal | 单价 |
| **member_price** | BigDecimal | 会员价（秒杀时为null） |
| **member_status** | Integer | 会员价状态 |
| **member_start_time** | Long | 会员价开始时间 |
| **member_end_time** | Long | 会员价结束时间 |
| **promotion_description** | String | 促销语（全国商品） |
| **promotion_desc** | String | 促销语描述 |
| **price_style** | Object | 大促样式对象 |
| **price_style_str** | String | 大促样式字符串（内部使用） |

#### 秒杀信息

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **seckill_status** | Integer | 秒杀状态：0=未开始, 1=即将开始, 2=进行中, 3=已结束 |
| **seckill_price** | BigDecimal | 秒杀价 |
| **seckill_start_time** | Long | 秒杀开始时间（时间戳） |
| **seckill_end_time** | Long | 秒杀结束时间（时间戳） |

#### 礼品卡信息

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **giftcard_price** | BigDecimal | 礼品卡价格 |
| **giftcard_status** | Integer | 礼品卡状态 |
| **giftcard_start_time** | Long | 礼品卡开始时间 |
| **giftcard_end_time** | Long | 礼品卡结束时间 |

#### 库存与限购

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **goods_number** | Integer | 库存数量 |
| **stock_warning** | Integer | 低库存提醒阈值 |
| **limit_quantity** | Integer | 限购数量 |
| **weekly_qty** | String | 周销量 |
| **weekly_qty_sign** | String | 周销量标识 |
| **isShareInventory** | Integer | 是否共享库存：1=是, 0=否 |

#### 标签与标识

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **new_arrival** | String | 是否新品 |
| **quality** | String | 品质标识 |
| **hot** | String | 是否热卖 |
| **is_district** | String | 是否区域商品 |
| **is_favorite** | Integer | 是否已收藏 |
| **is_gift** | Integer | 是否赠品 |
| **is_canada** | Integer | 是否加拿大商品 |
| **is_uk** | Integer | 是否英国商品 |
| **is_limit_area** | Boolean | 是否限制区域 |
| **tags** | Array<Object> | 商品标签列表 |

#### 评论与积分

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **rate** | BigDecimal | 商品评分 |
| **comment_count** | Integer | 评论数量 |
| **point_rate** | BigDecimal | 积分比例 |

#### 品牌信息（内嵌）

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **brand_id** | Integer | 品牌ID |
| **brand_name** | String | 品牌名称 |
| **brand_logo** | String | 品牌Logo URL |
| **brand_item_count** | Integer | 品牌商品总数 |

#### 分类信息（内嵌）

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **category_id** | Integer | 分类ID |
| **category_name** | String | 分类名称 |
| **category_slug** | String | 分类URL标识 |
| **category_pic_url** | String | 分类图片URL |
| **parent_category_id** | Integer | 父分类ID |
| **parent_category_name** | String | 父分类名称 |
| **parent_category_slug** | String | 父分类URL标识 |
| **parent_category_pic_url** | String | 父分类图片URL |
| **grand_parent_category_id** | Integer | 祖父分类ID |
| **grand_parent_category_name** | String | 祖父分类名称 |
| **grand_parent_category_slug** | String | 祖父分类URL标识 |
| **grand_parent_category_pic_url** | String | 祖父分类图片URL |

#### 卖家信息（内嵌）

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **seller_id** | Integer | 卖家ID |
| **seller_sn** | String | 卖家编号 |
| **seller_name** | String | 卖家名称 |
| **seller_logo** | String | 卖家Logo URL |
| **seller_item_count** | Integer | 卖家商品总数 |
| **seller_shipping_list** | Array<Object> | 卖家邮寄规则列表 |

#### 促销与活动

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **promotion_name** | String | 促销活动名称（来自mkt） |
| **group** | Object | 商品组信息 |
| **group_id** | Integer | 商品组ID |
| **mkt_ps_id** | Integer | 正在生效的活动ID |

#### 配送信息

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **expectedTime** | Object | 预计配送时间 |
| **declare_progress** | String | 集运发货进度 |
| **sameDayDeliveryInfo** | Object | 一日达配送信息 |

#### 规格与单位

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **specification** | Double | 规格（默认：1.0） |
| **bundle_num** | Integer | 捆绑数量 |
| **unit** | String | 单位 |
| **unit_cn** | String | 单位（中文，默认："件"） |
| **unit_en** | String | 单位（英文，默认："piece"） |

#### 区域价格

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **limitIds** | Array<Integer> | 限制ID列表 |
| **localPrice** | Array<Object> | 区域价格设置列表 |

#### 排名信息

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **ranking_info** | Object | 商品排名信息 |

#### 用户购买数据

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **purchaseAvatorList** | Array<String> | 已购买用户头像列表 |
| **purchaseCount** | Integer | 已购买人数 |

#### 法案信息

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **itemNumberActList** | Array<Object> | 商品法案列表 |
| **categoryActList** | Array<Object> | 分类法案列表（兜底） |
| **itemActVOList** | Array<Object> | 前端展示的州法案列表 |

#### 预售信息（年货节）

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **shelf_life** | Integer | 预售商品保质期（天） |
| **estimated_arrival_start** | Long | 预计到货开始时间（时间戳） |
| **estimated_arrival_end** | Long | 预计到货结束时间（时间戳） |

#### 其他

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **clone_type** | Integer | 克隆类型 |
| **paypal** | Integer | PayPal支付（默认：0） |
| **original_id** | String | 原始商品ID |
| **original_slug** | String | 原始商品Slug |

---

### 2. sellerInfo（商家信息）

商家的详细信息，包括评分、服务、邮寄规则等。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **seller_id** | Integer | 卖家ID |
| **seller_sn** | String | 卖家编号 |
| **vendor_sn** | String | 卖家新标识 |
| **seller_name** | String | 卖家名称 |
| **logo** | String | 卖家Logo URL |
| **logo_color** | String | 卖家Logo颜色版 |
| **slug** | String | 卖家URL标识 |
| **item_count** | Integer | 所售商品总数 |
| **join_time** | Long | 入驻时间（时间戳） |
| **deposit** | BigDecimal | 保证金 |
| **multiple_rate** | Double | 综合评分 |
| **shopping_rate** | Double | 购物体验评分 |
| **items_rate** | Double | 商品评分 |
| **avg_shopping_rate** | Double | 购物体验平均分 |
| **avg_items_rate** | Double | 商品平均分 |
| **avg_multiple_rate** | BigDecimal | 综合平均分（计算值：(avg_shopping_rate + avg_items_rate) / 2） |
| **services** | Array<Object> | 商家服务列表 |
| **shippings** | Array<Object> | 邮寄规则列表 |
| **free_shipping_amount** | BigDecimal | 免邮金额 |
| **shipping_fee** | BigDecimal | 邮费 |
| **shipping_info** | String | 邮费信息 |
| **return_policy** | String | 退换货规则 |
| **delivery** | String | 邮寄规则说明 |
| **quality** | String | 正品保证说明 |
| **points** | String | 积分规则说明 |
| **bannerInfo** | Array<Object> | Banner信息列表 |
| **ship_type** | Integer | 配送类型 |
| **declare_progress** | String | 集运发货方式 |
| **seller_coupon** | Array<Object> | 商家优惠券列表 |
| **expectedTime** | Object | 预计配送时间 |
| **seller_tier_badge** | Object | 商家等级徽章 |
| **actual_business** | Integer | 商家类型：<br>-1=未上架商品<br>0=同时在卖FBY和直邮<br>1=只卖直邮<br>2=只卖FBY |

---

### 3. brandInfo（品牌信息）

品牌的详细信息。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **brand_id** | Integer | 品牌ID |
| **brand_name** | String | 品牌名称 |
| **logo** | String | 品牌Logo URL |
| **logo_color** | String | 品牌Logo颜色版 |
| **index** | String | 首字母索引 |
| **origin_country** | String | 品牌产地 |
| **slug** | String | 品牌URL标识 |
| **desc** | String | 品牌描述 |
| **item_count** | Integer | 品牌商品总数 |
| **bannerInfo** | Array<Object> | Banner信息列表 |

---

### 4. giftActivityList（赠品活动列表）

赠品活动信息数组，每个元素包含一个赠品活动的详细信息。

**数组元素结构**：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **activity_id** | Integer | 活动ID |
| **activity_name** | String | 活动名称 |
| **activity_type** | String | 活动类型 |
| **start_time** | Long | 活动开始时间（时间戳） |
| **end_time** | Long | 活动结束时间（时间戳） |
| **gift_list** | Array<Object> | 赠品列表 |
| **rule_desc** | String | 规则描述 |

**注意**：V4已过滤掉包含"Gift out of stock"字样的赠品。

---

### 5. itemDescList（商品属性列表）

商品的详细属性信息数组。

**数组元素结构**：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **attr_name** | String | 属性名称（如"品牌"、"产地"等） |
| **attr_value** | String | 属性值 |
| **attr_type** | String | 属性类型 |
| **sort_order** | Integer | 排序顺序 |

---

### 6. same_day_delivery（一日达配送信息）

一日达配送的相关信息。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **is_available** | Boolean | 是否支持一日达 |
| **delivery_time** | String | 配送时间说明 |
| **cutoff_time** | String | 截单时间 |
| **fee** | BigDecimal | 配送费用 |
| **min_amount** | BigDecimal | 最低订单金额 |

---

### 7. group（商品组信息）

商品组的详细信息，包含组内所有商品。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **group_id** | Integer | 商品组ID |
| **group_name** | String | 商品组名称 |
| **group_type** | String | 商品组类型 |
| **groupItemList** | Array<Object> | 商品组内商品列表 |

**groupItemList 元素结构**（V4新增字段）：

| 字段名 | 类型 | 说明 | 版本 |
|--------|------|------|------|
| **item_number** | String | 商品编号 | V3/V4 |
| **item_title** | String | 商品标题 | V3/V4 |
| **image** | String | 商品图片 | V3/V4 |
| **price** | BigDecimal | 商品价格 | V3/V4 |
| **is_limit_area** | Boolean | 是否限制区域（仅非PC端） | ✨ V4 |
| **is_instock** | Integer | 库存状态（仅非PC端） | ✨ V4 |
| **status** | String | 商品状态：A=可售, D=下架（仅非PC端） | ✨ V4 |

---

### 8. itemRelateUserData（用户趋势数据）✨ V4新增

展示商品的用户购买和浏览趋势，用于社交证明。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **purchaseCount** | Integer | 购买人数 |
| **viewCount** | Integer | 浏览人数 |
| **avatarList** | Array<String> | 用户头像URL列表 |
| **trend** | String | 趋势描述（如"热卖中"） |

**使用场景**：
- 展示"XX人已购买"
- 展示用户头像墙
- 增强用户信任感和购买决策

---

### 9. tabList（Tab展示顺序）✨ V4新增

APP端前三个Tab的展示顺序配置。

**数组元素结构**：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **id** | String | Tab标识（如"detail"、"spec"、"nutrition"） |
| **name** | String | Tab名称（多语言） |
| **order** | Integer | 排序顺序 |
| **visible** | Boolean | 是否可见 |

**Tab类型**：
- `detail`: 商品详情
- `spec`: 规格参数
- `nutrition`: 营养成分

---

### 10. nutritionalFacts（营养成分图片）✨ V4新增

营养成分表图片URL列表。

**类型**：`Array<String>`

**说明**：
- 仅当配置开关 `PDP_NI_SHOW = "YES"` 时返回
- 图片URL格式：`{NF_IMAGE_PREFIX}{imageId}{NF_IMAGE_SUFFIX}`
- 用于展示商品的营养成分信息

---

### 11. psCode（优惠券码）✨ V4新增

可复制的优惠券码。

**类型**：`String | null`

**说明**：
- 有优惠券时返回优惠券码
- 无优惠券时为 `null`
- 前端可添加一键复制功能

---

### 12. couponCodeDesc（优惠券描述）✨ V4新增

智能生成的促销文案。

**类型**：`String`

**生成逻辑**：
1. 如果没有优惠券：返回 `itemInfo.promotion_desc`
2. 如果有优惠券但没有促销语：返回 `{优惠券名称}, 优惠码: {psCode}`
3. 如果有优惠券且有促销语：返回 `itemInfo.promotion_desc`

**使用建议**：优先使用此字段而非 `itemInfo.promotion_desc`

---

### 13. badgeList（商品徽章列表）✨ V4新增

商品徽章标签列表，用于突出商品特性。

**数组元素结构**：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **badge_id** | Integer | 徽章ID |
| **badge_name** | String | 徽章名称（如"新品"、"热卖"、"限时"） |
| **badge_type** | String | 徽章类型 |
| **badge_color** | String | 徽章颜色 |
| **badge_icon** | String | 徽章图标URL |
| **sort_order** | Integer | 排序顺序 |

---

### 14. itemTags（商品标签）✨ V4新增

商品的标签信息列表。

**类型**：`Array<Map<String, Object>>`

**说明**：用于商品的分类和筛选标记。

---

### 15. vipTipInfo（会员升级提示）

会员升级相关的提示信息。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **show_tip** | Boolean | 是否显示提示 |
| **tip_content** | String | 提示内容 |
| **upgrade_url** | String | 升级链接 |
| **benefit_list** | Array<String> | 会员权益列表 |

---

### 16. config（配置信息）

商品详情页的配置信息。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **show_comment** | Boolean | 是否显示评论 |
| **show_rating** | Boolean | 是否显示评分 |
| **show_inventory** | Boolean | 是否显示库存 |
| **show_sales** | Boolean | 是否显示销量 |
| **enable_cart** | Boolean | 是否启用购物车 |
| **enable_wishlist** | Boolean | 是否启用收藏 |

---

### 17. currency（货币符号）

当前商品使用的货币符号。

**类型**：`String`

**取值**：
- `"$"`: 美元（默认）
- `"US $"`: 加拿大地区显示

**判断逻辑**：根据用户所在国家（whNumber.country_name）判断。

---

### 18. purchaseProtection（购物保障）✨ V4新增（仅non用户）

购物保障信息，仅对non用户（未登录/新用户）显示。

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **title** | String | 保障标题 |
| **items** | Array<Object> | 保障项目列表 |

**items 元素结构**：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **icon** | String | 图标URL |
| **name** | String | 保障名称（如"正品保证"、"退换货保障"） |
| **desc** | String | 保障描述 |

---

### 19. recommendTabList（推荐Tab列表）

推荐商品的Tab列表。

**数组元素结构**：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **tab_id** | String | Tab标识 |
| **tab_name** | String | Tab名称 |
| **item_list** | Array<Object> | 推荐商品列表 |

---

### 20. topImage（顶部图片）✨ V4新增（仅non用户）

商品详情页顶部Banner图片URL。

**类型**：`String`

**说明**：
- 仅对non用户显示
- 从配置 `PDP_TOP_IMAGES` 根据语言获取
- 用于展示营销Banner

---

### 21. topImageBackgroundColor（顶部图片背景色）✨ V4新增（仅non用户）

顶部图片的背景颜色。

**类型**：`String`

**说明**：
- 仅当有 `topImage` 时返回
- 用于设置Banner的背景色

---

### 22. userType（用户类型）✨ V4新增

用户类型标识，用于差异化展示。

**类型**：`String`

**可能值**：
- `"U1"`: non用户（未登录/新用户）
- `"U2"`: 普通用户
- `"U3"`: 会员用户

**使用场景**：
- 判断是否展示 `purchaseProtection`、`topImage` 等non用户专属内容
- 差异化展示会员专属内容

---

### 23. googleSeo（SEO信息）- 仅PC端

Google SEO相关的结构化数据。

**类型**：`String`（JSON字符串）

**说明**：
- 仅PC端返回
- 包含商品的结构化数据标记
- 用于搜索引擎优化

---

### 24. collectionPage（关联商品链接）- 仅PC端

关联商品的链接列表。

**类型**：`Array<Object>`

**数组元素结构**：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **collection_id** | Integer | 专辑ID |
| **collection_name** | String | 专辑名称 |
| **collection_url** | String | 专辑链接 |
| **item_count** | Integer | 商品数量 |

**说明**：仅PC端返回。

---

### 25. essayList（专辑列表）

商品关联的专辑信息。

**平台差异**：
- **PC端**：独立字段 `essayList`，类型为 `Array<Object>`
- **非PC端**：专辑数据被拆散合并到返回数据的根级别

**PC端数组元素结构**：

| 字段名 | 类型 | 说明 |
|--------|------|------|
| **essay_id** | Integer | 专辑ID |
| **essay_title** | String | 专辑标题 |
| **essay_cover** | String | 专辑封面URL |
| **essay_url** | String | 专辑链接 |
| **item_count** | Integer | 商品数量 |

**注意**：非PC端需要根据实际返回的字段名读取专辑数据。

---

## 使用示例

### 示例1：基础请求

```javascript
// JavaScript/TypeScript 示例
const response = await fetch('/items/getItemInfoAllV4?item_number=1234567', {
  method: 'GET',
  headers: {
    'token': 'your_token_here',
    'y_platform': 'pc',
    'y_language': 'zh_CN',
    'CF-Connecting-IP': '192.168.1.1'
  }
});

const data = await response.json();

if (data.code === 200) {
  const itemInfo = data.data.itemInfo;
  console.log('商品标题:', itemInfo.item_title);
  console.log('商品价格:', itemInfo.promotion_price || itemInfo.market_price);
  console.log('库存数量:', itemInfo.goods_number);
}
```

---

### 示例2：使用V4新增功能

```javascript
// 展示用户趋势数据
if (data.data.itemRelateUserData) {
  const userData = data.data.itemRelateUserData;
  console.log(`${userData.purchaseCount}人已购买`);
  
  // 展示用户头像
  if (userData.avatarList && userData.avatarList.length > 0) {
    renderAvatars(userData.avatarList);
  }
}

// 展示优惠券信息
if (data.data.psCode) {
  console.log('优惠券码:', data.data.psCode);
  console.log('优惠券描述:', data.data.couponCodeDesc);
  // 添加复制功能
  addCopyButton(data.data.psCode);
}

// 展示营养成分
if (data.data.nutritionalFacts && data.data.nutritionalFacts.length > 0) {
  renderNutritionImages(data.data.nutritionalFacts);
}

// 展示商品徽章
if (data.data.badgeList && data.data.badgeList.length > 0) {
  renderBadges(data.data.badgeList);
}

// 根据用户类型差异化展示
if (data.data.userType === 'U1') {
  // non用户专属内容
  if (data.data.topImage) {
    renderTopBanner(data.data.topImage, data.data.topImageBackgroundColor);
  }
  if (data.data.purchaseProtection) {
    renderPurchaseProtection(data.data.purchaseProtection);
  }
}
```

---

### 示例3：处理商品组（非PC端）

```javascript
// V4新增：商品组区域限制判断
if (data.data.group && data.data.group.groupItemList) {
  data.data.group.groupItemList.forEach(item => {
    console.log('商品:', item.item_title);
    
    // V4新增字段
    if (item.is_limit_area) {
      console.log('  ⚠️ 该商品在当前区域限制销售');
    }
    
    if (!item.is_instock) {
      console.log('  ❌ 该商品无库存');
    }
    
    if (item.status === 'D') {
      console.log('  ❌ 该商品已下架');
    }
  });
}
```

---

### 示例4：处理秒杀商品

```javascript
const itemInfo = data.data.itemInfo;

// 判断秒杀状态
switch (itemInfo.seckill_status) {
  case 0:
    console.log('秒杀未开始');
    break;
  case 1:
    console.log('秒杀即将开始');
    console.log('开始时间:', new Date(itemInfo.seckill_start_time));
    break;
  case 2:
    console.log('秒杀进行中');
    console.log('秒杀价:', itemInfo.seckill_price);
    console.log('结束时间:', new Date(itemInfo.seckill_end_time));
    // 注意：秒杀时 member_price 为 null
    break;
  case 3:
    console.log('秒杀已结束');
    break;
}
```

---

## 注意事项

### 1. 平台差异

#### PC端特有字段
- `googleSeo`: SEO结构化数据
- `collectionPage`: 关联商品链接
- `essayList`: 专辑列表（独立字段）

#### 非PC端特有处理
- `essayList`: 专辑数据被拆散合并到根级别
- `group.groupItemList`: 包含 `is_limit_area`、`is_instock`、`status` 等区域限制字段
- 执行 `filterGroupItemLimit` 过滤不可售商品

---

### 2. 用户类型差异

#### non用户（userType = "U1"）专属字段
- `purchaseProtection`: 购物保障信息
- `topImage`: 顶部Banner图片
- `topImageBackgroundColor`: 顶部Banner背景色

**使用建议**：根据 `userType` 字段判断是否展示这些内容。

---

### 3. 价格显示逻辑

#### 价格优先级（从高到低）
1. **秒杀价**（`seckill_status = 2` 且 `seckill_price` 存在）
2. **促销价**（`is_promotion = "Y"` 且 `promotion_price` 存在）
3. **会员价**（`member_status` 有效 且 `member_price` 存在）
4. **市场价**（`market_price`）

#### 特殊规则
- 秒杀进行中时，`member_price` 会被设置为 `null`
- 礼品卡商品使用 `giftcard_price`

```javascript
// 价格显示逻辑示例
function getDisplayPrice(itemInfo) {
  // 秒杀价
  if (itemInfo.seckill_status === 2 && itemInfo.seckill_price) {
    return {
      price: itemInfo.seckill_price,
      type: 'seckill',
      label: '秒杀价'
    };
  }
  
  // 促销价
  if (itemInfo.is_promotion === 'Y' && itemInfo.promotion_price) {
    return {
      price: itemInfo.promotion_price,
      type: 'promotion',
      label: '促销价'
    };
  }
  
  // 会员价
  if (itemInfo.member_price) {
    return {
      price: itemInfo.member_price,
      type: 'member',
      label: '会员价'
    };
  }
  
  // 市场价
  return {
    price: itemInfo.market_price,
    type: 'market',
    label: '价格'
  };
}
```

---

### 4. 库存显示

#### 库存状态判断
```javascript
function getStockStatus(itemInfo) {
  const stock = itemInfo.goods_number;
  const warning = itemInfo.stock_warning || 10;
  
  if (stock <= 0) {
    return { status: 'out', text: '缺货', color: 'red' };
  } else if (stock <= warning) {
    return { status: 'low', text: `仅剩${stock}件`, color: 'orange' };
  } else {
    return { status: 'normal', text: '有货', color: 'green' };
  }
}
```

---

### 5. 促销文案显示

#### 推荐使用顺序
1. **优先使用** `couponCodeDesc`（V4智能生成的促销文案）
2. **备选** `itemInfo.promotion_desc`
3. **兜底** `itemInfo.promotion_description`

```javascript
// 促销文案显示逻辑
function getPromotionText(data) {
  return data.couponCodeDesc 
    || data.itemInfo?.promotion_desc 
    || data.itemInfo?.promotion_description 
    || '';
}
```

---

### 6. 图片处理

#### 图片类型
- **主图**：`itemInfo.primary_image`
- **图片列表**：`itemInfo.images`（数组）
- **详细图片列表**：`itemInfo.itemImageList`（包含更多元数据）
- **视频和图片混合**：`itemInfo.videoAndImageList`
- **营养成分图片**：`nutritionalFacts`（V4新增）

#### 图片加载建议
```javascript
// 图片懒加载
function getImageList(itemInfo) {
  const images = [];
  
  // 主图
  if (itemInfo.primary_image) {
    images.push({
      url: itemInfo.primary_image,
      type: 'primary',
      priority: 'high'
    });
  }
  
  // 其他图片
  if (itemInfo.images && itemInfo.images.length > 0) {
    itemInfo.images.forEach((url, index) => {
      if (url !== itemInfo.primary_image) {
        images.push({
          url: url,
          type: 'gallery',
          priority: index < 3 ? 'high' : 'low'
        });
      }
    });
  }
  
  return images;
}
```

---

### 7. 回收站商品访问

如果需要访问回收站商品（已下架商品），需要提供 `recycle_key` 参数。

```javascript
// 访问回收站商品
const response = await fetch(
  '/items/getItemInfoAllV4?item_number=1234567&recycle_key=your_recycle_key',
  {
    headers: {
      'token': 'your_token',
      'y_platform': 'pc',
      'y_language': 'zh_CN'
    }
  }
);
```

**注意**：`recycle_key` 需要通过特定方式获取，不能随意访问。

---

### 8. 商品组操作标识

`group_op` 参数用于商品组内的特殊标记逻辑。

**格式**：`{last_viewed_item_number},{type}`

**类型说明**：
- `type = 1`: 商品组内切换商品
- `type = 2`: 商品组内点击过last pdp

**示例**：
```javascript
// 从商品A切换到商品B
const response = await fetch(
  '/items/getItemInfoAllV4?item_number=B123456&group_op=A123456,1',
  { /* headers */ }
);
```

---

### 9. 性能优化建议

#### 数据获取优化
V4接口采用批处理任务并行查询，相比V3有以下优化：

1. **并行查询**：7个批处理任务同时执行
2. **异步处理**：商家信息、品牌信息、用户趋势数据等异步获取
3. **缓存利用**：商家信息、品牌信息使用Redis缓存

#### 前端优化建议
```javascript
// 1. 按需加载数据
function loadProductPage(itemNumber) {
  // 首屏必需数据
  const essentialData = [
    'itemInfo',
    'sellerInfo',
    'same_day_delivery',
    'currency'
  ];
  
  // 次要数据（可延迟加载）
  const secondaryData = [
    'itemRelateUserData',
    'badgeList',
    'recommendTabList'
  ];
  
  // 可选数据（按需加载）
  const optionalData = [
    'essayList',
    'collectionPage',
    'googleSeo'
  ];
}

// 2. 图片懒加载
function lazyLoadImages(images) {
  const observer = new IntersectionObserver((entries) => {
    entries.forEach(entry => {
      if (entry.isIntersecting) {
        const img = entry.target;
        img.src = img.dataset.src;
        observer.unobserve(img);
      }
    });
  });
  
  images.forEach(img => observer.observe(img));
}

// 3. 数据缓存
const cache = new Map();
function getCachedProductData(itemNumber) {
  if (cache.has(itemNumber)) {
    return cache.get(itemNumber);
  }
  
  return fetchProductData(itemNumber).then(data => {
    cache.set(itemNumber, data);
    return data;
  });
}
```

---

### 10. 错误处理

#### 常见错误码

| 错误码 | 说明 | 处理建议 |
|--------|------|---------|
| 200 | 成功 | 正常处理数据 |
| 400 | 参数错误 | 检查必填参数 |
| 401 | 未授权 | 检查token是否有效 |
| 404 | 商品不存在 | 显示商品不存在页面 |
| 500 | 服务器错误 | 显示错误提示，建议重试 |

#### 错误处理示例
```javascript
async function fetchProductData(itemNumber) {
  try {
    const response = await fetch(`/items/getItemInfoAllV4?item_number=${itemNumber}`, {
      headers: {
        'token': getToken(),
        'y_platform': getPlatform(),
        'y_language': getLanguage()
      }
    });
    
    const data = await response.json();
    
    if (data.code === 200) {
      return data.data;
    } else if (data.code === 404) {
      showNotFoundPage();
    } else {
      showErrorMessage(data.message || '获取商品信息失败');
    }
  } catch (error) {
    console.error('请求失败:', error);
    showErrorMessage('网络错误，请稍后重试');
  }
}
```

---

### 11. 数据校验

#### 必要的数据校验
```javascript
function validateProductData(data) {
  const errors = [];
  
  // 检查商品信息
  if (!data.itemInfo) {
    errors.push('缺少商品信息');
  } else {
    if (!data.itemInfo.item_number) {
      errors.push('缺少商品编号');
    }
    if (!data.itemInfo.item_title) {
      errors.push('缺少商品标题');
    }
    if (data.itemInfo.goods_number === undefined) {
      errors.push('缺少库存信息');
    }
  }
  
  // 检查价格信息
  if (!data.itemInfo?.market_price && !data.itemInfo?.promotion_price) {
    errors.push('缺少价格信息');
  }
  
  // 检查图片
  if (!data.itemInfo?.images || data.itemInfo.images.length === 0) {
    errors.push('缺少商品图片');
  }
  
  return {
    valid: errors.length === 0,
    errors: errors
  };
}
```

---

### 12. TypeScript 类型定义

完整的TypeScript类型定义供参考：

```typescript
// 主响应类型
interface GetItemInfoAllV4Response {
  code: number;
  message: string;
  data: ProductDetailData;
}

// 商品详情数据
interface ProductDetailData {
  itemInfo: ItemInfo;
  sellerInfo: SellerInfo | null;
  brandInfo: BrandInfo | null;
  giftActivityList: GiftActivity[];
  itemDescList: ItemDesc[];
  same_day_delivery: SameDayDelivery;
  group?: Group;
  config: Config;
  currency: string;
  
  // V4新增字段
  itemRelateUserData?: ItemRelateUserData;
  tabList?: TabItem[];
  nutritionalFacts?: string[];
  psCode?: string;
  couponCodeDesc?: string;
  badgeList?: Badge[];
  itemTags?: ItemTag[];
  vipTipInfo?: VipTipInfo;
  recommendTabList?: RecommendTab[];
  
  // non用户专属
  purchaseProtection?: PurchaseProtection;
  topImage?: string;
  topImageBackgroundColor?: string;
  userType?: 'U1' | 'U2' | 'U3';
  
  // PC端专属
  googleSeo?: string;
  collectionPage?: CollectionPage[];
  essayList?: Essay[];
}

// 商品信息
interface ItemInfo {
  goods_id: number;
  item_number: string;
  item_type: number;
  business_type: number;
  slug: string;
  item_title: string;
  line_description: string;
  selling_point: string;
  images: string[];
  primary_image: string;
  detail_specification: string;
  overview: string;
  market_price: number;
  promotion_price?: number;
  is_promotion: 'Y' | 'N';
  member_price?: number;
  seckill_status?: 0 | 1 | 2 | 3;
  seckill_price?: number;
  seckill_start_time?: number;
  seckill_end_time?: number;
  goods_number: number;
  stock_warning: number;
  limit_quantity?: number;
  rate: number;
  comment_count: number;
  point_rate: number;
  brand_id: number;
  brand_name: string;
  brand_logo: string;
  category_id: number;
  category_name: string;
  seller_id: number;
  seller_sn: string;
  seller_name: string;
  promotion_desc?: string;
  oneDesc?: string;
  oneDescColor?: string;
  nfImageList?: string[];
  purchaseAvatorList?: string[];
  purchaseCount?: number;
  // ... 其他字段
}

// 商家信息
interface SellerInfo {
  seller_id: number;
  seller_sn: string;
  vendor_sn: string;
  seller_name: string;
  logo: string;
  logo_color: string;
  slug: string;
  item_count: number;
  join_time: number;
  deposit: number;
  multiple_rate: number;
  shopping_rate: number;
  items_rate: number;
  avg_shopping_rate: number;
  avg_items_rate: number;
  avg_multiple_rate: number;
  services: SellerService[];
  shippings: Shipping[];
  free_shipping_amount: number;
  shipping_fee: number;
  return_policy: string;
  seller_tier_badge?: SellerTierBadge;
  actual_business: -1 | 0 | 1 | 2;
}

// 品牌信息
interface BrandInfo {
  brand_id: number;
  brand_name: string;
  logo: string;
  logo_color: string;
  index: string;
  origin_country: string;
  slug: string;
  desc: string;
  item_count: number;
  bannerInfo: BannerInfo[];
}

// 用户趋势数据（V4新增）
interface ItemRelateUserData {
  purchaseCount?: number;
  viewCount?: number;
  avatarList?: string[];
  trend?: string;
}

// Tab项（V4新增）
interface TabItem {
  id: string;
  name: string;
  order: number;
  visible: boolean;
}

// 商品徽章（V4新增）
interface Badge {
  badge_id: number;
  badge_name: string;
  badge_type: string;
  badge_color: string;
  badge_icon: string;
  sort_order: number;
}

// 购物保障（V4新增，non用户）
interface PurchaseProtection {
  title: string;
  items: ProtectionItem[];
}

interface ProtectionItem {
  icon: string;
  name: string;
  desc: string;
}

// 商品组
interface Group {
  group_id: number;
  group_name: string;
  group_type: string;
  groupItemList: GroupItem[];
}

interface GroupItem {
  item_number: string;
  item_title: string;
  image: string;
  price: number;
  // V4新增（非PC端）
  is_limit_area?: boolean;
  is_instock?: number;
  status?: 'A' | 'D';
}

// 赠品活动
interface GiftActivity {
  activity_id: number;
  activity_name: string;
  activity_type: string;
  start_time: number;
  end_time: number;
  gift_list: Gift[];
  rule_desc: string;
}

// 商品属性
interface ItemDesc {
  attr_name: string;
  attr_value: string;
  attr_type: string;
  sort_order: number;
}

// 一日达配送
interface SameDayDelivery {
  is_available: boolean;
  delivery_time?: string;
  cutoff_time?: string;
  fee?: number;
  min_amount?: number;
}

// 配置信息
interface Config {
  show_comment: boolean;
  show_rating: boolean;
  show_inventory: boolean;
  show_sales: boolean;
  enable_cart: boolean;
  enable_wishlist: boolean;
}
```

---

### 13. 与V3版本的主要差异

详细的V3与V4对比请参考：[V3-V4字段详细对比-前端迁移指南.md](./V3-V4字段详细对比-前端迁移指南.md)

#### 主要新增功能
1. ✨ **用户趋势数据**（`itemRelateUserData`）：展示购买人数、用户头像等社交证明
2. ✨ **商品徽章**（`badgeList`）：突出商品特性
3. ✨ **营养成分图片**（`nutritionalFacts`）：展示营养成分表
4. ✨ **优惠券优化**（`psCode`、`couponCodeDesc`）：更智能的促销文案
5. ✨ **Tab配置**（`tabList`）：替代V3的need_tabs参数
6. ✨ **用户类型区分**（`userType`）：支持差异化展示
7. ✨ **non用户专属内容**（`purchaseProtection`、`topImage`）：增强新用户信任

#### 主要变化
1. ⚠️ **字段名变化**：`gift_list` → `giftActivityList`
2. ⚠️ **商品组增强**：非PC端新增区域限制字段
3. ⚠️ **专辑数据**：非PC端的essayList被拆散合并到根级别
4. ⚠️ **数据获取方式**：更多异步并行查询，性能优化

---

## 常见问题（FAQ）

### Q1: V4接口的响应时间比V3慢吗？
**A**: 不会。V4采用了更多的并行查询和批处理任务，理论上性能应该相当或更好。如果遇到性能问题，请检查网络环境和服务器状态。

### Q2: 如何判断商品是否可以购买？
**A**: 需要综合判断多个条件：
```javascript
function canPurchase(itemInfo, group) {
  // 1. 检查库存
  if (itemInfo.goods_number <= 0) return false;
  
  // 2. 检查商品状态（如果在商品组中）
  if (group) {
    const currentItem = group.groupItemList.find(
      item => item.item_number === itemInfo.item_number
    );
    if (currentItem?.status === 'D') return false;
    if (currentItem?.is_limit_area) return false;
    if (!currentItem?.is_instock) return false;
  }
  
  // 3. 检查是否回收站商品（需要特殊权限）
  // 根据业务逻辑判断
  
  return true;
}
```

### Q3: 如何处理秒杀商品的倒计时？
**A**: 根据 `seckill_status` 和时间戳计算：
```javascript
function getSeckillCountdown(itemInfo) {
  const now = Date.now();
  
  if (itemInfo.seckill_status === 1) {
    // 即将开始
    return {
      type: 'start',
      countdown: itemInfo.seckill_start_time - now
    };
  } else if (itemInfo.seckill_status === 2) {
    // 进行中
    return {
      type: 'end',
      countdown: itemInfo.seckill_end_time - now
    };
  }
  
  return null;
}
```

### Q4: 营养成分图片什么时候显示？
**A**: 需要同时满足两个条件：
1. 配置开关 `PDP_NI_SHOW = "YES"`（后端控制）
2. `nutritionalFacts` 数组不为空

### Q5: non用户专属内容如何判断？
**A**: 通过 `userType` 字段判断：
```javascript
const isNonUser = data.userType === 'U1';
if (isNonUser) {
  // 展示 purchaseProtection、topImage 等
}
```

### Q6: 如何处理多语言？
**A**: 通过 `y_language` 请求头参数控制：
- `zh_CN`: 中文
- `en_US`: 英文

大部分文本字段会根据语言返回对应的翻译。

### Q7: 商品组内切换商品需要传什么参数？
**A**: 使用 `group_op` 参数：
```javascript
// 从商品A切换到商品B
const url = `/items/getItemInfoAllV4?item_number=B123456&group_op=A123456,1`;
```

---

## 更新日志

### V4版本（当前）
- ✨ 新增用户趋势数据
- ✨ 新增商品徽章列表
- ✨ 新增营养成分图片
- ✨ 新增优惠券码和智能促销文案
- ✨ 新增Tab展示顺序配置
- ✨ 新增用户类型区分
- ✨ 新增non用户专属内容
- ⚡ 优化数据获取性能（批处理任务）
- ⚡ 优化商品组区域限制判断（非PC端）
- 🔄 字段名变化：gift_list → giftActivityList

### V3版本
- 基础商品详情功能
- 商家、品牌、赠品、专辑信息
- 秒杀、促销、会员价支持

---

## 相关文档

- [V3-V4字段详细对比-前端迁移指南](./V3-V4字段详细对比-前端迁移指南.md)
- [V3-V4字段快速对比表](./V3-V4字段快速对比表.md)
- [getItemInfoAllV3接口文档](./getItemInfoAllV3接口文档.json)

---

## 联系方式

如有疑问或发现文档错误，请联系：
- **后端团队**：ec-item-service 开发组
- **文档维护**：技术文档组

---

**文档版本**: 1.0  
**最后更新**: 2026-01-29  
**维护状态**: ✅ 活跃维护中
