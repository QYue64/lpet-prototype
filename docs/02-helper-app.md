# 毛孩子邻里 - 帮手端小程序 开发文档

## 1. 项目概述

### 1.1 基本信息

| 字段 | 内容 |
|------|------|
| **应用名称** | 毛孩子邻里（帮手端） |
| **小程序 AppID** | `wx_xxxxxxxxxxxx_helper`（待申请） |
| **页面类型** | 微信小程序（独立 App，非宠主端子模块） |
| **定位** | 邻里宠物服务平台 - 帮手端 |
| **目标用户** | 通过平台认证、具备宠物照顾技能的社区居民 |
| **核心功能** | 抢单/接单、订单管理、收入提现、帮手认证 |
| **与宠主端关系** | 同一微信账号体系，需分别下载/进入 |

### 1.2 账号与认证

- **登录方式**：微信一键登录（与宠主端同一 OpenID）
- **帮手认证流程**（3步审核）：
  1. 身份认证：上传身份证正反面，平台审核真实身份
  2. 宠物照顾经验证明：上传证书/证明材料
  3. 小区认证：验证居住在本小区（门禁卡/房产证/物业证明）
- **认证状态**：`pending` → `approved`（通过）/ `rejected`（拒绝，附原因）
- **认证有效期**：认证通过后长期有效，每年需重新认证一次

### 1.3 核心业务逻辑

#### 抢单池机制

- 所有新发布的需求进入"抢单池"（公共订单池）
- 帮手可查看池内所有订单（按距离、时间筛选）
- 帮手点击"抢单"后，该订单锁定（15分钟内其他帮手不可见）
- 若15分钟内未确认，订单释放回池
- **先到先得**，同一订单仅第一个确认者获得

#### 平台派单机制

- 以下情况由平台主动派单给高评分帮手（被动接收）：
  - 紧急订单（30分钟内上门）
  - 用户指定高评分帮手
  - 高价值订单
- 帮手可选择开启/关闭"接受平台派单"
- 平台派单后，帮手有15分钟决策时间

#### 结算周期

- **结算周期**：每周一结算上周（周一00:00 - 周日24:00）完成的订单
- **到账时间**：周一处理，周一至周三陆续到账微信钱包（约1-3个工作日）
- **最低提现金额**：¥50，不满¥50顺延至下周
- **提现方式**：仅支持微信钱包（自动到账）

---

## 2. UI/UX 规范

### 2.1 设备适配

- 与宠主端一致：375 × 812 px，底部 tabBar 84px

### 2.2 颜色系统

```css
/* 主色调 - 专业蓝（与宠主端绿色区分） */
--primary:        #5C8DDC;   /* 主蓝色 */
--primary-light:  #7BA4E8;   /* 浅蓝 */
--primary-dark:   #3D6DC4;   /* 深蓝 */
--primary-bg:     rgba(92, 141, 220, 0.08);

/* 辅助色（与宠主端一致） */
--accent:         #F4A261;
--accent-light:  #F7C59F;
--accent-bg:     rgba(244, 162, 97, 0.1);
--danger:        #E57373;
--danger-bg:     rgba(229, 115, 115, 0.1);
--warning:        #FFB74D;
--warning-bg:     rgba(255, 183, 77, 0.1);
--success:        #81C784;
--success-bg:     rgba(129, 199, 132, 0.1);
--info:           #64B5F6;
--info-bg:        rgba(100, 181, 246, 0.1);
--purple:         #BA68C8;
--purple-bg:      rgba(186, 104, 200, 0.1);
--gold:           #FFD700;   /* 帮手等级金色 */
--gold-bg:        rgba(255, 215, 0, 0.1);

/* 背景与文字 */
--bg:             #F4F8FF;   /* 浅蓝背景（与宠主端暖白区分） */
--bg-card:        #FFFFFF;
--bg-secondary:   #EEF3FB;
--text:           #2D3748;
--text-light:     #718096;
--text-muted:     #A0AEC0;
--border:         #DDE6F0;
--shadow:         rgba(92, 141, 220, 0.08);
--shadow-lg:      rgba(92, 141, 220, 0.15);
```

