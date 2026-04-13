# 毛孩子邻里 - 宠主端小程序 开发文档

## 1. 项目概述

### 1.1 基本信息

| 字段 | 内容 |
|------|------|
| **应用名称** | 毛孩子邻里（宠主端） |
| **小程序 AppID** | `wx_xxxxxxxxxxxx`（待申请） |
| **页面类型** | 微信小程序（原生开发或 uni-app） |
| **定位** | 邻里宠物服务平台 - 宠主端 |
| **目标用户** | 有宠物照顾需求的社区居民（宠主） |
| **核心功能** | 发布服务需求、搜索选择帮手、订单管理、支付结算 |
| **对接系统** | 帮手端小程序、运营后台管理系统 |

### 1.2 账号与登录

- **登录方式**：微信一键登录（获取 `uni.getUserProfile` 用户信息）
- **账号体系**：手机号 + 微信 OpenID 双重绑定
- **身份角色**：默认为"宠主"，同一账号可申请成为"帮手"（跳转到帮手端）
- **用户信息**：昵称、头像、联系电话、所属小区、宠主认证状态

### 1.3 接入外部服务

- 地图服务：高德地图/腾讯地图微信小程序 SDK（选帮手时展示距离）
- 微信支付：订单支付、优惠券核销
- 订阅消息：订单状态变更推送、帮手接单通知
- 微信分享：生成海报分享到微信群/朋友圈

---

## 2. UI/UX 规范

### 2.1 设备适配

- 设计尺寸：375 × 812 px（iPhone X / iPhone 14 基准）
- 安全区域：顶部 44px（状态栏）+ 底部 34px（Home Indicator）
- 支持分辨率：iPhone 8 及以上（750 × 1334 逻辑像素起）
- **tabBar 固定高度**：84px（含底部安全区）

### 2.2 颜色系统

```css
/* 主色调 - 温暖自然绿 */
--primary:        #7BA05B;   /* 主绿色 */
--primary-light:  #9BC27A;   /* 浅绿 */
--primary-dark:   #5C8040;   /* 深绿 */
--primary-bg:     rgba(123, 160, 91, 0.08); /* 绿色背景 */

/* 辅助色 */
--accent:         #F4A261;   /* 橙棕强调色 */
--accent-light:   #F7C59F;
--accent-bg:      rgba(244, 162, 97, 0.1);

/* 状态色 */
--danger:         #E57373;   /* 危险/紧急 */
--danger-bg:      rgba(229, 115, 115, 0.1);
--warning:        #FFB74D;   /* 警告 */
--warning-bg:     rgba(255, 183, 77, 0.1);
--success:        #81C784;   /* 成功 */
--success-bg:     rgba(129, 199, 132, 0.1);
--info:           #64B5F6;   /* 信息 */
--info-bg:        rgba(100, 181, 246, 0.1);
--purple:         #BA68C8;
--purple-bg:      rgba(186, 104, 200, 0.1);

/* 背景色 */
--bg:             #FDF8F3;   /* 页面背景 - 暖白 */
--bg-card:        #FFFFFF;   /* 卡片背景 */
--bg-secondary:   #F5EDE4;  /* 次级背景 - 淡米色 */

/* 文字色 */
--text:           #4A3728;   /* 主文字 - 深棕 */
--text-light:     #8B7355;   /* 次级文字 */
--text-muted:     #B8A890;   /* 辅助文字 */
--border:         #E8DFD4;   /* 边框色 */

/* 阴影 */
--shadow:         rgba(74, 55, 40, 0.08);
--shadow-lg:      rgba(74, 55, 40, 0.15);

/* 圆角 */
--radius:         20px;   /* 大圆角 - 卡片、弹窗 */
--radius-sm:       12px;   /* 中圆角 - 按钮、输入框 */
--radius-xs:       8px;    /* 小圆角 - 标签、小元素 */
```

### 2.3 字体规范

```css
/* 主字体 - 中文 */
font-family: 'Noto Sans SC', -apple-system, BlinkMacSystemFont, sans-serif;

/* 数字/英文辅助字体 */
font-family: 'Quicksand', -apple-system, sans-serif;
```

