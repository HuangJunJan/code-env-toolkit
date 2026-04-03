# AGENTS.md - 项目开发规则配置文件

## 目录

1. [概述](#1-概述)
2. [文件编码规范](#2-文件编码规范)
3. [注释规范](#3-注释规范)
4. [命名规范](#4-命名规范)
5. [文件组织规范](#5-文件组织规范)
6. [版本控制规范](#6-版本控制规范)
7. [附录](#7-附录)

---

## 1. 概述

### 1.1 文档目的

本文档定义了项目的开发规则和编码规范，旨在：
- 为项目开发者提供统一的编码标准
- 指导AI辅助工具（如CodeArts代码智能体）生成符合项目规范的代码
- 确保代码质量和可维护性
- 促进团队协作和代码一致性

### 1.2 适用范围

本文档涵盖以下方面的规范：
- 文件编码格式
- 代码注释规范
- 命名约定
- 文件组织结构
- 版本控制规范

### 1.3 目标读者

- 项目开发者
- AI辅助开发工具
- 代码审查人员
- 新加入项目的团队成员

---

## 2. 文件编码规范

### 2.1 默认编码

**所有文本文件默认使用 UTF-8 without BOM 编码格式。**

### 2.2 文件类型编码要求

| 文件类型 | 编码格式 | 说明 |
|---------|---------|------|
| 源代码文件 (.ts, .js, .tsx, .jsx) | UTF-8 without BOM | TypeScript/JavaScript源代码 |
| 配置文件 (.json, .yaml, .yml, .xml) | UTF-8 without BOM | 项目配置文件 |
| 文档文件 (.md, .txt) | UTF-8 without BOM | Markdown和纯文本文档 |
| 样式文件 (.css, .scss, .less) | UTF-8 without BOM | CSS/预处理器样式文件 |
| HTML文件 (.html, .htm) | UTF-8 without BOM | HTML标记文件 |
| Shell脚本 (.sh) | UTF-8 without BOM | Unix/Linux shell脚本 |

### 2.3 特殊文件编码处理

| 文件类型 | 编码格式 | 原因 |
|---------|---------|------|
| Windows批处理 (.bat, .cmd) | GBK/GB2312 | Windows命令行默认编码 |
| PowerShell脚本 (.ps1) | UTF-8 with BOM | PowerShell推荐编码格式 |

### 2.4 IDE设置建议

**VS Code设置：**
```json
{
  "files.encoding": "utf8",
  "files.autoGuessEncoding": false,
  "files.eol": "\n"
}
```

**WebStorm/IntelliJ IDEA设置：**
- File → Settings → Editor → File Encodings
- Global Encoding: UTF-8
- Project Encoding: UTF-8
- Default encoding for properties files: UTF-8

---

## 3. 注释规范

### 3.1 文件头注释

每个源代码文件应在文件开头包含文件头注释，说明文件的基本信息。

**模板：**
```typescript
/**
 * @file 文件名称
 * @description 文件功能描述
 * @author 作者姓名
 * @date 创建日期 (YYYY-MM-DD)
 * @lastModified 最后修改日期 (YYYY-MM-DD)
 * @version 版本号
 */
```

**示例：**
```typescript
/**
 * @file user-service.ts
 * @description 用户服务模块，处理用户相关的业务逻辑
 * @author Zhang San
 * @date 2024-01-15
 * @lastModified 2024-01-20
 * @version 1.2.0
 */
```

### 3.2 函数/方法注释

使用JSDoc风格的注释说明函数的功能、参数和返回值。

**模板：**
```typescript
/**
 * 函数功能描述
 * @param {类型} 参数名 - 参数说明
 * @returns {类型} 返回值说明
 * @throws {异常类型} 异常说明（如有）
 * @example
 * // 使用示例
 * functionName(param);
 */
```

**示例：**
```typescript
/**
 * 根据用户ID获取用户信息
 * @param {string} userId - 用户唯一标识符
 * @returns {Promise<User>} 用户信息对象
 * @throws {NotFoundError} 当用户不存在时抛出
 * @example
 * const user = await getUserById('user-123');
 * console.log(user.name);
 */
async function getUserById(userId: string): Promise<User> {
  // 实现代码
}
```

### 3.3 类和接口注释

```typescript
/**
 * 用户服务类
 * 提供用户相关的业务操作方法
 */
class UserService {
  /**
   * 用户数据访问对象
   * @private
   */
  private userRepository: UserRepository;

  // 类成员
}

/**
 * 用户信息接口
 * @interface IUser
 */
interface IUser {
  id: string;
  name: string;
  email: string;
}
```

### 3.4 行内注释

**规则：**
- 使用 `//` 进行单行注释
- 注释符号与注释内容之间保留一个空格
- 注释应说明"为什么"而非"是什么"
- 避免无意义的注释

**正确示例：**
```typescript
// 使用缓存减少数据库查询次数，提升性能
const cachedUser = userCache.get(userId);

// 延迟3秒执行，等待外部服务初始化完成
setTimeout(initExternalService, 3000);
```

**错误示例：**
```typescript
// 获取用户
const user = getUser(); // ❌ 无意义的注释

//设置值
value = 10; // ❌ 缺少空格
```

### 3.5 注释最佳实践

1. **保持同步**：代码修改时同步更新相关注释
2. **避免冗余**：不注释显而易见的代码
3. **解释意图**：注释应解释代码的目的和原因
4. **使用标准格式**：遵循JSDoc规范，便于生成文档
5. **适度注释**：代码应自解释，注释作为补充

---

## 4. 命名规范

### 4.1 命名约定矩阵

| 元素类型 | 命名风格 | 示例 | 说明 |
|---------|---------|------|------|
| 文件名 | kebab-case | `user-service.ts` | 小写字母，单词间用连字符分隔 |
| 变量名 | camelCase | `userName` | 小驼峰命名 |
| 常量名 | UPPER_SNAKE_CASE | `MAX_RETRY_COUNT` | 全大写，单词间用下划线分隔 |
| 函数名 | camelCase | `getUserInfo()` | 小驼峰命名 |
| 类名 | PascalCase | `UserService` | 大驼峰命名 |
| 接口名 | PascalCase | `IUserService` | 大驼峰命名，可加I前缀 |
| 类型别名 | PascalCase | `UserConfig` | 大驼峰命名 |
| 枚举名 | PascalCase | `UserStatus` | 大驼峰命名 |
| 枚举值 | UPPER_SNAKE_CASE | `ACTIVE_STATUS` | 全大写下划线分隔 |
| CSS类名 | kebab-case | `.user-card` | 小写字母，单词间用连字符分隔 |
| CSS ID | kebab-case | `#main-container` | 小写字母，单词间用连字符分隔 |

### 4.2 命名语义要求

#### 4.2.1 有意义的命名

**正确示例：**
```typescript
// ✅ 清晰表达意图
const userCount = users.length;
const isActive = user.status === 'active';
function calculateTotalPrice(items: CartItem[]): number { }

// ❌ 含义不清
const n = users.length;
const flag = user.status === 'active';
function calc(items: CartItem[]): number { }
```

#### 4.2.2 避免单字母变量

**例外情况：**
- 循环索引：`i`, `j`, `k`
- 坐标：`x`, `y`, `z`
- 数学公式中的通用变量

**示例：**
```typescript
// ✅ 循环索引可以使用单字母
for (let i = 0; i < items.length; i++) {
  console.log(items[i]);
}

// ✅ 其他情况使用有意义的名称
users.forEach((user, index) => {
  console.log(`User ${index}: ${user.name}`);
});
```

#### 4.2.3 布尔类型命名

布尔变量和返回布尔值的函数应使用肯定形式，推荐前缀：
- `is` - 状态判断：`isActive`, `isValid`
- `has` - 拥有判断：`hasPermission`, `hasChildren`
- `can` - 能力判断：`canEdit`, `canDelete`
- `should` - 行为判断：`shouldUpdate`, `shouldRender`

**示例：**
```typescript
// ✅ 布尔变量命名
const isLoading: boolean = true;
const hasError: boolean = false;
const canModify: boolean = checkPermission();

// ✅ 布尔函数命名
function isValidEmail(email: string): boolean { }
function hasUserRole(user: User, role: string): boolean { }
```

### 4.3 命名避免事项

- ❌ 避免使用保留字和关键字
- ❌ 避免使用数字前缀/后缀（除版本号外）
- ❌ 避免使用缩写（通用缩写除外，如 `id`, `url`, `http`）
- ❌ 避免使用否定形式的布尔名称（如 `isNotActive`）

---

## 5. 文件组织规范

### 5.1 推荐目录结构

```
project-root/
├── src/                    # 源代码目录
│   ├── components/         # UI组件
│   │   ├── common/         # 通用组件
│   │   └── features/       # 功能组件
│   ├── services/           # 业务服务
│   ├── utils/              # 工具函数
│   ├── types/              # 类型定义
│   ├── constants/          # 常量定义
│   ├── hooks/              # 自定义Hooks (React)
│   ├── models/             # 数据模型
│   ├── api/                # API接口
│   └── index.ts            # 入口文件
├── tests/                  # 测试文件
│   ├── unit/               # 单元测试
│   ├── integration/        # 集成测试
│   └── e2e/                # 端到端测试
├── docs/                   # 项目文档
│   ├── api/                # API文档
│   └── guides/             # 使用指南
├── config/                 # 配置文件
│   ├── dev/                # 开发环境配置
│   ├── prod/               # 生产环境配置
│   └── test/               # 测试环境配置
├── scripts/                # 构建和部署脚本
│   ├── build/              # 构建脚本
│   └── deploy/             # 部署脚本
├── assets/                 # 静态资源
│   ├── images/             # 图片资源
│   ├── fonts/              # 字体文件
│   └── styles/             # 全局样式
├── .github/                # GitHub配置
│   └── workflows/          # CI/CD工作流
├── package.json            # 项目依赖配置
├── tsconfig.json           # TypeScript配置
├── README.md               # 项目说明文档
└── AGENTS.md               # 开发规则配置文件
```

### 5.2 目录用途说明

| 目录 | 用途 | 说明 |
|-----|------|------|
| `src/` | 源代码 | 所有业务代码存放位置 |
| `src/components/` | UI组件 | 可复用的UI组件 |
| `src/services/` | 业务服务 | 业务逻辑和数据处理 |
| `src/utils/` | 工具函数 | 通用工具函数和辅助方法 |
| `src/types/` | 类型定义 | TypeScript类型、接口定义 |
| `src/constants/` | 常量定义 | 全局常量和枚举值 |
| `tests/` | 测试文件 | 所有测试代码 |
| `docs/` | 项目文档 | 项目相关文档 |
| `config/` | 配置文件 | 环境配置和构建配置 |
| `scripts/` | 脚本文件 | 自动化脚本 |
| `assets/` | 静态资源 | 图片、字体、样式等 |

### 5.3 文件分类规则

#### 5.3.1 同类型文件同目录

相同类型、相关功能的文件应放在同一目录下：

```
src/components/user/
├── user-card.tsx          # 用户卡片组件
├── user-list.tsx          # 用户列表组件
├── user-form.tsx          # 用户表单组件
├── user-types.ts          # 用户相关类型
└── user-utils.ts          # 用户相关工具函数
```

#### 5.3.2 测试文件组织

**方式一：对应目录（推荐）**
```
src/
├── services/
│   ├── user-service.ts
│   └── __tests__/
│       └── user-service.test.ts
```

**方式二：集中存放**
```
src/
├── services/
│   └── user-service.ts
tests/
└── unit/
    └── services/
        └── user-service.test.ts
```

### 5.4 文件命名约定

| 文件类型 | 命名格式 | 示例 |
|---------|---------|------|
| 组件文件 | PascalCase | `UserCard.tsx` |
| 服务文件 | kebab-case | `user-service.ts` |
| 工具文件 | kebab-case | `date-utils.ts` |
| 类型文件 | kebab-case | `user-types.ts` |
| 测试文件 | 源文件名 + .test | `user-service.test.ts` |
| 配置文件 | kebab-case | `app-config.json` |

---

## 6. 版本控制规范

### 6.1 提交信息格式

遵循 **Conventional Commits** 规范：

```
<type>(<scope>): <subject>

<body>

<footer>
```

**格式说明：**
- `type`（必需）：提交类型
- `scope`（可选）：影响范围
- `subject`（必需）：简短描述（50字符以内）
- `body`（可选）：详细描述
- `footer`（可选）：关联Issue或Breaking Change

### 6.2 提交类型定义

| 类型 | 说明 | 示例 |
|-----|------|------|
| `feat` | 新功能 | `feat(auth): 添加用户登录功能` |
| `fix` | Bug修复 | `fix(api): 修复请求超时问题` |
| `docs` | 文档更新 | `docs(readme): 更新安装说明` |
| `style` | 代码格式（不影响功能） | `style: 统一代码缩进格式` |
| `refactor` | 重构（非新功能、非修复） | `refactor(user): 重构用户服务逻辑` |
| `perf` | 性能优化 | `perf(list): 优化列表渲染性能` |
| `test` | 测试相关 | `test(user): 添加用户服务单元测试` |
| `chore` | 构建/工具相关 | `chore: 更新依赖版本` |
| `ci` | CI/CD相关 | `ci: 添加自动化测试流程` |
| `revert` | 回滚提交 | `revert: 回滚登录功能修改` |

### 6.3 提交信息示例

**简单提交：**
```
feat(user): 添加用户头像上传功能
```

**完整提交：**
```
feat(auth): 实现OAuth2.0认证流程

- 添加Google OAuth登录支持
- 添加GitHub OAuth登录支持
- 实现token自动刷新机制

Closes #123
```

**Breaking Change：**
```
feat(api): 重构API响应格式

BREAKING CHANGE: API响应格式从 {data, code} 改为 {data, status}
```

### 6.4 分支命名规范

| 分支类型 | 命名格式 | 示例 | 说明 |
|---------|---------|------|------|
| 主分支 | `main` / `master` | `main` | 生产环境代码 |
| 开发分支 | `develop` | `develop` | 开发环境代码 |
| 功能分支 | `feature/<name>` | `feature/user-auth` | 新功能开发 |
| 修复分支 | `fix/<name>` | `fix/login-error` | Bug修复 |
| 发布分支 | `release/<version>` | `release/v1.2.0` | 版本发布准备 |
| 热修复分支 | `hotfix/<version>-<name>` | `hotfix/v1.1.1-login` | 生产环境紧急修复 |

### 6.5 分支管理策略

推荐使用 **Git Flow** 工作流：

1. **功能开发**：从 `develop` 创建 `feature` 分支
2. **功能完成**：合并回 `develop` 分支
3. **准备发布**：从 `develop` 创建 `release` 分支
4. **发布完成**：合并到 `main` 和 `develop`
5. **紧急修复**：从 `main` 创建 `hotfix` 分支

---

## 7. 附录

### 7.1 参考资源

- [Google Style Guides](https://google.github.io/styleguide/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [TypeScript Official Style Guide](https://www.typescriptlang.org/docs/handbook/declaration-files/do-s-and-don-ts.html)
- [Conventional Commits](https://www.conventionalcommits.org/)
- [Git Flow Workflow](https://www.atlassian.com/git/tutorials/comparing-workflows/gitflow-workflow)
- [JSDoc Documentation](https://jsdoc.app/)

### 7.2 综合示例

以下是一个完整的TypeScript文件示例，展示各项规范的应用：

```typescript
/**
 * @file user-service.ts
 * @description 用户服务模块，提供用户相关的业务操作
 * @author Development Team
 * @date 2024-01-15
 * @lastModified 2024-01-20
 * @version 1.0.0
 */

import { Injectable } from '@nestjs/common';
import { User, IUser } from '../types/user-types';
import { MAX_RETRY_COUNT } from '../constants/app-constants';

/**
 * 用户服务类
 * 提供用户CRUD操作和业务逻辑处理
 * @class UserService
 */
@Injectable()
export class UserService {
  /**
   * 用户数据仓库
   * @private
   */
  private userRepository: UserRepository;

  /**
   * 根据用户ID获取用户信息
   * @param {string} userId - 用户唯一标识符
   * @returns {Promise<User>} 用户信息对象
   * @throws {NotFoundError} 当用户不存在时抛出
   * @example
   * const user = await userService.getUserById('user-123');
   * console.log(user.name);
   */
  async getUserById(userId: string): Promise<User> {
    // 使用缓存减少数据库查询次数
    const cachedUser = this.cache.get(userId);
    if (cachedUser) {
      return cachedUser;
    }

    const user = await this.userRepository.findById(userId);
    if (!user) {
      throw new NotFoundError(`User ${userId} not found`);
    }

    return user;
  }

  /**
   * 检查用户是否具有指定权限
   * @param {User} user - 用户对象
   * @param {string} permission - 权限标识
   * @returns {boolean} 是否具有权限
   */
  hasPermission(user: User, permission: string): boolean {
    return user.permissions.includes(permission);
  }
}
```

### 7.3 规则更新机制

本开发规则文档将根据项目发展和技术演进持续更新：

1. **版本控制**：每次更新增加版本号
2. **变更记录**：在文档末尾维护变更历史
3. **团队评审**：重大变更需团队评审通过
4. **向后兼容**：尽量保持规则的向后兼容性

### 7.4 变更历史

| 版本 | 日期 | 变更内容 | 作者 |
|-----|------|---------|------|
| 1.0.0 | 2024-01-15 | 初始版本创建 | Development Team |

---

**文档结束**
