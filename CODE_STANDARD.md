# 代码规范 (Code Standard)

> **核心理念**：组件化、纯函数、类型先行、渐进式重构

---

## 🤖 AI 开发者指令

你是一个严格遵循规范的 AI 开发助手，必须：
1. 先阅读并理解 TS 类型定义再编码
2. 任何函数超过 50 行必须拆分
3. 禁止使用 `any` 类型
4. 所有修改必须保持纯函数原则
5. 提交代码前自动运行 ESLint 检查
6. 对不符合规范的代码主动提出质疑

---

## 1. TypeScript 规范

### 1.1 类型定义优先
```typescript
// ❌ 错误：先写代码再补类型
function fetchUser(id) { /* ... */ }

// ✅ 正确：先定义类型
interface User {
  id: string;
  name: string;
  email: string;
}

async function fetchUser(id: string): Promise<User> { /* ... */ }
```

### 1.2 禁止 `any`
```typescript
// ❌ 禁止
const data: any = await fetchAPI();

// ✅ 正确：定义明确类型
interface APIResponse {
  code: number;
  data: User[];
}
const response: APIResponse = await fetchAPI();
```

### 1.3 类型选择
- **`type`**：联合类型、交叉类型、基础类型别名
- **`interface`**：对象结构、可扩展的类型

```typescript
// type 用于联合类型
type Status = 'pending' | 'success' | 'error';

// interface 用于对象结构
interface UserProps {
  user: User;
  onUpdate: (user: User) => void;
}
```

### 1.4 组件 Props 必须定义
```typescript
// ✅ 所有组件必须定义 Props interface
interface ButtonProps {
  label: string;
  onClick: () => void;
  disabled?: boolean;
}

const Button: React.FC<ButtonProps> = ({ label, onClick, disabled }) => {
  // ...
};
```

---

## 2. 组件开发规范

### 2.1 单一职责原则
```typescript
// ❌ 错误：一个组件做太多事
function UserDashboard() {
  // 获取用户数据
  // 渲染用户信息
  // 处理表单提交
  // 管理权限
  return <div>...</div>;
}

// ✅ 正确：拆分为多个组件
function UserDashboard() {
  return (
    <div>
      <UserProfile />
      <UserForm />
      <UserPermissions />
    </div>
  );
}
```

### 2.2 组件拆分：即使不复用也要拆分
```typescript
// ✅ 即使 UserAvatar 只用一次，也要拆分
function UserProfile({ user }: UserProfileProps) {
  return (
    <div>
      <UserAvatar url={user.avatar} />
      <UserInfo name={user.name} email={user.email} />
    </div>
  );
}
```

**原则**：大问题递归拆解为小问题，便于维护和测试

### 2.3 纯函数原则
```typescript
// ❌ 错误：修改输入参数
function updateUser(user: User) {
  user.updatedAt = Date.now();  // 直接修改引用
  return user;
}

// ✅ 正确：返回新对象
function updateUser(user: User): User {
  return {
    ...user,
    updatedAt: Date.now(),
  };
}
```

**定义**：同一输入 → 唯一输出，不修改输入，不产生副作用

### 2.4 逻辑与 UI 分离
```typescript
// ❌ 错误：业务逻辑混在组件中
function OrderList() {
  const [orders, setOrders] = useState([]);

  const total = orders.reduce((sum, o) => sum + o.price * o.quantity, 0);
  const tax = total * 0.1;
  const finalPrice = total + tax;

  return <div>Total: {finalPrice}</div>;
}

// ✅ 正确：业务逻辑抽离为纯函数
function calculateOrderTotal(orders: Order[]): number {
  const total = orders.reduce((sum, o) => sum + o.price * o.quantity, 0);
  const tax = total * 0.1;
  return total + tax;
}

function OrderList() {
  const [orders, setOrders] = useState([]);
  const finalPrice = calculateOrderTotal(orders);

  return <div>Total: {finalPrice}</div>;
}
```

---

## 3. React Hooks 规范

