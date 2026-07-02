# 中文 LaTeX 极简模板 (基于 Pixi + Tectonic)

> **⚠️ 重要提示：** 不建议在 Windows 系统上直接使用 Tectonic。Windows 版本的 Biber 存在已知问题，可能导致编译过程无响应。请使用容器开发环境（WSLC）或 Linux 环境进行编译。

## 初始状态

空目录，无 `pixi.toml`。

---

## Step 1: `pixi init -c conda-forge`

```bash
$ pixi init -c conda-forge
✔ Created ./pixi.toml
```

生成 `pixi.toml`：
```toml
[workspace]
channels = ["conda-forge"]
name = "test"
platforms = ["linux-64"]
version = "0.1.0"

[tasks]

[dependencies]
```

---

## Step 2: `pixi workspace channel add https://conda.anaconda.org/dnachun`

```bash
$ pixi workspace channel add https://conda.anaconda.org/dnachun
✔ Added https://conda.anaconda.org/dnachun
```

`pixi.toml` 变化：
```toml
[workspace]
channels = ["conda-forge", "https://conda.anaconda.org/dnachun"]
name = "test"
platforms = ["linux-64"]
version = "0.1.0"

[tasks]

[dependencies]
```

---

## Step 3: `pixi workspace name set latex-pixi-tectonic`

```bash
$ pixi workspace name set latex-pixi-tectonic
✔ Updated workspace name to 'latex-pixi-tectonic'.
```

`pixi.toml` 变化：
```toml
[workspace]
channels = ["conda-forge", "https://conda.anaconda.org/dnachun"]
name = "latex-pixi-tectonic"
platforms = ["linux-64"]
version = "0.1.0"

[tasks]

[dependencies]
```

---

## Step 4: `pixi add tectonic biber chktex tex-fmt`

```bash
$ pixi add tectonic biber chktex tex-fmt
✔ Added tectonic >=0.16.9,<0.17
✔ Added biber >=2.20,<3
✔ Added chktex >=1.7.10,<2
✔ Added tex-fmt >=0.5.7,<0.6
```

`pixi.toml` 变化：
```toml
[workspace]
channels = ["conda-forge", "https://conda.anaconda.org/dnachun"]
name = "latex-pixi-tectonic"
platforms = ["linux-64"]
version = "0.1.0"

[tasks]

[dependencies]
tectonic = ">=0.16.9,<0.17"
biber = ">=2.20,<3"
chktex = ">=1.7.10,<2"
tex-fmt = ">=0.5.7,<0.6"
```

---

## Step 5: 修改 biber 为 2.17.*

```bash
$ pixi remove biber
✔ Removed biber

$ pixi add "biber=2.17.*"
✔ Added biber=2.17.*
```

`pixi.toml` 变化：
```toml
[workspace]
channels = ["conda-forge", "https://conda.anaconda.org/dnachun"]
name = "latex-pixi-tectonic"
platforms = ["linux-64"]
version = "0.1.0"

[tasks]

[dependencies]
tectonic = ">=0.16.9,<0.17"
chktex = ">=1.7.10,<2"
tex-fmt = ">=0.5.7,<0.6"
biber = "2.17.*"
```

---

## Step 6: `pixi install`

```bash
$ pixi install
```

生成 `pixi.lock` 和 `.pixi/` 目录。

---

## Step 7: 添加编译任务

```bash
$ pixi task add build "tectonic -X compile main.tex --keep-intermediates && biber main && tectonic -X compile main.tex --keep-intermediates && tectonic -X compile main.tex --keep-intermediates"
✔ Added task 'build'
$ pixi task add clean "rm -f *.aux *.bcf *.bbl *.blg *.run.xml *.toc *.out *.log"
✔ Added task 'clean'
$ pixi task add rebuild "clean build"
✔ Added task 'rebuild'
```

`pixi.toml` 变化：
```toml
[tasks.build]
cmd = """
tectonic -X compile main.tex --keep-intermediates &&
biber main &&
tectonic -X compile main.tex --keep-intermediates &&
tectonic -X compile main.tex --keep-intermediates
"""

[tasks.clean]
cmd = "rm -f *.aux *.bcf *.bbl *.blg *.run.xml *.toc *.out *.log"

[tasks.rebuild]
depends-on = ["clean","build"]
```

---

## 最终 pixi.toml

```toml
[workspace]
channels = ["conda-forge", "https://conda.anaconda.org/dnachun"]
name = "latex-pixi-tectonic"
platforms = ["linux-64"]
version = "0.1.0"

[tasks.build]
cmd = """
tectonic -X compile main.tex --keep-intermediates &&
biber main &&
tectonic -X compile main.tex --keep-intermediates &&
tectonic -X compile main.tex --keep-intermediates
"""

[tasks.clean]
cmd = "rm -f *.aux *.bcf *.bbl *.blg *.run.xml *.toc *.out *.log"

[tasks.rebuild]
depends-on = ["clean","build"]

[dependencies]
tectonic = ">=0.16.9,<0.17"
chktex = ">=1.7.10,<2"
tex-fmt = ">=0.5.7,<0.6"
biber = "2.17.*"
```

