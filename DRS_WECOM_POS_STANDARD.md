# drs-wecom-pos 项目开发规范

> ⚠️ **项目特定规范**：本文档仅适用于 `drs-wecom-pos` 项目（企业微信小程序）
>
> 通用前端开发规范请参考：[CODE_STANDARD.md](./CODE_STANDARD.md) | [WORKFLOW_STANDARD.md](./WORKFLOW_STANDARD.md)

---

## 🤖 AI 开发者指令

你是 `drs-wecom-pos` 项目的 AI 开发助手，必须：
1. 先阅读 [AI_DEV_RULES.md](./AI_DEV_RULES.md) 了解通用规范
2. **Figma 测量值必须 ×2**（375px 设计稿 → 750px 代码）
3. **优先使用 POS-UI 组件**，禁止直接使用 Taro 原始标签（View/Text/Button）
4. **所有样式文件必须使用 CSS Modules** (`*.module.scss`)
5. **默认字体已设置**：`font-size: 28px`（Figma 14px），无需重复声明

---

## 📚 技术栈

| 类别     | 技术选型                          |
|---------|----------------------------------|
| 框架     | Taro 4 (React 18)                |
| 语言     | TypeScript (TSX)                 |
| 样式     | SCSS Modules                     |
| UI 库    | POS-UI (基于 @antmjs/vantui)     |
| 状态管理 | React Hooks + PosGlobalContext   |
| 请求库   | ahooks (useRequest) + apiGet/apiPost |

---

## 🎨 Figma 像素转换规则（必须遵守）

### 转换公式
```
代码值 = Figma 测量值 × 2
```

### 原理
- Figma 设计稿基于 **375px** 宽度
- 小程序基于 **750px** 宽度（rpx 单位）
- Taro 自动将 `px` 转换为 `rpx`

### 常用转换表
| Figma | 代码  | 用途       |
|-------|------|-----------|
| 8px   | 16px | 小圆角     |
| 10px  | 20px | 间距       |
| 12px  | 24px | 辅助文字   |
| 14px  | 28px | **正文（默认，无需声明）** |
| 16px  | 32px | 标题       |
| 20px  | 40px | 卡片内边距 |
| 24px  | 48px | 大标题     |
| 180px | 360px| 固定宽高   |

### 字体默认值（无需声明）
```scss
// ✅ 全局已设置，无需重复声明
font-size: 28px;        // 对应 Figma 14px
font-family: [默认字体]; // 全局默认

// ❌ 错误：重复声明默认值
.text {
  font-size: 28px;      // 不需要！
  font-family: xxx;     // 不需要！
}

// ✅ 正确：仅在非默认值时声明
.title {
  font-size: 32px;      // Figma 16px，需要声明
}

.small-text {
  font-size: 24px;      // Figma 12px，需要声明
}
```

---

## 🧩 POS-UI 组件使用规范

### 强制要求
❌ **禁止**直接使用 Taro 原始标签（View, Text, Button）
✅ **必须**优先从 `src/components/POS-UI` 导入封装组件

### 完整组件清单

#### 布局 & 导航
```typescript
import CustomHeader from '@/components/CustomHeader';           // 自定义导航栏
import { POSTabs, POSTab } from '@/components/POS-UI/POSTabs'; // 标签页
```

#### 基础组件
```typescript
import POSButton from '@/components/POS-UI/POSButton';         // 按钮
import { POSCellGroup, POSCell } from '@/components/POS-UI/POSCell'; // 列表单元格
import POSTag from '@/components/POS-UI/POSTag';               // 标签
import POSDivider from '@/components/POS-UI/POSDivider';       // 分割线
import POSImage from '@/components/POS-UI/POSImage';           // 图片（支持懒加载）
```

#### 反馈组件
```typescript
import POSDialog from '@/components/POS-UI/POSDialog';         // 弹窗
import POSToast from '@/components/POS-UI/POSToast';           // 轻提示
import POSLoading from '@/components/POS-UI/POSLoading';       // 加载状态
import POSEmpty from '@/components/POS-UI/POSEmpty';           // 空状态占位
import POSNoticeBar from '@/components/POS-UI/POSNoticeBar';   // 通知栏
import POSSkeleton from '@/components/POS-UI/POSSkeleton';     // 骨架屏
```