### 3.1 useState：状态合并与作用域限制
```typescript
// ❌ 错误：过多的 state
function UserForm() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [phone, setPhone] = useState('');
  // ...
}

// ✅ 正确：合并相关状态
function UserForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    phone: '',
  });
}

// ✅ 更好：自定义 Hook 限制作用域
function useUserForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    phone: '',
  });

  const updateField = (field: keyof typeof formData, value: string) => {
    setFormData(prev => ({ ...prev, [field]: value }));
  };

  return { formData, updateField };
}
```

### 3.2 useEffect：谨慎使用
```typescript
// ❌ 错误：依赖数组不完整
useEffect(() => {
  fetchUser(userId);
}, []); // 缺少 userId

// ✅ 正确：完整依赖
useEffect(() => {
  fetchUser(userId);
}, [userId]);

// ⚠️ 注意：useEffect 是 AOP 切面编程，理解副作用后再用
// 尽可能少用，避免复杂业务逻辑
```

### 3.3 useMemo / useCallback：谨慎使用（非银弹）

**核心原则**：仅在计算成本 > memoization 开销时使用

```typescript
// ❌ 错误：简单计算无需 useMemo
const sum = useMemo(() => a + b, [a, b]); // 过度优化

// ✅ 正确：昂贵计算才用
const expensiveResult = useMemo(() => {
  return heavyData.filter(/* 复杂条件 */)
                  .map(/* 复杂转换 */)
                  .reduce(/* 复杂聚合 */, 0);
}, [heavyData]);

// ❌ 错误：useCallback 滥用
const handleClick = useCallback(() => {
  console.log('clicked');
}, []); // 子组件未用 React.memo()，无意义

// ✅ 正确：仅在传递给 memo 组件时使用
const MemoizedChild = React.memo(Child);

function Parent() {
  const handleClick = useCallback(() => {
    console.log('clicked');
  }, []);

  return <MemoizedChild onClick={handleClick} />;
}
```

**使用场景**：
- `useMemo`：重循环、复杂数据转换、>16ms 计算
- `useCallback`：传递给 `React.memo()` 包裹的子组件

**危害**：过度使用增加代码复杂度 + memoization 本身的性能开销

**原则**：先测量，后优化（React DevTools Profiler）

### 3.4 通用规则
```typescript
// ❌ 禁止：在循环/条件中调用 Hooks
if (condition) {
  useState(0); // 错误！
}

// ✅ 正确：Hooks 必须在顶层调用
const [count, setCount] = useState(0);
```

---

## 4. 数据流与状态管理

### 4.1 不可变性原则
```typescript
// ❌ 错误：直接修改
const newList = list;
newList.push(item); // 修改了原数组

// ✅ 正确：使用不可变操作
const newList = [...list, item];

// ✅ 对象更新
const newUser = { ...user, name: 'New Name' };

// ✅ 数组过滤
const filtered = list.filter(item => item.id !== deleteId);
```

### 4.2 全局状态管理
```typescript
// ✅ 使用 Context API
const UserContext = createContext<User | null>(null);

// ✅ 配合 sessionStorage 持久化
useEffect(() => {
  sessionStorage.setItem('user', JSON.stringify(user));
}, [user]);

// ❌ 禁止：window 全局变量
window.currentUser = user; // 禁止！
```

---

## 5. 命名与代码风格

### 5.1 命名规范
```typescript
// 变量/函数：camelCase
const userName = 'Alice';
function getUserById(id: string) { /* ... */ }

// 组件/类/类型：PascalCase
interface UserProps { /* ... */ }
class UserService { /* ... */ }
function UserProfile() { /* ... */ }

// 常量/枚举：UPPER_CASE
const API_BASE_URL = 'https://api.example.com';
enum UserRole {
  ADMIN = 'admin',
  USER = 'user',
}

// CSS 类名：kebab-case
.user-profile-card { /* ... */ }

// 文件名
UserProfile.tsx       // 组件
userService.ts        // 工具/服务
constants.ts          // 常量
```

### 5.2 遵循 Airbnb 风格
参考：[Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)

---

## 6. 代码质量标准

### 6.1 函数参数限制
```typescript
// ❌ 错误：参数过多
function createUser(name: string, email: string, phone: string, age: number) {
  // ...
}

// ✅ 正确：封装为对象
interface CreateUserParams {
  name: string;
  email: string;
  phone: string;
  age: number;
}

function createUser(params: CreateUserParams) {
  // ...
}
```

