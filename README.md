# 中文 LaTeX 极简模板 (基于 Pixi + Tectonic)

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

## 最终 pixi.toml

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

## 完整构建命令汇总

```bash
pixi init -c conda-forge
pixi workspace channel add https://conda.anaconda.org/dnachun
pixi workspace name set latex-pixi-tectonic
pixi add tectonic biber chktex tex-fmt
pixi remove biber
pixi add "biber=2.17.*"
pixi install
```

---

## 生成文件结构

```
pixi.toml      ← pixi init + pixi add
pixi.lock       ← pixi install
main.tex        ← pixi run bash
references.bib   ← pixi run bash
main.pdf         ← pixi run tectonic
.pixi/           ← pixi install
```
