# RDKit

## 环境配置与先决条件

* **前置知识：** 具备基础的 Python 编程能力，了解基本的化学结构表示方法（如 SMILES 字符串）。
* **安装包管理器：** 强烈建议使用 Anaconda 或 Miniconda 管理 RDKit 环境，可以极大避免复杂的 C++ 底层依赖冲突。
* **安装命令：** 在终端中运行 `conda install -c conda-forge rdkit` 即可完成安装。
* **开发工具：** 推荐使用 Jupyter Notebook，它能直接在单元格中渲染分子的 2D 结构图，非常适合学习、数据探索和调试。


## 分子的读写

RDKit 中的分子对象统一称为 `Mol` 对象，使用 `Chem.MolFromSmiles()` 函数进行转换。

### 核心操作

```python
from rdkit import Chem

# 1. 单个读取
smiles = "CC(=O)OC1=CC=CC=C1C(=O)O"  # 阿司匹林
mol = Chem.MolFromSmiles(smiles)

if mol:
    print(f"原子数: {mol.GetNumAtoms()}")
else:
    print("SMILES 解析失败")

```

### 注意事项

* **合法性检查**：如果输入的 SMILES 语法错误，函数会返回 `None`。建议在代码中加入 `if mol:` 判断。
* **默认处理**：RDKit 默认会进行 **Sanitization**（清理），包括计算价键、芳香性处理等。

---

## 2. 读取 .mol 文件

`.mol` 文件（通常指 MDL V2000 或 V3000 格式）通常包含分子的 2D 或 3D 坐标信息。

### 核心操作

```python
# 从文件路径读取
mol_file = "molecule.mol"
mol = Chem.MolFromMolFile(mol_file)

# 如果是读取文件流或字符串
# mol = Chem.MolFromMolBlock(mol_block_string)

```

---

## 3. 批量读取 .sdf 文件

`.sdf` 文件是多个 `.mol` 文件的集合，通常还附带分子的属性数据（如 ID、活性数值、分子量等）。

### 使用 `SDMolSupplier`

这是最常用的批量读取方式，它像一个迭代器，不会一次性将所有分子读入内存，非常适合处理大型数据库。

```python
suppl = Chem.SDMolSupplier('data.sdf')

for i, mol in enumerate(suppl):
    if mol is None: 
        continue  # 跳过解析失败的分子
    
    # 获取 SDF 中的属性数据
    name = mol.GetProp('_Name') # 获取分子名称
    logp = mol.GetProp('LogP')   # 获取自定义属性
    
    print(f"分子 {i}: {name}, 原子数: {mol.GetNumAtoms()}")

```

---

## 4. 将分子写出（保存）

读取之后，你往往需要将处理后的 `Mol` 对象保存回文件。

### 写出到 SMILES 或 Mol 文件

```python
# 转回 SMILES
new_smiles = Chem.MolToSmiles(mol)

# 保存为 .mol 文件
print(Chem.MolToMolBlock(mol), file=open('output.mol', 'w'))

```

### 批量写出到 .sdf

使用 `SDWriter` 可以将多个分子及其属性打包保存。

```python
writer = Chem.SDWriter('output_batch.sdf')

for mol in my_mols:
    # 你可以为分子添加新的属性
    mol.SetProp("Source", "RDKit_Process")
    writer.write(mol)

writer.close()

```

---

## 总结：常用函数速查表

| 数据格式 | 读取函数 | 写出函数 | 特点 |
| --- | --- | --- | --- |
| **SMILES** | `MolFromSmiles()` | `MolToSmiles()` | 纯文本，适合存储和快速传输 |
| **.mol** | `MolFromMolFile()` | `MolToMolFile()` | 包含坐标信息，单个分子 |
| **.sdf** | `SDMolSupplier()` | `SDWriter()` | 批量处理，支持存储元数据（Props） |

**想试试进阶操作吗？** 比如，我可以教你如何利用 Pandas 结合 RDKit（使用 `PandasTools`）直接读取整个 SDF 文件并展示分子结构表格。要尝试一下吗？
* **分子的输出：** 掌握如何将 RDKit 分子对象转换回规范的 SMILES 字符串或保存为文件格式供其他软件使用。
* **遍历原子和键：** 学习提取分子的基本微观属性，例如原子类型、原子序号、化学键类型（单键、双键、芳香键等）。
* **环系信息提取：** 使用内置方法判断分子中是否存在环状结构、环的数量以及环的大小。

### 第三阶段：进阶化学信息学

进入这个阶段，您将开始使用 RDKit 进行真正的数据分析和结构处理。

* **子结构搜索：** 学习使用 SMARTS 语法（SMILES 的进阶规则版）来匹配和搜索特定的化学基团或骨架（重点掌握 `HasSubstructMatch` 方法）。
* **分子描述符计算：** 使用 `rdkit.Chem.Descriptors` 模块计算分子的物理化学性质，如分子量（MolWt）、脂水分配系数（LogP）、氢键供体/受体数量等（也就是著名的 Lipinski 类药五法则参数）。
* **分子指纹提取：**  掌握如何生成用于机器学习和计算分子相似性的指纹特征，重点学习 Morgan 指纹（圆形指纹）和 MACCS 密钥。
* **结构可视化：**  学习 `rdkit.Chem.Draw` 模块，将分子对象渲染为高质量的图片，并将匹配到的特定子结构进行高亮显示。

### 第四阶段：高级应用与数据科学结合

将 RDKit 融入到现代数据科学、AI 药物发现和机器学习工作流中。

* **PandasTools 模块：** 学习如何将带有结构和属性的 `.sdf` 文件直接加载到 Pandas DataFrame 中，让分子结构图直接显示在数据表格里，极大地提升批量数据清洗的效率。
* **化学反应处理：** 了解 `rdkit.Chem.rdChemReactions` 模块，通过反应 SMARTS 定义反应规则（如酰胺化反应），并在代码中模拟虚拟化学反应生成产物库。
* **3D 构象生成与优化：** 学习 `rdkit.Chem.AllChem` 模块，将 2D 分子图转换为 3D 空间构象，并使用基于力场（如 MMFF94 或 UFF）的算法进行能量最小化优化。
* **结合机器学习：** 将 RDKit 生成的分子指纹和描述符作为特征（Features）输入到 Scikit-learn 或 PyTorch 中，构建 QSAR（定量构效关系）模型，从而预测分子的生物活性或毒性。

### 核心学习资源汇总

为了配合上述路线，以下资源是您在学习过程中高效排坑的利器。

| 资源名称 | 特点与用途 | 适用阶段 |
| --- | --- | --- |
| **RDKit 官方文档** | 查询 API 的终极指南，包含每个模块的详细说明。 | 全阶段适用 |
| **Getting Started in RDKit** | 官方提供的核心入门教程，适合零基础跑通代码。 | 第一阶段至第二阶段 |
| **RDKit Cookbook** | 提供大量常见化学信息学任务的代码片段和最佳实践。 | 第三阶段至第四阶段 |
| **TeachOpenCADD 项目** | GitHub 上非常优秀的开源教程，将 RDKit 结合实际的药物发现流程（CADD）进行实战演练。 | 第四阶段及实战应用 |