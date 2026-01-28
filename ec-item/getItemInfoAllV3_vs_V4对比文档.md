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

### 返回数据处理逻辑详细对比

#### 1️⃣ 后处理逻辑差异

**V3接口后处理**:
```java
Map<String, Object> result = itemQueryService.getItemInfoAllV3(reqDTO, recycleKey);
// 仅执行filterSoldOut - 过滤sold out字样
itemNBService.filterSoldOut(result);
return BaseResponse.send(result);
```

**V4接口后处理**:
```java
Map<String, Object> result = itemQueryService.getItemInfoAllV4(reqDTO, recycleKey);
// 执行afterHandle - 包含更多后处理逻辑
afterHandle(result, reqDTO);
return BaseResponse.send(result);
```

**afterHandle 方法的额外处理**:
```java
public void afterHandle(Map<String, Object> result, ItemInfoAllReqDTO reqDTO) {
    // 1. 过滤sold out字样（和V3相同）
    itemNBService.filterSoldOut(result);
    
    // 2. 【新增】iOS重构 - 商品组商品增加当前区域是否可售
    // 仅对非PC平台执行
    if (!ECItemConstant.PLATFORM_PC.equalsIgnoreCase(platform)) {
        itemNBService.filterGroupItemLimit(result, whNumber.get(ECItemConstant.ZIPCODE));
    }
}
```

#### 2️⃣ 核心查询方法差异

| 对比项 | V3实现 | V4实现 |
|--------|--------|--------|
| **商品信息查询** | `getItemInfo()` | `getItemInfoMain()` |
| **预售商品处理** | ❌ 无 | ✅ `handlePreSaleProduct(itemInfo, true)` |
| **商品扩展信息** | ❌ 无单独构造 | ✅ `constructItemExtend(itemInfo, tk, reqDTO)` |
| **多线程查询方法** | `multithreadGetData()` (4个线程) | `asyncFullMoreItemInfo()` (7个批处理任务) |

#### 3️⃣ 返回字段差异对比表

| 字段名 | V3 | V4 | 说明 |
|--------|----|----|------|
| **itemInfo** | ✅ | ✅ | 商品基本信息（V4增加了扩展字段处理） |
| **sellerInfo** | ✅ | ✅ | 商家信息 |
| **brandInfo** | ✅ | ✅ | 品牌信息 |
| **gift_list / giftActivityList** | ✅ | ✅ | 赠品活动列表 |
| **itemDescList** | ✅ | ✅ | 商品属性列表 |
| **essayList** | ✅ | ✅ (PC端) | 专辑列表（V4在非PC端改为合并到result） |
| **same_day_delivery** | ✅ | ✅ | 一日达信息 |
| **group** | ✅ | ✅ | 商品组信息 |
| **config** | ✅ | ✅ | 配置信息 |
| **currency** | ✅ | ✅ | 货币符号 |
| **vipTipInfo** | ✅ | ✅ | 会员提示信息 |
| **recommendTabList** | ✅ | ✅ | 推荐Tab列表 |
| **googleSeo** | ✅ (PC端) | ✅ (PC端) | Google SEO信息 |
| **collectionPage** | ✅ (PC端) | ✅ (PC端) | Collection关联商品链接 |
| **paypal** | ✅ | ❌ | PayPal信息（V4移除） |
| **psCode** | ❌ | ✅ | 促销优惠券码 |
| **couponCodeDesc** | ❌ | ✅ | 优惠券描述 |
| **nutritionalFacts** | ❌ | ✅ | 营养成分图片列表 |
| **tabList** | ❌ | ✅ | APP前三个Tab展示顺序 |
| **itemRelateUserData** | ❌ | ✅ | **【核心新增】用户趋势数据** |
| **badgeList** | ❌ | ✅ | 商品徽章列表 |
| **itemTags** | ❌ | ✅ | 商品标签 |
| **purchaseProtection** | ❌ | ✅ | 购物保障信息（仅non用户） |
| **topImage** | ❌ | ✅ | 顶部图片（仅non用户） |
| **topImageBackgroundColor** | ❌ | ✅ | 顶部图片背景色（仅non用户） |
| **userType** | ❌ | ✅ | 用户类型（U1/U2等） |

