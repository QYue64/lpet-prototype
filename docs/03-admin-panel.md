# 毛孩子邻里 - 运营后台管理系统 开发文档

## 1. 项目概述

### 1.1 基本信息

| 字段 | 内容 |
|------|------|
| **系统名称** | 毛孩子邻里 - 运营后台管理系统 |
| **项目类型** | PC Web 管理后台（SaaS 型） |
| **定位** | 平台运营人员使用，管理用户、订单、帮手、优惠券等核心业务 |
| **使用部门** | 运营、客服、管理层 |
| **访问方式** | 浏览器（Chrome / Edge，建议 1440p+ 分辨率） |
| **部署环境** | Vue 3 + Element Plus / React + Ant Design（前后端分离） |

### 1.2 角色权限

| 角色|权限范围|
|-----|--------|
|超级管理员|全部模块，含系统设置|
|运营主管|全部业务模块（不含系统设置）|
|客服|用户管理、订单管理、投诉管理、消息推送|
|财务|财务管理、提现审批、订单管理|
|审核专员|帮手认证审核、用户管理|

---

## 2. UI/UX 规范

### 2.1 布局结构

```
┌─────────────────────────────────────────────────────┐
│  顶部导航栏（60px）                                  │
│  Logo + 系统名称    搜索框    消息通知    管理员信息  │
├────────────┬────────────────────────────────────────┤
│            │                                        │
│  侧边栏    │   主内容区                              │
│  (260px)   │                                        │
│            │   面包屑导航                            │
│  · 工作台  │   ────────────                         │
│  · 用户    │                                        │
│  · 帮手    │   页面标题 + 副标题                     │
│  · 宠物    │                                        │
│  · 小区    │   筛选栏 / 快捷操作                    │
│  · 订单    │                                        │
│  · 紧急    │   数据内容区                           │
│  · 提现    │   （卡片/表格/表单）                    │
│  · 财务    │                                        │
│  · 评价    │                                        │
│  · 内容    │                                        │
│  · 投诉    │                                        │
│  · 推送    │                                        │
│  · 认证    │                                        │
│  · 优惠券  │                                        │
│  · 系统    │                                        │
│            │                                        │
└────────────┴────────────────────────────────────────┘
```

### 2.2 颜色系统

```css
/* 主色调 - 品牌绿（与前端小程序呼应） */
--primary:         #7BA05B;
--primary-light:   #9BC27A;
--primary-dark:    #5C8040;
--primary-bg:      rgba(123, 160, 91, 0.08);

/* 辅助色 */
--accent:          #F4A261;
--accent-light:   #F7C59F;
--accent-bg:      rgba(244, 162, 97, 0.1);

/* 状态色 */
--danger:          #E57373;
--danger-bg:      rgba(229, 115, 115, 0.1);
--warning:         #FFB74D;
--warning-bg:      rgba(255, 183, 77, 0.1);
--success:         #81C784;
--success-bg:      rgba(129, 199, 132, 0.1);
--info:            #64B5F6;
--info-bg:         rgba(100, 181, 246, 0.1);
--purple:          #BA68C8;
--purple-bg:       rgba(186, 104, 200, 0.1);

/* 侧边栏 */
--bg-sidebar:      #2D3436;
--bg-sidebar-hover: #3D4A4C;

/* 背景 */
--bg:             #F5F3F0;
--bg-card:        #FFFFFF;
--border:          #E8E5E2;

/* 文字 */
--text:           #2D3436;
--text-secondary: #636E72;
--text-muted:     #B2BEC3;
```

### 2.3 字体规范

```css
font-family: 'Noto Sans SC', -apple-system, BlinkMacSystemFont, sans-serif;
```

| 用途|字号|字重|行高|
|-----|-----|-----|-----|
|页面大标题|28px|700|1.3|
|卡片标题|16px|600|1.4|
|正文|14px|400|1.6|
|表格表头|13px|600|1.4|
|表格内容|13px|400|1.5|
|辅助说明|12px|400|1.5|
|角标/徽章|11px|500|1.4|

### 2.4 间距系统

- 侧边栏宽度：260px
- 顶部导航高度：60px
- 内容区边距：24px
- 卡片间距：20px
- 表格行高：52px（可点击操作）
- 弹窗内边距：20px

### 2.5 动效

