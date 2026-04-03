# 前端 + Go 开发环境清单

> 仅包含开发环境本身，不包含项目依赖的工具（如ESLint、Webpack等）

---

## 一、前端开发环境

### 1. Node.js 版本管理器

#### FNM (Fast Node Manager) ⭐ 推荐
- **说明**：Rust编写的Node.js版本管理器，速度极快
- **必要性**：⭐⭐⭐ 必装
- **功能**：
  - 安装/切换多个Node.js版本
  - 自动切换项目所需版本（.node-version、.nvmrc）
  - 跨平台支持
- **安装方式**：引导脚本自动安装
- **配置项**：
  - 安装目录
  - 代理地址
  - 多线程下载

#### NVM for Windows（备选）
- **说明**：NVM的Windows实现
- **必要性**：⭐⭐ 备选
- **功能**：Node.js版本管理
- **安装方式**：MSI安装包

---

### 2. Node.js 运行时

#### Node.js
- **说明**：JavaScript运行时环境
- **必要性**：⭐⭐⭐ 必装
- **版本选择**：
  - **LTS版本**（推荐）：
    - 20.x（当前LTS）
    - 18.x（维护LTS）
    - 16.x（维护LTS）
  - **Current版本**：
    - 21.x（最新特性）
- **安装方式**：通过FNM安装
- **配置项**：
  - 全局包安装目录
  - 缓存目录
  - 镜像源

---

### 3. NPM 源管理器

#### NRM (NPM Registry Manager)
- **说明**：NPM源切换工具
- **必要性**：⭐⭐⭐ 推荐
- **功能**：
  - 快速切换NPM源
  - 测试源速度
  - 添加自定义源
- **常用源**：
  - 淘宝源：`https://registry.npmmirror.com`
  - 腾讯源：`https://mirrors.cloud.tencent.com/npm/`
  - 华为源：`https://repo.huaweicloud.com/repository/npm/`
  - npm官方：`https://registry.npmjs.org`
- **安装方式**：`npm install -g nrm`
- **配置项**：
  - 默认源
  - 自定义源列表

---

### 4. 包管理器

#### PNPM ⭐ 推荐
- **说明**：快速、磁盘空间高效的包管理器
- **必要性**：⭐⭐⭐ 推荐
- **优势**：
  - 节省磁盘空间（硬链接）
  - 安装速度快
  - 严格的依赖管理
  - Monorepo支持
- **安装方式**：`npm install -g pnpm`
- **配置项**：
  - store目录（全局存储）
  - 镜像源
  - 代理设置

#### Yarn（备选）
- **说明**：Facebook开发的包管理器
- **必要性**：⭐⭐ 备选
- **版本**：
  - Yarn 1.x（Classic）
  - Yarn 2+（Berry）
- **安装方式**：`npm install -g yarn` 或 `corepack enable`

#### NPM（默认）
- **说明**：Node.js默认包管理器
- **必要性**：⭐⭐⭐ 自带
- **配置项**：
  - 全局安装目录
  - 缓存目录
  - 镜像源

---

### 5. 前端环境配置项汇总

```json
{
  "frontend": {
    "fnm": {
      "installDir": "D:\\dev\\nodejs\\fnm",
      "proxy": "http://127.0.0.1:7890",
      "multiThread": true
    },
    "node": {
      "version": "20.11.0",
      "globalDir": "D:\\dev\\nodejs\\global",
      "cacheDir": "D:\\dev\\nodejs\\cache"
    },
    "nrm": {
      "registry": "taobao",
      "customRegistries": []
    },
    "pnpm": {
      "version": "latest",
      "storeDir": "D:\\dev\\pnpm-store",
      "proxy": "http://127.0.0.1:7890"
    }
  }
}
```

---

## 二、Go 开发环境

### 1. Go 版本管理器

#### GVM (Go Version Manager) - Windows
- **说明**：Go版本管理器（Windows实现）
- **必要性**：⭐⭐⭐ 推荐
- **功能**：
  - 安装/切换多个Go版本
  - 管理GOPATH
- **安装方式**：PowerShell脚本安装
- **配置项**：
  - 安装目录
  - 代理地址（GOPROXY）

#### Go官方安装（备选）
- **说明**：官方MSI安装包
- **必要性**：⭐⭐ 备选
- **特点**：简单直接，但不支持多版本
- **安装方式**：MSI安装包

---

### 2. Go 运行时

#### Go
- **说明**：Go编程语言环境
- **必要性**：⭐⭐⭐ 必装
- **版本选择**：
  - **最新稳定版**：
    - 1.22.x
    - 1.21.x
  - **维护版本**：
    - 1.20.x
    - 1.19.x