#### 4️⃣ 核心处理差异详解

##### 📌 商品组区域限制处理（V4新增）

**V3**: 无此处理

**V4**: 对于非PC平台，会检查商品组中的商品是否在当前区域可售
```java
// filterGroupItemLimit 方法逻辑
- 遍历商品组中的所有商品
- 根据zipcode检查每个商品的区域限制
- 设置 is_limit_area 字段标识是否限制区域
- 设置 is_instock 和 status 字段
```

##### 📌 促销优惠券处理（V4新增）

**V3**: 无此处理

**V4**: 单独处理促销优惠券信息
```java
if (couponInfo为空 || couponCode为空) {
    psCode = null
    couponCodeDesc = 促销语
} else {
    psCode = 优惠券码
    couponCodeDesc = 促销语或优惠券文案
}
```

##### 📌 营养成分图片处理（V4新增）

**V3**: 无此处理

**V4**: 根据配置决定是否返回营养成分图片
```java
if (配置开启显示营养成分) {
    将图片URL添加前缀后缀：NF_IMAGE_PREFIX + item + NF_IMAGE_SUFFIX
    返回 nutritionalFacts 字段
}
```

##### 📌 Tab列表处理（V4新增）

**V3**: 无此处理

**V4**: 处理APP端前三个Tab的展示顺序
```java
result.put(TAB_LIST, handleShowTabList(
    APP_PDP_TAB_LIST, 
    商品详情规格, 
    商品概览, 
    营养成分图片列表, 
    是否显示营养成分
));
```

##### 📌 用户趋势数据处理（V4核心新增）

**V3**: 无此处理

**V4**: 
```java
Object itemRelateUserData = pdpItemService.getItemRelateUserData(
    goodsId, 
    itemNumber, 
    purchaseCount,      // 购买次数
    purchaseAvatorList  // 购买用户头像列表
);
result.put(ITEM_RELATE_USER_DATA, itemRelateUserData);
```

这是V4接口最重要的新增功能，用于展示商品的用户趋势数据（如多少人购买、用户头像等）。

##### 📌 字段清理（V4新增）

**V3**: 无此处理

**V4**: 调用 `clearNoUseField(itemInfo)` 清除无用字段，减少返回数据量

---

## 📊 迁移影响分析

### 对客户端的影响

#### 1. 必须处理的变化

##### 入参调整
- ❌ **移除参数**: `need_tabs` 参数需要移除
- ✅ **替代方案**: V4中 `tabList` 字段默认返回，无需参数控制

##### 返回字段名称变化（需要适配）
| V3字段名 | V4字段名 | 影响 |
|---------|---------|------|
| `itemDescList` | `itemDescList` | ✅ 不变 |
| `vipTipInfo` | `vipTipInfo` | ✅ 不变 |
| `essayList` | `essayList` (PC) / 合并到result (非PC) | ⚠️ **非PC端需要适配** |

#### 2. V4新增字段（可选使用）

##### 核心功能增强
- ✅ **itemRelateUserData**: 用户趋势数据（购买人数、用户头像等）
- ✅ **tabList**: APP前三个Tab展示顺序（替代need_tabs）
- ✅ **nutritionalFacts**: 营养成分图片列表
- ✅ **badgeList**: 商品徽章列表
- ✅ **itemTags**: 商品标签

##### 优惠券相关
- ✅ **psCode**: 促销优惠券码
- ✅ **couponCodeDesc**: 优惠券描述（如果没有优惠券则显示促销语）

##### 用户体验优化（仅non用户）
- ✅ **purchaseProtection**: 购物保障信息
- ✅ **topImage**: 顶部图片
- ✅ **topImageBackgroundColor**: 顶部图片背景色
- ✅ **userType**: 用户类型分组

#### 3. V4移除字段（需要注意）