**规则**：参数 ≤ 3 个

### 6.2 条件分支限制
```typescript
// ❌ 错误：过多 if-else
if (status === 'pending') { /* ... */ }
else if (status === 'processing') { /* ... */ }
else if (status === 'completed') { /* ... */ }
else if (status === 'failed') { /* ... */ }
else if (status === 'cancelled') { /* ... */ }

// ✅ 正确：使用 enum + switch 或对象映射
enum OrderStatus {
  PENDING = 'pending',
  PROCESSING = 'processing',
  COMPLETED = 'completed',
  FAILED = 'failed',
  CANCELLED = 'cancelled',
}

const statusHandlers = {
  [OrderStatus.PENDING]: handlePending,
  [OrderStatus.PROCESSING]: handleProcessing,
  [OrderStatus.COMPLETED]: handleCompleted,
  [OrderStatus.FAILED]: handleFailed,
  [OrderStatus.CANCELLED]: handleCancelled,
};

statusHandlers[status]();
```

**规则**：条件分支 ≤ 4 个

### 6.3 循环嵌套限制
```typescript
// ❌ 错误：嵌套过深
for (const user of users) {
  for (const order of user.orders) {
    for (const item of order.items) {
      // ...
    }
  }
}

// ✅ 正确：拆分函数
function processOrderItems(order: Order) {
  for (const item of order.items) {
    // ...
  }
}

function processUserOrders(user: User) {
  for (const order of user.orders) {
    processOrderItems(order);
  }
}

for (const user of users) {
  processUserOrders(user);
}
```

**规则**：循环嵌套 ≤ 2 层

### 6.4 函数长度
```typescript
// ✅ 函数应该一屏可见（约 50 行）
// 超过则拆分为多个函数
```

### 6.5 注释原则
```typescript
// ❌ 错误：注释"是什么"
// 设置用户名为 Alice
const userName = 'Alice';

// ✅ 正确：注释"为什么"
// 使用 Alice 作为默认用户名，因为测试环境需要固定账号
const userName = 'Alice';

// ✅ 复杂逻辑注释
// 计算订单总价：商品价格 * 数量 + 10% 税费 - 会员折扣
const total = calculateOrderTotal(order);
```

**原则**：
- 代码自解释，不需要过多注释
- 注释解释"为什么"而非"是什么"
- 复杂算法/业务逻辑必须注释

---

## 7. 重构原则

### 7.1 事不过三，三则重构
```typescript
// 第一次：直接写
function handleUserA() { /* 处理逻辑 */ }

// 第二次：复制粘贴（可接受）
function handleUserB() { /* 类似逻辑 */ }

// 第三次：必须重构
function handleUser(type: 'A' | 'B' | 'C') {
  // 抽象通用逻辑
}
```

### 7.2 渐进式重构
```typescript
// ✅ 小步快跑
// 1. 提交一：提取函数
// 2. 提交二：重命名变量
// 3. 提交三：优化类型

// ❌ 大规模重写（高风险）
// 一次性重构整个模块 → 难以 Review，容易出错
```

### 7.3 重构不改变外部行为
```typescript
// ✅ 重构前后，API 保持一致
// 重构前
function getUser(id: string): User { /* ... */ }

// 重构后
function getUser(id: string): User {
  // 内部实现改变，但签名和返回值不变
}
```

---

## 8. 错误处理

### 8.1 统一异常处理
```typescript
// ❌ 错误：空 catch
try {
  await fetchUser(id);
} catch (e) {
  // 什么都不做
}

// ✅ 正确：至少要 log
try {
  await fetchUser(id);
} catch (error) {
  console.error('Failed to fetch user:', error);
  throw error; // 或者处理错误
}
```

### 8.2 Promise 异常处理
```typescript
// ✅ Async/Await
async function loadData() {
  try {
    const data = await fetchAPI();
    return data;
  } catch (error) {
    handleError(error);
    throw error;
  }
}

// ✅ Promise.catch
fetchAPI()
  .then(data => processData(data))
  .catch(error => handleError(error));
```

