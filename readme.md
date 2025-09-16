# Selpoc: PDB 文件选择与分析工具

这是一个命令行工具，用于从 PDB 文件中选择特定的原子，并计算其质心 (COM)、几何中心 (COG)、回转半径 (Rg) 以及为分子对接软件 (如 Vina/Gnina) 生成对接盒子参数。

## 📦 安装 (Installation)

1.  **将脚本复制到本地**
    首先，创建脚本文件并赋予执行权限。
    ```bash
    # 创建文件并粘贴脚本内容
    nano ~/.local/bin/selpoc

    # 赋予执行权限
    chmod +x ~/.local/bin/selpoc
    ```

2.  **确保 `~/.local/bin` 在您的 `PATH` 环境变量中**
    ```bash
    echo 'export PATH="$HOME/.local/bin:$PATH"' >> ~/.bashrc
    source ~/.bashrc
    ```

3.  **安装依赖**
    该脚本依赖 `MDAnalysis` 库。
    ```bash
    # 使用 pip 安装
    pip install MDAnalysis

    # 或者使用 conda
    conda install -c conda-forge mdanalysis
    ```

## 🚀 使用方法 (Usage)

#### 1. 基础调用

* **通过残基 ID 选择**
    ```bash
    selpoc myprot.pdb --resid 45 67 120
    ```
* **使用 MDAnalysis 的选择语法**
    ```bash
    selpoc myprot.pdb --sel "segid A and name CA and resid 45 67 120"
    ```

#### 2. 可选功能

* **输出几何中心 (Center of Geometry, COG)**
    ```bash
    selpoc myprot.pdb --resid 45 67 120 --geom-center
    ```
* **计算回转半径 (Radius of Gyration, Rg)**
    ```bash
    selpoc myprot.pdb --resid 45 67 120 --radius
    ```
* **导出 Vina/Gnina 对接盒子参数** (默认使用质心 `COM` 作为中心)
    ```bash
    selpoc myprot.pdb --resid 45 67 120 --box
    ```
* **自定义盒子参数** (使用 `COG` 作为中心, 增加 2Å 的 padding, 小数位保留 2 位)
    ```bash
    selpoc myprot.pdb --resid 45 67 120 --box --box-center cog --box-pad 2 --box-round 2
    ```
* **以 JSON 格式输出**
    ```bash
    selpoc myprot.pdb --resid 45 67 120 --json
    ```

## 📖 示例输出 (Example Output)

#### 普通模式 (Normal Mode)

```yaml
PDB        : /home/zt/myprot.pdb
Selection  : resid 45 67 120  (atoms: 42)
Center(COM): [12.345  8.901  5.678]
BBox min   : [10.111  7.222  5.000]
BBox max   : [14.222  9.888  6.222]
Box size   : [4.111 2.666 1.222]
Rg         : 2.345
```
---
#### Vina/Gnina 盒子参数 (Vina/Gnina Box Parameters)
```bash
--center_x 12.345 --center_y 8.901 --center_z 5.678 --size_x 20.0 --size_y 18.0 --size_z 16.0
```
---
### JSON 模式 (JSON Mode)
```JSON
{
  "pdb": "/home/zt/myprot.pdb",
  "selection": "resid 45 67 120",
  "n_atoms": 42,
  "center_of_mass": [12.345, 8.901, 5.678],
  "bbox_min": [10.111, 7.222, 5.0],
  "bbox_max": [14.222, 9.888, 6.222],
  "box_size": [4.111, 2.666, 1.222],
  "radius_of_gyration": 2.345
}
```
---

## 📑 参数说明 (Parameters)

| 参数 | 类型 | 默认值 | 说明 | 示例 |
| :--- | :--- | :--- | :--- | :--- |
| `pdb` | **必填** | 无 | 输入的 PDB 文件路径 | `myprot.pdb` |
| `--resid` | 列表 | 无 | 指定一个或多个残基 ID | `--resid 45 67 120` |
| `--sel` | 字符串 | 无 | 使用 MDAnalysis 的选择语法 | `--sel "segid A and name CA"` |
| `--geom-center` | flag | `False` | 计算并输出几何中心 (COG) | `--geom-center` |
| `--radius` | flag | `False` | 计算并输出回转半径 (Rg) | `--radius` |
| `--box` | flag | `False` | 输出 Vina/Gnina 的盒子参数 | `--box` |
| `--box-center` | `com` / `cog` | `com` | 指定盒子中心是质心(COM)还是几何中心(COG) | `--box-center cog` |
| `--box-pad` | float | `0.0` | 在盒子每个方向上增加的 padding (单位 Å) | `--box-pad 2` |
| `--box-round` | int | `3` | 输出盒子参数时保留的小数位数 | `--box-round 2` |
| `--json` | flag | `False` | 以 JSON 格式输出所有信息 | `--json` |