- ❌ **paypal**: PayPal支付信息（V4中移除）
  - **影响**: 如果客户端使用了此字段，需要确认替代方案

#### 4. 数据处理逻辑变化（可能影响展示）

##### 商品组区域限制（非PC端）
V4在非PC平台（iOS、Android等）会自动检查商品组中的商品是否在当前区域可售，返回数据中会包含：
- `is_limit_area`: 是否限制区域
- `is_instock`: 是否有库存  
- `status`: 商品状态（A/D）

**影响**: 客户端可以利用这些字段更准确地展示商品可售状态

##### 促销信息展示
V4优化了促销信息的展示逻辑：
- 有优惠券时，优先显示优惠券码和描述
- 没有优惠券时，显示促销语
- 促销语会和大促样式联动判断是否展示

**影响**: 客户端需要使用 `couponCodeDesc` 字段（V4新增）而非直接使用 `promotion_desc`

##### 预售商品处理
V4新增了预售商品（年货节）的专门处理逻辑

**影响**: 预售商品的展示逻辑可能有优化

### 兼容性评估

#### ✅ 高兼容性（无需改动）
- `itemInfo` 主体商品信息结构保持兼容
- `sellerInfo` 商家信息保持兼容
- `brandInfo` 品牌信息保持兼容
- `gift_list` 赠品列表保持兼容
- `same_day_delivery` 一日达信息保持兼容
- `group` 商品组信息保持兼容（但非PC端会有额外的区域限制字段）

#### ⚠️ 需要适配（建议改动）
1. **移除 `need_tabs` 参数**，改用返回的 `tabList` 字段
2. **非PC端的 `essayList` 处理**: V4中专辑数据会合并到result主体
3. **优惠券信息**: 使用 `couponCodeDesc` 替代直接读取 `promotion_desc`
4. **PayPal信息**: 如果使用了 `paypal` 字段，需要确认替代方案

#### 🎁 功能增强（可选升级）
1. **用户趋势数据**: 使用 `itemRelateUserData` 展示购买人数等社交证明
2. **营养成分**: 使用 `nutritionalFacts` 展示营养成分信息
3. **商品徽章**: 使用 `badgeList` 展示商品特殊标签
4. **购物保障**: non用户可展示 `purchaseProtection` 信息

### 迁移建议

#### ⚡ 推荐迁移步骤

##### 阶段一：代码准备（1-2天）
1. 移除客户端代码中的 `need_tabs` 参数
2. 适配非PC端的 `essayList` 数据读取方式
3. 如果使用了 `paypal` 字段，确认替代方案
4. 添加对V4新增字段的类型定义（可选）

##### 阶段二：测试验证（3-5天）
1. 在测试环境对比V3和V4的实际返回数据
2. 重点测试以下场景：
   - 普通商品、商品组商品
   - 有促销/无促销商品
   - 有优惠券/无优惠券商品  
   - 不同区域的商品可售状态（非PC端）
   - PC端和移动端的数据差异
   - 预售商品（年货节）
3. 性能测试：对比V3和V4的响应时间

##### 阶段三：灰度发布（1周）
1. 小流量灰度（5%）
2. 监控关键指标：
   - 接口成功率
   - 接口响应时间
   - 商品详情页PV/UV
   - 加购率、下单率等业务指标
3. 收集异常日志和用户反馈

##### 阶段四：全量替换
1. 逐步扩大灰度比例（5% → 20% → 50% → 100%）
2. 持续监控业务指标
3. 准备回滚预案

#### 🔍 关键验证点

| 验证项 | 说明 | 优先级 |
|--------|------|--------|
| need_tabs功能 | 确认tabList能否完全替代need_tabs功能 | 🔴 高 |
| 非PC端essayList | 确认数据结构和展示逻辑 | 🔴 高 |
| PayPal支付 | 如果使用了paypal字段，确认是否有影响 | 🔴 高 |
| 商品组区域限制 | 非PC端商品组的展示逻辑 | 🟡 中 |
| 优惠券展示 | 促销信息和优惠券的联动展示 | 🟡 中 |
| 用户趋势数据 | 新增字段的展示效果 | 🟢 低 |
| 性能对比 | V4是否有性能提升或下降 | 🟡 中 |

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

