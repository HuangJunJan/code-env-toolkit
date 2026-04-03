# Windows开发环境配置清单

## 1. 基础工具层（必装）

### 1.1 版本控制
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Git** | 版本控制系统 | ⭐⭐⭐ 必装 | 引导脚本自动安装 |
| **Git LFS** | 大文件存储 | ⭐⭐ 推荐 | Git扩展，按需安装 |

### 1.2 终端工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Windows Terminal** | 现代化终端 | ⭐⭐⭐ 推荐 | Microsoft Store / winget |
| **PowerShell 7+** | 增强版PowerShell | ⭐⭐ 推荐 | winget / MSI安装 |

---

## 2. 前端开发环境

### 2.1 Node.js生态
| 工具 | 说明 | 必要性 | 版本管理 |
|-----|------|--------|---------|
| **FNM** | Node.js版本管理器 | ⭐⭐⭐ 必装 | 引导脚本安装 |
| **Node.js** | JavaScript运行时 | ⭐⭐⭐ 必装 | FNM管理多版本 |
| **NRM** | NPM源管理器 | ⭐⭐⭐ 推荐 | npm全局安装 |
| **PNPM** | 高效包管理器 | ⭐⭐⭐ 推荐 | npm全局安装 |

**Node.js常用版本：**
- LTS版本：20.x, 18.x, 16.x
- 当前版本：21.x

**NPM源选项：**
- 淘宝源：https://registry.npmmirror.com
- 腾讯源：https://mirrors.cloud.tencent.com/npm/
- npm官方：https://registry.npmjs.org

### 2.2 前端框架CLI
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **@vue/cli** | Vue项目脚手架 | ⭐⭐ 按需 | npm全局安装 |
| **create-vite** | Vite项目创建工具 | ⭐⭐⭐ 推荐 | npm/npx |
| **create-react-app** | React项目脚手架 | ⭐⭐ 按需 | npx |
| **@angular/cli** | Angular项目脚手架 | ⭐⭐ 按需 | npm全局安装 |

### 2.3 前端构建工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Vite** | 下一代前端构建工具 | ⭐⭐⭐ 推荐 | 项目依赖 |
| **Webpack** | 模块打包器 | ⭐⭐ 按需 | 项目依赖 |
| **Rollup** | JavaScript模块打包器 | ⭐⭐ 按需 | 项目依赖 |
| **esbuild** | 极速JavaScript打包器 | ⭐⭐ 按需 | 项目依赖 |

### 2.4 代码质量工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **ESLint** | JavaScript代码检查 | ⭐⭐⭐ 推荐 | 项目依赖 |
| **Prettier** | 代码格式化工具 | ⭐⭐⭐ 推荐 | 项目依赖 |
| **Stylelint** | CSS/Less/Sass代码检查 | ⭐⭐ 推荐 | 项目依赖 |
| **Husky** | Git hooks工具 | ⭐⭐ 推荐 | 项目依赖 |
| **lint-staged** | 暂存文件lint | ⭐⭐ 推荐 | 项目依赖 |

### 2.5 测试工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Jest** | JavaScript测试框架 | ⭐⭐⭐ 推荐 | 项目依赖 |
| **Vitest** | Vite原生测试框架 | ⭐⭐⭐ 推荐 | 项目依赖 |
| **Cypress** | E2E测试框架 | ⭐⭐ 推荐 | 项目依赖 |
| **Playwright** | 跨浏览器测试 | ⭐⭐ 推荐 | 项目依赖 |

---

## 3. 后端开发环境

### 3.1 Python环境
| 工具 | 说明 | 必要性 | 版本管理 |
|-----|------|--------|---------|
| **pyenv-win** | Python版本管理器 | ⭐⭐⭐ 推荐 | PowerShell安装 |
| **Python** | Python解释器 | ⭐⭐⭐ 必装 | pyenv管理多版本 |
| **pip** | Python包管理器 | ⭐⭐⭐ 必装 | Python自带 |
| **poetry** | Python依赖管理 | ⭐⭐⭐ 推荐 | pip安装 |
| **virtualenv** | 虚拟环境管理 | ⭐⭐ 推荐 | pip安装 |

