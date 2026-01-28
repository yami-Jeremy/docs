# 加入购物车接口文档

## 接口信息

- **接口名称**: 加入购物车并返回推荐商品
- **接口路径**: `/pages/hub/product/a2c`
- **请求方法**: `POST`
- **接口描述**: 将商品加入购物车，成功后返回购物车信息和推荐商品列表

## 请求参数

### Header 参数

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| token | String | 是 | - | 用户登录令牌 |
| device_id | String | 否 | "" | 神策设备ID |
| ym_id | String | 否 | - | 亚米ID |
| y_version | Integer | 否 | - | 应用版本号 |
| y_platform | String | 否 | "pc" | 平台标识（pc/app/h5） |
| y_language | String | 否 | "zh_CN" | 语言设置（zh_CN/en_US等） |
| source_flag | Integer | 否 | - | 来源标识 |

### Body 参数（JSON）

#### 请求体结构 - AddCartRequest

```json
{
  "extra_action": "pop_up",
  "page_name": "pdp",
  "add_cart_req": [
    {
      "goods_id": 123456,
      "item_number": "ITEM001",
      "qty": 2
    }
  ]
}
```

#### 字段说明

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| extra_action | String | 否 | 加购成功后的操作，枚举值：<br/>- `pop_up`: 打开侧边弹框 |
| page_name | String | 否 | 当前页面类型，枚举值：<br/>- `pdp`: 商品详情页<br/>- `search`: 搜索页<br/>- `category`: 分类页<br/>- `homePage`: 首页 |
| add_cart_req | Array | 是 | 加入购物车的商品列表 |

#### add_cart_req 数组元素结构

| 字段名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| goods_id | Integer | 否 | 商品ID |
| item_number | String | 否 | 商品编号 |
| qty | Integer | 是 | 加购数量 |

**注意**: `goods_id` 和 `item_number` 至少需要提供一个，用于标识商品。

## 返回参数

### 响应体结构 - BaseResponse

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "cart_info": {
      "cart_count": 5,
      "total_amount": "99.99"
    },
    "recommend_items": [
      {
        "item_number": "ITEM002",
        "item_name": "推荐商品名称",
        "price": "19.99",
        "image_url": "https://..."
      }
    ],
    "tabs": [
      {
        "tab_name": "为你推荐",
        "tab_key": "recommend"
      }
    ]
  }
}
```

### 字段说明

| 字段名 | 类型 | 说明 |
|--------|------|------|
| code | Integer | 响应状态码，200表示成功 |
| message | String | 响应消息 |
| data | Object | 响应数据对象 |

#### data 对象结构

| 字段名 | 类型 | 说明 |
|--------|------|------|
| cart_info | Object | 购物车信息 |
| cart_info.cart_count | Integer | 购物车商品总数量 |
| cart_info.total_amount | String | 购物车总金额 |
| recommend_items | Array | 推荐商品列表 |
| tabs | Array | 推荐商品的Tab分类 |

**注意**: `data` 为 Map 类型，具体返回字段可能根据业务场景有所不同。

## 请求示例

### cURL

```bash
curl -X POST 'https://api.yamibuy.com/pages/hub/product/a2c' \
  -H 'Content-Type: application/json' \
  -H 'token: your_token_here' \
  -H 'y_platform: pc' \
  -H 'y_language: zh_CN' \
  -d '{
    "extra_action": "pop_up",
    "page_name": "pdp",
    "add_cart_req": [
      {
        "item_number": "ITEM001",
        "qty": 2
      }
    ]
  }'
```

### JavaScript (Axios)

```javascript
axios.post('/pages/hub/product/a2c', {
  extra_action: 'pop_up',
  page_name: 'pdp',
  add_cart_req: [
    {
      item_number: 'ITEM001',
      qty: 2
    }
  ]
}, {
  headers: {
    'token': 'your_token_here',
    'y_platform': 'pc',
    'y_language': 'zh_CN'
  }
})
.then(response => {
  console.log(response.data);
})
.catch(error => {
  console.error(error);
});
```

## 错误码说明

| 错误码 | 说明 |
|--------|------|
| 200 | 请求成功 |
| 400 | 请求参数错误 |
| 401 | token无效或已过期 |
| 500 | 服务器内部错误 |

## 业务说明

1. **加购流程**: 用户在商品详情页或列表页点击加购按钮，前端调用此接口将商品添加到购物车
2. **推荐商品**: 加购成功后，接口会返回相关推荐商品，用于提升用户购物体验
3. **侧边弹框**: 当 `extra_action` 为 `pop_up` 时，前端应展示侧边购物车弹框
4. **批量加购**: `add_cart_req` 支持数组，可以一次性添加多个商品到购物车

## 更新记录

| 日期 | 版本 | 说明 |
|------|------|------|
| 2026-01-28 | v1.0 | 初始版本 |