- 页面切换：淡入，opacity 0→1，300ms
- 卡片加载：fadeInUp，translateY 20px→0，400ms，delay 50ms 逐级出现
- 按钮悬停：transform scale(1.02)，150ms
- 表格行悬停：背景色变浅灰

---

## 3. 页面模块详解

### 3.1 模块概览

| 模块|路由|描述|
|-----|-----|-----|
|工作台|/|运营数据概览 + 待办事项|
|用户管理|/users|所有注册用户列表|
|帮手管理|/helpers|认证帮手管理|
|宠物管理|/pets|平台宠物档案|
|小区管理|/communities|服务覆盖小区管理|
|订单管理|/orders|所有服务订单|
|紧急求助|/emergency|紧急求助请求处理|
|提现审批|/withdraw|帮手提现申请|
|财务管理|/finance|平台收支流水|
|评价管理|/reviews|用户/帮手互评|
|内容社区|/posts|社区帖子管理|
|投诉管理|/complaints|用户投诉处理|
|消息推送|/notifications|系统公告/推送|
|认证审核|/certification|帮手认证材料审核|
|优惠券管理|/coupons|优惠券创建/发放|
|系统设置|/settings|平台基础配置|

---

### 3.2 工作台（Dashboard）

#### 3.2.1 页面结构

```
工作台
├── 欢迎横幅
│   └── 时间问候语 + 快捷待办入口
├── 核心数据统计卡片（4栏）
│   ├── 注册用户数（当日+本月，趋势）
│   ├── 认证帮手数（当日+本月，趋势）
│   ├── 本月订单数（当日+本月，趋势）
│   └── 本月平台收入（当日+本月，趋势）
├── 图表区（2栏）
│   ├── 订单趋势图（折线图）
│   │   ├── 时间维度切换（近7天/30天/90天）
│   │   └── 支持鼠标悬停查看具体数值
│   └── 服务类型分布（饼图/环形图）
│       ├── 遛狗 / 喂猫 / 陪玩 / 清洁
│       └── 显示各类型订单占比
├── 快捷数据面板（4栏）
│   ├── 待处理紧急求助（红色高亮）
│   ├── 待审核帮手认证
│   ├── 待处理提现申请
│   └── 待回复投诉
├── 最新订单列表
│   └── 实时订单（最新5条）
│       ├── 订单号 + 宠主 + 帮手 + 服务类型 + 金额 + 状态
│       └── 操作（查看详情）
└── 系统公告
    └── 公告列表（最新3条）
```

#### 3.2.2 图表说明

**订单趋势折线图：**

- X 轴：日期
- Y 轴：订单数量
- 数据源：`GET /api/admin/stats/orders-trend`
- 支持时间维度切换（7d / 30d / 90d）

**服务类型分布图：**

- 类型：遛狗 / 喂猫 / 陪玩 / 清洁
- 颜色对应各服务类型主色
- 悬停显示具体数量和占比

#### 3.2.3 API 接口

```
GET /api/admin/dashboard/summary
  Response: {
    users: { total, today, month, trend },
    helpers: { total, certified, today, month, trend },
    orders: { total, today, month, trend },
    income: { total, today, month, trend },
    pending: { emergency, certification, withdraw, complaint }
  }

GET /api/admin/stats/orders-trend
  Query: days: 7|30|90
  Response: { labels: string[], data: number[] }

GET /api/admin/stats/service-distribution
  Response: { labels: string[], data: number[], colors: string[] }

GET /api/admin/orders/recent
  Query: limit?: number (default 5)
  Response: Order[]

GET /api/admin/notices
  Response: Notice[]
```

---

### 3.3 用户管理（Users）

#### 3.3.1 页面结构

```
用户管理
├── 页面标题 + 用户总数统计
├── 筛选栏
│   ├── 搜索框（昵称 / 手机号 / ID）
│   ├── 身份筛选（全部 / 宠主 / 帮手 / 两者）
│   ├── 认证状态（全部 / 已认证 / 未认证）
│   └── 注册时间范围
├── 用户数据表格
│   ├── 用户ID / 昵称 / 头像 / 手机号
│   ├── 身份标签（宠主 / 帮手）
│   ├── 所属小区
│   ├── 注册时间
│   ├── 订单数
│   ├── 状态（正常 / 禁用）
│   └── 操作（查看详情 / 编辑 / 禁用）
├── 分页器
└── 批量操作（批量禁用/启用）
```

