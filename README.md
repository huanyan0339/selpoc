# selpoc

蛋白质结构分析和分子对接盒子生成的命令行工具。

## 功能特点

- **质心 (COM)** 和 **几何中心 (COG)** 计算
- 蛋白质区域的**边界框**分析
- **回转半径 (Rg)** 计算
- 分子对接的 **Vina/gnina 盒子参数生成**
- 灵活语法的**多链残基选择**
- 高效残基指定的**范围语法支持**

## 安装

### 依赖要求

- Python 3.7+
- MDAnalysis 库

### 安装依赖

```bash
pip install MDAnalysis numpy
```

### 安装 selpoc

```bash
# 克隆仓库
git clone https://github.com/yourusername/selpoc.git
cd selpoc

# 使其可执行并全局安装
chmod +x selpoc
cp selpoc ~/.local/bin/selpoc

# 确保 ~/.local/bin 在您的 PATH 中
export PATH="$HOME/.local/bin:$PATH"
```

## 使用方法

### 基本用法

```bash
# 查看 PDB 结构信息
selpoc protein.pdb --pdbview

# 选择所有链的残基
selpoc protein.pdb --resid 45 67 120

# 选择残基范围
selpoc protein.pdb --resid 470-476

# 选择特定链
selpoc protein.pdb --chain A --resid 45-50
```

### 多链选择

```bash
# 选择不同链的特定残基
selpoc protein.pdb --resid A:471 B:472

# 选择不同链的范围
selpoc protein.pdb --resid A:470-476 B:480-485

# 混合单个残基和范围
selpoc protein.pdb --resid A:470-472 B:475 A:480-485
```

### 分子对接盒子生成

```bash
# 生成 Vina/gnina 盒子参数
selpoc protein.pdb --resid A:470-476 --box --box-pad 5.0

# 输出: --center_x -18.112 --center_y 3.054 --center_z 21.264 --size_x 22.617 --size_y 21.97 --size_z 21.024
```

### 高级选项

```bash
# 包含几何中心
selpoc protein.pdb --resid A:470-476 --geom-center

# 包含回转半径
selpoc protein.pdb --resid A:470-476 --radius

# JSON 输出
selpoc protein.pdb --resid A:470-476 --json

# 自定义 MDAnalysis 选择
selpoc protein.pdb --sel "protein and name CA and resid 470:476"
```

## 选择格式

### 残基 ID 格式

1. **简单格式**: `45 67 120` (从所有链选择)
2. **范围格式**: `470-476` (选择470到476残基)
3. **链特定**: `A:471 B:472` (从链A选择471残基，从链B选择472残基)
4. **链范围**: `A:470-476` (从链A选择470-476残基)
5. **混合格式**: `A:470-472 B:475 A:480-485`

### 链限制

对简单格式使用 `--chain`：
```bash
selpoc protein.pdb --chain A --resid 470-476  # 仅链A
```

不能将 `--chain` 与链特定格式组合：
```bash
# ❌ 这会报错
selpoc protein.pdb --chain A --resid A:470 B:471

# ✅ 应该使用这种方式
selpoc protein.pdb --resid A:470 B:471
```

## 输出

### 标准输出
```
PDB        : /path/to/protein.pdb
Selection  : (segid A and resid 470 471 472 473 474 475 476)  (atoms: 77)
Center(COM): [-18.112   3.054  21.264]
BBox min   : [-24.659  -2.892  15.205]
BBox max   : [-12.042   9.078  26.229]
Box size   : [12.617 11.97  11.024]
```

### Vina 盒子输出
```
--center_x -18.112 --center_y 3.054 --center_z 21.264 --size_x 22.617 --size_y 21.97 --size_z 21.024
```

此输出可直接用于 Vina 或 gnina 命令：
```bash
vina --receptor protein.pdbqt --ligand ligand.pdbqt \
     --center_x -18.112 --center_y 3.054 --center_z 21.264 \
     --size_x 22.617 --size_y 21.97 --size_z 21.024 \
     --out result.pdbqt
```

## 示例

### 结合位点分析
```bash
# 分析结合口袋残基
selpoc protein.pdb --resid A:195-205 B:310-320 --box --box-pad 3.0 --radius
```

### 多结构域蛋白质
```bash
# 选择多个结构域的残基
selpoc protein.pdb --resid A:50-80 A:150-180 B:200-230
```

### 大范围选择
```bash
# 高效选择大的连续范围
selpoc protein.pdb --resid A:100-200 --geom-center --radius
```

## 命令行选项

| 选项 | 描述 |
|------|------|
| `--pdbview` | 显示 PDB 链信息和残基范围 |
| `--resid` | 指定残基 ID（支持范围和链标记） |
| `--sel` | 自定义 MDAnalysis 选择字符串 |
| `--chain` | 限制选择到特定链（仅简单格式） |
| `--geom-center` | 计算几何中心 (COG) |
| `--radius` | 计算回转半径 |
| `--box` | 生成 Vina/gnina 盒子参数 |
| `--box-center` | 盒子中心类型：`com`（默认）或 `cog` |
| `--box-pad` | 填充距离，单位 Å（默认：0.0） |
| `--box-round` | 盒子输出的小数位数（默认：3） |
| `--json` | 以 JSON 格式输出结果 |

## 依赖

- **MDAnalysis**: 分子结构分析
- **NumPy**: 数值计算
- **Python 3.7+**: 运行环境

## 许可证

MIT 许可证

## 贡献

1. Fork 仓库
2. 创建您的功能分支 (`git checkout -b feature/amazing-feature`)
3. 提交您的更改 (`git commit -m 'Add amazing feature'`)
4. 推送到分支 (`git push origin feature/amazing-feature`)
5. 打开 Pull Request

## 引用

如果您在研究中使用 selpoc，请引用：

```
ZhangTao. (2025). selpoc: 蛋白质结构分析和分子对接盒子生成的命令行工具。
GitHub. https://github.com/yourusername/selpoc
```