| 用途 |字号|字重|行高|
|------|-----|-----|-----|
|页面大标题|24px|700|1.2|
|卡片标题|16px|600|1.3|
|正文|14px|400|1.6|
|辅助说明|12px|400|1.5|
|标签/角标|11px|500|1.4|
|数字金额|20-36px|700|1.1|

### 2.4 间距系统

- 页面边距：`16px`（部分 `20px`）
- 卡片内边距：`18px`
- 元素间距（垂直）：`8px` / `12px` / `16px` / `20px`
- 卡片间距：`14px`
- 列表项间距：底部 `1px` 边框分隔

### 2.5 动效规范

| 动效类型|时长|easing|用途|
|---------|-----|-----|-----|
|页面切换|350ms|ease|tab 切换|
|弹窗滑入|350ms|ease-out|底部弹窗|
|淡入|300ms|ease|元素显示|
|按钮点击|150ms|ease|scale(0.97)|
|Toast|250ms|ease|出现/消失|

- 列表项：使用 `animation-delay` 依次延迟 50ms 出现
- 新订单卡片：顶部滑入动画
- Tab 切换：淡出 + 轻微位移

### 2.6 图标规范

- 图标风格：Emoji + 线性 SVG 混用
- 常用 Emoji：🐕 🐱 🎾 🧹 ⚡ 📍 ⭐ ✅ 🏅
- SVG 图标：微信原生 `icon` 组件或字体图标库

---

## 3. 页面结构

### 3.1 页面概览

```
├── tabBar（底部导航，5个tab）
│   ├── 首页（index 0）
│   ├── 发现（index 1）
│   ├── 发布（index 2）
│   ├── 订单（index 3）
│   └── 我的（index 4）
│
├── 弹窗/Modal 覆盖层
│   ├── 帮手详情弹窗
│   ├── 确认发布弹窗
│   ├── 订单完成弹窗
│   ├── 紧急求助弹窗
│   ├── 添加宠物弹窗
│   ├── 我的宠物弹窗
│   ├── 订单详情弹窗（全屏）
│   ├── 我的地址弹窗
│   ├── 消息通知弹窗
│   ├── 联系客服弹窗
│   ├── 帮助与反馈弹窗
│   ├── 关于我们弹窗
│   ├── 服务时间设置弹窗
│   └── 我的优惠券弹窗
│
└── 非 tabBar 页面（通过 wx.navigateTo）
    └── （暂无独立子页面）
```

### 3.2 页面路由

| 路由路径|页面名称|描述|
|--------|--------|-----|
| `/pages/index/index` | 首页 | 欢迎语、快捷入口、进行中订单、附近帮手 |
| `/pages/discover/discover` | 发现 | 搜索帮手、筛选、帮手列表 |
| `/pages/publish/publish` | 发布需求 | 创建服务需求单 |
| `/pages/orders/orders` | 我的订单 | 订单列表管理 |
| `/pages/my/my` | 我的 | 个人中心、设置 |

---

## 4. 功能模块详解

### 4.1 首页（首页 tab）

#### 4.1.1 页面结构

```
首页
├── 顶部状态栏区域（StatusBar 44px）
├── 首页头部
│   ├── 欢迎语（时间问候 + 用户名 + 宠物emoji）
│   └── 消息通知按钮（带红点角标）
├── 快捷操作区
│   ├── 遛狗快捷入口
│   ├── 喂猫快捷入口
│   └── 急事入口（紧急求助）
├── 进行中订单区
│   ├── 区块标题（"进行中的订单" + 查看全部链接）
│   └── 订单卡片列表（水平滚动，最多3条）
└── 附近靠谱帮手区
    ├── 区块标题（"附近靠谱帮手" + 更多链接）
    └── 帮手横向滚动卡片（显示头像、姓名、评分、距离）
```

#### 4.1.2 交互逻辑