#### 3.3.2 用户详情侧滑抽屉

- 侧滑打开，用户信息完整展示
- Tab：基本信息 / 宠物档案 / 订单记录 / 评价历史
- 操作：编辑信息 / 禁用账号 / 设为帮手 / 查看认证

#### 3.3.3 API 接口

```
GET /api/admin/users
  Query: search?, role?, verified?, dateFrom?, dateTo?, page?, pageSize?
  Response: { list: User[], total: number }

GET /api/admin/users/:id
  Response: UserDetail

PUT /api/admin/users/:id
  Body: { nickname?, phone?, status? }
  Response: { success: boolean }

PUT /api/admin/users/:id/disable
  Body: { reason: string }
  Response: { success: boolean }

PUT /api/admin/users/:id/enable
  Response: { success: boolean }

GET /api/admin/users/:id/pets
  Response: Pet[]

GET /api/admin/users/:id/orders
  Query: page?, pageSize?
  Response: { list: Order[], total: number }
```

---

### 3.4 帮手管理（Helpers）

#### 3.4.1 页面结构

```
帮手管理
├── 页面标题 + 帮手总数 / 已认证数
├── 筛选栏
│   ├── 搜索框（姓名 / 手机号 / ID）
│   ├── 认证状态（全部 / 已认证 / 待审核 / 已拒绝）
│   ├── 服务类型（遛狗 / 喂猫 / 陪玩 / 清洁）
│   ├── 等级筛选（Lv.1-5）
│   └── 状态（接单中 / 暂停接单 / 已封禁）
├── 帮手数据表格
│   ├── 帮手ID / 姓名 / 头像
│   ├── 认证服务类型
│   ├── 等级（Lv.）
│   ├── 评分 / 评价数
│   ├── 本月收入
│   ├── 小区
│   ├── 接单状态
│   └── 操作（查看 / 编辑等级 / 封禁）
└── 分页器
```

#### 3.4.2 帮手等级管理

- 后台可手动调整帮手等级
- 等级调整记录留存日志
- 封禁后帮手无法登录（仅限严重违规）

#### 3.4.3 API 接口

```
GET /api/admin/helpers
  Query: search?, status?, service?, level?, page?, pageSize?
  Response: { list: Helper[], total: number }

GET /api/admin/helpers/:id
  Response: HelperDetail

PUT /api/admin/helpers/:id/level
  Body: { level: number, reason: string }
  Response: { success: boolean }

PUT /api/admin/helpers/:id/status
  Body: { status: 'active' | 'inactive' | 'banned', reason?: string }
  Response: { success: boolean }

GET /api/admin/helpers/:id/reviews
  Response: Review[]

GET /api/admin/helpers/:id/orders
  Query: page?, pageSize?
  Response: { list: Order[], total: number }
```

---

### 3.5 宠物管理（Pets）

#### 3.5.1 页面结构

```
宠物管理
├── 页面标题 + 宠物总数
├── 筛选栏
│   ├── 搜索（宠物名 / 所属用户）
│   ├── 类型（狗 / 猫 / 其他）
│   └── 品种
├── 宠物数据表格
│   ├── 宠物ID / 名称 / 类型图标
│   ├── 品种 / 年龄
│   ├── 所属用户（点击跳转用户详情）
│   ├── 健康状态（已标注/正常）
│   └── 操作（查看详情）
└── 分页器
```

#### 3.5.2 API 接口

```
GET /api/admin/pets
  Query: search?, type?, breed?, page?, pageSize?
  Response: { list: Pet[], total: number }

GET /api/admin/pets/:id
  Response: PetDetail

GET /api/admin/users/:id/pets
  Response: Pet[]
```

---

### 3.6 小区管理（Communities）

#### 3.6.1 页面结构

```
小区管理
├── 页面标题 + 已开通小区数
├── 小区数据卡片（3栏/4栏网格）
│   └── 小区卡片
│       ├── 小区名称
│       ├── 区域（城市 + 区）
│       ├── 覆盖用户数
│       ├── 认证帮手数
│       ├── 本月订单数
│       ├── 开通状态标签
│       └── 操作（编辑 / 查看详情）
└── 小区列表表格
    └── 更详细的筛选和操作
```