### 2.3 帮手等级体系

| 等级|名称|图标|晋级条件|
|----|----|----|--------|
|Lv.1|新手帮手|🐣|注册通过|
|Lv.2|铜牌帮手|🏅|累计20单 + 评分≥4.5|
|Lv.3|银牌帮手|🥈|累计50单 + 评分≥4.7|
|Lv.4|金牌帮手|🥇|累计100单 + 评分≥4.8|
|Lv.5|钻石帮手|💎|累计200单 + 评分≥4.9|

- 高等级帮手优先获得平台派单
- 高等级帮手在抢单池中可见更多订单

---

## 3. 页面结构

### 3.1 页面概览

```
帮手端小程序
├── tabBar（底部导航，4个tab）
│   ├── 首页（抢单/任务）
│   ├── 订单（进行中/已完成）
│   ├── 收入（本周/本月/提现）
│   └── 我的（认证/设置）
│
├── 弹窗/Modal 覆盖层
│   ├── 提现弹窗（modal-withdraw）
│   ├── 帮手认证详情弹窗（modal-cert）
│   ├── 服务时间设置弹窗（modal-servicetime）
│   └── 消息通知弹窗（modal-messages）
│
└── 独立功能
    ├── 切换宠主端入口（跳转宠主端小程序）
    └── 聊天页面（wx.navigateTo，非 modal）
```

### 3.2 页面路由

| 路由路径|页面名称|描述|
|--------|--------|-----|
| `/pages/home/index` | 首页 | 抢单池 + 平台派单 + 今日任务 |
| `/pages/orders/index` | 订单管理 | 进行中/已完成/全部订单 |
| `/pages/income/index` | 收入中心 | 收入统计 + 提现 + 结算记录 |
| `/pages/my/index` | 个人中心 | 认证状态/帮手等级/设置 |

---

## 4. 功能模块详解

### 4.1 首页（首页 tab）

#### 4.1.1 页面结构

```
帮手端首页
├── 顶部问候区
│   ├── 头像 + 在线状态绿点
│   ├── 问候语（"王丽，早上好"）
│   ├── 小区信息（"阳光花城三期 · 已认证帮手"）
│   ├── 帮手等级徽章（"⭐ Lv.3 铜牌帮手"）
│   └── 接单状态切换（"接单中" / "暂停接单"）
├── 今日数据统计（3栏卡片）
│   ├── 今日任务数量（3单）
│   ├── 本周收入（¥186）
│   └── 综合评分（4.9）
├── 平台派单区（若有）
│   ├── 平台派单提示 Banner
│   │   ├── 派单类型图标
│   │   ├── 订单基本信息（服务类型 + 宠主 + 价格 + 时间）
│   │   └── 接单按钮
│   └── 拒绝按钮（可忽略）
├── 抢单池刷新倒计时
│   ├── 提示文案（"新订单池刷新倒计时"）
│   └── 倒计时（HH:MM格式）
└── 抢单池列表
    └── 抢单任务卡片（循环渲染）
        ├── 宠物信息区（头像emoji + 宠物名 + 品种 + 小区）
        ├── 紧急/距离标签
        ├── 服务标签（服务类型）
        ├── 订单信息网格（宠主 / 时间 / 时长 / 地址）
        ├── 价格展示
        └── 操作按钮（详情 + 立即抢）
```

#### 4.1.2 平台派单 Banner

- 仅当平台主动推送派单时显示
- 平台派单高亮边框（橙色渐变）
- 有"接单"和"忽略"两个操作
- 若15分钟不操作，自动消失

#### 4.1.3 抢单池刷新机制

- 订单池每4小时刷新一次（示例显示4:32小时后更新）
- 每次刷新，新释放的订单进入池中
- 帮手可在"筛选"中按服务类型、时间、距离筛选

