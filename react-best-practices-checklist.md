# React 官方最佳实践检查清单

基于 React 官方文档 (https://react.dev/learn) 整理的最佳实践检查清单，用于代码审查和项目改进。

---

## 一、组件基础

### 1.1 组件命名

| 检查项 | 状态 |
|--------|------|
| 组件名以大写字母开头 | ☐ |
| 组件文件名与组件名一致 | ☐ |

❌ **错误：**
```jsx
// 小写字母开头
function myComponent() {
  return <div>Hello</div>;
}
```

✅ **正确：**
```jsx
// 大写字母开头
function MyComponent() {
  return <div>Hello</div>;
}
```

### 1.2 组件定义位置

| 检查项 | 状态 |
|--------|------|
| 组件不在其他组件内部定义 | ☐ |

❌ **错误：**
```jsx
function Parent() {
  // 永远不要在组件内部定义组件！
  function Child() {
    return <div>Child</div>;
  }
  return <Child />;
}
```

✅ **正确：**
```jsx
// 在顶层定义
function Child() {
  return <div>Child</div>;
}

function Parent() {
  return <Child />;
}
```

---

## 二、Props 最佳实践

### 2.1 Props 解构

| 检查项 | 状态 |
|--------|------|
| 使用解构语法获取 props | ☐ |
| 为可选 props 设置默认值 | ☐ |

❌ **错误：**
```jsx
function Avatar(props) {
  return (
    <img
      src={props.person.imageUrl}
      alt={props.person.name}
      width={props.size}
      height={props.size}
    />
  );
}
```

✅ **正确：**
```jsx
function Avatar({ person, size = 100 }) {
  return (
    <img
      src={person.imageUrl}
      alt={person.name}
      width={size}
      height={size}
    />
  );
}
```

### 2.2 Children Prop

| 检查项 | 状态 |
|--------|------|
| 使用 children prop 进行组件组合 | ☐ |

✅ **正确：**
```jsx
function Card({ children }) {
  return <div className="card">{children}</div>;
}

// 使用
<Card>
  <Avatar />
  <p>Description</p>
</Card>
```

### 2.3 Props 只读原则

| 检查项 | 状态 |
|--------|------|
| 不直接修改 props | ☐ |

❌ **错误：**
```jsx
function Profile({ user }) {
  user.name = 'New Name'; // 不要修改 props！
  return <div>{user.name}</div>;
}
```

✅ **正确：**
```jsx
function Profile({ user }) {
  const displayName = user.name; // 只读取
  return <div>{displayName}</div>;
}
```

---

## 三、条件渲染

### 3.1 条件渲染方式

| 检查项 | 状态 |
|--------|------|
| 使用三元运算符 (?) 进行二选一 | ☐ |
| 使用 && 进行条件显示 | ☐ |
| 使用 if 语句提前返回 | ☐ |

✅ **正确：**
```jsx
// 三元运算符 - 二选一
function Item({ name, isPacked }) {
  return (
    <li>
      {name} {isPacked ? '✅' : '❌'}
    </li>
  );
}

// && 运算符 - 条件显示
function Item({ name, isPacked }) {
  return (
    <li>
      {name} {isPacked && '✅'}
    </li>
  );
}

// if 语句 - 提前返回
function Item({ name, isPacked }) {
  if (isPacked) {
    return <li className="packed">{name} ✅</li>;
  }
  return <li>{name}</li>;
}
```

### 3.2 避免常见陷阱

| 检查项 | 状态 |
|--------|------|
| && 左侧不使用可能为 0 的数字 | ☐ |

❌ **错误：**
```jsx
// 当 count 为 0 时，会显示 0
{count && <Badge count={count} />}
```

✅ **正确：**
```jsx
// 使用显式布尔检查
{count > 0 && <Badge count={count} />}
```

---

## 四、列表渲染

### 4.1 Key 的使用

| 检查项 | 状态 |
|--------|------|
| 每个列表项有唯一的 key | ☐ |
| key 来自数据本身（如 ID） | ☐ |
| 不使用数组索引作为 key（除非静态列表） | ☐ |

❌ **错误：**
```jsx
// 使用索引作为 key
{items.map((item, index) => (
  <li key={index}>{item.name}</li>
))}
```

✅ **正确：**
```jsx
// 使用数据中的唯一 ID
{items.map(item => (
  <li key={item.id}>{item.name}</li>
))}
```

---

## 五、State 管理

### 5.1 useState 基础

| 检查项 | 状态 |
|--------|------|
| State 在组件顶层调用 | ☐ |
| 不在条件/循环/嵌套函数中调用 Hooks | ☐ |

❌ **错误：**
```jsx
function Component({ isLoggedIn }) {
  if (isLoggedIn) {
    const [user, setUser] = useState(null); // 不要在条件中调用！
  }
}
```

✅ **正确：**
```jsx
function Component({ isLoggedIn }) {
  const [user, setUser] = useState(null); // 始终在顶层调用

  if (!isLoggedIn) {
    return null;
  }
  // ...
}
```

### 5.2 惰性初始化

| 检查项 | 状态 |
|--------|------|
| 昂贵的初始值使用函数初始化 | ☐ |

❌ **错误：**
```jsx
// 每次渲染都会执行 createInitialTodos()
const [todos, setTodos] = useState(createInitialTodos());
```

✅ **正确：**
```jsx
// 只在首次渲染时执行
const [todos, setTodos] = useState(() => createInitialTodos());
```

### 5.3 函数式更新

| 检查项 | 状态 |
|--------|------|
| 基于前一个状态更新时使用函数式更新 | ☐ |

❌ **错误：**
```jsx
// 批量更新时可能出错
function handleClick() {
  setCount(count + 1);
  setCount(count + 1);
  setCount(count + 1); // 只增加了 1
}
```

✅ **正确：**
```jsx
// 使用函数式更新
function handleClick() {
  setCount(c => c + 1);
  setCount(c => c + 1);
  setCount(c => c + 1); // 正确增加了 3
}
```

### 5.4 State 结构设计

| 检查项 | 状态 |
|--------|------|
| 相关 state 分组在一起 | ☐ |
| 避免矛盾的 state | ☐ |
| 避免冗余的 state（可计算的值） | ☐ |
| 避免 state 重复 | ☐ |
| 避免深层嵌套的 state | ☐ |

❌ **错误：冗余 state**
```jsx
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');
const [fullName, setFullName] = useState(''); // 冗余！
```

✅ **正确：计算派生值**
```jsx
const [firstName, setFirstName] = useState('');
const [lastName, setLastName] = useState('');

// 在渲染时计算
const fullName = firstName + ' ' + lastName;
```

---

## 六、对象和数组更新

### 6.1 不可变更新

| 检查项 | 状态 |
|--------|------|
| 不直接修改 state 中的对象 | ☐ |
| 不直接修改 state 中的数组 | ☐ |
| 使用展开语法创建新对象/数组 | ☐ |

❌ **错误：直接修改**
```jsx
// 对象
function handleNameChange(e) {
  person.name = e.target.value; // 不要直接修改！
  setPerson(person);
}

// 数组
function handleAdd(item) {
  items.push(item); // 不要直接修改！
  setItems(items);
}
```

✅ **正确：创建新对象/数组**
```jsx
// 对象
function handleNameChange(e) {
  setPerson({
    ...person,
    name: e.target.value
  });
}

// 数组添加
function handleAdd(item) {
  setItems([...items, item]);
}

// 数组删除
function handleRemove(id) {
  setItems(items.filter(item => item.id !== id));
}

// 数组更新
function handleUpdate(id, newData) {
  setItems(items.map(item =>
    item.id === id ? { ...item, ...newData } : item
  ));
}
```

### 6.2 嵌套对象更新

| 检查项 | 状态 |
|--------|------|
| 嵌套对象每层都创建新引用 | ☐ |

✅ **正确：**
```jsx
const [person, setPerson] = useState({
  name: 'Alice',
  artwork: {
    title: 'Blue Nana',
    city: 'Hamburg'
  }
});

function handleCityChange(e) {
  setPerson({
    ...person,
    artwork: {
      ...person.artwork, // 每层都要展开
      city: e.target.value
    }
  });
}
```

### 6.3 使用 Immer 简化

| 检查项 | 状态 |
|--------|------|
| 复杂嵌套更新考虑使用 Immer | ☐ |

✅ **推荐：**
```bash
npm install use-immer
```

```jsx
import { useImmer } from 'use-immer';

function Form() {
  const [person, updatePerson] = useImmer({
    name: 'Alice',
    artwork: {
      title: 'Blue Nana',
      city: 'Hamburg'
    }
  });

  function handleCityChange(e) {
    updatePerson(draft => {
      draft.artwork.city = e.target.value; // 可以直接修改！
    });
  }
}
```

---

## 七、状态提升

### 7.1 共享状态

| 检查项 | 状态 |
|--------|------|
| 共享状态放在最近的共同父组件 | ☐ |
| 子组件通过 props 接收状态和更新函数 | ☐ |

✅ **正确：**
```jsx
function Parent() {
  const [value, setValue] = useState('');

  return (
    <>
      <ChildA value={value} />
      <ChildB onChange={setValue} />
    </>
  );
}

function ChildA({ value }) {
  return <div>Value: {value}</div>;
}

function ChildB({ onChange }) {
  return (
    <input onChange={e => onChange(e.target.value)} />
  );
}
```

### 7.2 状态保留与重置

| 检查项 | 状态 |
|--------|------|
| 理解状态与组件位置的关联 | ☐ |
| 使用 key 强制重置组件状态 | ☐ |

✅ **使用 key 重置状态：**
```jsx
function ChatApp() {
  const [recipient, setRecipient] = useState(contacts[0]);

  return (
    <>
      <ContactList onSelect={setRecipient} />
      {/* key 变化时，Chat 组件状态会重置 */}
      <Chat key={recipient.id} recipient={recipient} />
    </>
  );
}
```

---

## 八、useReducer 模式

### 8.1 何时使用

| 检查项 | 状态 |
|--------|------|
| 复杂状态逻辑使用 useReducer | ☐ |
| 多个子值的状态考虑 useReducer | ☐ |
| 下一个状态依赖前一个状态时使用 | ☐ |

### 8.2 Reducer 模式

✅ **正确：**
```jsx
import { useReducer } from 'react';

function tasksReducer(tasks, action) {
  switch (action.type) {
    case 'added': {
      return [...tasks, {
        id: action.id,
        text: action.text,
        done: false
      }];
    }
    case 'changed': {
      return tasks.map(t =>
        t.id === action.task.id ? action.task : t
      );
    }
    case 'deleted': {
      return tasks.filter(t => t.id !== action.id);
    }
    default: {
      throw Error('Unknown action: ' + action.type);
    }
  }
}

function TaskApp() {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  function handleAdd(text) {
    dispatch({ type: 'added', id: nextId++, text });
  }

  function handleChange(task) {
    dispatch({ type: 'changed', task });
  }

  function handleDelete(id) {
    dispatch({ type: 'deleted', id });
  }
  // ...
}
```

---

## 九、Context API

### 9.1 Context 使用

| 检查项 | 状态 |
|--------|------|
| 避免深层 prop drilling | ☐ |
| 使用 Context 传递深层数据 | ☐ |

✅ **正确：**
```jsx
// 1. 创建 Context
import { createContext, useContext } from 'react';

const LevelContext = createContext(1);

// 2. Provider 提供值
function Section({ children }) {
  const level = useContext(LevelContext);
  return (
    <section className="section">
      <LevelContext.Provider value={level + 1}>
        {children}
      </LevelContext.Provider>
    </section>
  );
}

// 3. Consumer 使用值
function Heading({ children }) {
  const level = useContext(LevelContext);
  // ...
}
```

### 9.2 Reducer + Context 组合

| 检查项 | 状态 |
|--------|------|
| 复杂全局状态使用 Reducer + Context | ☐ |

✅ **推荐模式：**
```jsx
// TasksContext.js
import { createContext, useContext, useReducer } from 'react';

const TasksContext = createContext(null);
const TasksDispatchContext = createContext(null);

export function TasksProvider({ children }) {
  const [tasks, dispatch] = useReducer(tasksReducer, initialTasks);

  return (
    <TasksContext.Provider value={tasks}>
      <TasksDispatchContext.Provider value={dispatch}>
        {children}
      </TasksDispatchContext.Provider>
    </TasksContext.Provider>
  );
}

export function useTasks() {
  return useContext(TasksContext);
}

export function useTasksDispatch() {
  return useContext(TasksDispatchContext);
}

// 使用
function App() {
  return (
    <TasksProvider>
      <TaskList />
      <AddTask />
    </TasksProvider>
  );
}

function TaskList() {
  const tasks = useTasks();
  // ...
}

function AddTask() {
  const dispatch = useTasksDispatch();
  // ...
}
```

---

## 十、Refs (useRef)

### 10.1 何时使用 Refs

| 检查项 | 状态 |
|--------|------|
| 存储不用于渲染的值 | ☐ |
| 操作 DOM 元素 | ☐ |
| 存储上一次的值 | ☐ |

### 10.2 Refs vs State

| 场景 | 使用 |
|------|------|
| 需要在渲染中使用 | State |
| 需要触发重新渲染 | State |
| 存储 DOM 引用 | Ref |
| 存储 setTimeout ID | Ref |
| 存储不影响渲染的值 | Ref |

✅ **正确：**
```jsx
function TextInput() {
  const inputRef = useRef(null);

  function handleClick() {
    inputRef.current.focus();
  }

  return (
    <>
      <input ref={inputRef} />
      <button onClick={handleClick}>Focus</button>
    </>
  );
}
```

### 10.3 避免在渲染期间读写 ref.current

| 检查项 | 状态 |
|--------|------|
| 不在渲染期间读写 ref.current | ☐ |

❌ **错误：**
```jsx
function Component() {
  const ref = useRef(0);

  // 不要在渲染期间写入！
  ref.current = ref.current + 1;

  return <div>{ref.current}</div>; // 不要在渲染期间读取！
}
```

✅ **正确：**
```jsx
function Component() {
  const ref = useRef(0);

  // 在事件处理或 Effect 中读写
  function handleClick() {
    ref.current += 1;
    console.log(ref.current);
  }

  return <button onClick={handleClick}>Click</button>;
}
```

---

## 十一、Effects (useEffect)

### 11.1 何时使用 Effect

| 检查项 | 状态 |
|--------|------|
| Effect 只用于与外部系统同步 | ☐ |
| 不在 Effect 中计算派生值 | ☐ |
| 不在 Effect 中响应用户事件 | ☐ |

### 11.2 不需要 Effect 的场景

| 场景 | 正确做法 |
|------|----------|
| 计算派生值 | 直接在渲染时计算 |
| 响应用户事件 | 在事件处理器中处理 |
| 初始化状态 | 使用惰性初始化 |
| 重置状态 | 使用 key 或条件渲染 |

❌ **错误：用 Effect 计算值**
```jsx
function Form({ firstName, lastName }) {
  const [fullName, setFullName] = useState('');

  useEffect(() => {
    setFullName(firstName + ' ' + lastName); // 不需要！
  }, [firstName, lastName]);

  return <div>{fullName}</div>;
}
```

✅ **正确：直接计算**
```jsx
function Form({ firstName, lastName }) {
  const fullName = firstName + ' ' + lastName; // 直接计算
  return <div>{fullName}</div>;
}
```

❌ **错误：用 Effect 响应事件**
```jsx
function Form() {
  const [submitted, setSubmitted] = useState(false);

  useEffect(() => {
    if (submitted) {
      postData(formData); // 不应该在 Effect 中！
    }
  }, [submitted]);

  function handleSubmit() {
    setSubmitted(true);
  }
}
```

✅ **正确：在事件处理器中处理**
```jsx
function Form() {
  function handleSubmit() {
    postData(formData); // 直接在事件处理器中处理
  }

  return <form onSubmit={handleSubmit}>...</form>;
}
```

### 11.3 Effect 清理

| 检查项 | 状态 |
|--------|------|
| Effect 中订阅/定时器有清理函数 | ☐ |

✅ **正确：**
```jsx
useEffect(() => {
  const connection = createConnection();
  connection.connect();

  // 返回清理函数
  return () => {
    connection.disconnect();
  };
}, []);

useEffect(() => {
  const interval = setInterval(() => {
    setCount(c => c + 1);
  }, 1000);

  // 清理定时器
  return () => clearInterval(interval);
}, []);
```

### 11.4 Effect 依赖

| 检查项 | 状态 |
|--------|------|
| 依赖数组包含所有外部变量 | ☐ |
| 不随意省略依赖项 | ☐ |
| 使用 React 部分来验证依赖 | ☐ |

---

## 十二、自定义 Hooks

### 12.1 自定义 Hooks 规则

| 检查项 | 状态 |
|--------|------|
| Hook 名称以 `use` 开头 | ☐ |
| 只在自定义 Hook 中调用其他 Hooks | ☐ |
| 共享逻辑而非状态 | ☐ |

✅ **正确：**
```jsx
// 自定义 Hook
function useWindowSize() {
  const [size, setSize] = useState({
    width: window.innerWidth,
    height: window.innerHeight
  });

  useEffect(() => {
    function handleResize() {
      setSize({
        width: window.innerWidth,
        height: window.innerHeight
      });
    }

    window.addEventListener('resize', handleResize);
    return () => window.removeEventListener('resize', handleResize);
  }, []);

  return size;
}

// 使用
function Component() {
  const { width, height } = useWindowSize();
  return <div>{width} x {height}</div>;
}
```

### 12.2 常见自定义 Hook 模式

```jsx
// 网络请求
function useFetch(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    fetch(url)
      .then(res => res.json())
      .then(setData)
      .catch(setError)
      .finally(() => setLoading(false));
  }, [url]);

  return { data, loading, error };
}

// 本地存储
function useLocalStorage(key, initialValue) {
  const [value, setValue] = useState(() => {
    const stored = localStorage.getItem(key);
    return stored ? JSON.parse(stored) : initialValue;
  });

  useEffect(() => {
    localStorage.setItem(key, JSON.stringify(value));
  }, [key, value]);

  return [value, setValue];
}

// 上一次的值
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  }, [value]);
  return ref.current;
}
```

---

## 十三、纯组件

### 13.1 组件纯净性

| 检查项 | 状态 |
|--------|------|
| 组件不修改外部变量 | ☐ |
| 相同输入产生相同输出 | ☐ |
| 副作用在 Effect 中处理 | ☐ |

❌ **错误：不纯的组件**
```jsx
let guestCount = 0;

function Cup() {
  guestCount++; // 副作用！
  return <h2>Tea cup for guest #{guestCount}</h2>;
}
```

✅ **正确：纯组件**
```jsx
function Cup({ guest }) {
  return <h2>Tea cup for guest #{guest}</h2>;
}

// 或者使用 state
function Cup() {
  const [guest, setGuest] = useState(0);
  // 在事件处理器中更新
}
```

---

## 十四、Thinking in React 五步法

### 14.1 组件设计流程

| 步骤 | 说明 | 状态 |
|------|------|------|
| 1. 分解 UI 为组件层级 | 根据 UI 设计图绘制组件边界 | ☐ |
| 2. 构建静态版本 | 只用 props，无交互 | ☐ |
| 3. 找出最小完整状态 | DRY 原则，可计算的不存 | ☐ |
| 4. 确定状态位置 | 最近共同父组件 | ☐ |
| 5. 添加反向数据流 | 子组件通知父组件更新 | ☐ |

### 14.2 判断是否为 State

对于每个数据，问三个问题：

1. **是否从父组件通过 props 传递？** → 不是 state
2. **是否随时间保持不变？** → 不是 state
3. **能否根据其他 state 或 props 计算？** → 不是 state

---

## 十五、常见错误速查

| 错误 | 正确做法 |
|------|----------|
| 直接修改 state | 使用 setState 创建新对象/数组 |
| 使用索引作为 key | 使用数据中的唯一 ID |
| 在条件中调用 Hooks | 始终在组件顶层调用 |
| 在 Effect 中计算派生值 | 直接在渲染时计算 |
| 在 Effect 中处理用户事件 | 在事件处理器中处理 |
| 忘记清理 Effect | 返回清理函数 |
| 组件内部定义组件 | 在顶层定义所有组件 |
| 使用 && 时左侧为 0 | 使用显式布尔检查 |

---

## 使用方法

1. **代码审查时**：对照检查清单逐项检查
2. **重构时**：找出违反最佳实践的地方进行修复
3. **新功能开发时**：遵循清单中的模式

---

## 参考资料

- [React 官方文档](https://react.dev/learn)
- [React API 参考](https://react.dev/reference/react)