- **安装方式**：通过GVM或官方MSI
- **配置项**：
  - GOROOT（Go安装目录）
  - GOPATH（工作空间）
  - GOPROXY（模块代理）
  - GOSUMDB（校验和数据库）

---

### 3. Go 模块代理

#### GOPROXY 配置
- **说明**：Go模块下载代理
- **必要性**：⭐⭐⭐ 必配
- **常用代理**：
  - **七牛云**：`https://goproxy.cn`
  - **阿里云**：`https://mirrors.aliyun.com/goproxy/`
  - **官方**：`https://proxy.golang.org`
  - **直连**：`direct`
- **推荐配置**：
  ```bash
  # 国内环境
  go env -w GOPROXY=https://goproxy.cn,direct

  # 或多个代理
  go env -w GOPROXY=https://goproxy.cn,https://proxy.golang.org,direct
  ```

#### GOSUMDB 配置
- **说明**：Go模块校验和数据库
- **必要性**：⭐⭐ 推荐
- **配置**：
  ```bash
  # 国内环境
  go env -w GOSUMDB=sum.golang.org

  # 或使用国内镜像
  go env -w GOSUMDB=sum.golang.google.cn
  ```

---

### 4. Go 开发工具

#### Go工具链（自带）
- **go build**：编译
- **go run**：运行
- **go test**：测试
- **go mod**：模块管理
- **go get**：依赖下载
- **go fmt**：代码格式化
- **go vet**：静态检查
- **go doc**：文档生成

#### Delve（调试器）
- **说明**：Go调试工具
- **必要性**：⭐⭐⭐ 推荐
- **安装方式**：`go install github.com/go-delve/delve/cmd/dlv@latest`
- **用途**：VS Code / GoLand调试支持

#### gopls（语言服务器）
- **说明**：Go语言服务器
- **必要性**：⭐⭐⭐ 推荐
- **安装方式**：VS Code Go插件自动安装
- **用途**：代码补全、跳转、重构等

---

### 5. Go 环境配置项汇总

```json
{
  "go": {
    "gvm": {
      "installDir": "D:\\dev\\go\\gvm"
    },
    "go": {
      "version": "1.22.0",
      "goroot": "D:\\dev\\go\\go1.22.0",
      "gopath": "D:\\dev\\go\\gopath",
      "goproxy": "https://goproxy.cn,direct",
      "gosumdb": "sum.golang.org",
      "goprivate": "*.internal.company.com"
    },
    "tools": {
      "delve": true,
      "gopls": true
    }
  }
}
```

---

## 三、环境变量配置

### 1. 前端环境变量

```powershell
# FNM
FNM_DIR=D:\dev\nodejs\fnm

# Node.js (通过FNM自动配置)
# 或手动配置
NODE_HOME=D:\dev\nodejs\node20
NPM_GLOBAL=D:\dev\nodejs\global
NPM_CACHE=D:\dev\nodejs\cache

# PATH
PATH=%PATH%;%FNM_DIR%
PATH=%PATH%;%NPM_GLOBAL%
```

### 2. Go 环境变量

```powershell
# Go
GOROOT=D:\dev\go\go1.22.0
GOPATH=D:\dev\go\gopath
GOPROXY=https://goproxy.cn,direct
GOSUMDB=sum.golang.org
GOPRIVATE=*.internal.company.com

# PATH
PATH=%PATH%;%GOROOT%\bin
PATH=%PATH%;%GOPATH%\bin
```

---

## 四、安装顺序

### 前端环境安装顺序
```
1. FNM (版本管理器)
   ↓
2. Node.js (通过FNM安装)
   ↓
3. 配置NPM全局路径和缓存
   ↓
4. NRM (源管理器)
   ↓
5. 切换NPM源
   ↓
6. PNPM (包管理器)
   ↓
7. 配置PNPM
   ↓
✅ 完成
```

### Go 环境安装顺序
```
1. GVM (版本管理器，可选)
   ↓
2. Go (通过GVM或官方安装)
   ↓
3. 配置GOPROXY
   ↓
4. 配置GOSUMDB
   ↓
5. 安装Delve (调试器)
   ↓
6. 安装gopls (语言服务器)
   ↓
✅ 完成
```

---

## 五、目录结构建议

```
D:\dev\
├── nodejs\              # 前端环境
│   ├── fnm\            # FNM安装目录
│   │   ├── node-20\    # Node.js 20.x
│   │   ├── node-18\    # Node.js 18.x
│   │   └── node-16\    # Node.js 16.x
│   ├── global\         # NPM全局包
│   ├── cache\          # NPM缓存
│   └── pnpm-store\     # PNPM存储
│
├── go\                  # Go环境
│   ├── gvm\            # GVM安装目录（可选）
│   ├── go1.22\         # Go 1.22.x
│   ├── go1.21\         # Go 1.21.x
│   └── gopath\         # Go工作空间
│       ├── src\        # 源代码
│       ├── pkg\        # 包对象
│       └── bin\        # 可执行文件
│
└── projects\            # 项目目录
    ├── frontend\       # 前端项目
    └── golang\         # Go项目
```