#### 3.6.2 小区状态

| 状态|含义|操作|
|-----|----|----|
|已开通|已开放服务，可正常下单|编辑/暂停|
|待开通|申请开通，待运营审核|审核开通|
|未覆盖|从未申请|申请开通|
|已暂停|运营手动暂停|恢复开通|

#### 3.6.3 API 接口

```
GET /api/admin/communities
  Query: status?, city?, page?, pageSize?
  Response: { list: Community[], total: number }

PUT /api/admin/communities/:id/status
  Body: { status: 'active' | 'paused' | 'pending' | 'inactive' }
  Response: { success: boolean }

POST /api/admin/communities
  Body: { name, city, district, boundary }
  Response: { communityId: number }
```

---

### 3.7 订单管理（Orders）

#### 3.7.1 页面结构

```
订单管理
├── 页面标题 + 订单总数
├── 筛选栏
│   ├── 搜索（订单号 / 宠主名 / 帮手名）
│   ├── 订单状态（全部 / 待接单 / 进行中 / 已完成 / 已取消）
│   ├── 服务类型
│   ├── 时间范围
│   └── 小区
├── 订单数据表格
│   ├── 订单号（可点击跳转详情）
│   ├── 宠主信息
│   ├── 帮手信息（待接单显示"—"）
│   ├── 服务类型标签
│   ├── 服务时间
│   ├── 订单金额
│   ├── 凭证缩略图（已完成订单）
│   ├── 订单状态 + 状态点
│   └── 操作（查看详情）
└── 分页器
```

#### 3.7.2 订单详情页面（点击订单跳转）

- 独立页面（非抽屉），包含完整订单信息
- 包含服务进度时间轴
- 包含宠主和帮手双方信息
- 可查看凭证照片（点击大图预览）
- 操作：手动取消订单、修改订单金额、处理投诉

#### 3.7.3 API 接口

```
GET /api/admin/orders
  Query: search?, status?, serviceType?, dateFrom?, dateTo?,
          communityId?, page?, pageSize?
  Response: { list: Order[], total: number }

GET /api/admin/orders/:id
  Response: OrderDetail

PUT /api/admin/orders/:id/status
  Body: { status, reason? }
  Response: { success: boolean }

PUT /api/admin/orders/:id/price
  Body: { price: number, reason: string }
  Response: { success: boolean }
```

---

### 3.8 紧急求助（Emergency）

#### 3.8.1 页面结构

```
紧急求助
├── 页面标题 + 待处理求助数
├── 紧急求助列表
│   └── 求助卡片
│       ├── 紧急程度标签（高/中/低）
│       ├── 宠物信息（类型 + 名称）
│       ├── 求助描述
│       ├── 用户手机号 + 联系方式
│       ├── 求助时间
│       ├── 状态（待处理 / 处理中 / 已解决）
│       ├── 操作用户数（已广播给X位帮手）
│       └── 操作（立即处理 / 已解决 / 转客服）
└── 历史记录 Tab
```

#### 3.8.2 紧急求助处理流程

1. 收到紧急求助 → 系统自动广播给本小区所有帮手
2. 运营人员可主动"广播帮手"催促接单
3. 有帮手接单后 → 状态变为"处理中"
4. 求助完成后 → 标记"已解决"
5. 若超时无帮手响应 → 运营人工介入联系用户

#### 3.8.3 API 接口

```
GET /api/admin/emergency
  Query: status?, page?, pageSize?
  Response: { list: EmergencyRequest[], total: number }

PUT /api/admin/emergency/:id/status
  Body: { status: 'processing' | 'resolved' | 'transferred' }
  Response: { success: boolean }

POST /api/admin/emergency/:id/broadcast
  Response: { notifiedHelpers: number }
```

---

### 3.9 提现审批（Withdraw）

#### 3.9.1 页面结构

```
提现审批
├── 页面标题 + 待处理数
├── 筛选栏
│   ├── 帮手姓名 / ID
│   ├── 状态（待审批 / 已通过 / 已拒绝 / 已打款）
│   └── 时间范围
├── 提现申请表格
│   ├── 申请ID
│   ├── 帮手姓名 + 头像
│   ├── 申请金额
│   ├── 微信昵称 / OpenID
│   ├── 申请时间
│   ├── 账户余额（参考）
│   ├── 累计提现金额
│   ├── 状态
│   └── 操作（通过 / 拒绝 / 查看详情）
└── 分页器
```