| 交互|行为|
|-----|-----|
|点击消息通知图标|跳转消息中心，带红点数量角标|
|点击遛狗/喂猫/急事入口|跳转发布页，自动选中对应服务类型|
|点击进行中订单卡片|打开订单详情弹窗（全屏 modal）|
|点击查看全部|跳转订单 tab|
|点击帮手卡片|打开帮手详情弹窗|
|点击更多（帮手区）|跳转发现 tab|

#### 4.1.3 数据来源

- 欢迎语时间问候：本地时间判断（早/午/晚）
- 进行中订单：`GET /api/orders?userId=&status=ongoing,matched`
- 附近帮手：`GET /api/helpers?community=&latitude=&longitude=&limit=4`

---

### 4.2 发现页（发现 tab）

#### 4.2.1 页面结构

```
发现页
├── 页面标题（"发现帮手" + 副标题"找到你信任的邻里宠伴"）
├── 搜索栏
│   └── 搜索输入框（placeholder: "搜索帮手姓名或关键词"）
├── 筛选标签栏
│   ├── 综合
│   ├── 离我最近
│   ├── 评分最高
│   └── 可接急单
└── 帮手列表
    └── 帮手卡片（循环渲染）
        ├── 头像（圆形，渐变背景）
        ├── 姓名 + 认证标签 + 明星帮手标签
        ├── 服务类型 · 所属小区 · 从业经验
        ├── 评分 + 评价数 + 距离
        └── 服务标签（遛狗/喂猫/陪玩等）
```

#### 4.2.2 帮手卡片交互

- **点击卡片任意区域**：打开 `modal-helper`（帮手详情弹窗）
- **搜索**：实时过滤（前端本地过滤，`onInput` 事件）
- **筛选切换**：
  - 综合：默认排序
  - 离我最近：按 `distance` 字段升序
  - 评分最高：按 `rating` 字段降序
  - 可接急单：过滤 `canUrgent === true`

#### 4.2.3 API 接口

```
GET /api/helpers
  Query: search?, filter?(all|nearby|rating|urgent),
          community?, latitude?, longitude?, page?, pageSize?
  Response: { list: Helper[], total: number }

GET /api/helpers/:id
  Response: HelperDetail
```

---

### 4.3 发布需求页（发布 tab）

#### 4.3.1 页面结构

```
发布需求页
├── 页面标题（"发布需求"）
├── 服务类型选择
│   ├── 遛狗（🐕）
│   ├── 喂猫（🐱）
│   ├── 陪玩（🎾）
│   └── 清洁（🧹）
├── 选择宠物
│   ├── 宠物卡片列表（圆形图标 + 名称 + 品种）
│   └── + 添加宠物入口
├── 选择日期
│   ├── 日期标签（今天/明天/周末）
│   └── 时段网格（上午/下午 4个时段可选）
├── 服务地址
│   ├── 小区/地址输入框（默认填充用户常用地址）
│   └── 备注输入框
├── 设置预算
│   ├── 期望价格输入框
│   └── 快捷预算标签（¥30-50 / ¥50-80 / ¥80-100 / ¥100以上）
└── 底部固定区域
    ├── 价格预估显示
    └── 立即发布按钮
```

#### 4.3.2 状态管理

| 状态变量|类型|默认值|
|---------|-----|------|
|`selectedServiceType`|string|'walk'|
|`selectedPetId`|number|1|
|`selectedDate`|string|'today'|
|`selectedTimeSlot`|string|'14:00-16:00'|
|`serviceAddress`|string|（用户地址）|
|`serviceNote`|string|''|
|`serviceBudget`|number|60|

#### 4.3.3 交互逻辑

| 交互|行为|
|-----|-----|
|选择服务类型|高亮选中项，更新价格预估范围|
|选择宠物|高亮选中项，更新宠物 ID|
|选择日期/时段|高亮选中项|
|点击宠物卡片|高亮选中状态|
|点击添加宠物|打开 `modal-add-pet` 弹窗|
|输入地址|同步更新状态|
|选择预算快捷标签|填入对应价格到输入框|
|点击立即发布|打开 `modal-confirm` 确认弹窗|
|确认发布|提交订单 → 跳转订单页 → 显示进行中订单|

