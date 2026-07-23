# 加拿大增长活动专区 - 接口对接文档

> OP-37234 · 加拿大站第三个 Tabbar → 活动专区页面

## 1. 功能说明

加拿大站 App/H5 底部第三个 tabbar 入口改为「活动专区」，页面展示竖屏平铺的 Banner 列表（从上到下排列），点击跳转到对应活动页面。

> **渲染方式：** 不使用首页的横屏轮播，改为竖屏平铺（从上到下排列所有 Banner 图片），每条 Banner 可点击跳转。

## 2. 接口信息

| 项目 | 说明 |
|------|------|
| 接口 | `GET /ec-prebff/pages/cms/canada_growth?page=1` |
| GQC 环境 | `https://gqc-ecapi.yamibuy.tech` |
| DEV 环境 | `https://dev-ecapi.yamibuy.tech` |
| 触发条件 | 加拿大站 App/H5 第三个 tabbar 点击时调用 |

### 请求 Headers

| Header | 必填 | 说明 |
|--------|------|------|
| token | 是 | 用户登录 token |
| y_platform | 是 | App: `android` / `ios`；H5: `h5` |
| y_language | 是 | 语言：`zh_CN` / `en_US` / `ja_JP` / `ko_KR` / `zh_TW` / `fr_FR` |
| site_code | 是 | 固定传 `ca`（加拿大站必须传） |
| device_id | 是 | 设备 ID |
| ym_id | 否 | 用户匿名 ID |
| y_version | 否 | App 版本号（H5 不传） |

### 请求 Query 参数

| 参数 | 必填 | 说明 |
|------|------|------|
| page | 是 | 固定传 `1`（活动专区只有 1 个组件，不涉及分页） |

> **重要：** `site_code: ca` 必须传，否则后端无法路由到加拿大页面，接口会返回 null。`page` 必传且 >0，不传会返回 null。

## 3. 请求示例

### App 端
```bash
curl -X GET \
  "https://gqc-ecapi.yamibuy.tech/ec-prebff/pages/cms/canada_growth?page=1" \
  -H "token: {用户token}" \
  -H "y_platform: android" \
  -H "y_language: en_US" \
  -H "y_version: 276" \
  -H "site_code: ca" \
  -H "device_id: {设备ID}" \
  -H "ym_id: {匿名ID}"
```

### H5 端
```bash
curl -X GET \
  "https://gqc-ecapi.yamibuy.tech/ec-prebff/pages/cms/canada_growth?page=1" \
  -H "token: {用户token}" \
  -H "y_platform: h5" \
  -H "y_language: en_US" \
  -H "site_code: ca" \
  -H "device_id: {设备ID}"
```

## 4. 返回数据结构

```json
{
  "messageId": "10000",
  "success": "true",
  "body": {
    "components": [
      {
        "component_key": "cms_component_slide_vertical",
        "component_file": "cms_component_slide_vertical",
        "component_config_id": 517760,
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
| component_key | String | 固定 `cms_component_slide_vertical` |
| component_file | String | 固定 `cms_component_slide_vertical`（前端用此字段匹配渲染组件） |
| properties.title | String | 组件标题（可选展示） |
| dataObjectResult | Array | Banner 列表，按 sort 排序 |

### dataObjectResult[n] — 单条 Banner

| 字段 | 类型 | 说明 |
|------|------|------|
| obj_id | Integer | Banner 唯一 ID |
| aimUrl | String | 跳转链接（H5 使用） |
| appAimUrl | String | App 跳转链接（App 优先使用此字段） |
| imageUrl | String | Banner 图片地址 |
| imageUrl2 | String | 备用图片地址（可用于高分屏） |
| imgScale | Double | 图片高宽比（可用于占位计算） |
| background_color | String | Banner 背景色 |
| start_time | Long | 生效开始时间（Unix 秒） |
| end_time | Long | 生效结束时间（Unix 秒） |
| visibility_settings | Integer | 可见性：0=所有用户，1=新用户，2=老用户 |
| is_sticky | Integer | 是否置顶：0=否，1=是 |
| has_item | Integer | 是否有关联商品：0=否，1=是（本页面始终为 0） |

## 6. 前端实现要求

### App 端

- 加拿大站第三个 tabbar 点击时调用上述接口
- 从返回的 `components[0].dataObjectResult` 取 Banner 列表
- 渲染为**竖屏平铺**（从上到下排列所有 Banner 图片，非轮播）
- 每条 Banner 点击跳转到 `appAimUrl`（优先）或 `aimUrl`
- 图片使用 `imageUrl` 展示
- 可用 `imgScale` 计算图片占位高度（高度 = 宽度 × imgScale）
- Banner 数量不固定（运营配几条展示几条），做好列表滚动

### H5 端

- 通过 `component_file` 字段匹配渲染组件：`cms_component_slide_vertical` → 竖屏平铺组件
- 新建 `cms-component-slide-vertical.vue` 实现竖屏平铺布局
- 数据结构和 App 端完全一致，取 `dataObjectResult` 渲染 Banner 列表
- 每条 Banner 点击跳转到 `aimUrl`
- 后续如有新组件加入该页面，会以新的 `component_file` 返回，H5 用通用组件加载机制自动适配

## 7. 注意事项

- `site_code: ca` header 必传，否则接口返回 null
- 接口和首页用的是同一个 `/pages/cms/{pageKey}`，只是 pageKey 不同
- 当 Banner 列表为空时，展示空态页面
- 支持下拉刷新（重新调接口）
- `component_key` 为 `cms_component_slide_vertical`（非 `cms_component_slide`），区别于首页横向轮播 Banner
- 各环境均已可联调（GQC/DEV）