#### 4.1.4 抢单任务卡片

| 字段 | 来源数据 |
|------|---------|
| 宠物头像 | 根据宠物类型映射 emoji（🐕🐱🎾🧹）|
| 紧急标签 | `urgent: true` 时显示红色"⚡ 紧急"标签 |
| 附近标签 | 距离 < 500m 时显示蓝色"📍 附近"标签 |
| 价格 | `price` 字段，格式"¥70 / 2小时" |

#### 4.1.5 API 接口

```
GET /api/helper/orders/pool
  Query: helperId, lat?, lng?, serviceType?, page?, pageSize?
  Response: { list: PoolOrder[], poolRefreshAt: string }

GET /api/helper/orders/assigned
  Query: helperId
  Response: { order: AssignedOrder | null }  // 平台派单

GET /api/helper/today-stats
  Query: helperId
  Response: { todayTasks: number, weekIncome: number, rating: number }
```

---

### 4.2 订单页（订单 tab）

#### 4.2.1 页面结构

```
我的订单页
├── 页面标题
├── 订单状态 Tab 栏
│   ├── 进行中
│   ├── 已完成
│   └── 全部
└── 订单列表
    └── 订单卡片
        ├── 服务类型图标 + 订单号
        ├── 服务状态标签（服务中 / 待上门 / 已完成）
        ├── 服务信息（宠物、日期时间、地址）
        ├── 收入金额（蓝色醒目）
        └── 操作按钮区（进行中：联系/导航；已完成：查看评价）
```

#### 4.2.2 订单状态

| 状态|含义|帮手可执行操作|
|-----|----|---------|
|`pending`|已被匹配，等待帮手确认|确认接单 / 拒绝|
|`accepted`|帮手已接单，等待服务开始|开始服务 / 取消|
|`ongoing`|服务进行中|确认完成|
|`completed`|服务已完成，等待结算|查看评价|
|`cancelled`|订单已取消|——|

#### 4.2.3 进行中订单操作

| 操作|触发行为|
|-----|--------|
|📞 联系宠主|调起微信电话或跳转聊天页面|
|💬 聊天|跳转聊天页面|
|📍 导航|调起微信地图导航到目的地|
|确认完成|打开完成确认，提交服务凭证（照片）|

#### 4.2.4 API 接口

```
GET /api/helper/orders
  Query: helperId, status?, page?, pageSize?
  Response: { list: HelperOrder[], total: number }

PUT /api/helper/orders/:id/status
  Body: { status: 'accepted' | 'ongoing' | 'completed' | 'cancelled' }
  Response: { success: boolean }

POST /api/helper/orders/:id/proof
  Body: FormData { photos: File[] }
  Response: { success: boolean }
```

---

### 4.3 收入页（收入 tab）

#### 4.3.1 页面结构

```
收入中心页
├── 可提现余额 Hero 区（深蓝渐变背景）
│   ├── 标签（"可提现余额"）
│   ├── 余额金额（超大字体，"¥128.50"）
│   ├── 结算周期说明（"每周一可提现上周收入"）
│   └── 操作按钮（立即提现 + 收支明细）
├── 收入统计卡片（2×2 网格）
│   ├── 本周收入（绿色数字）
│   ├── 本月收入（绿色数字）
│   ├── 累计收入
│   └── 累计完成订单（橙色数字）
├── 本期结算进度条
│   ├── 进度条（已结算百分比）
│   ├── 文字说明（"本周已完成 3/5 单，待结算 ¥156"）
│   └── 到账时间提示
├── 近期提现记录列表
│   ├── 提现金额
│   ├── 到账状态（已到账）
│   └── 提现时间
└── 结算周期说明（步骤卡片）
    ├── 步骤1：每周一0点自动结算
    ├── 步骤2：最低¥50，不满顺延下周
    └── 步骤3：1-3个工作日到账微信钱包
```

#### 4.3.2 提现弹窗（modal-withdraw）