#### 表单组件
```typescript
import POSForm from '@/components/POS-UI/POSForm';             // 表单容器
import POSField from '@/components/POS-UI/POSField';           // 表单字段
import POSInput from '@/components/POS-UI/POSInput';           // 输入框
import POSPicker from '@/components/POS-UI/POSPicker';         // 选择器
import POSSwitch from '@/components/POS-UI/POSSwitch';         // 开关
import POSCheckbox from '@/components/POS-UI/POSCheckbox';     // 复选框
import POSRadio from '@/components/POS-UI/POSRadio';           // 单选框
import POSCalendar from '@/components/POS-UI/POSCalendar';     // 日历
import POSUploader from '@/components/POS-UI/POSUploader';     // 文件上传
import POSSearch from '@/components/POS-UI/POSSearch';         // 搜索框
```

#### 容器组件
```typescript
import POSPopup from '@/components/POS-UI/POSPopup';           // 弹出层
import POSOverlay from '@/components/POS-UI/POSOverlay';       // 遮罩层
import POSActionSheet from '@/components/POS-UI/POSActionSheet'; // 动作面板
import POSDropdown from '@/components/POS-UI/POSDropdown';     // 下拉菜单
```

#### 展示组件
```typescript
import POSTable from '@/components/POS-UI/POSTable';           // 简单表格
import POSCountDown from '@/components/POS-UI/POSCountDown';   // 倒计时
```

#### 高级组件
```typescript
import POSInfiniteScroll from '@/components/POS-UI/POSInfiniteScroll'; // 无限滚动
import POSPullToRefresh from '@/components/POS-UI/POSPullToRefresh';   // 下拉刷新
import POSVirtualList from '@/components/POS-UI/POSVirtualList';       // 虚拟列表
```

#### 业务组件
```typescript
import NoticeHeader from '@/components/NoticeHeader';          // 通知头部
import LeaveInterception from '@/components/LeaveInterception'; // 离开拦截
import SelectBpId from '@/components/SelectBpId';              // 客户选择器
import ListComponent from '@/components/ListComponent';        // 通用列表组件
```

---

## 🎨 样式规范

### CSS Modules 强制使用
```scss
// ✅ 必须：*.module.scss
// src/pages/xxx/index.module.scss
@use '_styles/theme' as *;

.page {
  background: $background-page;
  padding: 40px; // Figma 20px × 2
}
```

### 主题变量速查表
```scss
// 背景色
$background-page: #ffffff;        // 页面背景
$background-card: #eeeff2;        // 卡片背景
$background-component: #010205;   // 组件背景（黑色主调）
$background-color-other: #f7f7f7; // 次要背景

// 文字颜色
$text-primary: #010205;           // 主要文字
$text-secondary: #535457;         // 次要文字
$text-disabled: #949598;          // 禁用文字
$text-inverse: #ffffff;           // 反色文字
$text-link: #2762ec;              // 链接文字

// 边框 & 分割线
$border-primary: #010205;         // 主要边框
$border-secondary: #d3d3d7;       // 次要边框
$divider: #eeeff2;                // 分割线

// 功能色
$functional-error: #cc1922;       // 错误
$functional-warning: #d97300;     // 警告
$functional-success: #197e10;     // 成功
$functional-info: #2762ec;        // 信息

// 功能色背景
$functional-error-background: #ffe2e4;
$functional-warning-background: #fff6eb;
$functional-success-background: #e4ffec;
$functional-info-background: #eef3ff;

// 遮罩
$cover-mask: rgba(36, 37, 40, 0.6);
$cover-frosted-glass: rgba(1, 2, 5, 0.33);
```

### CSS 命名规范
```scss
// ✅ 正确：kebab-case（小写+横杠）
.user-profile-card { }
.list-item-wrapper { }
.button-primary { }

// ❌ 错误：禁止下划线和驼峰
.user_profile_card { }  // 禁止下划线
.listItemWrapper { }    // 禁止驼峰
.UserProfileCard { }    // 禁止大驼峰
```

