# StoryPage 接口说明（getStoryPage）

本文档基于后端实现整理，供前端开发同事参考。重点说明 `cms_component_story_seckill` 组件在 `if_show_tab` 不同取值时的返回结构差异。

## 接口信息
- **URL**: `/cms/pages/stories/{page_key}`
- **Method**: `GET`
- **说明**: storyPage 分页接口（页面组件列表 + 页面信息）

## Path 参数
- `page_key`：页面 key（必填）

## Header 参数
- `token`：登录令牌（必填）
- `y_language`：语言（必填，`zh_CN` / `en_US`）
- `y_platform`：平台（必填，`pc` / `h5` / `app` 等，需与后端配置一致）
- `device_id`：设备 ID（可选）
- `ym_id`：用户 ID（可选）

## Query 参数
- `env`：环境标记（可选，预发 `pre` 时会直接走新样式）
- `autoAddComponent`：是否自动追加商品列表组件（可选，默认 `false`）

## 响应结构
`BaseResponse<Map<String, Object>>`

> 这里只展开 `data` 字段的结构（BaseResponse 标准字段以项目统一定义为准）。

```json
{
  "components": [StoryComponentVO],
  "page": StoryPageInfo,
  "user_is_validated": true,
  "email": "user@example.com"
}
```

### StoryComponentVO
- `component_key`：组件类型
- `component_config_id`：组件配置 ID
- `component_file`：组件渲染文件标识（含 S0/S1 版本）
- `properties`：组件属性（CMS 配置字段，`content` 会被移除）
- `content`：组件内容（不同组件结构不同）

### StoryPageInfo（`page`）
- `page_key` / `page_name` / `page_dis` / `share_img`
- `link_url` / `version_flag` / `pageStyle` / `ab_value`
- `is_expired` / `story_mapping`（如有）

---

## 重点：`cms_component_story_seckill` 返回结构

`component_key = "cms_component_story_seckill"` 时，`content` 中包含 `seckill_item` 列表。  
该列表的结构取决于 CMS 配置中的 `if_show_tab`：

### 1) `if_show_tab = false`（普通模式，不分 Tab）

`seckill_item` 为秒杀活动列表，每个活动直接返回 `item_info`：

```json
{
  "component_key": "cms_component_story_seckill",
  "component_config_id": 123,
  "component_file": "cms_component_story_seckill_S1",
  "properties": {
    "show_num": "6",
    "if_show_life_style": "1"
  },
  "content": {
    "...其他配置字段": "...",
    "seckill_item": [
      {
        "ps_id": "670802",
        "status": 1,
        "start_time": 1700000000,
        "end_time": 1700003600,
        "count_down": 1200,
        "item_show_type": 1,
        "item_count": 6,
        "item_info": [ItemVO]
      }
    ]
  }
}
```

**说明**
- `item_info` 为商品卡片列表（`ItemVO`，字段与通用商品卡片一致）
- `item_count` 为活动下实际返回的商品数量
- `show_num`：限制每个活动返回的商品数量

---

### 2) `if_show_tab = true`（Tab 模式，按分类分组）

`seckill_item` 中每个活动返回 `tabs`，不返回 `item_info` / `item_count`：

```json
{
  "component_key": "cms_component_story_seckill",
  "component_config_id": 123,
  "component_file": "cms_component_story_seckill_S1",
  "properties": {
    "show_num": "6",
    "if_show_life_style": "1"
  },
  "content": {
    "...其他配置字段": "...",
    "seckill_item": [
      {
        "ps_id": "670802",
        "status": 1,
        "start_time": 1700000000,
        "end_time": 1700003600,
        "count_down": 1200,
        "item_show_type": 1,
        "item_info": null,
        "item_count": null,
        "tabs": [
          {
            "tab_text": "全部",
            "tab_value": 0,
            "item_count": 6,
            "items": [ItemVO]
          },
          {
            "tab_text": "零食",
            "tab_value": 12,
            "item_count": 4,
            "items": [ItemVO]
          }
        ]
      }
    ]
  }
}
```

**说明**
- `tabs` 来源于 CMS 的 `tab_setting` 配置
- `tab_value = 0` 表示“全部分类”
- `items` 为该分类下的商品卡片列表（`ItemVO`）
- `show_num`：限制每个 tab 返回的商品数量

---

## 其他补充说明
- `content` 中会移除配置项：`sales`、`show_number`、`number`、`tab_setting`、`if_show_tab`
- `show_number`：限制活动列表长度（即 `seckill_item` 的数量）
- `item_info` / `tabs.items` 只在组件序号 `< return_item_index` 时返回（后端配置项，用于控制首屏商品量）
