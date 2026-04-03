# 技术设计文档：AGENTS.md 开发规则配置文件

## 1. 设计概述

### 1.1 设计目标
将需求规格中定义的"创建AGENTS.md开发规则配置文件"需求转化为具体的技术实现方案，明确文件结构、内容格式和具体规则定义。

### 1.2 技术选型
| 技术项 | 选择 | 说明 |
|--------|------|------|
| 文件格式 | Markdown | 通用文档格式，易于阅读和版本控制 |
| 编码格式 | UTF-8 (无BOM) | 国际标准，支持多语言 |
| 文档结构 | 模块化章节 | 便于维护和扩展 |

### 1.3 设计原则
- **简洁性**：规则描述清晰简洁，避免冗余
- **可执行性**：规则具体可操作，无歧义
- **可扩展性**：结构模块化，便于后续添加新规则
- **兼容性**：适用于多种编程语言和开发场景

---

## 2. 架构设计

### 2.1 文件位置
```
project-root/
├── AGENTS.md          ← 目标文件位置
├── .gitignore
├── package.json
└── src/
```

### 2.2 文档结构设计

```
AGENTS.md
├── 1. 概述
│   ├── 目的
│   └── 适用范围
├── 2. 文件编码规范
│   ├── 默认编码
│   └── 特殊文件处理
├── 3. 注释规范
│   ├── 文件头注释
│   ├── 函数/方法注释
│   └── 行内注释
├── 4. 命名规范
│   ├── 文件命名
│   ├── 变量命名
│   ├── 函数命名
│   └── 类/接口命名
├── 5. 文件组织规范
│   ├── 目录结构
│   └── 文件分类
├── 6. 版本控制规范
│   ├── 提交信息格式
│   └── 分支命名
└── 7. 附录
    └── 示例代码
```

---

## 3. 详细设计

### 3.1 文件编码规范设计

#### 3.1.1 默认编码规则
| 文件类型 | 编码格式 | 说明 |
|----------|----------|------|
| 源代码文件 (.js, .ts, .py, .java, etc.) | UTF-8 without BOM | 默认标准 |
| 配置文件 (.json, .yaml, .xml) | UTF-8 without BOM | 保持一致性 |
| 文档文件 (.md, .txt) | UTF-8 without BOM | 支持多语言 |
| Shell脚本 (.sh) | UTF-8 | Unix环境 |

#### 3.1.2 特殊文件处理
| 文件类型 | 编码格式 | 原因 |
|----------|----------|------|
| Windows批处理 (.bat, .cmd) | GBK/GB2312 | Windows系统兼容 |
| PowerShell脚本 (.ps1) | UTF-8 with BOM | PowerShell要求 |

### 3.2 注释规范设计

#### 3.2.1 文件头注释模板
```typescript
/**
 * @file 文件名称
 * @description 文件功能描述
 * @author 作者名称
 * @date 创建日期
 * @lastModified 最后修改日期
 */
```

#### 3.2.2 函数/方法注释模板
```typescript
/**
 * 函数功能描述
 * @param {类型} 参数名 - 参数说明
 * @returns {类型} 返回值说明
 * @throws {类型} 异常说明（可选）
 * @example
 * // 使用示例
 * functionName(param);
 */
```

#### 3.2.3 行内注释规范
- 使用 `//` 进行单行注释
- 注释与代码之间保留一个空格
- 注释应说明"为什么"而非"是什么"

### 3.3 命名规范设计

#### 3.3.1 命名约定矩阵
| 元素类型 | 命名风格 | 示例 | 说明 |
|----------|----------|------|------|
| 文件名 | kebab-case | `user-service.ts` | 小写+连字符 |
| 变量名 | camelCase | `userName` | 首字母小写驼峰 |
| 常量名 | UPPER_SNAKE_CASE | `MAX_COUNT` | 全大写+下划线 |
| 函数名 | camelCase | `getUserInfo()` | 首字母小写驼峰 |
| 类名 | PascalCase | `UserService` | 首字母大写驼峰 |
| 接口名 | PascalCase (I前缀可选) | `IUserService` | 首字母大写驼峰 |
| 枚举名 | PascalCase | `UserStatus` | 首字母大写驼峰 |
| CSS类名 | kebab-case | `.user-card` | 小写+连字符 |

#### 3.3.2 命名语义要求
- 使用有意义的英文单词或缩写
- 避免使用单字母变量（循环变量除外）
- 布尔类型使用 `is`, `has`, `can` 等前缀
- 私有成员使用 `_` 前缀（可选）

### 3.4 文件组织规范设计

