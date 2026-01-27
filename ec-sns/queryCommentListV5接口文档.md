# queryCommentListV5 接口文档

## 接口概述

**接口名称**: 商品评论列表查询（包含回复，英文站评论列表接口）

**接口路径**: `GET /products/{id}/comments_v5`

**接口描述**: 查询指定商品的评论列表，支持分页、筛选和排序。返回评论列表及每条评论的回复信息。

---

## 请求参数

### 路径参数（Path Parameters）

| 参数名 | 类型 | 必填 | 说明 |
|--------|------|------|------|
| id | Integer | 是 | 商品ID |

### 查询参数（Query Parameters）

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| type | String | 否 | - | 评论类型筛选<br/>- `B`: 仅显示已购买用户的评论<br/>- `S`: 仅显示晒单评论<br/>- 不传或传其他值: 显示所有评论 |
| startColumn | Integer | 是 | - | 页码（从0开始），实际表示第几页 |
| pageSize | Integer | 是 | - | 每页返回的记录数 |
| startIndex | Integer | 否 | - | 查询开始索引。如果提供此参数，将优先使用此值作为查询起始位置，而不是 `startColumn * pageSize` |
| displayReplyCount | Integer | 否 | 3 | 每条评论显示的回复数量 |
| rating | String | 否 | - | 评分筛选，例如: "5" 表示只显示5星评论 |
| order_by | String | 否 | - | 排序方式（具体值需参考后端实现） |
| include_group_comments | Boolean | 否 | false | 是否包含分组评论 |

### 请求头（Request Headers）

| 参数名 | 类型 | 必填 | 默认值 | 说明 |
|--------|------|------|--------|------|
| y_language | String | 否 | zh_CN | 语言设置<br/>- `zh_CN`: 中文<br/>- 其他: 英文等 |
| token | String | 否 | - | 用户认证token，用于获取当前用户ID，影响是否点赞等个性化数据 |

---

## 响应参数

### 响应结构

