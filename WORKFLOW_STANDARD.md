# 行为规范 (Workflow Standard)

> **核心理念**：小步快跑、责任共担、主动沟通、工具服务实际

---

## 🤖 AI 开发者指令

作为 AI 开发助手，你必须：
1. 理解需求后先输出 TS 类型定义方案
2. 提交 MR 前主动运行测试和 ESLint
3. 发现不符合规范的代码时主动提出
4. 重构时保持小步提交，便于 Review
5. 遇到技术选型时，基于 ROI 原则评估

---

## 1. 开发流程

### 1.1 标准流程
```
1. 理解需求（不明确时先澄清）
   ↓
2. TS 类型定义（接口优先）
   ↓
3. 组件/函数拆分设计
   ↓
4. 编码实现（遵循代码规范）
   ↓
5. 自测 + 单元测试
   ↓
6. 提交 MR + Code Review
   ↓
7. 合并后集成测试
```

### 1.2 需求澄清原则
```typescript
// ❌ 错误：盲目开发
收到需求 → 立即编码 → 发现理解偏差 → 返工

// ✅ 正确：澄清后开发
收到需求 → 确认细节 → TS 类型定义 → 编码
```

**15 分钟原则**：
- 需求不明确超过 15 分钟 → 主动找产品/业务确认
- 技术问题卡住超过 15 分钟 → 主动找团队成员求助

---

## 2. Git 工作流

### 2.1 分支规范
```bash
main              # 生产分支（受保护）
  ├── develop     # 开发分支
      ├── feature/CARD-123-add-login    # 功能分支
      ├── feature/CARD-456-user-profile
      └── fix/CARD-789-form-validation  # 修复分支
```

**命名规则**：
- `feature/<CARD-ID>-<description>`: 新功能
- `fix/<CARD-ID>-<description>`: Bug 修复
- `refactor/<CARD-ID>-<description>`: 重构
- `hotfix/<CARD-ID>-<description>`: 紧急修复

### 2.2 提交规范（Conventional Commits）
```bash
# 格式
<type>(<scope>): <subject>

# 示例
feat(CARD-123): 添加用户登录功能
fix(CARD-456): 修复表单验证逻辑
refactor(CARD-789): 重构订单计算模块
docs: 更新 README 安装步骤
style: 修复 ESLint 警告
test(CARD-321): 添加用户服务单元测试
chore: 升级 React 到 18.3.0
```

**Type 类型**：
- `feat`: 新功能
- `fix`: Bug 修复
- `refactor`: 重构（不改变外部行为）
- `docs`: 文档更新
- `style`: 代码格式（不影响逻辑）
- `test`: 测试相关
- `chore`: 构建/工具/依赖更新

### 2.3 提交频率
```typescript
// ✅ 推荐节奏
每天至少 1 次提交
单次提交不超过 3 天工作量

// ❌ 避免
1 周才提交 1 次（风险高，难以 Review）
1 天提交 20 次（碎片化，缺乏逻辑）
```

**原则**：小步快跑，每个提交是一个完整的逻辑单元

### 2.4 提交前检查（Husky + lint-staged）
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

**自动检查**：
- ✅ ESLint 检查通过
- ✅ Prettier 格式化
- ✅ TS 类型检查无错误
- ✅ 提交信息符合规范

---

## 3. Code Review 规范

### 3.1 核心原则

#### 1. 对代码不对人
```typescript
// ❌ 错误评论
"你这代码写得太烂了"
"为什么要这样写？有问题吧"

// ✅ 正确评论
"这里使用 useMemo 可能带来的收益小于开销，建议移除"
"建议将这个函数拆分为两个，便于测试和维护"
```

#### 2. CR 与开发同级重要
```
开发时间 = 编码时间 + CR 时间
CR 不是"额外工作"，是开发流程的一部分
```

#### 3. CR 通过者是第二责任人
```
提交者：第一责任人
Review 通过者：第二责任人（共担责任）

→ 如果线上出 Bug，两人共同负责
```

#### 4. 垃圾代码不允许合并
```typescript
// 即使有 deadline 压力，也不能合并低质量代码
// 技术债会越积越多，最终拖垮项目

// ✅ 正确做法
1. 发现代码质量问题 → 提出修改意见
2. 必须解决后才能合并
3. 如果时间紧急 → 缩小功能范围，而非降低质量
```

### 3.2 Review 流程

#### 1. 及时 Review（24 小时内）
```bash
# 收到 MR 通知
→ 24 小时内完成 Review
→ 如果忙不过来，至少回复预计时间
```