---

## 📝 总结

### 关键变化点

#### 入参层面
1. ❌ **移除**: `need_tabs` 参数
2. ✅ **新增**: `group_op` 参数（商品组操作标识）
3. ✅ **保持**: 其他16个参数完全一致

#### 返回值层面
1. **类型**: 完全一致（`BaseResponse<Map<String, Object>>`）
2. **新增字段**（10+个）:
   - `itemRelateUserData` - 用户趋势数据（核心）
   - `tabList` - Tab展示顺序
   - `nutritionalFacts` - 营养成分
   - `badgeList` - 商品徽章
   - `itemTags` - 商品标签
   - `psCode` - 优惠券码
   - `couponCodeDesc` - 优惠券描述
   - `purchaseProtection` - 购物保障
   - `topImage/topImageBackgroundColor` - 顶部图片
   - `userType` - 用户类型
3. **移除字段**（1个）:
   - `paypal` - PayPal信息
4. **字段变化**:
   - `essayList`: 非PC端改为合并到result主体

#### 处理逻辑层面
1. **后处理**: V4的 `afterHandle` 新增非PC端商品组区域限制检查
2. **商品查询**: V4使用 `getItemInfoMain` 替代 `getItemInfo`
3. **预售处理**: V4新增预售商品专门处理
4. **扩展信息**: V4新增 `constructItemExtend` 构造扩展信息
5. **多线程**: V4改用 `asyncFullMoreItemInfo`（7个批处理任务 vs V3的4个线程）
6. **字段清理**: V4新增 `clearNoUseField` 减少返回数据量

### 核心优化亮点

#### 🚀 性能优化
- 多线程查询从4个线程优化为7个批处理任务
- 新增字段清理机制，减少无用数据传输
- 使用 `BatchTaskUtils` 进行任务批处理管理

#### 🎯 功能增强
- **用户趋势数据**: 展示购买人数、用户头像等社交证明
- **商品组优化**: 非PC端自动检查区域限制和可售状态
- **优惠券联动**: 促销信息和优惠券智能展示
- **营养成分**: 支持营养成分图片展示
- **预售支持**: 专门的预售商品处理逻辑

#### 📱 平台差异化
V4更注重平台差异化处理：
- **PC端**: 保持essayList独立字段，返回SEO和Collection信息
- **非PC端**: essayList合并到result，新增商品组区域限制检查
- **non用户**: 额外返回购物保障、推荐Tab、顶部图片等

### 兼容性评估

| 评估项 | 等级 | 说明 |
|--------|------|------|
| **向后兼容性** | ⚠️ 部分兼容 | 移除need_tabs和paypal，需要适配 |
| **迁移难度** | 🟡 中等 | 需要处理参数和部分字段变化 |
| **风险等级** | 🟢 低 | 核心字段保持兼容，新增字段为扩展 |
| **测试工作量** | 🟡 中等 | 需要覆盖多场景、多平台测试 |
| **回滚难度** | 🟢 低 | V3接口保留，可随时回滚 |

### 推荐迁移策略

🎯 **适合迁移的场景**:
- 需要展示用户趋势数据（社交证明）
- 需要更精确的商品组区域限制判断
- 需要优化的促销和优惠券展示
- 追求更好的性能表现

⚠️ **谨慎迁移的场景**:
- 严重依赖 `paypal` 字段的页面
- 对 `need_tabs` 有特殊处理逻辑的页面
- 近期有重大活动，风险窗口期较短

---

## 📞 联系方式

如有疑问，请联系后端团队确认：
- need_tabs 功能在V4中的实现方式
- 用户趋势数据的具体字段结构和用途
- 是否有其他隐含的行为变化

---

**文档维护**: 如发现接口有进一步变化，请及时更新本文档