#### 3.9.2 提现审批操作

| 操作|条件|结果|
|-----|----|----|
|通过|金额 < ¥500，自动通过|进入微信支付打款队列|
|拒绝|填写拒绝原因|帮手收到拒绝通知，退还余额|
|人工复核|金额 ≥ ¥500|需财务主管复核|

#### 3.9.3 API 接口

```
GET /api/admin/withdraw
  Query: status?, dateFrom?, dateTo?, page?, pageSize?
  Response: { list: WithdrawRequest[], total: number }

PUT /api/admin/withdraw/:id/approve
  Response: { success: boolean, status: 'approved' }

PUT /api/admin/withdraw/:id/reject
  Body: { reason: string }
  Response: { success: boolean, status: 'rejected' }

POST /api/admin/withdraw/:id/process
  // 触发微信支付打款
  Response: { wxPayId: string, status: 'processing' }
```

---

### 3.10 财务管理（Finance）

#### 3.10.1 页面结构

```
财务管理
├── 页面标题
├── 财务概览卡片
│   ├── 累计平台收入
│   ├── 本月平台收入（= 订单额 × 平台抽成）
│   ├── 本月已提现总额
│   └── 待打款总额
├── 时间筛选
│   └── 近7天 / 近30天 / 自定义时间范围
├── 收支流水表格
│   ├── 流水号
│   ├── 类型（收入/支出/提现/退款）
│   ├── 关联订单/用户
│   ├── 金额
│   ├── 余额变动
│   ├── 时间
│   └── 备注
└── 分页器
```

#### 3.10.2 平台结算规则

- 平台从每笔订单中抽取服务费的 **10%** 作为平台收入
- 例：订单 ¥60，帮手收入 ¥54，平台收入 ¥6
- 退款订单：全额退款，平台收入退还

#### 3.10.3 API 接口

```
GET /api/admin/finance/summary
  Query: dateFrom?, dateTo?
  Response: {
    totalIncome: number,
    monthIncome: number,
    monthWithdraw: number,
    pendingWithdraw: number
  }

GET /api/admin/finance/records
  Query: type?, dateFrom?, dateTo?, page?, pageSize?
  Response: { list: FinanceRecord[], total: number }
```

---

### 3.11 评价管理（Reviews）

#### 3.11.1 页面结构

```
评价管理
├── 页面标题 + 评价总数
├── 筛选栏
│   ├── 评分（5星 / 4星 / 3星及以下）
│   ├── 是否有晒图
│   ├── 时间范围
│   └── 排序（最新 / 评分最低）
├── 评价表格
│   ├── 评价人头像 + 昵称
│   ├── 被评价者（帮手）
│   ├── 关联订单号（可跳转）
│   ├── 评分星级
│   ├── 评价内容
│   ├── 晒图缩略图（点击放大）
│   ├── 发布时间
│   └── 操作（回复 / 隐藏 / 查看详情）
└── 分页器
```

#### 3.11.2 评价操作

- **回复**：运营人员可代帮手回复差评（需留存记录）
- **隐藏**：差评过多时，运营可隐藏负面评价（用户仍可见"该评价已被屏蔽"）
- **查看详情**：跳转订单详情页

#### 3.11.3 API 接口

```
GET /api/admin/reviews
  Query: rating?, hasPhoto?, dateFrom?, dateTo?, sort?, page?, pageSize?
  Response: { list: Review[], total: number }

PUT /api/admin/reviews/:id/hide
  Body: { reason: string }
  Response: { success: boolean }

PUT /api/admin/reviews/:id/reply
  Body: { content: string }
  Response: { success: boolean }
```

---

### 3.12 内容社区（Posts）

#### 3.12.1 页面结构

```
内容社区
├── 页面标题 + 帖子总数
├── 筛选栏
│   ├── 内容类型（经验分享 / 求助 / 晒宠）
│   ├── 状态（已发布 / 待审核 / 已删除）
│   └── 关键词搜索
├── 帖子列表
│   ├── 帖子ID
│   ├── 用户信息
│   ├── 内容预览（文字+图片）
│   ├── 类型标签
│   ├── 发布时间
│   ├── 点赞数 / 评论数
│   └── 操作（审核通过 / 删除 / 查看详情）
└── 分页器
```