#### 2. 必须本地测试
```bash
# ❌ 错误：只看代码
git diff → 看完就 LGTM

# ✅ 正确：本地运行测试
git checkout feature/CARD-123
npm install
npm run dev
# 手动测试功能
npm run test
# 确认无问题后 LGTM
```

#### 3. 提出问题必须解决
```typescript
// CR 意见分级
🔴 Blocker：必须修改（阻塞合并）
🟡 Major：建议修改（最好改）
🟢 Minor：可选修改（提升代码质量）

// 所有 Blocker 必须解决后才能合并
```

#### 4. 最后一步：LGTM
```bash
# 所有问题解决后，评论：
LGTM (Looks Good To Me)

# 或更友好的表达
✅ Approved! Great work on this feature!
```

### 3.3 Review 检查清单

```markdown
### 功能性
- [ ] 功能是否符合需求？
- [ ] 边界情况是否处理？
- [ ] 错误处理是否完善？

### 代码质量
- [ ] 是否遵循代码规范（CODE_STANDARD.md）？
- [ ] TS 类型是否完整（无 `any`）？
- [ ] 函数是否满足单一职责？
- [ ] 是否有明显的性能问题？
- [ ] 是否有不必要的 useMemo/useCallback？

### 安全性
- [ ] 是否有 XSS 风险？
- [ ] 是否有 SQL 注入风险？
- [ ] 敏感信息是否加密/隐藏？

### 测试
- [ ] ESLint 检查是否通过？
- [ ] 本地测试是否通过？
- [ ] 是否有单元测试（公共函数）？

### 依赖管理
- [ ] 是否引入了不必要的依赖？
- [ ] 新依赖是否经过团队评审？

### 可维护性
- [ ] 代码是否易于理解？
- [ ] 是否有必要的注释（复杂逻辑）？
- [ ] 是否有技术债（需要后续优化）？
```

---

## 4. 测试规范

### 4.1 必须项
```bash
# 提交前必须通过
✅ ESLint 检查
✅ 自测覆盖所有分支逻辑（if/else、try/catch）
```

### 4.2 推荐项
```typescript
// 公共函数/工具函数：单元测试
// utils/calculateTotal.test.ts
import { calculateTotal } from './calculateTotal';

describe('calculateTotal', () => {
  it('should calculate total with tax', () => {
    const orders = [
      { price: 100, quantity: 2 },
      { price: 50, quantity: 1 },
    ];
    expect(calculateTotal(orders)).toBe(275); // (200 + 50) * 1.1
  });
});

// 单元测试覆盖率 ≥ 10%（公共函数）
```

### 4.3 关键业务逻辑：集成测试
```typescript
// 示例：用户登录流程集成测试
describe('User Login Flow', () => {
  it('should login successfully with valid credentials', async () => {
    const { getByLabelText, getByText } = render(<LoginForm />);

    fireEvent.change(getByLabelText('Email'), {
      target: { value: 'test@example.com' },
    });
    fireEvent.change(getByLabelText('Password'), {
      target: { value: 'password123' },
    });
    fireEvent.click(getByText('Login'));

    await waitFor(() => {
      expect(window.location.pathname).toBe('/dashboard');
    });
  });
});
```

---

## 5. 依赖管理

### 5.1 定期审查
```bash
# 每季度一次依赖审查
npm outdated           # 查看过期依赖
npm audit              # 检查安全漏洞
npm audit fix          # 自动修复安全问题
```

### 5.2 新增依赖评审
```typescript
// 引入新依赖前，团队评审
评估维度：
1. 是否解决实际问题？（ROI 原则）
2. Bundle Size 多大？（影响加载速度）
3. 社区活跃度如何？（GitHub Stars/Issues）
4. 是否有更轻量的替代方案？

// 示例：日期处理库选择
❌ moment.js (2.29.4): 329 KB（太大）
✅ date-fns (2.30.0): 78 KB（推荐）
✅ dayjs (1.11.9): 6.5 KB（更轻量）
```

### 5.3 移除未使用的依赖
```bash
# 使用工具检测
npx depcheck

# 移除未使用的包
npm uninstall <package-name>
```

---

## 6. 文档规范

### 6.1 代码注释
```typescript
// ✅ 关键业务逻辑注释
/**
 * 计算订单总价
 *
 * 规则：
 * 1. 商品价格 * 数量
 * 2. 加 10% 税费
 * 3. 减去会员折扣（如果有）
 *
 * @param order - 订单对象
 * @returns 最终价格
 */
function calculateOrderTotal(order: Order): number {
  // ...
}
```

### 6.2 Confluence 文档
```markdown
# 重要组件/架构决策记录

## 场景
- 技术选型决策
- 重要架构设计
- 复杂业务逻辑

## 内容
1. 背景/问题
2. 决策方案
3. 备选方案（为什么不选）
4. 实施细节
```