```
提现弹窗
├── 标题（"提现到微信钱包"）
├── 可提现金额展示
│   ├── 标签 + 金额
│   └── 本周/本月收入参考
├── 快捷金额预设按钮（¥50 / ¥100 / 全部）
├── 自定义金额输入框
├── 温馨提示（结算规则说明）
└── 确认提现按钮
```

#### 4.3.3 API 接口

```
GET /api/helper/income/summary
  Query: helperId
  Response: {
    withdrawableBalance: number,
    weekIncome: number,
    monthIncome: number,
    totalIncome: number,
    totalOrders: number,
    settlementProgress: { completed: number, total: number, amount: number }
  }

GET /api/helper/income/withdraw-records
  Query: helperId, page?, pageSize?
  Response: { list: WithdrawRecord[] }

POST /api/helper/income/withdraw
  Body: { helperId: number, amount: number }
  Response: { withdrawId: string, status: 'pending' }

GET /api/helper/income/settlement-cycle
  Response: { currentWeekStart: string, currentWeekEnd: string, settleAt: string }
```

---

### 4.4 我的页（我的 tab）

#### 4.4.1 页面结构

```
我的页
├── 帮手资料头部（深蓝渐变背景）
│   ├── 头像（圆形，认证徽章右下角）
│   ├── 昵称 + 小区
│   └── 编辑资料按钮
├── 帮手等级卡片（浅金色背景）
│   ├── 等级图标
│   ├── 等级名称 + 进度说明
│   └── 星级展示
├── 切换宠主端 Banner（虚线边框）
│   ├── 宠主端图标
│   ├── 文案（"切换到宠主端 — 我有宠物需要服务"）
│   └── 箭头
├── 认证状态卡片（可点击）
│   ├── 认证图标 + 状态
│   ├── 认证有效期限
│   └── 认证进度条（已通过 = 100%）
├── 功能菜单
│   ├── 服务时间设置（⏰）
│   ├── 服务范围（📍）
│   ├── 我的评分（⭐）
│   ├── 资质证书（📜）
│   ├── 消息通知（🔔，带未读角标）
│   ├── 帮助与反馈（❓）
│   └── 关于我们（ℹ️）
└── 版本信息
```

#### 4.4.2 认证状态卡片交互

- 已认证状态：点击打开 `modal-cert`（认证详情弹窗）
- 待审核状态：点击查看认证进度
- 未认证状态：点击跳转认证申请流程

#### 4.4.3 服务时间设置弹窗（modal-servicetime）

```
服务时间设置弹窗
├── 标题
├── 提示文案
├── 工作日时间段设置
│   ├── 上午（8:00-12:00）开关
│   └── 下午（14:00-18:00）开关
├── 周末时间段设置
│   ├── 上午（9:00-12:00）开关
│   └── 下午（14:00-18:00）开关
├── 温馨提示
└── 保存设置按钮
```

- 默认全部开启
- 设置后，平台仅在设置时段内向该帮手推送订单

#### 4.4.4 API 接口

```
GET /api/helper/profile
  Query: helperId
  Response: HelperProfile

PUT /api/helper/profile
  Body: { nickname?, avatar?, bio?, serviceTypes? }
  Response: { success: boolean }

GET /api/helper/certification
  Query: helperId
  Response: { status, steps: CertificationStep[], validTo: string }

PUT /api/helper/service-time
  Body: {
    weekdayMorning: boolean,
    weekdayAfternoon: boolean,
    weekendMorning: boolean,
    weekendAfternoon: boolean
  }
  Response: { success: boolean }

PUT /api/helper/service-area
  Body: { communityIds: number[], maxDistance: number }
  Response: { success: boolean }
```

---

## 5. 数据模型

### 5.1 帮手（Helper）