#### 3.12.2 API 接口

```
GET /api/admin/posts
  Query: type?, status?, keyword?, page?, pageSize?
  Response: { list: Post[], total: number }

PUT /api/admin/posts/:id/status
  Body: { status: 'approved' | 'rejected' | 'deleted' }
  Response: { success: boolean }
```

---

### 3.13 投诉管理（Complaints）

#### 3.13.1 页面结构

```
投诉管理
├── 页面标题 + 待处理投诉数（红色高亮Badge）
├── 筛选栏
│   ├── 投诉对象（宠主 / 帮手）
│   ├── 类型（服务态度 / 迟到 / 物品损坏 / 其他）
│   └── 状态（待处理 / 处理中 / 已处理）
├── 投诉列表
│   ├── 投诉ID
│   ├── 投诉人 / 被投诉人
│   ├── 关联订单
│   ├── 投诉类型
│   ├── 投诉内容摘要
│   ├── 提交时间
│   ├── 状态
│   └── 操作（立即处理 / 查看详情）
└── 历史记录
```

#### 3.13.2 投诉处理流程

1. 收到投诉 → 运营初步审核
2. 联系被投诉方了解情况
3. 依据证据做出裁决（赔偿/警告/封禁）
4. 通知双方处理结果
5. 重大投诉上报管理层

#### 3.13.3 API 接口

```
GET /api/admin/complaints
  Query: target?, type?, status?, page?, pageSize?
  Response: { list: Complaint[], total: number }

PUT /api/admin/complaints/:id/status
  Body: { status: 'processing' | 'resolved', resolution?: string }
  Response: { success: boolean }

POST /api/admin/complaints/:id/penalty
  Body: { targetId, type: 'warning' | 'suspend' | 'ban', reason: string }
  Response: { success: boolean }
```

---

### 3.14 消息推送（Notifications）

#### 3.14.1 页面结构

```
消息推送
├── 页面标题
├── 推送类型 Tab
│   ├── 系统公告
│   ├── 活动推送
│   └── 个性化推送
├── 历史推送列表
│   ├── 推送ID / 标题
│   ├── 类型标签
│   ├── 发送范围（全部用户 / 定向小区 / 定向人群）
│   ├── 发送时间
│   ├── 阅读量 / 触达量
│   └── 操作（查看 / 删除）
└── 创建推送按钮
    └── 打开推送创建表单/弹窗
```

#### 3.14.2 推送创建表单

```
创建推送
├── 推送标题（必填，限30字）
├── 推送内容（必填，限500字）
├── 推送类型（公告 / 活动 / 版本更新）
├── 跳转链接（可选，跳转小程序页面）
├── 发送范围
│   ├── 全部用户
│   ├── 指定小区（多选）
│   └── 指定人群标签（新手/活跃/沉默）
├── 定时发送（可选，填写日期时间）
└── 发送预览（可输入测试手机号）
```

#### 3.14.3 API 接口

```
GET /api/admin/notifications
  Query: type?, page?, pageSize?
  Response: { list: Notification[], total: number }

POST /api/admin/notifications
  Body: {
    title: string,
    content: string,
    type: 'announcement' | 'campaign' | 'personalized',
    target: 'all' | { communityIds: number[] } | { tags: string[] },
    link?: string,
    scheduledAt?: string
  }
  Response: { notificationId: string, status: 'sent' | 'scheduled' }

DELETE /api/admin/notifications/:id
  Response: { success: boolean }
```

---

### 3.15 认证审核（Certification）

#### 3.15.1 页面结构

```
认证审核
├── 页面标题 + 待审核数（Badge高亮）
├── 筛选栏
│   ├── 认证状态（全部 / 待审核 / 已通过 / 已拒绝）
│   └── 时间范围
├── 认证申请表格
│   ├── 申请ID
│   ├── 用户姓名 + 头像
│   ├── 申请时间
│   ├── 认证服务类型
│   ├── 小区信息
│   ├── 状态
│   └── 操作（立即审核 / 查看详情）
└── 分页器
```

#### 3.15.2 认证审核详情

每个帮手认证包含 3 个步骤的审核：