**Python常用版本：**
- 3.12.x（最新）
- 3.11.x（稳定）
- 3.10.x（LTS）
- 3.9.x（兼容性）

### 3.2 Java环境
| 工具 | 说明 | 必要性 | 版本管理 |
|-----|------|--------|---------|
| **scoop/sdkman** | Java版本管理器 | ⭐⭐⭐ 推荐 | 脚本安装 |
| **JDK** | Java开发工具包 | ⭐⭐⭐ 必装 | 版本管理器安装 |
| **Maven** | Java项目构建工具 | ⭐⭐⭐ 推荐 | scoop安装 |
| **Gradle** | Java构建自动化工具 | ⭐⭐⭐ 推荐 | scoop安装 |

**JDK常用版本：**
- JDK 21（LTS）
- JDK 17（LTS）
- JDK 11（LTS）
- JDK 8（兼容性）

### 3.3 Go环境
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Go** | Go语言环境 | ⭐⭐ 按需 | 官方MSI安装 |
| **gvm** | Go版本管理器 | ⭐⭐ 推荐 | PowerShell安装 |

**Go常用版本：**
- 1.22.x（最新）
- 1.21.x（稳定）

### 3.4 Rust环境
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Rustup** | Rust工具链管理器 | ⭐⭐ 按需 | 官方脚本安装 |
| **Cargo** | Rust包管理器 | ⭐⭐ 按需 | Rustup自带 |

### 3.5 .NET环境
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **dotnet** | .NET SDK | ⭐⭐ 按需 | 官方安装包 |
| **dotnet-cli** | .NET命令行工具 | ⭐⭐ 按需 | SDK自带 |

**.NET常用版本：**
- .NET 8（LTS）
- .NET 7
- .NET 6（LTS）

---

## 4. 数据库环境

### 4.1 关系型数据库
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **MySQL** | 关系型数据库 | ⭐⭐ 按需 | MSI安装 / Docker |
| **PostgreSQL** | 对象关系型数据库 | ⭐⭐ 按需 | MSI安装 / Docker |
| **SQL Server** | 微软关系型数据库 | ⭐⭐ 按需 | MSI安装 / Docker |
| **SQLite** | 嵌入式数据库 | ⭐⭐ 推荐 | scoop安装 |

### 4.2 NoSQL数据库
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **MongoDB** | 文档数据库 | ⭐⭐ 按需 | MSI安装 / Docker |
| **Redis** | 内存数据库 | ⭐⭐ 按需 | MSI安装 / Docker |
| **Elasticsearch** | 搜索引擎 | ⭐⭐ 按需 | Docker |

### 4.3 数据库工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **DBeaver** | 通用数据库工具 | ⭐⭐⭐ 推荐 | scoop / MSI安装 |
| **DataGrip** | JetBrains数据库IDE | ⭐⭐ 推荐 | Toolbox安装 |
| **Navicat** | 数据库管理工具 | ⭐⭐ 推荐 | MSI安装 |

---

## 5. 容器与虚拟化

### 5.1 容器工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Docker Desktop** | Docker桌面版 | ⭐⭐⭐ 推荐 | MSI安装 |
| **Docker Compose** | 多容器编排工具 | ⭐⭐⭐ 推荐 | Docker Desktop自带 |
| **Podman** | 无守护进程容器 | ⭐⭐ 按需 | MSI安装 |

### 5.2 虚拟化工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **WSL2** | Windows子系统Linux | ⭐⭐⭐ 推荐 | Windows功能 |
| **VirtualBox** | 虚拟机软件 | ⭐⭐ 按需 | MSI安装 |
| **Vagrant** | 虚拟环境管理 | ⭐⭐ 按需 | MSI安装 |