```typescript
interface Helper {
  id: number;
  userId: number;           // 对应用户 ID
  name: string;
  avatar: string;
  rating: number;           // 4.0 - 5.0
  reviews: number;           // 评价总数
  services: ServiceType[];   // ['遛狗', '喂猫']
  community: string;
  communityId: number;
  distance?: string;         // 距离用户（若开启定位）
  experience: string;        // 从业经验
  isVerified: boolean;
  isStar: boolean;           // 明星帮手
  canUrgent: boolean;        // 可接紧急单
  bio: string;
  level: number;             // 1-5
  status: 'active' | 'inactive' | 'banned';
  acceptAssigned: boolean;   // 接受平台派单
  serviceTime: {
    weekdayMorning: boolean;
    weekdayAfternoon: boolean;
    weekendMorning: boolean;
    weekendAfternoon: boolean;
  };
  serviceArea: {
    communityIds: number[];
    maxDistance: number;     // 米
  };
  certification: {
    status: 'pending' | 'approved' | 'rejected';
    idCard: string[];
    experienceProof: string[];
    communityProof: string[];
    validTo: string;
  };
  createdAt: string;
}
```

### 5.2 抢单池订单（PoolOrder）

```typescript
interface PoolOrder {
  id: string;
  userId: number;
  petId: number;
  serviceType: 'walk' | 'feed' | 'play' | 'clean';
  date: string;
  timeSlot: string;
  address: string;
  price: number;
  budget?: number;
  urgent: boolean;
  distance?: string;
  pet: {
    name: string;
    type: 'dog' | 'cat';
    breed: string;
    icon: string;
  };
  owner: {
    name: string;
    avatar?: string;
  };
  poolExpiredAt: string;      // 释放时间
}
```

### 5.3 帮手订单（HelperOrder）

```typescript
interface HelperOrder {
  id: string;
  orderId: string;
  helperId: number;
  userId: number;
  serviceType: 'walk' | 'feed' | 'play' | 'clean';
  status: 'pending' | 'accepted' | 'ongoing' | 'completed' | 'cancelled';
  date: string;
  timeSlot: string;
  address: string;
  price: number;            // 帮手实际收入
  income: number;           // 平台扣除后收入
  rating?: number;
  review?: string;
  proofPhotos?: string[];
  startedAt?: string;
  completedAt?: string;
  createdAt: string;
}
```

### 5.4 收入记录（Income）

```typescript
interface IncomeSummary {
  withdrawableBalance: number;
  weekIncome: number;
  monthIncome: number;
  totalIncome: number;
  totalOrders: number;
  settlementProgress: {
    completed: number;       // 已结算单数
    total: number;           // 本周期总单数
    amount: number;          // 本周期待结算金额
  };
}

interface WithdrawRecord {
  id: string;
  amount: number;
  status: 'pending' | 'processing' | 'completed' | 'failed';
  wxTransactionId?: string;
  createdAt: string;
  processedAt?: string;
}
```

### 5.5 帮手认证（Certification）

```typescript
interface CertificationStep {
  id: 'identity' | 'experience' | 'community';
  name: string;
  status: 'pending' | 'approved' | 'rejected';
  proofUrls: string[];
  rejectedReason?: string;
}

interface Certification {
  helperId: number;
  status: 'pending' | 'approved' | 'rejected';
  steps: CertificationStep[];
  submittedAt: string;
  approvedAt?: string;
  validTo?: string;
}
```

---

## 6. API 接口汇总

### 6.1 认证

| 方法|路径|描述|
|----|----|-----|
| `GET` | `/api/helper/certification` | 获取认证状态 |
| `POST` | `/api/helper/certification/submit` | 提交认证材料 |
| `GET` | `/api/helper/certification/:step` | 获取单步认证详情 |

### 6.2 订单

| 方法|路径|描述|
|----|----|-----|
| `GET` | `/api/helper/orders/pool` | 获取抢单池 |
| `GET` | `/api/helper/orders/assigned` | 获取平台派单 |
| `POST` | `/api/helper/orders/:id/grab` | 抢单 |
| `PUT` | `/api/helper/orders/:id/status` | 更新订单状态 |
| `POST` | `/api/helper/orders/:id/proof` | 上传服务凭证 |
| `GET` | `/api/helper/orders` | 获取帮手订单列表 |