| 步骤|审核内容|
|-----|--------|
|身份认证|身份证照片 + 姓名 + 身份证号（人工核验）|
|经验证明|上传的材料（证书/培训记录/以往照片等）|
|小区认证|门禁卡/物业证明/房产证照片|

#### 3.15.3 审核操作

- **通过**：三个步骤全部点击"通过"，系统自动变更认证状态
- **拒绝**：填写拒绝原因（发送给用户推送+短信）
- **退回补充**：让用户重新上传某项材料

#### 3.15.4 API 接口

```
GET /api/admin/certifications
  Query: status?, page?, pageSize?
  Response: { list: CertificationRequest[], total: number }

GET /api/admin/certifications/:id
  Response: CertificationDetail

PUT /api/admin/certifications/:id/step/:stepType/approve
  Response: { success: boolean }

PUT /api/admin/certifications/:id/step/:stepType/reject
  Body: { reason: string }
  Response: { success: boolean, status: 'rejected' }

PUT /api/admin/certifications/:id/approve-all
  Response: { success: boolean, status: 'approved' }
```

---

### 3.16 优惠券管理（Coupons）

#### 3.16.1 页面结构

```
优惠券管理
├── 页面标题 + 统计卡片（4栏）
│   ├── 全部优惠券数
│   ├── 可用优惠券数
│   ├── 已发放数量
│   └── 优惠总额
├── 筛选栏
│   ├── 全部 / 满减券 / 折扣券 / 新人券 / 限时券 / 已过期
├── 优惠券表格
│   ├── 优惠券名称
│   ├── 类型标签
│   ├── 优惠内容（¥20 / 9折）
│   ├── 使用条件（满X可用）
│   ├── 已发放 / 已使用
│   ├── 有效期范围
│   ├── 状态（进行中 / 未开始 / 已过期）
│   └── 操作（编辑 / 暂停发放 / 复制延期）
└── 创建优惠券按钮
```

#### 3.16.2 创建优惠券表单

```
创建优惠券
├── 基本信息
│   ├── 优惠券名称（必填）
│   ├── 类型（满减券 / 折扣券 / 新人券 / 限时券）
│   └── 适用服务范围（全部 / 指定服务类型）
├── 优惠规则
│   ├── 优惠金额（满减券）
│   ├── 折扣率（折扣券，如 9折）
│   └── 使用条件（满X元可用，0=无门槛）
├── 发放设置
│   ├── 发放数量
│   ├── 每人限领数量
│   ├── 发放方式（手动领取 / 平台自动发放）
│   └── 有效期（开始日期 ~ 结束日期）
└── 保存创建
```

#### 3.16.3 API 接口

```
GET /api/admin/coupons
  Query: type?, status?, page?, pageSize?
  Response: { list: Coupon[], total: number }

POST /api/admin/coupons
  Body: {
    name: string,
    type: 'full_reduce' | 'discount' | 'new_user' | 'limited',
    value: number,
    minAmount: number,
    scope: string[],
    totalQuantity: number,
    perUserLimit: number,
    distributionType: 'manual' | 'auto',
    validFrom: string,
    validTo: string
  }
  Response: { couponId: string }

PUT /api/admin/coupons/:id
  Body: { ...partial fields }
  Response: { success: boolean }

PUT /api/admin/coupons/:id/status
  Body: { status: 'active' | 'paused' | 'expired' }
  Response: { success: boolean }

POST /api/admin/coupons/:id/distribute
  Body: { userIds?: number[], communityIds?: number[], tags?: string[] }
  Response: { distributed: number }
```

---

### 3.17 系统设置（Settings）

#### 3.17.1 页面结构

```
系统设置
├── 基础配置
│   ├── 平台名称
│   ├── 平台 Logo
│   ├── 联系电话
│   ├── 客服时间
│   └── 平台公告
├── 抽成比例配置
│   ├── 平台服务费比例（默认 10%）
│   └── 最低服务费金额
├── 小程序配置
│   ├── AppID
│   ├── AppSecret（加密显示）
│   └── 服务器域名白名单
├── 通知配置
│   ├── 紧急求助自动广播开关
│   ├── 新订单推送帮手开关
│   └── 订单完成提醒开关
└── 保存按钮
```

#### 3.17.2 API 接口