### 注释原则
```scss
// ❌ 错误：过度注释
.container {
  display: flex;         // 使用 flex 布局
  flex-direction: column; // 纵向排列
  padding: 40px;         // 内边距 40px
}

// ✅ 正确：非必要不注释，复杂逻辑从输入输出角度说明
.container {
  display: flex;
  flex-direction: column;
  padding: 40px;
}

// ✅ 正确：复杂计算或业务逻辑才需要注释
.special-layout {
  // 根据设计稿要求：左侧固定 240px，右侧自适应，间距 32px
  width: calc(100% - 240px - 32px);
}
```

---

## 📁 目录结构规范

### 主包页面结构
```
src/pages/[模块]/[页面]/
├── index.tsx                      # 主容器（数据获取、核心布局）
├── index.module.scss              # 样式文件（必须使用 theme 变量）
├── index.config.ts                # Taro 页面配置
├── interface.ts                   # 类型定义（可选）
├── constants.ts                   # 页面常量（可选）
├── use[PageName]Data.ts           # 核心数据 Hook（复杂逻辑必须）
└── components/                    # 页面级局部组件（>50 行必须拆分）
    ├── [ComponentA]/
    │   ├── index.tsx
    │   └── index.module.scss
    └── [ComponentB]/
        └── index.tsx
```

### 分包结构
```
src/packages/[分包名]/
├── pages/                         # 分包页面
│   └── [页面名]/
├── services/                      # 分包专用 API
│   ├── index.ts
│   └── interface.ts
└── components/                    # 分包共享组件（可选）
```

---

## 🪝 use[PageName]Data Hook 模式

### 标准模板（精简版）
```typescript
// src/pages/xxx/useXxxData.ts
import { useState, useCallback } from 'react';
import { useDidShow } from '@tarojs/taro';
import { useRequest } from 'ahooks';
import { getXxxList } from '@/services/xxx';

export const useXxxData = () => {
  const [listData, setListData] = useState([]);
  const { loading, runAsync } = useRequest(getXxxList, { manual: true });

  const initPageData = useCallback(async () => {
    const res = await runAsync();
    setListData(res?.data || []);
  }, []);

  // 页面显示时刷新数据
  useDidShow(() => {
    initPageData();
  });

  return { loading, listData, initPageData };
};
```

### 关键 API
- `useRequest(api, { manual: true })` - ahooks 请求封装
- `useDidShow()` - 页面显示时触发（Taro 生命周期）
- `useRouter()` - 获取路由参数

---

## 🌐 API 服务层规范

### 标准 API 定义
```typescript
// src/services/[模块]/index.ts
import { SERVICES } from '@/utils/env';
import { apiGet, apiPost, apiPut, apiDelete, uploadApi } from '@/utils/request';
import { IXxxParams, IXxxResponse } from './interface';

// GET 请求
export const getXxxList = (params?: Partial<IXxxParams>) => {
  return apiGet<IXxxResponse[]>(`${SERVICES.Customer()}/xxx/list`, params);
};

// POST 请求
export const createXxx = (params: IXxxParams) => {
  return apiPost<IXxxParams, IXxxResponse>(`${SERVICES.Customer()}/xxx/create`, params);
};

// PUT 请求
export const updateXxx = (id: number, params: IXxxParams) => {
  return apiPut<IXxxParams, boolean>(`${SERVICES.Customer()}/xxx/update/${id}`, params);
};

// DELETE 请求
export const deleteXxx = (id: number) => {
  return apiDelete<{ id: number }, boolean>(`${SERVICES.Customer()}/xxx/delete/${id}`);
};

// 上传文件
export const uploadFile = (filePath: string, formData: Record<string, unknown>) => {
  return uploadApi<IUploadResponse>({
    url: `${SERVICES.Customer()}/xxx/upload`,
    formData,
    filePath,
    name: 'file',
  });
};
```

---

## 🛤️ 路由管理规范