### 6.3 README.md
```markdown
# 项目名称

## 简介
简要描述项目功能

## 技术栈
- React 18.3.0
- TypeScript 5.2.0
- Vite 4.5.0

## 安装
npm install

## 运行
npm run dev

## 构建
npm run build

## 测试
npm run test
```

---

## 7. 沟通协作

### 7.1 需求澄清
```typescript
// ❌ 错误：盲目开发
收到需求 → 立即动手 → 做错方向 → 浪费时间

// ✅ 正确：先澄清
收到需求 → 提出疑问 → 确认细节 → 开发

// 示例：接到需求"添加用户搜索"
问：搜索范围是全部用户还是当前组织？
问：是实时搜索还是点击搜索？
问：支持哪些字段搜索（姓名/邮箱/手机号）？
```

### 7.2 重构沟通
```typescript
// ✅ 重构前必须沟通
1. 发现需要重构的代码
2. 评估重构范围和风险
3. 准备重构方案（多个选项）
4. 团队评审方案
5. 获得共识后执行

// ❌ 不要突然提交大规模重构
直接提交 500+ 行改动 → CR 难度大 → 容易引入 Bug
```

### 7.3 技术选型沟通
```markdown
# 技术选型提案模板

## 背景
我们需要实现 [功能]，当前方案存在 [问题]

## 方案对比
### 方案 A: [名称]
- 优点：...
- 缺点：...
- 成本：学习成本 X 天，维护成本 Y

### 方案 B: [名称]
- 优点：...
- 缺点：...
- 成本：...

## 推荐方案
方案 A，理由：[ROI 分析]

## 风险
1. ...
2. ...

## 需要团队确认
- [ ] 技术负责人审批
- [ ] 团队成员投票
```

### 7.4 15 分钟求助原则
```bash
卡在技术问题超过 15 分钟 → 立即求助

# 求助格式
问题：[简要描述]
已尝试：[列出尝试的方案]
期望：[期望结果]
相关代码：[代码片段或链接]
```

---

## 8. 工具与自动化

### 8.1 ESLint + Prettier
```javascript
// .eslintrc.js
module.exports = {
  extends: [
    'airbnb',
    'airbnb-typescript',
    'plugin:@typescript-eslint/recommended',
  ],
  rules: {
    '@typescript-eslint/no-explicit-any': 'error', // 禁止 any
    'react/function-component-definition': 'off',
    'react-hooks/exhaustive-deps': 'warn',
  },
};
```

### 8.2 Husky + lint-staged
```bash
# 安装
npm install -D husky lint-staged

# 初始化
npx husky install

# 添加 pre-commit hook
npx husky add .husky/pre-commit "npx lint-staged"
```

### 8.3 Commitlint
```bash
# 安装
npm install -D @commitlint/cli @commitlint/config-conventional

# commitlint.config.js
module.exports = {
  extends: ['@commitlint/config-conventional'],
};

# 添加 commit-msg hook
npx husky add .husky/commit-msg 'npx --no -- commitlint --edit "$1"'
```

---

## 9. 应急处理

### 9.1 线上 Bug 紧急修复
```bash
# 1. 从 main 拉取 hotfix 分支
git checkout main
git pull
git checkout -b hotfix/CARD-XXX-critical-bug

# 2. 最小化修复（只改必要的）
# 3. 本地测试
# 4. 提交 MR（可以快速 Review，1 小时内）
# 5. 合并后立即发布
# 6. 验证修复效果
# 7. 同步到 develop 分支
```

### 9.2 回滚流程
```bash
# 发现线上问题严重
git revert <commit-hash>  # 回滚提交
# 或
git checkout <previous-tag>  # 回滚到上一版本
```

---

## 10. 团队文化

### 10.1 持续学习
```markdown
# 每周技术分享
- 轮流分享新技术/踩过的坑
- 时长：15-30 分钟
- 形式：代码演示 + 讨论
```

### 10.2 知识沉淀
```markdown
# Confluence 知识库
- 技术选型决策
- 踩坑记录
- 最佳实践
- 架构设计
```

### 10.3 Code Review 是学习机会
```typescript
// CR 不仅是检查错误，更是：
1. 学习他人的代码风格
2. 讨论更好的实现方式
3. 统一团队编码习惯
4. 知识传递（新人 ← 老人）
```

---

## 11. 工程化工具选择哲学

### 11.1 核心原则
```
技术服务于实际，不盲目追新
工具本身有成本，需权衡 ROI
```