```
GET /api/admin/settings
  Response: Settings

PUT /api/admin/settings
  Body: { ...partial settings }
  Response: { success: boolean }
```

---

## 4. 通用组件

### 4.1 表格组件

- 支持列排序（点击表头切换升/降/默认）
- 支持列宽拖拽调整
- 支持批量选择（左侧复选框）
- 支持行悬停高亮
- 支持固定列（操作列固定在右侧）
- 支持加载骨架屏

### 4.2 筛选栏组件

- 搜索框：带防抖（300ms）
- 下拉选择：支持单选/多选
- 日期范围选择器：支持快捷选项（今天/本周/本月/自定义）
- 重置按钮：清空所有筛选条件

### 4.3 分页组件

- 显示总数、当前页、每页条数
- 支持跳转至指定页
- 支持每页条数切换（20/50/100）

### 4.4 弹窗/抽屉组件

- **普通弹窗**：用于确认操作（如删除确认）
- **侧滑抽屉**：用于详情查看/编辑（宽度 480px 或 640px）
- **全屏弹窗**：用于复杂表单（如创建优惠券）

### 4.5 统计卡片组件

- 图标 + 颜色主题（primary/accent/info/danger）
- 数值（超大字体）
- 标签文字
- 可选趋势指示（↑ 12% / ↓ 5%）

### 4.6 图表组件

- ECharts 封装
- 常用图表：折线图、柱状图、饼图、环形图
- 支持响应式尺寸
- 支持导出图片

---

## 5. 权限控制

### 5.1 前端权限控制

```javascript
// 路由守卫（示例）
const permission = {
  'super_admin': ['*'],  // 全部权限
  'operation_manager': ['dashboard', 'users', 'helpers', 'pets', 'communities',
                        'orders', 'emergency', 'reviews', 'posts', 'complaints',
                        'notifications', 'certification', 'coupons', 'finance', 'withdraw'],
  'customer_service': ['users', 'orders', 'complaints', 'notifications'],
  'finance': ['finance', 'withdraw', 'orders'],
  'reviewer': ['certification', 'users']
}
```

### 5.2 按钮级权限

- 根据用户角色动态显示/隐藏操作按钮
- 未授权按钮显示禁用状态（灰色 + tooltip 提示）

---

## 6. 数据导出

- 订单数据支持导出 Excel（按筛选条件）
- 用户数据支持导出
- 帮手收入记录支持导出
- 导出字段可配置（高级导出）

---

## 7. 项目结构

```
admin-web/
├── src/
│   ├── api/                  # API 接口封装
│   │   ├── user.js
│   │   ├── order.js
│   │   ├── finance.js
│   │   └── index.js
│   ├── components/          # 通用组件
│   │   ├── Table/           # 数据表格
│   │   ├── FilterBar/       # 筛选栏
│   │   ├── Pagination/      # 分页
│   │   ├── StatCard/        # 统计卡片
│   │   ├── Drawer/          # 侧滑抽屉
│   │   ├── Modal/           # 弹窗
│   │   └── Charts/          # 图表封装
│   ├── layouts/
│   │   ├── MainLayout.vue   # 主布局（含侧边栏）
│   │   └── BlankLayout.vue
│   ├── pages/
│   │   ├── Dashboard/       # 工作台
│   │   ├── Users/           # 用户管理
│   │   ├── Helpers/         # 帮手管理
│   │   ├── Pets/            # 宠物管理
│   │   ├── Communities/      # 小区管理
│   │   ├── Orders/          # 订单管理
│   │   ├── Emergency/       # 紧急求助
│   │   ├── Withdraw/        # 提现审批
│   │   ├── Finance/         # 财务管理
│   │   ├── Reviews/         # 评价管理
│   │   ├── Posts/           # 内容社区
│   │   ├── Complaints/      # 投诉管理
│   │   ├── Notifications/   # 消息推送
│   │   ├── Certification/   # 认证审核
│   │   ├── Coupons/         # 优惠券管理
│   │   └── Settings/         # 系统设置
│   ├── router/              # 路由配置
│   ├── store/                # Pinia 状态管理
│   ├── styles/               # 全局样式
│   │   ├── variables.scss
│   │   └── common.scss
│   ├── utils/               # 工具函数
│   ├── App.vue
│   └── main.js
├── public/
└── package.json
```