### 6.3 收入

| 方法|路径|描述|
|----|----|-----|
| `GET` | `/api/helper/income/summary` | 获取收入摘要 |
| `GET` | `/api/helper/income/records` | 获取收支记录 |
| `POST` | `/api/helper/income/withdraw` | 申请提现 |
| `GET` | `/api/helper/income/withdraw-records` | 获取提现记录 |
| `GET` | `/api/helper/income/settlement-cycle` | 获取结算周期信息 |

### 6.4 设置

| 方法|路径|描述|
|----|----|-----|
| `GET` | `/api/helper/profile` | 获取帮手资料 |
| `PUT` | `/api/helper/profile` | 更新帮手资料 |
| `PUT` | `/api/helper/service-time` | 更新服务时间 |
| `PUT` | `/api/helper/service-area` | 更新服务范围 |
| `PUT` | `/api/helper/accept-assigned` | 设置是否接受派单 |

---

## 7. 微信支付 - 提现

### 7.1 提现到账流程

```
用户申请提现
    ↓
后台校验（余额是否足够、是否满最低金额）
    ↓
创建微信支付企业付款到零钱工单
    ↓
微信支付处理（1-3个工作日）
    ↓
到账用户微信钱包
    ↓
推送提现到账通知（订阅消息）
```

### 7.2 提现限制

- 单次最低 ¥50
- 单次最高 ¥5000
- 每日最多提现 3 次
- 手续费：平台承担（0费率企业付款）

---

## 8. 消息通知

### 8.1 帮手通知场景

| 场景|模板|触发|
|-----|-----|-----|
|新平台派单|TM_XXXXX|有派单时推送|
|抢单成功通知|TM_XXXXX|抢单确认后推送|
|订单开始提醒|TM_XXXXX|服务前30分钟推送|
|订单取消通知|TM_XXXXX|订单被取消时推送|
|新评价通知|TM_XXXXX|用户评价后推送|
|提现到账通知|TM_XXXXX|提现成功到账后|
|认证审核结果|TM_XXXXX|认证审核完成时|
|收入到账通知|TM_XXXXX|每周一结算完成后|

### 8.2 WebSocket 实时通知

与宠主端共用同一 WebSocket 连接，但区分 `role: 'helper'`：

| 事件|描述|
|----|----|
|`new_order_assigned`|平台派发新订单|
|`order_grabbed`|有帮手抢了池中订单（更新池列表）|
|`pool_refreshed`|抢单池刷新 |
|`new_review`|收到新评价|

---

## 9. 风险控制

### 9.1 抢单风控

- 同一帮手每单完成时间不得少于15分钟（防止刷单）
- 连续3单被用户投诉，取消抢单资格7天
- 紧急单：需在10分钟内响应

### 9.2 收入风控

- 新认证帮手（前30天）收入上限 ¥2000/月
- 提现需人工复审（金额超过¥500）

### 9.3 认证风控

- 认证材料人工审核，不支持 AI 自动通过
- 发现虚假材料永久封禁账号

---

## 10. 项目结构

```
src/
├── pages/
│   ├── home/            # 首页（抢单/任务）
│   ├── orders/         # 订单管理
│   ├── income/         # 收入中心
│   └── my/             # 个人中心
├── components/
│   ├── OrderPoolCard/  # 抢单卡片
│   ├── OrderCard/      # 帮手订单卡片
│   ├── IncomeHero/     # 收入英雄区
│   ├── CertStatusCard/ # 认证状态卡片
│   └── ServiceTimePicker/
├── services/
│   ├── api.js
│   ├── order.js        # 抢单/订单服务
│   ├── income.js       # 收入服务
│   └── auth.js         # 认证服务
├── store/
│   └── helper.js       # Pinia - 帮手状态
└── utils/
    └── format.js
```