---

## 为什么采用分阶段编译？

Tectonic 捆绑了自身的 `libxml2`，而 `biber` 依赖系统安装的 `libxml2`。
如果在一个长命令中连续调用 `tectonic` 和 `biber`，动态链接器可能因共享库冲突导致
段错误（segfault）或其它未定义行为。

**解决方式**：拆分为多步命令（`tectonic → biber → tectonic → tectonic`），
每一步结束后进程退出，释放所有加载的共享库，下次调用时重新加载，从而彻底避免 `libxml2`
版本冲突。

> 注意：`--keep-intermediates` 保留中间文件（`.aux`、`.bcf` 等），确保 `biber`
> 和后续 `tectonic` 编译能读到必要信息。

---

## 完整构建命令汇总

```bash
pixi init -c conda-forge
pixi workspace channel add https://conda.anaconda.org/dnachun
pixi workspace name set latex-pixi-tectonic
pixi add tectonic biber chktex tex-fmt
pixi remove biber
pixi add "biber=2.17.*"
pixi task add build "tectonic -X compile main.tex --keep-intermediates && biber main && tectonic -X compile main.tex --keep-intermediates && tectonic -X compile main.tex --keep-intermediates"
pixi task add clean "rm -f *.aux *.bcf *.bbl *.blg *.run.xml *.toc *.out *.log"
pixi task add rebuild "clean build"
pixi install
```

---

## 使用方式

```bash
# 编译
pixi run build

# 清理中间文件
pixi run clean

# 清理后重新编译
pixi run rebuild
```

---

## 生成文件结构

```
pixi.toml      ← pixi init + pixi add
pixi.lock       ← pixi install
main.tex        ← 手动创建
references.bib   ← 手动创建
main.pdf         ← pixi run build
.pixi/           ← pixi install
.devcontainer/   ← 容器开发环境配置
```

---

## 容器开发环境 (WSLC)

### 背景

微软于 2026 年 Build 大会发布了 **WSLC (Windows Subsystem for Linux Container)** 预览版，让 Windows 开发者可以直接在 Windows 上使用 Linux 容器，无需安装 Docker Desktop 或其他第三方容器工具。

参考：
- [WSL container is now available for public preview](https://devblogs.microsoft.com/commandline/wsl-container-is-now-available-for-public-preview/)
- [WSL container 文档](https://learn.microsoft.com/en-us/windows/wsl/wsl-container)

### 为什么需要容器开发环境？

1. **跨平台一致性** - 容器确保 LaTeX 编译环境在 Windows、Linux、macOS 上完全一致
2. **依赖管理** - Tectonic、Biber 等工具及其依赖（如 libxml2）在容器内隔离，避免系统污染
3. **团队协作** - 团队成员使用相同的开发环境，减少"在我机器上能跑"的问题
4. **WSLC 优势** - 相比传统 Docker，WSLC 更轻量、启动更快，与 Windows 集成更紧密

### devcontainer 配置说明

项目包含 `.devcontainer/` 目录，支持 VS Code 的 Remote Container 扩展：

```
.devcontainer/
├── devcontainer.json    # 容器配置
└── Dockerfile           # 容器镜像定义
```

**Dockerfile 关键配置：**
- 基于 `mcr.microsoft.com/devcontainers/base:debian`
- 安装 pixi 包管理器
- 配置 git 自动处理行尾符 (`core.autocrlf input`)
- 设置安全目录 (`safe.directory '*'`)

**devcontainer.json 关键配置：**
- 使用 volume 挂载 `.pixi` 目录，避免重复安装
- 自动运行 `pixi install`
- 集成 LaTeX Workshop 和 pixi 扩展

### 使用方式

1. **安装 WSLC 预览版**
   ```powershell
   wsl --update --pre-release
   ```

2. **安装 VS Code 扩展**
   - [Dev Containers](https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.remote-containers)
   - 设置 Docker Path 为 `wslc`（在 VS Code 设置中）

3. **打开项目**
   - 使用 VS Code 打开项目
   - 点击左下角绿色图标，选择 "Reopen in Container"

### 跨平台行尾符处理

Windows 使用 CRLF 行尾符，Linux 使用 LF。为避免 git 误判文件修改，已在 `.gitattributes` 中配置：

```
* text=auto
```

这会自动在 checkout 时转换行尾符，确保跨平台兼容。