### 页面注册流程
```typescript
// src/utils/router.ts

// 1. 在 E_PAGE_NAME 枚举中注册
export enum E_PAGE_NAME {
  MY_NEW_PAGE = 'myNewPage',
}

// 2. 在 ROUTER 数组中添加配置
export const ROUTER: IRouter[] = [
  // 主包页面
  {
    pagePath: 'pages/myModule/myPage/index',
    roles: [ROLES.ROLE_SALES_CONSULTANT],
    configureRoles: ROUTER_KEY.WECHAT_XXX,
    text: '页面标题',
    pageName: E_PAGE_NAME.MY_NEW_PAGE,
  },

  // 分包页面
  {
    subPackageName: 'packages/myPackage',
    isSubPackages: true,
    pagePath: 'pages/myPage/index',
    configureRoles: ROUTER_KEY.MY_PACKAGE,
    text: '分包页面',
    pageName: E_PAGE_NAME.MY_NEW_PAGE,
  },
];
```

---

## 🌍 全局 Context 使用

### PosGlobalContext 可用状态
```typescript
import { useContext } from 'react';
import { PosGlobalContext } from '@/context/PosGlobalContext';

const {
  // TabBar 相关
  tabBarSelected,
  setTabBarSelected,
  isShowTabBar,

  // 侧边栏工具相关
  isTools,          // 是否为侧边栏入口
  toolsEntry,       // 入口类型
  toolsEUserId,     // 外部用户 ID

  // 业务状态
  unreadPreOrder,
  setUnreadPreOrder,

  // WebSocket
  testDriveSignFinish,
} = useContext(PosGlobalContext);
```

---

## ⚙️ Taro 页面配置

### 标准配置模板
```typescript
// src/pages/xxx/index.config.ts
export default definePageConfig({
  navigationStyle: 'custom',        // 使用 CustomHeader
  enablePullDownRefresh: false,     // 是否启用下拉刷新
});
```

---

## 📝 开发日志规范

### 日志文件位置
`docs/DEVELOPMENT_LOG.md`

### 格式模板
```markdown
# 开发日志 - YYYY-MM-DD

## 任务目标
[简要描述本次开发的目标]

## 已完成工作

### 1. [功能/模块名称]
- **组件路径**：`src/xxx/xxx`
- **功能描述**：
  - [功能点 1]
  - [功能点 2]
- **技术点**：
  - [关键技术决策或实现细节]

## 待办事项
- [ ] [后续需要完成的工作]
```

### 日志要求
- 每次开发任务完成后必须更新
- 新日志添加在文件顶部（向上追加）
- 记录完整的文件路径
- 记录关键的技术选型或实现思路

---

## ✅ 质量检查清单

### 编码前
- [ ] Figma 测量值是否 ×2？
- [ ] 是否使用 POS-UI 组件（禁止 View/Text/Button）？
- [ ] TS 类型定义是否完成？
- [ ] 是否需要使用 use[PageName]Data Hook？

### 编码中
- [ ] 样式文件是否使用 `*.module.scss`？
- [ ] 是否引入 `@use '_styles/theme' as *`？
- [ ] CSS 命名是否使用 kebab-case？
- [ ] 是否避免声明默认字体（28px）？
- [ ] 复杂逻辑（>50 行）是否拆分至 Hook 或子组件？
- [ ] 是否避免过度注释（非必要不注释）？

### 提交前
- [ ] 页面是否在 `router.ts` 中注册（E_PAGE_NAME + ROUTER）？
- [ ] API 请求是否使用 `useRequest` 封装？
- [ ] 是否更新 `docs/DEVELOPMENT_LOG.md`？
- [ ] ESLint 检查是否通过？
- [ ] 是否正确处理 loading、empty、error 状态？

---

## 📚 参考资料

### 通用规范（必读）
- [CODE_STANDARD.md](./CODE_STANDARD.md) - 代码规范
- [WORKFLOW_STANDARD.md](./WORKFLOW_STANDARD.md) - 工作流规范
- [AI_DEV_RULES.md](./AI_DEV_RULES.md) - AI 开发者速查手册

### 官方文档
- Taro 文档：https://taro-docs.jd.com/
- @antmjs/vantui 文档：https://antmjs.github.io/vantui/
- React Hooks 文档：https://react.dev/reference/react/hooks
- ahooks 文档：https://ahooks.js.org/

---

**最后更新**：2026-01-11 | 基于项目版本 `drs-wecom-pos`