#### 3.4.1 推荐目录结构
```
project-root/
├── src/                    # 源代码目录
│   ├── components/         # 组件目录
│   ├── services/           # 服务目录
│   ├── utils/              # 工具函数目录
│   ├── types/              # 类型定义目录
│   └── constants/          # 常量定义目录
├── tests/                  # 测试文件目录
├── docs/                   # 文档目录
├── config/                 # 配置文件目录
└── scripts/                # 脚本目录
```

#### 3.4.2 文件分类规则
- 同类型文件放置于同一目录
- 测试文件与源文件对应或统一存放于 `tests/`
- 配置文件统一存放于 `config/` 或项目根目录

### 3.5 版本控制规范设计

#### 3.5.1 提交信息格式
```
<type>(<scope>): <subject>

<body>

<footer>
```

#### 3.5.2 提交类型定义
| 类型 | 说明 | 示例 |
|------|------|------|
| feat | 新功能 | feat(auth): add login feature |
| fix | 修复bug | fix(api): resolve timeout issue |
| docs | 文档更新 | docs(readme): update installation guide |
| style | 代码格式 | style: fix indentation |
| refactor | 重构 | refactor(user): optimize validation logic |
| test | 测试 | test(auth): add unit tests |
| chore | 构建/工具 | chore: update dependencies |

#### 3.5.3 分支命名规范
| 分支类型 | 命名格式 | 示例 |
|----------|----------|------|
| 功能分支 | feature/<feature-name> | feature/user-auth |
| 修复分支 | fix/<bug-name> | fix/login-timeout |
| 发布分支 | release/<version> | release/1.0.0 |
| 热修复分支 | hotfix/<version>-<name> | hotfix/1.0.1-critical |

---

## 4. 接口设计

### 4.1 文件内容接口

AGENTS.md 文件内容应包含以下主要章节：

```markdown
# AGENTS.md - 开发规则指南

## 1. 概述
- 目的说明
- 适用范围定义

## 2. 文件编码规范
- 默认编码要求
- 特殊文件处理规则

## 3. 注释规范
- 文件头注释格式
- 函数/方法注释格式
- 行内注释规则

## 4. 命名规范
- 各类型元素命名约定
- 语义化命名要求

## 5. 文件组织规范
- 目录结构约定
- 文件分类规则

## 6. 版本控制规范
- 提交信息格式
- 分支命名约定

## 7. 附录
- 示例代码
- 参考资源
```

---

## 5. 数据模型

### 5.1 规则条目模型
```typescript
interface RuleItem {
  id: string;           // 规则ID，如 "ENC-001"
  category: string;     // 规则类别
  title: string;        // 规则标题
  description: string;  // 规则描述
  examples?: string[];  // 示例（可选）
  exceptions?: string;  // 例外情况（可选）
}
```

### 5.2 规则类别枚举
```typescript
enum RuleCategory {
  ENCODING = 'encoding',       // 编码规范
  COMMENT = 'comment',         // 注释规范
  NAMING = 'naming',           // 命名规范
  ORGANIZATION = 'organization', // 文件组织
  VERSION_CONTROL = 'version-control' // 版本控制
}
```

---

## 6. 实现约束

### 6.1 必须满足
- 文件必须使用 UTF-8 编码保存
- 文件必须位于项目根目录
- 内容必须符合 Markdown 语法规范
- 所有规则必须有明确说明

### 6.2 建议遵循
- 每个章节包含示例代码
- 使用表格呈现结构化信息
- 保持章节编号一致性

---

## 7. 验证方案

### 7.1 文件验证
| 验证项 | 验证方法 | 预期结果 |
|--------|----------|----------|
| 文件存在 | 检查根目录 | AGENTS.md 存在 |
| 编码正确 | 检测文件编码 | UTF-8 without BOM |
| 格式正确 | Markdown解析 | 无语法错误 |
| 内容完整 | 章节检查 | 包含所有必需章节 |

### 7.2 内容验证
- 所有规则类别已定义
- 规则描述清晰无歧义
- 示例代码格式正确
- 表格结构完整

---

## 8. 风险与缓解

| 风险 | 影响 | 缓解措施 |
|------|------|----------|
| 规则过于严格 | 开发效率降低 | 提供合理的例外条款 |
| 规则不完整 | 开发标准不统一 | 预留扩展章节 |
| 规则过时 | 误导开发者 | 定期审查更新机制 |

---

## 9. 附录

### 9.1 参考资源
- [Google Style Guides](https://google.github.io/styleguide/)
- [Airbnb JavaScript Style Guide](https://github.com/airbnb/javascript)
- [Conventional Commits](https://www.conventionalcommits.org/)

### 9.2 版本历史
| 版本 | 日期 | 说明 |
|------|------|------|
| 1.0 | 待定 | 初始版本 |