---

## 六、配置文件示例

### 完整配置文件

```json
{
  "version": "1.0.0",
  "environments": {
    "frontend": {
      "enabled": true,
      "installDir": "D:\\dev\\nodejs",
      "fnm": {
        "version": "latest",
        "installDir": "D:\\dev\\nodejs\\fnm",
        "proxy": "http://127.0.0.1:7890",
        "multiThread": true
      },
      "node": {
        "strategy": "lts",           // 版本策略: lts | current | specific | interactive
        "default": "20.11.0",        // 默认版本（LTS）
        "versions": ["20.11.0", "18.19.0", "16.20.0"],  // 已安装版本列表
        "globalDir": "D:\\dev\\nodejs\\global",
        "cacheDir": "D:\\dev\\nodejs\\cache",
        "autoSwitch": true           // 自动切换项目版本
      },
      "nrm": {
        "registry": "taobao",
        "registries": {
          "npm": "https://registry.npmjs.org",
          "taobao": "https://registry.npmmirror.com",
          "tencent": "https://mirrors.cloud.tencent.com/npm/",
          "huawei": "https://repo.huaweicloud.com/repository/npm/"
        }
      },
      "pnpm": {
        "version": "8.x",
        "storeDir": "D:\\dev\\pnpm-store",
        "proxy": "http://127.0.0.1:7890"
      }
    },
    "go": {
      "enabled": true,
      "installDir": "D:\\dev\\go",
      "gvm": {
        "enabled": true,
        "installDir": "D:\\dev\\go\\gvm"
      },
      "go": {
        "strategy": "stable",        // 版本策略: stable | latest | specific | interactive
        "default": "1.22.0",         // 默认版本（稳定版）
        "versions": ["1.22.0", "1.21.6", "1.20.13"],  // 已安装版本列表
        "gopath": "D:\\dev\\go\\gopath",
        "goproxy": "https://goproxy.cn,direct",
        "gosumdb": "sum.golang.org",
        "goprivate": "",
        "autoSwitch": true           // 自动切换项目版本
      },
      "tools": {
        "delve": true,
        "gopls": true
      }
    }
  },
  "proxy": {
    "http": "http://127.0.0.1:7890",
    "https": "http://127.0.0.1:7890"
  }
}
```

---

## 七、安装命令示例

### 前端环境安装命令

```powershell
# 1. 安装FNM (通过引导脚本)
# 或手动安装
Invoke-WebRequest -Uri https://github.com/Schniz/fnm/releases/latest/download/fnm-windows.zip -OutFile fnm.zip
Expand-Archive fnm.zip -DestinationPath C:\fnm

# 2. 安装Node.js
fnm install 20.11.0
fnm use 20.11.0

# 3. 配置NPM
npm config set prefix "D:\dev\nodejs\global"
npm config set cache "D:\dev\nodejs\cache"

# 4. 安装NRM
npm install -g nrm
nrm use taobao

# 5. 安装PNPM
npm install -g pnpm

# 6. 配置PNPM
pnpm config set store-dir "D:\dev\pnpm-store"
```

### Go 环境安装命令

```powershell
# 1. 下载Go (官方方式)
# 或使用GVM

# 2. 配置环境变量
go env -w GOPROXY=https://goproxy.cn,direct
go env -w GOSUMDB=sum.golang.org
go env -w GOPATH=D:\dev\go\gopath

# 3. 安装Delve
go install github.com/go-delve/delve/cmd/dlv@latest

# 4. 安装gopls (通常由编辑器自动安装)
go install golang.org/x/tools/gopls@latest
```

---

## 八、验证安装

### 前端环境验证

```powershell
# 检查FNM
fnm --version

# 检查Node.js
node --version
npm --version

# 检查NRM
nrm --version
nrm ls

# 检查PNPM
pnpm --version
```

### Go 环境验证

```powershell
# 检查Go
go version
go env

# 检查Delve
dlv version

# 检查gopls
gopls version
```

---

## 总结

### 前端环境（5个核心组件）
1. ✅ **FNM** - Node.js版本管理器
2. ✅ **Node.js** - JavaScript运行时
3. ✅ **NRM** - NPM源管理器
4. ✅ **PNPM** - 包管理器
5. ✅ **NPM** - 默认包管理器（自带）

### Go 环境（3个核心组件）
1. ✅ **GVM** - Go版本管理器（可选）
2. ✅ **Go** - Go运行时
3. ✅ **GOPROXY** - 模块代理配置

**总计：前端5个 + Go 3个 = 8个核心环境组件**