#### 4.3.4 价格估算规则

- 根据 `selectedServiceType` 从 `services` 表获取 `priceRange`
- 用户预算输入优先，价格范围仅供参考

#### 4.3.5 API 接口

```
POST /api/orders
  Body: {
    userId: string,
    serviceType: string,   // walk|feed|play|clean
    petId: number,
    date: string,         // today|tomorrow|weekend
    timeSlot: string,     // "08:00-10:00"
    address: string,
    note: string,
    budget: number,
    couponId?: string
  }
  Response: { orderId: string, status: 'pending' }
```

---

### 4.4 订单页（订单 tab）

#### 4.4.1 页面结构

```
我的订单页
├── 页面标题
├── 订单状态筛选栏
│   ├── 全部
│   ├── 进行中
│   └── 已完成
└── 订单列表
    └── 订单卡片
        ├── 服务类型图标 + 订单状态标签
        ├── 服务信息（宠物名、日期时间、地址）
        ├── 帮手信息（头像 + 姓名，完结订单显示）
        ├── 价格
        ├── 进行中订单操作区（联系帮手/确认完成）
        └── 已完成订单操作区（查看评价/再来一单）
```

#### 4.4.2 订单卡片状态

| 订单状态|显示内容|可执行操作|
|--------|--------|---------|
|`pending`|等待帮手接单|取消订单|
|`matched`|已匹配帮手|联系帮手/取消订单|
|`ongoing`|服务进行中|联系帮手/确认完成|
|`completed`|已完成|查看评价/再发布|
|`cancelled`|已取消|——|

#### 4.4.3 API 接口

```
GET /api/orders
  Query: userId, status?, page?, pageSize?
  Response: { list: Order[], total: number }

GET /api/orders/:id
  Response: OrderDetail

PUT /api/orders/:id/status
  Body: { status: 'cancelled' | 'completed' }
  Response: { success: boolean }
```

---

### 4.5 我的页（我的 tab）

#### 4.5.1 页面结构

```
我的页
├── 个人资料头部
│   ├── 头像（圆形，可编辑）
│   ├── 昵称
│   ├── 认证状态标签（"已认证宠主"）
│   └── 数据统计（发布订单数 / 我的宠物数 / 积分）
├── 功能菜单（分组）
│   ├── 我的订单
│   ├── 我的钱包（余额展示）
│   ├── 我的宠物（宠物数量角标）
│   ├── 我的地址
│   └── 我的优惠券（可用数量角标）
│   ├── 消息通知（未读数量角标）
│   └── 联系客服
│   ├── 帮助与反馈
│   └── 关于我们
└── （无底部切换帮手入口 → 帮手功能独立为帮手端App）
```

#### 4.5.2 我的钱包

- 显示可用余额（单位：元，精确到小数点后2位）
- 点击提示"功能开发中"（实际需要关联微信支付分/余额体系）

#### 4.5.3 API 接口

```
GET /api/users/:id
  Response: { userId, nickname, avatar, phone, community, petCount, orderCount, points }

GET /api/users/:id/pets
  Response: Pet[]

GET /api/users/:id/addresses
  Response: Address[]

GET /api/users/:id/coupons
  Query: status? // available|used|expired
  Response: Coupon[]

GET /api/users/:id/messages
  Query: type? // order|system|interaction
  Response: Message[]
```

---

## 5. 弹窗（Modal）详解

### 5.1 弹窗列表

