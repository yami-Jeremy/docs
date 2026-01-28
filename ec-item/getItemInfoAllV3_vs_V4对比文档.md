# getItemInfoAllV3 vs getItemInfoAllV4 接口对比文档

## 📋 文档信息
- **文档创建时间**: 2026-01-28
- **接口路径**: 
  - V3: `/items/getItemInfoAllV3`
  - V4: `/items/getItemInfoAllV4`
- **接口描述**: 商品详情页接口，根据item_number查询商品信息(读DB)
- **V4改动说明**: 在getItemInfoAllV3基础上增加用户趋势数据

---

## 🔄 入参对比

### 相同参数 (保持不变)

以下参数在两个版本中完全一致：

| 参数名 | 传参方式 | 是否必填 | 默认值 | 说明 |
|--------|---------|---------|--------|------|
| item_number | RequestParam | 是 | - | 商品编号 |
| ip_address | RequestHeader (CF-Connecting-IP) | 否 | "" | IP地址 |
| platform_code | RequestParam | 否 | "B2C" | 平台编码 |
| channel_code | RequestParam | 否 | "Computer" | 渠道编码 |
| banner_page_param | RequestParam | 否 | - | Banner页面参数 |
| termini | RequestParam | 否 | "yamibuy_app" | 终端标识 |
| token | RequestHeader (KEY_TOKEN) | 是 | - | 用户token |
| platform | RequestHeader (y_platform) | 否 | "h5" | 平台类型 |
| y_version | RequestHeader | 否 | - | 版本号 |
| app_name | RequestHeader | 否 | "" | 应用名称 |
| y_language | RequestHeader | 否 | "zh_CN" | 语言 |
| regionID | RequestParam (region_id) | 否 | - | 区域ID |
| recycleKey | RequestParam (recycle_key) | 否 | "" | 回收键 |
| device_id | RequestHeader | 否 | - | 设备ID |
| ym_id | RequestHeader | 否 | - | YM用户ID |
| userAgent | RequestHeader (User-Agent) | 否 | - | 用户代理 |

### ❌ V3独有参数 (V4中移除)

| 参数名 | 传参方式 | 是否必填 | 默认值 | 说明 |
|--------|---------|---------|--------|------|
| need_tabs | RequestParam | 否 | "0" | 是否需要tabs标签 |

**⚠️ 重要提示**: V4接口移除了 `need_tabs` 参数，如果客户端需要tab相关功能，需要确认V4接口返回的数据中是否包含了tabs信息。

### ✅ V4新增参数

| 参数名 | 传参方式 | 是否必填 | 默认值 | 说明 |
|--------|---------|---------|--------|------|
| group_op | RequestParam | 否 | "" | 商品组操作标识，只有商品组操作打tag的新逻辑才需要 |

**💡 说明**: `group_op` 参数是V4新增的，用于支持商品组操作的打标签功能。

---

## 📤 返回值对比

### 返回值类型
两个接口的返回值类型**完全一致**：

```java
BaseResponse<Map<String, Object>>
```

### 返回数据差异

根据接口描述和代码实现分析：

#### V3 返回数据处理逻辑
```java
Map<String, Object> result = itemQueryService.getItemInfoAllV3(reqDTO, recycleKey);
// 英文站 ios重构 h5重构 赠品逻辑适配: 需要去掉mkt返回的结果中携带的sold out字样
itemNBService.filterSoldOut(result);
return BaseResponse.send(result);
```

#### V4 返回数据处理逻辑
```java
Map<String, Object> result = itemQueryService.getItemInfoAllV4(reqDTO, recycleKey);
afterHandle(result, reqDTO);
return BaseResponse.send(result);
```

**主要差异**:
1. **V3**: 调用 `getItemInfoAllV3` 方法 + `filterSoldOut` 过滤sold out字样
2. **V4**: 调用 `getItemInfoAllV4` 方法 + `afterHandle` 统一后处理（包含filterSoldOut + 其他逻辑）
3. **V4新增内容**: 在V3基础上增加了**用户趋势数据**

---

## 📊 迁移影响分析

### 对客户端的影响

#### 1. 必须处理的变化
- **移除参数**: 如果客户端在调用V3时传递了 `need_tabs` 参数，需要在迁移到V4时移除该参数
- **参数验证**: 客户端需要更新接口参数验证逻辑

#### 2. 可选处理的变化
- **新增参数**: 如果客户端需要使用商品组操作的打标签功能，可以传递 `group_op` 参数
- **默认行为**: 如果不传递 `group_op`，默认值为空字符串，不影响现有功能

#### 3. 返回数据变化
- V4返回数据在V3基础上**增加了用户趋势数据**
- 原有V3的返回字段应该都保留，客户端现有的数据解析逻辑理论上**无需修改**
- 客户端可以选择性地使用V4新增的用户趋势数据字段

### 迁移建议

✅ **低风险项**:
- 入参结构基本一致，只需移除 `need_tabs` 参数
- 返回值类型完全相同，数据是扩展而非替换

⚠️ **需要验证的项**:
- 确认 `need_tabs` 参数的功能在V4中如何实现（是否默认返回或通过其他方式获取）
- 测试验证V4返回的数据结构是否完全兼容客户端现有的解析逻辑
- 确认用户趋势数据的字段结构，评估是否需要使用

⚡ **推荐迁移步骤**:
1. 在测试环境中对比V3和V4的实际返回数据结构
2. 移除客户端代码中的 `need_tabs` 参数
3. 在测试环境进行充分测试
4. 灰度发布，监控异常情况
5. 全量替换

---

## 💻 代码示例

### V3 调用示例
```javascript
// JavaScript/TypeScript 示例
const response = await fetch('/items/getItemInfoAllV3', {
  method: 'GET',
  headers: {
    'KEY_TOKEN': userToken,
    'y_platform': 'h5',
    'y_language': 'zh_CN',
    'CF-Connecting-IP': ipAddress,
    // ... 其他headers
  },
  params: {
    item_number: '12345',
    platform_code: 'B2C',
    channel_code: 'Computer',
    need_tabs: 1,  // V4中需要移除这个参数
    // ... 其他params
  }
});
```

### V4 调用示例
```javascript
// JavaScript/TypeScript 示例
const response = await fetch('/items/getItemInfoAllV4', {
  method: 'GET',
  headers: {
    'KEY_TOKEN': userToken,
    'y_platform': 'h5',
    'y_language': 'zh_CN',
    'CF-Connecting-IP': ipAddress,
    // ... 其他headers
  },
  params: {
    item_number: '12345',
    platform_code: 'B2C',
    channel_code: 'Computer',
    // need_tabs 参数已移除
    group_op: '',  // V4新增参数（可选）
    // ... 其他params
  }
});
```