接口返回 `BaseResponse` 包装的 `PostResponsePage` 对象。

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "recordsTotal": 100,
    "recordsFiltered": 0,
    "draw": null,
    "page": 0,
    "total_page": 10,
    "avg_rating": 4.5,
    "posts_count": 100,
    "posts_count_info": "100+",
    "data": [
      // CommentEN 对象数组
    ]
  }
}
```

### PostResponsePage 字段说明

| 字段名 | 类型 | 说明 |
|--------|------|------|
| recordsTotal | Long | 总记录数（不参与分页，仅供前台显示） |
| recordsFiltered | Long | 分页用记录总数（当前为0） |
| draw | Integer | 请求标识（当前为null） |
| page | Integer | 当前页码（从0开始） |
| total_page | Integer | 总页数 |
| avg_rating | Double | 商品平均评分 |
| posts_count | Integer | 评论总数 |
| posts_count_info | String | 评论总数格式化显示（如 "100+"） |
| data | Array\<CommentEN\> | 评论列表 |

### CommentEN 对象字段说明

| 字段名 | 类型 | 说明 |
|--------|------|------|
| ref_id | Integer | 商品ID |
| ref_item_number | String | 商品编号 |
| ref_name | String | 商品名称 |
| ref_thumb | String | 商品首图URL |
| ref_ename | String | 商品英文名称 |
| ref_img | String | 商品大图URL |
| post_id | Integer | 评论ID |
| post_title | String | 评论标题 |
| rating | Double | 评分（星级） |
| post_type | Integer | 类型（0: 商品评论） |
| root_id | Integer | 根ID，为0则是一级评论 |
| comment_root_id | Integer | 一级回复ID |
| parent_id | Integer | 父级回复ID，为空则是一级回复 |
| last_id | Integer | 最后一个回贴ID |
| last_time | Long | 最后一个回贴时间（时间戳） |
| parent_user | Integer | 父贴创建人ID |
| parent_user_name | String | 父贴创建人名称 |
| parent_user_type | Integer | 父用户类型 |
| parent_user_tags | String | 父用户标签 |
| parent_user_en_tags | String | 父用户英文标签 |
| parent_in_dtm | Long | 父贴创建时间（时间戳） |
| level | Integer | 深度 |
| category_id | Integer | 分类编号 |
| category_name | String | 分类名称 |
| status | Integer | 状态：0-待审核，1-通过审核，2-已屏蔽，3-被举报 |
| summary | String | 概要内容 |
| summary_en | String | 英文翻译概要 |
| content | String | 评论内容 |
| content_en | String | 英文翻译内容 |
| has_translate | Boolean | 是否有翻译 |
| post_images | String | 相关图片（逗号分隔的图片ID） |
| likes_count | Integer | 点赞数 |
| likes_count_info | String | 点赞数格式化显示（如 "1.2K"） |
| reply_count | Integer | 回复数 |
| reply_count_info | String | 回复数格式化显示 |
| read_count | Integer | 浏览数 |
| read_count_info | String | 浏览数格式化显示 |
| report_count | Integer | 被举报数 |
| report_count_info | String | 被举报数格式化显示 |
| share_count | Integer | 分享数 |
| share_count_info | String | 分享数格式化显示 |
| hot_level | Integer | 热度 |
| ip | String | IP地址 |
| source_flag | Integer | 来源标识：0-APP，1-MOBILE，2-DESKTOP |
| in_dtm | Long | 创建时间（时间戳） |
| in_user | Integer | 提交人ID |
| audit_dtm | Long | 审核时间（时间戳） |
| audit_user | Integer | 审核人ID |
| edit_dtm | Long | 编辑时间（时间戳） |
| edit_user | Integer | 编辑人ID |
| language | Integer | 语言：0-中文，1-英文 |
| price | BigDecimal | 平均消费 |
| is_sticky | Integer | 是否置顶 |
| is_recommend | Integer | 是否推荐 |
| is_liked | Boolean | 是否已点赞（需要token） |
| is_bought | Integer | 是否已购买：0-未购买，1-已购买 |
| is_showed | Integer | 是否晒单 |
| audit_user_name | String | 审核人名称 |
| user_name | String | 用户名称（已处理emoji） |
| avatar | String | 用户头像URL |
| vip_name | String | 用户会员等级名称 |
| vip_icon | String | 用户会员等级图标 |
| is_editing | Integer | 是否编辑过：1-已编辑 |
| is_edit | Integer | 可否编辑：0-不可以编辑，1-可以编辑 |
| is_celebrity | Integer | 是否达人 |
| is_servant | Integer | 是否亚米小二 |
| old_status | Integer | 旧状态 |
| user_type | Integer | 客户类型：0-客服，1-米小二，2-二妹，3-商家，4-达人 |
| user_tags | String | 客户标签 |
| user_en_tags | String | 客户英文标签 |
| default_image_type | String | 默认图片类型 |
| replyList | Array\<ReplyEN\> | 回复列表（初始显示数量由displayReplyCount控制） |
| imageList | Array\<PostImage\> | 图片列表 |
| contentList | Array\<PostContent\> | 内容列表 |
| tagList | Array\<PostTag\> | 标签列表 |
| rateList | Array\<PostRating\> | 评分列表 |
| userTagList | Array\<PostTag\> | @好友列表 |
| groupPropertyList | Array\<GroupProperty\> | 被评论商品的商品组属性列表 |
| purchased_item_property | String | 已购买商品属性（格式化后的字符串） |

### ReplyEN 对象字段说明（replyList中的元素）

| 字段名 | 类型 | 说明 |
|--------|------|------|
| post_id | Integer | 等同于root_id（冗余字段） |
| summary | String | 概要 |
| reply_post_id | Integer | 回复ID |
| reply_ref_id | Integer | 商品ID |
| reply_ref_item_number | String | 商品编号 |
| reply_ref_name | String | 商品名称 |
| reply_type | Integer | 回复类型 |
| reply_root_id | Integer | 父级评论ID，为0则是一级评论 |
| reply_comment_root_id | Integer | 一级回复ID |
| reply_parent_id | Integer | 父级回复ID，为0则是一级回复 |
| reply_status | Integer | 状态：0-待审核，1-已审核，2-已作废 |
| reply_last_id | Integer | 最后一个回贴ID |
| reply_last_time | Long | 最后一个回贴时间（时间戳） |
| parent_user | Integer | 父贴创建人ID |
| parent_user_name | String | 父贴创建人名称 |
| parent_user_type | Integer | 父用户类型 |
| parent_user_tags | String | 父用户标签 |
| parent_user_en_tags | String | 父用户英文标签 |
| parent_in_dtm | Long | 父贴创建时间（时间戳） |
| reply_level | Integer | 深度 |
| reply_summary | String | 概要内容 |
| reply_summary_en | String | 概要英文翻译 |
| reply_content | String | 回复内容 |
| reply_content_en | String | 回复内容英文翻译 |
| reply_has_translate | Boolean | 是否有翻译 |
| reply_in_dtm | Integer | 创建时间（时间戳） |
| reply_in_user | String | 创建人ID |
| reply_user_name | String | 回复用户名称 |
| reply_avatar | String | 回复用户头像URL |
| reply_vip_name | String | 回复用户会员等级名称 |
| reply_vip_icon | String | 回复用户会员等级图标 |
| reply_email | String | 回复用户邮箱 |
| reply_audit_dtm | Integer | 审核时间（时间戳） |
| reply_audit_user | String | 审核人ID |
| reply_audit_user_name | String | 审核人名称 |
| reply_likes_count | Integer | 点赞数 |
| reply_likes_count_info | String | 点赞数格式化显示 |
| reply_reply_count | Integer | 回复数 |
| reply_reply_count_info | String | 回复数格式化显示 |
| reply_report_count | Integer | 被举报数 |
| reply_report_count_info | String | 被举报数格式化显示 |
| reply_is_servant | Integer | 是否亚米小二 |
| reply_is_celebrity | Integer | 是否达人 |
| reply_is_liked | Boolean | 是否已点赞（需要token） |
| reply_user_type | Integer | 回复用户类型 |
| reply_user_tags | String | 回复用户标签 |
| reply_user_en_tags | String | 回复用户英文标签 |
| replyV2List | Array\<ReplyV2\> | 二级回复列表 |
| userTagList | Array\<PostTag\> | @好友列表 |

---

## 请求示例

### 示例1：基本查询

```http
GET /products/12345/comments_v5?startColumn=0&pageSize=20
Headers:
  y_language: zh_CN
  token: your_token_here
