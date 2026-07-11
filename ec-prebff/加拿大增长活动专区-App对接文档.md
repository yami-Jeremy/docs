# 加拿大增长活动专区 - App 对接文档

> OP-37234 · 加拿大站第三个 Tabbar → 活动专区页面

## 1. 功能说明

加拿大站 App 底部第三个 tabbar 入口改为「活动专区」，页面展示竖屏平铺的 Banner 列表（从上到下排列），点击跳转到对应活动页面。

> **渲染方式：** 不使用首页的横屏轮播，改为竖屏平铺（从上到下排列所有 Banner 图片），每条 Banner 可点击跳转。

## 2. 接口信息

| 项目 | 说明 |
|------|------|
| 接口 | `GET /ec-prebff/pages/cms/canada_growth?page=1` |
| DEV 环境 | `https://dev-ecapi.yamibuy.tech` |
| 触发条件 | 加拿大站 App 第三个 tabbar 点击时调用 |

### 请求 Headers

| Header | 必填 | 说明 |
|--------|------|------|
| token | 是 | 用户登录 token |
| y_platform | 是 | `android` 或 `ios` |
| y_language | 是 | 语言：`zh_CN` / `en_US` / `ja_JP` / `ko_KR` / `zh_TW` / `fr_FR` |
| site_code | 是 | 固定传 `ca`（加拿大站必须传） |
| device_id | 是 | 设备 ID |
| ym_id | 是 | 用户匿名 ID |
| y_version | 是 | App 版本号（Android 如 252，iOS 如 20260108），影响部分组件是否返回 |

### 请求 Query 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| page | 是 | 分页页码，必须 >0。活动专区只有 1 页，固定传 `1` |
| excluded_items | 否 | 翻页时传。上一页返回的 `page.excluded_items` 值，用于跨页商品去重。第 1 页不传 |
| excluded_groups | 否 | 翻页时传。上一页返回的 `page.excluded_groups` 值，用于跨页商品组去重。第 1 页不传 |
| last_component_id | 否 | 翻页时可传。上一页最后一个组件的 component_config_id，用于精确定位分页起始位置 |

> **重要：** `site_code: ca` 必须传，否则后端无法路由到加拿大页面，接口会返回 null。`page` 必传且 >0，不传会返回 null。

## 3. 请求示例

```bash
curl -X GET \
  "https://dev-ecapi.yamibuy.tech/ec-prebff/pages/cms/canada_growth?page=1" \
  -H "token: {用户token}" \
  -H "y_platform: android" \
  -H "y_language: en_US" \
  -H "y_version: 252" \
  -H "site_code: ca" \
  -H "device_id: {设备ID}" \
  -H "ym_id: {匿名ID}"
```

## 4. 返回数据结构

```json
{
  "messageId": "10000",
  "success": "true",
  "body": {
    "components": [
      {
        "component_key": "cms_component_slide",
        "component_file": "cms_component_slide",
        "component_config_id": 1742645,
        "component_priority": 1,
        "properties": {
          "title": "Growth Zone",
          "border_style": "0",
          "style": "[]"
        },
        "dataObjectResult": [
          {
            "obj_id": 8059,
            "title": null,
            "aimUrl": "https://www.yami.com/ca/invite",
            "appAimUrl": "https://www.yami.com/ca/invite",
            "imageUrl": "https://cdn.yamibuy.net/mkpl/xxx.png",
            "imageUrl2": "https://cdn.yamibuy.net/mkpl/xxx.png",
            "imgScale": 1.13,
            "visibility_settings": 0,
            "start_time": 1783669411,
            "end_time": 1815205410,
            "background_color": "#FFF",
            "sub_title": "",
            "button_text": "",
            "is_shadow": 0,
            "is_sticky": 0,
            "has_item": 0,
            "interval_time": 5
          },
          {
            "obj_id": 8060,
            "aimUrl": "https://www.yami.com/ca/follow-buy",
            "appAimUrl": "https://www.yami.com/ca/follow-buy",
            "imageUrl": "https://cdn.yamibuy.net/mkpl/yyy.png"
          },
          {
            "obj_id": 8061,
            "aimUrl": "https://www.yami.com/ca/bargain",
            "appAimUrl": "https://www.yami.com/ca/bargain",
            "imageUrl": "https://cdn.yamibuy.net/mkpl/zzz.jpg"
          }
        ]
      }
    ],
    "page": {
      "page_key": "yamibuy_mobile_en_canada_growth_ca",
      "page_name": "Canada Growth Zone",
      "language": "en",
      "platform": "mobile",
      "hasNext": false,
      "page": 1,
      "total": 1
    }
  }
}
```

## 5. 字段说明

### components[0] — Banner 组件

| 字段 | 类型 | 说明 |
|------|------|------|
| component_key | String | 固定 `cms_component_slide` |
| properties.title | String | 组件标题（可选展示） |
| dataObjectResult | Array | Banner 列表，按 sort 排序 |

### dataObjectResult[n] — 单条 Banner

| 字段 | 类型 | 说明 |
|------|------|------|
| obj_id | Integer | Banner 唯一 ID |
| aimUrl | String | 跳转链接（H5） |
| appAimUrl | String | App 跳转链接（优先使用此字段） |
| imageUrl | String | Banner 图片地址 |
| imageUrl2 | String | 备用图片地址（可用于高分屏） |
| imgScale | Double | 图片高宽比（可用于占位计算） |
| background_color | String | Banner 背景色 |
| start_time | Long | 生效开始时间（Unix 秒） |
| end_time | Long | 生效结束时间（Unix 秒） |
| visibility_settings | Integer | 可见性：0=所有用户，1=新用户，2=老用户 |
| is_sticky | Integer | 是否置顶：0=否，1=是 |
| has_item | Integer | 是否有关联商品：0=否，1=是 |

## 6. App 端实现要求

- 加拿大站第三个 tabbar 点击时调用上述接口
- 从返回的 `components[0].dataObjectResult` 取 Banner 列表
- 渲染为**竖屏平铺**（从上到下排列所有 Banner 图片，非轮播）
- 每条 Banner 点击跳转到 `appAimUrl`（优先）或 `aimUrl`
- 图片使用 `imageUrl` 展示
- 可用 `imgScale` 计算图片占位高度（高度 = 宽度 × imgScale）
- Banner 数量不固定（运营配几条展示几条），做好列表滚动

> **与首页 Banner 的区别：**
> - 首页 Banner 是横屏轮播（自动翻页），本页是竖屏平铺（全部展示）
> - 首页有 `interval_time` 控制轮播间隔，本页不需要
> - 组件 key 相同（都是 `cms_component_slide`），数据结构相同，只是渲染方式不同

## 7. 注意事项

- `site_code: ca` header 必传，否则接口返回 null
- 接口和首页用的是同一个 `/pages/cms/{pageKey}`，只是 pageKey 不同
- 当 Banner 列表为空时，展示空态页面
- 支持下拉刷新（重新调接口）
- 目前 DEV 环境已可联调
