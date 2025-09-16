# selpoc

A command-line tool for protein structure analysis and molecular docking box generation.

## Features

- **Center of Mass (COM)** and **Center of Geometry (COG)** calculation
- **Bounding box** analysis for protein regions
- **Radius of gyration (Rg)** computation
- **Vina/gnina box parameter generation** for molecular docking
- **Multi-chain residue selection** with flexible syntax
- **Range syntax support** for efficient residue specification

## Installation

### Prerequisites

- Python 3.7+
- MDAnalysis library

### Install Dependencies

```bash
pip install MDAnalysis numpy
```

### Install selpoc

```bash
# Clone the repository
git clone https://github.com/yourusername/selpoc.git
cd selpoc

# Make executable and install globally
chmod +x selpoc
cp selpoc ~/.local/bin/selpoc

# Ensure ~/.local/bin is in your PATH
export PATH="$HOME/.local/bin:$PATH"
```

## Usage

### Basic Usage

```bash
# View PDB structure information
selpoc protein.pdb --pdbview

# Select residues from all chains
selpoc protein.pdb --resid 45 67 120

# Select residue range
selpoc protein.pdb --resid 470-476

# Select from specific chain
selpoc protein.pdb --chain A --resid 45-50
```

### Multi-chain Selection

```bash
# Select specific residues from different chains
selpoc protein.pdb --resid A:471 B:472

# Select ranges from different chains
selpoc protein.pdb --resid A:470-476 B:480-485

# Mix single residues and ranges
selpoc protein.pdb --resid A:470-472 B:475 A:480-485
```

### Molecular Docking Box Generation

```bash
# Generate Vina/gnina box parameters
selpoc protein.pdb --resid A:470-476 --box --box-pad 5.0

# Output: --center_x -18.112 --center_y 3.054 --center_z 21.264 --size_x 22.617 --size_y 21.97 --size_z 21.024
```

### Advanced Options

```bash
# Include center of geometry
selpoc protein.pdb --resid A:470-476 --geom-center

# Include radius of gyration
selpoc protein.pdb --resid A:470-476 --radius

# JSON output
selpoc protein.pdb --resid A:470-476 --json

# Custom MDAnalysis selection
selpoc protein.pdb --sel "protein and name CA and resid 470:476"
```

## Selection Formats

### Residue ID Formats

1. **Simple format**: `45 67 120` (selects from all chains)
2. **Range format**: `470-476` (selects residues 470 through 476)
3. **Chain-specific**: `A:471 B:472` (selects residue 471 from chain A, 472 from chain B)
4. **Chain ranges**: `A:470-476` (selects residues 470-476 from chain A)
5. **Mixed formats**: `A:470-472 B:475 A:480-485`

### Chain Restriction

Use `--chain` with simple formats:
```bash
selpoc protein.pdb --chain A --resid 470-476  # Only chain A
```

Cannot combine `--chain` with chain-specific formats:
```bash
# ❌ This will error
selpoc protein.pdb --chain A --resid A:470 B:471

# ✅ Use this instead
selpoc protein.pdb --resid A:470 B:471
```

## Output

### Standard Output
```
PDB        : /path/to/protein.pdb
Selection  : (segid A and resid 470 471 472 473 474 475 476)  (atoms: 77)
Center(COM): [-18.112   3.054  21.264]
BBox min   : [-24.659  -2.892  15.205]
BBox max   : [-12.042   9.078  26.229]
Box size   : [12.617 11.97  11.024]
```

### Vina Box Output
```
--center_x -18.112 --center_y 3.054 --center_z 21.264 --size_x 22.617 --size_y 21.97 --size_z 21.024
```

This output can be directly used in Vina or gnina commands:
```bash
vina --receptor protein.pdbqt --ligand ligand.pdbqt \
     --center_x -18.112 --center_y 3.054 --center_z 21.264 \
     --size_x 22.617 --size_y 21.97 --size_z 21.024 \
     --out result.pdbqt
```

## Examples

### Binding Site Analysis
```bash
# Analyze binding pocket residues
selpoc protein.pdb --resid A:195-205 B:310-320 --box --box-pad 3.0 --radius
```

### Multi-domain Protein
```bash
# Select residues from multiple domains
selpoc protein.pdb --resid A:50-80 A:150-180 B:200-230
```

### Large Range Selection
```bash
# Select large consecutive range efficiently
selpoc protein.pdb --resid A:100-200 --geom-center --radius
```

## Command-line Options

| Option | Description |
|--------|-------------|
| `--pdbview` | Show PDB chain information and residue ranges |
| `--resid` | Specify residue IDs (supports ranges and chain notation) |
| `--sel` | Custom MDAnalysis selection string |
| `--chain` | Restrict selection to specific chain (simple format only) |
| `--geom-center` | Calculate center of geometry (COG) |
| `--radius` | Calculate radius of gyration |
| `--box` | Generate Vina/gnina box parameters |
| `--box-center` | Box center type: `com` (default) or `cog` |
| `--box-pad` | Padding in Å (default: 0.0) |
| `--box-round` | Decimal places for box output (default: 3) |
| `--json` | Output results as JSON |

## Dependencies

- **MDAnalysis**: Molecular structure analysis
- **NumPy**: Numerical computations
- **Python 3.7+**: Runtime environment

## License

MIT License

## Contributing

1. Fork the repository
2. Create your feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

## Citation

If you use selpoc in your research, please cite:

```
Your Name. (2025). selpoc: A command-line tool for protein structure analysis 
and molecular docking box generation. GitHub. https://github.com/yourusername/selpoc
```