### 8.3 用户友好的错误提示
```typescript
// ❌ 技术性错误
alert('Error: 500 Internal Server Error');

// ✅ 用户友好提示
showToast('加载失败，请稍后重试');
```

---

## 9. 性能优化

### 9.1 React.memo()
```typescript
// ✅ 仅在高频渲染组件使用
const ExpensiveComponent = React.memo(({ data }: Props) => {
  // 复杂渲染逻辑
});

// ❌ 简单组件无需 memo
const SimpleText = React.memo(({ text }: { text: string }) => <p>{text}</p>);
```

### 9.2 代码分割
```typescript
// ✅ 使用 React.lazy + Suspense
const UserDashboard = React.lazy(() => import('./UserDashboard'));

function App() {
  return (
    <Suspense fallback={<Loading />}>
      <UserDashboard />
    </Suspense>
  );
}
```

### 9.3 列表虚拟化
```typescript
// ✅ 长列表使用虚拟化
import { FixedSizeList } from 'react-window';

<FixedSizeList
  height={600}
  itemCount={1000}
  itemSize={35}
>
  {Row}
</FixedSizeList>
```

### 9.4 避免匿名函数
```typescript
// ❌ 错误：JSX 中的内联函数会导致重渲染
<Button onClick={() => handleClick(id)} />

// ✅ 正确：提取为命名函数
const handleButtonClick = () => handleClick(id);
<Button onClick={handleButtonClick} />

// ✅ 或使用 useCallback（如果 Button 被 memo）
const handleButtonClick = useCallback(() => handleClick(id), [id]);
<MemoizedButton onClick={handleButtonClick} />
```

---

## 10. 工程化工具选择原则

### 10.1 核心理念
**技术服务于实际**：工具本身有学习成本和维护成本

### 10.2 ROI 评估
```
解决问题的价值 > 引入成本 → 坚决使用
解决问题的价值 < 引入成本 → 反对使用
```

### 10.3 评估维度
1. **是否解决实际痛点？**
   - 有明确的业务需求或技术问题

2. **团队学习成本多大？**
   - 新工具是否易于上手
   - 是否有充足的文档和示例

3. **长期维护成本如何？**
   - 社区活跃度
   - 是否有持续更新

4. **是否有成熟的社区支持？**
   - GitHub Stars / Issues 数量
   - Stack Overflow 问答量

### 10.4 决策流程
```typescript
// 示例：是否引入 Redux？
const shouldUseRedux = (
  isStateComplexEnough &&        // 状态复杂度足够
  teamKnowsRedux &&               // 团队熟悉
  projectNeedsTimeTravel          // 需要时间旅行调试
);

// 否则使用 Context API + useReducer 即可
```

---

## 11. 安全规范

### 11.1 防 XSS
```typescript
// ❌ 危险：直接插入 HTML
<div dangerouslySetInnerHTML={{ __html: userInput }} />

// ✅ 安全：使用文本节点
<div>{userInput}</div>

// ✅ 如果必须插入 HTML，使用 DOMPurify
import DOMPurify from 'dompurify';
<div dangerouslySetInnerHTML={{ __html: DOMPurify.sanitize(userInput) }} />
```

### 11.2 敏感信息
```typescript
// ❌ 禁止：硬编码敏感信息
const API_KEY = 'sk-1234567890abcdef';

// ✅ 正确：使用环境变量
const API_KEY = process.env.REACT_APP_API_KEY;
```

---

## 12. 总结：黄金准则 Checklist

开发前：
- [ ] TS 类型定义是否完整？
- [ ] 组件是否满足单一职责？
- [ ] 是否可以进一步拆分？

开发中：
- [ ] 是否遵循纯函数原则？
- [ ] 是否避免直接修改 state/props？
- [ ] Hooks 依赖数组是否完整？

提交前：
- [ ] ESLint 检查是否通过？
- [ ] 是否有适当的错误处理？
- [ ] 是否有安全隐患（XSS、注入等）？
- [ ] 函数长度是否超过 50 行？
- [ ] 是否有不必要的优化（useMemo/useCallback 滥用）？

---

**参考资料**：
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [React Official Docs](https://react.dev)
- [TypeScript Handbook](https://www.typescriptlang.org/docs/)