```

### 示例2：查询已购买用户的评论

```http
GET /products/12345/comments_v5?type=B&startColumn=0&pageSize=20&displayReplyCount=5
Headers:
  y_language: en_US
  token: your_token_here
```

### 示例3：按评分筛选

```http
GET /products/12345/comments_v5?rating=5&startColumn=0&pageSize=20
Headers:
  y_language: zh_CN
```

---

## 响应示例

```json
{
  "code": 200,
  "message": "success",
  "data": {
    "recordsTotal": 150,
    "recordsFiltered": 0,
    "draw": null,
    "page": 0,
    "total_page": 8,
    "avg_rating": 4.6,
    "posts_count": 150,
    "posts_count_info": "150+",
    "data": [
      {
        "post_id": 1001,
        "ref_id": 12345,
        "ref_name": "商品名称",
        "ref_thumb": "https://example.com/thumb.jpg",
        "user_name": "用户名",
        "avatar": "https://example.com/avatar.jpg",
        "rating": 5.0,
        "content": "评论内容",
        "content_en": "Comment content",
        "has_translate": true,
        "likes_count": 10,
        "likes_count_info": "10",
        "reply_count": 5,
        "reply_count_info": "5",
        "read_count": 100,
        "read_count_info": "100",
        "in_dtm": 1640995200000,
        "is_liked": false,
        "is_bought": 1,
        "is_showed": 0,
        "user_type": 0,
        "vip_name": "普通会员",
        "purchased_item_property": "颜色:红色,尺寸:L",
        "replyList": [
          {
            "reply_post_id": 2001,
            "reply_content": "回复内容",
            "reply_user_name": "回复用户名",
            "reply_avatar": "https://example.com/reply_avatar.jpg",
            "reply_in_dtm": 1640995300000,
            "reply_is_liked": false
          }
        ],
        "imageList": [
          {
            "image_id": "img001",
            "image_url": "https://example.com/image1.jpg"
          }
        ]
      }
    ]
  }
}
```

---

## 注意事项

1. **分页逻辑**：
   - `startColumn` 表示页码（从0开始），实际查询位置为 `startColumn * pageSize`
   - 如果提供了 `startIndex`，将优先使用 `startIndex` 作为查询起始位置

2. **排序**：
   - 默认按创建时间倒序（`in_dtm DESC`）排序
   - `order_by` 参数的具体值需参考后端实现

3. **回复数量**：
   - 每条评论默认显示3条回复（可通过 `displayReplyCount` 调整）
   - 回复按创建时间正序（`ASC`）排序

4. **语言处理**：
   - 当 `y_language` 不是 `zh_CN` 时，如果评论有英文翻译，会返回 `content_en` 和 `summary_en`
   - `has_translate` 字段标识是否有翻译

5. **用户个性化数据**：
   - `is_liked`（是否点赞）字段需要提供有效的 `token` 才能正确返回
   - 未提供 `token` 时，该字段可能为 `false` 或 `null`

6. **图片URL处理**：
   - 返回的图片URL已经过处理，可直接使用
   - 商品图片、用户头像等URL都已格式化

7. **数量格式化**：
   - 点赞数、回复数、浏览数等都有对应的 `*_count_info` 字段，用于前端显示格式化后的数量（如 "1.2K"）

8. **评论类型筛选**：
   - `type=B`: 仅显示已购买用户的评论
   - `type=S`: 仅显示晒单评论
   - 不传或传其他值: 显示所有评论

---

## 错误码说明

接口返回标准的 `BaseResponse` 格式，错误码遵循系统统一规范。

常见错误情况：
- 商品ID不存在
- 参数格式错误
- 服务器内部错误

---

## 更新记录

- 2026-01-27: 创建文档