| Modal ID|名称|类型|触发来源|
|---------|----|----|-------|
|`modal-helper`|帮手详情|底部滑出弹窗|发现页/首页帮手卡片|
|`modal-confirm`|确认发布|底部滑出弹窗|发布页立即发布按钮|
|`modal-complete`|订单完成|底部滑出弹窗|订单详情-确认完成按钮|
|`modal-emergency`|紧急求助|底部滑出弹窗|首页急事入口|
|`modal-add-pet`|添加宠物|底部滑出弹窗|发布页/我的-添加宠物|
|`modal-pets`|我的宠物|底部滑出弹窗|我的-我的宠物|
|`modal-order-detail`|订单详情|**全屏弹窗**|首页订单卡片/订单列表|
|`modal-address`|我的地址|底部滑出弹窗|我的-我的地址|
|`modal-messages`|消息通知|底部滑出弹窗|我的-消息通知|
|`modal-support`|联系客服|底部滑出弹窗|我的-联系客服|
|`modal-help`|帮助与反馈|底部滑出弹窗|我的-帮助与反馈|
|`modal-about`|关于我们|底部滑出弹窗|我的-关于我们|
|`modal-servicetime`|服务时间设置|底部滑出弹窗|（当前未使用，预留）|
|`modal-coupons`|我的优惠券|底部滑出弹窗|我的-我的优惠券|

### 5.2 modal-order-detail（全屏订单详情）

```
订单详情（全屏弹窗）
├── 订单状态标签（待接单/服务中/已完成等，带颜色）
├── 帮手信息卡片（头像+姓名+认证+评分）
│   ├── 📞 联系帮手按钮
│   └── 💬 聊天按钮
├── 服务进度时间轴
│   ├── 需求已发布（完成）
│   ├── 帮手已接单（完成/待进行）
│   └── 服务完成（待进行/完成）
├── 服务信息卡片
│   ├── 服务类型、宠物、时间、地址
│   └── 订单金额
├── 服务凭证（已完成订单显示）
│   └── 服务照片缩略图网格（点击查看大图）
└── 操作按钮区（根据状态显示不同按钮）
    ├── 进行中：联系帮手 + 确认完成
    ├── 已完成（未评价）：立即评价
    └── 已完成（已评价）：评价星级展示
```

**凭证照片（proofPhotos）**

- 帮手提交服务凭证后，订单详情展示
- 点击缩略图调用微信 `wx.previewImage` 全屏预览
- 最多显示 9 张，缩略图网格 3×3

### 5.3 modal-complete（订单完成/评价）

```
订单完成弹窗
├── 评分星级（5星，点击切换）
│   └── 评分标签（5星=超棒/4星=满意/3星=一般...）
├── 文字评价输入框（placeholder: "分享你的服务体验"）
├── 服务凭证上传区
│   ├── 图片/视频模式切换标签
│   ├── 上传区域（点击唤起微信上传组件）
│   ├── 已上传图片预览网格
│   └── 提示文案（最多9张）
├── 打赏选项
│   ├── 不打赏
│   ├── +¥10
│   ├── +¥20
│   └── +¥50
└── 提交评价按钮
```

**前端限制：**

- 星级：必选（1-5），默认0未选
- 图片/视频：二选一模式，通过切换 Tab 实现
- 上传组件：使用微信 `wx.chooseMedia` 或 `wx.chooseImage`
- 打赏：可选，不打赏默认 0

### 5.4 modal-emergency（紧急求助）

```
紧急求助弹窗
├── 警示图标 + 紧急提示文案
├── 宠物选择（下拉/列表）
├── 问题描述（文本框，必填）
├── 联系方式（预填用户手机号，可修改）
├── 预计上门时间
└── 提交求助按钮
```

- 提交后触发平台紧急求助流程：广播给所有本小区已认证帮手
- 同时通知后台运营人员

### 5.5 modal-coupons（我的优惠券）

```
优惠券弹窗
├── 页面标题 + 关闭按钮
├── 优惠券分类 Tab
│   ├── 可用（数量）
│   ├── 已使用（数量）
│   └── 已过期（数量）
├── 优惠券卡片列表
│   ├── 优惠券样式（渐变背景，左侧金额，右侧信息）
│   │   ├── 金额（¥20）
│   │   ├── 使用条件（满100可用）
│   │   ├── 名称
│   │   ├── 适用范围
│   │   └── 有效期
│   └── 立即使用按钮（可用券显示）
├── 使用说明区块
└── 关闭按钮
```

**优惠券卡片样式规则：**