---

## 6. 开发工具（IDE/编辑器）

### 6.1 代码编辑器
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **VS Code** | 轻代码编辑器 | ⭐⭐⭐ 必装 | MSI / scoop安装 |
| **Sublime Text** | 轻量级编辑器 | ⭐⭐ 推荐 | MSI安装 |
| **Notepad++** | 文本编辑器 | ⭐⭐ 推荐 | MSI安装 |

### 6.2 IDE
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **WebStorm** | 前端IDE | ⭐⭐ 推荐 | Toolbox安装 |
| **IntelliJ IDEA** | Java IDE | ⭐⭐ 推荐 | Toolbox安装 |
| **PyCharm** | Python IDE | ⭐⭐ 推荐 | Toolbox安装 |
| **GoLand** | Go IDE | ⭐⭐ 推荐 | Toolbox安装 |
| **Visual Studio** | .NET IDE | ⭐⭐ 按需 | MSI安装 |

### 6.3 JetBrains工具管理
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **JetBrains Toolbox** | JetBrains工具管理器 | ⭐⭐⭐ 推荐 | MSI安装 |

---

## 7. 构建与包管理工具

### 7.1 通用包管理器
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Scoop** | Windows命令行包管理器 | ⭐⭐⭐ 推荐 | PowerShell脚本安装 |
| **Chocolatey** | Windows包管理器 | ⭐⭐ 推荐 | PowerShell脚本安装 |
| **Winget** | Windows官方包管理器 | ⭐⭐⭐ 推荐 | Windows 11自带 |

### 7.2 构建工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **CMake** | 跨平台构建工具 | ⭐⭐ 按需 | scoop / MSI安装 |
| **Make** | 构建自动化工具 | ⭐⭐ 按需 | scoop安装 |
| **Ninja** | 小型构建系统 | ⭐⭐ 按需 | scoop安装 |

---

## 8. 网络与API工具

### 8.1 API测试工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Postman** | API测试平台 | ⭐⭐⭐ 推荐 | MSI安装 |
| **Insomnia** | REST客户端 | ⭐⭐ 推荐 | MSI安装 |
| **HTTPie** | 命令行HTTP客户端 | ⭐⭐ 推荐 | pip安装 |

### 8.2 网络工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **curl** | 数据传输工具 | ⭐⭐⭐ 必装 | Windows 10+自带 |
| **wget** | 文件下载工具 | ⭐⭐ 推荐 | scoop安装 |
| **OpenSSH** | SSH客户端 | ⭐⭐⭐ 推荐 | Windows 10+自带 |
| **PuTTY** | SSH/Telnet客户端 | ⭐⭐ 推荐 | MSI安装 |

---

## 9. 文档与协作工具

### 9.1 文档工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Typora** | Markdown编辑器 | ⭐⭐⭐ 推荐 | MSI安装 |
| **Obsidian** | 知识管理工具 | ⭐⭐ 推荐 | MSI安装 |
| **Notion** | 协作文档工具 | ⭐⭐ 推荐 | Web / 桌面应用 |

### 9.2 设计工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **Figma** | UI设计工具 | ⭐⭐ 推荐 | Web / 桌面应用 |
| **Draw.io** | 流程图工具 | ⭐⭐ 推荐 | VS Code插件 / Web |

---

## 10. 系统工具

### 10.1 压缩工具
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **7-Zip** | 开源压缩软件 | ⭐⭐⭐ 推荐 | MSI安装 |
| **WinRAR** | 压缩软件 | ⭐⭐ 推荐 | MSI安装 |

