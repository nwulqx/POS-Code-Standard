# POS 前端开发规范

> **高密度、高价值的前端工程规范**，适用于人类开发者和 AI 开发助手

---

## 📚 文档结构

本规范由 **3 个核心文档** 组成：

| 文档 | 适用对象 | 特点 | 阅读时长 |
|------|----------|------|----------|
| [CODE_STANDARD.md](./CODE_STANDARD.md) | 开发者（人类 + AI） | 代码规范，详细示例 | 15 分钟 |
| [WORKFLOW_STANDARD.md](./WORKFLOW_STANDARD.md) | 团队协作 | 工作流程、Git、CR | 12 分钟 |
| [AI_DEV_RULES.md](./AI_DEV_RULES.md) | AI 开发助手 | 超高密度速查手册 | 5 分钟 |

---

## 🎯 核心理念

```typescript
组件化 + 纯函数 + 类型先行 + 渐进式重构 + ROI 驱动
```

### 1. 组件化与纯函数
- 大问题递归拆解为小问题
- 即使不复用也要拆分（单一职责）
- 同一输入 → 唯一输出，不修改输入

### 2. TypeScript 定义先行
- 禁止 `any`，必须明确类型
- 接口/API 对接前先定义 TS 类型
- 数据流清晰，便于维护

### 3. 渐进式重构
- 事不过三，三则重构
- 小步快跑，避免大规模重写
- 每次提交是完整逻辑单元

### 4. 工具服务实际（ROI 原则）
- 技术不是越新越好
- 工具引入需评估：ROI > 2，痛点 ≥ 7
- 学习成本 + 集成成本 + 维护成本

---

## 🚀 快速开始

### 对于开发者

1. **阅读核心规范**（必须）
   ```bash
   # 代码规范（必读）
   cat CODE_STANDARD.md

   # 工作流规范（必读）
   cat WORKFLOW_STANDARD.md
   ```

2. **配置开发环境**
   ```bash
   # 安装依赖
   npm install -D eslint prettier husky lint-staged @commitlint/cli

   # 初始化 Husky
   npx husky install

   # 复制配置文件（见下方配置示例）
   ```

3. **开始开发**
   - 理解需求 → TS 类型定义 → 编码 → 自测 → 提交 MR

### 对于 AI 开发助手

1. **加载精简规则**
   ```bash
   # 将 AI_DEV_RULES.md 作为 System Prompt 或上下文
   cat AI_DEV_RULES.md
   ```

2. **遵循黄金准则**
   - 类型优先，拒绝 `any`
   - 纯函数思维
   - >50 行必拆分
   - 主动质疑不规范代码

---

## 📋 黄金准则速览

### 代码规范
```typescript
✅ TS 类型优先，禁止 any
✅ 纯函数：不修改输入
✅ 组件拆分：>50 行必拆
✅ Hooks 谨慎：useMemo/useCallback 非银弹
✅ 代码质量：参数 ≤3，分支 ≤4，嵌套 ≤2
✅ 错误处理：禁止空 catch
```

### 工作流规范
```typescript
✅ Git: Conventional Commits，小步提交
✅ Code Review: 24 小时响应，必须本地测试，第二责任人
✅ 测试: ESLint 通过，自测覆盖所有分支
✅ 工具引入: ROI > 2，痛点 ≥7
✅ 重构: 事不过三，三则重构
```

---

## 🔧 配置文件示例

### ESLint 配置
```javascript
// .eslintrc.js
module.exports = {
  extends: [
    'airbnb',
    'airbnb-typescript',
    'plugin:@typescript-eslint/recommended',
  ],
  parserOptions: {
    project: './tsconfig.json',
  },
  rules: {
    '@typescript-eslint/no-explicit-any': 'error', // 禁止 any
    'react/function-component-definition': 'off',
    'react-hooks/exhaustive-deps': 'warn',
    'max-lines-per-function': ['warn', { max: 50 }], // 函数最多 50 行
  },
};
```

### Prettier 配置
```json
// .prettierrc
{
  "singleQuote": true,
  "trailingComma": "all",
  "printWidth": 80,
  "tabWidth": 2,
  "semi": true
}
```

### Husky + lint-staged
```json
// package.json
{
  "husky": {
    "hooks": {
      "pre-commit": "lint-staged",
      "commit-msg": "commitlint -E HUSKY_GIT_PARAMS"
    }
  },
  "lint-staged": {
    "*.{ts,tsx}": [
      "eslint --fix",
      "prettier --write"
    ]
  }
}
```

### Commitlint 配置
```javascript
// commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
  rules: {
    'type-enum': [2, 'always', [
      'feat', 'fix', 'refactor', 'docs', 'style', 'test', 'chore'
    ]],
  },
};
```

---

## 💡 使用建议

### 对于团队 Leader
1. **新人 Onboarding**：
   - Day 1: 阅读 CODE_STANDARD.md（1 小时）
   - Day 2: 阅读 WORKFLOW_STANDARD.md（1 小时）
   - Day 3: 配置开发环境，提交第一个 MR

2. **Code Review**：
   - 使用 CODE_STANDARD.md 作为 CR Checklist
   - 发现问题引用具体章节（如"违反 3.3 useMemo 原则"）

3. **技术选型**：
   - 使用 WORKFLOW_STANDARD.md § 11 的 ROI 框架评估

### 对于开发者
1. **日常开发**：
   - 编码前：过一遍 AI_DEV_RULES.md 的检查清单
   - 提交前：运行 ESLint + 自测

2. **遇到问题**：
   - 先查 AI_DEV_RULES.md 的"高频问题速查"
   - 再查对应的详细文档

### 对于 AI 开发助手
1. **上下文加载**：
   ```bash
   # 优先级 1: AI_DEV_RULES.md（必须）
   # 优先级 2: CODE_STANDARD.md（推荐）
   # 优先级 3: WORKFLOW_STANDARD.md（可选）
   ```

2. **主动行为**：
   - 发现不规范代码 → 主动提醒
   - 建议优化方案 → 解释原因
   - 重构代码 → 渐进式，小步提交

---

## 🌟 规范的价值

> "规范不是束缚，而是让团队高效协作的共识"

### 对个人
- ✅ 代码质量更高，Bug 更少
- ✅ 维护成本更低，改起来更快
- ✅ 职业能力提升，工程素养增强

### 对团队
- ✅ 协作更顺畅，沟通成本更低
- ✅ 代码风格统一，像一个人写的
- ✅ CR 更高效，质量更可控

### 对项目
- ✅ 技术债更少，可持续发展
- ✅ 新人上手更快，知识传递更容易
- ✅ 交付质量更高，用户体验更好

---

## 📞 联系与贡献

- **文档维护者**：POS 前端团队
- **问题反馈**：提交 Issue 或联系技术负责人
- **改进提案**：发起 MR 或团队评审会

---

**最后的最后**：写出让 6 个月后的自己和团队成员都能看懂的代码 🚀