| 券类型|背景渐变|
|------|--------|
|满减券（通用）|红橙渐变 `#FF6B6B → #FF8E53`|
|邻里专属|绿色渐变 `#7BA05B → #9BC27A`|
|限时折扣|橙色渐变 `#F4A261 → #F7C59F`|

### 5.6 modal-address（我的地址）

```
地址管理弹窗
├── 页面标题
├── 已有地址列表
│   └── 地址卡片
│       ├── 地址标签（"家" / "公司"）
│       ├── 详细地址
│       └── 编辑 / 删除操作
├── 添加新地址按钮
└── 添加地址表单（点击展开）
    ├── 地址标签输入
    ├── 小区名称
    ├── 楼栋单元
    └── 保存按钮
```

---

## 6. 数据模型

### 6.1 用户（User）

```typescript
interface User {
  id: number;
  openid: string;           // 微信 OpenID
  nickname: string;
  avatar: string;            // 头像 URL
  phone: string;
  community: string;         // 所属小区
  communityId: number;
  role: 'owner' | 'helper' | 'both';
  isOwnerVerified: boolean; // 宠主认证
  isHelperVerified: boolean; // 帮手认证（若已申请）
  helperId?: number;        // 若已认证帮手，对应帮手 ID
  points: number;            // 积分
  createdAt: string;
}
```

### 6.2 帮手（Helper）

```typescript
interface Helper {
  id: number;
  userId: number;
  name: string;
  avatar: string;
  rating: number;            // 4.0 - 5.0
  reviews: number;           // 评价总数
  services: ServiceType[];   // ['遛狗', '喂猫']
  community: string;
  communityId: number;
  distance?: string;          // 距离用户的距离 "200m"
  experience: string;        // 从业经验 "3年"
  isVerified: boolean;       // 是否已认证
  isStar: boolean;           // 是否明星帮手
  canUrgent: boolean;        // 是否可接紧急单
  bio: string;               // 个人简介
  level: number;             // 帮手等级 1-5
  status: 'active' | 'inactive' | 'banned';
  rating: number;
}
```

### 6.3 宠物（Pet）

```typescript
interface Pet {
  id: number;
  userId: number;
  name: string;
  type: 'dog' | 'cat' | 'other';
  breed: string;             // 品种
  age: string;               // "3岁"
  icon: string;              // emoji
  photo?: string;             // 照片 URL
  note?: string;              // 备注（习性、注意事项）
  isVaccinated: boolean;     // 是否已打疫苗
  createdAt: string;
}
```

### 6.4 订单（Order）

```typescript
interface Order {
  id: string;                // "20260413045"
  userId: number;
  helperId?: number;          // null 表示待接单
  serviceType: 'walk' | 'feed' | 'play' | 'clean';
  petId: number;
  date: string;              // "today" | "tomorrow" | "weekend" | "2026-04-15"
  timeSlot: string;          // "10:00-12:00"
  address: string;
  note?: string;
  price: number;              // 最终成交价
  budget?: number;            // 用户预算（用于匹配参考）
  status: 'pending' | 'matched' | 'ongoing' | 'completed' | 'cancelled';
  progress: number;           // 进度 0-4
  rating?: number;           // 1-5，完结后有
  review?: string;            // 评价内容
  proofPhotos?: string[];     // 服务凭证照片 URL[]
  tip?: number;              // 打赏金额
  couponId?: string;
  couponDiscount?: number;   // 优惠券优惠金额
  createdAt: string;
  updatedAt: string;
}
```

### 6.5 优惠券（Coupon）

```typescript
interface Coupon {
  id: string;
  userId: number;
  name: string;
  type: 'full_reduce' | 'discount' | 'new_user' | 'limited';
  value: number;             // 优惠金额或折扣率
  minAmount: number;         // 满X可用，0表示无门槛
  scope: string[];           // 适用范围，如 ['遛狗', '喂猫']
  validFrom: string;
  validTo: string;
  status: 'available' | 'used' | 'expired';
  source: string;            // 获取来源
}
```

### 6.6 地址（Address）