### 10.2 系统增强
| 工具 | 说明 | 必要性 | 安装方式 |
|-----|------|--------|---------|
| **PowerToys** | Windows增强工具集 | ⭐⭐⭐ 推荐 | GitHub / winget |
| **Everything** | 文件搜索工具 | ⭐⭐⭐ 推荐 | MSI安装 |
| **TreeSize** | 磁盘空间分析 | ⭐⭐ 推荐 | MSI安装 |

---

## 11. 推荐安装组合

### 11.1 前端开发者
```
必装：
✅ Git
✅ Node.js (FNM管理)
✅ NRM + PNPM
✅ VS Code

推荐：
⭐ Windows Terminal
⭐ PowerShell 7+
⭐ Docker Desktop
⭐ Postman
⭐ Typora
⭐ 7-Zip
⭐ PowerToys
```

### 11.2 全栈开发者
```
必装：
✅ Git
✅ Node.js (FNM管理)
✅ Python (pyenv-win管理)
✅ VS Code
✅ Docker Desktop

推荐：
⭐ JDK (scoop管理)
⭐ DBeaver
⭐ Postman
⭐ JetBrains Toolbox
⭐ WSL2
```

### 11.3 后端开发者
```
必装：
✅ Git
✅ JDK (scoop管理)
✅ Maven / Gradle
✅ Docker Desktop

推荐：
⭐ Python
⭐ Go
⭐ DBeaver
⭐ IntelliJ IDEA
⭐ Postman
⭐ WSL2
```

### 11.4 Python开发者
```
必装：
✅ Git
✅ Python (pyenv-win管理)
✅ poetry / pipenv
✅ VS Code

推荐：
⭐ PyCharm
⭐ Jupyter Notebook
⭐ DBeaver
⭐ Postman
⭐ Docker Desktop
```

---

## 12. 环境变量配置建议

### 12.1 目录结构
```
D:\dev\
├── nodejs\           # Node.js环境
│   ├── fnm\         # FNM安装目录
│   ├── global\      # NPM全局包
│   └── cache\       # NPM缓存
├── python\          # Python环境
│   ├── pyenv-win\   # pyenv安装目录
│   └── virtualenvs\ # 虚拟环境
├── java\            # Java环境
│   ├── jdk-21\      # JDK 21
│   ├── jdk-17\      # JDK 17
│   └── maven\       # Maven
├── go\              # Go环境
├── rust\            # Rust环境
├── tools\           # 通用工具
│   ├── git\         # Git
│   ├── scoop\       # Scoop
│   └── scripts\     # 自定义脚本
└── projects\        # 项目目录
```

### 12.2 环境变量示例
```powershell
# Node.js
FNM_DIR=D:\dev\nodejs\fnm
NPM_GLOBAL=D:\dev\nodejs\global
PATH=%PATH%;%NPM_GLOBAL%

# Python
PYENV=D:\dev\python\pyenv-win
PATH=%PATH%;%PYENV%\bin

# Java
JAVA_HOME=D:\dev\java\jdk-17
PATH=%PATH%;%JAVA_HOME%\bin

# Go
GOPATH=D:\dev\go
PATH=%PATH%;%GOPATH%\bin

# Rust
RUSTUP_HOME=D:\dev\rust\rustup
CARGO_HOME=D:\dev\rust\cargo
PATH=%PATH%;%CARGO_HOME%\bin
```

---

## 13. 配置优先级

### 高优先级（必装）
1. Git - 版本控制基础
2. Node.js - 前端开发核心
3. VS Code - 代码编辑器
4. Docker Desktop - 容器化开发

### 中优先级（推荐）
1. Python - 脚本和后端开发
2. JDK - Java开发
3. Scoop - 包管理器
4. Windows Terminal - 终端增强
5. Postman - API测试

### 低优先级（按需）
1. Go / Rust - 特定语言开发
2. 数据库 - 本地开发需要
3. IDE - 大型项目开发
4. 虚拟化工具 - 特殊需求

---

**总结：根据开发方向选择合适的工具组合，优先安装高优先级工具，逐步完善开发环境。**