### 11.2 决策框架
```typescript
interface ToolEvaluation {
  // 1. 问题定义
  problem: string;              // 解决什么问题？
  currentPain: number;          // 当前痛点严重程度（1-10）

  // 2. 方案评估
  solution: string;             // 工具/技术名称
  benefits: string[];           // 带来的收益
  costs: {
    learning: number;           // 学习成本（人天）
    integration: number;        // 集成成本（人天）
    maintenance: number;        // 维护成本（人天/年）
  };

  // 3. ROI 计算
  roi: number;                  // benefits / costs
}

// 决策规则
const shouldAdoptTool = (evaluation: ToolEvaluation) => {
  return (
    evaluation.currentPain >= 7 &&    // 痛点足够严重
    evaluation.roi > 2 &&              // ROI > 2（收益是成本的 2 倍）
    evaluation.costs.learning < 5      // 学习成本可接受
  );
};
```

### 11.3 示例：是否引入 Redux？
```typescript
// 场景：中型项目，状态管理复杂

const reduxEvaluation: ToolEvaluation = {
  problem: 'Context API 嵌套过深，状态管理混乱',
  currentPain: 8,

  solution: 'Redux Toolkit',
  benefits: [
    '统一状态管理',
    '时间旅行调试',
    '中间件扩展（日志、异步）',
  ],
  costs: {
    learning: 3,        // 团队需 3 天熟悉
    integration: 2,     // 2 天集成到项目
    maintenance: 1,     // 每年 1 天维护（升级等）
  },

  roi: 8 / (3 + 2 + 1) = 1.33,  // ROI = 1.33
};

// 决策：ROI < 2，且有更轻量的替代方案（Zustand）
// 结论：不引入 Redux，使用 Zustand
```

### 11.4 示例：是否引入 Storybook？
```typescript
const storybookEvaluation: ToolEvaluation = {
  problem: '组件文档缺失，协作效率低',
  currentPain: 9,

  solution: 'Storybook',
  benefits: [
    '组件可视化文档',
    '隔离开发环境',
    '自动生成文档',
    '提升设计-开发协作效率',
  ],
  costs: {
    learning: 2,
    integration: 3,
    maintenance: 2,
  },

  roi: 9 / (2 + 3 + 2) = 1.29,
};

// 决策：ROI < 2，但痛点严重（9 分）
// 结论：采纳，但需优化集成成本（使用 Vite 插件）
```

---

## 12. 总结：行为规范 Checklist

### 开发前
- [ ] 需求是否清晰？（不清晰则澄清）
- [ ] TS 类型定义是否完成？
- [ ] 是否有技术选型需要团队评审？

### 开发中
- [ ] 是否遵循代码规范？
- [ ] 是否每天至少提交一次？
- [ ] 遇到问题是否及时求助（15 分钟原则）？

### 提交前
- [ ] ESLint 检查是否通过？
- [ ] 是否完成自测（覆盖所有分支）？
- [ ] 提交信息是否符合规范？

### Code Review
- [ ] 是否在 24 小时内响应 MR？
- [ ] 是否本地测试？
- [ ] 是否提出建设性意见？
- [ ] 是否确认所有问题已解决？

### 工具引入
- [ ] 是否解决实际痛点（≥7 分）？
- [ ] ROI 是否 > 2？
- [ ] 团队学习成本是否可接受（< 5 天）？
- [ ] 是否有团队评审？

---

**参考资料**：
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git Flow](https://nvie.com/posts/a-successful-git-branching-model/)
- [Code Review Best Practices](https://github.com/google/eng-practices/blob/master/review/reviewer/)
- [ROI-driven Development](https://martinfowler.com/articles/is-quality-worth-cost.html)

---

**Sources:**
- [medium.com - Frontend Code Review Checklist](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQGaJhi0MgipjXYtrS6wCGyjHBW0LxaubW2AvUVgWCMeIAYXH0ttItyw4lB7hAonZwSnS0VIO5FmKBbFoL5JRM-sbvA6tEGgalyfwaag9BbZcbToL4KVqotfZhOyZJfZz_CgTl93cr20cGnPRSn8c_hee4bbgIzCWehzW2k0sKA_sxnf22YJ94c0DcphCz1tgeuRjJbwVl6TOf8s132kSYf2nylsqLDy)
- [homeoffice.gov.uk - Code Review Standards](https://vertexaisearch.cloud.google.com/grounding-api-redirect/AUZIYQGuCv1ZznO9NcTjyGp3pqbZlAE9Uh2CRLmCHpJf2VsWpbOFrRJCIn7D2sae6XIl88e7dya5n5yNVHf4n7wWy6QmlR2B_6dWRR1rl6DQQVDc9G9zMn1Oh5zD-zhMB1xJ2mxdG1K6yCOKP4vOedLSMN1wKblRzR9DeA==)