```typescript
interface Address {
  id: number;
  userId: number;
  label: string;             // "家" / "公司" / 自定义
  community: string;           // 小区名称
  building?: string;           // 楼栋
  unit?: string;               // 单元
  detail: string;              // 详细地址
  isDefault: boolean;
}
```

### 6.7 消息（Message）

```typescript
interface Message {
  id: string;
  userId: number;
  type: 'order' | 'system' | 'interaction';
  title: string;
  content: string;
  isRead: boolean;
  link?: string;              // 点击跳转链接
  createdAt: string;
}
```

---

## 7. API 接口汇总

### 7.1 认证与用户

| 方法|路径|描述|
|----|----|-----|
| `POST` | `/api/auth/login` | 微信登录获取 token |
| `POST` | `/api/auth/register` | 注册（绑定手机号） |
| `GET` | `/api/users/:id` | 获取用户信息 |
| `PUT` | `/api/users/:id` | 更新用户信息 |
| `GET` | `/api/users/:id/profile` | 获取宠主个人主页 |

### 7.2 帮手

| 方法|路径|描述|
|----|----|-----|
| `GET` | `/api/helpers` | 获取帮手列表（支持搜索/筛选/分页） |
| `GET` | `/api/helpers/:id` | 获取帮手详情 |
| `GET` | `/api/helpers/:id/reviews` | 获取帮手评价列表 |
| `GET` | `/api/helpers/nearby` | 获取附近帮手 |

### 7.3 订单

| 方法|路径|描述|
|----|----|-----|
| `POST` | `/api/orders` | 创建订单 |
| `GET` | `/api/orders` | 获取订单列表 |
| `GET` | `/api/orders/:id` | 获取订单详情 |
| `PUT` | `/api/orders/:id` | 更新订单信息 |
| `PUT` | `/api/orders/:id/status` | 更新订单状态 |
| `POST` | `/api/orders/:id/rating` | 提交评价（含凭证） |
| `DELETE` | `/api/orders/:id` | 取消订单 |

### 7.4 宠物

| 方法|路径|描述|
|----|----|-----|
| `GET` | `/api/users/:id/pets` | 获取用户宠物列表 |
| `POST` | `/api/pets` | 添加宠物 |
| `PUT` | `/api/pets/:id` | 更新宠物信息 |
| `DELETE` | `/api/pets/:id` | 删除宠物 |

### 7.5 地址

| 方法|路径|描述|
|----|----|-----|
| `GET` | `/api/users/:id/addresses` | 获取地址列表 |
| `POST` | `/api/addresses` | 添加地址 |
| `PUT` | `/api/addresses/:id` | 更新地址 |
| `DELETE` | `/api/addresses/:id` | 删除地址 |
| `PUT` | `/api/addresses/:id/default` | 设为默认地址 |

### 7.6 优惠券

| 方法|路径|描述|
|----|----|-----|
| `GET` | `/api/users/:id/coupons` | 获取用户优惠券 |
| `POST` | `/api/coupons/claim` | 领取优惠券 |
| `GET` | `/api/coupons/available` | 获取可用优惠券（下单时） |

### 7.7 紧急求助

| 方法|路径|描述|
|----|----|-----|
| `POST` | `/api/emergency` | 提交紧急求助 |
| `GET` | `/api/emergency/:id` | 获取求助详情 |

### 7.8 消息

| 方法|路径|描述|
|----|----|-----|
| `GET` | `/api/users/:id/messages` | 获取消息列表 |
| `PUT` | `/api/messages/:id/read` | 标记已读 |

---

## 8. WebSocket 实时消息

### 8.1 连接地址

```
wss://api.petcare.com/ws?token=xxx&userId=xxx
```

### 8.2 消息类型

| 事件名|方向|描述|
|-------|----|-----|
| `helper_accept`| server→client | 帮手接单通知 |
| `order_status_change`| server→client | 订单状态变更 |
| `new_message`| server→client | 新消息 |
| `helper_location`| server→client | 帮手实时位置（服务中） |
| `helper_arrive`| server→client | 帮手已到达 |

---

## 9. 微信支付集成

### 9.1 支付流程

1. 用户下单 → 调用 `wx.requestPayment` 发起支付
2. 支付成功 → 后台创建订单 → 推送订阅消息
3. 支付失败 → 订单取消

### 9.2 关键参数

```javascript
wx.requestPayment({
  timeStamp: '',      // 时间戳
  nonceStr: '',       // 随机字符串
  package: '',        // 预支付会话标识
  signType: 'MD5',
  paySign: ''         // 签名
})
```

### 9.3 退款流程（后台处理）

- 用户取消订单 → 原路退款
- 订单完成后 7 天可申诉退款

---

## 10. 消息订阅

### 10.1 订阅场景

| 场景|模板ID|触发时机|
|----|-----|--------|
| 帮手接单 | TM_XXXXX | 有人接单时 |
| 服务开始 | TM_XXXXX | 订单开始时 |
| 服务完成 | TM_XXXXX | 帮手确认完成时 |
| 新消息 | TM_XXXXX | 收到聊天消息时 |
| 优惠券到期 | TM_XXXXX | 优惠券即将过期（3天前） |

### 10.2 订阅触发

- 下单前：`wx.requestSubscribeMessage` 请求订阅
- 支付成功页：提示开启订阅

---

## 11. 分享机制

### 11.1 分享卡片

- **标题**：`毛孩子邻里 - 帮你找到靠谱的邻里宠伴`
- **封面图**：默认配图或宠物照片
- **路径**：`/pages/index/index?inviter_id=xxx`
- **分享来源**：首页、帮手详情页

### 11.2 分享海报

- 生成带小程序码的分享海报
- 长按识别跳转

---

## 12. 性能与优化

| 优化项|方案|
|-----|----|
|图片懒加载|`wx.lazy-load` 或 IntersectionObserver|
|列表分页|每页 20 条，触底加载更多|
|帮手搜索|前端本地过滤（数据量<100时），否则调 API|
|地图定位|`wx.getLocation` 缓存在本地（有效期1天）|
|骨架屏|列表加载时显示骨架屏占位|
|代码分割|各 tab 页面独立分包加载|

---

## 13. 测试要点

### 13.1 功能测试用例

| 场景|预期结果|
|-----|-------|
|未登录访问订单页|跳转登录页|
|发布页不选宠物直接点发布|提示"请选择宠物"|
|选择已选中宠物|高亮状态不变|
|切换优惠券 Tab|列表正确切换|
|完成订单不选星级|提示"请选择评分"|
|上传9张凭证后再点上传|提示"最多上传9张" |
|紧急求助提交后|推送通知所有本小区帮手|

### 13.2 边界测试

- 价格为0的订单 → 不允许
- 过期优惠券 → 自动移至已过期 Tab
- 无常用地址 → 显示空，需新增
- 网络断开 → Toast 提示"网络异常"
- 小程序切后台 → 订单状态轮询暂停

---

## 14. 项目结构（uni-app 示例）

```
src/
├── pages/
│   ├── index/          # 首页
│   ├── discover/        # 发现帮手
│   ├── publish/         # 发布需求
│   ├── orders/          # 我的订单
│   └── my/              # 个人中心
├── components/
│   ├── HelperCard/      # 帮手卡片组件
│   ├── OrderCard/       # 订单卡片组件
│   ├── CouponCard/      # 优惠券组件
│   ├── StarRating/      # 星级评分组件
│   └── ServiceTypeGrid/ # 服务类型选择组件
├── static/
│   ├── images/          # 本地图片
│   └── icons/          # 图标资源
├── services/
│   ├── api.js           # API 封装
│   ├── auth.js          # 认证服务
│   ├── order.js         # 订单服务
│   ├── user.js          # 用户服务
│   └── websocket.js     # WebSocket 服务
├── store/
│   └── index.js         # Pinia 状态管理
├── styles/
│   ├── variables.scss   # CSS 变量
│   └── common.scss      # 公共样式
├── utils/
│   ├── request.js       # axios 封装
│   ├── format.js        # 格式化工具
│   └── format.js       # 工具函数
├── App.vue
├── main.js
└── manifest.json        # uni-app 配置
```
